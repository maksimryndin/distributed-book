# Practice: virtual interface

As we already discussed, at the link layer of TCP/IP we have network interfaces allowing us to interact with a network devices. But this network devices can be completely virtual allowing a userspace program interact with a network[^userspace_tcpip] at the link layer (TAP device) and the internet layer (TUN device) which is used in different tunneling programs like vpns. You can create such interfaces with console commands (like `ip tuntap`) but let's try to create a TAP device programmaticaly. There [C examples](https://www.kernel.org/doc/Documentation/networking/tuntap.txt) but let's do it in Rust from scratch[^tcpip_tutorial].

We create rust workspace (a collection of several packages) to have some experiments with the network in further chapters (you can find all the code in the [netstack repository](https://github.com/maksimryndin/netstack)).

```bash,ignore
$ mkdir netstack
$ cd netstack
```

In the `netstack` directory we create `Cargo.toml` with the contents:

```toml,ignore
[workspace]

members = [
    "virtual-interface",
    "bindings",
]
```

After we create libs running `cargo new --lib virtual-interface` and `cargo new --lib bindings` in the `netstack` directory. So to create a virtual network device, we need to open for a read-write access file `/dev/net/tun` and configure it with `ioctl` - a swiss knife of device management in Unix/Linux. Essentialy, `ioctl` as a universal system call (please see `man ioctl`) was created to control different devices and be extensible to manage new types of devices. To achieve this goal `ioctl` accepts an open file descriptor (device), a request code - unsigned long number which is [unique across devices](https://www.kernel.org/doc/Documentation/ioctl/ioctl-number.txt) and it can accept a pointer to some datastructure related to the specific device. In our case of a network device (TAP device) such a control structure is `ifreq` (*interface request*, see `man 7 netdevice`) and a request code is `TUNSETIFF`.

Rust provides [libc](https://crates.io/crates/libc) crate as raw ffi bindings for system libraries so we could use [ioctl](https://rust-lang.github.io/libc/aarch64-unknown-linux-gnu/doc/libc/fn.ioctl.html) and some constants but it is missing constants for TUN and TAP devices. So anyway as we need to create some bindings ourselves let use [bindgen](https://crates.io/crates/bindgen) crate to generate bindings for system libraries at the build time. Following [bidngen tutorial](https://rust-lang.github.io/rust-bindgen/tutorial-0.html) we create a `netstack/bindings/wrapper.h` file where we place necessary headers:

```c,ignore
#include <sys/ioctl.h>
#include <linux/if.h>
#include <linux/if_tun.h>
```

and create `bindings/build.rs` with the contents

```rust,ignore
extern crate bindgen;

use std::env;
use std::path::PathBuf;

fn main() {
    // Tell cargo to invalidate the built crate whenever the wrapper changes
    println!("cargo:rerun-if-changed=wrapper.h");

    // The bindgen::Builder is the main entry point
    // to bindgen, and lets you build up options for
    // the resulting bindings.
    let bindings = bindgen::Builder::default()
        // The input header we would like to generate
        // bindings for.
        .header("wrapper.h")
        // Tell cargo to invalidate the built crate whenever any of the
        // included header files changed.
        .parse_callbacks(Box::new(bindgen::CargoCallbacks))
        // Finish the builder and generate the bindings.
        .generate()
        // Unwrap the Result and panic on failure.
        .expect("Unable to generate bindings");

    // Write the bindings to the $OUT_DIR/bindings.rs file.
    let out_path = PathBuf::from(env::var("OUT_DIR").unwrap());
    bindings
        .write_to_file(out_path.join("bindings.rs"))
        .expect("Couldn't write bindings!");
}
```

Run `cargo build` and at `target/debug/build/bindings-.../out/bindings.rs` we can find needed bindings except `TUNSETIFF` which is a required request code for `ioctl` (bindgen [do not generate bindings for functional macros](https://github.com/rust-lang/rust-bindgen/issues/753)) so if we check contents of `/usr/include/linux/if_tun.h` then we see `TUNSETIFF` is a functional macro `#define TUNSETIFF _IOW('T', 202, int)` so let's port the C macro code from `/usr/include/asm-generic/ioctl.h` where participating macros live.

In `netstack/bindings/src/lib.rs`

```rust,ignore
include!(concat!(env!("OUT_DIR"), "/bindings.rs"));
use std::mem;
use std::os::raw;

// /usr/include/asm-generic/ioctl.h
const IOC_SIZEBITS: u8 = 14;
const IOC_NRBITS: u8 = 8;
const IOC_TYPEBITS: u8 = 8;
const IOC_NRSHIFT: u8 = 0;
const IOC_TYPESHIFT: u8 = IOC_NRSHIFT + IOC_NRBITS;
const IOC_SIZESHIFT: u8 = IOC_TYPESHIFT + IOC_TYPEBITS;
const IOC_DIRSHIFT: u8 = IOC_SIZESHIFT + IOC_SIZEBITS;
const IOC_WRITE: u32 = 1;

//#define TUNSETIFF     _IOW('T', 202, int)
const INT_SIZE: usize = mem::size_of::<raw::c_int>();
pub const TUNSETIFF: u32 = IOC_WRITE << IOC_DIRSHIFT
    | (b'T' as u32) << IOC_TYPESHIFT
    | 202 << IOC_NRSHIFT
    | (INT_SIZE as u32) << IOC_SIZESHIFT;
```

Let's add file `netstack/virtual-interface/src/tap.rs` where main things happen

```rust,ignore
use std::ffi;
use std::fs::{File, OpenOptions};
use std::io;
use std::mem;
use std::os::unix::io::{FromRawFd, IntoRawFd};

use bindings;

const VIRTUAL_DEVICE: &str = "/dev/net/tun";

#[derive(Debug)]
pub enum VirtualInterfaceError {
    IoError(io::Error),
    IoctlError,
    DeviceNameTooLong,
    DeviceNameContainsNulByte(ffi::NulError),
}

impl From<io::Error> for VirtualInterfaceError {
    fn from(error: io::Error) -> Self {
        VirtualInterfaceError::IoError(error)
    }
}

impl From<ffi::NulError> for VirtualInterfaceError {
    fn from(error: ffi::NulError) -> Self {
        VirtualInterfaceError::DeviceNameContainsNulByte(error)
    }
}

pub struct VirtualInterface {
    device: File,
}

impl VirtualInterface {
    pub fn create(name: &str) -> Result<Self, VirtualInterfaceError> {
        // reserve 1 byte for '\0'
        if name.len() >= bindings::IFNAMSIZ as usize {
            return Err(VirtualInterfaceError::DeviceNameTooLong);
        }
        // We have to check that the device name has no zero bytes in the middle
        let device_name = ffi::CString::new(name)?.into_bytes_with_nul();
        let device = OpenOptions::new()
            .read(true)
            .write(true)
            .open(VIRTUAL_DEVICE)?;
        // ifreq is a structure to control network device (see man 7 netdevice)
        let mut ifr: bindings::ifreq = unsafe { mem::zeroed() };

        // create stack allocated array to hold the device name
        let mut name_buffer = [0_u8; bindings::IFNAMSIZ as usize];
        // and copy name bytes to it
        for (i, b) in device_name.into_iter().enumerate() {
            name_buffer[i] = b;
        }
        ifr.ifr_ifrn.ifrn_name = name_buffer;
        // IFF_TAP - tap device
        // IFF_NO_PI - no additional info for Ethernet package
        // IFF_TUN_EXCL - prevent creation of duplicates
        ifr.ifr_ifru.ifru_flags = (bindings::IFF_TAP | bindings::IFF_NO_PI | bindings::IFF_TUN_EXCL)
            as std::os::raw::c_short;

        let raw_fd = device.into_raw_fd();
        if unsafe { bindings::ioctl(raw_fd, bindings::TUNSETIFF as u64, &mut ifr as *mut _) } == -1
        {
            return Err(VirtualInterfaceError::IoctlError);
        }
        let device = unsafe { File::from_raw_fd(raw_fd) };
        Ok(Self { device })
    }

    pub fn device(&mut self) -> &mut File {
        &mut self.device
    }
}
```

and import it inside `netstack/virtual-interface/src/lib.rs`:

```rust,ignore
pub mod tap;
```

To test the code let's start with an example `netstack/virtual-interface/examples/read_loop.rs` 

```rust,ignore
use std::io::Read;
use virtual_interface::tap::VirtualInterface;

fn main() {
    let mut interface = VirtualInterface::create("dev0").unwrap();
    let mut buffer = [0; 4096];
    println!("starting read loop for device `dev0`");
    loop {
        let n = interface.device().read(&mut buffer[..]).unwrap();
        println!("The bytes: {:?}", &buffer[..n]);
    }
}
```

and build and run it:
```bash,ignore
$ cargo build --example read_loop
$ ./target/debug/examples/read_loop
```

Oh, no some ioctl error: `thread 'main' panicked at 'called Result::unwrap() on an Err value: IoctlError'`. Let's debug with strace.

```bash,ignore
$ strace ./target/debug/examples/read_loop
...
ioctl(3, TUNSETIFF, 0xffffce5f30e0)     = -1 EPERM (Operation not permitted)
...
```

So it complains about permissions. Yes, checking `man 7 netdevice` again
> If an ioctl is marked as privileged, then using it requires an effective user ID of 0  or  the CAP_NET_ADMIN capability.  If this is not the case, EPERM will be returned.

So let's try with `sudo ./target/debug/examples/read_loop` and check if the inerface is created in another terminal:

```bash,ignore
$ ip link show dev0
dev0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether b2:24:f3:9a:71:c1 brd ff:ff:ff:ff:ff:ff
```

It works![^ip_cli] But how our binary knows how to call `libc` functions and use `libc` structs? Rust dynamically links some system libraries and `libc` is among them:

```bash,ignore
$ ldd ./target/debug/examples/read_loop
linux-vdso.so.1 (0x0000ffffb2199000)
libgcc_s.so.1 => /lib/aarch64-linux-gnu/libgcc_s.so.1 (0x0000ffffb20c0000)
libc.so.6 => /lib/aarch64-linux-gnu/libc.so.6 (0x0000ffffb1f10000)
/lib/ld-linux-aarch64.so.1 (0x0000ffffb2160000)
```

So our `netstack/bindings` works as expected.

Now you can think that giving `sudo` access to the program is probably too much power and you're right. And Linux comes to resque with its capabilities[^capabilities] system allowing to provide grains of superuser power. So we need to provide only `CAP_NET_ADMIN` capability to our program. From `man capabilities`:

```ignore
CAP_NET_ADMIN
    Perform various network-related operations:
    * interface configuration;
    * administration of IP firewall, masquerading, and accounting;
    * modify routing tables;
    * bind to any address for transparent proxying;
    * set type-of-service (TOS);
    * clear driver statistics;
    * set promiscuous mode;
    * enabling multicasting;
```

Note: if a filesystem with your compiled binary is mounted with `nosuid` flag (for example, a home directory is encrypted - you can check the flag for a home directory with the command `mount | grep $USER`) then you should copy the binary to some other directory which filesystem has not `nosuid` flag.

```bash,ignore
$ sudo setcap cap_net_admin=eip target/debug/examples/read_loop
```

Now let's run our example binary. In another terminal run `ip link show dev0` and check that the interface is down, so let's make it up with `sudo ip link set dev0 up`. Now we can see some bytes in the console and our next task is to decode them.

[^userspace_tcpip]: Actually offloading network stack to a userspace in traditional heavy-kernel OSes (as opposed to so called microkernel OSes) can bring performance boost - see for example [OpenOnload](https://github.com/Xilinx-CNS/onload).

[^tcpip_tutorial]: Many of the practice in this chapter is inspired by an excellent [Let's code a TCP/IP stack, 1: Ethernet & ARP](http://www.saminiir.com/lets-code-tcp-ip-stack-1-ethernet-arp/) tutorial by [Sami Niiranen](https://github.com/saminiir).

[^capabilities]: Comprehensive overview of Linux capabilities and different related exploits can be found at [Linux Capabilities](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities) by Carlos Polop.

[^ip_cli]: We could create a TAP device with the command `sudo ip tuntap add dev0 mode tap`. The only difference with our programmatically created device is that our device is not persistent - it lives while our process lives. To make our device persistent we could just add the flag `IFF_PERSIST` to `ifru_flags`.
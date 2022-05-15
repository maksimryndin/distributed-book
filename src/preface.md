# Preface

*To my girls: wife, daughter, and mom - for their patience and love*

## Who should read this book?

To be honest, I don't have vast practical experience of building distributed systems except the most popular one among web developers (hint: client-server). But a usual requirement to acquire this practical experience is some non-basic knowledge of the topic. So this is a classical "chicken-egg" problem: to work on a distributed software product you are usually required to have some related projects in your portfolio. So writing this book is a way to close these gaps.

Anyway I assume that readers have developed web projects and already have some experience in vertical and horizontal scaling, containerization, data races, classical algorithms and data structures. I will try at my best to introduce all used vocabulary.

I also do not devote any decent time for single machine patterns, as I believe you have enough working practice with them.

## Why yet another book on distributed systems?

I've read some excellent books devoted to distributed systems or related topics. Among them:
* Mikito Takada [Distributed systems: for fun and profit](http://book.mixu.net/distsys/)
* Brendan Burns [Designing Distributed Systems](https://azure.microsoft.com/mediahandler/files/resourcefiles/designing-distributed-systems/Designing_Distributed_Systems.pdf)
* Martin Kleppmann [Designing Data-Intensive Applications](https://dataintensive.net/)
* Donne Martin [System design primer](https://github.com/donnemartin/system-design-primer)

All above mentioned books have their own view on what topics and at what level should be covered. Martin Kleppman's outstanding book (highly recommended) obviously focuses on data-related topics.

Despite I've read the books above, I had no clear mental picture and systematic view of Raft Algorithm, Lamport clocks and other distributed buzzwords. So this book tries to cover important topics (choice is subjective, of course) and to serve as a practical guide for busy engineers striving to grasp core concepts in timely manner.

True understanding comes with real doing, so practice through exercises is a necessary part of learning. I have chosen Rust as a language to use throughout the book as I believe that Rust incorporates modern best practices and solid theoretical achievements. Experienced developers will dive without further ado. But for those who like more gradual involvement I could recommend the following excellent free resources to start with:
* [Easy Rust](https://dhghomon.github.io/easy_rust/)
* [Rust by example](https://doc.rust-lang.org/rust-by-example/)


## Why should we learn about distributed systems?

I admit that you already know the answer if you've started reading the book. But for the sake of completeness let's enlist some points:
* modern scalable products inevitably rely on concepts discussed further. So if you want to get working experience in modern tech companies or even build your own product with highload and fault-tolerance in mind, you should grasp the basics[^note];
* expecting your software being distributed at some point in the future can radically improve your architecture decisions and even determine your preferred stack of technologies to use;
* emerging [Web 3.0](https://en.wikipedia.org/wiki/Web3) is build around an idea of *decentralization* which is tightly connected with *distributed*. So at least you will be able to understand underlying technologies and explore them deeper.

The following goal for the book comes out of the points above - *get reader familiar with selected topics on distributed systems with strong emphasis on practice*.

## Errata, typos, bugs and other feedback

I am not a native English speaker so I will be glad to any feedback on correct usage of English grammar and phrases. 
Also any suggestions, improvements, fixes are welcome. You can provide merge requests at book's Github repository or email me at *{auhtor's name}.{author's last name}@gmail.com*.

## Acknowledgments

I would like to tell many kind words to people who influenced me as a software engineer during my career. First of all, the friend of mine, [Farit Sadykov](https://github.com/Farit), who helped me a lot to become a developer. Also my colleagues, especially [Pavel Antonov](https://github.com/softzilla), [Nikolay Yurov](https://github.com/dbz-legacy), [Vladimir Galkin](https://github.com/lan-pnz), Pavel Gorbunov, [Andrey Belov](https://github.com/andybelov), [Kirill Zhuravlev](https://github.com/kazhuravlev), [Ilya Scherbakov](https://github.com/scherbakoviv).

Also many thanks to open source enthusiasts creating wonderful products, books, and articles, generously sharing knowledge with the rest of the world. We truly stand on the shoulders of giants.


*Maksim Ryndin*

*Montenegro, 2022.*


[^note]: Being scalable doesn't only mean software and infrastructure while adding more servers. It's also about your team being ready to grow with growing traffic. So being prepared to go distributed is a must-have for every team member in terms of some introductory learning experience, automated pipelines and merge reviews etc.
Please watch very informative [presentation](https://www.youtube.com/watch?v=hnpzNAPiC0E) by Lisa Guo on scaling Instagram.


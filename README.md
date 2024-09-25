# SysDesign Meetup

Hi, I'm your host, Dima ([Github](https://github.com/dkorolev), [LinkedIn](https://linkedin.com/in/dimakorolev), [Twitter](https://twitter.com/@UniqueDima)), and it's great to have you here. Most important links for our SysDesign Meetup:

* Public — [YouTube channel](https://www.youtube.com/dimakorolev).
* Community, new members welcome! — [Slack](https://tinyurl.com/sdm-slack-invite).
* Community, new members welcome! — [Discord](https://tinyurl.com/sdm-discord-invite).
* X — [SysDesignMeetup Twitter](https://twitter.com/@SysDesignMeetup).
* Telegram — [Telegram channel](https://t.me/SysDesignMeetup).
* My Blog — [Substack](https://dimakorolev.substack.com).

# Episodes

* Stateful Orchestration, [video](https://tinyurl.com/sdm-orchestration), [slides](https://tinyurl.com/sdm-orchestration-slides).
* Testing, [video](https://tinyurl.com/sdm-testing-video), [slides](https://tinyurl.com/sdm-testing-slides).
* Web3 and Blockchain, [video](https://tinyurl.com/sdm-blockchain), [slides](https://tinyurl.com/sdm-blockchain-slides).
* Benchmarking Production Systems, [video](https://tinyurl.com/sdm-benchmarking), [slides](https://tinyurl.com/sdm-benchmarking-slides).
* When Wrong Makes Right, [video](https://tinyurl.com/sdm-wrongmakesright), [slides](https://tinyurl.com/sdm-wrongmakesright-slides).
* Distributed Task Queue, [video](https://tinyurl.com/sdm-taskqueue), [slides](https://tinyurl.com/sdm-taskqueue-slides).
* Coroutines / Leveraging the Cores, [video](https://tinyurl.com/sdm-coroutines), [slides](https://tinyurl.com/sdm-coroutines-slides).
* Kafka and RabbitMQ, [video](https://tinyurl.com/sdm-kafka-rabbit), [slides](https://tinyurl.com/sdm-kafka-rabbit-slides).
* Those Dreaded Microservices, [video](https://tinyurl.com/sdm-microservices), [slides](https://tinyurl.com/sdm-microservices-slides).
* Search and Recommendations, [video](https://tinyurl.com/sdm-search), [slides](https://tinyurl.com/sdm-search-slides).
* Consistent Hashing, [video](https://tinyurl.com/sdm-consistenthashing), [slides](https://tinyurl.com/sdm-consistenthashing-slides).
* TinyURL / URL Shortener, [video](https://tinyurl.com/sdm-tinyurl), [slides](https://tinyurl.com/sdm-tinyurl-slides).
* DBs and OLTPs, [video](https://tinyurl.com/sdm-oltp), [slides](https://tinyurl.com/sdm-oltp-slides).
* Kafka, [video](https://tinyurl.com/sdm-kafka), [slides](https://tinyurl.com/sdm-kafka-slides).
* Monitoring and Alerting, [video](https://tinyurl.com/sdm-pagerduty), [slides](https://tinyurl.com/sdm-pagerduty-slides).
* Exactly Once, [video](https://tinyurl.com/sdm-exactlyonce), [slides](https://tinyurl.com/sdm-exactlyonce-slides).
* MapReduce, [video](https://www.youtube.com/watch?v=HR82uF7vQ0k), [slides](https://docs.google.com/presentation/d/19_Nk8XhmUZfNgdKNV6qL9pb5yhzhUPOp-LPZ8aCMeG0).

# Vision and the State of the Art

When I started this meetup, my long-term goal has always been to establish some "Architect's Corner", where we have gathered the critical mass of high-profile, experienced, and motivated people who enjoy learning from each other, sharing the knowledge, and building something fun together.

I'd say we are about half way there.

On-air events are still mostly me preparing the content and presenting it, answering questions from you folks both during and after the recorded part. On top of this, we manage to have roughly two off the record gatherings per one on-air event that we release. Quite often, the conversations we have on those off the record events have to do with architectural problems that we are facing in real life.

Thus, if you have a System Design challenge at work, and, on a high level, it is not proprietary and confidendial -- do feel free to bring it to the meetup! Chances are, most of us will be genuinely curious, and, at the very least, we would be able to offer a well-rounded perspective. We would ask many questions, including from the angles that you may have not encountered before. And, if we are lucky, you may discover a worthy approach that you have not thought of yet.

# Educational Designs

We are looking into working on designing a real-life system, or a few of them, in a meetup-orchestrated crowdsourced fashion.

_[ This is the early stage effort, and the below is subject to change. In a way, it's an RFC. Also, it should probably be moved into a separate `.md` file. ]_

Here are some core principles I would like to adhere to while doing so.

1. **Distributed First**.

   The theme of the meetup is system design, and I personally understand system design primarily in the context of distributed dataflows.

   When designed properly, each component can fail, and the state machine that governs the system should remain logically unified yet physically distributed, so that it can correctly satisfy all the functional requirements and do its best to fit the non-functional ones.

   Also, the distributed-first paradigm enables implementing individual components of the system in different programming languages, to then compare their performance in various cross-combinations.

   A well-designed distributed system is a rare example of something that is far greater than the sum of its parts. The principles by which we design it should result in systems that illustrate this thesis.

2. **Technology-Agnostic**.

   Since the focus is on what happens between components, we should strive to build individual components in the least opinionated ways possible.

   Language-agnostic comes first. Independent components should be able to communicate regardless of the programming language in which each of them is implemented. In fact, I expect most components to be implemented in more than one language.

   Docker is a must have these days, whether we like it or not. Ideally, each component should be designed such that it can be built & run on bare metal, or packed into a Debian module or into a unikernel of its own, or even be coupled with other components into a single combined piece. Still, for the purposes of keeping the design clean, each component should come with a `Dockerfile`, so that a `docker compose`-based setup can be natively used to spin up, run, use, and test the entire constellation.

   In particular, if a certain component requires warmup, or has nontrivial dependencies, we implement these in a Docker-native way first, using health checks and additional containers so that the dependency graph is respected by any well-designed test.

   I also want our components to be agnostic with respect to now-common standard building blocks. There should be no "Database" or "Cache" or "Message Queue" or "Leader Elections" components, and there should be no Postgres/MySQL/Mongo, Redis/Memcached, Kafka/RabbitMQ, or Consul/etcd ones. Instead, each component should serve a specific well-articulated logical function.

   Of course, certain implementations of certain components would effectively be thin wrappers over standard building blocks such as Postgres or Kafka; by no means we plan to re-implement their functionality from scratch. What is important is that our components are logical building blocks, tailored to our needs, not to what a particular existing solution such as Postgres can provide. And the API contract for our component should highlight its function, such as "transactionally update X", or "broadcast state change Y", not its implementation detail such as "run this SQL query" or "update this Consul DNS record".

   This should be done such that it is possible to implement the same functionality using multiple underlying building blocks, and effectively compare them to one another, instead of fixating on a particular standard component as the "building block of choice". However tempting it may be, no design we provide should be set on using any particular technology as the backbone. In fact, if a particular technology appears to be a perfect fit, then we likely are either not attacking a generic enough problem, or not thinking of it broadly enough.

3. **Contract-Centric**.

   By contract-centric I mostly mean API-centric, but I keep teaching people to reason in terms of contracts, not APIs. Focusing on the specs of APIs of individual components often is about not seeing the forest behind the trees, and The Contract is a far more encompassing term.

   I would like every module to expose its main interfaces via HTTP/JSON first. This enables demonstrating the core functionality of each individual component from the command line right away, which is a good habit to have, and a great design pattern overall.

   The next step is the gRPC interface, the de-facto standard of language-agnostic service-to-service communication.

   From there it's uncharted territory. Personally, I am down for going all the way, including raw binary protocols and client libraries for major languages when it comes to setting performance records. It is more important though to get the big picture done right first, and HTTP/JSON is good enough for most intents and purposes, even if they are poor fit for a real-life large-scale system.

4. **Tested Through**.

   Testing is a big topic, but this set of principles would be incomplete without it. At the very least, the implementation, both of individual components and the whole system, should provide the following tests.

   Unit tests: To ensure individual, isolated pieces of logic are correct.

   Integration tests: To test how the pieces work together.

   Smoke tests: To demonstrate that the "happy path" of the core functionality is fully operational with no apparent side effects such as memory leaks or performance degradation over time.

   End-to-end tests: To ensure that deviating from the happy path, at least in several hard-coded scenarios, results in the respective failsafe & self-healing mechanisms correctly coming to play at the right time.

   Performance tests: So that various implementations can be compared head-to-head, and so that, once it comes to this, we can reprodicibly demonstrate absolute performance numbers that are worth being proud of.

   Stress tests: So that we can put the system into various non-standard modes of operation, and validate, at least probabilistically, that it does satisfy its functional requirements.

   Property-based, exhaustive, model-based, and proof-based tests: Would be great to get this far, but no promises.

   Other types of tests, including, but not limited to, component tests: We should have a meetup episode some time soon about different test types. I have converged to understanding that the terminology is flaky these days, and the content of the tests is more important than their categorization. Rest assured, we will have all the tests necessary to convince both ourselves and the external observers that we are doing the right thing!

5. **Open and Extensible**.

   Unless we agree otherwise on a case-by-case basis, the design and its implementation is open under the MIT license. And the skeleton of each end-to-end system that we build should be fully functional when composed only of the openly released components.

   It is perfectly fine to have proprietary, closed-source pieces of code designed to be injected into any component that we open. Ideally, no forking should be required if a business would like to use our system, or any of its components, as we aim at providing enough hooks to extend our code without having to hack into it on a low level. We would much rather have these hooks generic, and maintained version-to-version, to make our solutions attractive for production use, so that us making improvements does not result in other authors fixing the "glue code" on their side.

   This, of course, would happen on a case-by-case basis; for example, I do not expect a plugin for a particular proprietary tool to be implemented in all the programming languages that we use.

   Last but not least: of course, we do not constrain ourselves, or anyone else, really, from making money based on our work. If we build something useful for educational and/or production purposes, great news, even if you never tell us who you are and what you are building. Of course, if things go well, I am hoping that the core contributors of the respective components on our side would be the prime candidates to be contacted, for educational, non-profit, or for-profit causes.

6. **Production-Grade**.

   This extends the previous principle on testing.

   Of course, all tests should be runnable (and should be run!) on Github by its runners. Of course, it should only take one command to run the test on a local machine.

   For truly distributed runs, if and when we have the resources, we should design our systems so that running a full-blown end-to-end or performance test in a properly distributed setting, such as AWS or Azure of GCP, is a straightforward task.

   Effectively, the system that we have collectively designed should be ready to try out. And, if someone would like to use it in production, they should immediately feel right at home after trying it out, and be confident that they can put together a POC to see if it fits their needs in no time.

7. **Simple**.

   Our systems should not be bloated. Instead, we strive for doing one thing and doing it well, both in the scope of an individual component, and in the scope of their constellation.

   In particular, this implies that we do not introduce heavy components unless necessary.

   A container that stores text files (or maybe an official "fake S3" container) is good enough for storing logs. A quick `gnuplot`- or `graphviz`- or `d3`-enabled renderer of a certain rich JSON payload is preferable compared to Grafana. A reference command with `curl -s`, `tail -f`, `grep`, `jq`, and `awk`, copy-pasteable from the status page, is better than a monstrous component with a sole purpose of enabling the respective functionality.

   Some [Swagger](https://petstore.swagger.io/) or [Metabase](https://www.metabase.com/demo) or [Streamlit](https://example-time-series-annotation.streamlit.app/) would probably be at about the level of complexity and weight-to-value ratio that I think is worth it for our purposes. Anything lighter that introduces more value is likely worth it. Anything heavier that does not bring immediate value is probably best to be avoided.

8. **Beautiful and Presentable**.

   Last but not least: our systems should be a thing of beauty for an engineer looking at it.

   Myself, I am not a frontend guy, and thus I am not expecting fancy interactive dashboards.

   But some `http://localhost:8888` should be self-describing for most individual components, and even more so for the orchestrators. There should be links that are easy to follow, between the status pages of these components, and to/from their metrics pages.

   Those status pages should cite example usage commands, and/or links to the test-running orchestrators that are injected into our `docker` topologies specifically to illustrate the functionality and the performance of the whole system or its parts.

   We do not aim at pleasing non-engineers. But curious engineers should encounter no obstacles on their way to discovering what a thing of beauty we have built, should they decide to clone our repo and give it a shot.

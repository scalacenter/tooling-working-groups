# Scala LSP Meeting Minutes, 11 January 2018

In attendance: Jon Pretty, Bill Venners, Jorge Vicente Cantero, P. Oscar
Boykin, Dale Wijnand, Martin Odersky, Heather Miller, Ólafur Páll Geirsson
(Olaf), Eugene Burmako, Nikolay Tropin, Stu Hood, Seth Tisue, Adriaan Moors,
Eugene Yokota, Gabriele Petronella, Alexey Alekhin, Iulian Dragos, Vladimir
Polushin, Shane Delmore, Guillaume Martres, Rory Graves, Martin Duhem, and
others listening only.

Chair: Jon Pretty

These minutes are an approximate reflection of the proceedings of the meeting.

## Introduction

Jon welcomes everyone to the meeting, and gives brief instructions on how
people should raise discussion points to minimize crosstalk during the call,
and what should happen in the event that his connection drops.

Jon starts by explaining the background to the meeting, which was proposed by a
sponsor during the last Scala Center Advisory Board meeting. The people who
were invited was everyone who was deemed relevant to the conversation.

Jon notes that LSP has both a generic and a specific meaning, distinguishing
between the LSP protocol created by Microsoft, and other interactions between
tools which aren't covered by the Microsoft proposal, and that the meeting is
to discuss both of these ideas. The term "LSP" should refer to the
Microsoft-specified protocol, and the term "BSP" (build server protocol) should
be used to refer to other interactions between tools.

Jon notes that it's historically been very difficult for the open-source
community to be successful in tooling development from an organizational
perspective, and it doesn't play well with shipping a finished product by a
particular date. There's an opportunity for the Scala Center to play a pivotal
role in the organization of the work.

He also explains that different open-source developers have many different (and
innovative) ideas about how tools should be designed, and decisions will need
to be made on what can be supported quickly by any LSP or BSP proposals, what
we should aim to support, and what should be considered out-of-scope.

He continues to say, these decisions are not expected to be made during this
meeting. The intention of the meeting is to determine the framework within
which these decisions can be made, how to avoid the fragmentation that Open
Source is prone to. He suggests that a successful solution will involve a
critical mass of people unifying behind a single idea.

Jon reminisces that in nearly fifteen years in the Scala community, Scala has
never had good tooling, and this has held the language back, but the LSP
project is an idea which already has momentum and enthusiasm that Scala can
build upon, and this is a unique opportunity for Scala to have a future which
includes rich tooling.

He cautions the attendees to be aware of the "sunk-cost fallacy", which he
describes as the feeling that a solution utilizing work already invested seems
better than a different approach that reaches the end-goal faster, but does not
make use of the earlier investment, though the latter is the better solution.
He is aware that this may require some tooling developers to leave behind work
they're already invested heavily in, and that this may be both difficult and
necessary.

Jon is especially glad that Martin Odersky is able to attend the meeting, as
having his blessing on any proposals to arise from the meeting will be
important in getting support from elsewhere in the community.

## Existing projects

Jon invites many of the people already involved in Scala tooling to speak about
projects they're involved in, and notes that this is a very important part of
the discussion to ensure that everyone involved is brought to a common
understanding of the existing tooling ecosystem.

### Scalameta & SemanticDB - Eugene Burmako, Twitter

Scalameta has almost 50 open-source developers, and has been funded by EPFL,
JetBrains, Scala Center and Twitter. Scalameta provides APIs optimised for
tools, designed to be cross-platform, and to work with multiple compilers. It
provides a syntactic API and a semantic API. SemanticDB is an interchange
format for Scalameta based on protocol buffers. A SemanticDB document
corresponds to a source file. SemanticDB can be generated with a compiler
plugin. The file contains full details of symbols, and how identifiers resolve
to symbols.

Eugene shared a link to a Scalameta [pull
request](https://github.com/scalameta/scalameta/pull/1220) detailing the
SemanticDB specification.

There's a SemanticDB plugin for Scalac 2.11 and 2.12. Many open-source Scala
projects have been indexed already, including all of Twitter's code. It enables
Scalafix, Metadoc, Metals and several internal tools at Twitter (which Eugene
is not at liberty to talk about). Processing SemanticDB data does not require a
running compiler, and is very fast, even for large codebases. The information
can be put into an SQL database. As an example, two million lines of code can
be indexed into an 800MB SQLite database, and offers 10ms access times.

Eugene shared the [slides from his presentation](http://goo.gl/TsFfqC).

### Metals - Eugene Burmako, Twitter

[Metals](https://github.com/scalameta/metals) ("Scala*meta* *L*anguage
*S*erver") in an LSP implementation based on Scalameta, developed primarily by
Ólafur Páll Geirsson, Gabriele Petronella and Alexey Alekhin. Olaf was not able
to attend the full meeting, so Eugene Burmako presented on his behalf.
Unfortunately, this part of the presentation was not fully captured on the
recording, but Olaf shared a document showing some of the [features provided by
Metals](https://geirsson.com/assets/metals/).

### Bloop - Jorge Vicente Cantero, Scala Center

Bloop is a build server developed by Jorge and Martin Duhem, integrating with
all build tools including sbt, Maven and Gradle, which can be considered an
small enhancement over the existing Scalac compiler. A stable release will be
available soon.

Jorge also shared a document detailing a draft proposal for a [Build Server
Protocol](https://github.com/scalacenter/bsp/blob/master/docs/bsp.md) or "BSP".

### Sbt LSP implementation - Eugene Yokota, Lightbend

The sbt LSP server predates Eugene Yokota starting at Lightbend: there has been
an sbt server since 2015, with separation of front-end and backend, running on
two JVMs. A simpler approach was proposed in 2016, running on a single JVM. The
server can be thought of an event-based CQRS system, where commands arrive, and
responses are asynchronously returned.

While sbt as a build tool has full knowledge of the build (source, Zinc,
libraries, tests, extensible plugins) and acts as text-based IDE, there is a
growing demand for richer development environment (jumping to error position,
code navigation, tab completion, visual debugging, etc). Eugene sees LSP to be
an intersection of these two, and the relevant feature in sbt 1 is called
"Event Logging".

Eugene recommended reading his blog about the [Sbt Server
Reboot](http://eed3si9n.com/sbt-server-reboot), details of [sbt 1.1.0 with sbt
server and slash
syntax](https://developer.lightbend.com/blog/2017-11-30-sbt-1-1-0-RC1-sbt-server/)
and shared [his slides from the
meeting](https://www.slideshare.net/EugeneYokota/sbt-server-lsp-discussion-2018-jan).

### Pants - Stu Hood, Twitter

Pants is a build tool which has recently reached 5000 commits, and has just
become a nailgun server. Work is ongoing on remoting. Parts of Pants are
implemented in Rust, and lots of work going into improving throughput. Stu
talked about "@rules", and gave a demonstration of Pants running. Pants also
offers dependency inference from source-code imports.

He emphasized that he thought a "thin" LSP model would be the best approach,
where a build tool proxies the thin LSP protocol to a thin LSP server to invoke
a presentation compiler.

Stu shared [his
slides](https://drive.google.com/open?id=1GbViJxPsrNfbYgOXyF1aFSz20PwmVtmc).

### Bazel - P. Oscar Boykin, Stripe

Bazel is a build tool developed by Google focussing on fast, pure, reproducible
and correct builds, taking heavy advantage of caching. It's been used at Stripe
for two years, and supports multiple different languages. Bazel uses external
source dependencies.

The build language is a restricted subset to ensure reproducibility. Google
have a team of fifty people working on Bazel.

### Ensime LSP server - Vladimir Polushin & Rory Graves

Vladimir had a bad connection during the call, but after the meeting he
provided the following notes on the Ensime LSP server.

Ensime supports IDE features in non-IDE editors, it uses a built index and the
presentation compiler like LSP/Scalameta. Ensime supports LSP out-of-the-box
now, but not all clients have been migrated to it. The LSP implementation is a
port of Iulien Dragos's original glue layer implementing all of the same
features. It is purely focused on IDE support (completion, reverse lookup,
etc), and does not manage building features.

It was funded by the sponsorship program which also supported a shared LSP
client for VSCode/Atom, though this funding has basically run out. Ensime has
support for exporting build info from sbt/Gradle/Maven.

The indexer is the key component for many of the LSP semantic calls, and this
should move to using SemanticDB as an interchange format. To provide all of the
IDE features you need more than just the protocol layer, much of which would be
shared.

Rory added, during the call, that Ensime creates an index, much like the
SemanticDB index, but predating SemanticDB. Ensime could support all clients
once LSP support is available.

### IntelliJ IDE - Nikolay Tropin, JetBrains

Nikolay is the team leader for the Scala plugin for IntelliJ, which provides
support for Scala in IntelliJ. The IntelliJ plugin does not depend on Scalac,
instead using its own implementation of the Scala typechecker for providing
presentation support in the IntelliJ editor. IntelliJ also offers Java support.

JetBrains are very interested in LSP and BSP, though they're not sure whether
they can use it in IntelliJ, which currently relies on their own
implementation. He noted that Bloop may be particularly useful for IntelliJ,
though is uncertain about whether it could easily become a replacement for
IntelliJ's own typechecker, or to remove their existing integration with Zinc.

Jorge added following the meeting, that he is working with Justin Kieser from
JetBrains on implementing BSP suport in IntelliJ.

### ScalaIDE/Eclipse - Iulian Dragos, Triplequote

Iulian has been working on the LSP implementation for VSCode, based on Ensime.
The implementation delegates the protocol to an in-process Ensime instance,
circumventing the socket protocol.

This approach avoided many of the problems Iulian faced working on the ScalaIDE
for Eclipse (which he spent approximately four years on). The ScalaIDE used the
Scalac presentation compiler directly, through an asynchronous API that was
able to interact directly with the typechecker. It worked happily with
incomplete on incorrect programs, providing errors as the user typed, and
offering early feedback.

All ASTs were available to the IDE, and offered advanced, precise and rich
information. Unfortunately there was a tight coupling between the plugin and
the compiler. Memory requirements were high, because each different classpath
required a different instance of the compiler, without sharing of (for example)
symbol tables. Iulian is very much in favor of the language-server approach as
it avoids many of these problems, though having a presentation compiler also
provides many benefits, particularly for providing feedback for incomplete or
non-compiling programs.

### Dotty IDE/VSCode - Guillaume Martres, EPFL

Any Dotty project built with sbt can launch VSCode with a single command, and
will automatically provide common IDE features like code-linking and
completion. Guillaume suggests that he would prefer the editor to interact
directly with the language server, rather than having the editor interact with
the build tool (which then interacts with the language server), and that this
approach has been accommodated in the [Build Server Protocol
proposal](https://github.com/scalacenter/bsp/blob/master/docs/bsp.md) developed
by Jorge and Olaf.

Guillaume also spoke about syntax highlighting, and debugging support in IDEs.

### Dotty and TASTY - Martin Odersky, EPFL

Martin had to leave the call early, so he talked about the Dotty compiler
slightly earlier than scheduled.

Dotty has a presentation compiler, which he considers important for providing
information in the case of errors, but the editor also needs support for
building. Currently sbt has to be spun up in a separate terminal. He proposes a
protocol where *n* editors can talk to *m* build tools, but would start with
just a few.

Martin suggests that the presentation compiler could have configuration to
start up any build tool, possibly with an overridable favourite, and then talk
to that build tool through a common BSP, which would share the classpath and
other configuration.

He proposes starting with sbt, which already has an LSP implementation, but
inviting other build tools such as Bloop to use the proposal.

Jorge noted after the meeting that the Scala Center is already working with the
sbt team at Lightbend to implement a bsp server prototype in sbt.

TASTY is a way for the compiler to serialize its abstact syntax trees. Any file
that Dotty compiles will produce a TASTY tree. Any TASTY trees from files that
have been built will be available to clients, quickly, whereas any files which
have not been built successfully would need to have that information provided
by the compiler by some other means. Martin is concerned about the
out-of-the-box experience for newcomers, and thinks that it's essential that
feedback is provided early for these users.

Martin is extremely excited by all of the development, and hopes that all the
efforts can come together to produce something really great!

## Compilers

Jon introduces the next section of the meeting by observing that there are
different Scala compilers for variants of the Scala language, and that now is
the best time to be considering support for Scala compilers other than Scalac,
rather than trying to retrofit support at a later date, even though the vast
majority of users will be using Scalac 2.x.

He sees a strong desire from other Scala compiler developers to have good
tooling, and with that a strong willingness to contribute to efforts like this,
so we should do everything possible to work together.

### Scalac - Adriaan Moors, Lightbend

Adriaan reiterates that tooling will be key to the next wave of Scala adoption,
and wants to encourage the current efforts.

Scalac originated as a batch compiler, and adapted into an incremental
compiler. Dependencies for fine-grained compilation are different for Scala
than for, say, Java. TASTY and SemanticDB are useful for exchanging that
information. He's very happy to help enable the development of these features
for Scalac.

### Rsc - Eugene Burmako, Twitter

Rsc is an experiment aiming to get 5-10x compilation speedups. It currently
only supports a subset of Scala, and provides partial support for typechecking,
but is already showing [impressive
performance](https://github.com/twitter/reasonable-scala/blob/99f55744fe31181544ff563b7344ac22287da895/docs/compiler.md#summary).

Eugene believes that it will be possible to typecheck methods in parallel
during compilation, and notes that separating name resolution and typechecking
is essential for a good user experience. Rsc cross-compiles to Scala Native.
Eugene spoke briefly about some future plans for Rsc, described in more detail
[in his slides](http://goo.gl/LHmuym).

### Hydra - Iulian Dragos, Triplequote

Hydra is a Scala compiler that aims to be a drop-in replacement for Scalac,
focussing on compile performance. Everything in Scalac from 2.11.8 - 2.12.4 is
supported. The language-server front-end should not need specific knowledge of
Hydra.

Regarding building, special integration would be required, but it still aims to
be a drop-in replacement for Scalac with a few additional options. Any build
protocol would need to accommodate those additional parameters (e.g. specifying
the number of cores to use for compilation).

## Open discussion

Jon noted that the open discussion was the least-structured part of the
discussion, and should attempt to work out the scope of the project, rather
than to solve the problems now, and advises everyone to avoid technical
discussions.

Jorge makes everyone aware that he and Olaf have been working on a build server
protocol, the "BSP", which he sees as important for supporting certain
features. The language server communicates with the build server and asks it to
perform certain tasks, like compilation or requesting metadata. There is
already a working integration with Bloop.

Jon asks how the BSP could work with Pants and Bazel, and Jorge confirms that
they should be compatible, as the BSP proposal has been expressly designed for
this purpose.

The BSP proposal has followed the "style" of the LSP protocol.

Rory Graves made the point that different components have different
responsibilities, and he encourages a modular approach with a "single point of
entry" or a "busbar" for a number of different tools to communicate through,
which holds the different components together. Different components or editor
features have, and need, different subsets of the information available. So
rather than having a thick component, a thin "shim" which defers to
"information providers" would be preferable. Stu agreed with this approach,
though thinks more thought needs to go into which process receives the
connection from the client, i.e. which process is the "hub" or "bus".

Eugene Yokota notes that sbt can provide a lot of details like type
information. He notes there are two approaches, the SemanticDB and the
presentation compiler, and asks where they can collaborate. He says that, as he
sees it, the presentation compiler can work with incomplete data, but it can
suffer from performance problems, and asks for clarification.

In response, Stu Hood thinks that it's entirely possible for the SemanticDB to
provide partial information. Regarding the "thick" vs "thin" model, he
suggested using a build tool as the "hub", which was essentially the same as as
the "bus" model proposed by Rory. He points out that the LSP server does not
need a classpath.

Jon sees the question more about whether we should standardize on a model, or
whether this is something every build tool should define for itself. Stu
suggests that it should be just "simple extensions" to the LSP protocol. Jon
asks whether it might be simpler to treat the LSP and BSP as orthogonal
projects, and Stu and Jorge both agree.

Jorge and Stu debate some of the interactions between LSP and BSP. Stu notes
that the LSP server does not need a compiler. The discussion goes into some
technical detail, without reaching a conclusion. Stu would like LSP servers to
be thinner.

Jon tries with limited success to keep the conversation focussed away from
technical details.

Eugene Burmako added that he sees SemanticDB as a useful standard that works
for both complete and partial information. He points out that it doesn't have
to be persisted, so it can be exchanged in-memory, and that it's used to
index third-party Scala and Java dependencies in Metals. It is used to
communicate with the presentation compiler: the presentation compiler can
produce SemanticDB data. Eugene suggests that SemanticDB could be a good
standard for exchanging information between tools.

Jon suggests that the LSP proposal is well-defined, and implementations exist,
but suggests that the BSP should be designed to be modular to support more
heterogeneous features. He suggest that different people could be involved with
working on the two proposals.

Shane Delmore asks about timescales for development, and asks what we're trying
to support, and mentioned that people may want to work on projects which
interoperate with other languages. Shane asks whether TASTY works currently
with Scalac 2.11 or 2.12, and Adriaan says there's currently no schedule for
including TASTY support in Scalac 2.13. He's open to it, but thinks it's more
of a Scalac 2.14 target.

Jon claims (in Martin's absence) that Martin didn't think it would be a huge
amount of work to get a basic implementation of TASTY working for Scalac though
challenges would exist in deciding the exact representations of Scalac
structures in a format designed primarily with Dotty in mind, and Adriaan
thinks that it's a challenge to get TASTY to scale between Java, Scala and
Dotty.

Jorge suggests some solutions for cross-language support.

## Next Steps

With limited time remaining in the meeting, Jon makes a proposal that two
working groups should be formed to work on the LSP and the BSP, made up of
different people with some overlap between the two, and that the working groups
should come under the remit of the Scala Center.

Jon notes that the work of the Scala Center should be recommended by the
Advisory Board, and that it would be good to have a proposal from one of the
sponsors so that an official vote could be taken during a Scala Center Advisory
Board meeting, proposing that the Scala Center support the initiative.

Jon suggests six to eight people should be in each group.

Stu suggests a more generally-titled "protocols subgroup" as opposed to a "BSP
subgroup" to ensure that all options are considered before choosing any
particular approach, and suggests that Twitter could put in a proposal for the
next meeting.

Jorge was skeptical that we had a good definition of what problem the groups
should be solving, and Jon advises that this was never going to be possible in
the limited time available during the meeting.

Jon agreed to take the next steps offline. He will take nominations, and will
aim to agree on what the remit of each group should be.

Eugene Yokota sees two separate axes, one of which is to give Scala users a
better working experience, while the other is to support different build tools.

Adriaan suggested that GitHub would provide a good venue for the working
groups.

Jorge was interested to know how many people would have time to support LSP or
BSP, though Jon says it's not possible to gauge this during the meeting, though
it would be useful.

## Close

Jon thanks everyone for attending, and reiterates that it's been a useful
conversation, and hopes that it paves the way towards a vastly improved tooling
experience for all Scala users.


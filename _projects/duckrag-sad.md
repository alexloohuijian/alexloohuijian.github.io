---
layout: page
title: "Project DuckRAG: Software Architecture Description (SAD)"
permalink: /_projects/duckrag-sad/
---
# Software Architecture Description (SAD)

Project Name: Project DuckRag(working name)  
A design document for an application to automate the process of setting up a RAG pipeline to a rubber duck conversational windows, with intent awareness

# Document Versioning

Pre-alpha01 (21/8/26)

* Initial document

Pre-alpha02 (26/7/26)

* Reframed project goals  
* Cleaned up stretch goals  
* Defined Meta-Invariants  
* Added glossary

Pre-alpha03 (8/9/26)

* Added some QnA  
* Phased out DSSP

# Section 1: Introduction, Scope & Constraints

## 1.1 Introduction

Project DuckRag is a developer utility application for automating the process of hooking a codebase/repo to an offline database, for the purpose of allowing developers to utilize RAG for their LLM assistants, as an alternative to context stuffing.

It is to be noted that unlike most existing RAG tools, this project’s primary objective is NOT to provide coding assistance, although it can still do that, but to allow developers to consult an AI about their codebase’s design and architecture, and to help with development alongside the design intent, instead of whatever works best and fast. Hence the name DuckRag, it is meant to be an AI Rubber Duck (& consultant).

## 1.2 Architectural & Engineering Goals

This project has 2 distinct yet complementary goals:

* Primary Objective: Deep Learning & Understanding  
  * The main goal of this project is to learn and understand several fields that hold my interest:  
    * Systems Programming  
    * Rust as a language  
    * Data Oriented Design  
    * LLM context management and related  
  * As such, many aspects of the project which could probably be solved with a few lines of imported code are being reinvented for the sake of education.  
* Secondary Objective: A Useful Tool  
  * The amount of effort required for a project like this is non-trivial, so something that can be used at the end of the day would be good  
  * The endgame of the project would be a tool that would allow a developer to rubber duck with their codebase while stuck somewhere where they can mess about with their phones, but not pull out a laptop, like a train ride, or waiting for an appointment.  
  * A well-documented and fully-featured tool would also look real nice on a portfolio.

In order to achieve the above goals, there are several fields we will try to focus on:

* Developer/User Experience (DX/UX)  
  * As the application will be targeting developers as the user, it makes sense that not only should the application have a good UX, but a good DX as well  
  * Primarily this will boil down to making the code as easy to read, understand, and work on as possible.  
* Forkability  
  * This project will likely have a fairly limited scope, in terms of OS platforms and languages supported, so it should be designed and developed in a way to make it as easy as possible for other future developers to add in their own additional functionality  
* Maintainability  
  * Due to the expected yet inevitable abandonment of this project by the original author, this application should be made to be as easy to maintain and update as possible  
* Engineering Excellence   
  * This project is developed with high software engineering discipline in mind. The primary purpose of this project is actually for educational purposes, although having a finished product that is easy on the eyes is nice to have.

## 1.3 Project Vision & Goals

“Context Stuffing” is a trick that many developers use, in order to get an LLM to be able to understand their code, so that they can get help. However, it comes with several downsides, such as rate limitations and middle context decay.  
RAG is a method in which an LLM can grab only relevant information, so that their context windows can stay as free as possible, but it requires a surprisingly large amount of legwork to get started.  
DuckRag is meant to help with these issues to help painlessly and automatically set up databases from local repos. In short, its a little like a ghetto github copilot, but with a Bring Your Own Key concept, so the user can use whichever AI they want, as long as its Gemini, for now at least, but the idea is that we can extend the Project to accept other agents.

**Functional Requirements:**

* Code Aware Chunking: The System shall be able to chunk code into reasonable pieces, without splitting them apart in the middle, while maintaining code syntax integrity  
* Metadata Tagging: The System shall create proper metadata tags for chunks to allow for more efficient RAG data retrieval for cheaper and more efficient token usage. This includes things such as function scope, children, class etc  
* Incremental Diff Engine: The System shall be able to only need to reprocess changed files, so it does not need to reprocess anything that wasn't changed  
* Standardised Agentic Interface: The System shall allow for the user to select whichever model they want to use.  
* Intent Awareness: The System shall allow for users to define a design document, with Architectural Decision Records(ADRs), invariants and other design intentions.

**Non-Functional Requirements:**

* Configurable AST/Token Intensity: The System shall allow for the user to set how much of their code they want to send at a time, to manage their own token budget to verbosity ratio  
* AOT Compilation: The System shall be AOT compliant from the very beginning, in order to allow for more flexible future ports to other OSs or even mobile devices.  
* Hybrid Code Recognition: The System shall not use just vector similarity. After the initial vector similarity check, the System shall use metadata tags generated during the chunking phase, to identify parents, children and sibling functions for submission to the Query AI  
* Ghetto Copilot: The System shall use tricks such as giving higher weights to recently modified files and currently viewed files.  
* Dual Mode Diff Detection: The System shall use quick methods, such as comparing a file’s name, size and date modified, to guess if it has been changed, and then perform a hash check to confirm. It will also use tricks to check against the cache to make sure its not just a file rename or move.

**Stretch Goals:**  
**Stretch 1 (Extra Features):**

* Avalonia/GUI Frontend: The System shall be designed to allow for eventual adoption of other front ends beyond the MVP  
* Session Refreshing: The System shall allow for users to automatically or manually trigger a “Session Refresh”, where the system asks the agent for all the context they think is important to bring into a next session as well as any most current relevant code snippets, to refresh the agent’s context and flush out older prompts.  
* Response Parroting / Socratic Method: The System will have optional higher thoroughness settings, which will allow the Program to first run the Query AI’s initial response back to itself, in a new Query AI conversation alongside the golden rules and relevant rules, to double check its output  
* Duck Brain Telemetry: The System will allow for transparency for the user to see what the Program is currently trying to do, as well as stream the Query AI’s live thought logs, the audit process etc  
* Conversational Sharding: The System will use the Embedding AI to check the user’s input, to see if it is regular questions, or something more conversational, and route the output to the appropriate Query AI conversation session.  
* Duck Scribe: The System will use the Query AI to help the user polish their own SAD, by questioning if certain rules should be added to the SAD. THe system can also use it as a cold start, if the user does not have their own SAD, or if it is too unstructured to be useful, by interviewing the user what they want out of their system, and backporting the requirements into rules.

**Stretch 2 (Mobile Portability):**

* Rubber Duck Distillation: The System shall allow for users to continue their rubber ducking sessions on mobile, with a thin mobile application that can still reference code snippets on the user’s home machine, but without the ability to vectorise new code.  
* Thin Mobile Port: The System shall be designed to allow for a future thin mobile client

**Stretch 3 (Other Systems and Beyond):**

* Swappable Embedding AI: The System shall allow for multiple modes of embedding, including using a different cloud AI as the Querying AI and offline models.  
* Other Operating Systems: The System shall be designed to be portable to other operating systems  
* Polyglot Implementation: The System shall be designed to allow for reading through codebases in other languages as well

## 1.4 System Scope & Boundaries

**In-Scope Features:**

* Read local codebase/repository  
* Code Chunking  
* Code Vectorisation  
* Prompt Vectorisation  
* Prompt-Code Comparisons  
* GUI for LLM interfacing  
  * A simple chat window for chatting with the LLM  
* GUI for Code comparisons, highlights etc

**Out-Scope Features:**

* IDE  
  * This will not be an IDE, it should only allows users to hook up a codebase for LLM queries and consultations

**Initial Targets:**

* Operating System  
  * Windows  
* Code Languages(To be operated on)  
  * C\#  
* LLM Interfaces  
  * Google Gemini

## 1.5 Project Flow

**Phase 1 (Init/Diff Update)**

* Chunker  
  * Program reads codebase, then chunks and tags them into chunks  
* Codebase Vectorisation  
  * Embedding AI goes through the chunks, and vectorises them

**Phase 2 (Runtime)**

* User Query  
  * User plugs in their favourite AI API key and question  
* Query Vectorisation  
  * Embedding AI vectorises the user’s question  
* Chunk Matching  
  * Program compares the user’s question as a vector, with the codebase and grabs the top 3 or however many chunks  
* AI Query  
  * The program submits the user’s question alongside the chunks  
* AI Response  
  * Query AI responds

## 1.6 Meta-Invariants

In order to align with the primary goal of the project, education, below are a list of rules that we will try to follow

**MI-01: Minimal Magic Black Boxes**

* In order to understand more about the processes within the project, we will reject using common high level packages that “just work”, in favor of engineering how they work  
* However, this limitation is only within reason, so we will still use some basic things that don't warrant study into just yet (GUI, ), as well as things that cover areas that are way beyond scope (tree-sitter, command busses)  
* Actually there are still quite a few black boxes about, but time, effort and patience is finite

**MI-02: No Pasted AI Stuff**

* AI is perhaps most infamously known for the low quality slop it produces.  
* However, the author views it closer to a human problem than an AI problem, as AIs don't create AI slop, humans do.   
* Regardless, to avoid the stigma, as well as to truly understand what is being done, no direct copy pastes of AI generated artefacts are allowed in this SAD or project

**MI-03: Mechanical Sympathy**

* There is a trend of throwing more compute and tokens at a problem to fix it, which feels pretty bad, not just from a moral and disciplinary point of view, but an environmental one as well  
* As such, we will try to work with the limitations of the hardware and AIs we have, rather than trying to mask poor performance by throwing more compute at it

**MI-04: Rust Sympathy**

* Similarly to Mechanical Sympathy, there are those who try to fight the borrow checker by applying OPP crutches like (\`Arc\<Mutex\<T\>\>\`).  
* Rust is actually pretty cool in how it works, and by understanding how low level systems work, plenty of what Rust limits developers is actually makes a lot of sense

## 1.7 Questions and Answers

**Q1: This SAD does not look like a Rust design.**  
A: The author’s primary experience was in C\#, but with a Data-Oriented Design approach, and so Rust was chosen as the programming language, but was initially designed with a C\# mindset. Work is being done to clean up any remaining artefacts, but some may still remain

**Q2: This System seems overengineered for just an AI Wrapper tool**  
A: The Project was initially envisioned to be a less hacky version of setting up a code RAG tool for the author’s own use, but subsequently evolved to become the AI rubber ducking tool that it is now. The System was initially overengineered, as parts of the SAD was reused from a previous project for the construction of an Incremental Game Engine. For more information, read the ADR about it below

**Q3: This System seems to take itself way too seriously when it comes to monitoring design intent etc**  
A: This Project takes the “Garbage In, Garbage Out” viewpoint from data science and applies it to LLM usage. If LLM is not made aware of any implicit or explicit rules it needs to follow, it will just produce results that just works, without caring about anything else. This is not a problem with AI, if a human were given just a prompt with no previous knowledge or insight into a program, they too will likely give a response that works, but breaks everything else. 

**Q4: Why is this System so bottom heavy?**  
A: The author has a somewhat “bottom-up” understanding of computer science, as one of the first languages they studied were C and Assembly language, leading to a more “bare-metal” kind of engineer, followed by the natural higher abstraction languages such as C++, C\# and Java. Most of what they understand about AIs and LLMs are built on this foundation, wondering how the heck did we trick rocks into thinking. As such, they view the project through a performance first lens.

**Q5: Wow, a local first code RAG tool. Are you some sort of cyberpunk looking to take back code sovereignty back from the corporate overlords?**  
A: No. I had never really thought about code sovereignty or privacy as stuff that this tool could solve at all when I first worked on this project. However, Gemini and Claude thought otherwise, because apparently being able to work locally is some sort of high signal anti-corpo thing. Regardless, if this project can help anyone feel more secure about their code, I don't mind being a nice guy.

## 1.8 Glossary

* Embedding AI  
  * Refers to the AI that will be in charge of vectorising the codebase, as well as the user prompt.  
  * Will likely be a lighter, faster, cheaper AI, like a local one  
* Query AI  
  * Refers to the AI that the user will actually want to ask  
  * For the MVP, that is Gemini, but other potential Query AI is any big strong and smart cloud AI  
* ~~DSSP~~  
  * ~~Refers to the custom MVC model this project is based one~~

# Section 2 Logical & Structural View

## 2.1 System Architecture and Model

The System follows the Actor-EDA pattern. Actors serve as both simulators, and single writers for any state they are in charge of. Any requests between models are facilitated via an EDA powered by Rust’s tokio.

**Infrastructure**

* Core  
  * The root dependency of the project. All other modules have a one way dependency on Core  
  * Contains traits and DTO definitions  
* Bootstrapper  
  * Our Single Composition Root.  
  * Although the use of command buses in rust allows us to greatly simplify the wiring of components together, there are still some wiring to be done  
  * Creates various command buses, for each of the various inter-module communications.   
  * Exact details of channels, how many, how big etc not decided yet

**Domains**

* Data  
  * Represents long-term static definitions (AST grammar tables, AI prompt schemas, loaded ADR rules).  
  * They are implemented as immutable structs with public fields and integrated pure helper functions  
  * Since they are guaranteed to be immutable, they can be freely shared with the modules that need them  
* Simulation & State (The Rust Actor Model)  
  * Instead of having c\# style dumb DTOs, each module is in charge of its own state, and because Rust strictly enforces ownership of stuff, this means that each module is the single writer of their own states  
  * If any other module needs access to another module’s state, they can still request it via command bus  
  * This may seem to make the simulation stateful, but it actually doesn't, because although the module has a class has access to the state, all functions are pure and stateless, so it stays nice and spotless  
  * Split into 2 logical categories, Hot and Cold modules.  
  * Hot modules are modules which are compute-gated, such as vectorisation and diff hashing, so the faster it is the better.  
  * Cold modules are modules which are not compute-gated, such as responding to user inputs and waiting for AI responses, so it doesn't need to be as highly engineered  
  * Due to the shift to the Rust environment, all simulation modules will have the same basic DOD constraints and stuff, but designated Hot modules will still be subject to more rigor than Cold Modules  
  * Notably when it comes to the Hot modules and the bus, the bus should be used to wake up the Hot module to do its thing, rather than using the bus to send everything that needs doing.  
* Presentation  
  * Completely decoupled from the simulation, and communicates with the simulation back end via a command bus  
  * Reads output data from the Simulation to display to the user, as well as capture user inputs to send to the Simulation  
  * The current MVP is console based, but should still be run in an event based manner, rather than classical stop execution while waiting for next input.  
  * This is to ease up the eventual adoption of other GUIs

## 2.2 Seams

* Embedding Seam  
  * Codebase and user input vectorisation  
* Chunking Seam  
* Vectorisation Seam

## 2.3 Tech Stack

Backend

* Rust  
* tree-sitter

Frontend

* Console  
* Eventual desktop GUI  
* Eventual mobile application

Database

* mmap \+ rkyv

## 2.4 Folder Directory

Duckrag

* core  
  * chunk-dto  
  * newtypes  
* sim  
  * chunker

## 2.5 Technical Invariants

TI-01: Follow the Actor / Event Driven Architecture Model

* It is the core architectural model that this project is developed around

# Section 3: Data & State Specifics

## 3.1 Storage & Persistence: Zero-Copy Memory-Mapped Cache

Instead of using a regular db, or a json, the systems stores each chunk, their metadata and vectors all into a single binary file, for each codebase the user is working on.

**Binary File Structure**  
The binary file is structured in a certain way to make it easy and fast for the CPU to read. Also, due to particularities of doing SIMD stuff, each section will need to be padded out to 32 or 64 byte portions in order to align properly with CPU cache lines

* File Header  
  * File metadata, like date, number of chunks etc  
* Chunks  
  * Chunk metadata, like symbols, relations, filepath etc  
  * Also contains an address offset  
* Vectors  
  * All the giant vectors are stored contiguously and back to back  
  * Uses the address offset from the Chunks table to determine where in the giant BLOB the specific vector is

**Two Tier Memory Map Structure**

* When any changes or mutations inevitably occur to the user’s codebase, the binary file will no longer reflect the true state of the codebase, and any assumptions made based on it will be at best wrong, and at worst a crash  
* We maintain the on disk binary as our Tier 1 storage, and create an in-memory temporary database as our Tier 2 storage  
* This Tier 2 storage will basically serve as a memory delta bank, and be populated with changed chunks and the like, and will have a graveyard section for “dead” sections  
* When we want to read the codebase, we first go to the Tier 2 storage and retrieve the list of dead sections, then go to the Tier 1 storage and read everything except the dead data, then append the dead data in the Tier 2 storage, ensuring that we have the speed of the Tier 1 storage, with the flexible mutability of the Tier 2 storage  
* When the user triggers a save action, it will compactify the Tier 1 and Tier 2 storage, combining them back into a proper Tier 1 storage on disk  
* To achieve basic ACID, we will use the traditional write to a tmp file and perform atomic file rename

**In-Place Zero-Copy Access (\`rkyv\` \+ \`memmap2\`)**

* When the system boots, it uses memmap2 to ask the OS to map the file’s raw bytes on disk into memory. This results in a struct that contains the file’s data in memory.  
* The system then uses rkyv to turn the in-memory struct to create Rust references, without needing to copy again

**Hardware Accelerated SIMD Compute**

* If we were using a traditional database, we would need to individually load each chunk’s vector and load it into the CPU’s SIMD register, which would take a lot of I/O time  
* However, our memory is stored continuously and memory-mapped, we get several benefits  
* The system points the SIMD registers at the mapped memory addresses (our vectors)  
* The CPU then does an in-situ computation, grabbing memory and processing them without the need for a copy.  
* Because our data is contiguous, the CPU’s caches can prefetch the next vectors ahead of time, reducing memory bus latency and cache misses.

## 3.2 Chat Sessions

When the user creates a new conversation in this project, a new persistence .chat file will be created, to help logically separate the Query AI conversation from this project’s one.

**Delta Evaluation**

* The project keeps a log of all previously sent code snippets and ADRs, so the project will know what the Query AI already knows, and does not resend them  
* Only if a diff of the corresponding code is detected, does the project resend the code snippet.  
* This helps with token economisation, as well as avoid ballooning the Query AI’s conversation context

**Synthetic Insights**

* As part of the chat compactification, the project will store the resulting continuation seed within the persistence file.  
* The old session will then be internally archived in the file, so curious users can reread through them to see the evolution in they design

# Section 4: Simulation Specifics

## 4.1 Intent Awareness

An important part of the project is the ability for the incoming Query AI responses to align with the user’s own design intention. 

**Fuzzy SAD parsing**

* There are several SAD writing standards, and most developers don't follow them anyways. (including this one)  
* Instead of forcing the user to adopt a certain format for their SAD, as part of the boot process, the system will send the user’s designated SAD to the Query AI, and request for a structured output  
* The structured output should separate the design document into golden rules and ADRs

**Intent Injection**

* Golden rules are prepended to the Query AI’s System Instruction (or equivalent), so they always keep those in mind.  
* ADRs are referenced if needed.  
  * The system will have several options for how thoroughly this should be processed,  
  * The most basic mode will just use the same cosine similarity as the code chunks to try and match the user’s prompt with the ADRs. Very high miss rate.  
  * A second option is a preflight checklist, where the program will ask the Query AI which ADRs seem most relevant to the prompt, in a separate hidden Query. This introduces extra delay and token costs  
  * The third option is the inflight self routing, where we will use the basic mode, but in super wide mode, and ask the Query AI which one of these seem important. Quick and accurate, but will balloon the primary Query AI conversation

## 4.2 Context Deduplication / Delta Injection

A naive implementation of a code RAG tool would have the same code snippet sent to the Query AI with each user prompt. This results in extra token costs as well as context bloating, which lowers the Query AI response quality.

To avoid this, the project keeps a running log of previously sent code snippets and prompts, and if a code snippet has not been modified by the user since the last time it was sent, it is deemed redundant, and it is not sent to the Query AI, instead asking it to refer to the previously sent code snippet in relation to the user’s current prompt

## 4.3 Conversation Refresher / Context Compactification

As a conversation with any AI goes on, certain pieces of information will become irrelevant to the current and future topics, such as previous versions of the code that has already been fixed. Telling the Query AI to ignore those things is a short term band aid, as it does not delete them from the context, causing them to contribute to context decay.

As such, the project will have a Conversation Refreshener feature, which informs the user when the current Query AI conversation context has reached a certain number of tokens, which will lead to degrading conversational quality.

This will trigger a Context Compactification, where the project will ask the Query AI to provide a continuation seed, a summary passage of everything the Query AI thinks is important about the current conversation, as well as references to any code snippet it thinks is still relevant. This continuation seed is also combined with the latest code snippets, and sent to the Query AI as a new conversation.

This results in a fresh new Query AI conversation, with no irrelevant data in its context, while being seamless to the user, as the project maintains its own “chat” session, that is not vulnerable to the same context decay problems as LLMs.

# Section 5: Presentation Specifics

## 5.1 Non-Blocking TUI

For the initial MVP, we will be trying to create a non-blocking TUI, that does not have the normal weaknesses of normal console programs. The planned tech stack for it will be “tokio”, “ratatui” and “crossterm”. It will operate on an EDA, responding only in response to events, usually user input and feedback from the simulation.

# Section 6: Architectural Decision Records

## ~~6.1 The DSSP Model (Deprecated)~~

**~~Context:~~**

* ~~The project was initially planned to be MVC or MVVM based, but there were several pain points~~  
* ~~Firstly was that ‘Model’ as a concept was too nebulous, as it included both static persistent data and runtime mutable data.~~  
* ~~Besides that the ‘Controller’ was too big, having to manage literally all logic~~  
* ~~Also a ‘View’ infers that it is a passive output, and should also handle user input by right~~  
* ~~It is basically a reinvention of the Frontend and Backend development, but within a single program~~  
* ~~Since the frontend and backend dont care about how each other works, it makes it trivially easy to swap parts around~~

**~~Decision:~~**

* ~~The DSSP model was created after some brainstorming and tinkering with the words.~~  
* ~~The Model was broken up into Data and State.~~  
* ~~Data is for static data only, while State is for mutable data only.~~  
* ~~In both cases, neither Data or State are allowed to have any logic, beyond basic mutators~~  
* ~~The Controller was split into Simulation and Presentation~~  
* ~~The Simulation would be primarily pure functions, with some leftover controller parts to manage the wiring and wrangling of the modules.~~  
* ~~Meanwhile, View was merged into Presentation, which is now basically the front end of the system.~~  
* ~~The Presentation layer is meant to be both for displaying output, as well as reading input. Although how the input is processed is completely out of the scope of the Presentation Layer~~  
* ~~The DSSP model is actually stronger in a DOD language like Rust rather than the original OOP c\# environment it came from~~  
* ~~We don't need anemic data models and tons of manager services anymore~~  
  * ~~Data \- Immutable structs containing the static data~~  
  * ~~State \- Structs encapsulated by the simulation module or actor in charge of it~~  
  * ~~Simulation \- Pure functional modules that is called upon by the command bus~~  
  * ~~Presentation \- Terminal/GUI frontend consuming and emitting events via the command bus~~

Superseded by 6.6 below

## 6.2 Choice of Backend Programming Language:

**Context:**

* The project will require some fairly complex computing, so performance is major concern  
* The project should also avoid the need for extraneous external libraries, to differentiate itself from the current common hacky way of doing code RAG

**Alternatives:**

* Python  
  * The obvious choice for many programmers, due to its ease of use and plentiful public libraries.  
  * Unfortunately it is hecking slow, due to its scripting nature  
  * Its loosely typed variables are also a breeding ground for future spaghettification  
  * Also requires its own runtime environment stuff to be downloaded  
* Java  
  * Actually a pretty strong close contender, with its speed and strictly typed variables, and pretty good library support for what we need to do  
  * However, due to Native AOT targeting, java is not great due to it being very reflection heavy  
* C\#  
  * Relatively quick, amongst other programming languages, and has strict typing  
  * Decent AOT compatibility tooling options  
  * Initial choice actually, but dropped in favor of the decided language

**Decision:**

* Rust  
  * Really fast, due to being basically a systems programming language  
  * Completely AOT, and great heap management discipline  
  * Initially chosen as a way to learn the language  
  * However, after some rubber ducking, it turns out a lot of Data Oriented Design principles are way easier to handle in rust than c\#  
  * 

## 6.3 The Two Cores (Deprecated)

**Context 2:**

* The initial idea was that there might be a need to create c\# style code generators, that would write code directly into the project’s directory.  
* It turns out that rust is clever about its code generation, and can output stuff directly into memory without the need for any artefact creation.  
* Not only that, many of the reasons for needing code generation in c\# was due to targeting AOT compliance. Rust can only be AOT, so it never has anything to worry about runtime reflection etc

**Decision 2**:

* There is now just a Core, with no need for separating handwritten and generated code.  
* This ADR has been rendered obsolete, but remains here for archiving

**~~Context:~~**

* ~~In a previous project, which involved considerable amounts of ETL from a database and definition generation based on that.~~  
* ~~Handwriting that much code was unfeasible and human error prone~~  
* ~~However, it introduced an implicit circular dependency, where the code generators depended on Core, but generated into Core, resulting in a code generator that requires its own output to work properly~~

**~~Decision:~~**

* ~~We separated the Core into a generated Core and the handwritten Core.Contracts.~~  
* ~~Core.Contracts became the true root dependency of all our modules now, and is never generated into.~~  
* ~~This allowed our generator to continue working even if it accidentally bricked the entire main project~~

## 6.4 The Game Engine DNA

Context:

* Large parts of the Project’s SAD were reused from a previous project, which was making an incremental game engine  
* Certain features of the system are overengineered, or excessive for certain functions  
* Traditional AI Wrappers face several engineering challenges, such as:  
  * I/O Latency: Scanning, vectorising and diffing a large codebase can be slow  
  * Compute Latency: Vectorising and embedding stuff take time  
  * Network Latency: Waiting for AI responses, from either the Embedding or Query, will definitely take some time  
* All these latency issues can cause poorly or simply designed systems to freeze or be unresponsive

**Superseded Decisions:**

* Frame Rate and Logic Rate decoupling  
  * ~~Just like how a game engine might render at 60 Hz, but the physics engine only at around 20 Hz, DuckRag can be made to still have a responsive front end running at 30 Hz even if the back end are still waiting on other stuff like the AI API and file indexing(Depreciated)~~  
  * ~~The Presentation loop runs independently of the Simulation loop, and program output and user input are swapped between the front and back end via the triple buffering~~  
  * UPDATE: It turns out that rust does not need a polling loop for checking if events have fired off, allowing us to move the system to a completely Event Driven Architecture

**Decision:**

* The DSSP model designed for a game engine is a surprisingly good fit, as it is actually quite applicable to quite a few scenarios present in this System  
* States as a “Save File”  
  * An incremental game normally has to handle a large number of variables, some of which needs to be extra large to handle the nonsensical number scales expected of them  
  * A code RAG tool has to handle a large number of individual files, which need to be vectorised into extremely high dimensional float vectors  
  * By using the zero-copy for our codebase.bin, we can treat it like a save file, and maintain a highly responsive application, as we are not loading data into memory, we are just mapping it into the engine’s memory  
* “Dirty Flag” pattern  
  * Game engines often have to run on very tight timing budgets to meet their high frequency update targets, so usually only operate on things that have actually changed, rather than everything everywhere all at once  
  * Instead of revectorising the entire codebase each time, or having to do it manually from time to time, we can use the same pattern to only operate on things that need to be operated on in order to save time and compute.  
* Data Oriented Design (DOD) for maximising Mechanical Sympathy  
  * An incremental game is basically giant high frequency spreadsheet simulator, so DOD helps with these large scale number operations go by smoothly  
  * RAG is similarly just a large yet somewhat simple math operation, the complexity often lies in getting the data to where it needs to be to get operated on  
  * Storing the vectors and data into large contiguous arrays the DOD way allows for the CPU to more trivially use SIMD, making it faster than a traditional database, which relies much more on pointers  
* Modder’s Paradise:  
  * The game engine was designed to be easily extensible both codewise and datawise, thus the large number of seams for systems to change without the individual module being aware or responsive to external changes  
  * This framework also allows the tool to be more easily extensible and maintainable

**Consequences:**

* High performance: This tool is projected to be way way faster than Python and Electron based tools  
* Economically Conscious: By applying mechanical sympathy, we work with the hardware, not despite the hardware, reducing overall token usage and compute  
* Maintainability and Forkability: The rigorous separation of concerns via the DSSP model allows for the front end to be trivially swapable, and the back end to easily accept additional languages or models to work with  
* Complexity: The barrier for entry for collaborators may be higher, as they will likely need the same rare mix of low level systems engineering and high level LLM data science to fully understand the project.  
* Resource Usage: The reason most enterprise applications do not use a game engine style heartbeat loop is that it is usually asleep 99% of the time, and only uses compute when something needs doing. If the program’s heartbeat rate is not properly managed, it can easily drain more compute than it saves.

## 6.5 C\# Design Artifacts, or Long Live the Command Bus

**Context:**

* This SAD was initially designed with a c\# mindset not to mention inheriting portions of a c\# project, which left several design artefacts that is not suitable for the rust environment  
* One such thing was the over-reliance on interfaces and contracts, to make sure nobody was allowed to hold concrete references, in order to allow parts to be more plug and play  
* Another thing was that Data and State were allowed to self-mutate as well  
* In order to maintain a single writer pattern in c\#, we used a Registry, which was in charge of holding the state and mutating it for other modules  
* Using a c\# styled sub/pub event handler would require that something checks the event logs and wakes up the relevant modules to go do their job.  
* In c\#, there were a lot of things that needs to be injected into the various modules, necessitating a giant bootstrapper for wiring everything that need wiring together, and preconstructing the things as well

**Decision:**

* Event Hub was completely deleted in favour of the Command Bus  
* Data and State are no longer allowed to self-mutate  
* Registry has been deleted, and state writing is now distributed to each module  
* Because in rust, state belonging to somebody else is inherently off limits, this means that the single module is the single writer for their own data, and anybody who wants their data needs to ask for it (via command bus)  
* Because command buses come with the automagical ability to wake up the recipient, we no longer need a polling loop just for check for events, and we can move entirely to Event Driven Architecture (EDA)  
* In Rust, there are not nearly as many interfaces that need to be injected, so the bootstrapper can be much slimmer, we could even stuff it in main().  
* However, we prefer to keep it in a separate bootstrapper for separation of concerns, as well as other stuff that needs to be initialised.  
* Keeping the bootstrapper out of main() means that the bootstrapping process is portable to other front ends as well

## 6.6 Renaming Build/Architectural Pipeline to Actor-EDA

Context: 

* Using a self-coined term like DSSP is cringe.   
* We only used it in the first place because it did not seem to be an existing architectural model that was exactly like we were doing  
* After some further polishing and reflection, we finally found a proper name for it

Decision:

* The project architecture is now formally known as Actor-EDA, to make it standard and easier to explain

# Section 9: Accreditation, Acknowledgements and Attributions

Project DuckRag will be released under GNU GPL v3. (it isn't actually out yet)


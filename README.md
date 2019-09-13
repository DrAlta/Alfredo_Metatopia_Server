# Alfredo_Metatopia_Server
A P2P virtual world based on [Raynet from Solipsis](https://hal.inria.fr/inria-00337057/document), written in Rust.

I'm Going for something like Roblox with SL-like avatar with CopyLeft sims)

My first though before I decided on using Rust was to mod Lua to use fixpoint instead of floats.

Since it's the VW's purpose is to encourage CopyLeft I want it to distribute the scripts in source form. 

Performance is an issue but it can be mitigated by being easy to port to Rust. 

The way I plan for it to be used is for there to be an immutable state that every thread can read from and Events that only a specific tread can read. Threads can only queue events for the next state and finish by returning a part of the next state. 

If multiple events are queued for the same threat then they are shorted into a 'canonical' order before that state is processed. A script should make no assumption about the evepts recieved by other scripts. If an event is routed to multiple scripts then they are shorted on a per script basis e.g. is event X, Y an Z are routed to both script A and B, A may recieve them as Z, Y, X and script B may recieve them as Z, X, Y. THe order is only agreed upon at run time my the server cluster with no garities that it be the same for diffrent scripts. 

It's ok if each script is single threaded but it should be easy to have multiple scripts being processed in different threads

Every sim will have several servers runing in lockstep. they distribute the relevent information to the clients connect to that server and pass events from the clients to the sim(Do you have one trusted server to handle colecting and distributing events or do you use something like [BFT-SMaRt](https://github.com/bft-smart/library) Have another look at [FreeMMG](https://sourceforge.net/projects/freemmg/files/) and [teatime architexture](https://oneofus.la/have-emacs-will-hack/files/2005_Teatime_Framework_Design.pdf) needs to have a fallback if the primary server goes down)

If the admin hasn't logged in in the pass X days promote the oldest player that has logged in in the pass Y days to temp admin. temp admin can do anything a permenent admin can do but don't reset the days since admin has logged in. e.g. Admin hasn't longe in in X days Bob is olded player that has logged in in Y days. Bob doesn't make himself a permenent admin an at day X+9 Charlie logs on who has been a player longer than Bob. Charlie is now the temp admin. 

Physics are theire  own sim the main sim sends it events to advance the physics simulation and in return it generates a series of physics events and sends them to the main sim. Physics sims subcribe to naighbering physics sim and simulate objects near the boarder aswell s pass offs between physics sims. the Physics sim doesn't generate events to the main sim for physics objects it doesn't have ownership of but does generate internal events e.g. if object A is owned my physics sim Y and object B is owned by sim Z, sim Y needs to simulate object B to generate the event that A was hit by B but it's up to sim Y to generate the event that B was hit by A. [Gamasutra article about distributed physics](https://www.gamasutra.com/view/feature/173977/scaling_a_physics_engine_to_.php?page=2) 

ToDo.
1. Port Raynet from C++ to Rust.
2. Make basic chat server.
3. Make a client to connect to chat server
4. Add [Newdon1](https://github.com/DrAlta/Newdon1) to support physics for a basic sim.
5. Add scripting to server(Lua modified to use fixpoint instead of floats? some Forth variant that uses objects as a base type?)
6. Add [Newdon2](https://github.com/DrAlta/Newdon2) to support advance physics.
7. Screw Over Users.
8. Profit!

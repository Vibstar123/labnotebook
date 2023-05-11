---
title: Behavior Trees Investigatoin
author: Chris Tam
date: may-2000
type: report
---
# Behavior Trees (in progress)

## Author: Chris Tam

### Introduction
  The campus rover behavior tree project was conceived of as a potential avenue of exploration for campus rover’s planning framework. Behavior trees are a relatively new development in robotics, having been adapted from non-player character AI in the video game industry around 2012. They offer an alternative to finite state machine control that is both straightforward and highly extensible, utilizing a parent-child hierarchy of actions and control nodes to perform complex actions. The base control nodes - sequence, selector, and parallel - are used to dictate the overarching logic and runtime order of action nodes and other behavior subtrees. Because of their modular nature, behavior trees can be both combined and procedurally generated, even used as evolving species for genetic algorithms, as documented by Collendachise, Ogren, and others. Numerous graphical user interfaces make behavior trees yet more accessible to the practicing technician, evidenced by the many behavior tree assets available to game designers now as well as more recent attempts such as CoSTAR used for robotics. My aim for this project, then, was to implement these trees to be fully integrable with Python and ROS, discover the most user-friendly way to interact with them for future generations of campus rover, and integrate my library with the current campus rover codebase by communicating with move_base and sequencing navigation while, optimistically, replacing its finite state machine structure with predefined trees.

### What was created

  For the most part, I was successful in accomplishing these goals, and the final result can be found at https://github.com/chris8736/robot_behavior_trees. This repository contains a working behavior tree codebase written entirely in Python with ROS integration, including all main control nodes, some decorator nodes, and simple processes to create new action and condition nodes. Secondly, I was able to implement a unique command parser to interpret command strings to autonomously generate behavior trees designed to run. This command parser was later used to read a sequence of plain English statements from a text file, providing a means for anyone to construct a specific behavior tree with no knowledge of code. Thirdly, I was able to merge my project seamlessly with the robot arm interface project and the campus rover navigation stack to easily perform sequenced and parallel actions.

  The codebase is inspired by the organization of the C++ ROS-Behavior-Trees package, found here: https://github.com/miccol/ROS-Behavior-Tree. Though powerful, I found this package somewhat unsupportive of new Python nodes, requiring obscure changes to catkin’s CMakeLists text file after every new action node or tree declaration. Despite this, I found the package’s overall organization neat and its philosophy of implementing behavior tree action nodes as ROS action servers sensible, so I attempted to adopt them. Rather than have singular action nodes, I implement auxillary client nodes that call pre-instantiated action servers that may in the future accept unique goals and provide feedback messages. For now, these action servers simply execute methods that may publish ROS topics, such as moving forward for two seconds, twisting 90 degrees clockwise, or just printing to console. This modified structure is shown below, in a program that moves forward and backward if battery is sufficient:

![Imgur](https://i.imgur.com/iPB6esn.png)

  Control nodes - e.g. sequence, parallel, and selector nodes - are normal Python classes not represented by ROS nodes. I connect these classes and client nodes via parent-child relationships, such that success is implicitly propagated up the tree, and resets naturally propagate down. With these node files alone, behavior trees can be explicitly defined and run in a separate Python file through manually setting children to parent nodes.

  My command parsing script simply reads in strings from console or lines in a text file and interprets them as instructions for constructing a novel behavior tree. It utilizes the shunting-yard algorithm along with a dictionary of keywords to parse complex and potentially infinitely long commands, giving the illusion of natural language processing. Shunting-yard splits keywords recognized in the input string into a stack of values and a stack of operators, in this case allowing logic keywords such as “and” to access its surrounding phrases and set them as children of a control node. In addition to storing keys of phrases, the dictionary also holds references to important classes required to build the tree, such as condition nodes and action servers. The below figure shows example execution for the command “move forward then twist right and print hello” (note: one action represents both the client and server nodes):

![Imgur](https://i.imgur.com/AFWLqD3.png)

  On its own, this approach has its limitations, namely that all the trees it creates will be strictly binary and right-heavy. I was able to solve the latter problem while implementing file parsing through the keyword “is”, allowing keywords to reference entire subtrees through commands such as:

![Imgur](https://i.imgur.com/j4qVVlq.png)

  More complex behaviors can thus be built from the bottom up, allowing for multiple levels of abstraction. 
  Conciseness and good documentation allow this codebase to be highly modular. By inheriting from general Action and Condition node classes, making an external process an action or condition requires only a few extra lines of code, while incorporating it into the command parser requires only a dictionary assignment. This ease of use allowed me to very quickly implement action nodes referencing move_base to navigate campus rover towards landmarks, as well as actions publishing to the robot arm interface to make it perform sequenced gestures.

### Story of the project

  I began this project by searching online for behavior tree libraries I could use to further my understanding of and expedite my work. Within this week of searching, I settled on ROS-Behavior-Trees, utilized by Collendachise in the above literature. Though I succeeded at running the example code and writing up new Python action nodes, I had great difficulty attaching those new nodes to the library’s control nodes, written entirely in C++. After a particularly inefficient week, I decided to switch gears and move on to my own implementation, referencing the C++ library’s architecture and the literature’s pseudocode to set up my main nodes. Though successful, I found writing up a whole python file to represent a behavior tree while having to remember which parent-child relationships I had already set exceedingly inconvienient.
	
  As the third generation of campus rover was not yet stable by this time, I decided the best use of my remaining time was to make the use of my code more intuitive, leading me to the command parser. I had used the shunting-yard algorithm in the past to parse numerical expressions, and was curious to see if it would work for lexical ones as well. Though it was challenging to hook up each of my servers to a unique key phrase while importing across directories, I eventually made something workable without any major roadblocks.
	
  When I was finally able to get campus rover running on my machine, I had only a few weeks left to understand its code structure, map which topics I was to publish to, and rewrite existing action functionality with parameterized arguments if I wanted to do a meaningful integration. Though I worked hard to identify where navigation requests were sent and found success in having getting a robot find its way to the lab door, AMCL proved to be unreliable over many trials, and I struggled to figure out how to write new action goals to make even a surface level integration work with all landmarks. With the hope of making behavior trees easier to understand and access for future developers, I instead used the remaining time to refine my command parser, allowing it to read from file with all the functionality I mention above.
	
  All things considered, I feel I have satisfied nearly all the initial goals I could have realistically hoped to accomplish. I made my own behavior tree library from scratch with no previous knowledge of the topic, and I find my codebase both very easy and enjoyable to work with. Though I was unable to work with campus rover for the majority of the final project session, I proved that integrating my codebase with outside projects through ROS could be done incredibly quickly. Finally, I was able to make progress in what I believe to be an unexplored problem - interpreting human commands to generate plans robots can use in the future.

### References

  https://ieeexplore.ieee.org/abstract/document/6907656 

  https://ieeexplore.ieee.org/abstract/document/7790863

  https://ieeexplore.ieee.org/abstract/document/8319483

  https://ieeexplore.ieee.org/abstract/document/7989070 

This is an initial stream-of-consciousness README reference doc to provide the baseline for this project. This project is meant to explore, ideate, design, plan, develop, and implement a framework for agentic skill file sharing and the ability to supply for progressive disclosure. Similar to a concept that Stripe calls their tool shed that they use for a lot of their agentic, long-running coding. In a smaller-scale proof of concept, I implemented it in the T3 Hearst project.

The idea is having a group of specialized agents. When they're spawned from the main session, they don't automatically inherit skills. If you can provide them with an index of "here's the skill locker based off of the task you're given", review the skill locker and read any of the skill files and additional resources that apply to your task. In a way that's conservative and efficient token-wise, doesn't pollute the context window, and provides a lot of flexibility to give the right agents the right skills and the right knowledge at the right times.

The references below are linked to an explanation of the pattern, the skill locker pattern we proved out in the T3 Hearst project, and some documentation Stripe put out about their methodology. I want to figure out what's possible, what's helpful, and what will be the right set of tools and framework to help me as a freelance developer across multiple projects to get better and more easily spawn multi-agentic workflows with specialized agents with access to a wide variety of specialized skills. To really improve my implementation process and get better results quicker.

A couple of use cases I've thought about that I don't know if it makes sense: creating skills related to my IQ design and branding system. If I have agents doing any frontend work on any of my IQ branded apps, here's the skill for that, so everyone's playing off the same sheet of music. Other thoughts I have are skills related to quality gating, so testing tasks after they're complete and maybe breaking it down into a testing team. You have the test orchestrator, and then you have a specialized agent writing tests, and then the one who performs the test and then provides feedback, and managing the loop of all that. This is just my starting point. 


# References
SKILL-LOCKER-PATTERN.md
stripe-minions-2.md
stripe-minions.md
/Users/kmgdev/dev_projects/t3-hearst

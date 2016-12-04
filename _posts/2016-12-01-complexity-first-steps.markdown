---
layout: post
title:  "First steps in mitigating Software Complexity"
date:   2016-11-01 16:00:00 +0530
categories: software-development
tags: software-development software-leadership
excerpt: 
---

You are leading a software development team of more than 10 developers. Your code base has grown so complex that shipping features takes longer than it used to. In that case, along the lines of famous [mandate](https://plus.google.com/+RipRowan/posts/eVeouesvaVX) by Jeff Bezos, this is the mandate I propose you issue to your team.

1. No one talks with anyone else in the team.

2. When someone starts to work on a feature, they should also start documenting it. When the feature is pushed, the reviewer should read the documentation before reviewing the code.

3. When reviewing any code, the reviewer should make sure there are enough comments in the code that explain
     
     a. **Why** a module is written and why it is written the way it is written
          
     b. Tradeoffs and odd choices that are made
     
     c. Assumptions made
     
     d. Tricky pieces
     
     e. Things new developers might think are mistakes but aren't

4. When a developer is reading code and comes across a piece of code that he can't make sense of, he should put his query in a central channel (Slack channel or something similar). Whichever developer can help with that can reply to it. The developer that asked the question should now add it as a comment above the original piece of code.

5. When there is a production issue or issue found out by testing, it should be created as a task in project management tool. It can be assigned to a particular developer or left unassigned for someone else to pick.

6. Anyone who doesn't do this will be made to work on legacy systems.

7. Thank you; happy hacking and documenting!

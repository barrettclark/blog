---
author: Mike Perham
categories:
- Ruby
date: 2007-12-03T00:00:00Z
keywords:
- berkeley db, bdb, ruby, binding
title: Berkeley DB and Ruby
url: /2007/12/03/berkeley-db-and-ruby/
---

I've been working with Berkeley DB in Ruby to do some prototyping of a scalable metric store for FiveRuns. In theory it's a great toolkit. Rather than using a generic relational database, you can build the exact structures and functionality required to store/retrieve your data. So far, my impression is that the API is ugly compared to a typical Ruby API (bitmask configuration, for one) and can be difficult to debug. Neither is a showstopper though and I certainly do like the fact that it has a long history of success behind it.

To do this I've had to work with the [BDB ruby binding][1]. Unfortunately it's pretty crufty C code and doesn't have any mailing list or community around it at all. I found an [alternative binding][2] which is much smaller; I'm going to test it out tomorrow.

Anyone out there have experience with BDB and Ruby? Any advice or experiences to share?

 [1]: http://raa.ruby-lang.org/project/bdb/
 [2]: http://rubyforge.org/projects/bdb2/

---
publish: "true"
tags:
  - programming
---

## Description
* URL: https://spotandjake.github.io/NeoFox/
* Source: https://github.com/spotandjake/NeoFox

I wrote this project years ago, but it was the largest project I had ever written at the time and was my first true experience with react.

Neofox started an early life as chatter a serverless chat application based off firebase, it went through many stages native js, ejs, vue before I eventually landed on Next.js with React. This is around the time the name switched to Neofox.

The project is completely serverless the users connect directly to the database which poses various security challenges, write access is controlled through a statically evaluated set of rules on the firebase side but these are more declarative than your regular server which makes things like rate limiting far harder. The project uses what I refer to as a spine style approach to react, where a singular context variable is passed down the component tree and events are used to pass data between components this has the side effect of easy caching we use a singular hook to watch for messages being sent and the security rules require users to batch update when they write enforcing rate limiting. To handle XSS issues markdown is used and each client has to re convert the markdown over to HTML.
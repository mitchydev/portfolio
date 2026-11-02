+++
draft = false
title = 'Self Learning'
status = 'pending'
+++

> Identify a portion of your tech tutorial that you developed.
>
> - Describe the technology that the tutorial addresses and how it fits into your project.
> - Evaluate how useful the tool was in your work. What were its strengths and weaknesses, especially in comparison with other tools you have used.
> - Identify one particular sticking point to using this technology that you would want your team to know about when adopting the tool and how you resolved it.

## Self Learning Sample

### Location

The backend of our app is a Flask application (path: backend/src/app.py) that sits between our React frontend and our (as of writing this) SQLite database. Essentially, Flask maps URLs to ordinary Python functions. For example, GET /api/buildings, GET /api/buildings/\<name>/rooms and GET /api/rooms/\<id> return JSON (plain text format between programs) read through our rooms database layer which is db/src/queries.py and PUT /api/rooms/\<id>/status checks that the new status is Open, Taken, or Locked and then saves it. The backend also builds the database from the schema and seed script on first start and matches building names regardless of capitalization.

### My contributions

To make the backend work, I worked with Claude Code to write 34 automated tests, which are small programs that send requests to the backend and check the answers. For example, they check that asking for a room that doesn’t exist gives a “not found” message, and that changing a room’s status is really saved. What the tests don’t cover at the time of writing is many people changing rooms at the same moment, or the whole app running together in a browser.

### Generative AI use

I used Claude Code in conjunction with my learning.

### How this demonstrates the competency

As mentioned, this was coded in Flask. It is a free Python tool for building the behind-the-scenes part of a web app. It receives requests from the page, such as “show me rooms in this building,” runs the right Python function, and sends the answer back. I chose Flask because it is small enough to understand quickly, flexible, and it works with my team’s existing database code very well. Its downsides are that it provides very little on its own, so we wrote our own checks for valid input and our own error messages. It also has no built-in login, so anyone who can reach the server can change a room’s status, and its built-in server is meant for development, not for a public website.

A sticking point we’d warn any team about is that Flask answers a bad web address with an HTML error page by default, which would have broken our frontend because it expects JSON, so we added our own error handlers that reply with short JSON messages like “Room not found” instead.

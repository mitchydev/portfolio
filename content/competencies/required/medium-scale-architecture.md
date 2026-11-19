+++
draft = false
title = 'Medium Scale Architecture'
status = 'pending'
+++

> Choose one of the components of your project that you (co-)designed according to its architecture, e.g., front-end versus back-end.
>
> - What architectural pattern does your program employ?
> - What components result from this pattern in your program?
> - What technologies and/or libraries make-up each of the components?

## Medium Scale Architecture Sample

### Location

`frontend/vite.config.js` lines 8-11 and `backend/src/app.py` lines 474-480

### My contributions

I designed and worked with Claude Code to make the Flask back-end (`backend/src/`) and Claude Code wrote the “Frontend contract” table in backend. This defines every route and status code between the frontend and backend. Brian works on the frontend while Myles works on the database itself.

### Generative AI use

Claude Code was used to code the backend in collaboration with me.

### Competency Demonstration

I chose the backend as my component. The program uses a three-tier architecture, which splits an app into a presentation tier (what the user sees), an application tier (the logic and rules), and a data tier.

1. The presentation tier (Frontend) is Brian’s React and Vite frontend.
2. The application tier (backend) is my Flask 3.1 API,
   1. which sends and receives JSON (an API)
   2. uses Flask-JWT-Extended (a Flask library that handles login tokens) for login tokens
   3. Flask-Limiter for rate limits
   4. Flask-CORS for cross-site rules.
3. The data tier is one SQLite file, `room-reserve.db`.

The tiers only talk through the API, and the browser never contacts Flask directly. It sends requests to the Vite dev server, which forwards everything starting with `/api` to Flask.

```javascript
proxy: { '/api': env.ROOMRESERVE_API_URL || 'http://127.0.0.1:5000' },
```

This keeps the page and the API on one origin, so the browser sends the login cookie (which is `SameSite=Strict`), and the frontend has no hard-coded backend address. For example, when a user saves a room, the frontend sends `PUT /api/saved/12`, the Flask route checks the login and that the room exists, `saved_rooms.save()` inserts the row, and the API replies with `{"room_ids": [...]}`. The one limit at the time of writing this is that Myles’s room code reaches the database directly instead of going through my modules.

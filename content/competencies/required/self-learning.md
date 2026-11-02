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

[`backend/src/app.py` lines 59–60 (commit 726eddc)](https://github.com/grinnell-csc324-fall-2026/room-reserve/blob/726eddc/backend/src/app.py#L59-L60)

```python
def json_error(message, status):
    return jsonify({"error": message}), status
```

[`backend/src/app.py` lines 93–121 (commit 726eddc)](https://github.com/grinnell-csc324-fall-2026/room-reserve/blob/726eddc/backend/src/app.py#L93-L121)

```python
# The only values a room's status can have.
STATUSES = ("Open", "Taken", "Locked")


@app.route("/api/rooms/<int:room_id>/status", methods=["PUT"])
def set_status(room_id):
    # update_status() crashes on a room that does not exist and accepts any
    # text as a status, so both are checked here first.
    if not data.get_room(room_id):
        return json_error("Room not found", 404)
    body = request.get_json(silent=True)
    status = body.get("status") if isinstance(body, dict) else None
    if status not in STATUSES:
        return json_error("Status must be one of: " + ", ".join(STATUSES), 400)
    data.update_status(room_id, status)
    return jsonify(data.get_room(room_id))


@app.errorhandler(405)
def method_not_allowed(error):
    # Keep wrong-method errors JSON too, like the 404s below.
    return json_error("Method not allowed", 405)


@app.errorhandler(404)
def not_found(error):
    # Without this, a bad URL like /api/rooms/abc returns an HTML page and
    # the front end's response.json() would crash.
    return json_error("Not found", 404)
```

### My contributions

To make the backend work, I worked with Claude Code to write 34 automated tests, which are small programs that send requests to the backend and check the answers. For example, they check that asking for a room that doesn’t exist gives a “not found” message, and that changing a room’s status is really saved. What the tests don’t cover at the time of writing is many people changing rooms at the same moment, or the whole app running together in a browser.

### Generative AI use

I used Claude Code in conjunction with my learning.

### Competency Demonstration

As mentioned, this was coded in Flask. It is a free Python tool for building the behind-the-scenes part of a web app. It receives requests from the page, such as “show me rooms in this building,” runs the right Python function, and sends the answer back. I chose Flask because it is small enough to understand quickly, flexible, and it works with my team’s existing database code very well. Its downsides are that it provides very little on its own, so we wrote our own checks for valid input and our own error messages. It also has no built-in login, so anyone who can reach the server can change a room’s status, and its built-in server is meant for development, not for a public website.

A sticking point we’d warn any team about is that Flask answers a bad web address with an HTML error page by default, which would have broken our frontend because it expects JSON, so we added our own error handlers that reply with short JSON messages like “Room not found” instead.

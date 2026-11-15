# OpsFlow: guide for agents

OpsFlow maps a workflow as shapes and arrows. An agent can drive it three ways. No login, no server; everything stays in the browser.

## The workflow format

```json
{
  "name": "Lead to proposal",
  "nodes": [
    { "id": "s", "type": "start",    "title": "Lead in",  "next": ["q"] },
    { "id": "q", "type": "step",     "title": "Qualify",  "owner": "Sales", "tool": "CRM", "mins": 15, "next": ["d"] },
    { "id": "d", "type": "decision", "title": "Good fit?", "next": [ { "to": "p", "label": "Yes" }, { "to": "x", "label": "No" } ] },
    { "id": "p", "type": "step",     "title": "Send proposal", "owner": "Sales", "mins": 60, "next": ["e"] },
    { "id": "x", "type": "end",      "title": "Pass" },
    { "id": "e", "type": "end",      "title": "Won" }
  ]
}
```

- `type` is `start`, `step`, `decision` or `end` (default `step`).
- `owner`, `tool`, `mins` (minutes) and `notes` are optional.
- Arrows: put `next` on a node (an id, or `{ "to", "label" }`), or give a separate `"edges": [{ "from", "to", "label" }]` list.
- Leave out `x` and `y` and OpsFlow lays the workflow out top to bottom. Give `x` and `y` on every node to place them yourself.
- Limits: 300 nodes, 600 arrows, 200 characters per text field, 2000 for notes.

## 1. Open a workflow with a link (no browser control needed)

Add the URL-encoded JSON after `#w=j.`:

```
<page address>#w=j.<encodeURIComponent(JSON)>
```

Whoever opens the link gets the workflow as their own copy.

## 2. Drive the page from a browser agent or script

The page exposes `window.OpsFlow`:

| Call | Does |
| --- | --- |
| `OpsFlow.load(workflow)` | Opens a workflow (object or JSON text) as a new one; returns it |
| `OpsFlow.get()` | Returns the current workflow |
| `OpsFlow.add(node, afterId)` | Adds a shape, connected below `afterId` if given; returns its id |
| `OpsFlow.update(id, fields)` | Changes title, owner, tool, mins or notes |
| `OpsFlow.remove(id)` | Deletes a shape and its arrows |
| `OpsFlow.connect(from, to, label)` | Adds an arrow; returns its id |
| `OpsFlow.rename(name)` | Renames the workflow |
| `OpsFlow.tidy()` | Re-does the layout |
| `OpsFlow.summary()` | Steps, total minutes, time per owner, tools used |
| `OpsFlow.json()` | The save-file text |
| `OpsFlow.shareLink()` | Promise of a share link |
| `OpsFlow.png()` | Promise of a PNG Blob of the drawing |
| `OpsFlow.help` | This list in one line |

Every change goes through the same undo history and auto-save as a person's clicks.

## 3. Hand a person a file

Save the JSON above as a `.json` file. The person opens it with **Open file**, or drags it onto the page.

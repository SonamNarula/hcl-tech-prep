# Project Questions — CodeFusion (Real-Time Collaborative Editor: React/Node.js/Socket.io/Monaco Editor)

---

### Question 16 — Tell me about this project.
**Difficulty:** Easy | **Category:** Project Overview

**Interview Answer**
"CodeFusion is a real-time collaborative code editor — think a lightweight Google Docs but for code. Multiple users can edit the same file simultaneously and see each other's changes live. I built the frontend with React and Monaco Editor — the same editor that powers VS Code — and the real-time sync with Socket.io over a Node.js backend."

**Common Mistake**
Confusing "collaborative" with just "shared link" — be ready to explain the actual real-time sync mechanism, not just that multiple people can access it.

**Follow-up Questions**
- How does the real-time sync actually work under the hood?
- What happens when two people edit the same line at the same time?
- Why Monaco Editor instead of building a text area yourself, or using CodeMirror?

---

### Question 17 — Why did you choose Socket.io specifically (instead of raw WebSockets or something like WebRTC)?
**Difficulty:** Medium | **Category:** Architecture Decisions

**Answer**
Socket.io wraps raw WebSockets with automatic fallbacks (long-polling if WebSocket isn't available), automatic reconnection, and a simple room/namespace abstraction — which is exactly what's needed for "users in the same document" style grouping.

**Interview Answer**
"Socket.io gave me rooms out of the box — I can group all users editing the same file into a room and broadcast changes only within that room. It also handles reconnection and fallback transport automatically, which raw WebSockets don't give you for free. WebRTC is more for peer-to-peer media/data channels and would've been overkill and more complex for this text-sync use case."

**Follow-up Questions**
- What's the difference between WebSocket and HTTP polling?
- What is a Socket.io "room" internally?
- What happens if a user's socket disconnects mid-edit?

**Strong Candidate Point**
Explain the actual event flow: client emits a change event with the diff/delta on every edit → server receives it, broadcasts to all other clients in that room → each client applies the incoming change to their local Monaco editor instance.

---

### Question 18 — How do you handle conflict when two users edit the same line simultaneously?
**Difficulty:** Hard | **Category:** Core Technical Challenge

**Interview Answer**
"[Be precise about your actual implementation.] If it's a simple last-write-wins broadcast model, say that honestly: 'Right now it's closer to last-write-wins — the most recent change broadcast overwrites in that region, which works for most cases but isn't a true CRDT/OT-based conflict resolution system like what Google Docs uses.' If you did implement something more sophisticated (operational transform, or diffing), explain that specifically."

**Common Mistake**
Claiming to have implemented full Operational Transformation (OT) or CRDT-based conflict resolution when the project actually does simpler broadcast-based syncing — this is a very common exaggeration interviewers specifically probe on collaborative editors, since real OT/CRDT is genuinely hard (it's what Google Docs and Figma engineering teams have written papers about).

**Follow-up Questions**
- What is Operational Transformation? What is a CRDT? (Be ready with at least a definition-level answer even if you didn't implement one.)
- What would break in your current approach under high-frequency concurrent edits?
- How would you upgrade this to true conflict-free merging?

**Strong Candidate Point**
"I know the production-grade solution here is OT or CRDTs — like Yjs or ShareDB — and that would be the natural next iteration if I scaled this beyond a demo."

---

### Question 19 — Why Monaco Editor instead of CodeMirror or a plain textarea?
**Difficulty:** Medium | **Category:** Tech Choice

**Interview Answer**
"Monaco is the actual engine behind VS Code, so it comes with syntax highlighting, IntelliSense-style features, and a familiar editing experience out of the box. CodeMirror is lighter weight and more customizable at a lower level, but Monaco gave me a much richer editing experience with less custom work."

**Follow-up Questions**
- What are the trade-offs — bundle size, for instance — of using Monaco vs. CodeMirror?
- How did you integrate Monaco's change events with your Socket.io emit logic?

---

### Question 20 — Walk me through what happens when User A types a character.
**Difficulty:** Medium | **Category:** Data Flow / Internals

**Interview Answer**
"Monaco fires an `onDidChangeModelContent` (or equivalent) event → I capture the change (position, inserted/deleted text) → emit it via a Socket.io event to the server with the room/document ID → the server broadcasts that event to every other socket in the room except the sender → each receiving client applies the same change to their local Monaco model programmatically, without re-triggering their own change event (to avoid infinite loops)."

**Follow-up Questions**
- How do you prevent an infinite loop where applying a remote change triggers another local change event?
- Is the full document content sent every time, or just deltas? Why does that matter?

**Common Mistake**
Sending the entire document content on every keystroke instead of just the diff — this is a classic beginner mistake in collaborative editors and a great thing to proactively mention you avoided (or admit if you did do it, and explain why it's inefficient).

---

### Question 21 — How is the backend structured?
**Difficulty:** Medium | **Category:** Backend Architecture

**Interview Answer**
"Node.js with Express (or a bare HTTP server) handling the Socket.io server. Each 'document' or 'room' is tracked server-side — when a client connects, they join a room by document ID. The server's main job is relaying change events between clients in the same room, and [if persistence exists] periodically saving document state to a database."

**Follow-up Questions**
- Is document content persisted anywhere, or only held in memory while users are connected?
- What happens if the server restarts — is in-progress editing lost?
- How would you horizontally scale this across multiple server instances? (Hint: Socket.io needs a shared adapter like Redis for this.)

**Strong Candidate Point**
Knowing that scaling Socket.io across multiple Node instances requires the Socket.io Redis adapter (because rooms/broadcasts are in-memory per instance by default) is a strong, specific technical detail.

---

### Question 22 — What security measures exist — can anyone join any document?
**Difficulty:** Hard | **Category:** Security

**Interview Answer**
"[Be honest.] If there's currently no auth layer restricting who can join a room, say so directly: 'Right now anyone with the document link/ID can join — there's no access control layer yet. The natural next step would be authenticated sessions plus a permissions model (owner/editor/viewer) checked server-side before allowing a socket to join a room.'"

**Common Mistake**
Overstating security that isn't there. This is a very natural, expected gap for a portfolio-scale real-time editor — admitting it and describing the fix is a stronger answer than pretending it's solved.

---

### Question 23 — What's the hardest bug you fixed in this project?
**Difficulty:** Medium | **Category:** Challenges

**Interview Answer**
"[Have a specific real bug ready — likely candidates for a project like this: cursor position jumping/resetting when a remote change is applied, duplicate event listeners causing changes to apply multiple times, or race conditions when a user joins mid-edit and needs the current document state synced to them.]"

**Follow-up Questions**
- How did you debug it — what tools did you use?
- How do you know it's actually fixed, not just less frequent?

---

### Question 24 — How does a newly-joining user get the current state of the document (not just future changes)?
**Difficulty:** Medium | **Category:** Edge Case / Follow-up

**Interview Answer**
"When a client joins a room, the server needs to send them the current full document state — either from an in-memory store of the latest content per room, or from persisted storage — before they start receiving incremental change events. Otherwise they'd only see edits made after they joined, not the existing content."

**Common Mistake**
Not having thought about this edge case at all — it's one of the first "gotcha" follow-ups an interviewer asks about any real-time collaborative system.

---

### Question 25 — Why did you build this instead of just using an existing tool like VS Code Live Share?
**Difficulty:** Easy | **Category:** Motivation / Trick Question

**Interview Answer**
"This was about learning how real-time systems actually work under the hood — WebSockets, event broadcasting, state synchronization — rather than needing a production alternative to Live Share. Building it taught me things you don't learn just using an existing tool, like the actual complexity behind conflict handling in collaborative systems."

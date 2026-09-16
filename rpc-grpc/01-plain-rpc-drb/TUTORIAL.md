# Lesson 1: Plain RPC with Ruby DRb

**Goal:** build a tiny two-process RPC setup using Ruby's built-in DRb (Distributed Ruby), so you see the raw mechanism before gRPC hides it: a client calls a method → arguments get serialized → sent over a TCP socket → the real method runs in a *different process* → the result gets serialized back.

No gems needed — DRb ships in Ruby's standard library.

Check off each box as you complete it. Stop anytime; nothing here needs to happen in one sitting.

---

## Step 0 — Confirm Ruby is ready

- [x] Run `ruby -v` (any modern Ruby works)
- [x] Run `ruby -e "require 'drb/drb'; puts 'ok'"` — should print `ok`

Note: I did end up having to run `gem install drb`

## Step 1 — The concept (read before writing code)

DRb lets one Ruby process start a live object and expose it at a `druby://host:port` address. A second process gets a *proxy* to that object — calling a method on the proxy actually sends the method name + arguments over TCP to the first process, which runs the real method and sends the return value back.

The class you expose doesn't need to know anything about networking — it's a plain Ruby class. DRb wraps it.

## Step 2 — Write the server

Create `server.rb` in this folder. It needs to:

- [ ] `require 'drb/drb'`
- [ ] Define a plain Ruby class (e.g. `Calculator`) with at least two methods that take arguments and return a value (e.g. `add(a, b)`, `multiply(a, b)`) — no networking code inside the class itself
- [ ] Create an instance of it
- [ ] Call `DRb.start_service("druby://localhost:8787", your_instance)`
- [ ] Print something so you know it's running (e.g. `puts DRb.uri`)
- [ ] Keep the process alive with `DRb.thread.join`

## Step 3 — Run the server

- [ ] In one terminal: `ruby server.rb` — leave it running

## Step 4 — Write the client

Create `client.rb` in this folder. It needs to:

- [ ] `require 'drb/drb'`
- [ ] Call `DRb.start_service` (no URI argument needed on the client side)
- [ ] Get a proxy to the remote object: `DRbObject.new_with_uri("druby://localhost:8787")`
- [ ] Call at least two methods on the proxy and print the results

## Step 5 — Run and verify

- [ ] With the server still running, in a **second** terminal: `ruby client.rb`
- [ ] Confirm the output matches what you'd expect from calling those methods locally (e.g. `add(2, 3)` prints `5`)

If you get `Errno::ECONNREFUSED`, the server isn't running or the port doesn't match.

## Step 6 — Prove it's actually remote

These checks are the whole point of the exercise — they show real inter-process behavior, not just a working script.

- [ ] Inside a server method, add `puts "running on server pid #{Process.pid}"`. Also print `Process.pid` in the client. Confirm the two PIDs are **different** — proves the method body genuinely executes in the other process.
- [ ] Add a method to your class that deliberately raises an error for bad input (e.g. dividing by zero). Call it from the client wrapped in `begin/rescue`. Confirm the exception makes it across the wire to the client.

## Step 7 — Reflect (write these in your own `notes.md`, not here)

- What exactly gets sent over the socket when you call a remote method — and what happens if an argument isn't serializable?
- This only works because both sides are Ruby, using Ruby's `Marshal` format. What would break if the client were written in Python or Go? *(This is exactly the gap gRPC + Protocol Buffers exist to close — next lesson.)*

---

Once every box above is checked, you've built and understood a working RPC round-trip. Next up: `02-grpc-unary/`.

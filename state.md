# Your Agent Is a State Machine — the Copy-Paste Starter

Your AI agent is a board-game piece. It sits on ONE square, an event happens, it hops to the next square. That "square + the move to the next square" is a **state machine** — one tiny function, `(state, event) -> nextState` — and it's the thing agent frameworks quietly resell you. The one idea: **write that function yourself in a `switch` statement and you get two guarantees for free — your agent is always in exactly one state, and it can only move along paths you actually drew.**

## What a state machine actually is

A state machine answers a single question: given the current state, when an event happens, what is the next state? That's it — `(state, event) -> nextState`. Your agent's states are the squares it can sit on (idle, thinking, calling a tool, done). Events are the things that happen (start, tool came back, error, finish). The transition function is the rulebook that says which square you hop to next. Draw the squares as dots and the moves as arrows and you've drawn a graph — the "agent graph" everyone hypes is exactly this, nothing more.

## The copy-paste starter (plain JavaScript / TypeScript)

Paste this into a file, run `node starter.js`, and you have a working agent core. No library.

```js
// transition(state, event) -> next state. The whole "framework" in one switch.
function transition(state, event) {
  switch (state) {
    case "idle":
      // idle only handles "start" — every other event is ignored
      if (event === "start") return "thinking";
      break;

    case "thinking":
      if (event === "callTool") return "callingTool";
      if (event === "finish") return "done";
      break;

    case "callingTool":
      if (event === "toolResult") return "thinking"; // go think about the result
      if (event === "error") return "done";
      break;

    case "done":
      break; // terminal: no moves out
  }
  // No allowed move for this event? Stay put.
  // This one line is the guardrail: impossible states literally can't happen.
  return state;
}

// --- try it ---
let state = "idle";
for (const event of ["start", "callTool", "explode", "toolResult", "finish", "start"]) {
  const next = transition(state, event);
  console.log(`${state} --(${event})--> ${next}`);
  state = next;
}
```

Run it and you'll see:

```
idle --(start)--> thinking
thinking --(callTool)--> callingTool
callingTool --(explode)--> callingTool   <- bogus event ignored, agent stays safe
callingTool --(toolResult)--> thinking
thinking --(finish)--> done
done --(start)--> done                    <- terminal, nothing happens
```

Notice `explode` did nothing and `start` on a finished agent did nothing. You didn't write error handling for those — the machine *can't* enter a state you didn't draw. That's the free guarantee.

### Same thing as a data table (even shorter)

If you'd rather describe the machine as data than as code, this version is equivalent — the whole graph is one object:

```js
const STATES = {
  idle:        { start: "thinking" },
  thinking:    { callTool: "callingTool", finish: "done" },
  callingTool: { toolResult: "thinking", error: "done" },
  done:        {}, // terminal: no events handled
};

function transition(state, event) {
  const moves = STATES[state];
  // only move along an edge you drew; otherwise stay put
  return moves && event in moves ? moves[event] : state;
}
```

The object literally *is* the graph: each key is a node, each inner key is an edge label, each value is where that edge points. You can read your whole agent's behavior in six lines.

### Wiring it to real work (TypeScript, optional)

`transition` only decides *where* to go. The *doing* (calling the model, running the tool) is a thin layer on top — keep them separate so the rules stay easy to read:

```ts
type State = "idle" | "thinking" | "callingTool" | "done";
type Event = "start" | "callTool" | "toolResult" | "finish" | "error";

// same switch as above, just typed:
function transition(state: State, event: Event): State { /* ... */ return state; }

async function step(state: State): Promise<Event> {
  // do the side-effect for the current square, return the event that resulted
  switch (state) {
    case "thinking":    return (await askModel()).wantsTool ? "callTool" : "finish";
    case "callingTool": return (await runTool()).ok ? "toolResult" : "error";
    default:            return "start";
  }
}
```

The state machine is the referee; `step` is the player. The referee never runs a tool, and the player never decides the rules.

## The 3 steps from the video

1. **List your states.** Write down the squares your agent can be on — nothing fancy, just a list: `idle`, `thinking`, `callingTool`, `done`. Naming them is most of the work.
2. **Write the switch function.** One function: take the current state and the event, `switch` on the state, and each case returns where you go next. That single function is the entire "framework."
3. **Only allow the moves you drew.** In each case, handle *only* the events that make sense there (idle handles `start` and nothing else). For everything else, return the current state unchanged. Now the impossible states — the bugs you would have shipped — can't happen, because there's no line of code that reaches them.

## When you outgrow the switch

You don't need a library to start — the switch above is a real, complete state machine. But when the graph gets big (nested states, states that run in parallel, timers, "always" transitions, undo), hand-maintaining the switch gets noisy. That's when a dedicated library earns its place:

- **XState** — the free, open-source library for this exact pattern: https://github.com/statelyai/xstate. It runs the machine, catches impossible states for you, and handles the hard parts (hierarchy, parallel regions, delays) you'd otherwise hand-roll.
- **Stately editor** — a free visual editor: https://stately.ai/editor. You draw the states and arrows on a canvas and it generates the machine (and vice-versa), so the diagram and the code never drift apart.

Honest framing: reach for these when the graph is genuinely complex, not on day one. A plain switch is the right first move — adopt XState when you can feel the switch fighting you, not before.

## Key insight

The "agent graph" you're being sold is a `(state, event) -> nextState` function — a pattern that's decades old and fits in four lines. Writing it yourself doesn't just save the framework tax; it makes a whole class of bugs impossible, because your agent can only ever be on a square you drew and can only ever take a move you allowed. First it was loops, then graphs, next month it'll be something else — but underneath, it's still just this.

---

*Source: David K. Khourshid ([@DavidKPiano](https://x.com/DavidKPiano)), creator of XState — essay "State machines in 2 minutes" (July 2026): https://x.com/i/article/2064318167992979456*

chosen: B, C, D

# Bug log

One section per bug: bug A from lab 1.13, then the three you chose.
Write in your own words. Paste your own output, from your own tracker, with your own sample errands.

Note: in the pasted output I left out the last `> quit` and `Bye.` lines for bugs B to E, to keep the blocks short. Expected blocks show what the program prints, without `[trace]` lines.

## Bug A: the count is wrong

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node ./bug-hunt/tracker-v0.js
2. list
3. quit

**Expected:**
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> quit
Bye.
```

**Actual:** (paste the output)
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 2 errands
> quit
Bye.
```

**A guess I ruled out, and the one change that ruled it out:**

guess 3, ruled out by adding one errand.

**Cause chain** (from what I saw to the line that causes it; each link with its evidence):
1. Symptom: There are 3 errand lines (#1, #2, #3), but the last line says "You have 2 errands" instead of "You have 3 errands".
2. The count line prints the value of `count`, and `count` is 2 while `length` is 3 (evidence: the `[trace] formatList` line below).
3. Cause (file and line): `tracker-v0.js`, line 36: `const count = errands.length - 1;` subtracts one.

**Evidence** (paste the `[trace]` lines that show each link):
```
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":false}
[trace] formatErrand: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] formatErrand: {"id":3,"text":"Fix the bike light","done":false}
[trace] formatList: {"length":3,"count":2}
```

**The change I made** (the line before and after):

Before:
`const count = errands.length - 1;`

After:
`const count = errands.length;`

**How I confirmed the fix** (the test and the run after the change):

Test passed:
```
> node --test --test-name-pattern="bug A:" bug-hunt/bugs.test.js

✔ bug A: list says how many errands there are (0.7936ms)
ℹ tests 1
ℹ suites 0
ℹ pass 1
ℹ fail 0
ℹ cancelled 0
ℹ skipped 0
ℹ todo 0
ℹ duration_ms 61.0258
```

Run after the change:
```
$ node ./bug-hunt/tracker-v0.js
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> add Buy milk
Added #4: Buy milk
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
#4 [ ] Buy milk
You have 4 errands
```

## Bug B: search does not find "Buy" when I type "buy"

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node bug-hunt/tracker-v0.js --trace
2. search buy
3. quit

**Expected:**
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> search buy
#1 [ ] Buy a notebook
You have 1 errands
```

**Actual:** (paste the output)
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> search buy
[trace] searchErrands: {"text":"Buy a notebook","query":"buy","matches":false}
[trace] searchErrands: {"text":"Order a gas cylinder","query":"buy","matches":false}
[trace] searchErrands: {"text":"Fix the bike light","query":"buy","matches":false}
No errands match.
```

**A guess I ruled out, and the one change that ruled it out:**

My first guess was that `search` was broken for every query. I ruled it out by changing one thing: I searched `Buy` with a capital B instead of `buy`. It found the errand, so search works, and only the lower-case query fails.

```
> search Buy
[trace] searchErrands: {"text":"Buy a notebook","query":"Buy","matches":true}
[trace] searchErrands: {"text":"Order a gas cylinder","query":"Buy","matches":false}
[trace] searchErrands: {"text":"Fix the bike light","query":"Buy","matches":false}
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":false}
[trace] formatList: {"length":1,"count":1}
#1 [ ] Buy a notebook
You have 1 errands
```

**Cause chain** (from what I saw to the line that causes it; each link with its evidence):
1. Symptom: The errand "Buy a notebook" exists, but `search buy` prints "No errands match."
2. `matches` is false for the query `buy` and true for the query `Buy` on the same text (evidence: the `[trace] searchErrands` lines above and in the Actual block), so the comparison is case-sensitive.
3. Cause (file and line): `tracker-v0.js`, line 56: `const matches = errand.text.includes(query);` compares the text and the query without ignoring upper and lower case.

**Evidence** (paste the `[trace]` lines that show each link):
```
> search buy
[trace] searchErrands: {"text":"Buy a notebook","query":"buy","matches":false}
> search Buy
[trace] searchErrands: {"text":"Buy a notebook","query":"Buy","matches":true}
```

**The change I made** (the line before and after):

Before:
`const matches = errand.text.includes(query);`

After:
`const matches = errand.text.toLowerCase().includes(query.toLowerCase());`

**How I confirmed the fix** (the test and the run after the change):

The change on line 56 makes both the text and the query lower case before comparing. The test run below was made after this fix. Bug B passes; bugs C, D and E were not fixed yet, so they still fail.

```
$ npm run bugs

> errand-tracker@1.0.0 bugs
> node --test bug-hunt/bugs.test.js

✔ bug A: list says how many errands there are (0.7519ms)
✔ bug B: search ignores upper and lower case (0.1724ms)
✖ bug C: done ticks only the errand with that id (1.4335ms)
✖ bug D: an errand made only of spaces is refused (0.2423ms)
✖ bug E: a done errand shows a tick (0.3085ms)
ℹ tests 5
ℹ suites 0
ℹ pass 2
ℹ fail 3
ℹ cancelled 0
ℹ skipped 0
ℹ todo 0
ℹ duration_ms 66.1684
```

## Bug C: done marks every errand, not just the one I typed

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node bug-hunt/tracker-v0.js --trace
2. list
3. done 1
4. quit

**Expected:**
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> done 1
Done #1
> quit
Bye.
```
Only errand #1 should be marked done. In the `[trace] markDone` lines, #2 and #3 should keep their own ids and `"done":false`.

**Actual:** (paste the output)
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":false}
[trace] formatErrand: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] formatErrand: {"id":3,"text":"Fix the bike light","done":false}
[trace] formatList: {"length":3,"count":3}
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> done 1
[trace] markDone: {"id":1,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":1,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":1,"text":"Fix the bike light","done":true}
Done #1
```

Note: a `[x]` does not show in `list` after `done`, because bug E (the tick is never shown) was still present when I ran this. So I used the `[trace]` lines to see what `done` really does.

**A guess I ruled out, and the one change that ruled it out:**

My first guess was that `done` ignores the number I type and works on the whole list. I ruled it out by changing one thing: I typed `done 2` instead of `done 1`. The typed number appeared in the trace as the new id of every errand, so `done` does use the number I type.

```
> done 2
[trace] markDone: {"id":2,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":2,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":2,"text":"Fix the bike light","done":true}
Done #2
```

**Cause chain** (from what I saw to the line that causes it; each link with its evidence):
1. Symptom: After `done 1`, all three errands in the trace have `"id":1` and `"done":true`, not only #1.
2. The id I typed is written into every errand instead of being compared with it (evidence: after `done 1` every errand has id 1, and after `done 2` every errand has id 2, see the traces above and below).
3. Cause (file and line): `tracker-v0.js`, line 69: `if (errand.id = id)` uses `=` (assignment) instead of `===` (comparison). An assignment is always true here, so every errand is marked done.

**Evidence** (paste the `[trace]` lines that show each link):
```
> done 3
[trace] markDone: {"id":3,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":3,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":3,"text":"Fix the bike light","done":true}
Done #3
> done 5
[trace] markDone: {"id":5,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":5,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":5,"text":"Fix the bike light","done":true}
Done #5
```
Errand 5 does not exist, yet the program said "Done #5", which also shows that the check is always true.

**The change I made** (the line before and after):

Before:
`if (errand.id = id)`

After:
`if (errand.id === id)`

**How I confirmed the fix** (the test and the run after the change):

After the change, `done 5` is refused because there is no errand 5, and `done 1` changes only errand #1. In the last `list`, the trace shows `"done":true` for #1, but `[ ]` is still printed, because of bug E (see Bug E below).

```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":false}
[trace] formatErrand: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] formatErrand: {"id":3,"text":"Fix the bike light","done":false}
[trace] formatList: {"length":3,"count":3}
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> done 5
[trace] markDone: {"id":1,"text":"Buy a notebook","done":false}
[trace] markDone: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] markDone: {"id":3,"text":"Fix the bike light","done":false}
No errand #5.
> done 1
[trace] markDone: {"id":1,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] markDone: {"id":3,"text":"Fix the bike light","done":false}
Done #1
> list
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":true}
[trace] formatErrand: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] formatErrand: {"id":3,"text":"Fix the bike light","done":false}
[trace] formatList: {"length":3,"count":3}
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
```

The test run for this fix is the one in Bug D below: it shows `✔ bug C: done ticks only the errand with that id`.

## Bug D: an errand made of spaces is accepted

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node bug-hunt/tracker-v0.js --trace
2. add followed by two spaces (type `add`, press the space bar twice, press Enter)
3. list
4. quit

**Expected:**
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> add
Errand text cannot be empty.
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
```

**Actual:** (paste the output)
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> add
[trace] addErrand: {"text":" ","nextId":4}
Added #4:
> list
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":false}
[trace] formatErrand: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] formatErrand: {"id":3,"text":"Fix the bike light","done":false}
[trace] formatErrand: {"id":4,"text":" ","done":false}
[trace] formatList: {"length":4,"count":4}
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
#4 [ ]
You have 4 errands
```

**A guess I ruled out, and the one change that ruled it out:**

My first guess was that the program does not check for empty text at all. I ruled it out by changing one thing: I typed `add` followed by only one space. The trace shows an empty text, and the program refused it. So the empty check works, and it only fails when the text is made of a space.

```
> add
[trace] addErrand: {"text":"","nextId":4}
Errand text cannot be empty.
```

**Cause chain** (from what I saw to the line that causes it; each link with its evidence):
1. Symptom: `add` followed by one space is refused, but `add` followed by two spaces adds an errand #4 with no visible text, and `list` shows an empty `#4 [ ]` and "You have 4 errands".
2. In the two-space run, `addErrand` receives the text `" "` (a single space), which is not an empty string (evidence: `[trace] addErrand: {"text":" ","nextId":4}` above; in the one-space run the trace shows `"text":""` and is refused).
3. Cause (file and line): `tracker-v0.js`, line 45: `if (text === '')` only catches text that is exactly empty. The text `' '` is not equal to `''`, so the check lets it through.

**Evidence** (paste the `[trace]` lines that show each link):
```
[trace] addErrand: {"text":"","nextId":4}
Errand text cannot be empty.
[trace] addErrand: {"text":" ","nextId":4}
Added #4:
[trace] formatErrand: {"id":4,"text":" ","done":false}
```

**The change I made** (the line before and after):

Before:
`if (text === '')`

After:
`if (text.trim() === '')`

**How I confirmed the fix** (the test and the run after the change):

The test, run after the fix. Bugs A to D pass. Bug E was not fixed yet at this point, so it still fails.
```
> errand-tracker@1.0.0 bugs
> node --test bug-hunt/bugs.test.js

✔ bug A: list says how many errands there are (0.7725ms)
✔ bug B: search ignores upper and lower case (0.1877ms)
✔ bug C: done ticks only the errand with that id (0.5811ms)
✔ bug D: an errand made only of spaces is refused (0.1341ms)
✖ bug E: a done errand shows a tick (0.889ms)
ℹ tests 5
ℹ suites 0
ℹ pass 4
ℹ fail 1
ℹ cancelled 0
ℹ skipped 0
ℹ todo 0
ℹ duration_ms 65.8565
```

Run after the fix. The first `add` has no text and the second has ten spaces; both are refused, and a real errand is still accepted:
```
$ node bug-hunt/tracker-v0.js
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> add
Errand text cannot be empty.
> add
Errand text cannot be empty.
> add Meet CEO of NDURE
Added #4: Meet CEO of NDURE
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
#4 [ ] Meet CEO of NDURE
You have 4 errands
```

## Bug E: a done errand does not show a tick (extra bug, not one of my three chosen)

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node bug-hunt/tracker-v0.js
2. done 1
3. done 2
4. list
5. quit

**Expected:**
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> done 1
Done #1
> done 2
Done #2
> list
#1 [x] Buy a notebook
#2 [x] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
```

**Actual:** (paste the output)
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> done 1
Done #1
> done 2
Done #2
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
```

**A guess I ruled out, and the one change that ruled it out:**

My first guess was that `done` never marks the errand as done. I ruled it out by running the same commands with `--trace`. The trace shows `"done":true` for errand 1 after `done 1`, so the errand is marked and the problem is in how `list` shows it. (Bug C was already fixed in this run, so only errand 1 changed.)

**Cause chain** (from what I saw to the line that causes it; each link with its evidence):
1. Symptom: After `done 1` and `done 2`, `list` shows `[ ]` for both errands instead of `[x]`.
2. The errand data says done, but the display does not use it (evidence: the `[trace] formatErrand` line shows `"done":true` for errand 1, while the printed line is `#1 [ ]`).
3. Cause (file and line): `tracker-v0.js`, line 26: `const tick = errand.don ? '[x]' : '[ ]';` has a typo, `errand.don` instead of `errand.done`. `errand.don` does not exist, so it is always empty and the tick is always `[ ]`.

**Evidence** (paste the `[trace]` lines that show each link):
```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> done 1
[trace] markDone: {"id":1,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] markDone: {"id":3,"text":"Fix the bike light","done":false}
Done #1
> list
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":true}
[trace] formatErrand: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] formatErrand: {"id":3,"text":"Fix the bike light","done":false}
[trace] formatList: {"length":3,"count":3}
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
```

**The change I made** (the line before and after):

Before:
`const tick = errand.don ? '[x]' : '[ ]';`

After:
`const tick = errand.done ? '[x]' : '[ ]';`

**How I confirmed the fix** (the test and the run after the change):

The test:
```
> errand-tracker@1.0.0 bugs
> node --test bug-hunt/bugs.test.js

✔ bug A: list says how many errands there are (0.7938ms)
✔ bug B: search ignores upper and lower case (0.1762ms)
✔ bug C: done ticks only the errand with that id (0.5876ms)
✔ bug D: an errand made only of spaces is refused (0.1295ms)
✔ bug E: a done errand shows a tick (0.1201ms)
```

Run after the fix:
```
$ node bug-hunt/tracker-v0.js
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> done 1
Done #1
> done 2
Done #2
> list
#1 [x] Buy a notebook
#2 [x] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
```
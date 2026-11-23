chosen: B, C, D

# Bug log

One section per bug: bug A from lab 1.13, then the three you chose.
Write in your own words. Paste your own output, from your own tracker, with your own sample errands.

## Bug A: the count is wrong

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node ./bug-hunt/tracker-v0.js
2. list
3. quit

**Expected:**
```
Errand tracker v0. Commands: add <text>, list, search <text>, done
<id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> quit
Bye.
```

**Actual:** 
```
Errand tracker v0. Commands: add <text>, list, search <text>, done
<id>, quit
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
1. Symptom: There are 3 errands lines (#1, #2, #3), but the last line says "You have 2 errands" instead of "You have 3 errands".
2. The count line prints the value of `count` (evidence: [trace] line showing it is 2).
3. Cause (file and line): file: `tracker-v0.js`, line `36`

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

## Bug B

**Steps to reproduce**

1. node bug-hunt/tracker-v0.js
2. list
3. search buy
4. quit

**Expected:**
```
$ node bug-hunt/tracker-v0.js
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> search buy
#1 [ ] Buy a notebook
You have 1 errands
```

**Actual:**
```
$ node bug-hunt/tracker-v0.js
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> search buy
No errands match.
```

**Cause Chain**
1. Symptom: There is 1 task that contains the word 'Buy', but 'search buy' is case-sensitive.
2. The query contains `buy` and `matches` is `false`, where when query is `Buy` the `matches` is `True`.
3. Cause (file and line): file: `tracker-v0.js` and line no. `56`.

**Evidence**
```
> search buy
[trace] searchErrands: {"text":"Buy a notebook","query":"buy","matches":false}
[trace] searchErrands: {"text":"Order a gas cylinder","query":"buy","matches":false}
[trace] searchErrands: {"text":"Fix the bike light","query":"buy","matches":false}
No errands match.
> search Buy
[trace] searchErrands: {"text":"Buy a notebook","query":"Buy","matches":true}
[trace] searchErrands: {"text":"Order a gas cylinder","query":"Buy","matches":false}
[trace] searchErrands: {"text":"Fix the bike light","query":"Buy","matches":false}
[trace] formatErrand: {"id":1,"text":"Buy a notebook","done":false}
[trace] formatList: {"length":1,"count":1}
#1 [ ] Buy a notebook
You have 1 errands
```

**The changes I made:**

Before:
` const matches = errand.text.includes(query); `

After:
`const matches = errand.text.toLowerCase().includes(query.toLowerCase()); `

**How I confirmed the fix**

By searching for keywords in the given list. The chages to line `56` ensure the query and original text are in the lower-case.

Confirmed by running test:  
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


## Bug C

**Steps to reproduce**

1. node bug-hunt/tracker-v0.js --trace
2. list
3. done 1
4. list
4. quit

**Expected:**

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
[trace] markDone: {"id":2,"text":"Order a gas cylinder","done":false}
[trace] markDone: {"id":3,"text":"Fix the bike light","done":false}
Done #1
```

**Actual:**

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

**Cause Chain**

1. Sympton: The [trace] shows errands (#1, #2, #3), after `done 1`, the all of their ids are set to `1` and done to `true`
2. The `errand.id` is assigned (`=`) to the search id instead of `===` (comparison).
3. Cause (file and line): file: `tracker-v0.js` and line no. `69`.

**Evidence**

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
> done 2
[trace] markDone: {"id":2,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":2,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":2,"text":"Fix the bike light","done":true}
Done #2
> done 3
[trace] markDone: {"id":3,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":3,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":3,"text":"Fix the bike light","done":true}
Done #3
> done 4
[trace] markDone: {"id":4,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":4,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":4,"text":"Fix the bike light","done":true}
Done #4
> done 5
[trace] markDone: {"id":5,"text":"Buy a notebook","done":true}
[trace] markDone: {"id":5,"text":"Order a gas cylinder","done":true}
[trace] markDone: {"id":5,"text":"Fix the bike light","done":true}
Done #5
```

**The changes I made:**

Before:
`if (errand.id = id)`

After:
`if (errand.id === id)`

**How I confirmed the fix**

Since Bug E interferes with actually displaying the `x` mark when list is called. By following the trace, we can be ensured that actual change is taking place. As evidence shows, previously, no matter what `done <value>` would've been entered, all id's would've been set to the given `<value>`. Now the program clearly states when an Id does not exist and if it doesn, set's it's done to `true`.

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


## Bug D

**Steps to reproduce**
1. node bug-hunt/tracker-v0.js
2. `add __` '_' here dictate 1 space
3. list
4. quit   

**Expected:**

```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> add
Errand text cannot be empty.
```

**Actual:**

```
> add
Added #4:
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
#4 [ ]
You have 4 errands
```

**Cause Chain**
1. Symptom: Adding 2 or more spaces adds an empty errand. Whereas, 1 space results in no empty errand being added.
2. Empty errand is printed when doing `list`.
3. Cause (file and line): file: `tracker-v0.js` and line no: `45`

**Evidence**

```
Errand tracker v0. Commands: add <text>, list, search <text>, done <id>, quit
> add
[trace] addErrand: {"text":"","nextId":4}
Errand text cannot be empty.
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

**The changes I made:**

Before: 
`if (text === '')`

After:
`if (text.trim() === '')`

**How I confirmed the fix**

The test:
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

Run after fixing the bug:
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

Here the first `add` and vlaue was left empty. The subsequent one tried 10 spaces. 
Both were rejected. Thus, fixing the bug.


## Bug E

**Steps to reproduce:**

1. node bug-hunt/tracker-v0.js --trace
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

**Actual:**

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

**Cause Chain**

1. Symptom: done 1 and done 2, do not show the errands as ticked off
2. The errands are done, but they're not displayed as being ticked (evidence shows 1 as done, but displayed as [ ])
3. Cause (file and line): file: `tracker-v0.js` and line no: `26`

**Evidence**

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

**The changes I made**

Before:
`const tick = errand.don ? '[x]' : '[ ]';  `

After:
`const tick = errand.done ? '[x]' : '[ ]';  `

**How I confirmed the fix**

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

Run after fix:
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

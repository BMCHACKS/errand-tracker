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
3. Cause (file and line): file: `tracker-v0.js` and line no. ``.

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

**How I confirmed the fix**

## Bug C

**Steps to reproduce**
**Expected:**
**Actual:**
**Cause Chain**
**Evidence**
**The changes I made:**
**How I confirmed the fix**

## Bug D

**Steps to reproduce**
**Expected:**
**Actual:**
**Cause Chain**
**Evidence**
**The changes I made:**
**How I confirmed the fix**
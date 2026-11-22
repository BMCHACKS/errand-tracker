chosen: (write three of B, C, D, E here, for example: B, D, E)

# Bug log

One section per bug: bug A from lab 1.13, then the three you chose.
Write in your own words. Paste your own output, from your own tracker, with your own sample errands.

## Bug A: the count is wrong

**Steps to reproduce** (exact commands, in order, from a fresh start):
1. node ./bug-hunt/tracker-v0.js
2. list
3. quit

**Expected:**

Errand tracker v0. Commands: add <text>, list, search <text>, done
<id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 3 errands
> quit
Bye.

**Actual:** (paste the output)

Errand tracker v0. Commands: add <text>, list, search <text>, done
<id>, quit
> list
#1 [ ] Buy a notebook
#2 [ ] Order a gas cylinder
#3 [ ] Fix the bike light
You have 2 errands
> quit
Bye.

**A guess I ruled out, and the one change that ruled it out:**

guess 3, ruled out by adding one errand.

**Cause chain** (from what I saw to the line that causes it; each link with its evidence):
1. Symptom: There are 3 errands lines (#1, #2, #3), but the last line says "You have 2 errands" instead of "You have 3 errands".
2. 
3. Cause (file and line):

**Evidence** (paste the `[trace]` lines that show each link):

**The change I made** (the line before and after):

**How I confirmed the fix** (the test and the run after the change):

## Bug ?

(copy the headings above for each of your three bugs; start each heading with "## Bug B", "## Bug C", "## Bug D" or "## Bug E")

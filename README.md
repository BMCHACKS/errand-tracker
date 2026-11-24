# Errand tracker

A small command-line errand tracker.

**How to Run it**

```
npm start
```

**Features**

- add  some_errand : adds a given errand to the tracker
- list : shows all errands
  - list open : displays all open errands
  - list done : displays all done errands
- search ___ : searches for a given keyword in all tasks, displays all tasks which include the given string
- count : displays count of done and open errands
- quit : ends the program

**Sample Run**

```
$ npm start

> errand-tracker@1.0.0 start
> node src/cli.js

Errand tracker. Type help to see the commands, quit to stop.
> add Meet CEO of NDURE
Added #1: Meet CEO of NDURE
> list
#1 [ ] Meet CEO of NDURE
You have 1 errand
> add Go to design workshop on 25th nov
Added #2: Go to design workshop on 25th nov
> add Design 3D keyword with Mr. Brown
Added #3: Design 3D keyword with Mr. Brown
> list
#1 [ ] Meet CEO of NDURE
#2 [ ] Go to design workshop on 25th nov
#3 [ ] Design 3D keyword with Mr. Brown
You have 3 errands
> done 1
Done #1: Meet CEO of NDURE
> list
#1 [x] Meet CEO of NDURE
#2 [ ] Go to design workshop on 25th nov
#3 [ ] Design 3D keyword with Mr. Brown
You have 3 errands
> list done
#1 [x] Meet CEO of NDURE
You have 1 errand
> list open
#2 [ ] Go to design workshop on 25th nov
#3 [ ] Design 3D keyword with Mr. Brown
You have 2 errands
> count
Open: 2, done: 1
> quit
Bye.
```


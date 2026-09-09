# CMPUT 455 — Assignment 1: Heap Go
### Everything in one place (spec + rules + starter code + tests + workflow)

**Due:** Mon Sep 21, 2026, 11:55pm — submit `assignment1.tgz` on Canvas (group leader only)
**Worth:** 5 marks (5% of course grade)
**Responsible TA:** Parash

---

## 1. Task Summary

Implement a Python program `a1.py` for the game **Heap Go**:
1. **Part A — Rules**: represent the game state, enforce legal moves, detect game over, compute score/winner.
2. **Part B — Random player**: given the current state, generate a uniformly random legal move (`genmove`).

Your program is a **text-command interpreter**: it reads one command per line from stdin, executes it, prints any output, then prints a status line (`= 1` success, `= -1` error).

---

## 2. Rules of Heap Go

Heap Go is a simplified model of Go endgames, for two players: **Black `'b'`** and **White `'w'`**.

- The game consists of one or more **heaps**. Each heap is a **list of tokens**.
- A token is a tuple **`(color, value)`**, color is `'b'` or `'w'`, value is a positive integer.
- Tokens are always taken from the **top = end of the list**.
- A **komi** (a real number, ending in `.5` for this assignment so there are no draws) is added to White's starting score to compensate for Black usually moving first. Komi can be negative. Range: **-100 < komi < 100**.

### Start-of-game restrictions (for this assignment)
- Number of heaps: **1 to 10**
- Tokens per heap: **1 to 10**
- Token value: integer **1 to 20** (inclusive)
- Black's score starts at 0, White's score starts at komi.
- By default Black plays first, but `toplay` can change this at any time.

### How a move works
The player to move selects a **non-empty** heap, then takes tokens from the top of that heap in this order:

1. **Take all** consecutive tokens of their **own** color from the top (zero or more).
2. Then, if the heap is **still not empty**: take **exactly one more** token, which must be the **opponent's** color (zero or one — i.e. optional).

All taken tokens' values are added to the mover's score. Turn passes to the other player after a move.

### Worked example (from the assignment page)

```
G = [ [('w', 9), ('b', 12), ('w', 5)],  [('b', 6), ('b', 9)] ]
komi = 0.5
```

**If Black is to play**, there are two legal moves:

- **Play heap 0**: top token is `('w', 5)` — opponent's color, so step 1 takes zero tokens. Step 2 takes that one opponent token. Black scores `+5`. New state: `[[('w', 9), ('b', 12)], [('b', 6), ('b', 9)]]`
- **Play heap 1**: top two tokens `('b', 9)` and `('b', 6)` are both Black's — step 1 takes both (`9+6=15`). Heap now empty, so step 2 doesn't apply. New state: `[[('w', 9), ('b', 12), ('w', 5)], []]`

**If White is to play instead** (same starting G):
- **Play heap 0**: take `('w', 5)` (own color) then `('b', 12)` (opponent) → score `5+12=17`. New state: `[[('w', 9)], [('b', 6), ('b', 9)]]`
- **Play heap 1**: no white tokens on top, so take one opponent token `('b', 9)` → score `9`. New state: `[[('w', 9), ('b', 12), ('w', 5)], [('b', 6)]]`

### End of game
Game is over when **all heaps are empty**. Compare Black's score to White's score (which includes komi). Higher score wins. (Ties are avoided in this assignment because komi is always `x.5`.)

---

## 3. Text Commands You Must Implement

Format for every command in test files / stdin:
```
command args...
output line(s), if any
= 1          (or = -1 on any error)
```

| Command | Behavior |
|---|---|
| `heapgo komi G` | Start a new game. `komi` is a number; `G` is a Python-literal list of heaps as shown above. Validate everything (heap count 1–10, tokens per heap 1–10, colors `b`/`w` only, values 1–20, komi in range). Any malformed input → `= -1`. Black plays first by default. |
| `show` | Print `k <komi> <G>` — the exact current komi and game state, formatted like a Python list/tuple repr. |
| `toplay color` | Set whose turn it is (`b` or `w`). |
| `play heap_number` | Apply the move-taking rule above to that heap for the current `toplay` color; update scores; switch `toplay`. Errors on invalid/out-of-range/empty heap. |
| `legal heap_number` | Print `yes` or `no` — whether that heap number currently has a legal move (exists and is non-empty). **Status is `= 1` as long as the argument parses as a nonnegative integer**, regardless of yes/no. Non-integer input → `= -1`. |
| `genmove` | Choose a **uniformly random legal move** among all non-empty heaps, play it, print the heap number chosen. `= -1` if no moves remain (game over). |
| `score` | Print `b <blackscore> w <whitescore>` (white's score already includes komi). |
| `winner` | If the game is over (all heaps empty), print `b` or `w`. If not over, `= -1`. |

### Important edge cases seen in the public tests
- `heapgo` with missing komi, missing game, or garbage text → `= -1`.
- `legal 42` on a game with only 2 heaps → `no`, but still `= 1` (42 is a valid nonnegative integer, just out of range).
- `legal random text` → `= -1` (not a valid nonnegative integer).
- Playing an already-empty heap → `= -1`.
- `genmove` with only one legal heap left must always pick that heap (not random among zero choices).
- `winner` before the game ends → `= -1`.

---

## 4. Starter Code — `a1.py` (as provided, unmodified)

```python
# CMPUT 455 Assignment 1 starter code
# Implement the specified commands to complete the assignment
# Full assignment specification and game rules on Canvas

from sys import stderr
from typing import List, Dict, Callable

def not_yet() -> bool:
    raise NotImplementedError("Command not implemented.")
    return False

def print_error(error: str) -> None:
    print(error, file = stderr)

CommandMap = Dict[str, Callable[[str], bool]]

class CommandInterface:
    def __init__(self) -> None:
        # you can add your own initialisation here
        self.commands: CommandMap = {
            "help": self.cmd_help,
            "heapgo": self.cmd_heapgo,
            "show": self.cmd_show,
            "toplay": self.cmd_toplay,
            "play": self.cmd_play,
            "legal": self.cmd_legal,
            "genmove": self.cmd_genmove,
            "score": self.cmd_score,
            "winner": self.cmd_winner,
            }

#============================================================================
# You need to implement the following methods.
#============================================================================
    def cmd_heapgo(self, args: str) -> bool:
        return not_yet()
    def cmd_show(self, args: str) -> bool:
        return not_yet()
    def cmd_toplay(self, args: str) -> bool:
        return not_yet()
    def cmd_play(self, args: str) -> bool:
        return not_yet()
    def cmd_legal(self, args: str) -> bool:
        return not_yet()
    def cmd_genmove(self, args: str) -> bool:
        return not_yet()
    def cmd_score(self, args: str) -> bool:
        return not_yet()
    def cmd_winner(self, args: str) -> bool:
        return not_yet()
#============================================================================
# End of functions requiring implementation
#============================================================================

#============================================================================
# The code below should not need modification
# Anyway, you may change or add to this code as you see fit
# Examples:
# You can add class variables to __init__ above
# You can add better error messages
# You can put commands inside your own Heap Go class
# etc.
#============================================================================
    # List available commands
    def cmd_help(self, ignore_args: str) -> bool:
        print("\nKnown commands:")
        for cmd in self.commands:
            print(cmd)
        return True

    def process_command(self, cmd_name: str, cmd_args: str) -> None:
        # Try to find command, None if wrong name
        status = "= -1"
        cmd = self.commands.get(cmd_name)
        if cmd:
            try:
                if cmd(cmd_args): # success!
                    status = "= 1"
            except Exception as e:
                print_error(f"Command {cmd_name} with arguments {cmd_args} failed with exception: {e}")
        else:
            print_error("Unknown command. Type 'help' for commands.")
        print(status)
    
    def main_loop(self) -> None:
        process_commands = True
        while process_commands:
            try:
                line = input()
            except EOFError:
                break
            line = line.strip()
            if not line or line.startswith("#"):
                continue
            parts = line.split(maxsplit=1)
            cmd_name = parts[0]
            if cmd_name == "exit":
                process_commands = False
                continue
            cmd_args = parts[1] if len(parts) > 1 else ""
            self.process_command(cmd_name, cmd_args)

if __name__ == "__main__":
    interface = CommandInterface()
    interface.main_loop()
```

### How the interpreter loop works (read this before coding)
- `main_loop()` reads one line at a time, splits it into `cmd_name` + `cmd_args` (everything after the first word).
- `process_command()` looks up your `cmd_*` function and calls it with the args string.
- If your function **returns `True`** → prints `= 1`. If it **returns `False`** or **raises an exception** → prints `= -1`.
- So: your `cmd_*` functions should do their own argument parsing (from the raw string `args`), print whatever output the spec requires with `print(...)`, and return `True`/`False` accordingly. You can also just `raise` (e.g. a `ValueError`) on bad input instead of manually returning `False` — either produces `= -1`.
- `help` and `exit` are already implemented for you; don't worry about those.

---

## 5. Public Test File — `assignment1-public-tests.txt`

This is what `a1test.py` runs against your program. Study it closely — it's your best source of *exact* expected output formatting.

```
#CMPUT 455 - Assignment 1 public tests
#======================================================================
# Output format for each command:
#   command
#   output lines (if any)
#   = 1        on success
#   = -1       on error
# Lines starting with ? are marked tests. Lines starting with # are comments.
#======================================================================

# 1 heap 1 token
?heapgo 0.5 [[('w', 2)]]
= 1

# show prints the komi, then a space, then the game state
?show
k 0.5 [[('w', 2)]]
= 1

?toplay b
= 1

?score
b 0 w 0.5
= 1

?legal 0
yes
= 1

?legal 1
no
= 1

# game not over
?winner
= -1

?play 0
= 1

?show
k 0.5 [[]]
= 1

?score
b 2 w 0.5
= 1

?winner
b
= 1

# no moves left
?genmove
= -1

#======================================================================
# 1 heap 2 tokens
?heapgo 0.5 [[('w', 2), ('b', 2)]]
= 1

?show
k 0.5 [[('w', 2), ('b', 2)]]
= 1

?legal 0
yes
= 1

?legal 1
no
= 1

?legal 42
no
= 1

?legal random text
= -1

# play b --> both tokens taken
?play 0
= 1

?show
k 0.5 [[]]
= 1

?score
b 4 w 0.5
= 1

?winner
b
= 1

#======================================================================
# heapgo needs both a komi and a game
?heapgo [[('w', 2)]]
= -1

?heapgo 0.5
= -1

?heapgo this is bad input
= -1

# negative komi, white to play
?heapgo -2.5 [[('b', 3), ('w', 1)]]
= 1

?toplay w
= 1

?show
k -2.5 [[('b', 3), ('w', 1)]]
= 1

?score
b 0 w -2.5
= 1

# white takes ('w', 1), then one opponent token ('b', 3)
?play 0
= 1

?score
b 0 w 1.5
= 1

?winner
w
= 1

#======================================================================
# Example game G from the assignment page, Black to play
?heapgo 0.5 [[('w', 9), ('b', 12), ('w', 5)], [('b', 6), ('b', 9)]]
= 1

?show
k 0.5 [[('w', 9), ('b', 12), ('w', 5)], [('b', 6), ('b', 9)]]
= 1

?legal 0
yes
= 1

?legal 1
yes
= 1

?legal 2
no
= 1

# Black takes the top token ('w', 5) from heap 0
?play 0
= 1

?show
k 0.5 [[('w', 9), ('b', 12)], [('b', 6), ('b', 9)]]
= 1

?score
b 5 w 0.5
= 1

# after a move, it is the other player's turn: White takes ('b', 12)
?play 0
= 1

?show
k 0.5 [[('w', 9)], [('b', 6), ('b', 9)]]
= 1

?score
b 5 w 12.5
= 1

# Black takes ('b', 9), ('b', 6): heap empty, so no opponent token
?play 1
= 1

?show
k 0.5 [[('w', 9)], []]
= 1

?score
b 20 w 12.5
= 1

?winner
= -1

# only one move left, so genmove must play heap 0
?genmove
0
= 1

?show
k 0.5 [[], []]
= 1

?score
b 20 w 21.5
= 1

?winner
w
= 1

#======================================================================
# Example game G from the assignment page, White to play
?heapgo 0.5 [[('w', 9), ('b', 12), ('w', 5)], [('b', 6), ('b', 9)]]
= 1

?toplay w
= 1

# White takes ('w', 5), then ('b', 12): score 17
?play 0
= 1

?show
k 0.5 [[('w', 9)], [('b', 6), ('b', 9)]]
= 1

?score
b 0 w 17.5
= 1

# Black takes ('b', 9), ('b', 6): heap empty
?play 1
= 1

?show
k 0.5 [[('w', 9)], []]
= 1

?score
b 15 w 17.5
= 1

# heap 1 is empty, so playing it is an error
?play 1
= -1

# White takes ('w', 9)
?play 0
= 1

?score
b 15 w 26.5
= 1

?winner
w
= 1

#======================================================================
# genmove picks a random non-empty heap and prints its number
?heapgo 0.5 [[('b', 1)], [('w', 2)], [('b', 3)]]
= 1

?genmove
@[0-2]
= 1

?winner
= -1
```

> Note the last test: `@[0-2]` is a **regex pattern**, not literal text — `a1test.py` accepts any of `0`, `1`, or `2` there, since `genmove` is random.

---

## 6. `a1test.py` — the grading script (as provided, don't modify)

Run it like this once you've implemented `a1.py`:

```bash
python3 a1test.py a1.py assignment1-public-tests.txt
```

Optional verbose flag for more detail on failures:
```bash
python3 a1test.py a1.py assignment1-public-tests.txt -v
```

It will print a per-test diff for anything that fails, then a summary:
```
Summary report:
N tests performed
X / N output statuses matched.
X / N command outputs matched.
X / N tests timed out.
```
...and finally your **public test mark** (worth 1 of the 5 assignment marks / 20%).

You do **not** need to read or modify this file — just use it. (Full source is available if curious, but not required reading.)

---

## 7. Testing & Submission Workflow

1. Implement all 8 `cmd_*` methods in `a1.py`.
2. Run `python3 a1test.py a1.py assignment1-public-tests.txt` locally and fix mismatches.
3. Write your **own** additional test cases (same file format) covering edge cases the public tests don't hit — invalid komi, boundary heap/token counts, single-token heaps, all-same-color heaps, etc. Private tests are much more thorough than public ones.
4. **Go to (or SSH into) a UCOMM lab machine** — grading happens there, on **Python 3.8.10** (do NOT use `ucomm-2030-w01`, it's a different/newer setup). Confirm your version with `python3 --version`.
5. On the lab machine:
   - Use the `script` command to start logging your terminal session.
   - Run the public test script again there: `python3 a1test.py a1.py assignment1-public-tests.txt`
   - Type `exit` to stop `script` logging — this produces a typescript file; save/rename it as `test.log`.
6. Assemble your final `assignment1` folder containing **exactly**:
   - `a1.py` (your completed solution)
   - `a1test.py`
   - `assignment1-public-tests.txt`
   - `test.log`
   - `readme.txt` — team member names + student IDs, **group leader listed first**
7. Compress: `tar -cvzf assignment1.tgz assignment1`
8. **Sanity check the archive** before submitting:
   ```bash
   tar -tf assignment1.tgz
   ```
   Confirm there's exactly one `assignment1/` directory with exactly those 5 files inside — no `__pycache__`, no `.DS_Store`, no nested extra folders. If junk shows up, re-tar with e.g. `--exclude=__pycache__`.
9. Group leader submits `assignment1.tgz` on Canvas.

### SSH access to a lab machine (if not physically in a lab)
```bash
ssh your_ccid@ohaton.cs.ualberta.ca      # shared login node — do NOT run/test code here
ssh your_ccid@ucomm-XXXX-wYY.cs.ualberta.ca   # then hop to a specific undergrad machine
```
(`XXXX` = room number, `YY` = machine number; use your CCID + CCID password.) VS Code users can set up "Remote-SSH" with a `ProxyJump ohaton` config for a near-local experience — see the SSH guide if you want that.

---

## 8. Grading Breakdown

| Component | Weight |
|---|---|
| `test.log` + `readme.txt` present & correct | 1 mark (20%) |
| Passing public tests | 1 mark (20%) |
| Code quality | 1 mark (20%) |
| Passing private tests | 2 marks (40%) |

Additional policy notes:
- **Submission format is worth 20% on its own** — wrong filenames, wrong folder structure, or missing files can tank this regardless of correct code. Extra files are fine; extra directory nesting is not.
- Private tests check exact output down to whitespace, and include many more edge cases (large/small/special values) than the public set — but nothing outside the written spec.
- Code that doesn't run, or hardcodes answers to the public tests, receives **0**.
- **No late submissions.**
- AI coding assistants are allowed only to *review/improve* code or help you *learn* — not to generate your solution. Graders explicitly look for "AI-like artifacts" as a code-quality red flag.
- Tested on undergrad lab machines running **Python 3.8.10**.
- No tools that partially compile Python beyond the standard `python3` interpreter.

---

## 9. Suggested Two-Person Work Split

Since you're pairing up and working side by side:

**Together first (before splitting anything):**
- Read this whole doc + work through the example game `G` by hand until you both agree on what `play` produces at each step.
- Agree on your **game state representation** (how you'll store heaps/komi/scores/toplay as instance variables) — everything else depends on this being settled first.
- Agree on the *signature* of a shared helper function that performs the "take tokens from a heap" logic, since both `play` and `legal`/`genmove` need it.

**Then split:**
| Person A | Person B |
|---|---|
| `cmd_heapgo` (parsing + all validation — trickiest part) | Shared `take_from_heap()` helper + `cmd_play` |
| `cmd_show`, `cmd_toplay`, `cmd_score` | `cmd_legal`, `cmd_genmove`, `cmd_winner` |

**Back together:**
- Integrate both halves, run the public test file line-by-line together.
- Write and run your own extra edge-case tests together.
- One person drives final packaging: lab-machine testing, `test.log`, `readme.txt`, `tar` sanity check, submission.

---

## 10. Quick Reference Checklist

- [ ] All 8 commands implemented and passing public tests
- [ ] Own edge-case tests written and passing
- [ ] Verified on a UCOMM lab machine with Python 3.8.10
- [ ] `test.log` captured via `script` on the lab machine
- [ ] `readme.txt` with both names + student IDs (leader first)
- [ ] `tar -tf assignment1.tgz` shows exactly: `assignment1/a1.py`, `assignment1/a1test.py`, `assignment1/assignment1-public-tests.txt`, `assignment1/test.log`, `assignment1/readme.txt` — nothing else
- [ ] Group leader submits on Canvas before Sep 21, 11:55pm

# Carmino Says

A neon arcade take on the classic memory game **Simon Says**, built with HTML, CSS and vanilla JavaScript.

**▶ Play it live: https://carminnow.github.io/js-dev-final-capstone-starter-simon-says/**

[Carmino Says start screen] <img width="1280" height="860" alt="image" src="https://github.com/user-attachments/assets/aa0f664e-9a71-47f0-aaa2-0ec12c3bb627" />


## Contents

1. [The Game](#1-the-game)
2. [Plan: Functions, Features, Algorithms and Coding Choices](#2-plan-functions-features-algorithms-and-coding-choices)
3. [Implementation Plan](#3-implementation-plan)
4. [Reflection: Coding Trade-offs](#4-reflection-coding-trade-offs)
5. [Choices, Challenges and Debugging](#5-choices-challenges-and-debugging)
6. [AI Tools Used](#6-ai-tools-used)
7. [Project Summary](#7-project-summary)
8. [Commit History and Screenshots](#8-commit-history-and-screenshots)
9. [Run It Locally](#run-it-locally)

---

## 1. The Game

I chose to build **Simon Says**, a memory game. The computer lights up a sequence of colored pads, and each pad plays its own tone. The player repeats the sequence from memory. Every round adds one more pad to the sequence. One wrong press ends the game, and clearing every round wins it.

I named my version **Carmino Says** and kept a neon arcade look, which matches the arcade photo in the background.

**How to play**

1. Choose a difficulty from the dropdown.
2. Click **Start**.
3. Watch and listen as the computer plays the sequence.
4. When the status says it's your turn, click the pads in the same order.
5. Get it right and the sequence grows by one. Get it wrong and the game resets.

| Level | Rounds to win |
|---|---|
| 1 | 8 |
| 2 | 14 |
| 3 | 20 |
| 4 | 31 |

---

## 2. Plan: Functions, Features, Algorithms and Coding Choices

### Features

- Four colored pads, each with its own sound and a "lit" state
- A computer turn that plays a growing random sequence
- A player turn that checks every press as it happens
- Status messages showing whose turn it is and how many presses are left
- Win and lose detection, followed by a full reset
- **Originality (US-06):**
  - a difficulty dropdown (levels 1 to 4)
  - an original looping background track
  - a dropdown styled to match the Start button

### Data

| Variable | Purpose |
|---|---|
| `pads` | Array of four objects, each holding a pad's `color`, its DOM element (`selector`) and its `sound` |
| `computerSequence` | The colors the computer has chosen so far |
| `playerSequence` | The colors the player has pressed this round |
| `roundCount` / `maxRoundCount` | The current round and how many rounds are needed to win |
| `backgroundMusic` | One looping `Audio` object at 25% volume |

### Functions

| Function | Job |
|---|---|
| `startButtonHandler()` | Reads the level, sets the round limit, hides the controls, starts the music and starts the computer's turn |
| `setLevel(level)` | Turns a level (1–4) into a number of rounds (8, 14, 20 or 31), or returns an error message |
| `getRandomItem(array)` | Picks a random item from an array |
| `setText(element, text)` | Sets the text of an element |
| `activatePad(color)` | Lights up one pad and plays its sound for 500 ms |
| `activatePads(sequence)` | Plays a whole sequence, one pad every 600 ms |
| `playComputerTurn()` | Locks the pads, adds a random color and plays the sequence |
| `playHumanTurn()` | Unlocks the pads and shows how many presses are needed |
| `padHandler(event)` | Handles a click on any pad |
| `checkPress(color)` | Compares the player's latest press with the computer's sequence |
| `checkRound()` | After a completed round: either wins the game or starts the next round |
| `resetGame(text)` | Shows the result and puts the game back to its starting state |

### Algorithms

- **Random choice:** `Math.floor(Math.random() * pads.length)` gives a whole number from 0 to 3, so each pad has an equal chance of being picked.
- **Growing sequence:** each computer turn *adds* one color to `computerSequence` and never clears it until the game resets. That makes every round one step harder.
- **Timing a sequence:** `setTimeout` doesn't pause the code, so each pad gets its own timer with a delay of `(index + 1) * 600` ms. Pads then play in order, 600 ms apart, instead of all at once.
- **Handing over to the player:** the player's turn starts `roundCount * 600 + 1000` ms after the computer's turn begins. That's when the last pad finishes, plus one second.
- **Checking a press:** the newest press is always at index `playerSequence.length - 1`, and I compare it with the same index in `computerSequence`. Presses left is `computerSequence.length - playerSequence.length`.

### Coding choices

- **One click listener on the pad container** (event delegation) instead of four listeners. `padHandler` reads `data-color` from whichever pad was clicked.
- **A `pads` array of objects**, so `pads.find(pad => pad.color === color)` gets any pad's element and sound in one line. No function needs a separate case for each color.
- **CSS classes for state** (`hidden`, `activated`, `unclickable`) instead of changing styles in JavaScript. The JavaScript only adds and removes class names.
- **A `switch` in `setLevel`**, because it compares one value against several exact values.

---

## 3. Implementation Plan

I built the game one user story at a time and committed after each step:

| Step | User story | What I implemented |
|---|---|---|
| 1 | **US-01** Basic structure | Title, Start button and four pads in `index.html`. Query selectors and the `pads` array in `index.js`. |
| 2 | **US-02** Initialize game | `setLevel`, `startButtonHandler` and the Start button's click listener |
| 3 | **US-03** Computer's turn | `getRandomItem`, `setText`, `activatePad`, `activatePads`, `playComputerTurn` |
| 4 | **US-04** Player's turn | `playHumanTurn`, `padHandler`, `checkPress`, `checkRound` |
| 5 | **US-05** Reset game | `resetGame`, which made the game fully playable from start to finish |
| 6 | **US-06** Originality | Difficulty dropdown, background music and dropdown styling |
| 7 | **US-07** Deploy | GitHub Pages, sound path fix, README |

After each step I checked the work in the browser, and against the project's tests where they applied, before moving on. I also kept a `USER_STORIES.md` file with each story's acceptance criteria written in Gherkin format and a running checklist.

---

## 4. Reflection: Coding Trade-offs

- **The heading resets to "Simon Says", not "Carmino Says."** The US-05 test checks that `resetGame()` contains the exact code `setText(heading, "Simon Says")`. I kept that line so the test passes, even though it means my own title doesn't come back after a game ends.
- **`setLevel()` with an empty-parentheses fallback.** The US-02 test checks that `startButtonHandler` contains the text `setLevel()`. To add the dropdown without breaking that test, I wrote `maxRoundCount = level ? setLevel(level) : setLevel();`. It uses the player's level, and falls back to the default if there isn't one.
- **`alert()` for game over and wins.** It's simple and required by the tests, but it freezes the page while it's open. Because of that I pause the music *before* the alert, so it doesn't keep playing behind the popup.
- **Timers instead of async/await.** I scheduled pads with `setTimeout` delays, following the starter code. It works, but the timing formulas have to match each other: 500 ms lit, 600 ms per pad, plus a 1-second handover.
- **Generated music instead of a downloaded track.** An original track avoids licensing questions. I kept it quiet (25% volume) so the pad tones stay clear.
- **Keeping the starter arcade style.** I tried a medieval tavern theme but decided to keep the arcade look. I put my styling time into making the new dropdown match it. The dropdown reuses the Start button's CSS rules instead of copying them.
- **A known small bug I left in.** During the 1-second "Nice, Keep going!" pause, the pads can still be clicked, and a click there counts as a correct round. Fixing it means locking the pads at the start of that pause in `checkRound()`.

---

## 5. Choices, Challenges and Debugging

| Problem | How it showed up | Cause | Fix | Commit |
|---|---|---|---|---|
| Whole game stopped working | Nothing in `index.js` ran | `setLevel()` was missing its closing `}`. One missing brace breaks the entire file. | Added the brace | [`89cde86`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/89cde86) |
| Game froze on the first pad press | `ReferenceError` in the console | Typo: `computersequence` instead of `computerSequence` (JavaScript names are case-sensitive) | Fixed the spelling | [`1ba6699`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1ba6699) |
| Game froze after finishing a round | `ReferenceError`, but only after a round was completed | Typo: `rountCount` instead of `roundCount` | Fixed the spelling | [`1ba6699`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1ba6699) |
| Clicked pads didn't light up | Sound played, but no light | `padHandler` removed the `activated` class without ever adding it | Added `classList.add("activated")` | [`1ba6699`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1ba6699) |
| No sound, especially on GitHub Pages | `ERR_FILE_NOT_FOUND` for the mp3 files | Audio paths are resolved from `index.html`, not from `index.js`, so `../assets/` pointed outside the project | Changed to `./assets/` | [`c614900`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/c614900) |
| Start button did nothing after adding the dropdown | `levelSelect is not defined` | I used `levelSelect` but never created it with `querySelector` | Added the query selector | [`b5aa359`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/b5aa359) |


**What I took from this:**
- A syntax check can't catch a misspelled variable, because those errors only appear when that line runs. Playing the game with the browser console open (F12) caught them.
- For repeated colors, I used `playerSequence.length - 1` to find the newest press instead of `indexOf(color)`. `indexOf` returns the *first* match, which breaks on a sequence like red, blue, red.

---

## 6. AI Tools Used

| Tool | What I used it for | Why |
|---|---|---|
| **Claude (Anthropic), via Claude Code** | Turning the user stories into plain-language stories, Gherkin acceptance criteria and a progress checklist | To plan the work and track it like a real project |
| | Reviewing each commit, and finding the bugs listed in section 5 | A second pair of eyes catches typos and logic gaps faster |
| | Playing the game in a headless browser to confirm each feature worked, and replaying the project's test checks | To verify behavior, not just read the code |
| | Composing the original background music and capturing the screenshots in this README | No licensing issues, and the screenshots match my exact code |
| | Drafting this README, which I then reviewed and edited | To organize the documentation clearly |

I wrote and committed the game code myself, using the AI as a tutor and reviewer. Every change was checked in the browser before I moved on.

---

## 7. Project Summary

- **Process:** I built Carmino Says one user story at a time: HTML structure first, then the computer's turn, the player's turn and the reset. I committed after each feature, so my history shows the game growing piece by piece. I tested every step in the browser before moving on.
- **Challenges:** My hardest bugs came from small mistakes with big effects. A missing brace stopped the whole file from loading. Two misspelled variable names only failed once the game ran. Audio paths are resolved from the HTML page, not the script. Using the browser console turned these from mysteries into quick fixes.
- **Result:** The game is fully playable on GitHub Pages and passes all 43 test checks. It adds a difficulty dropdown, original background music and matching button styling. I used AI as a tutor and reviewer while writing the code myself.

---

## 8. Commit History and Screenshots

Full history: https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commits/main

### Commit history by decision

| Commit | User story | Decision |
|---|---|---|
| [`342c3ed`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/342c3ed) | US-01 | Named the game "Carmino Says" and added the Start button and four pads |
| [`7e022cc`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/7e022cc) | US-01 | Added query selectors and stored each pad's data in the `pads` array |
| [`968d365`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/968d365) | US-02 | Used a `switch` for `setLevel` and wired up the Start button |
| [`89cde86`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/89cde86) | US-03 | Built the computer's turn with `setTimeout` timing; fixed the missing brace |
| [`a94b887`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/a94b887) | US-04 | Built the player's turn, checking the newest press by index |
| [`1ba6699`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1ba6699) | US-04 | Fixed two variable typos and the missing pad light |
| [`18c69df`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/18c69df) | US-05 | Reset the game state and screen after a win or loss |
| [`c614900`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/c614900) | US-07 | Fixed the audio paths so sound works on GitHub Pages |
| [`5f9b769`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/5f9b769) | US-06 | Added the difficulty dropdown to the HTML |
| [`d1b3d0b`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/d1b3d0b) | US-06 | Used the dropdown value with a fallback so the US-02 test still passes |
| [`b5aa359`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/b5aa359) | US-06 | Added the missing `levelSelect` query selector |
| [`4a1bdbc`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/4a1bdbc) / [`4d045b3`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/4d045b3) | US-06 | Added looping background music that starts on Start and stops before the alert |
| [`332668c`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/332668c) | US-06 | Styled the dropdown by sharing the Start button's CSS rules |
| [`457ee39`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/457ee39) / [`7a086d8`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/7a086d8) | Code quality | Fixed inaccurate comments and removed leftover starter comments |
| [`94453b0`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/94453b0) – [`aa0c9c3`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/aa0c9c3) | US-07 | Replaced the starter README with project documentation |

### Screenshots

| Decision | Screenshot |
|---|---|
| **Difficulty dropdown (US-06):** the player picks a level before starting, styled to match the Start button | [Start screen with the level dropdown and Start button]<img width="1280" height="773" alt="image" src="https://github.com/user-attachments/assets/e95f318b-3d1e-4d60-acc9-b887629915e2" /> |
| **Computer's turn (US-03):** the heading shows the round out of the chosen level's total, and pads light up one at a time using `setTimeout` | [Round 3 of 14: the computer lights up the red pad]<img width="1280" height="860" alt="image" src="https://github.com/user-attachments/assets/59519185-700f-41b0-88f2-b0c2fe5315ff" /> |
| **Player's turn (US-04):** `checkPress` updates the presses left after every click | [Round 3 of 14: player's turn with 2 presses left]<img width="1280" height="860" alt="image" src="https://github.com/user-attachments/assets/888ff48c-8f62-4b99-bf8a-d66db3ab8104" /> |
| **Round complete (US-04):** `checkRound` shows "Nice, Keep going!" for one second before the next computer turn | [Round 2 of 14 completed: Nice, Keep going!]<img width="1280" height="860" alt="image" src="https://github.com/user-attachments/assets/166d646a-ec9c-4a43-b5ea-0a6946a6bd59" /> |

---

## Run It Locally

```bash
git clone https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says.git
cd js-dev-final-capstone-starter-simon-says
npm install
```

Open `index.html` with the VS Code **Live Server** extension. Run the tests with:

```bash
npm test          # all user stories
npm run test:1    # one user story (1 to 5)
```

**Project files:**
- `index.html`: the page layout
- `src/index.js`: the game logic
- `src/styles.css`: the styling
- `assets/`: sounds, music and images
- `test/`: the automated tests
- `USER_STORIES.md`: user stories, Gherkin acceptance criteria and the progress checklist

**Acknowledgements:** starter code, pad sounds and tests provided by Thinkful.

-----------------------------------------------------------------------------------------------------------------------------------------

## Round 2: Improvements After Grader Feedback

After my first submission, the grading team asked for two required fixes and suggested three optional improvements:

| Feedback | Type | What I did |
|---|---|---|
| Pads still accept clicks during "Nice, keep going!" | **Required** | **US-08:** lock the pads the moment a round is completed |
| Pads are clickable before the game starts | **Required** | **US-09:** cancel scheduled turns when the game resets |
| Add instructions for first-time players | Optional | **US-10:** a "?" button with a How to play tooltip |
| Make right and wrong feedback more noticeable | Optional | **US-11:** a green glow for a completed round, and a red shake for a wrong press |
| Control music and sound effects separately | Optional | **US-12:** Music and Sounds on/off buttons |

I planned this round the same way as the first: I wrote each item as a user story with Gherkin acceptance criteria and a checklist.

### Investigating the bugs first

Before changing any code, I reproduced both required issues in the browser to find the real cause:

| What I tried | What happened |
|---|---|
| Clicked a pad on a fresh page, before Start | Nothing happened. The pads start locked. |
| Clicked a pad during the "Nice, Keep going!" pause | Each click counted as a whole completed round. `roundCount` jumped to 3 while the heading still said "Round 1". |
| Pressed a wrong pad during that pause | The game ended, but the computer's next turn was already scheduled and ran 1 second later. On the start screen it showed **"Round 0 of 8"** and **unlocked the pads**. |

So both reported problems came from the same gap:
1. `checkRound()` never locked the pads during its 1-second pause.
2. `resetGame()` never cancelled timers that were already scheduled.

The "clickable before the game starts" issue was a ghost turn left over from the previous game.

### New variables and functions

| Name | Purpose |
|---|---|
| `nextTurnTimer` | Holds the ID of the next scheduled turn, so `resetGame()` can cancel it with `clearTimeout()` |
| `instructions` | The How to play tooltip, hidden during a game |
| `musicOn` / `soundsOn` | Whether the music and pad sounds are switched on |
| `toggleMusic()` | Flips `musicOn`, mutes or unmutes `backgroundMusic`, and updates its button |
| `toggleSounds()` | Flips `soundsOn`, mutes or unmutes all four pad sounds, and updates its button |
| `success` / `error` CSS classes | Trigger the `success-glow` and `error-shake` animations on the board and status text |

### Implementation order

| Story | Change | Commits |
|---|---|---|
| US-08 | One line in `checkRound()`: `padContainer.classList.add("unclickable")`, before the pause | [`eacc40a`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/eacc40a) |
| US-09 | Save each turn's `setTimeout` ID in `nextTurnTimer`, and `clearTimeout(nextTurnTimer)` at the start of `resetGame()` | [`94a792c`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/94a792c) |
| US-10 | "?" button and tooltip (HTML), floating panel shown on hover or focus (CSS), hidden during a game (JS) | [`92b9d06`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/92b9d06), [`d969744`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/d969744), [`dec0876`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/dec0876) |
| US-11 | `@keyframes` animations (CSS). JS adds `success` and `error` at the right moments, and delays the game-over alert by 800 ms. | [`7e33836`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/7e33836), [`184907d`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/184907d), [`1940f6d`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1940f6d) |
| US-12 | Two toggle buttons in the corner of the game box, using the audio `.muted` property | [`cd20ba7`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/cd20ba7), [`9be3ce3`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/9be3ce3), [`bf44212`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/bf44212), [`ce9d02c`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/ce9d02c) |

After each story I played the game in the browser and checked that all 43 original test checks still passed.

### Trade-offs in this round

- **Cancelling timers instead of adding guards.** The quick fix would have been `if (roundCount === 0) return;` at the top of `playHumanTurn()` and `playComputerTurn()`. But the original tests call those functions directly on a fresh page, where `roundCount` is 0, so that would have failed them. Cancelling the scheduled turn in `resetGame()` fixes the bug without changing how those functions behave.
- **One timer variable.** After the US-08 fix, only one turn is ever waiting at a time, so a single `nextTurnTimer` covers both the player's turn and the computer's next turn. US-11's delayed game over reuses it too.
- **A hover tooltip instead of a collapsible panel.** My page already had `overflow: hidden` and was 7px too tall for a common laptop window, so anything that grew the layout would push the pads off screen. I compared a collapsible `<details>` panel with a floating tooltip and chose the tooltip, because it floats over the board and doesn't change the layout. The downside: a "?" is less obvious than a "How to play" heading, and on touch screens it has to be tapped.
- **An 800 ms delay before the game-over alert.** `alert()` freezes the page, so an animation started right before it would never be seen. The delay makes the error effect visible, but the game ends slightly later.
- **`.muted` instead of `if` statements.** Muting the audio objects meant I didn't have to change any code that plays sounds. The original tests check for the exact text `pad.sound.play()`, so leaving those lines alone kept them passing, and the music resumes naturally when it's switched back on.

### Challenges and debugging in this round

| Problem | How it showed up | Cause | Fix | Commit |
|---|---|---|---|---|
| Clicks during the pause skipped rounds | `roundCount` jumped while the heading stayed on "Round 1" | Pads stayed unlocked during `checkRound()`'s 1-second pause | Lock the pads at the start of the pause | [`eacc40a`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/eacc40a) |
| Ghost turn after a game over | "Round 0 of 8" and unlocked pads on the start screen | A scheduled `setTimeout` still fired after `resetGame()` | `clearTimeout(nextTurnTimer)` in `resetGame()`. I confirmed it by calling `resetGame()` from the console mid-sequence, before and after the fix. | [`94a792c`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/94a792c) |
| Feedback effects never appeared | No glow or shake at all | My first US-11 commit only included the CSS. The JavaScript that adds the classes wasn't committed. | Committed the JS changes | [`184907d`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/184907d) |
| **A wrong press froze the game** | Pads locked, no alert, no Start button. The console showed `padConstainer is not defined`. | Typo: `padConstainer` | Fixed the spelling | [`1940f6d`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1940f6d) |
| Green glow only played in round 1 | The `success` class stayed on, so adding it again did nothing | A CSS animation only replays when its class is newly added | Remove `success` at the start of each computer turn | [`1940f6d`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/1940f6d) |
| Audio buttons in the wrong corner | Positioned against the browser window instead of the game box | Typo `postiion: relative` on `main`. Without a positioned parent, `position: absolute` uses the whole page. | Fixed the spelling to `position` | [`bf44212`](https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says/commit/bf44212) |

**What I took from this round:**
- Reproducing a bug before fixing it showed me that the two reported issues had one cause, so I fixed the source rather than the symptoms.
- The `padConstainer` typo was the same kind of bug as my first round's `computersequence` and `rountCount`. A syntax check can't catch it, but the browser console shows it the moment that line runs.

### AI tools used in this round

| Tool | What I used it for | Why |
|---|---|---|
| **Claude (Anthropic), via Claude Code** | Turning the grader's feedback into user stories, Gherkin acceptance criteria and a checklist (`ENHANCEMENTS.md`) | To plan the fixes like a real project |
| | Reproducing the reported bugs in a browser and finding their shared cause | To fix the source of the problem, not the symptoms |
| | Explaining techniques the course hadn't covered (`@keyframes`, `translateX`, `drop-shadow`, `clearTimeout`) and comparing implementation options | To understand *why* each approach works before writing it |
| | Building prototypes with screenshots before I wrote code | To see and choose between designs first |
| | Reviewing each commit, finding the `padConstainer` freeze and the missing class removal, and re-running the 43 original test checks | To catch bugs quickly and confirm nothing broke |
| | Capturing the screenshots below and drafting this section, which I reviewed and edited | To document the round clearly |

### Round 2 summary

- **Process:** I turned the grader's feedback into five user stories and reproduced the bugs before changing any code. That showed that both required issues came from one gap: pads left unlocked during the round pause, and scheduled turns that `resetGame()` never cancelled.
- **Challenges:** Several techniques were new to me, including CSS animations, timers I could cancel, and accessible toggles. Working through options with Claude before coding helped me choose approaches that also kept the original tests passing. A typo froze the game once, and the browser console found it immediately.
- **Result:** Both required fixes work, and all three optional suggestions are added: a How to play tooltip, clearer success and error feedback, and separate music and sound controls. All 43 original test checks still pass.

### Round 2 screenshots

| Feature | Screenshot |
|---|---|
| **How to play tooltip (US-10):** hovering or focusing the "?" shows instructions over the board, without changing the layout | [How to play tooltip showing the four steps and the levels]<img width="1280" height="760" alt="image" src="https://github.com/user-attachments/assets/af676d9e-1681-4930-88bc-7f83ebcb3c9c" /> |
| **Success feedback (US-11):** completing a round makes the board glow green, and the pads lock straight away (US-08) | [Round 2 of 14 completed: the board glows green and "Nice, Keep going!" shows]<img width="1280" height="760" alt="image" src="https://github.com/user-attachments/assets/825c8997-75aa-4be1-b536-6c8a6916e374" /> |
| **Error feedback (US-11):** a wrong press shakes the board and turns it red before the game-over alert | [A wrong press: red glow, faded pads and "Wrong pad!"]<img width="1280" height="760" alt="image" src="https://github.com/user-attachments/assets/9ca1ebc4-129e-44c9-9cfb-cc114e394be7" /> |
| **Audio controls (US-12):** Music and Sounds can each be switched off, even mid-game | [Round 3 of 14 with Music and Sounds both switched off]<img width="1280" height="760" alt="image" src="https://github.com/user-attachments/assets/1aea7aff-277b-4542-ad5c-f44c896dec14" /> |

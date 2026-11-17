# Carmino Says

A neon arcade take on the classic memory game **Simon Says**, built with HTML, CSS and vanilla JavaScript.

**▶ Play it live: https://carminnow.github.io/js-dev-final-capstone-starter-simon-says/**

The computer lights up a sequence of colored pads, each with its own tone. Repeat the sequence from memory. Every round adds one more pad. Make a mistake and the game is over; clear every round to win.

## How to Play

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

## Features

- **Four difficulty levels** selectable before each game
- **Original background music**: a looping chiptune track that starts with the game and stops when it ends
- **Sound and light feedback** on every pad, for both the computer's turn and the player's
- **Live status messages** showing whose turn it is and how many presses are left
- **Neon arcade look** to match the game's arcade roots

## Built With

- **HTML5** for the game structure
- **CSS3** for layout (CSS Grid) and styling
- **JavaScript (ES6+)** for the game logic, using DOM selection, event listeners, `setTimeout` timing and the `Audio` API
- **Jest + Puppeteer** for the automated tests

## How It Works

The game loops between two turns:

1. **Computer's turn** (`playComputerTurn`): locks the pads, adds a random color to the sequence, and plays the whole sequence with a 600 ms gap between pads.
2. **Player's turn** (`playHumanTurn`): unlocks the pads. Each click (`padHandler`) is checked against the computer's sequence (`checkPress`).
3. When a round is completed (`checkRound`), the game either advances to the next round or declares a win. A wrong press or a win calls `resetGame`, which clears the game state and brings back the Start screen.

## Run It Locally

```bash
git clone https://github.com/Carminnow/js-dev-final-capstone-starter-simon-says.git
cd js-dev-final-capstone-starter-simon-says
npm install
```

Then open `index.html` with the VS Code **Live Server** extension.

## Run the Tests

```bash
npm test          # all user stories
npm run test:1    # one user story (1 to 5)
```

## Project Structure

| Path | Description |
|---|---|
| `index.html` | Game layout: title, level dropdown, start button, status and pads |
| `src/index.js` | Game logic |
| `src/styles.css` | Styling |
| `assets/` | Pad sounds, background music and images |
| `test/` | Automated tests for each user story |
| `USER_STORIES.md` | User stories, Gherkin acceptance criteria and progress checklist |

## Acknowledgements

- Starter code, pad sounds and tests provided by Thinkful.
- Background music was created for this project with AI assistance.

---------------------------------------------------------------------------------------------------------------------------------------------------------------

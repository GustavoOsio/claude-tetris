# Repository Guidelines

## Project Structure & Module Organization

This is a small, dependency-free browser game. The repository root contains the application files:

- `index.html` defines the page, canvas elements, score panel, and overlays.
- `style.css` contains layout, colors, and responsive presentation.
- `game.js` contains the board model, piece movement, collision checks, scoring, and animation loop.
- `README.md` documents gameplay, controls, and local startup.

There are currently no separate test, build, or asset directories. Keep new assets in a clearly named directory if needed, and update paths in the HTML/CSS accordingly.

## Build, Test, and Development Commands

No build or install step is required. Open `index.html` directly, or run a static server from the repository root:

```bash
python3 -m http.server 8000
```

Then visit `http://localhost:8000`. Node.js is not required. No automated test framework or test command is currently configured; validate gameplay changes manually in a browser.

## Coding Style & Naming Conventions

Use vanilla JavaScript, HTML, and CSS; do not add a framework or package manager without a clear project need. Match existing style: two-space indentation, semicolons in JavaScript, `camelCase` for variables/functions, and `UPPER_SNAKE_CASE` for fixed constants. Keep game logic in `game.js`, presentation in `style.css`, and page structure in `index.html`. Use descriptive kebab-case for any new asset filenames. Preserve accessible labels and keyboard controls when changing the interface.

## Testing Guidelines

There is no automated test suite. For gameplay changes, manually verify movement, rotation near walls, drops, line clears, scoring/level speed, pause/resume, restart, and game-over behavior as applicable. Check the browser console for errors and test at desktop and narrow viewport sizes when UI changes.

## Commit & Pull Request Guidelines

The history currently contains only the initial commit, so no established commit convention is available. Use short imperative messages that describe one change, such as `Add touch controls`. Pull requests should summarize behavior changes, list manual verification performed, link related issues when applicable, and include screenshots for visual changes. Keep changes focused and update `README.md` when controls or startup instructions change.

## Configuration & Dependencies

The game has no external dependencies or secrets. Keep it runnable as static files and avoid adding generated files or local environment data to version control.

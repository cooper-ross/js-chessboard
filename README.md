# js-chessboard

An advanced, dependency-free, and customizable chessboard component for modern web applications.

<div align="center">
  <img src="https://github.com/user-attachments/assets/89408e42-736c-41c3-9862-1e0da8bbb0b0" alt="js-chessboard in action" width="100%"/>
</div>

## Features

-   **No Dependencies**: Written in pure vanilla JavaScript.
-   **Highly Customizable**: Control everything from piece sets and board colors to animation speeds and interaction behaviors.
-   **Modern API**: A clean, chainable event system for handling user interactions and game state changes.
-   **Interactive**: Supports piece dragging, clicking, highlighting squares, and drawing arrows on both PC and mobile devices, as well as promotion (with an inbuilt promotion menu popup).
-   **Game Logic Agnostic**: Integrates with any chess engine or handler (like `chess.js`) for move validation and game state management. A default handler is provided for out-of-the-box functionality, so you can build your own or quickly hook up any existing handler.
-   **Dynamic Theming**: All styling is injected dynamically, allowing for on-the-fly theme changes without reloading the page, and all pieces and audio are automatically cached.
-   **Move Classification**: Native support for move classfication badges.
-   **Fast**: You can have multiple boards rapdily running at once without any performance drops.
-   **Responsive**: The board resizes gracefully with its container.

## Table of Contents

-   [Getting Started](#getting-started)
-   [Instantiation](#instantiation)
-   [Configuration](#configuration)
    -   [Theme Settings](#theme-settings)
    -   [Styling Settings](#styling-settings)
    -   [Behavior Settings](#behavior-settings)
    -   [Callbacks](#callbacks)
    -   [Updating Options](#updating-options)
-   [Public API Methods](#public-api-methods)
    -   [Board State](#board-state)
    -   [Interaction & Appearance](#interaction--appearance)
    -   [Game Logic](#game-logic)
    -   [Customization](#customization)
    -   [Lifecycle](#lifecycle)
-   [Event System](#event-system)
-   [Using a Chess Handler](#using-a-chess-handler)

## Getting Started

First, include the `board.js` file in your project. Since it's an ES module, you can import it into your script.

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>My Chess App</title>
    <style>
        /* Give the container a size */
        #myBoard {
            width: 600px;
            height: 600px;
        }
    </style>
</head>
<body>
    <div id="myBoard"></div>
    <script type="module" src="app.js"></script>
</body>
</html>
```

```javascript
// app.js
import {  } from './board.js';

// Create a new chessboard instance
const board = new Chessboard('#myBoard');
```

Make sure you also have some kind of assets folder (containing pieces and sounds) in the correct path relative to your project, should you want to create or add your own themes and assign them with the configuration option. A bunch of goods ones can be found on the [Lichess Github](https://github.com/lichess-org/lila/tree/master/public/piece), and the board accepts that format if you [specify it](#pieceformat-standard-vs-lichess)!

## Instantiation

You create a new chessboard by calling the constructor.

`new Chessboard(selector, settings, handler)`

| Parameter  | Type   | Description                                                                                              |
| ---------- | ------ | -------------------------------------------------------------------------------------------------------- |
| `selector` | String | A CSS selector for the container element where the board will be rendered (e.g., `'#myBoard'`).            |
| `settings` | Object | (Optional) A configuration object to customize the board's appearance and behavior. See [Configuration](#configuration). |
| `handler`  | Object | (Optional) A chess logic handler, like an instance of `chess.js`. See [Using a Chess Handler](#using-a-chess-handler). |

### Example

```javascript
import { Chessboard } from './board.js';
import { Chess } from 'chess.js'; // Example using chess.js

const game = new Chess();
const board = new Chessboard('#myBoard', {}, game);
```

## Configuration

You can customize the chessboard extensively through the `settings` object passed to the constructor.

### Updating Options

You can get or set options after initialization using `getOption` and `setOption`.

```javascript
// Get a single option
const isInteractive = board.getOption('isInteractive');
console.log(isInteractive); // true

const arrowColor = board.getOption('styling.arrowColor');
console.log(arrowColor); // 'rgba(223, 145, 0, 0.59)'

// Set a single option
board.setOption('audioEnabled', false);

board.setOption({ audioEnabled: false });

// Set multiple options
board.setOption({
    'styling.arrowColor': 'rgba(0, 255, 0, 0.7)',
    'theme.pieceFolderName': 'staunty'
});

board.setOption({
    theme: {
        pieceFoldersPath: 'assets/pieces',
        pieceFolderName: 'fantasy',
        pieceFormat: 'lichess'
    },
    audioEnabled: false
})
```

### Theme Settings

Located under `settings.theme`.

| Key | Default | Description |
| --- | --- | --- |
| `boardLightSquareColor` | `'rgba(224, 224, 224, 1)'` | Color of the light squares. |
| `boardDarkSquareColor` | `'rgba(110, 161, 118, 1)'` | Color of the dark squares. |
| `boardBackgroundPath` | `'assets/chessboard-bg.jpeg'`| Path to an image for the board background. |
| `boardImageBackground` | `false` | Whether to use the background image. |
| `pieceFoldersPath` | `'assets/pieces'` | Path to the directory containing piece sets. |
| `pieceFolderName` | `'fantasy'` | Name of the piece set folder to use. |
| `pieceFormat` | `'lichess'` | Naming format for piece assets (`'lichess'` or `'standard'`). See below. |
| `customPieceUrls` | `{}` | Object to specify URLs for individual pieces. See below. |
| `soundFoldersPath` | `'assets/sounds'` | Path to the directory containing sound sets. |
| `soundFolderName` | `'default'` | Name of the sound set folder to use. |
| `customSoundUrls` | `{}` | Object to specify URLs for individual sounds. See `setCustomSoundUrl`. |

#### `customPieceUrls` Format

This option allows you to override the board's theme for specific pieces. It's an object where each key is a string identifying a piece, and the value is the URL to its image.

The key is formatted as `"{color}_{type}"`:
-   `color`: `'w'` for white, `'b'` for black.
-   `type`: `'p'` (Pawn), `'n'` (Knight), `'b'` (Bishop), `'r'` (Rook), `'q'` (Queen), `'k'` (King).

The URL can be a path to an image file or a Base64-encoded data URI. Note: these automatically override the file folder path system. If you start using this, and want to go back, you must set the `customPieceUrls` to something like `undefined`.

**Example:**
```javascript
const board = new Chessboard("#b1", {
    theme: {
        customPieceUrls: {
            'w_p': 'data:image/svg+xml;base64,PHN2Zy...',
            'w_n': 'data:image/svg+xml;base64,PHN2Zy...',
            // ... and so on for all pieces
        }
    }
});
```
You can do both at the same time, and setting these options after start will still cache the pieces successfully!
```js
const board = new Chessboard("#b1", {
    theme: {
        // Use default folder for most pieces
        pieceFoldersPath: 'assets/pieces',
        pieceFolderName: 'classic',
        
        // But use custom URLs for specific pieces
        customPieceUrls: {
            'w_k': 'https://mysite.com/special-white-king.svg',
            'b_k': 'https://mysite.com/special-black-king.svg'
        },
        
        // And custom sounds
        customSoundUrls: {
            'capture': 'https://mysite.com/epic-capture.mp3'
        }
    }
});
```

#### `pieceFormat`: Standard vs. Lichess

This setting determines how the board constructs file paths to find piece images when not using `customPieceUrls`.

-   **`'lichess'` (Default):** Assumes piece names combine color and an uppercase piece type. This matches the Lichess.org open-source assets.
    -   **Path Structure:** `{pieceFoldersPath}/{pieceFolderName}/{color}{type_uppercase}.svg`
    -   **Example:** For a white pawn (`'w_p'`), it would look for `assets/pieces/fantasy/wP.svg`.

-   **`'standard'`:** Assumes pieces are organized into subfolders by color.
    -   **Path Structure:** `{pieceFoldersPath}/{pieceFolderName}/{color}/{type}.svg`
    -   **Example:** For a white pawn (`'w_p'`), it would look for `assets/pieces/standard/w/p.svg`.

### Styling Settings

Located under `settings.styling`. These control the visual appearance of interactive elements.

| Key | Default | Description |
| --- | --- | --- |
| `fontFamily` | `'Arial, sans-serif'` | Font family for text elements on the board. |
| `selectedSquareColor` | `'rgba(255, 233, 38, 0.27)'` | Background color for the currently selected piece's square. |
| `justMovedColor` | `'rgba(255, 208, 0, 0.36)'` | Highlight color for the from/to squares of the last move. |
| `highlightColor` | `'rgba(255, 82, 82, 0.71)'` | Color for squares highlighted with a right-click. |
| `arrowColor` | `'rgba(223, 145, 0, 0.59)'` | Color of arrows drawn on the board. |
| `droppableIndicatorColor` | `'rgba(0, 0, 0, 0.15)'` | Color of the dot indicating a legal move square. |
| `droppableHoverBorderColor` | `'rgba(255, 255, 255, 0.781)'` | Border color when hovering over a legal move square. |
| `droppableHoverBorderWidth` | `'5px'` | Width of the border when hovering over a legal move square. |
| `captureIndicatorSize` | `'8px'` | Thickness of the ring indicating a capture move.  |
| `captureIndicatorColor` | `'rgba(0, 0, 0, 0.15)'` | Color of the ring indicating a capture move. |
| `draggedPieceScale` | `1.3` | How much to scale a piece while it's being dragged. |
| `hoverCursor` | `'grab'` | Cursor style when hovering over a draggable piece. |
| `grabCursor` | `'grabbing'` | Cursor style when dragging a piece. |
| `promotionPanelBackground` | `'rgba(241, 241, 241, 1)'` | Background color of the promotion selection panel. |
| `promotionPanelShadow` | `'0 4px 8px rgba(0, 0, 0, 0.2)'` | Box shadow for the promotion panel. |
| `promotionPieceHoverColor` | `'rgba(0, 0, 0, 0.1)'` | Background color when hovering over a piece in the promotion panel. |
| `promotionCancelBackground` | `'rgba(224, 224, 224, 1)'` | Background color for the cancel button in the promotion panel. |
| `promotionCancelHoverBackground`| `'rgba(208, 208, 208, 1)'` | Background color for the promotion cancel button on hover. |
| `promotionCancelHeight` | `'40px'` | Height of the promotion cancel button. |
| `classificationSize` | `'50%'` | Size of the move classification icon (e.g., brilliant move). |
| `classificationOffsetX` | `'150%'` | Horizontal offset of the classification icon. |
| `classificationOffsetY` | `'-50%'` | Vertical offset of the classification icon. |
| `classificationBorderOffsetX` | `'110%'` | Horizontal offset when the icon is near the board edge. |
| `classificationBorderOffsetY` | `'-10%'` | Vertical offset when the icon is near the board edge. |
| `notationFontSize` | `'1rem'` | Font size for algebraic notation on the squares. |
| `notationFontWeight` | `'600'` | Font weight for algebraic notation. |
| `notationOffset` | `'5px'` | Offset for algebraic notation from the edge of the square. |

### Behavior Settings

Top-level properties in the `settings` object.

| Key                           | Default   | Description                                                           |
| ----------------------------- | --------- | --------------------------------------------------------------------- |
| `audioEnabled`                | `true`    | Enable/disable sound effects.                                         |
| `isInteractive`               | `true`    | Enable/disable all user interactions (clicks, drags, etc.).           |
| `pieceAnimationDuration`      | `0.1`     | Duration of the piece move animation in seconds.                      |
| `pieceAnimationEasing`        | `ease-out`| CSS easing function for the piece move animation.                     |
| `pieceDragThreshold`          | `5`       | Minimum distance in pixels to drag before a drag action is initiated. |
| `classificationsHaveColoredBackgrounds`| `true` | Whether move classification squares have a colored background. |

### Callbacks

Located under `settings.callbacks`. These are an alternative to the `.on()` event system.

```javascript
const board = new Chessboard('#myBoard', {
    callbacks: {
        onMove: (move) => {
            console.log('A move was made:', move);
        },
        onCheck: (turn) => {
            console.log(`Player ${turn} is in check!`);
        }
    }
});
```

See the [Event System](#event-system) section for a full list of available events/callbacks.

## Public API Methods

All methods can be called on your `Chessboard` instance (e.g., `board.fen(...)`).

### Board State

#### `fen(fenString)`
Loads a position from a FEN string. If no FEN is provided, it loads the default starting position.

```javascript
// Load the starting position
board.fen();

// Load a custom position
board.fen('r1bqkbnr/p1pp1ppp/1pn5/4p3/2B1P3/5N2/PPPP1PPP/RNBQK2R w KQkq - 0 4');
```

#### `getState()`
Returns an object containing the current state of the board.

```javascript
const currentState = board.getState();
console.log(currentState);
/*
{
    fen: 'rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1',
    flipped: false,
    selectedPiece: undefined,
    arrows: [],
    highlights: [],
    history: [],
    turn: 'w'
}
*/
```

#### `setState(stateObject)`
Restores the board to a previously saved state.

```javascript
const savedState = {
    fen: '4k3/8/8/8/8/8/8/4K3 w - - 0 1',
    flipped: true,
    arrows: [[20, 36]], // e4 to e5 from black's perspective
    highlights: [27]    // d4
};

board.setState(savedState);
```

### Interaction & Appearance

#### `flip()`
Flips the board to the opposite perspective.

```javascript
board.flip(); // Flips to black's view
board.flip(); // Flips back to white's view
```

#### `refresh(clearHighlights = false)`
Redraws all pieces on the board according to the internal game state. Useful after the handler's state has changed externally.

```javascript
// Assuming `game` is a chess.js instance used as the handler
game.load('rnb1kbnr/pppp1ppp/8/4p3/5P2/8/PPPPP1PP/RNBQKBNR w KQkq - 0 2');

// The board UI is now out of sync. Refresh it:
board.refresh();
```

#### `createArrow(startSquareEl, endSquareEl)`
Toggles an arrow between two square elements. Right-dragging also creates arrows.

**Note:** This method requires the DOM elements of the squares, not algebraic notation. It's primarily used internally.

#### `clearBoardElements()`
Clears all arrows and right-click highlights from the board.

```javascript
// After drawing some arrows...
board.clearBoardElements();
```

#### `clearBoardHighlights()`
Clears all temporary highlights like "just moved" squares, selected squares, legal move indicators, and move classifications.

```javascript
board.clearBoardHighlights();
```

### Game Logic

These methods typically proxy to the provided chess handler.

#### `getPossibleMoves()`
Returns an array of all legal moves for the current position from the handler.

```javascript
const moves = board.getPossibleMoves();
console.log(moves); // [{ from: 'e2', to: 'e4', ... }, ...]
```

#### `getMoveHistory(options = {})`
Returns the game's move history from the handler.

```javascript
const history = board.getMoveHistory({ verbose: true });
console.log(history); // Array of move objects
```

#### `getSquareInfo(square)`
Returns detailed information about a square.

```javascript
const info = board.getSquareInfo('e2');
console.log(info);
/*
{
    square: 'e2',
    piece: { type: 'p', color: 'w' },
    legalMoves: [ { from: 'e2', to: 'e3', ... }, { from: 'e2', to: 'e4', ... } ],
    isAttacked: false, // (Note: requires advanced handler logic)
    isDefended: false  // (Note: requires advanced handler logic)
}
*/
```

#### `move(moveObject)`
Programmatically makes a move on the board.

```javascript
// Move white's e-pawn forward
board.move({ from: 'e2', to: 'e4' });

// Make a move with animation
board.move({ from: 'g1', to: 'f3' }, true);

// Make a promotion move
board.move({ from: 'e7', to: 'e8', promotion: 'q' });
```

#### `unmove()`
Programmatically undoes the last move on the board.

```javascript
// Make a move
board.move({ from: 'e2', to: 'e4' });

// Undo the move
board.unmove();
```

#### `importPGN(pgnString)` and `exportPGN()`
Imports or exports a game in PGN format. Requires the handler to support `.loadPgn()` and `.pgn()` methods (like `chess.js`).

```javascript
const pgn = '1. e4 e5 2. Nf3 Nc6';
board.importPGN(pgn);

const exportedPgn = board.exportPGN();
console.log(exportedPgn);
```

### Customization

#### `setCustomPieceUrl(color, type, url)`
Sets a custom image URL for a specific piece, overriding the theme.

```javascript
// Use a fancy custom SVG for the white king
board.setCustomPieceUrl('w', 'k', '/assets/my-pieces/white_king_alt.svg');
```

#### `removeCustomPieceUrl(color, type)`
Removes a custom piece URL, reverting to the theme's default.

```javascript
board.removeCustomPieceUrl('w', 'k');
```

#### `setCustomSoundUrl(sound, url)`
Sets a custom audio file URL for a specific game event.

```javascript
// Use a dramatic sound for captures
board.setCustomSoundUrl('capture', '/assets/my-sounds/boom.wav');
```

#### `removeCustomSoundUrl(sound)`
Removes a custom sound URL, reverting to the theme's default.

```javascript
board.removeCustomSoundUrl('capture');
```

### Lifecycle

#### `destroy()`
Completely removes the chessboard and all associated event listeners, timers, and objects from memory.

```javascript
// When you're done with the board (e.g., navigating to another page)
board.destroy();
board = null; // Allow garbage collection
```

## Event System

You can listen to events using the `on(eventName, callback)` method or by providing callbacks in the initial configuration.

#### `on(eventName, callback)`
```javascript
board.on('move', (move) => {
    console.log('Piece moved:', move);
});
```

#### `off(eventName, callback)`
Removes an event listener.

#### `once(eventName, callback)`
Adds a listener that runs only once.

### Available Events

| Event Name          | Callback Arguments          | Description                                                               |
| ------------------- | --------------------------- | ------------------------------------------------------------------------- |
| `move`              | `moveObject`                | Fired after a piece has been moved legally.                               |
| `movecancelled`     | `{ from, to }`              | Fired when an illegal move is attempted by the user.                      |
| `drop`              | `piece, from, to`           | Fired when a piece is dropped on a square.                                |
| `dragstart`         | `piece, square`             | Fired when a user starts dragging a piece.                                |
| `dragmove`          | `piece, square`             | Fired continuously as a piece is being dragged over squares.              |
| `piececlick`        | `piece, square`             | Fired when a user clicks on a piece.                                      |
| `positionchange`    | `fen`                       | Fired after any action that changes the board position (move, undo, fen). |
| `orientationchange` | `flipped (boolean)`         | Fired when the board is flipped.                                          |
| `promotionstart`    | `square`                    | Fired when the promotion panel is shown.                                  |
| `promotioncomplete` | `pieceType`                 | Fired after a promotion piece is selected.                                |
| `check`             | `turn`                      | Fired when a player is put in check.                                      |
| `arrowcreate`       | `fromSquare, toSquare`      | Fired when an arrow is created.                                           |
| `highlight`         | `square`                    | Fired when a square is highlighted (right-clicked).                       |
| `clear`             | -                           | Fired when board elements (arrows, highlights) are cleared.               |
| `destroy`           | -                           | Fired just before the board instance is destroyed.                        |

## Using a Chess Handler

`` is responsible for the UI, but it needs a "brain" to understand chess rules. This is provided by a handler object. You can use any object that implements the required methods. The most common choice is `chess.js`.

### Default Handler

If you don't provide a handler, `` uses a built-in `DefaultHandler`. **This handler has no real chess logic.** It allows any piece to move to any empty square. It's useful for setting up the UI or for non-chess variants, but for a real chess game, you must provide your own handler.

### Integrating `chess.js`

1.  **Install `chess.js`**:
    ```bash
    npm install chess.js
    ```

2.  **Pass an instance to the constructor**:
    ```javascript
    import { Chessboard } from './board.js';
    import { Chess } from 'chess.js';

    const chess = new Chess();
    const board = new Chessboard('#myBoard', {}, chess);
    ```

Now, `js-chessboard` will use `chess.js` to validate moves, check for game over conditions, and manage the game state. The two libraries work together seamlessly. When a user tries to move a piece, `js-chessboard` will first ask `chess.js` if the move is legal. 

Add a High-Tile Mode Button to 2048 Cupcakes
1. Add the button to index.html

Find the following code inside <div class="above-game">:

HTML
<a class="restart-button">New Game</a>


Replace it with:

HTML
<a class="restart-button">New Game</a>
<button id="toggle-tile-mode" type="button">High Tiles: OFF</button>

2. Update js/game_manager.js

Replace the existing addRandomTile function with the following code:

GameManager.prototype.addRandomTile = function () {
  if (this.grid.cellsAvailable()) {
    var value;

    if (this.highTileMode) {
      value = Math.random() < 0.5 ? 1024 :
              (Math.random() < 0.5 ? 2048 : 4096);
    } else {
      value = Math.random() < 0.5 ? 2 :
              (Math.random() < 0.5 ? 4 :
              (Math.random() < 0.5 ? 256 :
              (Math.random() < 0.5 ? 1024 :
              (Math.random() < 0.5 ? 2048 : 4096))));
    }

    var tile = new Tile(this.grid.randomAvailableCell(), value);
    this.grid.insertTile(tile);
  }
};

3. Add the button functionality to index.html

Insert this script immediately before the closing </body> tag:

HTML
<script>
  (function () {
    var button = document.getElementById("toggle-tile-mode");

    button.addEventListener("click", function () {
      var game = window.gameManager;

      if (!game) {
        alert("Could not find the game manager instance.");
        return;
      }

      game.highTileMode = !game.highTileMode;

      button.textContent = game.highTileMode
        ? "High Tiles: ON"
        : "High Tiles: OFF";
    });
  })();
</script>

4. Expose the game manager instance

The original game may not expose its GameManager instance globally. If the button reports that it cannot find the game manager, open js/application.js and locate where GameManager is instantiated.

For example, change:

new GameManager(4, KeyboardInputManager,
                HTMLActuator, LocalStorageManager);


to:

window.gameManager = new GameManager(
  4,
  KeyboardInputManager,
  HTMLActuator,
  LocalStorageManager
);


Preserve any additional arguments used by your existing constructor.

Expected behavior
High Tiles: OFF — generates tiles using the original selection logic: 2, 4, 256, 1024, 2048, or 4096.
High Tiles: ON — generates only 1024, 2048, or 4096.
Clicking the button again switches back to normal mode.

Note: The toggle only affects newly generated tiles. Existing tiles on the board will not change.

<script lang="ts">
  // PUBLIC_INTERFACE
  /**
   * A classic TicTacToe game: two players, local mode, win/draw detection, and board reset.
   */

  // Color Theme
  const COLOR_PRIMARY = '#2196F3'; // X color and accents
  const COLOR_ACCENT = '#FF9800'; // O color, result accent

  type Player = 'X' | 'O';
  type Cell = Player | null;
  type Result = { winner: Player } | { draw: true } | null;

  // Board state: 3x3, row-major
  let board: Cell[] = Array(9).fill(null);
  let currentPlayer: Player = 'X'; // X always starts
  
  let result: Result = null; // winner or draw
  let moves = 0; // number of moves played

  // Helper lines for win detection
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8], // rows
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8], // cols
    [0, 4, 8],
    [2, 4, 6]  // diags
  ];

  // PUBLIC_INTERFACE
  function handleCellClick(idx: number) {
    if (board[idx] || result) return; // occupied or game over

    board[idx] = currentPlayer;
    moves += 1;
    checkGameState();

    if (!result) {
      currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
    }
  }

  // PUBLIC_INTERFACE
  function checkGameState() {
    // Win detection
    for (const [a, b, c] of lines) {
      if (board[a] && board[a] === board[b] && board[a] === board[c]) {
        result = { winner: board[a] as Player };
        return;
      }
    }
    // Draw if all cells filled and no winner
    if (moves === 9) {
      result = { draw: true };
    }
  }

  // PUBLIC_INTERFACE
  function restartGame() {
    board = Array(9).fill(null);
    currentPlayer = 'X';
    result = null;
    moves = 0;
  }
</script>

<style>
  .container {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background: #FFFFFF;
    font-family: 'Inter', 'Segoe UI', Arial, sans-serif;
  }
  .status {
    margin-bottom: 2rem;
    font-size: 1.5rem;
    color: #2196F3;
    font-weight: 600;
    text-align: center;
    letter-spacing: 1px;
  }
  .grid {
    display: grid;
    grid-template-columns: repeat(3, clamp(64px, 10vw, 100px));
    grid-template-rows: repeat(3, clamp(64px, 10vw, 100px));
    gap: 10px;
    background: #2196F311;
    padding: 20px 24px;
    border-radius: 1.25rem;
    box-shadow: 0 2px 8px #0001;
    margin-bottom: 2rem;
  }
  .cell {
    background: #FFFFFF;
    border: 2.5px solid #2196F3;
    border-radius: 15px;
    width: 100%;
    height: 100%;
    font-size: 2.5rem;
    color: #FF9800;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    font-weight: 700;
    position: relative;
    transition: background 0.14s, color 0.14s;
    user-select: none;
  }
  .cell.played-x {
    color: #2196F3;
  }
  .cell.played-o {
    color: #FF9800;
  }
  .cell:disabled, .cell.disabled {
    opacity: 0.54;
    pointer-events: none;
    cursor: not-allowed;
  }
  .controls {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 16px;
    min-height: 40px;
  }
  .restart-btn {
    background: #2196F3;
    color: #FFFFFF;
    border: none;
    padding: 0.7em 2.2em;
    border-radius: 20px;
    font-weight: 600;
    font-size: 1.15rem;
    cursor: pointer;
    transition: background 0.16s;
    margin-bottom: 0.25rem;
    box-shadow: 0 1px 4px #2196F333;
  }
  .restart-btn:hover {
    background: #FF9800;
    color: #FFFFFF;
  }
  .result-msg {
    font-size: 1.18rem;
    font-weight: 500;
    color: #FF9800;
    background: #fff7ea;
    border-radius: 12px;
    padding: 0.5em 1.2em;
    margin-top: 0.3em;
    box-shadow: 0 2px 8px #ff980018;
    letter-spacing: 1px;
    min-width: 120px;
    text-align: center;
    border: 1.5px solid #FF980066;
    display: inline-block;
  }
  @media (max-width: 600px) {
    .grid {
      grid-template-columns: repeat(3, 22vw);
      grid-template-rows: repeat(3, 22vw);
      padding: 6vw 1vw 6vw 1vw;
    }
    .status {
      font-size: 1.12rem;
      margin-bottom: 1rem;
    }
    .restart-btn {
      font-size: 1rem;
      padding: 0.72em 1.2em;
    }
    .result-msg {
      font-size: 1rem;
    }
  }
</style>

<div class="container">
  <div class="status">
    {#if result}
      {#if 'winner' in result}
        <span style="color:{COLOR_ACCENT};">Winner: </span>
        <span style="color:{result.winner === 'X' ? COLOR_PRIMARY : COLOR_ACCENT};">
          {result.winner}
        </span>
      {:else}
        <span style="color:{COLOR_ACCENT};">Draw!</span>
      {/if}
    {:else}
      Current Turn:
      <span style="color:{currentPlayer === 'X' ? COLOR_PRIMARY : COLOR_ACCENT}; margin-left: 7px;">
        {currentPlayer}
      </span>
    {/if}
  </div>
  <div class="grid" role="grid" aria-label="TicTacToe Board">
    {#each board as cell, idx (idx)}
      <button
        type="button"
        class="cell {cell === 'X' ? 'played-x' : ''} {cell === 'O' ? 'played-o' : ''} {result ? 'disabled' : ''}"
        aria-label="TicTacToe Cell"
        on:click={() => handleCellClick(idx)}
        disabled={!!result || !!cell}
        tabindex={cell || result ? -1 : 0}
      >
        {cell}
      </button>
    {/each}
  </div>
  <div class="controls">
    <button class="restart-btn" on:click={restartGame}>Restart Game</button>
    {#if result}
      <span class="result-msg">
        {#if 'winner' in result}
          Player <span style="font-weight:600; color:{result.winner === 'X' ? COLOR_PRIMARY : COLOR_ACCENT};">{result.winner}</span> wins!
        {:else}
          Draw! No winner.
        {/if}
      </span>
    {/if}
  </div>
</div>

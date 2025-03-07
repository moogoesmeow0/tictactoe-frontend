<script lang="ts">
    let board: string[][] = [
        ['', '', ''],
        ['', '', ''],
        ['', '', '']
    ];
    let currentPlayer: string = 'X';
    let winner: string | null = null;
    let gameOver: boolean = false;

    function whoWon() {
        // Check rows
        for (let i = 0; i < 3; i++) {
            if (board[i][0] !== '' && board[i][0] === board[i][1] && board[i][0] === board[i][2]) {
                winner = board[i][0];
                return true;
            }
        }

        // Check columns
        for (let i = 0; i < 3; i++) {
            if (board[0][i] !== '' && board[0][i] === board[1][i] && board[0][i] === board[2][i]) {
                winner = board[0][i];
                return true;
            }
        }

        // Check diagonals
        if (board[0][0] !== '' && board[0][0] === board[1][1] && board[0][0] === board[2][2]) {
            winner = board[0][0];
            return true;
        }
        
        if (board[0][2] !== '' && board[0][2] === board[1][1] && board[0][2] === board[2][0]) {
            winner = board[0][2];
            return true;
        }

        // Check for tie
        let tie = true;
        for (let i = 0; i < 3; i++) {
            for (let j = 0; j < 3; j++) {
                if (board[i][j] === '') {
                    tie = false;
                    break;
                }
            }
            if (!tie) break;
        }
        
        if (tie) {
            winner = "Tie";
            return true;
        }
        
        return false;
    }
    
    function handleClick(row: number, col: number) {
        // Don't allow move if the cell is already filled or game is over
        if (board[row][col] !== '' || gameOver) return;
        
        // Place the current player's mark
        board[row][col] = currentPlayer;
        board = [...board]; // Trigger reactivity in Svelte
        
        // Check if game is won
        if (whoWon()) {
            gameOver = true;
            return;
        }
        
        // Switch player
        currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
    }
    
    function resetGame() {
        board = [
            ['', '', ''],
            ['', '', ''],
            ['', '', '']
        ];
        currentPlayer = 'X';
        winner = null;
        gameOver = false;
    }

	async function upload() {
		let resp = await fetch('https://api.taranathan.com/boards', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({ board: board })
		});
	}
</script>

<div class="grid">
	{#each board as row, rowIndex}
		{#each row as cell, colIndex}
			<div class="cell" role="button" tabindex="0" on:click={() => handleClick(rowIndex, colIndex)}
					 on:keydown={(e) => e.key === 'Enter' && handleClick(rowIndex, colIndex)}>
				{cell}
			</div>
		{/each}
	{/each}
</div>

{#if winner}
	<div class="winner">Winner: {winner}</div>
{/if}

<button class="button" on:click={() => resetGame()}>Reset Game</button>
<button class="button" on:click={() => upload()}>Upload</button>

<style>
    .grid {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 0.25rem;
        margin: 1.25rem auto;
    }

    .cell {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 6.25rem;
        height: 6.25rem;
        border: 1px solid black;
        font-size: 1.5rem;
        cursor: pointer;
    }

    .winner {
        text-align: center;
        margin-top: 1.25rem;
        font-size: 1.25rem;
    }

    .button {
        margin-left: auto;
        margin-right: auto;
        margin-top: 1.25rem;
        display: block;
        border-radius: 0.25rem;
        background-color: linen;
        margin: 0.5rem auto;
        font-family: 'Offside', sans-serif;
        cursor: pointer;
    }
</style>
<script lang="ts">
	import { onMount } from 'svelte';

	interface Board {
		id: string;
		board: string[][];
		timestamp?: string;
	}

	let boards: Board[] = $state([]);
	let isLoading = $state(true);
	let error: string | null = $state(null);
	let boardIds: string[] = [];
	let totalBoards = $state(0);
	let allBoards: Board[] = [];
	let page = $state(1);
	const boardsPerPage = 5;

	async function fetchBoards() {
		try {
			isLoading = true;
			error = null;

			const response = await fetch('https://api.taranathan.com/boards');
			const data = await response.json();

			allBoards = data.rows.map((item: any) => ({
				id: item.id.toString(),
				board: item.board,
				timestamp: item.date
			}));

			allBoards.sort((a, b) => {
				const dateA = a.timestamp ? new Date(a.timestamp).getTime() : 0;
				const dateB = b.timestamp ? new Date(b.timestamp).getTime() : 0;
				return dateB - dateA;
			});

			totalBoards = allBoards.length;
			console.log('totalBoards:', totalBoards);

			updateCurrentPageBoards();

			error = null;
		} catch (err) {
			console.error('Error fetching boards:', err);
			error = 'Failed to fetch boards. Please try again later.';
		} finally {
			isLoading = false;
		}
	}

	function updateCurrentPageBoards() {
		boards = allBoards.slice((page - 1) * boardsPerPage, page * boardsPerPage);
	}

	function nextPage() {
		if (page * boardsPerPage < totalBoards) {
			page += 1;
			updateCurrentPageBoards();
		}
	}

	function prevPage() {
		if (page > 1) {
			page -= 1;
			updateCurrentPageBoards();
		}
	}

	onMount(fetchBoards);
</script>

<div class="boards-container">
	<h2>Recent Games</h2>

	{#if isLoading}
		<div class="loading">Loading recent games...</div>
	{:else if error}
		<div class="error">Error: {error}</div>
		<button onclick={fetchBoards}>Try Again</button>
	{:else if boards.length === 0}
		<div class="no-boards">No games found</div>
		<button class="refresh-button" onclick={fetchBoards}>Refresh</button>
	{:else}
		<button class="refresh-button" onclick={fetchBoards}>Refresh</button>
		<div class="boards-list">
			{#each boards as board, index}
				<div class="board-item">
					<h3>Game {(page - 1) * boardsPerPage + index + 1}</h3>
					{#if board.timestamp}
						<div class="timestamp">{new Date(board.timestamp).toLocaleString()}</div>
					{/if}
					<div class="mini-grid">
						{#each board.board as row}
							{#each row as cell}
								<div class="mini-cell">{cell || ' '}</div>
							{/each}
						{/each}
					</div>
				</div>
			{/each}
		</div>

		<div class="pagination-controls">
			<button class="pagination-button" onclick={prevPage} disabled={page === 1}>Previous</button>
			<span class="page-info">Page {page} of {Math.ceil(totalBoards / boardsPerPage)}</span>
			<button
				class="pagination-button"
				onclick={nextPage}
				disabled={page * boardsPerPage >= totalBoards}>Next</button
			>
		</div>
	{/if}
</div>

<style>
	.boards-container {
		margin: 2rem auto;
		max-width: 600px;
		padding: 1rem;
	}

	h2 {
		text-align: center;
		margin-bottom: 1.5rem;
		font-family: 'Offside', sans-serif;
	}

	.boards-list {
		display: flex;
		flex-direction: column;
		gap: 2rem;
	}

	.board-item {
		border: 1px solid #ddd;
		border-radius: 8px;
		padding: 1rem;
		background-color: #f9f9f9;
	}

	h3 {
		margin: 0 0 0.5rem 0;
		font-family: 'Offside', sans-serif;
	}

	.timestamp {
		font-size: 0.8rem;
		color: #666;
		margin-bottom: 0.5rem;
	}

	.mini-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 2px;
		margin: 0 auto;
		width: 120px;
		height: 120px;
	}

	.mini-cell {
		display: flex;
		align-items: center;
		justify-content: center;
		background-color: white;
		border: 1px solid #ccc;
		height: 100%;
		font-size: 1.2rem;
		font-weight: bold;
	}

	.loading,
	.error,
	.no-boards {
		text-align: center;
		padding: 2rem;
		border: 1px solid #ddd;
		border-radius: 8px;
		background-color: #f9f9f9;
	}

	.error {
		color: #d32f2f;
		border-color: #ffcdd2;
		background-color: #ffebee;
	}

	button {
		padding: 0.5rem 1rem;
		margin: 1rem auto;
		display: block;
		background-color: linen;
		border-radius: 4px;
		cursor: pointer;
		font-family: 'Offside', sans-serif;
	}

	button:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}

	.refresh-button {
		margin-top: 1.5rem;
	}

	.pagination-controls {
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 1.5rem;
		gap: 1rem;
	}

	.pagination-button {
		margin: 0;
	}

	.page-info {
		font-family: 'Offside', sans-serif;
	}
</style>

<script lang="ts">
	import Boards from './lib/Boards.svelte';
	import Button from './lib/button.svelte';
	import Tictactoe from './lib/tictactoe.svelte';
</script>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Offside&display=swap');

    .container {
        display: flex;
        justify-content: center;
        align-items: center;
        margin-top: 2.5rem;
    }

    .text {
        font-family: 'Offside', sans-serif;
        font-size: 1.875rem;
        font-weight: 600;
    }

    .button-container {
        display: flex;
        justify-content: stretch;
        justify-items: stretch;
    }

    .button-position {
        position: absolute;
        right: 3.5rem;
        width: 8rem;
        height: 4rem;
				bottom: 1rem;
				margin-bottom: 8rem;
    }
</style>

<div class="container">
	<p class="text">Have Fun!</p>
</div>

<Tictactoe />

<div class="button-container">
	<div class="button-position">
		<Button />
	</div>
</div>

<Boards />
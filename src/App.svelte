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
</style>

<div class="container">
	<p class="text">Have Fun!</p>
</div>

<Tictactoe />

<Boards />
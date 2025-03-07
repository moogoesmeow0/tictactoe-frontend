<script lang="ts">
	import boing_mp3 from '../assets/boing.mp3';

	const audio = new Audio(boing_mp3);

	let count = $state(0);

	//const click_sound = useSound(boing_mp3,["click"]);

	function increment() {
		count += Math.floor(Math.random() * 8);
		audio.load();
		audio.play();
	}
</script>

<button onclick={increment} class="button">
	clicky here for {count} num
</button>

<style>
	.button {
		border-radius: 0.25rem;
		background-color: linen;
		margin: 0.5rem auto;
		font-family: 'Offside', sans-serif;
	}
</style>

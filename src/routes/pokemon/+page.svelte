<script>
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';

	import Pokemon from './pokemon.svelte';
	let isStart = false;
	/**
	 * @type {number | string}
	 *  */
	let poinTertinggi = 0;
	let playConfig = {
		heart: 3,
		mode: 'normal'
	};
	const handleSubmit = (/** @type {any} */ e) => {
		playConfig.heart = e.target.heart.value > 0 ? Number(e.target.heart.value) : 3;
		playConfig.mode = e.target.mode.value;
		isStart = true;
	};
	const back = () => {
		isStart = false;
	};
	onMount(() => {
		poinTertinggi = localStorage.getItem('poinTertinggi') || 0;
	});
</script>

<svelte:head>
	<title>Pokemon</title>
	<meta name="description" content="tebak pokemon" />
</svelte:head>
<section>
	{#if isStart}
		<Pokemon config={playConfig} on:back={back} />
	{:else}
		<div class="form" transition:fade>
			<div style="text-align: center;">
				<h3 style="font-weight: bold;">Buat Game baru</h3>
				<h4 style="margin-top: -10px;">poin Tertinggi Kamu {poinTertinggi}</h4>
			</div>
			<form on:submit|preventDefault={handleSubmit} style="display:flex;flex-direction: column;">
				<div>
					<label for="heart">Nyawa</label>
					<input type="number" id="heart" name="heart" value="3" min="1" max="10" />
				</div>
				<div>
					<label for="mode">mode</label>
					<select name="mode" id="mode">
						<option value="normal">normal (Tanpa Waktu)</option>
						<option value="tanpaWarna">tanpa warna</option>
					</select>
				</div>
				<button type="submit">Buat</button>
			</form>
		</div>
	{/if}
	<div />
</section>

<style>
	.form {
		display: flex;
		padding: 1rem;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 80vh;
		border-radius: 1rem;
		background-color: white;
	}
	form > div {
		margin-bottom: 1rem;
	}
	button {
		outline: none;
		border: none;
		border-radius: 1rem;
		color: white;
		padding: 0.5rem 2rem;
		background-color: var(--color-theme-2);
	}
</style>

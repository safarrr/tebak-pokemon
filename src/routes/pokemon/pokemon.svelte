<script>
	// @ts-nocheck
	import { blur, scale } from 'svelte/transition';
	import { createEventDispatcher } from 'svelte';
	import { onMount } from 'svelte';
	export let config = {
		heart: 3,
		mode: 'normal'
	};
	const dispatch = createEventDispatcher();

	let input = '';
	let heart = config.heart;
	let poin = 0;
	let tanpaWarna = config.mode == 'tanpaWarna' ? true : false;
	let pokemon = null;
	let poinTertinggi = 0;
	let selecteGambar = 'official-artwork';
	const getData = async () => {
		const randomInt = Math.floor(Math.random() * (1000 - 1) + 1);
		const res = await fetch('https://pokeapi.co/api/v2/pokemon/' + randomInt);
		const data = await res.json(); 
		pokemon = data;
		return data;
	};
	const handleKirirm = () => {
		if (input.toLocaleLowerCase() === pokemon.name.toLocaleLowerCase()) {
			if (config.mode === 'tanpaWarna') {
				tanpaWarna = false;
			}
			poin += 1;
			setTimeout(() => {
				getData();
				if (config.mode === 'tanpaWarna') {
					tanpaWarna = true;
				}

				input = '';
			}, 2000);
		} else {
			heart -= 1;
			input = '';
		}
	};
	onMount(() => {
		getData();
		poinTertinggi = localStorage.getItem('poinTertinggi') || 0;
	});
	$: if (poin > poinTertinggi) {
		localStorage.setItem('poinTertinggi', poin);
	}
</script>

<section>
	{#if pokemon}
		{#if heart <= 0}
			<div transition:scale={{ duration: 100 }} class="modal">
				<h3>Lost</h3>
				<button on:click={() => dispatch('back')}>Mulai ulang</button>
			</div>
		{/if}
		<div class="pokemon-card">
			<div class="pokemon-card-header">
				<div style="display: flex; flex-direction: column; align-items: start;">
					<h3>Apa nama pokemon ini?</h3>
					<h4>Pilih Gambar</h4>
					<select name="gambar" id="gambar" bind:value={selecteGambar}>
						<option value="official-artwork">Official Artwork</option>
						<option value="dream_world">Dream World</option>
						<option value="home">3D</option>
					</select>
				</div>
				<div>
					<div style="display: inline-flex; align-items: center; color:red">
						{#each Array(heart) as _}
							<svg
								transition:blur
								xmlns="http://www.w3.org/2000/svg"
								width="20"
								height="20"
								fill="currentColor"
								style="margin: 5px;"
								viewBox="0 0 16 16"
							>
								<path
									fill-rule="evenodd"
									d="M8 1.314C12.438-3.248 23.534 4.735 8 15-7.534 4.736 3.562-3.248 8 1.314z"
								/>
							</svg>
						{/each}
					</div>
				</div>
			</div>
			<h4 transition:blur>{poin}</h4>
			<img
				src={pokemon.sprites.other[selecteGambar].front_default}
				alt=""
				srcset=""
				style={tanpaWarna ? 'filter: brightness(0%);' : ''}
			/>
			<div style="display: inline-flex;align-items: center;">
				<input placeholder="masukan jawaban" bind:value={input} />
				<button on:click={handleKirirm} disabled={input == ''}>Kirim</button>
			</div>
		</div>
	{:else}
		<h1>loading</h1>
	{/if}
</section>

<style>
	select {
		outline: none;
		background-color: var(--color-bg-2);
		border: none;
		margin-top: -10px;
		padding: 0.5rem;
	}
	button {
		outline: none;
		border: none;
		border-radius: 0 1rem 1rem 0;
		color: white;
		padding: 1rem 2rem;
		background-color: var(--color-theme-2);
	}
	button:disabled {
		background-color: var(--color-theme-1);
	}
	.pokemon-card {
		display: flex;
		padding: 1rem;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 1rem;
		background-color: white;
	}
	.pokemon-card > img {
		width: 30%;
		margin: 0 auto;
	}
	.pokemon-card-header {
		width: 100%;
		display: inline-flex;
		justify-content: space-between;
		align-items: center;
	}
	.pokemon-card-header > div {
		display: flex;
		flex-direction: column;
		align-items: end;
	}
	input {
		outline: none;
		padding: 1rem;
		border: none;
		background-color: var(--color-bg-2);
		width: max-content;
		border-radius: 1rem 0 0 1rem;
	}
	.modal {
		position: absolute;
		z-index: 50;
		width: 50vw;
		padding: 1.4rem;
		border-radius: 1rem;
		text-align: center;
		color: white;
		top: 50%;
		left: 50%;
		background-color: #ef233c;
		transform: translate(-50%, -50%);
	}
	.modal button {
		border-radius: 1rem;
		padding: 0.9rem 1rem;
	}
</style>

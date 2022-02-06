<script lang="ts">
	import Icon from 'svelte-icons-pack/Icon.svelte';
	import FaHandRock from 'svelte-icons-pack/fa/FaHandRock';
	import FaHandPaper from 'svelte-icons-pack/fa/FaHandPaper';
	import FaHandScissors from 'svelte-icons-pack/fa/FaHandScissors';
	type Choice = 'rock' | 'paper' | 'scissors'
	type Winner = 'Player' | 'Bot'

	const getPointsFromLocalStorage = () => {
		if(localStorage.getItem('points')) {
			return parseInt(localStorage.getItem('points'));
		} 
		return 0;
	}
	
	let points = getPointsFromLocalStorage();
	let isGameOver = false;
	let result: string;

	let choices: Choice[] = ['rock', 'paper', 'scissors'];
	
	let playerChoice: Choice | '' = ''
	let botChoice: Choice | '' = ''
	let winner: Winner | '' = ''

	const onPlayerChoose: svelte.JSX.MouseEventHandler<HTMLButtonElement> = (e) => {
		if(!isGameOver) {
			const { choice } = e.currentTarget.dataset
			playerChoice = choice as Choice;
			onBotChoose()
		}
	}

	const onBotChoose = () => {
		botChoice = choices[Math.floor(Math.random() * choices.length)];
	}

	const compareChoices = () => {
		if(playerChoice === botChoice) {
			result = 'Draw';
			return;
		}

		switch (playerChoice) {
			case "paper":
				switch (botChoice) {
					case "rock":
						winner = 'Player';	
						break;
				
					case "scissors":
						winner = 'Bot';
						break;
				}		
				break;

			case "rock":
				switch (botChoice) {
					case 'paper':
						winner = 'Bot';	
						break;
					
					case 'scissors':
						winner = 'Player'
						break;
				}
				break;
			
			case 'scissors':
				switch (botChoice) {
					case "paper":
						winner = 'Player'	
						break;
				
					case "rock":
						winner = 'Bot'
						break;
				}
		}	
		result = `${winner} Won.`
	}

	const calculateScore = () => {
		switch(winner) {
			case 'Bot':
				points--;
				break;
			case 'Player':
				points++;
				break;
			default:
				break;
		}
	}

	$: if(playerChoice && botChoice) {
		compareChoices();
		isGameOver = true;
	}

	$: if(winner) {
		calculateScore();
	}

	$: localStorage.setItem('points', points.toString());

	const restart: svelte.JSX.KeyboardEventHandler<Window> = (e) => {
		if(e.key === 'Enter') {
			isGameOver = false;
			winner = '';
			botChoice = '';
			playerChoice = '';
		}
	}
</script>


<svelte:window on:keydown="{restart}"></svelte:window>

<main>
	<div class="container">
		{#if !isGameOver}
		<h1 class="player-turn">It's your turn</h1>
		{/if}
		<section class="players">
			<div class="player">
				<h2>Player: {points} points</h2>
				{#if playerChoice} 
				<p><Icon size="4rem" src={playerChoice === 'rock' ? FaHandRock : playerChoice === 'scissors' ? FaHandScissors :  FaHandPaper} /></p>
				{/if}
				{#if !isGameOver}
				<div class="btns">
					{#each choices as choice, i(i)}
						<button on:click="{onPlayerChoose}" data-choice={choice}>
							<Icon src={choice === 'rock' ? FaHandRock : choice === 'scissors' ? FaHandScissors : FaHandPaper } />
						</button>	
					{/each}
				</div>
				{/if}
			</div>
			<div class="player">
				<h2>Bot</h2>
				{#if botChoice}
				<p><Icon size="4rem" src={botChoice === 'rock' ? FaHandRock : botChoice === 'scissors' ? FaHandScissors : FaHandPaper} /></p>
				{/if}
			</div>
		</section>
		{#if isGameOver}
		<section class="game-over">
			<h1 class="result">{result}</h1>
			<h1 class="instruction">Press <code>Enter</code> to play again</h1>
		</section>
		{/if}
	</div>
</main>

<style>
	code {
		font-weight: normal;
		background: #eee;
		padding: 0.25rem;
	}
	main {
		padding: 2rem;
	}
	.container {
		max-width: 800px;
		margin: 0 auto;
	}
	.player-turn, .result, .instruction {
		text-align: center;
		margin: 1rem 0;
	}
	.players {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.player {
		text-align: center;
	}
</style>
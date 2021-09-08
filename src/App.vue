<template>
  <div id="app">
		<h1 class="titulo">Jogo da Memória</h1>		
		<div class="menu" v-if="!started">
			<div class="level" v-if="level">
				<button class="botao" @click="selectLevel(0)">Fácil</button>
				<button class="botao" @click="selectLevel(1)">Médio</button>
				<button class="botao" @click="selectLevel(2)">Difícil</button>
				<button class="botao" @click="selectLevel(3)">Expert</button>
			</div>
			<div class="modeGame" v-if="modeGame">
				<button class="botao" @click="startGame(0)">Jogador VS Jogador</button>
				<button class="botao" @click="startGame(1)">Jogador VS CPU</button>
				<button class="botao" @click="reset">Voltar</button>
			</div>
		</div>
		<div class="board" v-if="started && easy">
			<Easy :typePlayer="typePlayer" @mainMenu="reset" />
		</div>
		<div class="board" v-if="started && medium">
			<Medium :typePlayer="typePlayer" @mainMenu="reset" />
		</div>
		<div class="board" v-if="started && hard">
			<Hard :typePlayer="typePlayer" @mainMenu="reset" />
		</div>
		<div class="board" v-if="started && expert">
			<Expert :typePlayer="typePlayer" @mainMenu="reset" />
		</div>
  </div>
</template>

<script>
import Easy from './components/Easy.vue'
import Medium from './components/Medium.vue'
import Hard from './components/Hard.vue'
import Expert from './components/Expert.vue'
export default {
  name: 'App',
	components: {Easy, Medium, Hard, Expert},
	data() {
		return {
			started: false,
			level: true,
			modeGame: false,
			typePlayer: 0,
			easy: false,
			medium: false,
			hard: false,
			expert: false
		}
	},
	methods: {
		startGame(value) {
			this.typePlayer = value
			this.started = true
			if (this.easy) this.$emit('startGameEasy')
			if (this.medium) this.$emit('startGameMedium')
			if (this.hard) this.$emit('startGameHard')
			if (this.expert) this.$emit('startGameExpert')
		},
		selectLevel(type) {
			switch(type) {
				case 0 : 
					this.easy = true
					break
				case 1 : 
					this.medium = true
					break
				case 2 : 
					this.hard = true
					break
				case 3 : 
					this.expert = true
					break
			}
			this.level = false
			this.modeGame = true
		},
		reset() {
			this.started = false,
			this.level = true,
			this.modeGame = false,
			this.typePlayer = 0,
			this.easy = false,
			this.medium = false,
			this.hard = false
			this.expert = false
		}
	}
}
</script>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Bangers&display=swap');
	@import url('https://fonts.googleapis.com/css2?family=Carter+One&display=swap');
	* {
		margin: 0;
		padding: 0;		
	}

	body {
		background: #232526;  /* fallback for old browsers */
		background: -webkit-linear-gradient(to right, #414345, #232526);  /* Chrome 10-25, Safari 5.1-6 */
		background: linear-gradient(to right, #414345, #232526); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
	}

	.titulo {
		font-family: 'Bangers', cursive;
		text-align: center;
		font-size: 64px;
		font-weight: 100;
		margin-top: 25px;
		color: #70e000;
	}

	.menu,  .level,  .modeGame {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.botao {
		width: 250px;
		height: 40px;
		font-family: 'Carter One', cursive;
		font-size: 20px;
		border-radius: 5px;
		background-color: #3498db; 
		color: aliceblue;
		border: none;
		margin-top: 40px;		
		cursor: pointer;
	}	

</style>

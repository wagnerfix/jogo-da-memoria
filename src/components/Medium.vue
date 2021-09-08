<template>
	<div>
		<div class="board">
			<div 
				class="parts" 
				v-for="item in itens" 
				:key="item.id" 
				:style="[item.find ? {'background-image': 'url(' + require(`@/assets/${item.img}.jpg`) + ')'} : {'background-image': 'url(' + require(`@/assets/circle.jpg`) + ')'}]"
				@click="selected(item.id,item.img,item.find)"
			>
			</div>
			<div class="finished" :style="[finished ? {'display': 'flex'} : {'display': 'none'}]">
				<div class="painel">{{winner}}</div>
			</div>
			<div class="cpuPlaying" :style="[cpuPlaying ? {'display': 'flex'} : {'display': 'none'}]">
				<div class="painel">CPU Jogando...</div>
			</div>
		</div>
		<div class="score">
			<div class="player1" :style="[player === 0 && !cpuPlaying ? {'color': 'chartreuse'} : {'color': '#fff'}]">[{{game1}}] Jogador 1 = {{point1}}</div>
			<div class="player1">VS</div>
			<div class="player2" :style="[player !== 0 && !cpuPlaying ? {'color': 'chartreuse'} : {'color': '#fff'}]" v-if="typePlayer === 0">[{{game2}}] Jogador 2 = {{point2}}</div>			
			<div class="player2" :style="[cpuPlaying ? {'color': 'chartreuse'} : {'color': '#fff'}]" v-else>[{{game2}}] CPU = {{point2}}</div>			
		</div>
		<div class="rodape">
				<button class="botaoMenu" @click="restart()">Reiniciar o Jogo</button>
				<button class="botaoMenu" @click="mainMenu()">Menu Principal</button>
		</div>
	</div>
</template>

<script>
export default {
	name: 'Medium',
	props: {
		typePlayer: {
			type: Number,
			required: true
		}
	},
	created() {
		this.$on('startGameMedium', this.loadGame())
	},
	data() {
		return {
			itens: [],
			itemSelected: [],
			point1: 0,
			point2: 0,
			game1: 0,
			game2: 0,
			player: 0,
			finished: false,
			winner: '',
			cpuPlaying: false
		}
	},
	methods: {
		loadGame: function () {
			this.itens = []
			let id = 0			
			let list1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].sort(() => Math.random() - 0.5)
			let list2 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].sort(() => Math.random() - 0.5)
			for(let i = 0; i < list1.length; i++) {
				this.itens.push({					
					id: id++,
					img: list1[i],
					find: false
				})
			}
			for(let i = 0; i < list2.length; i++) {
				this.itens.push({
					id: id++,
					img: list2[i],
					find: false
				})
			}
		},
		selected: async function(id, img, find) {
			if (find === false) {
				if (this.itemSelected.length === 0) {
					this.itemSelected.push({
						id: id,
						img: img
					})
					this.itens[id].find = true
				} else {
					this.itemSelected.push({
						id: id,
						img: img
					})
					this.itens[id].find = true
					if (this.itemSelected[0].img === this.itemSelected[1].img) {
						this.itemSelected = []
						if (this.player === 0) ++this.point1
						if (this.player === 1) ++this.point2
						await this.check()
					} else {
						setTimeout(() => {
							this.itens[this.itemSelected[0].id].find = false
							this.itens[this.itemSelected[1].id].find = false
							this.itemSelected = []
							if (this.typePlayer === 1) {
								this.cpuPlaying = true
								this.cpuPlayer();
							} else {
								this.player = this.player ? 0 : 1					
							}
						}, 550)
					}
				}
			}
		},
		cpuPlayer() {
			if (!this.finished) {
				setTimeout(() => {
					const activeParts1 = this.itens.filter(encontrado => encontrado.find == false);
					const findOnly1 = Array.from(activeParts1.reduce((map, obj) => map.set(obj.img, obj), new Map()).values())
					let partSelected1 = findOnly1.sort(() => Math.random() - 0.5)
					this.itemSelected.push({
						id: partSelected1[0].id,
						img: partSelected1[0].img
					})
					this.itens[partSelected1[0].id].find = true
					if (partSelected1.length > 2) {
						partSelected1 = partSelected1.slice(0,partSelected1.length/2)
					}
					let partSelected2 = partSelected1.sort(() => Math.random() - 0.5)
					if (this.itemSelected[0].img === partSelected2[0].img) {
						for(let i = 0; i < this.itens.length; i++) {
							if (partSelected1[0].img === this.itens[i].img) {
								this.itens[i].find = true
							}
						}
						this.itemSelected = []
						++this.point2
						this.check()
						this.cpuPlayer()
					} else {
						this.itens[this.itemSelected[0].id].find = false
						this.itemSelected = []
						this.player = 0
						this.cpuPlaying = false
					}
				}, 500)				
			}
		},
		check() {
			const verify = this.itens.filter(encontrado => encontrado.find == false);
			if (verify.length === 0) {
				this.cpuPlaying = false
				this.finished = true
				if (this.point1 === this.point2) {
					this.winner = "Ninguém venceu, o jogo Empatou!"					
				} 
				if (this.point1 > this.point2) {
					this.winner = "O Jogador 1 Venceu!"
					this.player = 0
					this.game1++
				} 
				if (this.point1 < this.point2) {
					if (this.typePlayer === 0) this.winner = "O Jogador 2 Venceu!"
					if (this.typePlayer === 1) this.winner = "O CPU Venceu!"
					this.player = 1
					this.game2++
				} 					
			}
		},
		async restart() {
			this.itens = []
			this.itemSelected = []
			this.point1 = 0			
			this.point2 = 0			
			this.finished = false
			this.winner = ''			
			if (this.typePlayer === 1 && this.player === 1) {
				this.cpuPlaying = true
				await this.cpuPlayer();
			} else {
				this.cpuPlaying = false
			}			
			await this.loadGame()
		},
		mainMenu() {
			this.$emit('mainMenu', true)
		}
	},
}
</script>

<style scoped>
	@import url('https://fonts.googleapis.com/css2?family=Carter+One&family=Comfortaa:wght@700&display=swap');

	@media screen and (min-width: 320px) {
		.finished {
			width: 100%;
			height: 150px;
			background-color: #3498db;
			display: flex;
			top: 50%;
			left: 50%;
			margin-right: -50%;
			transform: translate(-50%, -50%);
			align-items: center;
			justify-content: center;
			color: #fff;
			position: absolute;
		}

		.cpuPlaying {
			width: 100%;
			height: 100%;
			display: flex;
			top: 50%;
			left: 50%;
			margin-right: -50%;
			transform: translate(-50%, -50%);
			align-items: center;
			justify-content: center;
			border-radius: 10px;
			color: black;
			position: absolute;
			opacity: 1;			
		}

		.board {
			width: 95%;
			height: 470px;
			display: flex;
			flex-direction: row;
			flex-wrap: wrap;
			justify-content: center;
			align-content: flex-start;
			background: #fff;		
			margin: 0 auto;
			border-radius: 10px;
			margin-top: 15px;
			position: relative;
		}

		.painel {
			font-family: 'Bangers', cursive;
			font-size: 50px;		
		}

		.parts {
			width: 70px;
			height: 70px;
			margin: 10px ;
			border-radius: 50%;		
			border: 1px solid #ccc;
			background-size: cover;
			background-repeat: no-repeat;
			cursor: pointer;
		}

		.parts:hover {
			box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
		}

		.score {
			display: flex;
			flex-direction: row;
			justify-content: center;
			font-family: 'Carter One', cursive;
			font-family: 'Comfortaa', cursive;
			color: #fff;
			margin-top: 20px;
			font-size: 20px;
		}

		.player1, .player2 {
			margin: 10px
		}

		.rodape {
			display: flex;
			flex-direction: row;
			justify-content: center;
		}

		.botaoMenu {
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
			margin: 20px
		}
	}

	@media screen and (min-width: 576px) {
	}

	@media screen and (min-width: 768px) {
		.board {
			width: 95%;
			height: 320px;
		}

		.parts {
			width: 85px;
			height: 85px;
			margin: 7px ;
			border-radius: 50%;		
		}

		.score {
			margin-top: 10px;
		}
	}

	@media screen and (min-width: 992px) {
		.board {
			width: 700px;
			height: 550px;
		}

		.parts {
			width: 100px;
			height: 100px;
			margin: 17px ;
			border-radius: 50%;		
		}
	}

	@media screen and (min-width: 1200px) {
	}

	@media screen and (min-width: 1364px) {
	}

	@media screen and (min-width: 1600px) {
	}


</style>
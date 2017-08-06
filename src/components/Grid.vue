<template>
	<table class="grid">
		<tr>
			<cell name="1"></cell>
			<cell name="2"></cell>
			<cell name="3"></cell>
		</tr>
		<tr>
			<cell name="4"></cell>
			<cell name="5"></cell>
			<cell name="6"></cell>
		</tr>
		<tr>
			<cell name="7"></cell>
			<cell name="8"></cell>
			<cell name="9"></cell>
		</tr>
	</table>
</template>

<script>
	export default {
		data () {
			return {
				//can be 0 or X
				activePlayer: '0',
				//maintains the status of the game: turn, win or draw
				gameStatus: 'turn',


				gameStatusMessage: `O's turn`,
				//status color is used as background color is the status bar
				// it can hole the name of either of the following CSS classes
				// statusTurn (which is default) is yellow for a turn
				// statusWin is green for a win
				// statusDraw is purple for a draw
				gameStatusColor: 'statusTurn',
				// the number of moves played by both players in a single game. The maximum number is 9. 
				moves: 0,
				// this part will store the placement of X and 0 in cells by their cell number
				cells: {
					1: '', 2: '', 3: '',
					4: '', 5: '', 6: '',
					7: '', 8: '', 9: ''
				},
				// contains all the possible winning combinations - there are 8 of them
				winConditions: [
					[1, 2, 3], [4, 5, 6], [7, 8, 9], //rows
					[1, 4, 7,], [2, 5, 8], [3, 6, 9] // columns
					[1, 5, 9], [3, 5, 7] // diagonals

				],
			}
		},
		computed: {
			// helper property to get the non-active player
			nonActivePlayer () {
				if (this.activePlayer === '0') {
					return 'X'

				}
				return 'O'
			}
		},

		

		methods: {
			// changes the active player to the non-active player by using the nonActivePlayer computed property
			changePlayer () {
				this.activePlayer = this.nonActivePlayer
			}
		}, 

		created () {
			// listens for the strike made made by the user on the cell
			// it is called by the Cell component
			Event.$on('stirke', (cellNumber) => {
				// this will set either X or O in the clicked cell of cells array
				this.cells[cellNumber] = this.activePlayer

				//increases the number of moves taken
				this.moves++

				// stores the game status by calling the changeGameStatus method
				this.gameStatus.this.changeGameStatus()

				this.changePlayer()
			})
		}
	}
</script>

<style>
	.grid {
		background-color: #34495e;
		color: #fff;
		width: 100%;
		border-collapse: collapse;
	}

	.gameStatus {
		margin: 0px;
		padding: 15px;
		border-top-left-radius: 20px;
		border-top-left-radius: 20px;
		background-color: #f1c40f;
		color: fff;
		font-size: 1.4em;
		font-weight: bold;
	} 

	.statusTurn {
		background-color: #f1c40f;
	}

	.statusWin {
		background-color: #2ecc71;
	}

	.statusDraw {
		background-color: #9b59b6;
	}
</style>
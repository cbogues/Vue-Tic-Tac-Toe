<template>
	<div>
		<div class="gameStatus" :class="gameStatusColor">
			{{ gameStatusMessage }}
		</div>
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
	</div>
</template>

<script>
import Cell from'./Cell.vue'

export default {
	components: { Cell },
	data () {
		return {
			//can be 0 or X
			activePlayer: 'O',
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
			if (this.activePlayer === 'O') {
				return 'X'

			}
			return 'O'
		}
	},

	watch: {
		// checks for the change in the value of gameStatus and changes the status message and the color
		gameStatus () {
			if (this.gameStatus === 'win') {
				this.gameStatusColor = 'statusWin'
				return
			} else if  (this.gameStatus === 'draw') {
				this.gameStatusColor = 'statusDraw'
				this.gameStatusMessage = 'Draw!'

				return
			}

				this.gameStatusMessage = `${this.activePlayer}'s turn`
		}
	},

	methods: {
		// changes the active player to the non-active player by using the nonActivePlayer computed property
		changePlayer () {
			this.activePlayer = this.nonActivePlayer
		},
	 		// this will check for the possible win conditions 
	 		checkForWin () {
	 			for (let i = 0; i < this.winConditions.length; i++) {
	 				// gets a single condition wC from the whole array 
	 				let wc = this.winConditions[i]
	 				let cells = this.cells

	 				//companres 3 cell values based on the cells 
	 				if  (this.areEqual(cells[wc[0]], cells[wc[1]], cells[wc[2]])) {
	 					return true
	 				}
	 			}
	 			return false
	 		},

	 		gameIsWon () {
	 			// This will fire the win event for the App component to change the score
	 			Event.$emit('win', this.activePlayer)

	 			// sets the game status message
	 			this.gameStatusMessage = `${this.activePlayer} Wins !`

	 			// fires the event for the Cell to freeze
	 			Event.$emit('freeze')

	 			// sets the status to win
	 			return 'win'
	 		},

	//returns the game status to the gameStatus property
			changeGameStatus () {
				if (this.checkForWin()) {
					return this.gameIsWon()

				//checks if the game is still not won and all the cells are filled
				} else if (this.moves === 9) {
					// sets the game status to draw
					return 'draw'
			}
			// sets the game status to turn - next player's turn
			return 'turn'
	},

	 	// helper function for comparing the cell values
	 	areEqual () {
	 		var len = arguments.length;

	 		// loops through each value and compares them with an empty string and for inequality
	 		for (var i = 1; i < len; i++){
	 			if (arguments[i] === '' || arguments[i] !== arguments[i-1])
	 				return false;
	 		}
	 		return true;
	 	}	
	},

	created () {
		// listens for the strike made made by the user on the cell
		// it is called by the Cell component
		Event.$on('strike', (cellNumber) => {
			// this will set either X or O in the clicked cell of cells array
			this.cells[cellNumber] = this.activePlayer

			//increases the number of moves taken
			this.moves++

			// stores the game status by calling the changeGameStatus method
			this.gameStatus = this.changeGameStatus()

			this.changePlayer()
		})

		// listens for the restart button to be pressed
		// the data is called by the App Component and is reinitialized
		Event.$on('gridReset', () => {
			Object.assign(this.$data, this.$options.data())
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
  border-top-right-radius: 20px;
  background-color: #f1c40f;
  color: #fff;	
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
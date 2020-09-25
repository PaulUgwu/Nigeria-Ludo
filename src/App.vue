<template>
<div class="container">
    <div class="row mt-5">
        <div class="col-12 text-center">
            <h1>Current Player: {{ currentPlayeInfo }}</h1>
        </div>
        <div class="col-md-6">
            <div id="ludo-board">
                <img @click="startNewMove('green')" :src="'./assets/pin_group/cross-' + remaining_green_pins + '.png'" alt="" id="cross-pin-group">
                <img @click="startNewMove('blue')" :src="'./assets/pin_group/squar-' + remaining_blue_pins + '.png'" alt="" id="squar-pin-group">
                <img @click="startNewMove('purple')" :src="'./assets/pin_group/star-' + remaining_purple_pins + '.png'" alt="" id="star-pin-group">
                <img @click="startNewMove('orange')" :src="'./assets/pin_group/t-angle-' + remaining_orange_pins + '.png'" alt="" id="t-angle-pin-group">

                <img v-for="(item, index) in greenPinsInPlay" :key="index" src="./assets/pins/cross-pin.png" alt="" class="pin" :style="{width:20+'px', position:'absolute', left: greenMovementeMap[item.position].x+'px', top:greenMovementeMap[item.position].y+'px'}" @click="movePin('green', index)">

                <img v-for="(item, index) in bluePinsInPlay" :key="index" src="./assets/pins/squar-pin.png" alt="" class="pin" :style="{width:20+'px', position:'absolute', left: blueMovementeMap[item.position].x+'px', top:blueMovementeMap[item.position].y+'px'}" @click="movePin('blue', index)">

                <img v-for="(item, index) in purplePinsInPlay" :key="index" src="./assets/pins/star-pin.png" alt="" class="pin" :style="{width:20+'px', position:'absolute', left: purpleMovementeMap[item.position].x+'px', top:purpleMovementeMap[item.position].y+'px'}" @click="movePin('purple', index)">

                <img v-for="(item, index) in orangePinsInPlay" :key="index" src="./assets/pins/t-angle-pin.png" alt="" class="pin" :style="{width:20+'px', position:'absolute', left: orangeMovementeMap[item.position].x+'px', top:orangeMovementeMap[item.position].y+'px'}" @click="movePin('orange', index)">

            </div>
        </div>

        <div class="col-md-6 text-center">
            <div>
                <img :src="'./assets/dice/dice-' + dice1 + '.png'" alt="dice" class="m-1 dice">
                <img :src="'./assets/dice/dice-' + dice2 + '.png'" alt="dice" class="m-1 dice">
            </div>
            <button class="btn btn-primary mt-3" @click="rollDice" :disabled="canNotRollDice">Roll Dice</button>
            <h1>Your Roll Resuls</h1>
            <div v-for="(diceRollResult, index) in diceRollResults" :key="index">
                <button @click="putInPlay(index, diceRollResult)" class="btn btn-success m-1" :disabled="currently_in_play(index)"> Play {{ diceRollResult }}</button>
            </div>

        </div>
    </div>
</div>
</template>

<script>
// import $ from 'jquery'

export default {
    computed: {
        remaining_green_pins() {
            return 4 - this.greenPinsInPlay.length;
        },
        remaining_blue_pins() {
            return 4 - this.bluePinsInPlay.length;
        },
        remaining_orange_pins() {
            return 4 - this.orangePinsInPlay.length;
        },
        remaining_purple_pins() {
            return 4 - this.purplePinsInPlay.length;
        },
        currentPlayeInfo() {
            console.log(this.currentPlayer)
            return this.players[this.currentPlayer].name + '(' + this.players[this.currentPlayer].colors.join(', ') + ')'
        }
    },
    watch: {
        // whenever diceRollResults changes, this function will run
        diceRollResults: {
            handler: function (val) {
                if (val.length === 0) {
                    this.canNotRollDice = false
                    this.switchPlayer()
                }
            },
            deep: true
        }
    },
    methods: {
        switchPlayer() {
            if (this.currentPlayer === 0) {
                this.currentPlayer = 1
            } else {
                this.currentPlayer = 0
            }
        },
        movePin(pin_type, index) {

            if (this.players[this.currentPlayer].colors[0] != pin_type && this.players[this.currentPlayer].colors[1] != pin_type) {
                alert('You cant move this pin');
                return;
            }

            if (this.rollInPlay.numberRolled >= 1) {
                if (pin_type == 'green') {
                    this.greenPinsInPlay[index].position += this.rollInPlay.numberRolled
                } else if (pin_type == 'blue') {
                    this.bluePinsInPlay[index].position += this.rollInPlay.numberRolled
                } else if (pin_type == 'orange') {
                    this.orangePinsInPlay[index].position += this.rollInPlay.numberRolled
                } else if (pin_type == 'purple') {
                    this.purplePinsInPlay[index].position += this.rollInPlay.numberRolled
                }

                // 
                this.diceRollResults.splice(this.rollInPlay.index, 1);
                this.rollInPlay = {};
                console.log(this.diceRollResults)
            }

        },
        startNewMove(pin_type) {

            if (this.players[this.currentPlayer].colors[0] != pin_type && this.players[this.currentPlayer].colors[1] != pin_type) {
                alert('You cant move this pin');
                return;
            }

            if (this.rollInPlay.numberRolled !== 6) {
                alert('you need a 6 to move a new pin');
                return;
            }

            if (pin_type == 'green') {
                this.greenPinsInPlay.push({
                    position: 0
                })
            } else if (pin_type == 'blue') {
                this.bluePinsInPlay.push({
                    position: 0
                })
            } else if (pin_type == 'orange') {
                this.orangePinsInPlay.push({
                    position: 0
                })
            } else if (pin_type == 'purple') {
                this.purplePinsInPlay.push({
                    position: 0
                })
            }

            // 
            this.diceRollResults.splice(this.rollInPlay.index, 1);
            this.rollInPlay = {};

            console.log(this.diceRollResults)

        },
        rollDice() {

            let rollResults = [];
            rollResults.push(this.generatRandonNumber())
            rollResults.push(this.generatRandonNumber())
            this.dice1 = rollResults[0]
            this.dice2 = rollResults[1]

            // change if the palayer roled 2 sixs
            if (rollResults[0] === 6 && rollResults[1] === 6) {
                this.canNotRollDice = false
            } else {
                this.canNotRollDice = true
            }

            if (this.currentPlayer == 0 && this.purplePinsInPlay.length === 0 && this.purplePinsInPlay.length === 0 && rollResults[0] != 6 && rollResults[1] != 6) {
                alert('You did not get a 6 and you don\'t have any pin in play, dice will be passed to the next player');
                this.switchPlayer()
                this.canNotRollDice = false
                return;
            } else if (this.currentPlayer == 1 && this.bluePinsInPlay.length === 0 && this.orangePinsInPlay.length === 0 && rollResults[0] != 6 && rollResults[1] != 6) {
                alert('You did not get a 6 and you don\'t have any pin in play, dice will be passed to the next player');
                this.switchPlayer()
                this.canNotRollDice = false
                return;
            }

            this.diceRollResults.push(...rollResults)

        },
        generatRandonNumber() {
            return Math.floor(Math.random() * 6) + 1
        },
        putInPlay(index, numberRolled) {
            this.rollInPlay = {
                index,
                numberRolled
            }
        },
        currently_in_play(index) {
            if (index === this.rollInPlay.index) {
                return true
            } else {
                return false
            }
        }
    },
    data() {
        return {
            players: [{
                    colors: ['purple', 'green'],
                    name: 'Palyer 1'
                },
                {
                    colors: ['blue', 'orange'],
                    name: 'Player 2'
                }
            ],
            dice1: 0,
            dice2: 0,
            currentPlayer: 0,
            rollInPlay: {},
            canNotRollDice: false,
            diceRollResults: [],
            bluePinsInPlay: [],
            greenPinsInPlay: [],
            orangePinsInPlay: [],
            purplePinsInPlay: [],
            blueMovementeMap: [{ //map all the movements of the blue pins from start to finish
                    x: 273,
                    y: 38
                },
                {
                    x: 273,
                    y: 72
                },
                {
                    x: 273,
                    y: 106
                },
                {
                    x: 273,
                    y: 140
                },
                {
                    x: 273,
                    y: 174
                },
                {
                    x: 306,
                    y: 207
                },
                {
                    x: 340,
                    y: 207
                },
                {
                    x: 374,
                    y: 207
                },
                {
                    x: 408,
                    y: 207
                },
                {
                    x: 442,
                    y: 207
                },
                {
                    x: 476,
                    y: 207
                },
                {
                    x: 476,
                    y: 240
                },
                {
                    x: 476,
                    y: 273
                },
                {
                    x: 441,
                    y: 273
                },
                {
                    x: 407,
                    y: 273
                },
                {
                    x: 373,
                    y: 273
                },
                {
                    x: 339,
                    y: 273
                },
                {
                    x: 305,
                    y: 273
                },
                {
                    x: 273,
                    y: 306
                },
                {
                    x: 273,
                    y: 340
                },
                {
                    x: 273,
                    y: 374
                },
                {
                    x: 273,
                    y: 408
                },
                {
                    x: 273,
                    y: 442
                },
                {
                    x: 273,
                    y: 476
                },
                {
                    x: 240,
                    y: 476
                },
                {
                    x: 207,
                    y: 476
                },
                {
                    x: 207,
                    y: 442
                },
                {
                    x: 207,
                    y: 408
                },
                {
                    x: 207,
                    y: 374
                },
                {
                    x: 207,
                    y: 340
                },
                {
                    x: 207,
                    y: 306
                },
                {
                    x: 175,
                    y: 272
                },
                {
                    x: 140,
                    y: 272
                },
                {
                    x: 106,
                    y: 272
                },
                {
                    x: 72,
                    y: 272
                },
                {
                    x: 38,
                    y: 272
                },
                {
                    x: 4,
                    y: 272
                },
                {
                    x: 4,
                    y: 239
                },
                {
                    x: 4,
                    y: 207
                },
                {
                    x: 38,
                    y: 207
                },
                {
                    x: 72,
                    y: 207
                },
                {
                    x: 106,
                    y: 207
                },
                {
                    x: 140,
                    y: 207
                },
                {
                    x: 174,
                    y: 207
                },
                {
                    x: 208,
                    y: 174
                },
                {
                    x: 207,
                    y: 140
                },
                {
                    x: 207,
                    y: 106
                },
                {
                    x: 207,
                    y: 72
                },
                {
                    x: 207,
                    y: 38
                },
                {
                    x: 207,
                    y: 4
                },
                {
                    x: 241,
                    y: 4
                },
                {
                    x: 241,
                    y: 38
                },
                {
                    x: 241,
                    y: 72
                },
                {
                    x: 241,
                    y: 106
                },
                {
                    x: 241,
                    y: 140
                },
                {
                    x: 241,
                    y: 176
                },
                {
                    x: 241,
                    y: 210
                }
            ],
            greenMovementeMap: [{ //map all the movements of the green pins from start to finish
                    x: 441,
                    y: 273
                },
                {
                    x: 407,
                    y: 273
                },
                {
                    x: 373,
                    y: 273
                },
                {
                    x: 339,
                    y: 273
                },
                {
                    x: 305,
                    y: 273
                },
                {
                    x: 273,
                    y: 306
                },
                {
                    x: 273,
                    y: 340
                },
                {
                    x: 273,
                    y: 374
                },
                {
                    x: 273,
                    y: 408
                },
                {
                    x: 273,
                    y: 442
                },
                {
                    x: 273,
                    y: 476
                },
                {
                    x: 240,
                    y: 476
                },
                {
                    x: 207,
                    y: 476
                },
                {
                    x: 207,
                    y: 442
                },
                {
                    x: 207,
                    y: 408
                },
                {
                    x: 207,
                    y: 374
                },
                {
                    x: 207,
                    y: 340
                },
                {
                    x: 207,
                    y: 306
                },
                {
                    x: 175,
                    y: 272
                },
                {
                    x: 140,
                    y: 272
                },
                {
                    x: 106,
                    y: 272
                },
                {
                    x: 72,
                    y: 272
                },
                {
                    x: 38,
                    y: 272
                },
                {
                    x: 4,
                    y: 272
                },
                {
                    x: 4,
                    y: 239
                },
                {
                    x: 4,
                    y: 207
                },
                {
                    x: 38,
                    y: 207
                },
                {
                    x: 72,
                    y: 207
                },
                {
                    x: 106,
                    y: 207
                },
                {
                    x: 140,
                    y: 207
                },
                {
                    x: 174,
                    y: 207
                },
                {
                    x: 208,
                    y: 174
                },
                {
                    x: 207,
                    y: 140
                },
                {
                    x: 207,
                    y: 106
                },
                {
                    x: 207,
                    y: 72
                },
                {
                    x: 207,
                    y: 38
                },
                {
                    x: 207,
                    y: 4
                },
                {
                    x: 240,
                    y: 4
                },
                {
                    x: 273,
                    y: 4
                },
                {
                    x: 273,
                    y: 38
                },
                {
                    x: 273,
                    y: 72
                },
                {
                    x: 273,
                    y: 106
                },
                {
                    x: 273,
                    y: 140
                },
                {
                    x: 273,
                    y: 174
                },
                {
                    x: 306,
                    y: 207
                },
                {
                    x: 340,
                    y: 207
                },
                {
                    x: 374,
                    y: 207
                },
                {
                    x: 408,
                    y: 207
                },
                {
                    x: 442,
                    y: 207
                },
                {
                    x: 476,
                    y: 207
                },
                {
                    x: 476,
                    y: 240
                },
                {
                    x: 441,
                    y: 240
                },
                {
                    x: 407,
                    y: 240
                },
                {
                    x: 373,
                    y: 240
                },
                {
                    x: 339,
                    y: 240
                },
                {
                    x: 305,
                    y: 240
                },
                {
                    x: 271,
                    y: 240
                },
            ],
            orangeMovementeMap: [{ //map all the movements of the orange pins from start to finish
                    x: 207,
                    y: 442
                },
                {
                    x: 207,
                    y: 408
                },
                {
                    x: 207,
                    y: 374
                },
                {
                    x: 207,
                    y: 340
                },
                {
                    x: 207,
                    y: 306
                },
                {
                    x: 175,
                    y: 272
                },
                {
                    x: 140,
                    y: 272
                },
                {
                    x: 106,
                    y: 272
                },
                {
                    x: 72,
                    y: 272
                },
                {
                    x: 38,
                    y: 272
                },
                {
                    x: 4,
                    y: 272
                },
                {
                    x: 4,
                    y: 239
                },
                {
                    x: 4,
                    y: 207
                },
                {
                    x: 38,
                    y: 207
                },
                {
                    x: 72,
                    y: 207
                },
                {
                    x: 106,
                    y: 207
                },
                {
                    x: 140,
                    y: 207
                },
                {
                    x: 174,
                    y: 207
                },
                {
                    x: 208,
                    y: 174
                },
                {
                    x: 207,
                    y: 140
                },
                {
                    x: 207,
                    y: 106
                },
                {
                    x: 207,
                    y: 72
                },
                {
                    x: 207,
                    y: 38
                },
                {
                    x: 207,
                    y: 4
                },
                {
                    x: 240,
                    y: 4
                },
                {
                    x: 273,
                    y: 4
                },
                {
                    x: 273,
                    y: 38
                },
                {
                    x: 273,
                    y: 72
                },
                {
                    x: 273,
                    y: 106
                },
                {
                    x: 273,
                    y: 140
                },
                {
                    x: 273,
                    y: 174
                },
                {
                    x: 306,
                    y: 207
                },
                {
                    x: 340,
                    y: 207
                },
                {
                    x: 374,
                    y: 207
                },
                {
                    x: 408,
                    y: 207
                },
                {
                    x: 442,
                    y: 207
                },
                {
                    x: 476,
                    y: 207
                },
                {
                    x: 476,
                    y: 240
                },
                {
                    x: 476,
                    y: 273
                },
                {
                    x: 441,
                    y: 273
                },
                {
                    x: 407,
                    y: 273
                },
                {
                    x: 373,
                    y: 273
                },
                {
                    x: 339,
                    y: 273
                },
                {
                    x: 305,
                    y: 273
                },
                {
                    x: 273,
                    y: 306
                },
                {
                    x: 273,
                    y: 340
                },
                {
                    x: 273,
                    y: 374
                },
                {
                    x: 273,
                    y: 408
                },
                {
                    x: 273,
                    y: 442
                },
                {
                    x: 273,
                    y: 476
                },
                {
                    x: 240,
                    y: 476
                },
                {
                    x: 240,
                    y: 442
                },
                {
                    x: 240,
                    y: 408
                },
                {
                    x: 240,
                    y: 374
                },
                {
                    x: 240,
                    y: 340
                },
                {
                    x: 240,
                    y: 306
                },
                {
                    x: 240,
                    y: 272
                },
            ],
            purpleMovementeMap: [{ //map all the movements of the blue purple from start to finish
                    x: 38,
                    y: 207
                },
                {
                    x: 72,
                    y: 207
                },
                {
                    x: 106,
                    y: 207
                },
                {
                    x: 140,
                    y: 207
                },
                {
                    x: 174,
                    y: 207
                },
                {
                    x: 208,
                    y: 174
                },
                {
                    x: 207,
                    y: 140
                },
                {
                    x: 207,
                    y: 106
                },
                {
                    x: 207,
                    y: 72
                },
                {
                    x: 207,
                    y: 38
                },
                {
                    x: 207,
                    y: 4
                },
                {
                    x: 240,
                    y: 4
                },
                {
                    x: 273,
                    y: 4
                },
                {
                    x: 273,
                    y: 38
                },
                {
                    x: 273,
                    y: 72
                },
                {
                    x: 273,
                    y: 106
                },
                {
                    x: 273,
                    y: 140
                },
                {
                    x: 273,
                    y: 174
                },
                {
                    x: 306,
                    y: 207
                },
                {
                    x: 340,
                    y: 207
                },
                {
                    x: 374,
                    y: 207
                },
                {
                    x: 408,
                    y: 207
                },
                {
                    x: 442,
                    y: 207
                },
                {
                    x: 476,
                    y: 207
                },
                {
                    x: 476,
                    y: 240
                },
                {
                    x: 476,
                    y: 273
                },
                {
                    x: 441,
                    y: 273
                },
                {
                    x: 407,
                    y: 273
                },
                {
                    x: 373,
                    y: 273
                },
                {
                    x: 339,
                    y: 273
                },
                {
                    x: 305,
                    y: 273
                },
                {
                    x: 273,
                    y: 306
                },
                {
                    x: 273,
                    y: 340
                },
                {
                    x: 273,
                    y: 374
                },
                {
                    x: 273,
                    y: 408
                },
                {
                    x: 273,
                    y: 442
                },
                {
                    x: 273,
                    y: 476
                },
                {
                    x: 240,
                    y: 476
                },
                {
                    x: 206,
                    y: 476
                },
                {
                    x: 207,
                    y: 442
                },
                {
                    x: 207,
                    y: 408
                },
                {
                    x: 207,
                    y: 374
                },
                {
                    x: 207,
                    y: 340
                },
                {
                    x: 207,
                    y: 306
                },
                {
                    x: 175,
                    y: 272
                },
                {
                    x: 140,
                    y: 272
                },
                {
                    x: 106,
                    y: 272
                },
                {
                    x: 72,
                    y: 272
                },
                {
                    x: 38,
                    y: 272
                },
                {
                    x: 4,
                    y: 272
                },
                {
                    x: 4,
                    y: 239
                },
                {
                    x: 38,
                    y: 239
                },
                {
                    x: 72,
                    y: 239
                },
                {
                    x: 106,
                    y: 239
                },
                {
                    x: 140,
                    y: 239
                },
                {
                    x: 174,
                    y: 239
                },
                {
                    x: 208,
                    y: 239
                },
            ],

        }
    }
}
</script>

<style scoped>
#ludo-board {
    width: 500px;
    height: 500px;
    background-image: url('./assets/board.png');
    background-repeat: no-repeat;
    background-size: cover;
    position: relative;
}

.dice {
    width: 100px;
}

#cross-pin-group,
#squar-pin-group,
#star-pin-group,
#t-angle-pin-group {
    width: 150px;
    position: absolute;
}

#cross-pin-group {
    right: 30px;
    bottom: 30px;
}

#t-angle-pin-group {
    left: 25px;
    bottom: 30px;
}

#squar-pin-group {
    right: 25px;
    top: 30px;
}

#star-pin-group {
    top: 30px;
    left: 25px;
}
</style>

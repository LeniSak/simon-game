<template>
    <div class="simongame">
		<h1>Игра Саймон</h1>
        <div class="wrapper">
				<div class="simon">
					<ul>
						<li class="tittle red" :class="lightUp[1]" @click='clickEvent($event.target)' id="1"></li>
						<li class="tittle blue" :class="lightUp[2]" @click='clickEvent($event.target)' id="2"></li>
						<li class="tittle yellow" :class="lightUp[3]" @click='clickEvent($event.target)' id="3"></li>
						<li class="tittle green" :class="lightUp[4]" @click='clickEvent($event.target)' id="4"></li>
					</ul>
				</div>
            <div class="game-board">
                <div class="game-info">
                    <h2>Раунд: {{arr.length}}</h2>
                    <button v-if="arr.length === 0" @click='nextRound'>Начать игру</button>
                    <button v-else @click='gameOver'>Остановить</button>
                    <p v-if="lost">Вы прошли {{lost}} раунда!</p>
                </div>
                <div class="game-options">
                    <h2>Уровень сложности:</h2>
                    <div class="radio">
                        <input type="radio" v-model="difficult" id="easy" value="easy" checked><label for="easy">Легкий</label>
                    </div>
                    <div class="radio">
                        <input type="radio" v-model="difficult" id="normal" value="normal"><label for="normal">Средний</label>
                    </div>
                    <div class="radio">
                        <input type="radio" v-model="difficult" id="hard" value="hard"><label for="hard">Сложный</label>
                    </div>				
                </div>
            </div>
			
        </div>
	</div>
</template>

<script>
export default {
    name: 'SimonGame',
    data () {
        return {
            arr: [],
            lightUp: {
                '1': '',
                '2': '',
                '3': '',
                '4': '',
            },
            round: 0,
            difficult: 'easy',
            active: false,
            mode: {
                easy: 1500,
                normal: 1000,
                hard: 400,
            },
            time: 1500,
            audio: {
                '1': () => {
                    const audio = new Audio(require('../assets/sounds/sounds_1.mp3'))
                    return audio.play()
                    },
                '2': () => {
                    const audio = new Audio(require('../assets/sounds/sounds_2.mp3'))
                    return audio.play()
                    },
                '3': () => {
                    const audio = new Audio(require('../assets/sounds/sounds_3.mp3'))
                    return audio.play()
                    },
                '4': () => {
                    const audio = new Audio(require('../assets/sounds/sounds_4.mp3'))
                    return audio.play()
                    },
            },
            lost: '',
        }
    },
    methods: {
        clickEvent(target) {
            if (this.active) {
                this.setLight(target.id)
                this.isWin(target.id)
            }
        },

        setLight(id, AI = false) {
            this.lightUp[id] = 'light'
            this.audio[id]()
            setTimeout(() => {
                    this.lightUp[id] = ''
                    }, AI ? this.time / 2 : 150)
        },
        filterTo(name) {
            return this.arr.filter(x => typeof(x) === name)
        },
        isWin(num) {
            const arrNum = this.filterTo("number")
            const arrStr = this.filterTo("string")
            if (arrNum[0] == num) {
                arrNum[0] = num
                this.arr = arrStr.map(x => x)
                this.arr.push(...arrNum)

                if (this.filterTo("number").length == 0) {
                    this.nextRound()
                }
            } else {
                this.gameOver(true)
            }
        },
        gameOver(bool) {
            this.active = false

            if (bool) {
                this.lost = this.arr.length
                console.log('lost the game');
            }
            this.arr = []
        },
        nextRound() {
            const num = this.randomNumber()

            this.arr.push(num)
            this.arr = this.arr.map(x => Number(x))

            this.lost = ''
            this.active = false
            this.arr.forEach((value, index) => {
                setTimeout(()=> {
                    this.setLight(value, true)

                    if (index == (this.arr.length - 1)) {
                        this.active = true;
                    }
                }, this.time * (index + 1))
            })
        },
        randomNumber() {
            return Math.floor((Math.random() * 4) + 1)
        }   
    },
    watch: {
        difficult: function (value) {
            this.time = this.mode[value]
            this.gameOver()
        },
    }
}
</script>

<style>
    body {
        font-family: Arial, serif;
        -webkit-user-select: none;    
        -moz-user-select: none; 
        -ms-user-select: none; 
        user-select: none;
    }

    .wrapper {
        display: flex;
        justify-content: center;
    }

    h1 {
        margin: 1em 0 2em;
        text-align: center;
    }

    ul {
        list-style: none;
    }

    ul, li {
        padding: 0;
        margin: 0;
    }

    .simon {
        background: #fff;
        margin-right: 3em;
        position: relative;
        left: 2.5px;
        width: 300px;
        height: 300px;
        border-radius: 150px;
        box-shadow: 2px 1px 12px #aaa;
    }
    
    .tittle {
        opacity: 0.6;
        transition: opacity 250ms ease;
    }

    .tittle.light {
        opacity: 1;
    }

    .red, .green, .blue, .yellow {
        height: 295px;
        border-radius: 150px;
        position: absolute;
    }

    .red:hover, .blue:hover, .yellow:hover, .green:hover {
	    border: 2px solid black
    }

    .red {
        background: #FF5643;
        clip: rect(0px, 300px, 150px, 150px);
        width: 295px;
        margin-left: 5px;
    }

    .blue {
        background: dodgerblue;
        clip: rect(0px, 150px, 150px, 0px);
        width: 295px;
    }

    .yellow {
        background: #FEEF33;
        clip: rect(150px, 150px, 300px, 0px);
        width: 295px;
        margin-top: 5px;
    }

    .green {
        background: #BEDE15;
        clip: rect(150px, 300px, 300px, 150px);
        width: 295px;
        margin: 5px 0 0 5px;
    }

    .game-board {
        display: inherit;
        flex-direction: column;
        justify-content: center;
    }

    .game-info p {
        margin: 20px 0 0;
        padding: 0;
    }

    .game-info button {
        font-size: 1.4em;
        border-radius: 10px;
        background: #6DABE8;
        border: none;
        padding: 0.3em 0.6em;
    }

    .game-info button:hover {
        background: #78BCFF;
    }

    .game-options {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
    }

    .game-options h2 {
        margin-top: 30px;
        margin-bottom: 0;
    }


</style>
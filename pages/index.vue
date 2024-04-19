<template>
    <div class="container">
        <h1>Typing Game</h1>
        <p v-if="gameStarted">CurrentWord : {{ currentWord }}</p>
        <p>Score : {{ score }}</p>
        <!-- <input class="userInput" @input="checkInput" v-if="gameStarted" /> -->
        <input class="userInput" v-model="userInput" @input="checkInput" ref="userInput">

        <button class="start-stop" @click="startGame">{{ gameStarted ? 'Stop' : 'Start' }}</button>
        <div class="keyboard">
            <div v-for="(row, rowIndex) in keyboard" :key="'row' + rowIndex">
                <div v-for="key in row" :key="key.letter" :class="{ 'active': key.active }">
                    {{ key.letter }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            words: ['apple', 'banana', 'cherry', 'date', 'strawberry'],
            currentWord: '',
            userInput: '',
            score: 0,
            gameStarted: false,
            keyboard: [
                ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p'],
                ['a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l'],
                ['z', 'x', 'c', 'v', 'b', 'n', 'm']
            ].map(row => row.map(letter => ({ letter, active: false })))
        }
    },
    mounted() {
        window.addEventListener('keydown', this.keydownHandler);
        window.addEventListener('keyup', this.keyupHandler);
    },
    beforeDestroy() {
        window.removeEventListener('keydown', this.keydownHandler);
        window.removeEventListener('keyup', this.keyupHandler);
    },
    methods: {
        startGame() {
            if (this.gameStarted) {
                // Reset the game
                this.currentWord = '';
                this.userInput = '';
                this.score = 0;
                this.keyboard.forEach(row => row.forEach(key => key.active = false));
                this.gameStarted = false; // Stop the game
            } else {
                // Start the game
                this.gameStarted = true;
                this.$nextTick(() => {
                    this.$refs.userInput.focus(); // Add this line
                });

                this.score = 0;
                this.userInput = '';
                this.pickNewWord();
            }
        },
        pickNewWord() {
            const randomIndex = Math.floor(Math.random() * this.words.length);
            this.currentWord = this.words[randomIndex];
        },
        checkInput() {
            if (this.userInput === this.currentWord) {
                this.score++;
                this.userInput = '';
                this.pickNewWord();
            }
            this.keyboard.forEach(key => {
                key.active = this.userInput.includes(key.letter);
            });
        },
        keydownHandler(e) {
            const key = this.keyboard.flat().find(key => key.letter.toLowerCase() === e.key.toLowerCase());
            if (key) key.active = true;
        },
        keyupHandler(e) {
            const key = this.keyboard.flat().find(key => key.letter.toLowerCase() === e.key.toLowerCase());
            if (key) key.active = false;
        }
    }
}
</script>

<style scoped>
.container {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 50px auto 50px;
}


.userInput {
    width: 200px;
    margin-bottom: 10px;
}

.start-stop {
    width: 100px;
    margin-bottom: 10px;
}

.keyboard {
    display: flex;
    flex-direction: column;
    align-items: center;
}

.keyboard div {
    display: flex;
    flex-wrap: wrap;
    padding: 10px;
    margin: 5px;
    border: 1px solid #000;
    border-radius: 8px;
}

.keyboard div div {
    display: block;
    width: 20px;
    text-align: center;
    margin: 5px;
    border: 1px solid #000;
    border-radius: 8px;
}

.keyboard .active {
    background-color: #99f;
}
</style>
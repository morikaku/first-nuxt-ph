<template>
    <div class="container">
        <h1>Typing Game</h1>
        <p v-if="gameStarted">currentWord : {{ currentWord }}</p>
        <p>Score : {{ score }}</p>
        <input v-model="userInput" @input="checkInput" v-if="gameStarted" />
        <button @click="startGame">{{ gameStarted ? 'Reset' : 'Start' }}</button>
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
        // 省略

        startGame() {
            this.gameStarted = true;
            this.score = 0;
            this.userInput = '';
            this.pickNewWord();
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
}

.keyboard .active {
    background-color: #f00;
}
</style>
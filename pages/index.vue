<template>
    <div class="container">
        <h1 class="title">Typing Game</h1>
        <p>CurrentWord : {{ currentWord }}</p>
        <p class="score">Score : {{ score }}</p>
        <!-- <input class="userInput" v-model="userInput.value" @input="checkInput" ref="userInput" /> -->
        <input class="userInput" v-model="userInput" @input="checkInput" ref="userInputRef">
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
import { ref, reactive, onMounted, onBeforeUnmount } from 'vue';
import words from '@/data/words.js';

export default {
    setup() {
        const currentWord = ref('');
        const userInput = ref('');
        const userInputRef = ref(null);
        const score = ref(0);
        const gameStarted = ref(false);
        const keyboard = reactive([
            ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p'],
            ['a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l'],
            ['z', 'x', 'c', 'v', 'b', 'n', 'm']
        ].map(row => row.map(letter => ({ letter, active: false }))));

        const startGame = () => {
            if (gameStarted.value) {
                // Reset the game
                currentWord.value = '';
                userInput.value = '';
                score.value = 0;
                keyboard.forEach(row => row.forEach(key => key.active = false));
                gameStarted.value = false; // Stop the game
            } else {
                // Start the game
                gameStarted.value = true;
                score.value = 0;
                userInput.value = '';
                // Set focus to userInput
                nextTick(() => {
                    userInputRef.value.focus();
                });
                pickNewWord();
            }
        };

        const pickNewWord = () => {
            const randomIndex = Math.floor(Math.random() * words.length);
            currentWord.value = words[randomIndex];
        };

        const checkInput = () => {
            if (userInput.value === currentWord.value) {
                score.value++;
                userInput.value = '';
                pickNewWord();
            }
            keyboard.forEach(key => {
                key.active = userInput.value.includes(key.letter);
            });
        };

        const keydownHandler = (e) => {
            const key = keyboard.flat().find(key => key.letter.toLowerCase() === e.key.toLowerCase());
            if (key) key.active = true;
        };

        const keyupHandler = (e) => {
            const key = keyboard.flat().find(key => key.letter.toLowerCase() === e.key.toLowerCase());
            if (key) key.active = false;
        };

        onMounted(() => {
            window.addEventListener('keydown', keydownHandler);
            window.addEventListener('keyup', keyupHandler);
        });

        onBeforeUnmount(() => {
            window.removeEventListener('keydown', keydownHandler);
            window.removeEventListener('keyup', keyupHandler);
        });

        return {
            currentWord,
            userInput,
            score,
            gameStarted,
            keyboard,
            startGame,
            checkInput,
            userInputRef
        };
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

.title {
    color: #99f;
}

.score {
    color: #99f;
    font-weight: bold;
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
    padding: 5px;
    margin: 5px;
    border: 1px solid #eee;
    border-radius: 8px;
}

.keyboard div div {
    display: block;
    width: 20px;
    text-align: center;
    padding: 10px;
    margin: 5px;
    border-radius: 10px;
    color: #fff;
    background-color: #ccc;
}

.keyboard .active {
    background-color: #99f;
}
</style>

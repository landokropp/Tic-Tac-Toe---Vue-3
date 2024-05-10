<template>
    <div class="container">

    <h1>{{ props.msg }}</h1>
        <p>Aktueller Spieler: <span class="">{{ currentPlayer }}</span></p>


    <div class="board">
        <div 
            class="row"
            v-for="(row, rowIndex) in board"
            :key="rowIndex"
        >

        <div
            class="cell"
            v-for="(cell, cellIndex) in row"
            :key="cellIndex"
            :class="{ 'cell-x': cell === 'X', 'cell-o': cell === 'O' }" :disabled="cell !== null"
            @click="makeMove(rowIndex, cellIndex)"

        >{{ cell }}</div>
        </div>
    </div>

        <div class="">
            <p v-if="winner">{{ winner }} wins!</p>
            <p v-else-if="isTie">It's a tie!</p>
            <button @click="resetGame">Reset Game</button>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface Props {
    msg?: string
    placeholder?: string
}

const props = defineProps<Props>();

const currentPlayer = ref('X');
const winner = ref<string | null>(null);
const gameOver = ref<boolean>(false);
const isTie = ref<boolean>(false);

const board = ref([
    ['', '', ''],
    ['', '', ''],
    ['', '', ''],
])


function makeMove(row: number, col: number){

    if (board.value[row][col]){
        return
    }

    if(!board.value[row][col] || !winner.value){
        board.value[row][col] = currentPlayer.value;

        if (checkWinner()){
            winner.value = currentPlayer.value;
        } else if (checkTie()){
            isTie.value = true;
        } else {
            // Weiter spielen, bsi einer gewonnen hat
            currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X';
        }
    }
    console.log(row, col, currentPlayer.value);
}

function checkTie(){
    for (let i = 0; i < 3; i++){
        for (let j = 0; j < 3; j++){
            if(!board.value[i][j]){
                return false;
            }
        }
    }
    return true;
}

function checkWinner(){
    const a = currentPlayer.value;

    for (let i= 0; i < 3; i++){
        if (board.value[i].every(cell => cell === a)){
            return true;
        }

        if (board.value.every(row => row[i] === a)){
            return true;
        }

        // check diagonals
        if (board.value[0][0] === a && board.value[1][1] === a && board.value[2][2] === a){
            return true;
        }

        if (board.value[0][2] === a && board.value[1][1] === a && board.value[2][0] === a){
            return true;
        }
    }

    // return false only after checking all cases
    return false;
}

function resetGame(){
    board.value = [
        ['', '', ''],
        ['', '', ''],
        ['', '', '']
    ]

    currentPlayer.value = 'X';
    gameOver.value = false;
    winner.value = null;
    isTie.value = false;
}



</script>


<style scoped>

.container{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

.board {
    width: 309px;
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    margin: 20px 0; /* Fügt etwas Abstand zwischen der Überschrift und dem Board hinzu */
}

.cell {
    width: 100px;
    height: 100px;
    border-radius: 10px;
    margin: 3px;
    align-items: center;
    display: flex;
    justify-content: center;
    cursor: pointer;
    font-size: 40px;
    background-color: #48cae4;
}
</style>
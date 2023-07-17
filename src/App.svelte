<script>
/**
* @typedef {'X'|'O'|' '} CellValue
* @typedef {{value: CellValue, row: number, column: number}} Cell
*/

const BoardSize = 3;
const Empty= ' ';
const X = 'X';
const O = 'O';
const NoWinner = 'NoWinner';


/**
* @type {Cell[][]} board
*/
let board = [];
for(let i = 0; i < BoardSize; ++i) {
    board.push(new Array(3));
    for(let j = 0; j < BoardSize; ++j) {
        board[i][j] = {
            value: Empty,
            row: i,
            column: j,
        };
    }
}

/**
* @type {CellValue} nextSymbol
*/
let nextSymbol = X;

/**
* @param {Cell} cell
*/
function updateCell(cell) {
    const { row, column } = cell;
    if(cell.value !== Empty) {
        return;
    }
    cell.value = nextSymbol;
    if(nextSymbol === X) {
        nextSymbol = O;
    } else {
        nextSymbol = X;
    }
    board[row][column] = cell;
}

function resetBoard() {
    board = board.map(row => row.map(cell => ({...cell, value: Empty})));
    winner = undefined;
}

/**
* @param {CellValue} symbol 
* @returns {boolean}
*/
function validateIfWinner(symbol) {
    /**
    * @param {Cell[]} array
    */
    const isArrayFull = array => array.reduce(
            /**
            * @param {boolean} carry
            * @param {Cell} cell
            */
            (carry, cell) => carry && cell.value === symbol, true
            );
    const isRowOrColumnFull = board.filter((row, index) => {
        const column = board.map(row => row[index]);
        return isArrayFull(row) || isArrayFull(column);
    }).length > 0;
    const diagonalTopLeft = board.map((row, index) => row[index]);
    const diagonalTopRight = board.map((row, index) => row[BoardSize - 1 - index]);
    console.log({diagonalTopRight, diagonalTopLeft});
    return isRowOrColumnFull || isArrayFull(diagonalTopLeft) || isArrayFull(diagonalTopRight);
}

let winner = undefined;
$: {
    if(validateIfWinner(X)) {
        winner = X;
    } else if(validateIfWinner(O)) {
        winner = O;
    } else if(board.every(row => row.every(cell => cell.value !== Empty))) {
        winner = NoWinner;
    }
}

</script>

<main>
<div class="board">
    {#each board as row}
        <div class="row">
            {#each row as cell}
                    <button style="cursor: {!!winner ? 'none' : 'pointer'};" class="cell" on:click={() => !winner && updateCell(cell)}>
                        {cell.value}
                    </button>
            {/each}
        </div>
    {/each}
</div>
{#if winner !== undefined}
    <button on:click={resetBoard}>Restart</button>
{/if}
</main>

<style>
    .row {
        display: flex;
        flex-direction: row;
        column-gap: 3px;
    }

    .board {
        display: flex;
        flex-direction: column;
        row-gap: 3px;
    }

    .cell {
        font-size: 20px;
        font-weight: bold;
        width: 50px;
        height: 50px;
        color: white;
        background-color: blue;
        border: 4px blueviolet solid;
    }
</style>

<script lang="ts">
    const boardWidth = 10;
    const boardHeight = 10;

    let snake: number[][];
    let direction: [number, number];
    let food: [number, number];
    let intervalID: number | undefined;
    // let gameOver = false;
    // let score = 0;

    // interval between each update in [ms]
    let intervalTimeout = 150;

    function setup() {
        // score = 0;
        snake = [
            [2, 0],
            [1, 0],
            [0, 0],
        ];
        direction = [1, 0];
        food = pickRandomFreePosition();
        // gameOver = false;
    }

    setup();

    function update() {
        const [headX, headY] = snake[0];
        const [directionX, directionY] = direction;

        // calculate next head position
        const [nextX, nextY] = [headX + directionX, headY + directionY];

        if (
            nextX < 0 ||
            nextX >= boardWidth ||
            nextY < 0 ||
            nextY >= boardHeight ||
            snake.find(
                ([snakeX, snakeY]) => snakeX === nextX && snakeY === nextY,
            )
        ) {
            // stop update interval
            clearInterval(intervalID);
            console.log("game over");
            return;
        }

        snake.unshift([nextX, nextY]);

        // check if collecting food
        if (nextX === food[0] && nextY === food[1]) {
            // score += 1;
            food = pickRandomFreePosition();
        } else {
            // remove tail to keep the same snake length if food was not collected
            snake.pop();
        }

        // update snake to trigger reactivity (rendering)
        snake = snake;
        console.log("update");
    }

    function handleKeydown(e: KeyboardEvent) {
        switch (e.key) {
            case "ArrowUp":
            case "w":
                if (snake[0][1] - snake[1][1] !== 1) direction = [0, -1];
                break;
            case "ArrowDown":
            case "s":
                if (snake[0][1] - snake[1][1] !== -1) direction = [0, 1];
                break;
            case "ArrowLeft":
            case "a":
                if (snake[0][0] - snake[1][0] !== 1) direction = [-1, 0];
                break;
            case "ArrowRight":
            case "d":
                if (snake[0][0] - snake[1][0] !== -1) direction = [1, 0];
                break;
        }
    }

    function pickRandomFreePosition(): [number, number] {
        let randomX: number, randomY: number;
        do {
            [randomX, randomY] = [
                Math.floor(Math.random() * boardWidth),
                Math.floor(Math.random() * boardHeight),
            ];
        } while (
            snake.find(
                ([snakeX, snakeY]) => snakeX === randomX && snakeY === randomY,
            )
        );
        return [randomX, randomY];
    }

    intervalID = setInterval(update, intervalTimeout);
</script>

<!-- globally capture keydown events -->
<svelte:window on:keydown={handleKeydown} />

<main>
    {#each Array(boardHeight).fill(0) as _, y}
        <div class="row">
            {#each Array(boardWidth).fill(0) as _, x}
                <!-- [{x}, {y}] -->
                <div
                    class="cell
                {snake.find(([sx, sy]) => sx === x && sy === y) ? 'snake' : ''}
                {food[0] === x && food[1] === y ? 'food' : ''} 
                "
                ></div>
            {/each}
        </div>
    {/each}
</main>

<style>
    .row {
        display: flex;
    }
    .cell {
        width: 40px;
        height: 40px;
        border: 2px solid gray;
        border-radius: 15%;
        background-color: greenyellow;
        margin: 1px;
    }
    .snake {
        background-color: slateblue;
    }
    .food {
        background-color: red;
    }
</style>
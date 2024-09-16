
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game for Aya Mohamed</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin: 0;
            padding: 0;
            background-color: #f4f4f4;
        }

        canvas {
            border: 2px solid black;
            display: block;
            margin: 20px auto;
            background-color: #eee;
        }

        .controls {
            display: flex;
            justify-content: center;
            margin-top: 20px;
            flex-wrap: wrap;
        }

        .controls button {
            padding: 15px 20px;
            font-size: 18px;
            margin: 5px;
            border-radius: 10px;
            background-color: #0073e6;
            color: white;
            border: none;
            cursor: pointer;
        }

        .controls button:active {
            background-color: #005bb5;
        }

        h1 {
            color: #0073e6;
        }

        .game-over {
            font-size: 24px;
            color: red;
            margin-top: 20px;
        }

        @media (max-width: 768px) {
            .controls button {
                padding: 12px 15px;
                font-size: 16px;
            }
        }
    </style>
</head>
<body>

    <h1>Snake Game for Aya Mohamed</h1>
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <p id="score">Score: 0</p>

    <div class="controls">
        <button id="startBtn" onclick="startGame()">Start</button>
        <button id="resetBtn" onclick="resetGame()">Reset</button>
        <button id="stopBtn" onclick="stopGame()">Stop</button>
    </div>

    <div id="gameOverMessage" class="game-over" style="display: none;">Game Over! Press Reset to play again.</div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const boxSize = 20; // حجم المربعات
        let snake = [{ x: 9 * boxSize, y: 10 * boxSize }];
        let direction = "RIGHT";
        let food = {
            x: Math.floor(Math.random() * 19 + 1) * boxSize,
            y: Math.floor(Math.random() * 19 + 1) * boxSize
        };
        let score = 0;
        let gameInterval = null;
        let gameOver = false;

        document.addEventListener("keydown", changeDirection);

        // اضافة الدعم للماوس والتاتش
        canvas.addEventListener("mousedown", handleMouseOrTouch);
        canvas.addEventListener("touchstart", handleMouseOrTouch);

        function handleMouseOrTouch(event) {
            const rect = canvas.getBoundingClientRect();
            const x = (event.touches ? event.touches[0].clientX : event.clientX) - rect.left;
            const y = (event.touches ? event.touches[0].clientY : event.clientY) - rect.top;

            const snakeHead = snake[0];

            if (x < snakeHead.x) {
                direction = "LEFT";
            } else if (x > snakeHead.x + boxSize) {
                direction = "RIGHT";
            } else if (y < snakeHead.y) {
                direction = "UP";
            } else if (y > snakeHead.y + boxSize) {
                direction = "DOWN";
            }
        }

        function changeDirection(event) {
            if (typeof event === "string") {
                direction = event;
            } else {
                if (event.keyCode === 37 && direction !== "RIGHT") direction = "LEFT";
                else if (event.keyCode === 38 && direction !== "DOWN") direction = "UP";
                else if (event.keyCode === 39 && direction !== "LEFT") direction = "RIGHT";
                else if (event.keyCode === 40 && direction !== "UP") direction = "DOWN";
            }
        }

        function drawGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // رسم الثعبان
            for (let i = 0; i < snake.length; i++) {
                ctx.fillStyle = i === 0 ? "green" : "lime";
                ctx.fillRect(snake[i].x, snake[i].y, boxSize, boxSize);
                ctx.strokeStyle = "darkgreen";
                ctx.strokeRect(snake[i].x, snake[i].y, boxSize, boxSize);
            }

            // رسم الطعام
            ctx.fillStyle = "red";
            ctx.fillRect(food.x, food.y, boxSize, boxSize);

            // تحريك الثعبان
            let snakeX = snake[0].x;
            let snakeY = snake[0].y;

            if (direction === "LEFT") snakeX -= boxSize;
            if (direction === "UP") snakeY -= boxSize;
            if (direction === "RIGHT") snakeX += boxSize;
            if (direction === "DOWN") snakeY += boxSize;

            // التحقق من تصادم الثعبان مع الطعام
            if (snakeX === food.x && snakeY === food.y) {
                score++;
                document.getElementById("score").textContent = "Score: " + score;
                food = {
                    x: Math.floor(Math.random() * 19 + 1) * boxSize,
                    y: Math.floor(Math.random() * 19 + 1) * boxSize
                };
            } else {
                snake.pop(); // حذف الذيل إذا لم يأكل الثعبان
            }

            const newHead = { x: snakeX, y: snakeY };

            // التحقق من التصادم مع الجدران أو الجسم نفسه
            if (snakeX < 0 || snakeX >= canvas.width || snakeY < 0 || snakeY >= canvas.height || collision(newHead, snake)) {
                stopGame();
                document.getElementById("gameOverMessage").style.display = "block";
                gameOver = true;
                return;
            }

            snake.unshift(newHead);
        }

        function collision(head, array) {
            for (let i = 0; i < array.length; i++) {
                if (head.x === array[i].x && head.y === array[i].y) {
                    return true;
                }
            }
            return false;
        }

        function startGame() {
            if (gameInterval || gameOver) return; // إذا كانت اللعبة بدأت بالفعل، لا تقم بشيء
            gameInterval = setInterval(drawGame, 100);
            document.getElementById("gameOverMessage").style.display = "none";
        }

        function stopGame() {
            clearInterval(gameInterval);
            gameInterval = null; // إعادة تعيين المتغير
        }

        function resetGame() {
            stopGame();
            score = 0;
            document.getElementById("score").textContent = "Score: 0";
            snake = [{ x: 9 * boxSize, y: 10 * boxSize }];
            direction = "RIGHT";
            food = {
                x: Math.floor(Math.random() * 19 + 1) * boxSize,
                y: Math.floor(Math.random() * 19 + 1) * boxSize
            };
            gameOver = false;
            startGame();
        }
    </script>
</body>
</html>


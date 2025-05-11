
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>五子棋 (Gomoku)</title>
    <style>
        canvas {
            background-color: #f0d9b5;
            display: block;
            margin: 20px auto;
        }
        button {
            display: block;
            margin: 10px auto;
            padding: 10px 20px;
            font-size: 16px;
        }
    </style>
</head>
<body>
    <canvas id="board" width="480" height="480"></canvas>
    <button onclick="window.open('https://www.google.com')">Go to Google</button>

    <script>
        const canvas = document.getElementById('board');
        const ctx = canvas.getContext('2d');
        const SIZE = 15;
        const CELL = canvas.width / SIZE;
        const board = Array.from({ length: SIZE }, () => Array(SIZE).fill(0));
        let playerTurn = true;

        function drawBoard() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            for (let i = 0; i < SIZE; i++) {
                ctx.beginPath();
                ctx.moveTo(CELL / 2, CELL / 2 + i * CELL);
                ctx.lineTo(canvas.width - CELL / 2, CELL / 2 + i * CELL);
                ctx.stroke();
                ctx.beginPath();
                ctx.moveTo(CELL / 2 + i * CELL, CELL / 2);
                ctx.lineTo(CELL / 2 + i * CELL, canvas.height - CELL / 2);
                ctx.stroke();
            }

            for (let y = 0; y < SIZE; y++) {
                for (let x = 0; x < SIZE; x++) {
                    if (board[y][x] === 1) drawStone(x, y, 'black');
                    else if (board[y][x] === 2) drawStone(x, y, 'white');
                }
            }
        }

        function drawStone(x, y, color) {
            ctx.beginPath();
            ctx.arc(CELL / 2 + x * CELL, CELL / 2 + y * CELL, CELL / 2.5, 0, Math.PI * 2);
            ctx.fillStyle = color;
            ctx.fill();
        }

        function checkWin(player) {
            const directions = [[1,0],[0,1],[1,1],[1,-1]];
            for (let y = 0; y < SIZE; y++) {
                for (let x = 0; x < SIZE; x++) {
                    if (board[y][x] !== player) continue;
                    for (let [dx, dy] of directions) {
                        let count = 1;
                        for (let i = 1; i < 5; i++) {
                            const nx = x + dx * i;
                            const ny = y + dy * i;
                            if (nx < 0 || ny < 0 || nx >= SIZE || ny >= SIZE) break;
                            if (board[ny][nx] === player) count++;
                            else break;
                        }
                        if (count === 5) return true;
                    }
                }
            }
            return false;
        }

        canvas.addEventListener('click', (e) => {
            if (!playerTurn) return;
            const rect = canvas.getBoundingClientRect();
            const x = Math.floor((e.clientX - rect.left) / CELL);
            const y = Math.floor((e.clientY - rect.top) / CELL);
            if (board[y][x] === 0) {
                board[y][x] = 1;
                drawBoard();
                if (checkWin(1)) {
                    alert("玩家獲勝！");
                    return;
                }
                playerTurn = false;
                setTimeout(aiMove, 10000); // AI 延遲 10 秒模擬思考
            }
        });

        function aiMove() {
            // 簡單的隨機落子 AI
            for (let y = 0; y < SIZE; y++) {
                for (let x = 0; x < SIZE; x++) {
                    if (board[y][x] === 0) {
                        board[y][x] = 2;
                        drawBoard();
                        if (checkWin(2)) {
                            alert("AI 獲勝！");
                        }
                        playerTurn = true;
                        return;
                    }
                }
            }
        }

        drawBoard();
    </script>
</body>
</html>

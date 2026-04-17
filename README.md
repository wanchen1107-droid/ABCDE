# ABCDE
小恐龍遊戲
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>變速小恐龍遊戲</title>
    <style>
        /* CSS 樣式直接整合 */
        body {
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f0f0f0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            overflow: hidden;
        }
        #game-container {
            position: relative;
            text-align: center;
        }
        canvas {
            background-color: #ffffff;
            border-bottom: 3px solid #535353;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }
        .info {
            margin-top: 15px;
            color: #535353;
            font-weight: bold;
        }
        #game-over-msg {
            position: absolute;
            top: 40%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: none; /* 初始隱藏 */
            background: rgba(255, 255, 255, 0.8);
            padding: 20px;
            border-radius: 10px;
        }
    </style>
</head>
<body>

    <div id="game-container">
        <canvas id="gameCanvas"></canvas>
        <div id="game-over-msg">
            <h2>GAME OVER</h2>
            <p>按「空白鍵」快速重新開始</p>
        </div>
        <div class="info">空白鍵：跳躍 / 重啟遊戲</div>
    </div>

    <script>
        // JavaScript 邏輯整合
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const gameOverMsg = document.getElementById('game-over-msg');

        canvas.width = 800;
        canvas.height = 200;

        // 遊戲狀態與參數
        let dino, obstacles, score, gameActive, frame, baseSpeed;

        function init() {
            dino = {
                x: 50,
                y: 150,
                width: 40,
                height: 40,
                dy: 0,
                jumpForce: 12,
                gravity: 0.6,
                grounded: false
            };
            obstacles = [];
            score = 0;
            frame = 0;
            baseSpeed = 6;
            gameActive = true;
            gameOverMsg.style.display = 'none';
            animate();
        }

        function spawnObstacle() {
            // 隨機障礙物寬度
            let width = 20 + Math.random() * 30;
            obstacles.push({
                x: canvas.width,
                y: 160,
                width: width,
                height: 40
            });
        }

        function animate() {
            if (!gameActive) {
                gameOverMsg.style.display = 'block';
                return;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);
            frame++;

            // --- 核心邏輯：忽快忽慢的速度變化 ---
            // 利用 Sin 函數讓速度在 (baseSpeed - 3) 到 (baseSpeed + 3) 之間震盪
            let currentSpeed = baseSpeed + Math.sin(frame * 0.03) * 3;

            // 恐龍跳躍重力感
            if (!dino.grounded) {
                dino.dy += dino.gravity;
                dino.y += dino.dy;
            }

            if (dino.y > 150) {
                dino.y = 150;
                dino.dy = 0;
                dino.grounded = true;
            }

            // 繪製恐龍 (簡單矩形代表)
            ctx.fillStyle = "#535353";
            ctx.fillRect(dino.x, dino.y, dino.width, dino.height);

            // 每隔一段時間產生障礙物
            if (frame % 80 === 0) spawnObstacle();

            // 更新與繪製障礙物
            for (let i = obstacles.length - 1; i >= 0; i--) {
                let o = obstacles[i];
                o.x -= currentSpeed;

                ctx.fillStyle = "#ff4d4d";
                ctx.fillRect(o.x, o.y, o.width, o.height);

                // 碰撞偵測
                if (dino.x < o.x + o.width &&
                    dino.x + dino.width > o.x &&
                    dino.y < o.y + o.height &&
                    dino.y + dino.height > o.y) {
                    gameActive = false;
                }

                // 移除超出螢幕的障礙物並加分
                if (o.x + o.width < 0) {
                    obstacles.splice(i, 1);
                    score++;
                }
            }

            // 顯示分數與速度 UI
            ctx.fillStyle = "#535353";
            ctx.font = "20px Arial";
            ctx.fillText(`分數: ${score}`, 20, 30);
            
            // 速度指示條 (讓玩家知道現在快還是慢)
            ctx.fillText(`當前速度: ${currentSpeed.toFixed(1)}`, 650, 30);

            requestAnimationFrame(animate);
        }

        // 鍵盤事件控制
        window.addEventListener('keydown', (e) => {
            if (e.code === 'Space') {
                if (gameActive) {
                    if (dino.grounded) {
                        dino.dy = -dino.jumpForce;
                        dino.grounded = false;
                    }
                } else {
                    // 如果遊戲結束，按下空白鍵立即重置
                    init();
                }
            }
        });

        // 啟動遊戲
        init();
    </script>
</body>
</html>

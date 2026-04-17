# ABCDE
小恐龍遊戲
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <title>變速小恐龍</title>
    <style>
        body { text-align: center; font-family: Arial, sans-serif; overflow: hidden; }
        canvas { background: #f7f7f7; border-bottom: 2px solid #535353; margin-top: 50px; }
    </style>
</head>
<body>
    <h1>按空白鍵跳躍</h1>
    <canvas id="gameCanvas" width="800" height="200"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        let score = 0;
        let gameSpeed = 5;
        let frame = 0;

        const dino = { x: 50, y: 150, w: 40, h: 40, dy: 0, jumpForce: 12, grounded: false };
        const obstacles = [];

        function drawDino() {
            ctx.fillStyle = '#535353';
            ctx.fillRect(dino.x, dino.y, dino.w, dino.h);
        }

        function update() {
            frame++;
            
            // --- 核心邏輯：忽快忽慢的速度變化 ---
            // 使用正弦函數 (Math.sin) 讓速度在 3 到 12 之間循環波動
            gameSpeed = 7 + Math.sin(frame * 0.02) * 4; 

            // 重力與跳躍
            if (!dino.grounded) {
                dino.dy += 0.6;
                dino.y += dino.dy;
            }
            if (dino.y >= 150) {
                dino.y = 150;
                dino.grounded = true;
            }

            // 障礙物生成 (頻率隨速度微調)
            if (frame % Math.floor(1000 / gameSpeed) === 0) {
                obstacles.push({ x: 800, w: 20, h: 40 });
            }

            // 障礙物移動與碰撞
            for (let i = obstacles.length - 1; i >= 0; i--) {
                obstacles[i].x -= gameSpeed;
                ctx.fillStyle = 'red';
                ctx.fillRect(obstacles[i].x, 150, obstacles[i].w, obstacles[i].h);

                // 碰撞檢測
                if (dino.x < obstacles[i].x + obstacles[i].w &&
                    dino.x + dino.w > obstacles[i].x &&
                    dino.y < 150 + obstacles[i].h &&
                    dino.y + dino.h > 150) {
                    alert("遊戲結束！得分：" + Math.floor(score));
                    document.location.reload();
                }

                if (obstacles[i].x < -20) obstacles.splice(i, 1);
            }
            score += gameSpeed / 10;
        }

        function loop() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            update();
            drawDino();
            ctx.fillStyle = "black";
            ctx.fillText("當前速度: " + gameSpeed.toFixed(2), 10, 20);
            ctx.fillText("得分: " + Math.floor(score), 10, 40);
            requestAnimationFrame(loop);
        }

        window.addEventListener('keydown', e => {
            if (e.code === 'Space' && dino.grounded) {
                dino.dy = -dino.jumpForce;
                dino.grounded = false;
            }
        });

        loop();
    </script>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML Shooter Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
        }
        canvas {
            display: block;
            background: #000;
        }
        #controls {
            position: fixed;
            bottom: 10px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 20px;
        }
        button {
            padding: 10px 20px;
            font-size: 18px;
            border: none;
            border-radius: 5px;
            background-color: #444;
            color: white;
            cursor: pointer;
        }
        button:active {
            background-color: #888;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <div id="controls">
        <button id="leftBtn">◀️</button>
        <button id="shootBtn">🔥</button>
        <button id="rightBtn">▶️</button>
    </div>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Set canvas size
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        const ship = {
            x: canvas.width / 2 - 25,
            y: canvas.height - 60,
            width: 50,
            height: 50,
            color: 'white',
            speed: 10
        };

        const bullets = [];
        const enemies = [];
        const enemySpeed = 2;

        let score = 0;

        function drawShip() {
            ctx.fillStyle = ship.color;
            ctx.fillRect(ship.x, ship.y, ship.width, ship.height);
        }

        function drawBullet(bullet) {
            ctx.fillStyle = 'yellow';
            ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
        }

        function drawEnemy(enemy) {
            ctx.fillStyle = 'red';
            ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
        }

        function spawnEnemy() {
            const x = Math.random() * (canvas.width - 40);
            enemies.push({ x, y: 0, width: 40, height: 40 });
        }

        function update() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw player ship
            drawShip();

            // Update bullets
            bullets.forEach((bullet, index) => {
                bullet.y -= 5;
                if (bullet.y < 0) bullets.splice(index, 1);
                drawBullet(bullet);
            });

            // Update enemies
            enemies.forEach((enemy, eIndex) => {
                enemy.y += enemySpeed;
                if (enemy.y > canvas.height) enemies.splice(eIndex, 1);

                // Check collision with ship
                if (
                    enemy.x < ship.x + ship.width &&
                    enemy.x + enemy.width > ship.x &&
                    enemy.y < ship.y + ship.height &&
                    enemy.y + enemy.height > ship.y
                ) {
                    alert('Game Over! Your score: ' + score);
                    document.location.reload();
                }

                // Check collision with bullets
                bullets.forEach((bullet, bIndex) => {
                    if (
                        bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y
                    ) {
                        enemies.splice(eIndex, 1);
                        bullets.splice(bIndex, 1);
                        score++;
                    }
                });

                drawEnemy(enemy);
            });

            requestAnimationFrame(update);
        }

        function shoot() {
            bullets.push({
                x: ship.x + ship.width / 2 - 5,
                y: ship.y,
                width: 10,
                height: 20
            });
        }

        // Mobile controls
        document.getElementById('leftBtn').addEventListener('click', () => {
            ship.x -= ship.speed;
            if (ship.x < 0) ship.x = 0;
        });

        document.getElementById('rightBtn').addEventListener('click', () => {
            ship.x += ship.speed;
            if (ship.x + ship.width > canvas.width) ship.x = canvas.width - ship.width;
        });

        document.getElementById('shootBtn').addEventListener('click', shoot);

        // Spawn enemies every 2 seconds
        setInterval(spawnEnemy, 2000);

        // Start game
        update();
    </script>
</body>
</html>

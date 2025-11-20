<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Moving Thing Game</title>
<style>
    body {
        margin: 0;
        overflow: hidden;
        background: #111;
        font-family: sans-serif;
        text-align: center;
        color: white;
    }
    #gameCanvas {
        background: #222;
        display: block;
        margin: 0 auto;
    }
</style>
</head>
<body>
<h2>Moving Game</h2>
<canvas id="gameCanvas" width="400" height="600"></canvas>

<script>
// Canvas
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

// Player
const player = {
    x: 180,
    y: 550,
    width: 40,
    height: 40,
    speed: 5
};

// Obstacles
let obstacles = [];
let score = 0;
let gameOver = false;

// Controls
let left = false;
let right = false;

document.addEventListener("keydown", e => {
    if (e.key === "ArrowLeft") left = true;
    if (e.key === "ArrowRight") right = true;
});
document.addEventListener("keyup", e => {
    if (e.key === "ArrowLeft") left = false;
    if (e.key === "ArrowRight") right = false;
});

function spawnObstacle() {
    const size = 40;
    const x = Math.floor(Math.random() * (canvas.width - size));
    obstacles.push({ x, y: -size, size });
}

setInterval(spawnObstacle, 1000);

function update() {
    if (gameOver) return;

    // Move player
    if (left && player.x > 0) player.x -= player.speed;
    if (right && player.x < canvas.width - player.width) player.x += player.speed;

    // Move obstacles
    obstacles.forEach(o => o.y += 4);

    // Collision detection
    obstacles.forEach(o => {
        if (
            o.x < player.x + player.width &&
            o.x + o.size > player.x &&
            o.y < player.y + player.height &&
            o.y + o.size > player.y
        ) {
            gameOver = true;
        }
    });

    // Remove off-screen obstacles
    obstacles = obstacles.filter(o => o.y < canvas.height);

    score++;
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw player
    ctx.fillStyle = "cyan";
    ctx.fillRect(player.x, player.y, player.width, player.height);

    // Draw obstacles
    ctx.fillStyle = "red";
    obstacles.forEach(o =>
        ctx.fillRect(o.x, o.y, o.size, o.size)
    );

    // Draw score
    ctx.fillStyle = "white";
    ctx.font = "20px Arial";
    ctx.fillText("Score: " + score, 10, 30);

    if (gameOver) {
        ctx.fillStyle = "yellow";
        ctx.font = "40px Arial";
        ctx.fillText("GAME OVER", 80, 300);
    }
}

function gameLoop() {
    update();
    draw();
    requestAnimationFrame(gameLoop);
}
gameLoop();
</script>
</body>
</html>

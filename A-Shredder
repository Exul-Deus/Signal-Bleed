<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Asteroid Shredder: Code Zero</title>
    <!-- Load Tailwind CSS for basic layout and responsive helpers -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom font for a sci-fi feel */
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap');
        body {
            font-family: 'Orbitron', sans-serif;
            background-color: #0d1117; /* Dark space background */
            color: #ffffff;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
            padding: 10px;
        }
        #gameArea {
            /* Neon glow border effect */
            box-shadow: 0 0 40px rgba(0, 255, 255, 0.5), 0 0 10px rgba(255, 255, 255, 0.8);
            border: 2px solid #00ffff;
            background-color: #00000a; /* Inner game background */
            max-width: 95vw;
            width: 400px; /* Max width for desktop viewing */
            max-height: 80vh;
            aspect-ratio: 1 / 1.5; /* Prioritize height for mobile view */
            border-radius: 12px;
            overflow: hidden;
            margin-bottom: 20px;
        }
        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
        .control-panel {
            display: flex;
            justify-content: space-around;
            width: 90%;
            max-width: 400px;
            padding: 10px 0;
        }
        /* Mobile Control Buttons Styling */
        .control-button {
            background: #00ffff;
            color: #000000;
            border: none;
            padding: 12px 24px;
            font-weight: bold;
            border-radius: 9999px;
            box-shadow: 0 0 10px #00ffff, inset 0 0 5px #ffffff;
            transition: transform 0.1s;
            touch-action: manipulation; /* Improves touch responsiveness */
            flex-grow: 1;
            margin: 0 5px;
            font-size: 1.1rem;
            cursor: pointer;
        }
        .control-button:active {
            transform: scale(0.95);
            box-shadow: 0 0 5px #00ffff, inset 0 0 2px #ffffff;
        }
        #fire-button {
            background: #ff00ff; /* Different color for fire button */
            box-shadow: 0 0 10px #ff00ff, inset 0 0 5px #ffffff;
        }
        #fire-button:active {
            box-shadow: 0 0 5px #ff00ff, inset 0 0 2px #ffffff;
        }
        #info-panel {
            position: absolute;
            top: 10px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            gap: 20px;
            font-size: 1.2rem;
            color: #00ffff;
            text-shadow: 0 0 5px #00ffff;
        }
        /* Custom message box for Game Over/Start instead of alert() */
        .message-box {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.9);
            border: 3px solid #ff00ff;
            padding: 40px;
            border-radius: 15px;
            text-align: center;
            z-index: 1000;
            box-shadow: 0 0 30px #ff00ff;
            color: #00ffff;
            max-width: 80%;
        }
        .message-box button {
            margin-top: 20px;
            padding: 10px 25px;
            background-color: #ff00ff;
            color: #000000;
            border: none;
            border-radius: 8px;
            font-size: 1.1rem;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.2s;
        }
        .message-box button:hover {
            background-color: #ff33ff;
        }
    </style>
</head>
<body>

    <div id="info-panel">
        <div id="score-display">Score: 0</div>
        <div id="lives-display">Lives: 3</div>
    </div>
    
    <div id="gameArea">
        <canvas id="gameCanvas"></canvas>
    </div>
    
    <!-- Mobile Controls -->
    <div class="control-panel">
        <button id="left-button" class="control-button">← Left</button>
        <button id="fire-button" class="control-button">Fire!</button>
        <button id="right-button" class="control-button">Right →</button>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreDisplay = document.getElementById('score-display');
        const livesDisplay = document.getElementById('lives-display');
        const apiKey = ""; // API key placeholder as required, unused for client-side game.

        let game;
        let gameLoopId;

        // --- Helper Functions ---

        /**
         * Shows a custom game message box instead of using alert/confirm.
         */
        function showMessage(title, message, buttonText, callback) {
            let msgBox = document.querySelector('.message-box');
            if (!msgBox) {
                msgBox = document.createElement('div');
                msgBox.className = 'message-box';
                document.body.appendChild(msgBox);
            }
            msgBox.innerHTML = `
                <h2 class="text-3xl font-bold mb-4">${title}</h2>
                <p class="text-lg mb-6">${message}</p>
                <button id="message-button">${buttonText}</button>
            `;
            msgBox.style.display = 'block';

            document.getElementById('message-button').onclick = () => {
                msgBox.style.display = 'none';
                if (callback) callback();
            };
        }

        /**
         * Checks for collision between two circular/approximated objects.
         */
        function checkCollision(obj1, obj2) {
            const obj1Radius = obj1.radius || (obj1.width / 2);
            const obj2Radius = obj2.radius || (obj2.width / 2);

            const dx = obj1.x - obj2.x;
            const dy = obj1.y - obj2.y;
            const distance = Math.sqrt(dx * dx + dy * dy);

            return distance < (obj1Radius + obj2Radius);
        }

        // --- Game Object Classes ---

        class Ship {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.width = 30;
                this.height = 40;
                this.speed = 7;
                this.lives = 3;
                this.isInvincible = false;
                this.invincibilityTimer = 0;
                this.maxInvincibilityTime = 120; // 2 seconds of invincibility
            }

            draw() {
                ctx.save();
                ctx.translate(this.x, this.y);

                // Invincibility flash effect: skip drawing every few frames
                if (this.isInvincible && this.invincibilityTimer % 10 < 5) {
                    ctx.restore();
                    return;
                }

                // Main Ship Body (Neon Cyan)
                ctx.fillStyle = '#00ffff';
                ctx.shadowBlur = 15;
                ctx.shadowColor = '#00ffff';
                ctx.beginPath();
                ctx.moveTo(0, -this.height / 2); // Top point
                ctx.lineTo(-this.width / 2, this.height / 2); // Bottom left
                ctx.lineTo(this.width / 2, this.height / 2); // Bottom right
                ctx.closePath();
                ctx.fill();

                // Cockpit/Details (White)
                ctx.fillStyle = '#ffffff';
                ctx.shadowBlur = 5;
                ctx.shadowColor = '#ffffff';
                ctx.beginPath();
                ctx.arc(0, 0, 5, 0, Math.PI * 2);
                ctx.fill();

                // Thrusters (Neon Magenta)
                ctx.fillStyle = '#ff00ff';
                ctx.shadowBlur = 20;
                ctx.shadowColor = '#ff00ff';
                ctx.fillRect(-this.width / 4, this.height / 2 - 5, this.width / 2, 10);

                ctx.restore();
                ctx.shadowBlur = 0; // Reset shadow
            }

            move(direction) {
                if (direction === 'left') {
                    this.x -= this.speed;
                } else if (direction === 'right') {
                    this.x += this.speed;
                }

                // Wrap ship horizontally
                if (this.x < 0) {
                    this.x = canvas.width;
                } else if (this.x > canvas.width) {
                    this.x = 0;
                }
            }

            update() {
                if (this.isInvincible) {
                    this.invincibilityTimer--;
                    if (this.invincibilityTimer <= 0) {
                        this.isInvincible = false;
                    }
                }
            }

            takeDamage() {
                if (!this.isInvincible) {
                    this.lives--;
                    this.isInvincible = true;
                    this.invincibilityTimer = this.maxInvincibilityTime;
                    return true;
                }
                return false;
            }
        }

        class Bullet {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.radius = 3;
                this.speed = 10;
            }

            draw() {
                ctx.fillStyle = '#ff00ff';
                ctx.shadowBlur = 10;
                ctx.shadowColor = '#ff00ff';
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.shadowBlur = 0;
            }

            update() {
                this.y -= this.speed;
                return this.y < 0; // Off-screen check
            }
        }

        class Asteroid {
            constructor(x, y, radius, level) {
                this.x = x;
                this.y = y;
                this.radius = radius;
                this.level = level || 3; // 3 (large), 2 (medium), 1 (small)
                this.speedY = 1 + (3 - this.level) * 0.5;
                this.speedX = (Math.random() - 0.5) * 1; // Slight horizontal drift

                // Define an irregular polygon shape for a rocky look
                this.numPoints = Math.floor(Math.random() * 5) + 6;
                this.points = [];
                const angleStep = (Math.PI * 2) / this.numPoints;
                for (let i = 0; i < this.numPoints; i++) {
                    const angle = i * angleStep;
                    // Randomize the radius slightly
                    const r = this.radius * (0.8 + Math.random() * 0.4);
                    this.points.push({
                        x: r * Math.cos(angle),
                        y: r * Math.sin(angle)
                    });
                }
            }

            draw() {
                ctx.strokeStyle = '#cccccc';
                ctx.lineWidth = 2;
                ctx.shadowBlur = 8;
                ctx.shadowColor = '#888888';
                ctx.beginPath();
                
                // Draw the irregular polygon
                ctx.moveTo(this.x + this.points[0].x, this.y + this.points[0].y);
                for (let i = 1; i < this.numPoints; i++) {
                    ctx.lineTo(this.x + this.points[i].x, this.y + this.points[i].y);
                }
                ctx.closePath();
                ctx.stroke();
                ctx.shadowBlur = 0;
            }

            update() {
                this.x += this.speedX;
                this.y += this.speedY;

                // Wrap horizontal movement
                if (this.x < -this.radius) {
                    this.x = canvas.width + this.radius;
                } else if (this.x > canvas.width + this.radius) {
                    this.x = -this.radius;
                }

                return this.y > canvas.height + this.radius; // Off-screen check
            }
        }


        class AsteroidShooterGame {
            constructor() {
                this.ship = new Ship(canvas.width / 2, canvas.height - 50);
                this.bullets = [];
                this.asteroids = [];
                this.score = 0;
                this.keys = { left: false, right: false, fire: false };
                this.lastAsteroidSpawnTime = 0;
                this.asteroidSpawnInterval = 1000; // Initial spawn rate
                this.gameTime = 0;
                this.gameOver = false;
                this.controlsDisabled = false;
            }

            reset() {
                this.ship = new Ship(canvas.width / 2, canvas.height - 50);
                this.bullets = [];
                this.asteroids = [];
                this.score = 0;
                this.gameTime = 0;
                this.gameOver = false;
                this.asteroidSpawnInterval = 1000;
                this.updateHUD();
            }

            updateHUD() {
                scoreDisplay.textContent = `Score: ${this.score}`;
                livesDisplay.textContent = `Lives: ${this.ship.lives}`;
            }

            spawnAsteroid(level = 3) {
                const radiusMap = { 3: 40, 2: 25, 1: 15 };
                const radius = radiusMap[level];
                const x = Math.random() * (canvas.width - radius * 2) + radius;
                const y = -radius; // Start off screen
                this.asteroids.push(new Asteroid(x, y, radius, level));
            }

            handleInput() {
                if (this.controlsDisabled) return;

                if (this.keys.left) {
                    this.ship.move('left');
                }
                if (this.keys.right) {
                    this.ship.move('right');
                }
                // For desktop keyboard, keys.fire is set on keydown and reset in update
                if (this.keys.fire) {
                    this.bullets.push(new Bullet(this.ship.x, this.ship.y - this.ship.height / 2));
                    // Prevent continuous firing on holding spacebar
                    this.keys.fire = false; 
                }
            }

            update(deltaTime) {
                if (this.gameOver) return;

                this.gameTime += deltaTime;

                // Difficulty scaling: spawn faster every 10 seconds
                // Interval won't go below 200ms
                if (this.gameTime > 10000) {
                    this.asteroidSpawnInterval = Math.max(200, 1000 - Math.floor(this.gameTime / 10000) * 100);
                }

                // Asteroid Spawning Logic
                if (this.gameTime - this.lastAsteroidSpawnTime > this.asteroidSpawnInterval) {
                    // Randomly spawn large (level 3) or medium (level 2) asteroids
                    const level = Math.random() < 0.7 ? 3 : 2;
                    this.spawnAsteroid(level);
                    this.lastAsteroidSpawnTime = this.gameTime;
                }

                // Update Ship and Bullets
                this.ship.update();
                this.bullets = this.bullets.filter(bullet => !bullet.update());

                // Update Asteroids (and filter out ones that went off-screen)
                this.asteroids = this.asteroids.filter(asteroid => !asteroid.update());

                // --- Collision Detection ---

                // 1. Bullet vs Asteroid
                for (let i = this.bullets.length - 1; i >= 0; i--) {
                    for (let j = this.asteroids.length - 1; j >= 0; j--) {
                        const bullet = this.bullets[i];
                        const asteroid = this.asteroids[j];

                        if (checkCollision(bullet, asteroid)) {
                            this.score += 10 * asteroid.level;

                            // Break asteroid into smaller pieces
                            if (asteroid.level > 1) {
                                const newLevel = asteroid.level - 1;
                                this.asteroids.push(new Asteroid(asteroid.x + 10, asteroid.y, asteroid.radius / 1.5, newLevel));
                                this.asteroids.push(new Asteroid(asteroid.x - 10, asteroid.y, asteroid.radius / 1.5, newLevel));
                            }

                            // Remove bullet and current asteroid
                            this.bullets.splice(i, 1);
                            this.asteroids.splice(j, 1);
                            i--; // Decrement i to compensate for removal from bullets array
                            break;
                        }
                    }
                }

                // 2. Ship vs Asteroid
                for (let j = this.asteroids.length - 1; j >= 0; j--) {
                    const asteroid = this.asteroids[j];
                    if (checkCollision(this.ship, asteroid)) {
                        if (this.ship.takeDamage()) {
                            // Ship took damage, reset position and remove asteroid
                            this.ship.x = canvas.width / 2;
                            this.asteroids.splice(j, 1);

                            if (this.ship.lives <= 0) {
                                this.endGame();
                                return;
                            }
                        }
                    }
                }

                this.updateHUD();
            }

            draw() {
                // Clear canvas with the space background color
                ctx.fillStyle = '#00000a';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                // Draw game elements
                this.asteroids.forEach(a => a.draw());
                this.bullets.forEach(b => b.draw());
                this.ship.draw();
            }

            endGame() {
                this.gameOver = true;
                cancelAnimationFrame(gameLoopId);
                this.controlsDisabled = true;

                const finalScore = this.score;
                const msg = `You survived the space rocks and scored **${finalScore}** points! Think you can beat that, Ronin?`;
                showMessage(
                    "GAME OVER, SPACE CADET!",
                    msg,
                    "Play Again",
                    () => {
                        this.controlsDisabled = false;
                        this.reset();
                        lastTime = performance.now();
                        gameLoopId = requestAnimationFrame(animate);
                    }
                );
            }
        }

        // --- Animation Loop & Initialization ---

        let lastTime = 0;
        function animate(currentTime) {
            const deltaTime = currentTime - lastTime;
            lastTime = currentTime;

            if (!game.gameOver) {
                game.handleInput();
                // deltaTime is used to make movement smoother regardless of actual framerate
                game.update(deltaTime);
                game.draw();
            }

            gameLoopId = requestAnimationFrame(animate);
        }

        // --- Setup and Event Listeners ---

        function resizeCanvas() {
            const gameArea = document.getElementById('gameArea');
            // Set canvas dimensions to match the container size
            canvas.width = gameArea.clientWidth;
            canvas.height = gameArea.clientHeight;

            // Reposition ship to center bottom on resize
            if (game && game.ship) {
                game.ship.x = canvas.width / 2;
                game.ship.y = canvas.height - 50;
            }
        }

        window.addEventListener('resize', resizeCanvas);

        // Keyboard Controls (Desktop)
        document.addEventListener('keydown', (e) => {
            if (game && !game.controlsDisabled) {
                if (e.key === 'ArrowLeft' || e.key.toLowerCase() === 'a') {
                    game.keys.left = true;
                } else if (e.key === 'ArrowRight' || e.key.toLowerCase() === 'd') {
                    game.keys.right = true;
                } else if (e.key === ' ') {
                    // Set fire flag for one shot per press
                    game.keys.fire = true;
                }
            }
        });

        document.addEventListener('keyup', (e) => {
            if (game) {
                if (e.key === 'ArrowLeft' || e.key.toLowerCase() === 'a') {
                    game.keys.left = false;
                } else if (e.key === 'ArrowRight' || e.key.toLowerCase() === 'd') {
                    game.keys.right = false;
                }
            }
        });

        // Mobile/Touch Controls Logic
        const leftButton = document.getElementById('left-button');
        const rightButton = document.getElementById('right-button');
        const fireButton = document.getElementById('fire-button');

        function startMove(direction) {
            if (game && !game.controlsDisabled) {
                game.keys[direction] = true;
            }
        }

        function stopMove(direction) {
            if (game) {
                game.keys[direction] = false;
            }
        }

        function handleFire() {
            if (game && !game.controlsDisabled) {
                // For touch/click, we explicitly fire one bullet
                game.bullets.push(new Bullet(game.ship.x, game.ship.y - game.ship.height / 2));
            }
        }

        // Attach listeners for both mouse and touch for movement (Left)
        leftButton.addEventListener('mousedown', () => startMove('left'));
        leftButton.addEventListener('mouseup', () => stopMove('left'));
        leftButton.addEventListener('touchstart', (e) => { e.preventDefault(); startMove('left'); }, { passive: false });
        leftButton.addEventListener('touchend', (e) => { e.preventDefault(); stopMove('left'); }, { passive: false });

        // Attach listeners for both mouse and touch for movement (Right)
        rightButton.addEventListener('mousedown', () => startMove('right'));
        rightButton.addEventListener('mouseup', () => stopMove('right'));
        rightButton.addEventListener('touchstart', (e) => { e.preventDefault(); startMove('right'); }, { passive: false });
        rightButton.addEventListener('touchend', (e) => { e.preventDefault(); stopMove('right'); }, { passive: false });

        // Attach listeners for both mouse and touch for firing (Tap/Click to fire)
        fireButton.addEventListener('click', handleFire);
        fireButton.addEventListener('touchstart', (e) => { e.preventDefault(); handleFire(); }, { passive: false });


        // Initial setup on window load
        window.onload = function() {
            resizeCanvas(); // Set initial canvas size
            game = new AsteroidShooterGame(); // Initialize the game object
            
            // Show a starting message
            showMessage(
                "ASTEROID SHREDDER: CODE ZERO",
                "Welcome, Ronin! Protect your ship from the asteroids. Use the buttons (or A/D and Space on keyboard) to move and fire!",
                "Start Game",
                () => {
                    lastTime = performance.now();
                    gameLoopId = requestAnimationFrame(animate);
                }
            );
        };
    </script>
</body>
</html>

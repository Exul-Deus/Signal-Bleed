<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GRAVE WORM</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Courier New', monospace;
            background: #000000;
            color: #ff00ff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            overflow: hidden;
            background-image: 
                radial-gradient(circle at 20% 50%, rgba(255, 0, 255, 0.03) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(255, 0, 255, 0.03) 0%, transparent 50%);
        }

        #gameContainer {
            text-align: center;
            max-width: 600px;
            width: 100%;
            padding: 20px;
        }

        h1 {
            font-size: 3.5em;
            margin-bottom: 5px;
            text-shadow: 
                0 0 10px #ff00ff,
                0 0 20px #ff00ff,
                0 0 30px #ff00ff,
                0 0 40px #ff00ff;
            letter-spacing: 8px;
            animation: titlePulse 3s infinite;
        }

        @keyframes titlePulse {
            0%, 100% { 
                text-shadow: 
                    0 0 10px #ff00ff,
                    0 0 20px #ff00ff,
                    0 0 30px #ff00ff,
                    0 0 40px #ff00ff;
            }
            50% { 
                text-shadow: 
                    0 0 20px #ff00ff,
                    0 0 30px #ff00ff,
                    0 0 40px #ff00ff,
                    0 0 50px #ff00ff,
                    0 0 60px #ff0066;
            }
        }

        .subtitle {
            font-size: 0.9em;
            color: #ff0066;
            margin-bottom: 20px;
            text-shadow: 0 0 10px #ff0066;
            letter-spacing: 4px;
        }

        #hud {
            display: flex;
            justify-content: space-between;
            margin: 20px 0;
            font-size: 1.3em;
            text-shadow: 0 0 10px #ff00ff;
            padding: 0 10px;
        }

        #gameCanvas {
            border: 3px solid #ff00ff;
            box-shadow: 
                0 0 20px #ff00ff,
                inset 0 0 20px rgba(255, 0, 255, 0.1);
            background: #0a0a0a;
            display: block;
            margin: 0 auto;
            max-width: 100%;
            height: auto;
        }

        #controls {
            margin-top: 20px;
            font-size: 0.9em;
            color: #ff00ff99;
            line-height: 1.6;
        }

        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.95);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .overlayContent {
            background: linear-gradient(135deg, #0a0a0a 0%, #1a0a1a 100%);
            border: 3px solid #ff00ff;
            padding: 40px;
            max-width: 500px;
            text-align: center;
            box-shadow: 
                0 0 50px #ff00ff,
                inset 0 0 30px rgba(255, 0, 255, 0.1);
            animation: overlayGlow 2s infinite;
        }

        @keyframes overlayGlow {
            0%, 100% { box-shadow: 0 0 50px #ff00ff, inset 0 0 30px rgba(255, 0, 255, 0.1); }
            50% { box-shadow: 0 0 70px #ff00ff, inset 0 0 40px rgba(255, 0, 255, 0.2); }
        }

        .overlayContent h2 {
            font-size: 2.5em;
            margin-bottom: 20px;
            text-shadow: 0 0 20px #ff00ff;
            letter-spacing: 4px;
        }

        .overlayContent p {
            margin: 15px 0;
            line-height: 1.8;
            font-size: 1.1em;
        }

        .flavorText {
            font-style: italic;
            color: #ff0066;
            margin: 20px 0;
            text-shadow: 0 0 10px #ff0066;
        }

        button {
            background: #ff00ff;
            color: #000;
            border: none;
            padding: 15px 40px;
            font-size: 1.3em;
            font-family: 'Courier New', monospace;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
            text-transform: uppercase;
            letter-spacing: 2px;
            box-shadow: 0 0 20px #ff00ff;
            font-weight: bold;
        }

        button:hover {
            background: #ff0066;
            box-shadow: 0 0 40px #ff0066;
            transform: scale(1.05);
        }

        button:active {
            transform: scale(0.95);
        }

        .hidden {
            display: none;
        }

        #highScore {
            color: #ffff00;
            text-shadow: 0 0 10px #ffff00;
        }

        @media (max-width: 768px) {
            h1 {
                font-size: 2.5em;
                letter-spacing: 4px;
            }
            
            .subtitle {
                font-size: 0.8em;
            }
            
            #hud {
                font-size: 1em;
            }
            
            #gameCanvas {
                width: 90vw;
                height: 90vw;
            }

            .overlayContent {
                padding: 30px 20px;
                margin: 20px;
            }

            .overlayContent h2 {
                font-size: 2em;
            }

            button {
                padding: 12px 30px;
                font-size: 1.1em;
            }
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <h1>☠ GRAVE WORM ☠</h1>
        <div class="subtitle">CONSUME THE SOULS</div>
        
        <div id="hud">
            <div>SOULS: <span id="score">0</span></div>
            <div id="highScore">BEST: <span id="highScoreValue">0</span></div>
        </div>
        
        <canvas id="gameCanvas" width="600" height="600"></canvas>
        
        <div id="controls">
            🎮 Desktop: Use ARROW KEYS or WASD<br>
            📱 Mobile: SWIPE to change direction
        </div>
    </div>

    <!-- Start Screen -->
    <div id="startScreen" class="overlay">
        <div class="overlayContent">
            <h2>☠ GRAVE WORM ☠</h2>
            <p>A hunger stirs beneath the cemetery...</p>
            <p>Guide the grave worm through eternal darkness</p>
            <p>💀 Consume the souls of the departed</p>
            <p>👻 Grow longer with each soul devoured</p>
            <p>⚠️ Do not bite yourself or strike the void's edge</p>
            <button onclick="startGame()">AWAKEN THE WORM</button>
        </div>
    </div>

    <!-- Game Over Screen -->
    <div id="gameOverScreen" class="overlay hidden">
        <div class="overlayContent">
            <h2>💀 THE WORM RESTS 💀</h2>
            <p class="flavorText">"The grave worm returns to its tomb..."</p>
            <p>SOULS CONSUMED: <span id="finalScore"></span></p>
            <p id="newHighScore" class="hidden" style="color: #ffff00; text-shadow: 0 0 20px #ffff00;">✨ NEW RECORD! ✨</p>
            <button onclick="restartGame()">RISE AGAIN</button>
        </div>
    </div>

    <script>
        // ======================
        // GAME CONFIGURATION
        // ======================
        const GRID_SIZE = 20;
        const TILE_SIZE = 30; // Canvas is 600x600, so 20x20 grid
        const INITIAL_SPEED = 150; // Milliseconds between moves
        const SPEED_INCREASE = 2; // Speed increases by this amount per soul
        const MIN_SPEED = 50; // Maximum speed (minimum delay)

        // ======================
        // CANVAS SETUP
        // ======================
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // ======================
        // GAME STATE
        // ======================
        let gameState = {
            worm: [
                { x: 10, y: 10 },
                { x: 9, y: 10 },
                { x: 8, y: 10 }
            ],
            direction: { x: 1, y: 0 },
            nextDirection: { x: 1, y: 0 },
            soul: { x: 15, y: 10 },
            score: 0,
            gameRunning: false,
            speed: INITIAL_SPEED,
            highScore: localStorage.getItem('graveWormHighScore') || 0
        };

        let gameLoop = null;

        // ======================
        // DRAWING FUNCTIONS
        // ======================
        function drawGrid() {
            ctx.strokeStyle = '#1a1a1a';
            ctx.lineWidth = 1;
            
            for (let i = 0; i <= GRID_SIZE; i++) {
                // Vertical lines
                ctx.beginPath();
                ctx.moveTo(i * TILE_SIZE, 0);
                ctx.lineTo(i * TILE_SIZE, canvas.height);
                ctx.stroke();
                
                // Horizontal lines
                ctx.beginPath();
                ctx.moveTo(0, i * TILE_SIZE);
                ctx.lineTo(canvas.width, i * TILE_SIZE);
                ctx.stroke();
            }
        }

        function drawWorm() {
            gameState.worm.forEach((segment, index) => {
                const x = segment.x * TILE_SIZE;
                const y = segment.y * TILE_SIZE;
                
                // Worm head is brighter
                if (index === 0) {
                    // Head glow
                    const gradient = ctx.createRadialGradient(
                        x + TILE_SIZE / 2, y + TILE_SIZE / 2, 0,
                        x + TILE_SIZE / 2, y + TILE_SIZE / 2, TILE_SIZE
                    );
                    gradient.addColorStop(0, '#ff00ff');
                    gradient.addColorStop(0.5, '#ff0066');
                    gradient.addColorStop(1, 'transparent');
                    
                    ctx.fillStyle = gradient;
                    ctx.fillRect(x - 5, y - 5, TILE_SIZE + 10, TILE_SIZE + 10);
                    
                    ctx.fillStyle = '#ff00ff';
                } else {
                    // Body segments fade slightly
                    const alpha = 1 - (index / gameState.worm.length) * 0.3;
                    ctx.fillStyle = `rgba(255, 0, 255, ${alpha})`;
                }
                
                // Draw segment
                ctx.fillRect(x + 2, y + 2, TILE_SIZE - 4, TILE_SIZE - 4);
                
                // Segment glow
                ctx.shadowBlur = 15;
                ctx.shadowColor = '#ff00ff';
                ctx.fillRect(x + 2, y + 2, TILE_SIZE - 4, TILE_SIZE - 4);
                ctx.shadowBlur = 0;
            });
        }

        function drawSoul() {
            const x = gameState.soul.x * TILE_SIZE + TILE_SIZE / 2;
            const y = gameState.soul.y * TILE_SIZE + TILE_SIZE / 2;
            const time = Date.now() / 1000;
            const pulse = Math.sin(time * 3) * 0.3 + 0.7;
            
            // Outer glow
            const outerGradient = ctx.createRadialGradient(x, y, 0, x, y, TILE_SIZE * 1.5);
            outerGradient.addColorStop(0, `rgba(255, 255, 255, ${pulse * 0.3})`);
            outerGradient.addColorStop(0.5, `rgba(255, 0, 255, ${pulse * 0.2})`);
            outerGradient.addColorStop(1, 'transparent');
            
            ctx.fillStyle = outerGradient;
            ctx.beginPath();
            ctx.arc(x, y, TILE_SIZE * 1.5, 0, Math.PI * 2);
            ctx.fill();
            
            // Soul core
            const gradient = ctx.createRadialGradient(x, y, 0, x, y, TILE_SIZE / 2);
            gradient.addColorStop(0, '#ffffff');
            gradient.addColorStop(0.4, '#ff00ff');
            gradient.addColorStop(1, '#ff0066');
            
            ctx.fillStyle = gradient;
            ctx.beginPath();
            ctx.arc(x, y, TILE_SIZE / 2 * pulse, 0, Math.PI * 2);
            ctx.fill();
            
            // Extra glow
            ctx.shadowBlur = 20;
            ctx.shadowColor = '#ff00ff';
            ctx.beginPath();
            ctx.arc(x, y, TILE_SIZE / 2 * pulse, 0, Math.PI * 2);
            ctx.fill();
            ctx.shadowBlur = 0;
        }

        function render() {
            // Clear canvas
            ctx.fillStyle = '#0a0a0a';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Draw elements
            drawGrid();
            drawSoul();
            drawWorm();
        }

        // ======================
        // GAME LOGIC
        // ======================
        function spawnSoul() {
            let newPosition;
            let valid = false;
            
            while (!valid) {
                newPosition = {
                    x: Math.floor(Math.random() * GRID_SIZE),
                    y: Math.floor(Math.random() * GRID_SIZE)
                };
                
                // Check if position is not on worm
                valid = !gameState.worm.some(segment => 
                    segment.x === newPosition.x && segment.y === newPosition.y
                );
            }
            
            gameState.soul = newPosition;
        }

        function moveWorm() {
            if (!gameState.gameRunning) return;
            
            // Update direction (prevent 180-degree turns)
            const head = gameState.worm[0];
            const neck = gameState.worm[1];
            const newHead = {
                x: head.x + gameState.nextDirection.x,
                y: head.y + gameState.nextDirection.y
            };
            
            // Don't allow moving directly backwards into neck
            if (!(newHead.x === neck.x && newHead.y === neck.y)) {
                gameState.direction = { ...gameState.nextDirection };
            }
            
            // Calculate new head position
            const finalHead = {
                x: head.x + gameState.direction.x,
                y: head.y + gameState.direction.y
            };
            
            // Check wall collision
            if (finalHead.x < 0 || finalHead.x >= GRID_SIZE || 
                finalHead.y < 0 || finalHead.y >= GRID_SIZE) {
                gameOver();
                return;
            }
            
            // Check self collision
            if (gameState.worm.some(segment => 
                segment.x === finalHead.x && segment.y === finalHead.y)) {
                gameOver();
                return;
            }
            
            // Add new head
            gameState.worm.unshift(finalHead);
            
            // Check soul collection
            if (finalHead.x === gameState.soul.x && finalHead.y === gameState.soul.y) {
                gameState.score++;
                document.getElementById('score').textContent = gameState.score;
                spawnSoul();
                
                // Increase speed (decrease delay)
                gameState.speed = Math.max(MIN_SPEED, gameState.speed - SPEED_INCREASE);
                restartGameLoop();
                
                // Soul collected - worm grows (don't remove tail)
            } else {
                // Remove tail (worm moves without growing)
                gameState.worm.pop();
            }
            
            render();
        }

        function restartGameLoop() {
            if (gameLoop) {
                clearInterval(gameLoop);
            }
            gameLoop = setInterval(moveWorm, gameState.speed);
        }

        // ======================
        // GAME FLOW
        // ======================
        function startGame() {
            document.getElementById('startScreen').classList.add('hidden');
            
            // Reset game state
            gameState = {
                worm: [
                    { x: 10, y: 10 },
                    { x: 9, y: 10 },
                    { x: 8, y: 10 }
                ],
                direction: { x: 1, y: 0 },
                nextDirection: { x: 1, y: 0 },
                soul: { x: 15, y: 10 },
                score: 0,
                gameRunning: true,
                speed: INITIAL_SPEED,
                highScore: localStorage.getItem('graveWormHighScore') || 0
            };
            
            document.getElementById('score').textContent = '0';
            document.getElementById('highScoreValue').textContent = gameState.highScore;
            
            spawnSoul();
            render();
            restartGameLoop();
        }

        function gameOver() {
            gameState.gameRunning = false;
            clearInterval(gameLoop);
            
            // Check for high score
            const isNewHighScore = gameState.score > gameState.highScore;
            if (isNewHighScore) {
                gameState.highScore = gameState.score;
                localStorage.setItem('graveWormHighScore', gameState.highScore);
                document.getElementById('newHighScore').classList.remove('hidden');
            } else {
                document.getElementById('newHighScore').classList.add('hidden');
            }
            
            document.getElementById('finalScore').textContent = gameState.score;
            document.getElementById('gameOverScreen').classList.remove('hidden');
        }

        function restartGame() {
            document.getElementById('gameOverScreen').classList.add('hidden');
            startGame();
        }

        // ======================
        // KEYBOARD CONTROLS
        // ======================
        document.addEventListener('keydown', (e) => {
            if (!gameState.gameRunning) return;
            
            switch(e.key) {
                case 'ArrowUp':
                case 'w':
                case 'W':
                    e.preventDefault();
                    if (gameState.direction.y === 0) {
                        gameState.nextDirection = { x: 0, y: -1 };
                    }
                    break;
                case 'ArrowDown':
                case 's':
                case 'S':
                    e.preventDefault();
                    if (gameState.direction.y === 0) {
                        gameState.nextDirection = { x: 0, y: 1 };
                    }
                    break;
                case 'ArrowLeft':
                case 'a':
                case 'A':
                    e.preventDefault();
                    if (gameState.direction.x === 0) {
                        gameState.nextDirection = { x: -1, y: 0 };
                    }
                    break;
                case 'ArrowRight':
                case 'd':
                case 'D':
                    e.preventDefault();
                    if (gameState.direction.x === 0) {
                        gameState.nextDirection = { x: 1, y: 0 };
                    }
                    break;
            }
        });

        // ======================
        // TOUCH CONTROLS (MOBILE)
        // ======================
        let touchStartX = 0;
        let touchStartY = 0;

        canvas.addEventListener('touchstart', (e) => {
            e.preventDefault();
            touchStartX = e.touches[0].clientX;
            touchStartY = e.touches[0].clientY;
        }, { passive: false });

        canvas.addEventListener('touchmove', (e) => {
            e.preventDefault();
        }, { passive: false });

        canvas.addEventListener('touchend', (e) => {
            if (!gameState.gameRunning) return;
            
            e.preventDefault();
            const touchEndX = e.changedTouches[0].clientX;
            const touchEndY = e.changedTouches[0].clientY;
            
            const deltaX = touchEndX - touchStartX;
            const deltaY = touchEndY - touchStartY;
            
            // Determine swipe direction
            if (Math.abs(deltaX) > Math.abs(deltaY)) {
                // Horizontal swipe
                if (deltaX > 30 && gameState.direction.x === 0) {
                    gameState.nextDirection = { x: 1, y: 0 }; // Right
                } else if (deltaX < -30 && gameState.direction.x === 0) {
                    gameState.nextDirection = { x: -1, y: 0 }; // Left
                }
            } else {
                // Vertical swipe
                if (deltaY > 30 && gameState.direction.y === 0) {
                    gameState.nextDirection = { x: 0, y: 1 }; // Down
                } else if (deltaY < -30 && gameState.direction.y === 0) {
                    gameState.nextDirection = { x: 0, y: -1 }; // Up
                }
            }
        }, { passive: false });

        // ======================
        // INITIALIZATION
        // ======================
        // Load high score
        document.getElementById('highScoreValue').textContent = 
            localStorage.getItem('graveWormHighScore') || 0;
        
        // Initial render
        render();
    </script>
</body>
</html>

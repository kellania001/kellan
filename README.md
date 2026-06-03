# <!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Urban Dash - Game Mobil Kota 2D</title>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            box-sizing: border-box;
        }

        body {
            margin: 0;
            min-height: 100vh;
            background: linear-gradient(135deg, #0a2e2c 0%, #0a1f1a 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', 'Poppins', 'Montserrat', system-ui, -apple-system, sans-serif;
            padding: 16px;
        }

        .game-wrapper {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border-radius: 48px;
            background: rgba(5, 15, 12, 0.5);
            backdrop-filter: blur(3px);
            padding: 20px 25px 25px 25px;
            box-shadow: 0 30px 45px rgba(0, 0, 0, 0.6), inset 0 1px 1px rgba(255,255,255,0.1);
        }

        canvas {
            display: block;
            margin: 0 auto;
            border-radius: 28px;
            box-shadow: 0 18px 32px rgba(0, 0, 0, 0.6), 0 0 0 3px rgba(255, 215, 130, 0.3);
            cursor: none;
            background-color: #1e2a2a;
        }

        .dashboard {
            display: flex;
            justify-content: space-between;
            align-items: center;
            width: 100%;
            margin-top: 20px;
            gap: 20px;
            flex-wrap: wrap;
            justify-content: center;
        }

        .stats {
            display: flex;
            gap: 20px;
            background: #0b1c17cc;
            backdrop-filter: blur(12px);
            padding: 8px 24px;
            border-radius: 60px;
            border: 1px solid rgba(255, 200, 100, 0.6);
        }

        .stat-card {
            background: transparent;
            padding: 5px 15px;
            color: #f9f3cf;
            font-weight: bold;
            font-size: 1.5rem;
            letter-spacing: 1px;
            text-shadow: 0 2px 3px black;
        }

        .stat-card span {
            font-size: 0.8rem;
            font-weight: 500;
            color: #ffdd99;
            margin-right: 8px;
            text-transform: uppercase;
        }

        button {
            background: linear-gradient(145deg, #ffb347, #ff8c1a);
            border: none;
            font-weight: bold;
            padding: 10px 28px;
            border-radius: 60px;
            font-family: inherit;
            font-size: 1rem;
            cursor: pointer;
            box-shadow: 0 5px 0 #a55818;
            transition: 0.07s linear;
            color: #1e2a1f;
            letter-spacing: 1px;
            display: inline-flex;
            align-items: center;
            gap: 6px;
        }

        button:active {
            transform: translateY(3px);
            box-shadow: 0 2px 0 #a55818;
        }

        .controls {
            margin-top: 18px;
            display: flex;
            gap: 32px;
            background: #00000070;
            padding: 10px 32px;
            border-radius: 80px;
            backdrop-filter: blur(12px);
        }

        .ctrl-btn {
            background: #2c3e33e6;
            border: none;
            font-size: 1.8rem;
            font-weight: bold;
            padding: 8px 32px;
            border-radius: 50px;
            color: white;
            cursor: pointer;
            transition: 0.07s linear;
            box-shadow: 0 4px 0 #0a1f1a;
            font-family: monospace;
            letter-spacing: 2px;
        }

        .ctrl-btn:active {
            transform: scale(0.95);
            box-shadow: 0 1px 0 #0a1f1a;
        }

        .instruction-tip {
            margin-top: 14px;
            font-size: 0.75rem;
            background: #00000080;
            padding: 5px 18px;
            border-radius: 60px;
            color: #ffe6b3;
            font-weight: 500;
            backdrop-filter: blur(4px);
        }

        @media (max-width: 600px) {
            .ctrl-btn {
                font-size: 1.4rem;
                padding: 6px 20px;
            }
            .stat-card {
                font-size: 1.2rem;
            }
            .game-wrapper {
                padding: 12px;
            }
        }
    </style>
</head>
<body>
<div>
    <div class="game-wrapper">
        <canvas id="gameCanvas" width="550" height="680"></canvas>
        <div class="dashboard">
            <div class="stats">
                <div class="stat-card"><span>🏆 SCORE</span> <span id="scoreValue">0</span></div>
                <div class="stat-card"><span>🏅 BEST</span> <span id="highScoreValue">0</span></div>
            </div>
            <button id="resetGameBtn">🔄 RESTART</button>
        </div>
        <div class="controls">
            <button class="ctrl-btn" id="leftBtn">◀ KIRI</button>
            <button class="ctrl-btn" id="rightBtn">KANAN ▶</button>
        </div>
        <div class="instruction-tip">
            🚘  GESER KIRI/KANAN | MOBIL WARNA BERBEDA — HINDARI SEMUA!  ⚡  MENGIRING  🏙️
        </div>
    </div>
</div>

<script>
    (function(){
        // ==================== KONFIGURASI GAME REALISTIK ====================
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        
        // DIMENSI JALAN KOTA (3 lajur)
        const LANE_COUNT = 3;
        const LANE_WIDTH = canvas.width / LANE_COUNT;  // ~183.33px
        const CAR_WIDTH = 52;
        const CAR_HEIGHT = 88;
        
        // PEMILIHAN WARNA MOBIL UNTUK MUSUH (variasi sporty & urban)
        const ENEMY_CAR_COLORS = [
            { body: "#C41E3A", light: "#B22222", name: "Merah Kota" },    // merah marun
            { body: "#2C3E50", light: "#1A252F", name: "Biru Malam" },     // biru gelap
            { body: "#D4AF37", light: "#B8860B", name: "Emas Metalik" },    // emas
            { body: "#4A6D5C", light: "#2F4F3A", name: "Hijau Zamrud" },    // hijau
            { body: "#5D3A1A", light: "#3E2A1A", name: "Coklat Perkotaan" }, // coklat
            { body: "#6A0DAD", light: "#4B0082", name: "Ungu Mewah" },       // ungu
            { body: "#DC143C", light: "#8B0000", name: "Kirmizi" },          // merah terang
            { body: "#1F6392", light: "#0D4A6E", name: "Biru Laguna" },      // biru muda
            { body: "#676767", light: "#404040", name: "Abu Metal" }         // silver
        ];
        
        // WARNA MOBIL PLAYER (kustom sport)
        const PLAYER_GRADIENTS = {
            bodyTop: "#2DD4BF",
            bodyBottom: "#0F766E",
            accent: "#FBBF24"
        };
        
        // POSISI PLAYER
        let playerLane = 1;
        let playerX = (playerLane * LANE_WIDTH) + (LANE_WIDTH/2) - (CAR_WIDTH/2);
        const PLAYER_FIXED_Y = canvas.height - CAR_HEIGHT - 22;
        
        // ARRAY MUSUH (setiap musuh punya warna, style)
        let enemies = [];
        
        // STATUS GAME
        let score = 0;
        let highScore = localStorage.getItem('urbanHighScore') ? parseInt(localStorage.getItem('urbanHighScore')) : 0;
        let gameRunning = true;
        let gameOverFlag = false;
        
        // DINAMIKA KECEPATAN & SPAWN
        let baseSpeed = 4.2;
        let currentSpeed = baseSpeed;
        let spawnCounter = 0;
        let baseSpawnDelay = 48;
        let currentSpawnDelay = baseSpawnDelay;
        let minSpawnDelay = 32;
        
        // efek lingkungan (lampu kota)
        let cityLights = [];
        let frameCount = 0;
        
        // Preload data kota
        for(let i=0; i<12; i++){
            cityLights.push({
                x: Math.random() * canvas.width,
                intensity: 0.4 + Math.random() * 0.6,
                size: 3+Math.random()*5
            });
        }
        
        // ==================== FUNGSI UTAMA ====================
        function updateHighScoreUI(){
            document.getElementById('highScoreValue').innerText = highScore;
        }
        
        function updateScoreUI(){
            document.getElementById('scoreValue').innerText = Math.floor(score);
        }
        
        function saveHighScore(){
            let floorScore = Math.floor(score);
            if(floorScore > highScore){
                highScore = floorScore;
                localStorage.setItem('urbanHighScore', highScore);
                updateHighScoreUI();
            }
        }
        
        // update posisi player berdasarkan lane
        function updatePlayerPosition(){
            playerX = (playerLane * LANE_WIDTH) + (LANE_WIDTH/2) - (CAR_WIDTH/2);
            playerX = Math.min(Math.max(playerX, 8), canvas.width - CAR_WIDTH - 8);
        }
        
        // Spawn musuh dengan warna acak & variasi bentuk
        function spawnEnemy(){
            const lane = Math.floor(Math.random() * LANE_COUNT);
            const enemyX = (lane * LANE_WIDTH) + (LANE_WIDTH/2) - (CAR_WIDTH/2);
            const colorStyle = ENEMY_CAR_COLORS[Math.floor(Math.random() * ENEMY_CAR_COLORS.length)];
            // variasi kecepatan individual (sedikit perbedaan)
            const speedOffset = 0.85 + Math.random() * 0.45;
            enemies.push({
                x: enemyX,
                y: -CAR_HEIGHT - (Math.random() * 70),
                width: CAR_WIDTH,
                height: CAR_HEIGHT,
                lane: lane,
                color: colorStyle.body,
                lightColor: colorStyle.light,
                speedMult: speedOffset,
                id: Math.random()
            });
        }
        
        // RESET FULL GAME
        function resetGame(){
            gameRunning = true;
            gameOverFlag = false;
            score = 0;
            currentSpeed = baseSpeed;
            enemies = [];
            playerLane = 1;
            updatePlayerPosition();
            spawnCounter = 6;
            currentSpawnDelay = baseSpawnDelay;
            updateScoreUI();
            saveHighScore();
            updateHighScoreUI();
        }
        
        // Pergerakan player
        function moveLeft(){
            if(!gameRunning) return;
            if(playerLane > 0){
                playerLane--;
                updatePlayerPosition();
            }
        }
        
        function moveRight(){
            if(!gameRunning) return;
            if(playerLane < LANE_COUNT-1){
                playerLane++;
                updatePlayerPosition();
            }
        }
        
        // Deteksi tabrakan presisi (dengan sedikit margin visual)
        function checkCollision(){
            const playerRect = {
                x: playerX + 4, y: PLAYER_FIXED_Y + 4,
                w: CAR_WIDTH - 8, h: CAR_HEIGHT - 8
            };
            for(let i=0; i<enemies.length; i++){
                const e = enemies[i];
                const enemyRect = {
                    x: e.x + 5, y: e.y + 5,
                    w: CAR_WIDTH - 10, h: CAR_HEIGHT - 8
                };
                if(playerRect.x < enemyRect.x + enemyRect.w &&
                   playerRect.x + playerRect.w > enemyRect.x &&
                   playerRect.y < enemyRect.y + enemyRect.h &&
                   playerRect.y + playerRect.h > enemyRect.y){
                    gameRunning = false;
                    gameOverFlag = true;
                    saveHighScore();
                    return true;
                }
            }
            return false;
        }
        
        // UPDATE LOGIKA
        function updateGame(){
            if(!gameRunning) return;
            
            // 1. Gerakan semua musuh (kecepatan dasar * variasi individu)
            for(let i=0; i<enemies.length; i++){
                enemies[i].y += currentSpeed * enemies[i].speedMult;
            }
            // hapus musuh yang sudah lewat layar
            enemies = enemies.filter(enemy => enemy.y < canvas.height + 200);
            
            // 2. Spawn musuh dinamis (semakin tinggi score, semakin cepat spawn)
            if(spawnCounter <= 0){
                spawnEnemy();
                let dynamicDelay = Math.max(minSpawnDelay, baseSpawnDelay - Math.floor(score / 340));
                currentSpawnDelay = dynamicDelay;
                spawnCounter = currentSpawnDelay;
            } else {
                spawnCounter--;
            }
            
            // 3. Penambahan score berdasarkan kecepatan dan survival
            let increment = 0.22 + (currentSpeed / 38);
            score += increment;
            updateScoreUI();
            
            // 4. Kecepatan meningkat seiring skor, maks 13.8
            let newSpeed = baseSpeed + (Math.floor(score) / 720);
            if(newSpeed > 13.6) newSpeed = 13.6;
            currentSpeed = newSpeed;
            
            // 5. Deteksi tabrakan
            checkCollision();
        }
        
        // ==================== GAMBAR JALANAN KOTA YANG REALISTIK ====================
        function drawUrbanRoad(){
            // Aspal berkualitas dengan tekstur
            const grad = ctx.createLinearGradient(0, 0, 0, canvas.height);
            grad.addColorStop(0, "#242c2a");
            grad.addColorStop(1, "#1a2320");
            ctx.fillStyle = grad;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // Trotoar kiri & kanan
            ctx.fillStyle = "#4a3b2c";
            ctx.fillRect(0, 0, 14, canvas.height);
            ctx.fillRect(canvas.width-14, 0, 14, canvas.height);
            ctx.fillStyle = "#6d5c47";
            ctx.fillRect(2, 0, 6, canvas.height);
            ctx.fillRect(canvas.width-8, 0, 6, canvas.height);
            
            // Garis marka jalan (putih kekuningan)
            ctx.beginPath();
            ctx.strokeStyle = "#FFD966";
            ctx.lineWidth = 6;
            ctx.setLineDash([20, 40]);
            for(let i = 1; i <= 2; i++){
                let laneMark = i * LANE_WIDTH;
                ctx.beginPath();
                ctx.moveTo(laneMark, 0);
                ctx.lineTo(laneMark, canvas.height);
                ctx.stroke();
            }
            ctx.setLineDash([]);
            
            // Garis tepi putih solid
            ctx.beginPath();
            ctx.strokeStyle = "#FFF2CC";
            ctx.lineWidth = 4;
            ctx.moveTo(14, 0);
            ctx.lineTo(14, canvas.height);
            ctx.moveTo(canvas.width-14, 0);
            ctx.lineTo(canvas.width-14, canvas.height);
            ctx.stroke();
            
            // Efek cat perkerasan (bintik-bintik)
            ctx.fillStyle = "#bcae7a30";
            for(let i=0;i<180;i++){
                ctx.fillRect(15 + Math.random()*(canvas.width-30), Math.random()*canvas.height, 2, 2);
            }
            
            // Bayangan pepohonan & lampu pinggir (gaya urban)
            ctx.fillStyle = "#31663daa";
            for(let i=0;i<8;i++){
                ctx.beginPath();
                ctx.rect(2, (frameCount*0.5 + i*85) % canvas.height, 8, 32);
                ctx.fill();
            }
            
            // Lampu kota animasi berkedip (realistis)
            for(let i=0;i<cityLights.length;i++){
                let l = cityLights[i];
                let flicker = 0.6 + Math.sin(frameCount*0.08 + i)*0.3;
                ctx.fillStyle = `rgba(255, 220, 120, ${l.intensity * flicker})`;
                ctx.beginPath();
                ctx.arc(l.x, (frameCount*0.3 + i*43) % canvas.height, l.size, 0, Math.PI*2);
                ctx.fill();
            }
            
            // Zebra cross dekat bawah (kesan kota)
            ctx.fillStyle = "#f4f1da";
            for(let i=0;i<12;i++){
                ctx.fillRect(40 + i*45, canvas.height-48, 22, 8);
                ctx.fillRect(40 + i*45, canvas.height-32, 22, 8);
            }
        }
        
        // MOBIL PLAYER dengan detail chrome & kaca spion
        function drawPlayerCar(){
            ctx.shadowBlur = 8;
            ctx.shadowColor = "#00000080";
            // body gradasi sport
            const bodyGrad = ctx.createLinearGradient(playerX, PLAYER_FIXED_Y, playerX+CAR_WIDTH, PLAYER_FIXED_Y+CAR_HEIGHT);
            bodyGrad.addColorStop(0, PLAYER_GRADIENTS.bodyTop);
            bodyGrad.addColorStop(1, PLAYER_GRADIENTS.bodyBottom);
            ctx.fillStyle = bodyGrad;
            ctx.fillRect(playerX, PLAYER_FIXED_Y, CAR_WIDTH, CAR_HEIGHT);
            // kaca depan
            ctx.fillStyle = "#A2E3FF";
            ctx.fillRect(playerX+8, PLAYER_FIXED_Y+12, CAR_WIDTH-16, 28);
            ctx.fillStyle = "#4B6E8A";
            ctx.fillRect(playerX+12, PLAYER_FIXED_Y+44, CAR_WIDTH-24, 18);
            // lampu LED
            ctx.fillStyle = "#FFD966";
            ctx.fillRect(playerX+5, PLAYER_FIXED_Y+CAR_HEIGHT-13, 12, 9);
            ctx.fillRect(playerX+CAR_WIDTH-17, PLAYER_FIXED_Y+CAR_HEIGHT-13, 12, 9);
            ctx.fillStyle = "#FF6B6B";
            ctx.fillRect(playerX+5, PLAYER_FIXED_Y+5, 10, 9);
            ctx.fillRect(playerX+CAR_WIDTH-15, PLAYER_FIXED_Y+5, 10, 9);
            // pelek
            ctx.fillStyle = "#202020";
            ctx.fillRect(playerX+7, PLAYER_FIXED_Y+CAR_HEIGHT-14, 12, 8);
            ctx.fillRect(playerX+CAR_WIDTH-19, PLAYER_FIXED_Y+CAR_HEIGHT-14, 12, 8);
            ctx.fillRect(playerX+7, PLAYER_FIXED_Y+5, 12, 8);
            ctx.fillRect(playerX+CAR_WIDTH-19, PLAYER_FIXED_Y+5, 12, 8);
            // strip oranye
            ctx.fillStyle = PLAYER_GRADIENTS.accent;
            ctx.fillRect(playerX+5, PLAYER_FIXED_Y+CAR_HEIGHT-28, CAR_WIDTH-10, 5);
            ctx.shadowBlur = 0;
        }
        
        // MOBIL MUSUH dengan variasi warna & gaya kaca spion
        function drawEnemyCar(x, y, bodyColor, lightColor){
            ctx.shadowBlur = 5;
            // body
            ctx.fillStyle = bodyColor;
            ctx.fillRect(x, y, CAR_WIDTH, CAR_HEIGHT);
            // kaca gelap
            ctx.fillStyle = "#34495E";
            ctx.fillRect(x+8, y+12, CAR_WIDTH-16, 24);
            ctx.fillStyle = "#2C3E50";
            ctx.fillRect(x+12, y+40, CAR_WIDTH-24, 18);
            // lampu belakang merah
            ctx.fillStyle = "#E67E22";
            ctx.fillRect(x+5, y+CAR_HEIGHT-13, 12, 8);
            ctx.fillRect(x+CAR_WIDTH-17, y+CAR_HEIGHT-13, 12, 8);
            ctx.fillStyle = lightColor;
            ctx.fillRect(x+5, y+5, 10, 8);
            ctx.fillRect(x+CAR_WIDTH-15, y+5, 10, 8);
            // velg
            ctx.fillStyle = "#1E1E1E";
            ctx.fillRect(x+7, y+CAR_HEIGHT-14, 11, 7);
            ctx.fillRect(x+CAR_WIDTH-18, y+CAR_HEIGHT-14, 11, 7);
            ctx.fillRect(x+7, y+5, 11, 7);
            ctx.fillRect(x+CAR_WIDTH-18, y+5, 11, 7);
            ctx.shadowBlur = 0;
        }
        
        // Efek kecepatan (motion blur ringan)
        function drawMotionEffect(){
            if(!gameRunning) return;
            let intensity = Math.min(0.45, (currentSpeed - baseSpeed) / 12);
            if(intensity > 0.05){
                ctx.fillStyle = `rgba(255, 235, 160, ${intensity * 0.3})`;
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                for(let i=0;i<12;i++){
                    ctx.beginPath();
                    ctx.moveTo(Math.random()*canvas.width, canvas.height - Math.random()*canvas.height);
                    ctx.lineTo(Math.random()*canvas.width, canvas.height);
                    ctx.strokeStyle = `rgba(255,200,70,${intensity * 0.6})`;
                    ctx.lineWidth = 2+Math.random()*5;
                    ctx.stroke();
                }
            }
        }
        
        // layar GAME OVER dengan gaya urban
        function drawGameOverUI(){
            if(!gameRunning && gameOverFlag){
                ctx.fillStyle = "rgba(0,0,0,0.82)";
                ctx.fillRect(0,0,canvas.width,canvas.height);
                ctx.font = "800 38px 'Segoe UI', system-ui";
                ctx.fillStyle = "#FFC857";
                ctx.shadowBlur = 0;
                ctx.textAlign = "center";
                ctx.fillText("⛔ GAME OVER", canvas.width/2, canvas.height/2-40);
                ctx.font = "bold 20px monospace";
                ctx.fillStyle = "#FFE6B3";
                ctx.fillText("TEKAN RESTART UNTUK MELAJU LAGI", canvas.width/2, canvas.height/2+35);
                ctx.font = "bold 28px monospace";
                ctx.fillStyle = "#FFB347";
                ctx.fillText("🏁 SCORE: "+Math.floor(score), canvas.width/2, canvas.height/2+100);
                ctx.textAlign = "left";
            }
        }
        
        // Animasi gedung / pencakar langit latar belakang
        function drawSkyline(){
            ctx.fillStyle = "#13212e";
            ctx.fillRect(0, 0, canvas.width, 48);
            ctx.fillStyle = "#1f3a3f";
            for(let i=0;i<12;i++){
                let h = 30 + Math.sin(i)*12;
                ctx.fillRect(i*50+10, 12, 28, h);
                ctx.fillStyle = "#ffda8822";
                for(let w=0;w<3;w++) ctx.fillRect(i*50+15+w*6, 25, 3, 6);
                ctx.fillStyle = "#1f3a3f";
            }
            // matahari senja
            ctx.fillStyle = "#ffb347";
            ctx.beginPath();
            ctx.arc(canvas.width-45, 28, 18, 0, Math.PI*2);
            ctx.fill();
            ctx.fillStyle = "#ffdd99";
            ctx.beginPath();
            ctx.arc(canvas.width-48, 26, 12, 0, Math.PI*2);
            ctx.fill();
        }
        
        // ==================== RENDER LENGKAP ====================
        function render(){
            drawSkyline();
            drawUrbanRoad();
            // gambar semua musuh
            for(let i=0; i<enemies.length; i++){
                drawEnemyCar(enemies[i].x, enemies[i].y, enemies[i].color, enemies[i].lightColor);
            }
            drawPlayerCar();
            drawMotionEffect();
            drawGameOverUI();
            
            // Tampilan speedometer sederhana
            ctx.font = "bold 13px 'Courier New'";
            ctx.fillStyle = "#FCE38A";
            ctx.shadowBlur = 2;
            ctx.fillText("⚡ SPEED: "+Math.floor(currentSpeed*3.2)+" km/h", 18, 70);
            ctx.fillText("🏙️ CITY TRAFFIC", 18, 95);
            ctx.font = "italic 10px monospace";
            ctx.fillStyle = "#DDD5B0";
            ctx.fillText("Hindari semua mobil!", canvas.width-110, 40);
            ctx.shadowBlur = 0;
            frameCount++;
        }
        
        // ==================== GAME LOOP ====================
        function gameLoop(){
            updateGame();
            render();
            requestAnimationFrame(gameLoop);
        }
        
        // ==================== EVENT HANDLER & KONTROL ====================
        function handleKey(e){
            if(e.key === 'ArrowLeft' || e.key === 'Left' || e.key === 'a'){
                e.preventDefault();
                moveLeft();
            } else if(e.key === 'ArrowRight' || e.key === 'Right' || e.key === 'd'){
                e.preventDefault();
                moveRight();
            } else if(e.key === 'r' || e.key === 'R'){
                e.preventDefault();
                resetGame();
            }
        }
        
        // Touch swipe on canvas
        let touchStart = null;
        function onTouchStart(e){
            e.preventDefault();
            const rect = canvas.getBoundingClientRect();
            touchStart = (e.touches[0].clientX - rect.left) * (canvas.width/rect.width);
        }
        function onTouchMove(e){
            if(!gameRunning || touchStart === null) return;
            e.preventDefault();
            const rect = canvas.getBoundingClientRect();
            let currentX = (e.touches[0].clientX - rect.left) * (canvas.width/rect.width);
            let diff = currentX - touchStart;
            if(Math.abs(diff) > 22){
                if(diff > 0) moveRight();
                else moveLeft();
                touchStart = currentX;
            }
        }
        function onTouchEnd(e){ touchStart = null; }
        
        // tombol UI
        document.getElementById('leftBtn').addEventListener('click', () => moveLeft());
        document.getElementById('rightBtn').addEventListener('click', () => moveRight());
        document.getElementById('resetGameBtn').addEventListener('click', () => { resetGame(); });
        
        window.addEventListener('keydown', handleKey);
        canvas.addEventListener('touchstart', onTouchStart, {passive: false});
        canvas.addEventListener('touchmove', onTouchMove, {passive: false});
        canvas.addEventListener('touchend', onTouchEnd);
        canvas.addEventListener('contextmenu', (e) => e.preventDefault());
        
        // inisialisasi game
        function init(){
            resetGame();
            updateHighScoreUI();
            for(let i=0;i<2;i++) setTimeout(()=> spawnEnemy(), 120*i);
            gameLoop();
        }
        init();
    })();
</script>
</body>
</html>


<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador de Civilização Tipo III - Escala Kardashev</title>
    <style>
        :root {
            --bg-color: #020208;
            --panel-bg: rgba(13, 10, 30, 0.90);
            --border-color: rgba(192, 132, 252, 0.35);
            --accent-purple: #c084fc;
            --accent-pink: #f472b6;
            --accent-cyan: #38bdf8;
            --accent-green: #10b981;
            --accent-gold: #f59e0b;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            user-select: none;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            height: 100vh;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        header {
            height: 60px;
            background: linear-gradient(180deg, rgba(13, 10, 30, 0.95) 0%, rgba(2, 2, 8, 0.8) 100%);
            border-bottom: 1px solid var(--border-color);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 25px;
            z-index: 10;
        }

        header h1 {
            font-size: 1.05rem;
            letter-spacing: 2px;
            color: var(--accent-purple);
            text-transform: uppercase;
        }

        .status-badge {
            font-size: 0.75rem;
            padding: 4px 12px;
            border-radius: 12px;
            background: rgba(192, 132, 252, 0.15);
            border: 1px solid var(--accent-purple);
            color: var(--accent-purple);
            letter-spacing: 1px;
        }

        .main-container {
            display: grid;
            grid-template-columns: 360px 1fr;
            height: calc(100vh - 60px);
            position: relative;
        }

        /* PAINEL LATERAL DE CONTROLE */
        .control-panel {
            background: var(--panel-bg);
            border-right: 1px solid var(--border-color);
            backdrop-filter: blur(12px);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            overflow-y: auto;
            z-index: 5;
        }

        .kardashev-box {
            background: radial-gradient(circle, rgba(192, 132, 252, 0.22) 0%, rgba(2, 2, 8, 0.7) 100%);
            border: 1px solid var(--accent-purple);
            border-radius: 10px;
            padding: 16px;
            text-align: center;
            box-shadow: 0 0 25px rgba(192, 132, 252, 0.2);
        }

        .kardashev-score {
            font-size: 2.3rem;
            font-family: monospace;
            font-weight: bold;
            color: #fff;
            text-shadow: 0 0 15px var(--accent-purple);
            margin: 4px 0;
        }

        .section-title {
            font-size: 0.78rem;
            text-transform: uppercase;
            letter-spacing: 1.5px;
            color: var(--accent-purple);
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 6px;
        }

        .metric-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .metric-header {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
        }

        .metric-value {
            font-family: monospace;
            font-weight: bold;
            color: var(--accent-purple);
        }

        input[type="range"] {
            width: 100%;
            height: 6px;
            border-radius: 3px;
            background: rgba(255, 255, 255, 0.1);
            outline: none;
            accent-color: var(--accent-purple);
            cursor: pointer;
        }

        .toggle-box {
            display: flex;
            align-items: center;
            justify-content: space-between;
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 6px;
            padding: 8px 12px;
            font-size: 0.78rem;
        }

        .switch {
            position: relative;
            display: inline-block;
            width: 36px;
            height: 18px;
        }

        .switch input { opacity: 0; width: 0; height: 0; }

        .slider {
            position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0;
            background-color: rgba(255,255,255,0.2); transition: .3s; border-radius: 18px;
        }

        .slider:before {
            position: absolute; content: ""; height: 12px; width: 12px; left: 3px; bottom: 3px;
            background-color: white; transition: .3s; border-radius: 50%;
        }

        input:checked + .slider { background-color: var(--accent-purple); }
        input:checked + .slider:before { transform: translateX(18px); }

        .info-card {
            background: rgba(0, 0, 0, 0.45);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 8px;
            padding: 12px;
            font-size: 0.8rem;
            line-height: 1.45;
            color: var(--text-muted);
        }

        .info-card strong {
            color: var(--text-main);
        }

        /* VIEWPORT CANVAS */
        .viewport {
            position: relative;
            width: 100%;
            height: 100%;
            background: radial-gradient(circle at center, #090514 0%, #020208 100%);
            overflow: hidden;
        }

        canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        .hud-overlay {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: var(--panel-bg);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 15px;
            font-family: monospace;
            font-size: 0.75rem;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            gap: 6px;
            backdrop-filter: blur(10px);
        }

        .hud-line {
            display: flex;
            justify-content: space-between;
            gap: 20px;
        }

        ::-webkit-scrollbar { width: 5px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: var(--border-color); border-radius: 3px; }
    </style>
</head>
<body>

    <header>
        <h1>CIVILIZAÇÃO TIPO III: DOMÍNIO GALÁCTICO</h1>
        <div class="status-badge">REDE VIA LÁCTEA: CONECTADA</div>
    </header>

    <div class="main-container">
        <!-- PAINEL LATERAL -->
        <aside class="control-panel">
            <div class="kardashev-box">
                <div style="font-size: 0.72rem; color: var(--text-muted); text-transform: uppercase;">Métrica de Kardashev</div>
                <div class="kardashev-score" id="k-score">K 3.000</div>
                <div style="font-size: 0.78rem; color: var(--accent-purple);" id="power-display">1.00 × 10³⁶ W</div>
            </div>

            <div class="section-title">1. Expansão na Via Láctea</div>
            <div class="metric-group">
                <div class="metric-header">
                    <span>Sistemas Estelares Dominados</span>
                    <span class="metric-value" id="val-colonies">100 BILHÕES</span>
                </div>
                <input type="range" id="input-colonies" min="10" max="100" value="100">
            </div>

            <div class="metric-group">
                <div class="metric-header">
                    <span>Ajuste da Demanda (x10³5 W)</span>
                    <span class="metric-value" id="val-demand">10.0 W</span>
                </div>
                <input type="range" id="input-demand" min="0.1" max="20.0" step="0.1" value="10.0">
            </div>

            <div class="section-title">2. Engenharia e Transporte FTL</div>
            
            <div class="toggle-box">
                <span>Malha de Dobra Interestelar (Subespaço)</span>
                <label class="switch"><input type="checkbox" id="sw-warp" checked><span class="slider"></span></label>
            </div>

            <div class="toggle-box">
                <span>Motor Penrose no Buraco Negro Central</span>
                <label class="switch"><input type="checkbox" id="sw-blackhole" checked><span class="slider"></span></label>
            </div>

            <div class="toggle-box">
                <span>Comunicação Quântica Instantânea</span>
                <label class="switch"><input type="checkbox" id="sw-comm" checked><span class="slider"></span></label>
            </div>

            <div class="section-title">3. Diagnóstico Cosmoclimático</div>
            <div class="info-card">
                <strong>Status de Domínio Universal:</strong><br>
                • Ocupação integral dos braços espirais.<br>
                • Manipulação de órbitas e dinâmica estelar.<br>
                • Captura do disco de acreção de Sagitário A*.<br>
                • Fase inicial de sondagem do Grupo Local.
            </div>
        </aside>

        <!-- VIEWPORT CANVAS SIMULAÇÃO -->
        <main class="viewport">
            <canvas id="simCanvas"></canvas>

            <div class="hud-overlay">
                <div class="hud-line"><span>ENERGIA DA VIA LÁCTEA:</span><span id="hud-capture" style="color: var(--accent-purple)">100.0%</span></div>
                <div class="hud-line"><span>TEMPO PARA MIGRAR DE BRAÇO:</span><span id="hud-travel" style="color: var(--accent-cyan)">INSTANTÂNEO</span></div>
                <div class="hud-line"><span>Massa de Sagitário A* Expropriada:</span><span id="hud-bh" style="color: var(--accent-pink)">ATIVO (4.1M M☉)</span></div>
                <div class="hud-line"><span>ALCANCE DA CIVILIZAÇÃO:</span><span style="color: var(--accent-green)">GRUPO LOCAL / ANDRÔMEDA</span></div>
            </div>
        </main>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        // Estado da Simulação
        const state = {
            powerWatts: 1e36,
            colonyRatio: 1.0,
            kardashevScale: 3.0,
            warpActive: true,
            blackHoleActive: true,
            commActive: true,
            rotationAngle: 0,
            pulse: 0
        };

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Elementos de Entrada
        const inputColonies = document.getElementById('input-colonies');
        const inputDemand = document.getElementById('input-demand');
        const swWarp = document.getElementById('sw-warp');
        const swBlackhole = document.getElementById('sw-blackhole');
        const swComm = document.getElementById('sw-comm');

        // Partículas Estelares para a Galáxia Espiral
        const stars = [];
        const NUM_STARS = 2200;
        const ARMS = 4;

        for (let i = 0; i < NUM_STARS; i++) {
            const armIndex = i % ARMS;
            const distance = Math.pow(Math.random(), 1.5) * 260 + 15;
            const armAngle = (armIndex / ARMS) * Math.PI * 2;
            const spiralAngle = distance * 0.022;
            const angle = armAngle + spiralAngle + (Math.random() - 0.5) * 0.45;

            stars.push({
                xRatio: Math.cos(angle) * distance,
                yRatio: Math.sin(angle) * distance,
                distance: distance,
                angle: angle,
                size: Math.random() * 1.6 + 0.5,
                color: Math.random() < 0.2 ? '#f472b6' : (Math.random() < 0.6 ? '#c084fc' : '#38bdf8'),
                speed: (0.0015 + (1 / (distance + 10)) * 0.3)
            });
        }

        // Rotas / Naves da Rede Warp
        const warpShips = [];
        const NUM_SHIPS = 70;
        for (let i = 0; i < NUM_SHIPS; i++) {
            warpShips.push({
                startStarIndex: Math.floor(Math.random() * NUM_STARS),
                endStarIndex: Math.floor(Math.random() * NUM_STARS),
                progress: Math.random(),
                speed: 0.005 + Math.random() * 0.01
            });
        }

        // Atualização dos Cálculos Físicos
        function updatePhysics() {
            state.colonyRatio = parseInt(inputColonies.value) / 100;
            state.powerWatts = parseFloat(inputDemand.value) * 1e35 * state.colonyRatio;

            // Fórmula de Kardashev: K = (log10(P) - 6) / 10
            state.kardashevScale = (Math.log10(state.powerWatts) - 6) / 10;

            state.warpActive = swWarp.checked;
            state.blackHoleActive = swBlackhole.checked;
            state.commActive = swComm.checked;

            // Interface
            document.getElementById('k-score').innerText = `K ${state.kardashevScale.toFixed(3)}`;
            
            const powerScaled = (state.powerWatts / 1e35).toFixed(2);
            document.getElementById('power-display').innerText = `${powerScaled} × 10³⁵ W`;

            const billions = Math.round(state.colonyRatio * 100);
            document.getElementById('val-colonies').innerText = `${billions} BILHÕES`;
            document.getElementById('val-demand').innerText = `${parseFloat(inputDemand.value).toFixed(1)} W`;

            // HUD
            document.getElementById('hud-capture').innerText = `${(state.colonyRatio * 100).toFixed(1)}%`;
            document.getElementById('hud-travel').innerText = state.warpActive ? 'INSTANTÂNEO (DOBRA)' : 'SUBLUMINAR (ANOS-LUZ)';
            
            const bhHUD = document.getElementById('hud-bh');
            if (state.blackHoleActive) {
                bhHUD.innerText = 'ATIVO (4.1M M☉)';
                bhHUD.style.color = 'var(--accent-pink)';
            } else {
                bhHUD.innerText = 'INATIVO';
                bhHUD.style.color = 'var(--text-muted)';
            }
        }

        // Eventos
        inputColonies.addEventListener('input', updatePhysics);
        inputDemand.addEventListener('input', updatePhysics);
        swWarp.addEventListener('change', updatePhysics);
        swBlackhole.addEventListener('change', updatePhysics);
        swComm.addEventListener('change', updatePhysics);

        // Loop de Renderização no Canvas
        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            const cx = canvas.width / 2;
            const cy = canvas.height / 2;

            state.rotationAngle += 0.001;
            state.pulse += 0.03;

            // 1. Fundo Espacial com Poeira de Fundo
            ctx.fillStyle = 'rgba(192, 132, 252, 0.03)';
            ctx.beginPath();
            ctx.arc(cx, cy, 320, 0, Math.PI * 2);
            ctx.fill();

            // 2. Estrelas e Estrutura da Galáxia Espiral
            ctx.save();
            ctx.translate(cx, cy);

            const activeStarsCount = Math.floor(NUM_STARS * state.colonyRatio);

            for (let i = 0; i < NUM_STARS; i++) {
                const star = stars[i];
                star.angle += star.speed;

                const currentX = Math.cos(star.angle) * star.distance;
                const currentY = Math.sin(star.angle) * star.distance * 0.45; // Projeção inclinada em 3D

                const isColonized = i < activeStarsCount;

                ctx.beginPath();
                ctx.arc(currentX, currentY, isColonized ? star.size : star.size * 0.5, 0, Math.PI * 2);

                if (isColonized) {
                    ctx.fillStyle = star.color;
                    ctx.shadowColor = star.color;
                    ctx.shadowBlur = 6;
                } else {
                    ctx.fillStyle = 'rgba(108, 115, 128, 0.2)';
                    ctx.shadowBlur = 0;
                }
                
                ctx.fill();
                ctx.shadowBlur = 0;
            }

            // 3. Rotas de Dobra e Conexões Interestelares
            if (state.warpActive && activeStarsCount > 20) {
                warpShips.forEach(ship => {
                    if (ship.startStarIndex >= activeStarsCount || ship.endStarIndex >= activeStarsCount) return;

                    ship.progress += ship.speed;
                    if (ship.progress > 1) {
                        ship.progress = 0;
                        ship.startStarIndex = Math.floor(Math.random() * activeStarsCount);
                        ship.endStarIndex = Math.floor(Math.random() * activeStarsCount);
                    }

                    const s1 = stars[ship.startStarIndex];
                    const s2 = stars[ship.endStarIndex];

                    const x1 = Math.cos(s1.angle) * s1.distance;
                    const y1 = Math.sin(s1.angle) * s1.distance * 0.45;
                    const x2 = Math.cos(s2.angle) * s2.distance;
                    const y2 = Math.sin(s2.angle) * s2.distance * 0.45;

                    // Linha da rota
                    ctx.beginPath();
                    ctx.moveTo(x1, y1);
                    ctx.lineTo(x2, y2);
                    ctx.strokeStyle = 'rgba(192, 132, 252, 0.06)';
                    ctx.lineWidth = 0.8;
                    ctx.stroke();

                    // Nave / Pulso de energia FTL
                    const currX = x1 + (x2 - x1) * ship.progress;
                    const currY = y1 + (y2 - y1) * ship.progress;

                    ctx.beginPath();
                    ctx.arc(currX, currY, 1.5, 0, Math.PI * 2);
                    ctx.fillStyle = '#f472b6';
                    ctx.fill();
                });
            }

            ctx.restore();

            // 4. Buraco Negro Supermassivo Central (Sagitário A*) + Disco de Acreção
            const bhRadius = 18;
            const pulseGlow = Math.sin(state.pulse) * 3;

            if (state.blackHoleActive) {
                // Disco de Acreção Radiante
                const accGrad = ctx.createRadialGradient(cx, cy, bhRadius, cx, cy, bhRadius + 38 + pulseGlow);
                accGrad.addColorStop(0, '#ffffff');
                accGrad.addColorStop(0.3, '#f472b6');
                accGrad.addColorStop(0.7, 'rgba(192, 132, 252, 0.5)');
                accGrad.addColorStop(1, 'rgba(0,0,0,0)');

                ctx.beginPath();
                ctx.arc(cx, cy, bhRadius + 38 + pulseGlow, 0, Math.PI * 2);
                ctx.fillStyle = accGrad;
                ctx.fill();

                // Anéis de Contenção de Energia Penrose
                ctx.beginPath();
                ctx.ellipse(cx, cy, bhRadius + 22, (bhRadius + 22) * 0.45, state.rotationAngle * 2, 0, Math.PI * 2);
                ctx.strokeStyle = 'rgba(56, 189, 248, 0.8)';
                ctx.lineWidth = 1.5;
                ctx.stroke();
            }

            // Sombra do Horizonte de Eventos
            ctx.beginPath();
            ctx.arc(cx, cy, bhRadius, 0, Math.PI * 2);
            ctx.fillStyle = '#000000';
            ctx.shadowColor = '#f472b6';
            ctx.shadowBlur = state.blackHoleActive ? 15 : 0;
            ctx.fill();
            ctx.shadowBlur = 0;

            requestAnimationFrame(render);
        }

        // Inicialização
        updatePhysics();
        render();
    </script>
</body>
</html>

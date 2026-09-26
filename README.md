
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Systems Control: Nuclear Bunker Interactive Matrix v4</title>
    <style>
        :root {
            --hud-green: #39ff14;
            --hud-red: #ff0055;
            --hud-blue: #00f3ff;
            --hud-orange: #ffe600;
            --hud-pink: #ff007f;
            --panel-bg: rgba(8, 14, 28, 0.95);
            --border-style: 1px solid rgba(0, 243, 255, 0.35);
        }

        * {
            box-sizing: border-box;
            user-select: none;
        }

        body {
            margin: 0;
            padding: 12px;
            background-color: #030612;
            color: var(--hud-blue);
            font-family: 'Consolas', 'Courier New', Courier, monospace;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
        }

        .header-system {
            width: 100%;
            max-width: 1200px;
            display: flex;
            justify-content: space-between;
            border-bottom: 2px solid var(--hud-blue);
            padding-bottom: 8px;
            margin-bottom: 12px;
            font-size: 12px;
            font-weight: bold;
            letter-spacing: 1.5px;
            color: #ffffff;
            text-shadow: 0 0 10px var(--hud-blue);
        }

        .main-frame {
            display: flex;
            gap: 15px;
            max-width: 1200px;
            width: 100%;
            justify-content: center;
            flex-wrap: wrap;
        }

        .screen-container {
            position: relative;
            border: 2px solid var(--hud-blue);
            box-shadow: 0 0 30px rgba(0, 243, 255, 0.25);
            border-radius: 6px;
            background: #000;
            overflow: hidden;
        }

        canvas {
            display: block;
            background-color: #030612;
            cursor: crosshair;
        }

        .side-panel {
            background: var(--panel-bg);
            border: 2px solid var(--hud-blue);
            box-shadow: 0 0 20px rgba(0, 243, 255, 0.15);
            border-radius: 6px;
            padding: 16px;
            width: 330px;
            box-sizing: border-box;
            display: flex;
            flex-direction: column;
        }

        h2 {
            font-size: 12px;
            margin: 10px 0 6px 0;
            text-transform: uppercase;
            border-bottom: var(--border-style);
            padding-bottom: 4px;
            color: var(--hud-orange);
            letter-spacing: 1px;
        }

        h2:first-of-type { margin-top: 0; }

        .telemetry-row {
            display: flex;
            justify-content: space-between;
            font-size: 11px;
            margin: 4px 0;
            font-weight: bold;
        }

        .status-ok { color: var(--hud-green); text-shadow: 0 0 6px var(--hud-green); }
        .status-blue { color: var(--hud-blue); text-shadow: 0 0 6px var(--hud-blue); }
        .status-red { color: var(--hud-red); text-shadow: 0 0 6px var(--hud-red); }
        .status-orange { color: var(--hud-orange); text-shadow: 0 0 6px var(--hud-orange); }

        .intel-box {
            margin-top: 10px;
            background: rgba(3, 8, 20, 0.9);
            border: 1px dashed rgba(0, 243, 255, 0.4);
            padding: 8px;
            font-size: 10.5px;
            height: 100px;
            overflow-y: auto;
            line-height: 1.4;
            color: #a5f3fc;
            border-radius: 4px;
        }

        .btn-action {
            width: 100%;
            padding: 9px;
            margin-top: 8px;
            font-weight: bold;
            font-family: inherit;
            font-size: 11px;
            cursor: pointer;
            border-radius: 4px;
            border: 1px solid;
            background: transparent;
            transition: all 0.2s ease;
        }

        #btn-icbm {
            border-color: var(--hud-red);
            color: var(--hud-red);
            text-shadow: 0 0 5px var(--hud-red);
        }
        #btn-icbm:hover {
            background: var(--hud-red);
            color: #000;
            box-shadow: 0 0 15px var(--hud-red);
        }

        #btn-mhd {
            border-color: var(--hud-blue);
            color: var(--hud-blue);
            text-shadow: 0 0 5px var(--hud-blue);
        }
        #btn-mhd:hover {
            background: var(--hud-blue);
            color: #000;
            box-shadow: 0 0 15px var(--hud-blue);
        }

        #btn-reset {
            border-color: var(--hud-green);
            color: var(--hud-green);
            text-shadow: 0 0 5px var(--hud-green);
        }
        #btn-reset:hover {
            background: var(--hud-green);
            color: #000;
            box-shadow: 0 0 15px var(--hud-green);
        }

        .controls-guide {
            margin-top: auto;
            border-top: var(--border-style);
            padding-top: 8px;
            font-size: 10px;
            color: #94a3b8;
            line-height: 1.3;
        }
    </style>
</head>
<body>

    <div class="header-system">
        <div>INTERACTIVE ENGINE: BUNKER_MATRIX_v4 // OPERATIONAL CONTROL</div>
        <div>SROS LINK: ONLINE [MANUAL TARGET OVERRIDE]</div>
    </div>

    <div class="main-frame">
        <div class="screen-container">
            <canvas id="tacticalCanvas" width="850" height="550"></canvas>
        </div>

        <div class="side-panel">
            <h2>STRATEGIC FORTRESS</h2>
            <div class="telemetry-row"><span>BLINDAGEM NÚCLEO:</span><span id="txt-struct" class="status-ok">100%</span></div>
            <div class="telemetry-row"><span>ESTRESSE DE PRESSÃO:</span><span id="txt-press" class="status-blue">1.00 atm</span></div>
            <div class="telemetry-row"><span>SENSOR SÍSMICO:</span><span id="txt-quake" class="status-ok">0.00 G</span></div>

            <h2>MHD INTERACTIVE</h2>
            <div class="telemetry-row"><span>CONVERSOR MAGNÉTICO:</span><span class="status-red" id="txt-mhd-status">DESATIVADO</span></div>
            <div class="telemetry-row"><span>CARGA ANEL (MHD):</span><span style="color: var(--hud-blue);" id="txt-mhd-load">0.00 MW</span></div>
            <button class="btn-action" id="btn-mhd" onclick="toggleMHD()">ATIVAR DEFESA SÍSMICA (ESPAÇO)</button>

            <h2>S.A.M.S AIR COUNTER</h2>
            <div class="telemetry-row"><span>INTERCEPTADORES AA:</span><span class="status-orange" id="txt-sams-ammo">3 / 3 CELLS</span></div>
            <div class="telemetry-row"><span>SISTEMA DE MIRA:</span><span style="color: #fff;">MANUAL (CLIQUE CÉU)</span></div>

            <h2>SROS ORBITAL MATRIX</h2>
            <div class="telemetry-row"><span>FEED ZONA ZERO:</span><span class="status-ok" id="txt-sros-status">MONITORANDO</span></div>
            <div class="telemetry-row"><span>RÁDIO ALVO INTERCEPT:</span><span class="status-blue" id="txt-target">0.00m</span></div>
            
            <div class="intel-box" id="intel-logs">
                [SYSTEM] Matriz de simulação em execução contínua.<br>
                [DIRETRIZ] Clique no CÉU para disparar míssil S.A.M.S interceptador.<br>
                [DIRETRIZ] Pressione ESPAÇO para alternar a cúpula magnetohidrodinâmica (MHD).<br>
            </div>

            <button class="btn-action" id="btn-icbm" onclick="lancarICBM()">⚡ DISPARAR ICBM INIMIGO</button>
            <button class="btn-action" id="btn-reset" onclick="reiniciarSimulacao()">🔄 REINICIAR SISTEMAS</button>

            <div class="controls-guide">
                <strong>GUIA OPERACIONAL:</strong><br>
                • <strong>Clique no Céu</strong>: Dispara o interceptador S.A.M.S na coordenada.<br>
                • <strong>MHD Ativo</strong>: Reduz o impacto e choque térmico da ogiva no bunker.
            </div>
        </div>
    </div>

<script>
    const canvas = document.getElementById('tacticalCanvas');
    const ctx = canvas.getContext('2d');

    // Estado da Simulação
    let mhdAtivo = false;
    let integridadeEstrutura = 100;
    let pressaoAtm = 1.0;
    let tremorG = 0.0;
    let samsMunicao = 3;

    let icbmInimigo = null;
    let misseisSAMS = [];
    let explosoes = [];
    let detonacaoOcorreu = false;
    let raioExplosaoNuke = 0;

    let mouseX = 425, mouseY = 275;
    let animTime = 0;

    function adicionarLog(msg) {
        const box = document.getElementById('intel-logs');
        box.innerHTML = `> ${msg}<br>` + box.innerHTML;
    }

    // Ouvintes de Eventos
    canvas.addEventListener('mousemove', e => {
        const rect = canvas.getBoundingClientRect();
        mouseX = e.clientX - rect.left;
        mouseY = e.clientY - rect.top;
    });

    canvas.addEventListener('mousedown', e => {
        if (e.button === 0) {
            const rect = canvas.getBoundingClientRect();
            let clickY = e.clientY - rect.top;
            let clickX = e.clientX - rect.left;
            
            if (clickY < 320) {
                dispararSAMS(clickX, clickY);
            }
        }
    });

    window.addEventListener('keydown', e => {
        if (e.code === 'Space') {
            e.preventDefault();
            toggleMHD();
        }
    });

    function toggleMHD() {
        mhdAtivo = !mhdAtivo;
        const statusEl = document.getElementById('txt-mhd-status');
        const loadEl = document.getElementById('txt-mhd-load');
        const btnEl = document.getElementById('btn-mhd');

        if (mhdAtivo) {
            statusEl.innerText = "PRONTO / CARREGADO";
            statusEl.className = "status-blue";
            loadEl.innerText = "120.00 MW";
            btnEl.style.background = "var(--hud-blue)";
            btnEl.style.color = "#000";
            adicionarLog("[MHD] Supercondutores magnéticos energizados.");
        } else {
            statusEl.innerText = "DESATIVADO";
            statusEl.className = "status-red";
            loadEl.innerText = "0.00 MW";
            btnEl.style.background = "transparent";
            btnEl.style.color = "var(--hud-blue)";
            adicionarLog("[MHD] Indutores desativados.");
        }
    }

    function lancarICBM() {
        if (icbmInimigo) {
            adicionarLog("[AVISO] Já existe uma ogiva ICBM em trajetória!");
            return;
        }
        let startX = Math.random() * 600 + 120;
        icbmInimigo = {
            x: startX,
            y: -20,
            targetX: 425,
            targetY: 360,
            vx: (425 - startX) / 180,
            vy: 2.2,
            active: true
        };
        detonacaoOcorreu = false;
        raioExplosaoNuke = 0;
        adicionarLog("⚠️ [ALERTA VERMELHO] Reentrada de ogiva ICBM detectada!");
    }

    function dispararSAMS(tx, ty) {
        if (samsMunicao <= 0) {
            adicionarLog("[S.A.M.S] Munição esgotada!");
            return;
        }
        samsMunicao--;
        document.getElementById('txt-sams-ammo').innerText = `${samsMunicao} / 3 CELLS`;

        misseisSAMS.push({
            x: 425,
            y: 360,
            targetX: tx,
            targetY: ty,
            speed: 6.5,
            active: true
        });
        adicionarLog(`[S.A.M.S] Interceptador disparado para (${Math.round(tx)}, ${Math.round(ty)})`);
    }

    function reiniciarSimulacao() {
        icbmInimigo = null;
        misseisSAMS = [];
        explosoes = [];
        detonacaoOcorreu = false;
        raioExplosaoNuke = 0;
        integridadeEstrutura = 100;
        pressaoAtm = 1.0;
        tremorG = 0.0;
        samsMunicao = 3;

        document.getElementById('txt-struct').innerText = "100%";
        document.getElementById('txt-struct').className = "status-ok";
        document.getElementById('txt-sams-ammo').innerText = "3 / 3 CELLS";
        document.getElementById('txt-press').innerText = "1.00 atm";
        document.getElementById('txt-quake').innerText = "0.00 G";

        adicionarLog("[SISTEMA] Todos os sistemas foram reiniciados.");
    }

    // Loop de Atualização Física
    function update() {
        animTime += 0.05;

        // Atualizar Míssil Inimigo (ICBM)
        if (icbmInimigo && icbmInimigo.active) {
            icbmInimigo.x += icbmInimigo.vx;
            icbmInimigo.y += icbmInimigo.vy;

            let distToBunker = Math.hypot(icbmInimigo.x - 425, icbmInimigo.y - 360);
            document.getElementById('txt-target').innerText = `${Math.round(distToBunker * 10)}m`;

            // Impacto no Solo / Bunker
            if (icbmInimigo.y >= 360) {
                icbmInimigo.active = false;
                icbmInimigo = null;
                detonacaoOcorreu = true;
                
                // Cálculo de Danos
                if (mhdAtivo) {
                    integridadeEstrutura = Math.max(0, integridadeEstrutura - 15);
                    tremorG = 1.8;
                    pressaoAtm = 2.4;
                    adicionarLog("💥 [IMPACTO] Campo MHD absorveu 85% do pulso térmico e cinético!");
                } else {
                    integridadeEstrutura = Math.max(0, integridadeEstrutura - 80);
                    tremorG = 8.5;
                    pressaoAtm = 12.0;
                    adicionarLog("☣️ [CRÍTICO] Impacto direto de ICBM sem escudo MHD! Danos estruturais massivos!");
                }

                const strEl = document.getElementById('txt-struct');
                strEl.innerText = `${Math.round(integridadeEstrutura)}%`;
                strEl.className = integridadeEstrutura > 40 ? "status-orange" : "status-red";
            }
        }

        // Expandir Detonação Nuclear
        if (detonacaoOcorreu && raioExplosaoNuke < 180) {
            raioExplosaoNuke += 3;
            tremorG = Math.max(0, tremorG - 0.04);
            pressaoAtm = Math.max(1.0, pressaoAtm - 0.1);
            document.getElementById('txt-quake').innerText = `${tremorG.toFixed(2)} G`;
            document.getElementById('txt-press').innerText = `${pressaoAtm.toFixed(2)} atm`;
        }

        // Atualizar Interceptadores S.A.M.S
        for (let i = misseisSAMS.length - 1; i >= 0; i--) {
            let m = misseisSAMS[i];
            let dx = m.targetX - m.x;
            let dy = m.targetY - m.y;
            let dist = Math.hypot(dx, dy);

            if (dist < m.speed) {
                explosoes.push({ x: m.targetX, y: m.targetY, radius: 2, maxRadius: 40, active: true });
                misseisSAMS.splice(i, 1);
            } else {
                m.x += (dx / dist) * m.speed;
                m.y += (dy / dist) * m.speed;
            }
        }

        // Atualizar Explosões Defensivas e Colisão com ICBM
        for (let i = explosoes.length - 1; i >= 0; i--) {
            let exp = explosoes[i];
            exp.radius += 1.5;

            // Checar Intercepção da Ogiva
            if (icbmInimigo && icbmInimigo.active) {
                let distToICBM = Math.hypot(icbmInimigo.x - exp.x, icbmInimigo.y - exp.y);
                if (distToICBM < exp.radius) {
                    icbmInimigo.active = false;
                    icbmInimigo = null;
                    adicionarLog("🎯 [SUCESSO] Ogiva nuclear destruída em pleno ar pelo S.A.M.S!");
                    document.getElementById('txt-target').innerText = "0.00m";
                }
            }

            if (exp.radius >= exp.maxRadius) {
                explosoes.splice(i, 1);
            }
        }
    }

    // Desenhar Elementos na Tela
    function draw() {
        let shakeX = (Math.random() - 0.5) * tremorG * 8;
        let shakeY = (Math.random() - 0.5) * tremorG * 8;

        ctx.save();
        ctx.translate(shakeX, shakeY);

        // Fundo Céu/Espaço Cyber Navy
        ctx.fillStyle = '#030612';
        ctx.fillRect(-10, -10, canvas.width + 20, canvas.height + 20);

        // Grade Tactical Radar
        ctx.strokeStyle = 'rgba(0, 243, 255, 0.12)';
        ctx.lineWidth = 1;
        for (let x = 0; x < canvas.width; x += 40) {
            ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, 320); ctx.stroke();
        }
        for (let y = 0; y < 320; y += 40) {
            ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
        }

        // SROS - Raio Laser e Mapeamento Orbital
        if (icbmInimigo && icbmInimigo.active) {
            ctx.strokeStyle = '#ff007f';
            ctx.lineWidth = 1.5;
            ctx.shadowColor = '#ff007f';
            ctx.shadowBlur = 10;
            ctx.beginPath();
            ctx.moveTo(icbmInimigo.x, 0);
            ctx.lineTo(icbmInimigo.x, canvas.height);
            ctx.stroke();

            ctx.beginPath();
            ctx.arc(icbmInimigo.x, icbmInimigo.y, 20 + Math.sin(animTime * 5) * 5, 0, Math.PI * 2);
            ctx.stroke();
            ctx.shadowBlur = 0;
        }

        // Desenhar Camadas do Solo Subterrâneo
        let gradGeologico = ctx.createLinearGradient(0, 320, 0, 550);
        gradGeologico.addColorStop(0, '#111d3a');
        gradGeologico.addColorStop(0.3, '#0b1326');
        gradGeologico.addColorStop(1, '#050814');
        ctx.fillStyle = gradGeologico;
        ctx.fillRect(0, 320, canvas.width, 230);

        // Linha da Superfície do Solo
        ctx.strokeStyle = '#00f3ff';
        ctx.lineWidth = 3;
        ctx.beginPath();
        ctx.moveTo(0, 320);
        ctx.lineTo(canvas.width, 320);
        ctx.stroke();

        // DESENHO DO BUNKER E NÚCLEO SUBTERRÂNEO
        ctx.fillStyle = '#09152e';
        ctx.fillRect(350, 350, 150, 140);
        ctx.strokeStyle = integridadeEstrutura > 40 ? '#00f3ff' : '#ff0055';
        ctx.lineWidth = 3;
        ctx.strokeRect(350, 350, 150, 140);

        // Núcleo Vital do Bunker
        ctx.fillStyle = integridadeEstrutura > 40 ? '#39ff14' : '#ff0055';
        ctx.shadowColor = ctx.fillStyle;
        ctx.shadowBlur = 12;
        ctx.beginPath();
        ctx.arc(425, 420, 22, 0, Math.PI * 2);
        ctx.fill();
        ctx.shadowBlur = 0;

        ctx.fillStyle = '#ffffff';
        ctx.font = 'bold 10px Consolas';
        ctx.fillText("NÚCLEO BSL-4", 390, 424);

        // Silo do Interceptador S.A.M.S
        ctx.fillStyle = '#ffe600';
        ctx.fillRect(415, 320, 20, 30);

        // ESCUDO MAGNETOHIDRODINÂMICO (MHD)
        if (mhdAtivo) {
            ctx.strokeStyle = '#00f3ff';
            ctx.lineWidth = 4;
            ctx.shadowColor = '#00f3ff';
            ctx.shadowBlur = 20;
            ctx.beginPath();
            ctx.arc(425, 360, 160 + Math.sin(animTime * 4) * 8, Math.PI, 0, false);
            ctx.stroke();
            ctx.shadowBlur = 0;
        }

        // Desenhar Míssil ICBM Inimigo
        if (icbmInimigo && icbmInimigo.active) {
            ctx.fillStyle = '#ff0055';
            ctx.shadowColor = '#ff0055';
            ctx.shadowBlur = 15;
            ctx.beginPath();
            ctx.arc(icbmInimigo.x, icbmInimigo.y, 6, 0, Math.PI * 2);
            ctx.fill();

            // Cauda de Plasma do Míssil
            ctx.strokeStyle = '#ffe600';
            ctx.lineWidth = 3;
            ctx.beginPath();
            ctx.moveTo(icbmInimigo.x, icbmInimigo.y);
            ctx.lineTo(icbmInimigo.x - icbmInimigo.vx * 8, icbmInimigo.y - icbmInimigo.vy * 8);
            ctx.stroke();
            ctx.shadowBlur = 0;
        }

        // Desenhar Interceptadores S.A.M.S
        misseisSAMS.forEach(m => {
            ctx.fillStyle = '#39ff14';
            ctx.shadowColor = '#39ff14';
            ctx.shadowBlur = 10;
            ctx.beginPath();
            ctx.arc(m.x, m.y, 4, 0, Math.PI * 2);
            ctx.fill();

            ctx.strokeStyle = 'rgba(57, 255, 20, 0.5)';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.moveTo(425, 350);
            ctx.lineTo(m.x, m.y);
            ctx.stroke();
            ctx.shadowBlur = 0;
        });

        // Desenhar Explosões dos Interceptadores
        explosoes.forEach(exp => {
            ctx.fillStyle = 'rgba(57, 255, 20, 0.25)';
            ctx.strokeStyle = '#39ff14';
            ctx.lineWidth = 2;
            ctx.beginPath();
            ctx.arc(exp.x, exp.y, exp.radius, 0, Math.PI * 2);
            ctx.fill();
            ctx.stroke();
        });

        // Detonação Nuclear na Superfície
        if (detonacaoOcorreu && raioExplosaoNuke > 0) {
            ctx.fillStyle = 'rgba(255, 0, 85, 0.4)';
            ctx.strokeStyle = '#ff0055';
            ctx.lineWidth = 4;
            ctx.shadowColor = '#ff0055';
            ctx.shadowBlur = 30;
            ctx.beginPath();
            ctx.arc(425, 320, raioExplosaoNuke, 0, Math.PI * 2);
            ctx.fill();
            ctx.stroke();
            ctx.shadowBlur = 0;
        }

        // Retículo do Cursor (Mira Manual)
        if (mouseY < 320) {
            ctx.strokeStyle = '#39ff14';
            ctx.lineWidth = 1.5;
            ctx.beginPath();
            ctx.arc(mouseX, mouseY, 12, 0, Math.PI * 2);
            ctx.moveTo(mouseX - 18, mouseY); ctx.lineTo(mouseX + 18, mouseY);
            ctx.moveTo(mouseX, mouseY - 18); ctx.lineTo(mouseX, mouseY + 18);
            ctx.stroke();
        }

        ctx.restore();
    }

    // Loop de Execução da Simulação
    function gameLoop() {
        update();
        draw();
        requestAnimationFrame(gameLoop);
    }

    // Inicialização
    gameLoop();
</script>
</body>
</html>

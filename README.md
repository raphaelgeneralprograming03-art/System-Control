
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Systems Control: Nuclear Bunker Interactive Matrix</title>
    <style>
        :root {
            --hud-green: #39ff14;
            --hud-red: #ff3333;
            --hud-blue: #00e5ff;
            --hud-orange: #ff9900;
            --panel-bg: rgba(5, 12, 5, 0.95);
            --border-style: 1px solid rgba(57, 255, 20, 0.3);
        }

        body {
            margin: 0;
            padding: 10px;
            background-color: #010301;
            color: var(--hud-green);
            font-family: 'Courier New', Courier, monospace;
            display: flex;
            flex-direction: column;
            align-items: center;
            overflow: hidden;
            user-select: none;
        }

        .header-system {
            width: 100%;
            max-width: 1200px;
            display: flex;
            justify-content: space-between;
            border-bottom: var(--border-style);
            padding-bottom: 5px;
            margin-bottom: 10px;
            font-size: 11px;
            letter-spacing: 1px;
        }

        .main-frame {
            display: flex;
            gap: 15px;
            max-width: 1240px;
            width: 100%;
            justify-content: center;
        }

        .screen-container {
            position: relative;
            border: 2px solid var(--hud-green);
            box-shadow: 0 0 25px rgba(57, 255, 20, 0.15);
            border-radius: 4px;
        }

        canvas {
            display: block;
            background-color: #000;
        }

        .side-panel {
            background: var(--panel-bg);
            border: 1px solid var(--hud-green);
            border-radius: 4px;
            padding: 15px;
            width: 330px;
            box-sizing: border-box;
            display: flex;
            flex-direction: column;
        }

        h2 {
            font-size: 13px;
            margin: 12px 0 6px 0;
            text-transform: uppercase;
            border-bottom: var(--border-style);
            padding-bottom: 4px;
            color: #ffffff;
        }

        h2:first-of-type { margin-top: 0; }

        .telemetry-row {
            display: flex;
            justify-content: space-between;
            font-size: 11px;
            margin: 4px 0;
        }

        .status-ok { color: var(--hud-green); font-weight: bold; }
        .status-blue { color: var(--hud-blue); font-weight: bold; }
        .status-red { color: var(--hud-red); font-weight: bold; }
        .status-orange { color: var(--hud-orange); font-weight: bold; }

        .intel-box {
            margin-top: 10px;
            background: rgba(0, 0, 0, 0.6);
            border: 1px dashed rgba(57, 255, 20, 0.2);
            padding: 8px;
            font-size: 10px;
            height: 95px;
            overflow-y: auto;
            line-height: 1.4;
            color: #a0cca0;
        }

        .btn-action {
            width: 100%;
            padding: 8px;
            margin-top: 8px;
            font-weight: bold;
            font-family: monospace;
            cursor: pointer;
            border-radius: 4px;
            border: 1px solid;
            background: transparent;
            transition: all 0.2s;
        }

        #btn-icbm { border-color: var(--hud-red); color: var(--hud-red); }
        #btn-icbm:hover:not(:disabled) { background: var(--hud-red); color: #000; box-shadow: 0 0 10px var(--hud-red); }

        #btn-mhd { border-color: var(--hud-blue); color: var(--hud-blue); }
        #btn-mhd:hover { background: var(--hud-blue); color: #000; box-shadow: 0 0 10px var(--hud-blue); }

        .key-btn {
            background: #112211;
            border: 1px solid var(--hud-green);
            padding: 1px 4px;
            border-radius: 2px;
            color: #fff;
        }

        .controls-guide {
            margin-top: auto;
            border-top: var(--border-style);
            padding-top: 10px;
            font-size: 10px;
            color: #88af88;
        }
    </style>
</head>
<body>

    <div class="header-system">
        <div>INTERACTIVE ENGINE: BUNKER_MATRIX_v3 // OPERATIONAL CONTROL</div>
        <div>SROS LINK: ONLINE [MANUAL TARGET OVERRIDE]</div>
    </div>

    <div class="main-frame">
        <div class="screen-container">
            <canvas id="tacticalCanvas" width="850" height="550"></canvas>
        </div>

        <div class="side-panel">
            <h2>STRATEGIC FORTRESS</h2>
            <div class="telemetry-row"><span>BLINDAGEM NÚCLEO:</span><span id="txt-struct" class="status-ok">100%</span></div>
            <div class="telemetry-row"><span>ESTRESSE DE PRESSÃO:</span><span id="txt-press">1.00 atm</span></div>
            <div class="telemetry-row"><span>SENSOR SÍSMICO:</span><span id="txt-quake">0.00 G</span></div>

            <h2>MHD INTERACTIVE</h2>
            <div class="telemetry-row"><span>CONVERSOR MAGNÉTICO:</span><span class="status-blue" id="txt-mhd-status">DESATIVADO</span></div>
            <div class="telemetry-row"><span>CARGA ANEL (MHD):</span><span style="color: var(--hud-blue);" id="txt-mhd-load">0.00 MW</span></div>
            <button class="btn-action" id="btn-mhd" onclick="toggleMHD()">ATIVAR DEFESA SÍSMICA (ESPAÇO)</button>

            <h2>S.A.M.S AIR COUNTER</h2>
            <div class="telemetry-row"><span>INTERCEPTADORES AA:</span><span class="status-orange" id="txt-sams-ammo">3 / 3 CELLS</span></div>
            <div class="telemetry-row"><span>SISTEMA DE MIRA:</span><span style="color: #fff;">MANUAL (MOUSE)</span></div>

            <h2>SROS ORBITAL MATRIX</h2>
            <div class="telemetry-row"><span>FEED DA ZONA ZERO:</span><span class="status-ok" id="txt-sros-status">MONITORANDO</span></div>
            <div class="telemetry-row"><span>RÁDIO ALVO INTERCEPT:</span><span class="status-ok" id="txt-target">0.00m</span></div>
            
            <div class="intel-box" id="intel-logs">
                [SYSTEM] AGUARDANDO COMANDO DE LANÇAMENTO INIMIGO.<br>
                [DIRETRIZ] Use o MOUSE para mirar e CLIQUE no céu para interceptar ameaças com mísseis S.A.M.S.<br>
                [DIRETRIZ] Pressione ESPAÇO para ligar/desligar os indutores MHD.<br>
            </div>

            <button class="btn-action" id="btn-icbm" onclick="lancarICBM()">ATIVAR DISPARO DE ICBM INIMIGO</button>

            <div class="controls-guide">
                <strong>INTERFACE DE ENGENHARIA DISPONÍVEL:</strong><br>
                * Clique com o **Botão Esquerdo** no céu para tentar abater o míssil caindo.<br>
                * Mantenha o **MHD ativo** na hora exata do impacto para reduzir o choque.
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('tacticalCanvas');
        const ctx = canvas.getContext('2d');

        // --- SISTEMA SROS: RENDERIZAÇÃO PROCEDURAL DO INFRAVERMELHO DO SOLO ---
        const soloBackground = document.createElement('canvas');
        soloBackground.width = 400; soloBackground.height = 550;
        const sCtx = soloBackground.getContext('2d');
        let gradGeologico = sCtx.createLinearGradient(0, 0, 0, 550);
        gradGeologico.addColorStop(0, '#8f7b5b');   
        gradGeologico.addColorStop(0.2, '#5e4f37'); 
        gradGeologico.addColorStop(0.5, '#382f21'); 
        gradGeologico.addColorStop(1.0, '#17130c'); 
        sCtx.fillStyle = gradGeologico; sCtx.fillRect(0,0,400,550);
        for(let i=0; i<30000; i++) {
            sCtx.fillStyle = Math.random() > 0.5 ? 'rgba(0,0,0,0.15)' : 'rgba(255,255,255,0.04)';
            sCtx.fillRect(Math.random()*400, Math.random()*550, 1.5, 1.5);
        }

        // --- ENGENHARIA DE PROPRIEDADES INTERATIVAS ---
        let icbmInimigo = null; // Guardará o vetor do míssil nuclear quando lançado
        let mísseisSAMS = [];  // Lista de interceptadores do jogador
        let explosoesDefensivas = [];
        
        let mhdAtivo = false;
        let detonacaoNuclearOcorreu = false;
        let cronometroNuke = 0;
        let raioExplosao = 0;
        let tremorX = 0, tremorY = 0;
        let integridadeEstrutura = 100;
        let samsMunicao = 3;

        let mouseX = 0, mouseY = 0;

        // Captura de mouse para mira e ativação de cliques
        canvas.addEventListener('mousemove', e => {
            const rect = canvas.getBoundingClientRect();
            mouseX = e.clientX - rect.left;
            mouseY = e.clientY - rect.top;
        });

        canvas.addEventListener('mousedown', e => {
            if (e.button === 0 && mouseY < 120 && samsMunicao > 0 && !detonacaoNuclearOcorreu) {
                dispararSAMS(mouseX, mouseY);
            }
        });

        // Evento de Teclado (Tecla Espaço ativa/desativa o MHD)
        window.addEventListener('keydown', e => {
            if (e.code === 'Space') {
                e.preventDefault();
                toggleMHD();
            }
        });

        function toggleMHD() {
            if (detonacaoNuclearOcorreu && cronometroNuke >= 4.0) return;
            mhdAtivo = !mhdAtivo;
            const statusEl = document.getElementById('txt-mhd-status');
            const btnEl = document.getElementById('btn-mhd');
            
            if (mhdAtivo) {
                statusEl.innerText = "PRONTO / CARREGADO";
                statusEl.className = "status-blue";
                btnEl.style.background = "var(--hud-blue)";
                btnEl.style.color = "#000";
                adicionarLog("[MHD] Supercondutores ativados. Consumo estático: 45 MW.");
            } else {
                statusEl.innerText = "DESATIVADO";
                statusEl.className = "status-red";
                btnEl.style.background = "transparent";
                btnEl.style.color = "var(--hud-blue)";

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vertical Resistor Circuit</title>
    <style>
        :root {
            --wire-color: #333;
            --electron-color: #facc15; /* Yellow */
            --bg-color: #f8fafc;
            --panel-bg: #ffffff;
            --accent: #2563eb; /* Blue */
            --danger: #dc2626; /* Red for switch arm */
        }

        body {
            font-family: system-ui, -apple-system, sans-serif;
            background-color: var(--bg-color);
            color: #1e293b;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
            margin: 0;
        }

        h2 { margin-bottom: 5px; }
        p.subtitle { color: #64748b; margin-bottom: 20px; font-size: 0.95rem; }

        .container {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
            justify-content: center;
            width: 100%;
            max-width: 950px;
        }

        /* Controls Panel */
        .controls-card {
            flex: 1;
            min-width: 280px;
            background: var(--panel-bg);
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            height: fit-content;
        }

        .control-group { margin-bottom: 25px; }
        label { display: block; font-weight: 600; margin-bottom: 10px; }
        
        /* Voltage Slider */
        input[type=range] {
            width: 100%;
            accent-color: var(--accent);
            cursor: pointer;
        }

        /* Switches Rows */
        .switch-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 12px 0;
            border-bottom: 1px solid #f1f5f9;
        }
        .switch-row:last-child { border-bottom: none; }

        /* Toggle Button Style */
        .toggle-wrapper {
            position: relative;
            display: inline-block;
            width: 44px;
            height: 24px;
        }
        .toggle-wrapper input { opacity: 0; width: 0; height: 0; }
        .slider {
            position: absolute;
            cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background-color: #cbd5e1;
            border-radius: 34px;
            transition: .3s;
        }
        .slider:before {
            position: absolute;
            content: "";
            height: 18px; width: 18px;
            left: 3px; bottom: 3px;
            background-color: white;
            border-radius: 50%;
            transition: .3s;
        }
        input:checked + .slider { background-color: var(--accent); }
        input:checked + .slider:before { transform: translateX(20px); }

        /* Circuit Visualization Panel */
        .circuit-card {
            flex: 2;
            min-width: 340px;
            background: var(--panel-bg);
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        svg {
            width: 100%;
            max-width: 550px;
            height: auto;
            background: #fff;
            border: 1px solid #e2e8f0;
            border-radius: 8px;
        }

        /* SVG Styling */
        .wire {
            fill: none;
            stroke: var(--wire-color);
            stroke-width: 3;
            stroke-linecap: round;
            stroke-linejoin: round;
        }

        .component-body {
            fill: none;
            stroke: #000;
            stroke-width: 2;
        }

        /* Switch Arm Visuals */
        .switch-arm {
            stroke: var(--danger);
            stroke-width: 4;
            transform-origin: 0 0; /* To be set dynamically via JS/ID */
            transition: transform 0.2s ease-in-out;
        }

        /* Battery Symbol */
        .batt-long { stroke: #000; stroke-width: 2; }
        .batt-short { stroke: #000; stroke-width: 4; }

        /* Electron Animation */
        .electron-flow {
            fill: none;
            stroke: var(--electron-color);
            stroke-width: 3;
            stroke-dasharray: 6, 12; /* Dot size, gap size */
            opacity: 0; /* Hidden by default */
            pointer-events: none;
        }

        @keyframes flow-anim {
            to { stroke-dashoffset: -200; }
        }

        .flowing {
            opacity: 1;
            animation: flow-anim 1s linear infinite;
        }

        /* Digital Readout */
        .readout {
            margin-top: 20px;
            background: #111;
            color: #4ade80; /* Bright Green */
            font-family: 'Courier New', monospace;
            padding: 15px 25px;
            border-radius: 6px;
            border: 3px solid #444;
            text-align: center;
            width: 80%;
        }
        .readout-val { font-size: 1.8rem; font-weight: 700; }
        .readout-label { font-size: 0.8rem; color: #9ca3af; letter-spacing: 1px; }

    </style>
</head>
<body>

    <h2>Circuit Simulator: Parallel Branches</h2>
    <p class="subtitle">Vertical resistors & Series Ammeter. Toggle switches to control flow.</p>

    <div class="container">
        <!-- LEFT: Controls -->
        <div class="controls-card">
            <div class="control-group">
                <label>Battery Voltage: <span id="voltDisplay">12</span>V</label>
                <input type="range" id="voltInput" min="0" max="24" value="12">
            </div>

            <div class="control-group">
                <label>Branch Switches</label>
                
                <!-- Switch 1 -->
                <div class="switch-row">
                    <span>Branch 1 (20&Omega;)</span>
                    <label class="toggle-wrapper">
                        <input type="checkbox" id="chk1" checked>
                        <span class="slider"></span>
                    </label>
                </div>

                <!-- Switch 2 -->
                <div class="switch-row">
                    <span>Branch 2 (20&Omega;)</span>
                    <label class="toggle-wrapper">
                        <input type="checkbox" id="chk2">
                        <span class="slider"></span>
                    </label>
                </div>

                <!-- Switch 3 -->
                <div class="switch-row">
                    <span>Branch 3 (20&Omega;)</span>
                    <label class="toggle-wrapper">
                        <input type="checkbox" id="chk3">
                        <span class="slider"></span>
                    </label>
                </div>
            </div>
            
            <div style="font-size: 0.85rem; background: #eff6ff; padding: 10px; border-radius: 6px; color: #1e40af;">
                <strong>Note:</strong> When a switch is OPEN, the circuit path is broken. No current flows into that vertical branch.
            </div>
        </div>

        <!-- RIGHT: Visualization -->
        <div class="circuit-card">
            <svg viewBox="0 0 500 350">
                <defs>
                    <!-- Vertical Resistor Symbol Definition -->
                    <!-- Starts at 0,0 goes down 100 units total -->
                    <path id="resistor-v" d="M0,0 V20 L-8,25 L8,35 L-8,45 L8,55 L-8,65 L8,75 L0,80 V100" 
                          class="component-body" fill="none" />
                </defs>

                <!-- --- STATIC WIRING --- -->
                
                <!-- Main Loop: Battery side -->
                <line x1="50" y1="100" x2="50" y2="50" class="wire" />  <!-- Up from Batt -->
                <line x1="50" y1="50" x2="120" y2="50" class="wire" /> <!-- Top to Ammeter -->
                
                <!-- Ammeter Circle -->
                <circle cx="140" cy="50" r="20" fill="#fff" stroke="#333" stroke-width="2" />
                <text x="140" y="56" text-anchor="middle" font-weight="bold" font-size="14">A</text>

                <!-- Top Bus Bar (Connecting top of branches) -->
                <line x1="160" y1="50" x2="400" y2="50" class="wire" />
                
                <!-- Bottom Bus Bar (Return path) -->
                <line x1="50" y1="300" x2="400" y2="300" class="wire" />
                <line x1="50" y1="300" x2="50" y2="200" class="wire" /> <!-- Up to Batt -->

                <!-- Battery Symbol -->
                <line x1="30" y1="150" x2="70" y2="150" class="batt-long" /> <!-- Positive -->
                <line x1="40" y1="200" x2="60" y2="200" class="batt-short" /> <!-- Negative -->
                <line x1="50" y1="150" x2="50" y2="100" class="wire" /> <!-- Connect Batt + -->
                <text x="20" y="160" font-weight="bold" font-size="18">+</text>

                <!-- BRANCH 1 (Left) -->
                <line x1="200" y1="50" x2="200" y2="80" class="wire" /> <!-- Drop from bus -->
                <!-- Switch 1 dots -->
                <circle cx="200" cy="80" r="3" fill="#000" />
                <circle cx="200" cy="120" r="3" fill="#000" />
                <!-- Switch 1 Arm (Visual only) -->
                <line id="arm1" x1="200" y1="80" x2="200" y2="120" class="switch-arm" />
                <!-- Resistor 1 -->
                <use href="#resistor-v" x="200" y="120" /> 
                <!-- Connect to bottom -->
                <line x1="200" y1="220" x2="200" y2="300" class="wire" />

                <!-- BRANCH 2 (Middle) -->
                <line x1="300" y1="50" x2="300" y2="80" class="wire" />
                <circle cx="300" cy="80" r="3" fill="#000" />
                <circle cx="300" cy="120" r="3" fill="#000" />
                <line id="arm2" x1="300" y1="80" x2="300" y2="120" class="switch-arm" />
                <use href="#resistor-v" x="300" y="120" />
                <line x1="300" y1="220" x2="300" y2="300" class="wire" />

                <!-- BRANCH 3 (Right) -->
                <line x1="400" y1="50" x2="400" y2="80" class="wire" />
                <circle cx="400" cy="80" r="3" fill="#000" />
                <circle cx="400" cy="120" r="3" fill="#000" />
                <line id="arm3" x1="400" y1="80" x2="400" y2="120" class="switch-arm" />
                <use href="#resistor-v" x="400" y="120" />
                <line x1="400" y1="220" x2="400" y2="300" class="wire" />


                <!-- --- ELECTRON FLOW ANIMATION LAYERS --- -->
                
                <!-- 1. Main Supply (Battery -> Ammeter -> Split Point) -->
                <!-- Stops exactly at the first branch junction to be clean -->
                <path id="flow-supply" d="M50,150 L50,50 L200,50" class="electron-flow" />

                <!-- 2. Top Bus Extension (To branch 2 and 3) -->
                <!-- This flow exists if Switch 2 OR Switch 3 is on -->
                <line id="flow-top-2" x1="160" y1="50" x2="300" y2="50" class="electron-flow" />
                <line id="flow-top-3" x1="300" y1="50" x2="400" y2="50" class="electron-flow" />

                <!-- 3. Vertical Branches (Controlled strictly by their switch) -->
                <path id="flow-br1" d="M200,50 L200,300" class="electron-flow" />
                <path id="flow-br2" d="M300,50 L300,300" class="electron-flow" />
                <path id="flow-br3" d="M400,50 L400,300" class="electron-flow" />

                <!-- 4. Main Return (Bottom Bus -> Battery) -->
                <path id="flow-return" d="M200,300 L50,300 L50,200" class="electron-flow" />

            </svg>

            <div class="readout">
                <div class="readout-label">TOTAL CURRENT (AMMETER)</div>
                <div class="readout-val"><span id="ampVal">0.00</span> A</div>
            </div>
        </div>
    </div>

    <script>
        // Constants
        const R_OHMS = 20;

        // Inputs
        const voltageInput = document.getElementById('voltInput');
        const voltText = document.getElementById('voltDisplay');
        const ampText = document.getElementById('ampVal');
        
        // Switches Data
        // armId: SVG line for the red switch
        // flowId: SVG path for the vertical electron flow
        // x, y: pivot point for rotation
        const switches = [
            { check: document.getElementById('chk1'), armId: 'arm1', flowId: 'flow-br1', x: 200, y: 80 },
            { check: document.getElementById('chk2'), armId: 'arm2', flowId: 'flow-br2', x: 300, y: 80 },
            { check: document.getElementById('chk3'), armId: 'arm3', flowId: 'flow-br3', x: 400, y: 80 }
        ];

        // Shared Flows
        const flowSupply = document.getElementById('flow-supply');
        const flowReturn = document.getElementById('flow-return');
        const flowTop2 = document.getElementById('flow-top-2'); // Wire between Br1 and Br2
        const flowTop3 = document.getElementById('flow-top-3'); // Wire between Br2 and Br3

        function updateSimulation() {
            const voltage = parseFloat(voltageInput.value);
            voltText.innerText = voltage;

            let totalCurrent = 0;
            let activeCount = 0;

            // 1. Update Individual Branches
            switches.forEach((sw, index) => {
                const isClosed = sw.check.checked;
                const armEl = document.getElementById(sw.armId);
                const flowEl = document.getElementById(sw.flowId);

                // A. Switch Arm Rotation
                // Closed = 0 deg (vertical). Open = -35 deg (angled left)
                const rotation = isClosed ? 0 : -35;
                armEl.setAttribute("transform", `rotate(${rotation}, ${sw.x}, ${sw.y})`);

                // B. Branch Flow Logic
                if (isClosed) {
                    activeCount++;
                    const branchCurrent = (voltage / R_OHMS);
                    totalCurrent += branchCurrent;

                    if (voltage > 0) {
                        flowEl.classList.add('flowing');
                        // Calculate speed. Higher current = smaller duration
                        const speed = Math.max(0.2, 2 / (branchCurrent + 0.5)) + 's';
                        flowEl.style.animationDuration = speed;
                    } else {
                        flowEl.classList.remove('flowing');
                    }
                } else {
                    // STRICT: If switch is open, remove flowing class AND set opacity to 0 via class removal
                    flowEl.classList.remove('flowing');
                }
            });

            // 2. Update Meter
            ampText.innerText = totalCurrent.toFixed(2);

            // 3. Update Main Bus Lines (Supply and Return)
            if (activeCount > 0 && voltage > 0) {
                flowSupply.classList.add('flowing');
                flowReturn.classList.add('flowing');
                
                // Main wire speed reflects TOTAL current
                const mainSpeed = Math.max(0.2, 2 / (totalCurrent + 0.5)) + 's';
                flowSupply.style.animationDuration = mainSpeed;
                flowReturn.style.animationDuration = mainSpeed;
            } else {
                flowSupply.classList.remove('flowing');
                flowReturn.classList.remove('flowing');
            }

            // 4. Update Intermediate Top Bus Wires
            // flowTop2 should flow if Switch 2 OR Switch 3 is active
            const s2 = switches[1].check.checked;
            const s3 = switches[2].check.checked;

            if ((s2 || s3) && voltage > 0) {
                flowTop2.classList.add('flowing');
                flowTop2.style.animationDuration = '1s'; // Avg speed
            } else {
                flowTop2.classList.remove('flowing');
            }

            // flowTop3 should flow only if Switch 3 is active
            if (s3 && voltage > 0) {
                flowTop3.classList.add('flowing');
                flowTop3.style.animationDuration = '1s';
            } else {
                flowTop3.classList.remove('flowing');
            }
        }

        // Event Listeners
        voltageInput.addEventListener('input', updateSimulation);
        switches.forEach(sw => sw.check.addEventListener('change', updateSimulation));

        // Initialize
        updateSimulation();

    </script>
</body>
</html>

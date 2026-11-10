<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart IoT Packet Sniffer & Traffic Visualizer</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        darkBg: '#0b132b',
                        cardBg: 'rgba(28, 37, 65, 0.65)',
                        borderGlass: 'rgba(255, 255, 255, 0.08)',
                        mintWhite: '#e0f2fe',
                        forestGreen: '#10b981',
                        paleGreen: '#34d399',
                        amberAlert: '#f59e0b',
                        dangerRed: '#ef4444',
                        accentBlue: '#3b82f6',
                        purpleIoT: '#8b5cf6'
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['JetBrains Mono', 'Fira Code', 'monospace']
                    }
                }
            }
        }
    </script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js for data visualization -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Vis.js for Network Topology Graph -->
    <script type="text/javascript" src="https://unpkg.com/vis-network/standalone/umd/vis-network.min.js"></script>
    <style>
        body {
            background: radial-gradient(circle at 10% 20%, rgba(16, 185, 129, 0.05) 0%, transparent 40%),
                        radial-gradient(circle at 90% 80%, rgba(59, 130, 246, 0.05) 0%, transparent 40%),
                        #070d1e;
            color: #e0f2fe;
            font-family: 'Inter', sans-serif;
            min-height: 100vh;
        }

        .glass-panel {
            background: rgba(23, 32, 54, 0.75);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37);
        }

        .glass-button {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(4px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.2s ease;
        }

        .glass-button:hover {
            background: rgba(255, 255, 255, 0.12);
            border-color: rgba(52, 211, 153, 0.4);
            box-shadow: 0 0 12px rgba(52, 211, 153, 0.2);
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(11, 19, 43, 0.5);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(52, 211, 153, 0.3);
            border-radius: 3px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(52, 211, 153, 0.6);
        }

        /* Pulse Animations */
        @keyframes pulse-glow {
            0%, 100% { opacity: 1; filter: drop-shadow(0 0 8px rgba(16, 185, 129, 0.6)); }
            50% { opacity: 0.4; filter: drop-shadow(0 0 2px rgba(16, 185, 129, 0.2)); }
        }
        .live-dot {
            animation: pulse-glow 1.8s infinite ease-in-out;
        }

        /* Vis.js network canvas styling override */
        #network-graph canvas {
            cursor: pointer;
        }
    </style>
</head>
<body class="p-4 md:p-6 text-mintWhite antialiased">

    <!-- Top Navigation Header -->
    <header class="glass-panel rounded-2xl p-4 mb-6 flex flex-col md:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-3">
            <div class="p-3 bg-gradient-to-br from-emerald-500/20 to-teal-500/10 rounded-xl border border-emerald-500/30 text-emerald-400">
                <i class="fa-solid font-bold fa-network-wired text-2xl"></i>
            </div>
            <div>
                <h1 class="text-xl md:text-2xl font-bold tracking-wide text-white flex items-center gap-2">
                    NetIoT <span class="text-xs px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-400 border border-emerald-500/30">v2.4 PRO</span>
                </h1>
                <p class="text-xs text-slate-400">Smart IoT Packet Sniffer & Protocol Traffic Visualizer</p>
            </div>
        </div>

        <!-- Controls Bar -->
        <div class="flex flex-wrap items-center gap-3 w-full md:w-auto justify-end">
            <!-- Capture Toggle -->
            <button id="btn-toggle-capture" class="glass-button px-4 py-2 rounded-xl text-xs md:text-sm font-semibold flex items-center gap-2 text-emerald-400 hover:text-emerald-300">
                <span id="capture-indicator" class="w-2.5 h-2.5 rounded-full bg-emerald-400 live-dot"></span>
                <span id="capture-btn-text">Capturing Live</span>
            </button>

            <!-- Interface Selector -->
            <div class="relative">
                <select id="interface-select" class="glass-button appearance-none pl-3 pr-8 py-2 rounded-xl text-xs md:text-sm font-medium bg-slate-900/60 text-slate-200 outline-none focus:border-emerald-500/50">
                    <option value="wlan0">wlan0 (Wi-Fi 802.11ah)</option>
                    <option value="eth0">eth0 (Gigabit Ethernet)</option>
                    <option value="zigbee0">zigbee0 (IEEE 802.15.4)</option>
                </select>
                <i class="fa-solid fa-chevron-down absolute right-3 top-1/2 -translate-y-1/2 text-xs text-slate-400 pointer-events-none"></i>
            </div>

            <!-- Clear & Export Options -->
            <button id="btn-clear" title="Clear Buffers" class="glass-button p-2.5 rounded-xl text-slate-300 hover:text-rose-400">
                <i class="fa-solid fa-trash-can"></i>
            </button>

            <div class="flex rounded-xl overflow-hidden border border-slate-700/60">
                <button id="btn-export-json" class="bg-slate-800/80 hover:bg-slate-700/80 px-3 py-2 text-xs font-semibold text-slate-300 border-r border-slate-700/60">
                    <i class="fa-solid fa-file-code text-amber-400 mr-1"></i> JSON
                </button>
                <button id="btn-export-csv" class="bg-slate-800/80 hover:bg-slate-700/80 px-3 py-2 text-xs font-semibold text-slate-300">
                    <i class="fa-solid fa-file-csv text-emerald-400 mr-1"></i> CSV
                </button>
            </div>
        </div>
    </header>

    <!-- Main Grid Layout -->
    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- Top Overview Stats (4 Cards) -->
        <div class="lg:col-span-12 grid grid-cols-2 md:grid-cols-4 gap-4">
            <div class="glass-panel p-4 rounded-xl flex items-center justify-between">
                <div>
                    <p class="text-xs font-medium text-slate-400">Total Packets</p>
                    <h3 id="stat-total-packets" class="text-xl md:text-2xl font-bold font-mono text-white mt-1">0</h3>
                </div>
                <div class="p-3 bg-blue-500/10 text-blue-400 rounded-lg border border-blue-500/20">
                    <i class="fa-solid fa-box-archive text-lg"></i>
                </div>
            </div>
            <div class="glass-panel p-4 rounded-xl flex items-center justify-between">
                <div>
                    <p class="text-xs font-medium text-slate-400">Bandwidth Rate</p>
                    <h3 id="stat-bandwidth" class="text-xl md:text-2xl font-bold font-mono text-emerald-400 mt-1">0 KB/s</h3>
                </div>
                <div class="p-3 bg-emerald-500/10 text-emerald-400 rounded-lg border border-emerald-500/20">
                    <i class="fa-solid fa-gauge-high text-lg"></i>
                </div>
            </div>
            <div class="glass-panel p-4 rounded-xl flex items-center justify-between">
                <div>
                    <p class="text-xs font-medium text-slate-400">Active IoT Nodes</p>
                    <h3 id="stat-active-nodes" class="text-xl md:text-2xl font-bold font-mono text-purple-400 mt-1">8</h3>
                </div>
                <div class="p-3 bg-purple-500/10 text-purple-400 rounded-lg border border-purple-500/20">
                    <i class="fa-solid fa-microchip text-lg"></i>
                </div>
            </div>
            <div class="glass-panel p-4 rounded-xl flex items-center justify-between">
                <div>
                    <p class="text-xs font-medium text-slate-400">Anomalies Detected</p>
                    <h3 id="stat-threats" class="text-xl md:text-2xl font-bold font-mono text-amber-400 mt-1">0</h3>
                </div>
                <div class="p-3 bg-amber-500/10 text-amber-400 rounded-lg border border-amber-500/20">
                    <i class="fa-solid fa-shield-cat text-lg"></i>
                </div>
            </div>
        </div>

        <!-- Left Column: Charts & Network Topology Graph (7 Cols) -->
        <div class="lg:col-span-7 flex flex-col gap-6">

            <!-- Real-Time Bandwidth Chart -->
            <div class="glass-panel rounded-2xl p-5">
                <div class="flex items-center justify-between mb-4">
                    <div class="flex items-center gap-2">
                        <i class="fa-solid fa-chart-line text-emerald-400"></i>
                        <h2 class="text-sm font-semibold text-white tracking-wide">Live Bandwidth Throughput (KB/s)</h2>
                    </div>
                    <span class="text-xs text-slate-400 font-mono">Window: 30s</span>
                </div>
                <div class="h-56 relative w-full">
                    <canvas id="bandwidthChart"></canvas>
                </div>
            </div>

            <!-- Protocol Breakdown & IoT Device Topology -->
            <div class="grid grid-cols-1 md:grid-cols-12 gap-6">

                <!-- Protocol Distribution Chart -->
                <div class="md:col-span-5 glass-panel rounded-2xl p-5 flex flex-col justify-between">
                    <div class="flex items-center gap-2 mb-3">
                        <i class="fa-solid fa-chart-pie text-purple-400"></i>
                        <h2 class="text-sm font-semibold text-white">Protocol Share</h2>
                    </div>
                    <div class="h-48 relative flex items-center justify-center">
                        <canvas id="protocolChart"></canvas>
                    </div>
                </div>

                <!-- IoT Network Topology Graph -->
                <div class="md:col-span-7 glass-panel rounded-2xl p-5">
                    <div class="flex items-center justify-between mb-3">
                        <div class="flex items-center gap-2">
                            <i class="fa-solid fa-diagram-project text-teal-400"></i>
                            <h2 class="text-sm font-semibold text-white">Device Topology Map</h2>
                        </div>
                        <span class="text-[10px] bg-slate-800 text-slate-400 px-2 py-0.5 rounded border border-slate-700">Interactive</span>
                    </div>
                    <div id="network-graph" class="h-48 w-full rounded-xl bg-slate-950/40 border border-slate-800/80"></div>
                </div>

            </div>

            <!-- Threat & Anomaly Detection Log -->
            <div class="glass-panel rounded-2xl p-5">
                <div class="flex items-center justify-between mb-3">
                    <div class="flex items-center gap-2">
                        <i class="fa-solid fa-triangle-exclamation text-amber-400"></i>
                        <h2 class="text-sm font-semibold text-white">Security Anomalies & Threats</h2>
                    </div>
                    <button id="btn-clear-alerts" class="text-xs text-slate-400 hover:text-slate-200">Clear Alerts</button>
                </div>
                <div id="alerts-container" class="space-y-2 max-h-40 overflow-y-auto pr-1">
                    <!-- Alerts dynamically injected here -->
                    <div class="text-xs text-slate-500 italic text-center py-4">No security threats detected in current stream...</div>
                </div>
            </div>

        </div>

        <!-- Right Column: Live Packet Stream & Inspector (5 Cols) -->
        <div class="lg:col-span-5 flex flex-col gap-6">

            <!-- Live Packet Stream Panel -->
            <div class="glass-panel rounded-2xl p-5 flex-1 flex flex-col min-h-[500px]">
                
                <!-- Stream Filter Controls -->
                <div class="flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3 mb-4 pb-3 border-b border-slate-800">
                    <div class="flex items-center gap-2">
                        <i class="fa-solid fa-list-ul text-emerald-400"></i>
                        <h2 class="text-sm font-semibold text-white">Live Packet Feed</h2>
                    </div>
                    
                    <!-- Protocol Filter Pills -->
                    <div class="flex flex-wrap gap-1" id="filter-pills">
                        <button class="filter-pill active px-2.5 py-1 rounded-lg text-xs font-medium bg-emerald-500/20 text-emerald-300 border border-emerald-500/40" data-filter="ALL">ALL</button>
                        <button class="filter-pill px-2.5 py-1 rounded-lg text-xs font-medium bg-slate-800/70 text-slate-400 hover:text-slate-200" data-filter="MQTT">MQTT</button>
                        <button class="filter-pill px-2.5 py-1 rounded-lg text-xs font-medium bg-slate-800/70 text-slate-400 hover:text-slate-200" data-filter="CoAP">CoAP</button>
                        <button class="filter-pill px-2.5 py-1 rounded-lg text-xs font-medium bg-slate-800/70 text-slate-400 hover:text-slate-200" data-filter="DNS">DNS</button>
                        <button class="filter-pill px-2.5 py-1 rounded-lg text-xs font-medium bg-slate-800/70 text-slate-400 hover:text-slate-200" data-filter="HTTP">HTTP</button>
                    </div>
                </div>

                <!-- Stream Table Header -->
                <div class="grid grid-cols-12 gap-1 text-[11px] font-mono font-semibold text-slate-400 px-2 py-1 bg-slate-900/60 rounded-t-lg border-b border-slate-800">
                    <span class="col-span-2">Time</span>
                    <span class="col-span-2">Protocol</span>
                    <span class="col-span-3">Source</span>
                    <span class="col-span-3">Destination</span>
                    <span class="col-span-2 text-right">Length</span>
                </div>

                <!-- Live Packet Rows Container -->
                <div id="packet-feed-container" class="flex-1 overflow-y-auto space-y-1 my-2 font-mono text-xs max-h-[380px] pr-1">
                    <!-- Dynamic packet rows inserted here -->
                </div>

                <!-- Detailed Packet Inspection Modal/Drawer Area -->
                <div id="packet-inspector" class="mt-auto border-t border-slate-800 pt-3 bg-slate-950/60 rounded-xl p-3">
                    <div class="flex items-center justify-between mb-2">
                        <span class="text-xs font-semibold text-slate-300 flex items-center gap-1.5">
                            <i class="fa-solid fa-microscope text-purple-400"></i> Packet Inspector Payload
                        </span>
                        <span id="inspect-id" class="text-[10px] text-slate-500 font-mono">Select a packet line</span>
                    </div>
                    <pre id="inspect-payload" class="text-[11px] text-emerald-400/90 bg-slate-900 p-2.5 rounded border border-slate-800 font-mono whitespace-pre-wrap overflow-x-auto h-24">Click any packet from above stream to inspect raw details & decoded payload layers...</pre>
                </div>

            </div>

        </div>

    </div>

    <!-- Interactive JavaScript Engine -->
    <script>
        // --- Data Models & Constants ---
        const PROTOCOLS = ['MQTT', 'CoAP', 'DNS', 'HTTP', 'TCP', 'UDP', 'Zigbee (ZCL)'];
        const IOT_DEVICES = [
            { ip: '192.168.1.1', name: 'Smart Gateway (Hub)', type: 'gateway' },
            { ip: '192.168.1.105', name: 'Smart Thermostat', type: 'sensor' },
            { ip: '192.168.1.112', name: 'IP Security Cam #1', type: 'camera' },
            { ip: '192.168.1.118', name: 'Smart Door Lock', type: 'lock' },
            { ip: '192.168.1.140', name: 'Philips Hue Bridge', type: 'lighting' },
            { ip: '192.168.1.155', name: 'Smart Metering Node', type: 'sensor' },
            { ip: '192.168.1.201', name: 'Rogue Rogue-ESP32', type: 'threat' },
            { ip: '10.0.4.12', name: 'Cloud Broker (AWS)', type: 'cloud' }
        ];

        let isCapturing = true;
        let activeFilter = 'ALL';
        let packetCounter = 0;
        let totalBandwidthBytes = 0;
        let anomalyCount = 0;
        let capturedPackets = [];

        // Protocol Counters for Donut Chart
        const protocolCounts = {
            'MQTT': 14,
            'CoAP': 8,
            'DNS': 22,
            'HTTP': 11,
            'TCP': 35,
            'UDP': 18
        };

        // --- Initialize Charts ---
        // 1. Line Chart for Bandwidth
        const ctxBandwidth = document.getElementById('bandwidthChart').getContext('2d');
        const bandwidthChart = new Chart(ctxBandwidth, {
            type: 'line',
            data: {
                labels: Array(25).fill(''),
                datasets: [{
                    label: 'KB/s',
                    data: Array(25).fill(0),
                    borderColor: '#10b981',
                    backgroundColor: 'rgba(16, 185, 129, 0.1)',
                    borderWidth: 2,
                    fill: true,
                    tension: 0.3,
                    pointRadius: 0
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                animation: false,
                scales: {
                    x: { grid: { display: false }, ticks: { display: false } },
                    y: { 
                        grid: { color: 'rgba(255, 255, 255, 0.05)' },
                        ticks: { color: '#94a3b8', font: { size: 10 } }
                    }
                },
                plugins: { legend: { display: false } }
            }
        });

        // 2. Protocol Doughnut Chart
        const ctxProtocol = document.getElementById('protocolChart').getContext('2d');
        const protocolChart = new Chart(ctxProtocol, {
            type: 'doughnut',
            data: {
                labels: Object.keys(protocolCounts),
                datasets: [{
                    data: Object.values(protocolCounts),
                    backgroundColor: ['#8b5cf6', '#06b6d4', '#f59e0b', '#10b981', '#3b82f6', '#64748b'],
                    borderWidth: 0,
                    hoverOffset: 6
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                plugins: {
                    legend: {
                        position: 'right',
                        labels: { color: '#cbd5e1', font: { size: 10 }, boxWidth: 10 }
                    }
                },
                cutout: '70%'
            }
        });

        // --- Initialize Network Topology Graph using Vis.js ---
        const nodes = new vis.DataSet([
            { id: 1, label: 'Gateway\n192.168.1.1', shape: 'hex', color: '#10b981', font: { color: '#fff', size: 11 } },
            { id: 2, label: 'Cam #1', shape: 'dot', color: '#3b82f6' },
            { id: 3, label: 'Thermostat', shape: 'dot', color: '#8b5cf6' },
            { id: 4, label: 'Smart Lock', shape: 'dot', color: '#34d399' },
            { id: 5, label: 'Hue Bridge', shape: 'dot', color: '#f59e0b' },
            { id: 6, label: 'Rogue ESP32', shape: 'triangle', color: '#ef4444' }
        ]);

        const edges = new vis.DataSet([
            { from: 2, to: 1, color: { color: '#3b82f6' }, width: 2 },
            { from: 3, to: 1, color: { color: '#8b5cf6' }, width: 1 },
            { from: 4, to: 1, color: { color: '#34d399' }, width: 1 },
            { from: 5, to: 1, color: { color: '#f59e0b' }, width: 1 },
            { from: 6, to: 1, color: { color: '#ef4444' }, width: 3, dashes: true }
        ]);

        const container = document.getElementById('network-graph');
        const networkData = { nodes: nodes, edges: edges };
        const networkOptions = {
            physics: {
                barnesHut: { gravitationalConstant: -1200, springLength: 70 }
            },
            interaction: { zoomView: true, hover: true }
        };
        const network = new vis.Network(container, networkData, networkOptions);

        // --- Helper Generators ---
        function getRandomInt(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function generatePacket() {
            const proto = PROTOCOLS[Math.floor(Math.random() * PROTOCOLS.length)];
            const src = IOT_DEVICES[Math.floor(Math.random() * IOT_DEVICES.length)];
            let dst = IOT_DEVICES[Math.floor(Math.random() * IOT_DEVICES.length)];
            
            while (dst.ip === src.ip) {
                dst = IOT_DEVICES[Math.floor(Math.random() * IOT_DEVICES.length)];
            }

            const length = getRandomInt(64, 1420);
            const now = new Date();
            const timeStr = now.toTimeString().split(' ')[0] + '.' + String(now.getMilliseconds()).padStart(3, '0');

            // Generate mock payloads based on protocol
            let payload = '';
            let isAnomaly = false;
            let anomalyDetails = '';

            if (proto === 'MQTT') {
                payload = `TOPIC: "home/sensors/temp"\nQoS: 1, Retain: false\nPayload: {"temp_c": ${ (Math.random()*15 + 18).toFixed(1) }, "humidity": ${getRandomInt(40,65)}}`;
            } else if (proto === 'CoAP') {
                payload = `CON [0x7a2b] GET /light/status\nToken: 0x4189\nType: Confirmable`;
            } else if (proto === 'DNS') {
                payload = `Standard query 0x1a8f A api.iot-cloud-platform.com\nResponse: 54.210.12.90`;
            } else if (proto === 'HTTP') {
                if (src.type === 'threat' || Math.random() < 0.15) {
                    isAnomaly = true;
                    anomalyDetails = `Unencrypted HTTP POST sensitive creds from ${src.ip}`;
                    payload = `POST /admin/login HTTP/1.1\nHost: ${dst.ip}\nUser-Agent: python-requests/2.28\nAuthorization: Basic YWRtaW46YWRtaW4xMjM=`;
                } else {
                    payload = `GET /v1/telemetry HTTP/1.1\nHost: firmware.local\nAccept: application/json`;
                }
            } else if (proto === 'Zigbee (ZCL)') {
                payload = `Frame Control: Cluster Specific\nCommand: Toggle Light State (0x02)\nNode ID: 0x4892`;
            } else {
                if (Math.random() < 0.08) {
                    isAnomaly = true;
                    anomalyDetails = `High frequency TCP Port Scan activity from ${src.ip}`;
                    payload = `TCP SYN Flood Packet\nFlags: [SYN], Seq: 28491028\nDst Port: ${getRandomInt(20, 8080)}`;
                } else {
                    payload = `UDP Data Stream\nSrcPort: ${getRandomInt(1024, 60000)} -> DstPort: ${getRandomInt(1024, 60000)}\nChecksum: 0x9a8f [VALID]`;
                }
            }

            return {
                id: ++packetCounter,
                time: timeStr,
                protocol: proto,
                src: src.ip,
                srcName: src.name,
                dst: dst.ip,
                dstName: dst.name,
                length: length,
                payload: payload,
                isAnomaly: isAnomaly,
                anomalyDetails: anomalyDetails
            };
        }

        // --- Render Packet Row into Stream Table ---
        function renderPacketRow(packet) {
            const container = document.getElementById('packet-feed-container');

            // Apply Active Filtering
            if (activeFilter !== 'ALL' && !packet.protocol.toUpperCase().includes(activeFilter)) {
                return;
            }

            const row = document.createElement('div');
            row.className = `grid grid-cols-12 gap-1 text-[11px] px-2 py-1.5 rounded cursor-pointer transition-colors border-b border-slate-900/40 hover:bg-slate-800/60 ${
                packet.isAnomaly ? 'bg-amber-500/10 text-amber-300' : 'text-slate-300'
            }`;

            // Color badge map
            const protoColors = {
                'MQTT': 'bg-purple-500/20 text-purple-300 border-purple-500/30',
                'CoAP': 'bg-cyan-500/20 text-cyan-300 border-cyan-500/30',
                'DNS': 'bg-amber-500/20 text-amber-300 border-amber-500/30',
                'HTTP': 'bg-emerald-500/20 text-emerald-300 border-emerald-500/30',
                'TCP': 'bg-blue-500/20 text-blue-300 border-blue-500/30',
                'UDP': 'bg-slate-700/40 text-slate-300 border-slate-600/30'
            };

            const badgeClass = protoColors[packet.protocol] || 'bg-slate-800 text-slate-300 border-slate-700';

            row.innerHTML = `
                <span class="col-span-2 text-slate-400">${packet.time}</span>
                <span class="col-span-2"><span class="px-1.5 py-0.5 rounded text-[10px] font-semibold border ${badgeClass}">${packet.protocol}</span></span>
                <span class="col-span-3 truncate text-slate-200" title="${packet.srcName}">${packet.src}</span>
                <span class="col-span-3 truncate text-slate-200" title="${packet.dstName}">${packet.dst}</span>
                <span class="col-span-2 text-right font-mono">${packet.length} B</span>
            `;

            row.addEventListener('click', () => {
                inspectPacket(packet);
            });

            container.insertBefore(row, container.firstChild);

            // Limit DOM items to keep interface smooth
            if (container.children.length > 50) {
                container.removeChild(container.lastChild);
            }
        }

        // --- Inspect Specific Packet ---
        function inspectPacket(pkt) {
            document.getElementById('inspect-id').innerText = `Packet #${pkt.id} | ${pkt.src} -> ${pkt.dst}`;
            document.getElementById('inspect-payload').innerText = 
`[TIMESTAMP]: ${pkt.time}
[PROTOCOL ]: ${pkt.protocol}
[SOURCE   ]: ${pkt.src} (${pkt.srcName})
[DEST     ]: ${pkt.dst} (${pkt.dstName})
[LENGTH   ]: ${pkt.length} Bytes
------------------------------------------------
[DECODED APPLICATION PAYLOAD LAYER]:
${pkt.payload}`;
        }

        // --- Trigger Threat Alert Notification ---
        function triggerAnomalyAlert(pkt) {
            anomalyCount++;
            document.getElementById('stat-threats').innerText = anomalyCount;

            const alertsContainer = document.getElementById('alerts-container');
            
            // Remove empty placeholder if existing
            if (alertsContainer.children.length === 1 && alertsContainer.children[0].classList.contains('italic')) {
                alertsContainer.innerHTML = '';
            }

            const alertCard = document.createElement('div');
            alertCard.className = 'p-2.5 bg-amber-500/10 border border-amber-500/30 rounded-xl text-xs flex items-start justify-between gap-2 animate-pulse';
            alertCard.innerHTML = `
                <div class="flex items-start gap-2">
                    <i class="fa-solid fa-triangle-exclamation text-amber-400 mt-0.5"></i>
                    <div>
                        <span class="font-semibold text-amber-300">ALERT: ${pkt.anomalyDetails}</span>
                        <div class="text-[10px] text-slate-400 font-mono mt-0.5">Time: ${pkt.time} | Protocol: ${pkt.protocol}</div>
                    </div>
                </div>
                <span class="text-[9px] px-1.5 py-0.5 rounded bg-amber-500/20 text-amber-300 font-mono">HIGH</span>
            `;

            alertsContainer.insertBefore(alertCard, alertsContainer.firstChild);

            // Limit max alert elements
            if (alertsContainer.children.length > 10) {
                alertsContainer.removeChild(alertsContainer.lastChild);
            }
        }

        // --- Main Data Loop ---
        setInterval(() => {
            if (!isCapturing) return;

            // Generate 1 to 3 packets per tick for dynamic stream feeling
            const burst = getRandomInt(1, 3);
            let tickBytes = 0;

            for (let i = 0; i < burst; i++) {
                const packet = generatePacket();
                capturedPackets.push(packet);
                tickBytes += packet.length;

                // Limit local storage buffer
                if (capturedPackets.length > 500) capturedPackets.shift();

                // Update charts data structures
                if (protocolCounts[packet.protocol] !== undefined) {
                    protocolCounts[packet.protocol]++;
                }

                // Threat detection
                if (packet.isAnomaly) {
                    triggerAnomalyAlert(packet);
                }

                // Render in UI
                renderPacketRow(packet);
            }

            // Update Statistics
            document.getElementById('stat-total-packets').innerText = packetCounter.toLocaleString();
            
            const rateKB = (tickBytes / 1024).toFixed(1);
            document.getElementById('stat-bandwidth').innerText = `${rateKB} KB/s`;

            // Update Bandwidth Chart Realtime Data
            const bwData = bandwidthChart.data.datasets[0].data;
            bwData.shift();
            bwData.push(parseFloat(rateKB));
            bandwidthChart.update();

            // Update Doughnut Chart periodically
            protocolChart.data.datasets[0].data = Object.values(protocolCounts);
            protocolChart.update();

        }, 800);

        // --- UI Event Controls ---

        // Pause / Resume Stream
        document.getElementById('btn-toggle-capture').addEventListener('click', function() {
            isCapturing = !isCapturing;
            const indicator = document.getElementById('capture-indicator');
            const btnText = document.getElementById('capture-btn-text');

            if (isCapturing) {
                indicator.className = 'w-2.5 h-2.5 rounded-full bg-emerald-400 live-dot';
                btnText.innerText = 'Capturing Live';
                this.classList.remove('text-amber-400');
                this.classList.add('text-emerald-400');
            } else {
                indicator.className = 'w-2.5 h-2.5 rounded-full bg-amber-400';
                btnText.innerText = 'Paused';
                this.classList.remove('text-emerald-400');
                this.classList.add('text-amber-400');
            }
        });

        // Filter Pills Event Listeners
        document.querySelectorAll('.filter-pill').forEach(pill => {
            pill.addEventListener('click', function() {
                document.querySelectorAll('.filter-pill').forEach(p => {
                    p.classList.remove('active', 'bg-emerald-500/20', 'text-emerald-300', 'border', 'border-emerald-500/40');
                    p.classList.add('bg-slate-800/70', 'text-slate-400');
                });
                this.classList.add('active', 'bg-emerald-500/20', 'text-emerald-300', 'border', 'border-emerald-500/40');
                this.classList.remove('bg-slate-800/70', 'text-slate-400');

                activeFilter = this.getAttribute('data-filter');

                // Re-populate Feed with filtered data from buffer
                const feedContainer = document.getElementById('packet-feed-container');
                feedContainer.innerHTML = '';
                capturedPackets.slice(-30).forEach(pkt => renderPacketRow(pkt));
            });
        });

        // Clear Feed Button
        document.getElementById('btn-clear').addEventListener('click', () => {
            document.getElementById('packet-feed-container').innerHTML = '';
            document.getElementById('inspect-payload').innerText = 'Click any packet from above stream to inspect raw details & decoded payload layers...';
            document.getElementById('inspect-id').innerText = 'Select a packet line';
        });

        // Clear Security Alerts Button
        document.getElementById('btn-clear-alerts').addEventListener('click', () => {
            document.getElementById('alerts-container').innerHTML = '<div class="text-xs text-slate-500 italic text-center py-4">No security threats detected in current stream...</div>';
            anomalyCount = 0;
            document.getElementById('stat-threats').innerText = '0';
        });

        // Export Functionality (JSON)
        document.getElementById('btn-export-json').addEventListener('click', () => {
            if (capturedPackets.length === 0) {
                alert('No packet data captured yet to export.');
                return;
            }
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(capturedPackets, null, 2));
            const dlAnchor = document.createElement('a');
            dlAnchor.setAttribute("href", dataStr);
            dlAnchor.setAttribute("download", `iot_packets_${Date.now()}.json`);
            document.body.appendChild(dlAnchor);
            dlAnchor.click();
            dlAnchor.remove();
        });

        // Export Functionality (CSV)
        document.getElementById('btn-export-csv').addEventListener('click', () => {
            if (capturedPackets.length === 0) {
                alert('No packet data captured yet to export.');
                return;
            }
            let csvContent = "data:text/csv;charset=utf-8,ID,Timestamp,Protocol,Source,Destination,Length\n";
            capturedPackets.forEach(p => {
                csvContent += `${p.id},"${p.time}","${p.protocol}","${p.src}","${p.dst}",${p.length}\n`;
            });
            const encodedUri = encodeURI(csvContent);
            const dlAnchor = document.createElement('a');
            dlAnchor.setAttribute("href", encodedUri);
            dlAnchor.setAttribute("download", `iot_packets_${Date.now()}.csv`);
            document.body.appendChild(dlAnchor);
            dlAnchor.click();
            dlAnchor.remove();
        });
    </script>
</body>
</html>

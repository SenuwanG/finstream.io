<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FinStream Solutions - OCTAVE Security Assessment & SOC 2 Readiness</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <!-- Chosen Palette: Warm Neutrals with Deep Teal Accents -->
    <!-- Application Structure Plan: The application is structured as a single-page dashboard with a sidebar navigation to facilitate a non-linear exploration of the risk assessment. The structure logically flows from an Executive Summary (Dashboard) to granular Asset/Threat Profiles (OCTAVE Phase 1), followed by a visual Risk Matrix (OCTAVE Phase 3), and concludes with Mitigation & Controls for SOC 2 readiness. This task-oriented view allows stakeholders (like a line manager) to quickly grasp top-line metrics before diving into specific operational risks or financial implications. -->
    <!-- Visualization & Content Choices: Dashboard -> High-level metrics -> Dynamic Stats & Bar Chart -> Provides immediate overview of risk posture. Asset Profiles -> Detailed asset-threat mapping -> Interactive Master-Detail Layout -> Allows deep dive without cluttering the screen. Risk Matrix -> Visual risk prioritization -> Scatter Plot (Chart.js) -> Best for mapping probability vs. impact to highlight critical areas. Mitigation Controls -> Operational planning -> Sortable Table -> Clear view of costs vs. risk reduction for SOC 2 planning. NO SVG/Mermaid used. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->

    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #faf9f8; color: #292524; }
        .nav-active { background-color: #e7e5e4; border-left: 4px solid #0f766e; font-weight: 600; color: #111827; }
        .nav-item { border-left: 4px solid transparent; transition: all 0.2s ease-in-out; }
        .nav-item:hover { background-color: #f5f5f4; border-left: 4px solid #d6d3d1; }
        
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            margin-left: auto;
            margin-right: auto;
            height: 45vh;
            max-height: 500px;
            min-height: 300px;
        }
        
        @media (max-width: 768px) {
            .chart-container {
                height: 35vh;
                min-height: 250px;
            }
        }
        
        .glass-panel {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border: 1px solid #e7e5e4;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.05), 0 2px 4px -1px rgba(0, 0, 0, 0.03);
        }
        
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f5f5f4; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #d6d3d1; border-radius: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #a8a29e; }
    </style>
</head>
<body class="h-screen flex overflow-hidden">

    <aside class="w-64 bg-stone-50 border-r border-stone-200 flex flex-col justify-between hidden md:flex z-10">
        <div>
            <div class="p-6 border-b border-stone-200">
                <h1 class="text-xl font-bold text-teal-800 tracking-tight">FinStream<br><span class="text-sm font-medium text-stone-500">Security Assessment</span></h1>
            </div>
            <nav class="mt-4 flex flex-col gap-1">
                <button onclick="navigate('dashboard')" id="nav-dashboard" class="nav-item nav-active flex items-center px-6 py-3 text-sm text-stone-600 w-full text-left">
                    <span class="mr-3 text-lg">📊</span> Executive Dashboard
                </button>
                <button onclick="navigate('assets')" id="nav-assets" class="nav-item flex items-center px-6 py-3 text-sm text-stone-600 w-full text-left">
                    <span class="mr-3 text-lg">🖥️</span> Asset & Threat Profiles
                </button>
                <button onclick="navigate('matrix')" id="nav-matrix" class="nav-item flex items-center px-6 py-3 text-sm text-stone-600 w-full text-left">
                    <span class="mr-3 text-lg">🎯</span> Risk Matrix
                </button>
                <button onclick="navigate('controls')" id="nav-controls" class="nav-item flex items-center px-6 py-3 text-sm text-stone-600 w-full text-left">
                    <span class="mr-3 text-lg">🛡️</span> Mitigation & Controls
                </button>
            </nav>
        </div>
        <div class="p-6 border-t border-stone-200 bg-stone-100">
            <div class="text-xs text-stone-500 font-semibold uppercase tracking-wider mb-2">Audit Target</div>
            <div class="flex items-center gap-2">
                <span class="text-xl">📋</span>
                <span class="text-sm font-bold text-stone-700">SOC 2 Type II</span>
            </div>
            <div class="text-xs text-stone-400 mt-1">Framework: OCTAVE Allegro</div>
        </div>
    </aside>

    <div class="flex-1 flex flex-col h-screen overflow-hidden bg-stone-100">
        
        <header class="md:hidden bg-stone-50 border-b border-stone-200 p-4 flex justify-between items-center z-20">
            <h1 class="text-lg font-bold text-teal-800">FinStream Assess</h1>
            <select id="mobile-nav" onchange="navigate(this.value)" class="bg-white border border-stone-300 text-stone-700 text-sm rounded-md focus:ring-teal-500 focus:border-teal-500 block p-2">
                <option value="dashboard">📊 Dashboard</option>
                <option value="assets">🖥️ Asset Profiles</option>
                <option value="matrix">🎯 Risk Matrix</option>
                <option value="controls">🛡️ Controls</option>
            </select>
        </header>

        <main class="flex-1 overflow-y-auto custom-scrollbar p-4 md:p-8">
            
            <section id="section-dashboard" class="block space-y-6 max-w-7xl mx-auto">
                <div class="mb-8">
                    <h2 class="text-3xl font-bold text-stone-800 mb-2">Executive Dashboard</h2>
                    <p class="text-stone-600 max-w-4xl text-sm md:text-base leading-relaxed">
                        This section provides a high-level overview of FinStream Solutions' current security posture based on the recent OCTAVE risk assessment. Designed for executive review prior to our SOC 2 Type II audit, it aggregates critical metrics, total identified risks across our core financial infrastructure, and estimated remediation costs. Interact with the summary metrics to understand the scale of our security landscape.
                    </p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                    <div class="glass-panel p-6 rounded-xl border-t-4 border-t-teal-600">
                        <div class="text-stone-500 text-xs font-bold uppercase tracking-wider mb-1">Critical Assets</div>
                        <div class="text-3xl font-black text-stone-800" id="dash-assets-count">0</div>
                        <div class="text-xs text-stone-500 mt-2">Core Banking & PII</div>
                    </div>
                    <div class="glass-panel p-6 rounded-xl border-t-4 border-t-rose-500">
                        <div class="text-stone-500 text-xs font-bold uppercase tracking-wider mb-1">High/Critical Risks</div>
                        <div class="text-3xl font-black text-stone-800" id="dash-high-risks">0</div>
                        <div class="text-xs text-stone-500 mt-2">Requires immediate mitigation</div>
                    </div>
                    <div class="glass-panel p-6 rounded-xl border-t-4 border-t-amber-500">
                        <div class="text-stone-500 text-xs font-bold uppercase tracking-wider mb-1">Total Vulnerabilities</div>
                        <div class="text-3xl font-black text-stone-800" id="dash-vuln-count">0</div>
                        <div class="text-xs text-stone-500 mt-2">Across 4 asset classes</div>
                    </div>
                    <div class="glass-panel p-6 rounded-xl border-t-4 border-t-indigo-500">
                        <div class="text-stone-500 text-xs font-bold uppercase tracking-wider mb-1">Est. Control CAPEX/OPEX</div>
                        <div class="text-3xl font-black text-stone-800" id="dash-cost">$0</div>
                        <div class="text-xs text-stone-500 mt-2">Annualized projection</div>
                    </div>
                </div>

                <div class="glass-panel p-6 rounded-xl mt-6">
                    <h3 class="text-lg font-bold text-stone-800 mb-4">Risk Exposure by Asset (Inherent vs Residual)</h3>
                    <div class="chart-container">
                        <canvas id="dashboardRiskChart"></canvas>
                    </div>
                    <div class="mt-4 text-sm text-stone-600 bg-stone-50 p-3 rounded-lg border border-stone-200">
                        <strong>Insight:</strong> The Customer PII Repository presents the highest inherent risk due to the sensitivity of the data and strict regulatory requirements (GDPR/CCPA). Implementing the proposed SOC 2 controls significantly reduces the residual risk to an acceptable threshold.
                    </div>
                </div>
            </section>

            <section id="section-assets" class="hidden h-full flex flex-col max-w-7xl mx-auto">
                <div class="mb-6 flex-shrink-0">
                    <h2 class="text-3xl font-bold text-stone-800 mb-2">Asset & Threat Profiles</h2>
                    <p class="text-stone-600 max-w-4xl text-sm md:text-base leading-relaxed">
                        Following OCTAVE Phase 1, this section catalogs FinStream's critical information and systems assets. Select an asset from the list to explore its specific threat profile, identified vulnerabilities, and the potential business impact. This granular view helps technical teams and auditors understand the specific attack vectors we are prioritizing for our core banking and payment gateways.
                    </p>
                </div>

                <div class="flex flex-col lg:flex-row gap-6 flex-1 min-h-0">
                    <div class="w-full lg:w-1/3 flex flex-col min-h-0">
                        <div class="glass-panel rounded-xl overflow-hidden flex flex-col h-full">
                            <div class="p-4 border-b border-stone-200 bg-stone-50">
                                <h3 class="font-bold text-stone-800">Information & System Assets</h3>
                            </div>
                            <div class="overflow-y-auto flex-1 p-2" id="asset-list-container">
                            </div>
                        </div>
                    </div>
                    
                    <div class="w-full lg:w-2/3 flex flex-col min-h-0">
                        <div class="glass-panel rounded-xl p-6 h-full overflow-y-auto custom-scrollbar" id="asset-detail-container">
                            <div class="flex h-full items-center justify-center text-stone-400 flex-col">
                                <span class="text-4xl mb-3">👈</span>
                                <p>Select an asset from the list to view its threat profile.</p>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <section id="section-matrix" class="hidden space-y-6 max-w-7xl mx-auto">
                <div class="mb-8">
                    <h2 class="text-3xl font-bold text-stone-800 mb-2">Risk Analysis Matrix</h2>
                    <p class="text-stone-600 max-w-4xl text-sm md:text-base leading-relaxed">
                        Representing OCTAVE Phase 3, this interactive scatter plot maps identified threats based on their Probability of occurrence versus their Business Impact. The size of each bubble correlates to the overall Risk Score. This visual tool is crucial for prioritizing resource allocation, focusing immediately on threats situated in the top-right quadrant (High Probability, High Impact) to satisfy SOC 2 risk mitigation requirements.
                    </p>
                </div>

                <div class="glass-panel p-6 rounded-xl">
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="text-lg font-bold text-stone-800">Probability vs. Impact (Inherent Risk)</h3>
                        <div class="flex gap-2 text-sm">
                            <span class="flex items-center gap-1"><span class="w-3 h-3 rounded-full bg-rose-500"></span> Critical</span>
                            <span class="flex items-center gap-1"><span class="w-3 h-3 rounded-full bg-amber-500"></span> High</span>
                            <span class="flex items-center gap-1"><span class="w-3 h-3 rounded-full bg-teal-500"></span> Medium</span>
                        </div>
                    </div>
                    <div class="chart-container" style="height: 60vh; max-height: 600px;">
                        <canvas id="riskMatrixChart"></canvas>
                    </div>
                </div>
            </section>

            <section id="section-controls" class="hidden space-y-6 max-w-7xl mx-auto">
                <div class="mb-8">
                    <h2 class="text-3xl font-bold text-stone-800 mb-2">Mitigation Strategy & Controls</h2>
                    <p class="text-stone-600 max-w-4xl text-sm md:text-base leading-relaxed">
                        This section details the specific security controls proposed to mitigate the risks identified in the OCTAVE assessment. It provides a strategic roadmap for achieving SOC 2 Type II compliance. You can review the control descriptions, targeted assets, implementation costs, and the Trust Services Criteria (TSC) they address. Use the filter to analyze controls by their specific security domain.
                    </p>
                </div>

                <div class="glass-panel rounded-xl overflow-hidden">
                    <div class="p-4 bg-stone-50 border-b border-stone-200 flex flex-col sm:flex-row justify-between items-center gap-4">
                        <h3 class="font-bold text-stone-800">Proposed Security Controls</h3>
                        <select id="control-filter" onchange="renderControlsTable()" class="bg-white border border-stone-300 text-stone-700 text-sm rounded-md focus:ring-teal-500 focus:border-teal-500 p-2">
                            <option value="all">All Domains</option>
                            <option value="Security">Security</option>
                            <option value="Availability">Availability</option>
                            <option value="Confidentiality">Confidentiality</option>
                        </select>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-sm text-left text-stone-600">
                            <thead class="text-xs text-stone-700 uppercase bg-stone-100 border-b border-stone-200">
                                <tr>
                                    <th scope="col" class="px-6 py-3">Control ID / Name</th>
                                    <th scope="col" class="px-6 py-3">SOC 2 TSC Domain</th>
                                    <th scope="col" class="px-6 py-3">Targeted Threat</th>
                                    <th scope="col" class="px-6 py-3">Est. Annual Cost</th>
                                    <th scope="col" class="px-6 py-3 text-center">Status</th>
                                </tr>
                            </thead>
                            <tbody id="controls-table-body" class="divide-y divide-stone-200 bg-white">
                            </tbody>
                        </table>
                    </div>
                    <div class="p-4 bg-stone-50 border-t border-stone-200 text-right">
                        <span class="text-sm font-bold text-stone-600">Total Projected Investment: <span id="total-control-cost" class="text-teal-700 font-black text-lg">$0</span></span>
                    </div>
                </div>
            </section>

        </main>
    </div>

    <script>
        const appData = {
            assets: [
                {
                    id: "A1",
                    name: "Core Transaction Database",
                    type: "Information / Software",
                    brand: "PostgreSQL on Dialog iDC Cloud",
                    description: "Houses all immutable transactional ledgers, settlement records, and operational telemetry.",
                    value: "$15,000,000",
                    inherentRisk: 95,
                    residualRisk: 30,
                    threats: [
                        { id: "T1.1", name: "Ransomware Deployment", vulnerability: "Unpatched legacy dependencies.", probability: 3, impact: 5, riskScore: 15, severity: "Critical" },
                        { id: "T1.2", name: "Insider Data Exfiltration", vulnerability: "Misconfigured RBAC.", probability: 2, impact: 5, riskScore: 10, severity: "High" }
                    ]
                },
                {
                    id: "A2",
                    name: "Thales payShield 10K HSM",
                    type: "Hardware",
                    brand: "Thales Group",
                    description: "Performs mathematically intensive cryptographic key operations, PIN routing, and EMV tokenization.",
                    value: "$1,200,000",
                    inherentRisk: 80,
                    residualRisk: 15,
                    threats: [
                        { id: "T2.1", name: "Physical Tampering", vulnerability: "Circumvention of data center physical access.", probability: 1, impact: 5, riskScore: 5, severity: "Medium" },
                        { id: "T2.2", name: "Cryptographic Protocol Exploit", vulnerability: "Failure to execute firmware updates.", probability: 2, impact: 5, riskScore: 10, severity: "High" }
                    ]
                },
                {
                    id: "A3",
                    name: "Customer PII & Credentials",
                    type: "Information",
                    brand: "Encrypted Cloud Storage",
                    description: "Highly sensitive personal and financial data strictly governed by the PDPA.",
                    value: "$20,000,000",
                    inherentRisk: 90,
                    residualRisk: 25,
                    threats: [
                        { id: "T3.1", name: "Account Takeover (ATO)", vulnerability: "Inadequate encryption standards at rest.", probability: 4, impact: 4, riskScore: 16, severity: "Critical" },
                        { id: "T3.2", name: "AI-Enabled Phishing", vulnerability: "Extended data retention periods.", probability: 3, impact: 4, riskScore: 12, severity: "High" }
                    ]
                },
                {
                    id: "A4",
                    name: "3rd-Party Integration API",
                    type: "Software",
                    brand: "React.js / Java Spring Boot",
                    description: "API Gateway and load balancer interfacing with external merchant platforms.",
                    value: "$8,500,000",
                    inherentRisk: 85,
                    residualRisk: 35,
                    threats: [
                        { id: "T4.1", name: "Volumetric DDoS", vulnerability: "Lack of dynamic rate-limiting controls.", probability: 4, impact: 4, riskScore: 16, severity: "Critical" },
                        { id: "T4.2", name: "Automated Botnet Scraping", vulnerability: "BOLA/IDOR flaws buried within the API source code.", probability: 4, impact: 3, riskScore: 12, severity: "High" }
                    ]
                },
                {
                    id: "A5",
                    name: "Security & DevOps Personnel",
                    type: "People",
                    brand: "Internal Staff / Contractors",
                    description: "Human actors possessing highly privileged, administrative access to the production environment.",
                    value: "N/A",
                    inherentRisk: 85,
                    residualRisk: 20,
                    threats: [
                        { id: "T5.1", name: "Spear-Phishing Credential Theft", vulnerability: "Inherent human susceptibility to social engineering.", probability: 3, impact: 5, riskScore: 15, severity: "Critical" },
                        { id: "T5.2", name: "Accidental Misconfiguration", vulnerability: "Weak passwords and lack of hardware MFA.", probability: 3, impact: 4, riskScore: 12, severity: "High" }
                    ]
                }
            ],
            controls: [
                { id: "C1", name: "FortiGate 100F NGFW", domain: "Security", target: "T4.1, T1.2", cost: 6500, status: "Planned" },
                { id: "C2", name: "SentinelOne Singularity XDR", domain: "Security", target: "T1.1, T3.1", cost: 18000, status: "In Progress" },
                { id: "C3", name: "Splunk Cloud SIEM", domain: "Security", target: "T1.2, T4.2", cost: 9315, status: "Planned" },
                { id: "C4", name: "Thales payShield Monitor", domain: "Security", target: "T2.1, T2.2", cost: 0, status: "Implemented" },
                { id: "C5", name: "Dialog Cloud BaaS", domain: "Availability", target: "T1.1, T5.2", cost: 10000, status: "In Progress" },
                { id: "C6", name: "Hardware MFA (YubiKey)", domain: "Security", target: "T3.2, T5.1", cost: 0, status: "Planned" },
                { id: "C7", name: "Continuous Pen Testing", domain: "Security", target: "T4.1, T1.1", cost: 10000, status: "Planned" },
                { id: "C8", name: "DLP & TLS 1.3 Encryption", domain: "Confidentiality", target: "T1.2, T3.1", cost: 0, status: "Implemented" },
                { id: "C9", name: "Security Awareness Training", domain: "Security", target: "T3.2, T5.1", cost: 0, status: "In Progress" },
                { id: "C10", name: "Cyber Liability Insurance", domain: "Security", target: "T1.1", cost: 5000, status: "Planned" },
                { id: "OPEX-1", name: "SOC 2 Type II Audit Readiness", domain: "Security", target: "Governance", cost: 50000, status: "Planned" },
                { id: "OPEX-2", name: "Dedicated Security Personnel", domain: "Security", target: "Operations", cost: 25000, status: "Implemented" }
            ]
        };

        const chartInstances = {};
        let currentActiveAsset = null;

        function navigate(sectionId) {
            document.querySelectorAll('section').forEach(sec => sec.classList.add('hidden'));
            document.querySelectorAll('.nav-item').forEach(nav => nav.classList.remove('nav-active'));
            
            document.getElementById(`section-${sectionId}`).classList.remove('hidden');
            
            const desktopNav = document.getElementById(`nav-${sectionId}`);
            if(desktopNav) desktopNav.classList.add('nav-active');
            
            document.getElementById('mobile-nav').value = sectionId;

            if (sectionId === 'dashboard') renderDashboard();
            if (sectionId === 'assets') renderAssetList();
            if (sectionId === 'matrix') renderMatrix();
            if (sectionId === 'controls') renderControlsTable();
        }

        function destroyChart(chartId) {
            if (chartInstances[chartId]) {
                chartInstances[chartId].destroy();
            }
        }

        function renderDashboard() {
            let totalVulns = 0;
            let highRisks = 0;
            appData.assets.forEach(a => {
                totalVulns += a.threats.length;
                a.threats.forEach(t => {
                    if (t.riskScore >= 10) highRisks++;
                });
            });

            const totalCost = appData.controls.reduce((sum, c) => sum + c.cost, 0);

            document.getElementById('dash-assets-count').innerText = appData.assets.length;
            document.getElementById('dash-high-risks').innerText = highRisks;
            document.getElementById('dash-vuln-count').innerText = totalVulns;
            document.getElementById('dash-cost').innerText = "$" + totalCost.toLocaleString();

            destroyChart('dashboardRiskChart');
            const ctx = document.getElementById('dashboardRiskChart').getContext('2d');
            
            chartInstances['dashboardRiskChart'] = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: appData.assets.map(a => a.name.length > 16 ? a.name.substring(0, 16) + '...' : a.name),
                    datasets: [
                        {
                            label: 'Inherent Risk Score',
                            data: appData.assets.map(a => a.inherentRisk),
                            backgroundColor: 'rgba(244, 63, 94, 0.8)', 
                            borderRadius: 4
                        },
                        {
                            label: 'Target Residual Risk (Post-Controls)',
                            data: appData.assets.map(a => a.residualRisk),
                            backgroundColor: 'rgba(15, 118, 110, 0.8)', 
                            borderRadius: 4
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'top', labels: { font: { family: 'Segoe UI' } } },
                        tooltip: {
                            callbacks: {
                                title: (context) => appData.assets[context[0].dataIndex].name
                            }
                        }
                    },
                    scales: {
                        y: { beginAtZero: true, max: 100, title: { display: true, text: 'Risk Score (0-100)' } },
                        x: { grid: { display: false } }
                    }
                }
            });
        }

        function renderAssetList() {
            const container = document.getElementById('asset-list-container');
            if (container.innerHTML.trim() !== "") return; 

            appData.assets.forEach(asset => {
                const div = document.createElement('div');
                div.className = `p-4 mb-2 rounded-lg border cursor-pointer transition-colors ${currentActiveAsset === asset.id ? 'bg-teal-50 border-teal-200' : 'bg-white border-stone-200 hover:bg-stone-50'}`;
                div.id = `asset-card-${asset.id}`;
                div.onclick = () => selectAsset(asset.id);
                
                let maxSeverity = "Medium";
                let hasCrit = asset.threats.some(t => t.severity === "Critical");
                let hasHigh = asset.threats.some(t => t.severity === "High");
                if (hasCrit) maxSeverity = "Critical";
                else if (hasHigh) maxSeverity = "High";

                const badgeColor = maxSeverity === "Critical" ? "bg-rose-100 text-rose-700" : (maxSeverity === "High" ? "bg-amber-100 text-amber-700" : "bg-teal-100 text-teal-700");

                div.innerHTML = `
                    <div class="flex justify-between items-start mb-1">
                        <h4 class="font-bold text-stone-800 text-sm">${asset.name}</h4>
                        <span class="text-[10px] font-bold px-2 py-1 rounded-full ${badgeColor}">${maxSeverity} Risk</span>
                    </div>
                    <div class="text-xs text-stone-500 mb-2">${asset.type} | ${asset.brand}</div>
                    <div class="text-xs text-stone-600 flex justify-between">
                        <span>Threats identified: <strong>${asset.threats.length}</strong></span>
                    </div>
                `;
                container.appendChild(div);
            });

            if(!currentActiveAsset && appData.assets.length > 0) {
                selectAsset(appData.assets[0].id);
            }
        }

        function selectAsset(assetId) {
            currentActiveAsset = assetId;
            
            document.querySelectorAll('[id^="asset-card-"]').forEach(el => {
                el.classList.remove('bg-teal-50', 'border-teal-200');
                el.classList.add('bg-white', 'border-stone-200');
            });
            const activeCard = document.getElementById(`asset-card-${assetId}`);
            if(activeCard) {
                activeCard.classList.remove('bg-white', 'border-stone-200');
                activeCard.classList.add('bg-teal-50', 'border-teal-200');
            }

            const asset = appData.assets.find(a => a.id === assetId);
            const detailContainer = document.getElementById('asset-detail-container');
            
            let threatsHTML = asset.threats.map(t => {
                const sColor = t.severity === "Critical" ? "bg-rose-50 border-rose-200 text-rose-800" : 
                              (t.severity === "High" ? "bg-amber-50 border-amber-200 text-amber-800" : "bg-teal-50 border-teal-200 text-teal-800");
                return `
                <div class="border rounded-lg p-4 mb-3 ${sColor}">
                    <div class="flex justify-between items-center mb-2">
                        <h5 class="font-bold text-sm">🚨 ${t.name}</h5>
                        <span class="text-xs font-bold px-2 py-1 bg-white rounded shadow-sm">Risk Score: ${t.riskScore}</span>
                    </div>
                    <div class="text-sm mb-2"><strong>Vulnerability:</strong> ${t.vulnerability}</div>
                    <div class="flex gap-4 text-xs mt-3 pt-3 border-t border-black/10">
                        <div><strong>Probability:</strong> ${t.probability}/5</div>
                        <div><strong>Impact:</strong> ${t.impact}/5</div>
                    </div>
                </div>
                `;
            }).join("");

            detailContainer.innerHTML = `
                <div class="mb-6">
                    <div class="flex items-center gap-2 mb-2">
                        <span class="text-2xl">${asset.type === 'Information Asset' ? '🗄️' : '⚙️'}</span>
                        <h3 class="text-2xl font-bold text-stone-800">${asset.name}</h3>
                    </div>
                    <p class="text-stone-600 text-sm mb-4">${asset.description}</p>
                    
                    <div class="grid grid-cols-2 gap-4 mb-6">
                        <div class="bg-stone-50 p-3 rounded border border-stone-200">
                            <div class="text-xs text-stone-500 uppercase tracking-wide">Technology Stack</div>
                            <div class="font-semibold text-stone-800 text-sm mt-1">${asset.brand}</div>
                        </div>
                        <div class="bg-stone-50 p-3 rounded border border-stone-200">
                            <div class="text-xs text-stone-500 uppercase tracking-wide">Est. Asset Value</div>
                            <div class="font-semibold text-stone-800 text-sm mt-1">${asset.value}</div>
                        </div>
                    </div>
                </div>
                
                <div>
                    <h4 class="text-lg font-bold text-stone-800 mb-3 border-b pb-2">Identified Threats & Vulnerabilities</h4>
                    ${threatsHTML}
                </div>
            `;
        }

        function renderMatrix() {
            destroyChart('riskMatrixChart');
            const ctx = document.getElementById('riskMatrixChart').getContext('2d');
            
            const datasets = [];
            
            appData.assets.forEach(asset => {
                asset.threats.forEach(threat => {
                    let color = 'rgba(15, 118, 110, 0.6)'; 
                    if(threat.severity === 'High') color = 'rgba(245, 158, 11, 0.6)';
                    if(threat.severity === 'Critical') color = 'rgba(244, 63, 94, 0.6)';

                    datasets.push({
                        label: `${asset.id} - ${threat.name}`,
                        data: [{
                            x: threat.probability,
                            y: threat.impact,
                            r: threat.riskScore * 1.5 
                        }],
                        backgroundColor: color,
                        borderColor: color.replace('0.6', '1'),
                        borderWidth: 1,
                        assetName: asset.name,
                        vulnerability: threat.vulnerability
                    });
                });
            });

            chartInstances['riskMatrixChart'] = new Chart(ctx, {
                type: 'bubble',
                data: { datasets: datasets },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false },
                        tooltip: {
                            callbacks: {
                                title: (context) => {
                                    return context[0].dataset.label;
                                },
                                label: (context) => {
                                    const ds = context.dataset;
                                    return [
                                        `Asset: ${ds.assetName}`,
                                        `Risk Score: ${context.raw.r / 1.5}`,
                                        `Vuln: ${ds.vulnerability.length > 40 ? ds.vulnerability.substring(0,40)+'...' : ds.vulnerability}`
                                    ];
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            title: { display: true, text: 'Probability (1-5)', font: { weight: 'bold' } },
                            min: 0, max: 6,
                            ticks: { stepSize: 1 },
                            grid: { color: '#e7e5e4' }
                        },
                        y: {
                            title: { display: true, text: 'Business Impact (1-5)', font: { weight: 'bold' } },
                            min: 0, max: 6,
                            ticks: { stepSize: 1 },
                            grid: { color: '#e7e5e4' }
                        }
                    }
                }
            });
        }

        function renderControlsTable() {
            const filter = document.getElementById('control-filter').value;
            const tbody = document.getElementById('controls-table-body');
            tbody.innerHTML = '';
            
            let filteredControls = appData.controls;
            if(filter !== 'all') {
                filteredControls = filteredControls.filter(c => c.domain === filter);
            }

            let totalCost = 0;

            filteredControls.forEach(ctrl => {
                totalCost += ctrl.cost;
                const tr = document.createElement('tr');
                tr.className = "hover:bg-stone-50 transition-colors";
                
                let statusBadge = '';
                if(ctrl.status === 'Implemented') statusBadge = '<span class="bg-teal-100 text-teal-800 text-[10px] font-bold px-2 py-1 rounded">✓ Implemented</span>';
                else if(ctrl.status === 'In Progress') statusBadge = '<span class="bg-amber-100 text-amber-800 text-[10px] font-bold px-2 py-1 rounded">⏳ In Progress</span>';
                else statusBadge = '<span class="bg-stone-200 text-stone-800 text-[10px] font-bold px-2 py-1 rounded">📋 Planned</span>';

                tr.innerHTML = `
                    <td class="px-6 py-4 font-medium text-stone-900 border-b border-stone-100">
                        <div class="text-xs text-stone-500 mb-1">${ctrl.id}</div>
                        ${ctrl.name}
                    </td>
                    <td class="px-6 py-4 border-b border-stone-100">
                        <span class="inline-flex items-center gap-1"><span class="w-2 h-2 rounded-full ${ctrl.domain === 'Security' ? 'bg-indigo-500' : (ctrl.domain === 'Availability' ? 'bg-sky-500' : 'bg-fuchsia-500')}"></span> ${ctrl.domain}</span>
                    </td>
                    <td class="px-6 py-4 border-b border-stone-100 text-xs">${ctrl.target}</td>
                    <td class="px-6 py-4 border-b border-stone-100 font-mono text-sm">$${ctrl.cost.toLocaleString()}</td>
                    <td class="px-6 py-4 border-b border-stone-100 text-center">${statusBadge}</td>
                `;
                tbody.appendChild(tr);
            });

            document.getElementById('total-control-cost').innerText = "$" + totalCost.toLocaleString();
        }

        navigate('dashboard');
    </script>
</body>
</html>

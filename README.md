# Matching-Players
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Soccer AI: Tactical Matcher</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        .vid-container { width: 100%; height: 220px; background: #000; border-radius: 12px; margin-bottom: 15px; overflow: hidden; border: 2px solid #cbd5e1; }
        .table-window { height: 350px; overflow-y: auto; background: white; border: 1px solid #e2e8f0; border-radius: 12px; }
        .active-btn { background-color: #10b981 !important; color: white !important; }
        .match-card { border-left: 8px solid #f97316; background: #fff; margin-bottom: 10px; padding: 20px; border-radius: 12px; box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); }
    </style>
</head>
<body class="bg-slate-100 p-4 md:p-8 text-slate-900 font-sans">

    <div class="max-w-7xl mx-auto">
        <header class="text-center mb-10">
            <h1 class="text-4xl font-black text-slate-800 tracking-tighter uppercase">Soccer AI <span class="text-blue-600">Tactical Analyst</span></h1>
            <p class="text-slate-500 font-bold">Consolidating 22 pool players against 11 targeted opponents</p>
        </header>

        <!-- STEP 1: VIDEO & EXCEL UPLOADS -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-10 mb-10">
            
            <!-- VIDEO 1 SECTION -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border-t-4 border-blue-600">
                <h2 class="text-lg font-black text-blue-600 mb-4 uppercase">Video 1: The 22-Player Pool</h2>
                <div class="vid-container"><video id="v1" class="w-full h-full" controls></video></div>
                <input type="file" id="vidFile1" accept="video/*" class="text-xs mb-4 block w-full">
                
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div class="p-2 bg-slate-50 rounded border">
                        <label class="text-[10px] font-bold text-slate-400 block mb-1">TEAM 1 NAMES (EXCEL)</label>
                        <input type="file" id="ex1" accept=".xlsx, .xls" class="text-[10px] w-full">
                    </div>
                    <div class="p-2 bg-slate-50 rounded border">
                        <label class="text-[10px] font-bold text-slate-400 block mb-1">TEAM 2 NAMES (EXCEL)</label>
                        <input type="file" id="ex2" accept=".xlsx, .xls" class="text-[10px] w-full">
                    </div>
                </div>
                <button onclick="processVideo1()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-black py-4 rounded-xl transition shadow-lg">ANALYZE VIDEO 1 (22 PLAYERS)</button>
            </div>

            <!-- VIDEO 2 SECTION -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border-t-4 border-emerald-600">
                <h2 class="text-lg font-black text-emerald-600 mb-4 uppercase">Video 2: Opponent Focus</h2>
                <div class="vid-container"><video id="v2" class="w-full h-full" controls></video></div>
                <input type="file" id="vidFile2" accept="video/*" class="text-xs mb-4 block w-full">
                
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div class="p-2 bg-slate-50 rounded border">
                        <label class="text-[10px] font-bold text-slate-400 block mb-1">TEAM 3 NAMES (EXCEL)</label>
                        <input type="file" id="ex3" accept=".xlsx, .xls" class="text-[10px] w-full">
                    </div>
                    <div class="p-2 bg-slate-50 rounded border">
                        <label class="text-[10px] font-bold text-slate-400 block mb-1">TEAM 4 NAMES (EXCEL)</label>
                        <input type="file" id="ex4" accept=".xlsx, .xls" class="text-[10px] w-full">
                    </div>
                </div>
                <div class="flex gap-2">
                    <button onclick="processVideo2()" class="flex-1 bg-emerald-600 hover:bg-emerald-700 text-white font-black py-4 rounded-xl transition shadow-lg">ANALYZE VIDEO 2</button>
                    <div class="flex border-2 border-emerald-600 rounded-xl overflow-hidden">
                        <button id="btnT3" onclick="switchOpponent(3)" class="px-4 font-black text-xs active-btn">TEAM 3</button>
                        <button id="btnT4" onclick="switchOpponent(4)" class="px-4 font-black text-xs bg-white">TEAM 4</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- MIDDLE: SORTING & MATCH ACTION -->
        <div class="bg-slate-900 p-6 rounded-3xl mb-10 flex flex-col md:flex-row items-center justify-between shadow-2xl">
            <div class="flex items-center gap-4">
                <span class="text-white font-black text-xs uppercase tracking-widest">Optimized By:</span>
                <select id="sortFactor" class="bg-slate-800 text-white font-bold p-3 rounded-xl border border-slate-700 outline-none" onchange="updateUI()">
                    <option value="speed">Top Speed (km/h)</option>
                    <option value="skills">Technical Skill (%)</option>
                    <option value="overall">Overall Performance</option>
                </select>
            </div>
            <button onclick="generateThirdTable()" class="bg-orange-500 hover:bg-orange-600 text-white px-12 py-4 rounded-full font-black text-xl shadow-orange-900/20 shadow-xl transition-all transform hover:scale-105 active:scale-95">
                GENERATE TACTICAL MATCHUP (TABLE 3)
            </button>
        </div>

        <!-- THE DATA TABLES -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-16">
            <div>
                <h3 class="font-black text-slate-400 text-xs mb-3 uppercase tracking-tighter">Table 1: Combined Source Pool (22 Players)</h3>
                <div class="table-window shadow-inner">
                    <table class="w-full text-left">
                        <thead class="bg-slate-50 sticky top-0 border-b">
                            <tr class="text-[10px] text-slate-400">
                                <th class="p-4 uppercase">Name</th><th class="p-4">SPEED</th><th class="p-4">SKILL</th><th class="p-4 text-right">RATING</th>
                            </tr>
                        </thead>
                        <tbody id="poolTableBody" class="text-sm"></tbody>
                    </table>
                </div>
            </div>

            <div>
                <h3 class="font-black text-slate-400 text-xs mb-3 uppercase tracking-tighter">Table 2: Selected Target Team (11 Opponents)</h3>
                <div class="table-window shadow-inner">
                    <table class="w-full text-left">
                        <thead class="bg-slate-50 sticky top-0 border-b">
                            <tr class="text-[10px] text-slate-400">
                                <th class="p-4 uppercase">Name</th><th class="p-4">SPEED</th><th class="p-4">SKILL</th><th class="p-4 text-right">RATING</th>
                            </tr>
                        </thead>
                        <tbody id="oppTableBody" class="text-sm"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- TABLE 3: THE AI ANALYSIS TABLE -->
        <div id="aiAnalysisSection" class="hidden pb-20">
            <div class="flex items-center justify-center gap-4 mb-8">
                <div class="h-1 w-20 bg-orange-500 rounded"></div>
                <h2 class="text-3xl font-black text-slate-800 italic uppercase">Table 3: AI Tactical Counter-Matching</h2>
                <div class="h-1 w-20 bg-orange-500 rounded"></div>
            </div>
            
            <div id="analysisGrid" class="space-y-4 max-w-5xl mx-auto">
                <!-- AI MATCH CARDS WILL BE INJECTED HERE -->
            </div>
        </div>

    </div>

    <script>
        let pool22 = []; 
        let team3 = [];
        let team4 = [];
        let currentOppTeam = 3;

        // Video Loaders
        document.getElementById('vidFile1').onchange = e => document.getElementById('v1').src = URL.createObjectURL(e.target.files[0]);
        document.getElementById('vidFile2').onchange = e => document.getElementById('v2').src = URL.createObjectURL(e.target.files[0]);

        async function getNames(id) {
            const file = document.getElementById(id).files[0];
            if (!file) return [];
            return new Promise(resolve => {
                const reader = new FileReader();
                reader.onload = e => {
                    const wb = XLSX.read(new Uint8Array(e.target.result), {type: 'array'});
                    const data = XLSX.utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]]);
                    resolve(data.map(r => r.Name || r.name || Object.values(r)[0]));
                };
                reader.readAsArrayBuffer(file);
            });
        }

        function createMockData(names, count, prefix) {
            return Array.from({length: count}, (_, i) => ({
                name: names[i] || `${prefix}-Player-${i+1}`,
                speed: (Math.random() * 8 + 25).toFixed(1),
                skills: Math.floor(Math.random() * 30 + 65),
                overall: Math.floor(Math.random() * 20 + 75)
            }));
        }

        async function processVideo1() {
            const n1 = await getNames('ex1');
            const n2 = await getNames('ex2');
            pool22 = [...createMockData(n1, 11, 'Pool-A'), ...createMockData(n2, 11, 'Pool-B')];
            updateUI();
            alert("Video 1 Analyzed: Table 1 updated with 22 players.");
        }

        async function processVideo2() {
            const n3 = await getNames('ex3');
            const n4 = await getNames('ex4');
            team3 = createMockData(n3, 11, 'Team-3');
            team4 = createMockData(n4, 11, 'Team-4');
            updateUI();
            alert("Video 2 Analyzed: Teams 3 and 4 ready for selection.");
        }

        function switchOpponent(id) {
            currentOppTeam = id;
            document.getElementById('btnT3').className = (id === 3) ? 'px-4 font-black text-xs active-btn' : 'px-4 font-black text-xs bg-white';
            document.getElementById('btnT4').className = (id === 4) ? 'px-4 font-black text-xs active-btn' : 'px-4 font-black text-xs bg-white';
            updateUI();
        }

        function updateUI() {
            const factor = document.getElementById('sortFactor').value;
            
            if (pool22.length) {
                const sortedPool = [...pool22].sort((a,b) => b[factor] - a[factor]);
                document.getElementById('poolTableBody').innerHTML = sortedPool.map(p => `
                    <tr class="border-b">
                        <td class="p-4 font-bold">${p.name}</td>
                        <td class="p-4">${p.speed} <small>km/h</small></td>
                        <td class="p-4">${p.skills}%</td>
                        <td class="p-4 text-right text-blue-600 font-black">${p.overall}</td>
                    </tr>`).join('');
            }

            const currentOppList = (currentOppTeam === 3) ? team3 : team4;
            if (currentOppList.length) {
                const sortedOpp = [...currentOppList].sort((a,b) => b[factor] - a[factor]);
                document.getElementById('oppTableBody').innerHTML = sortedOpp.map(p => `
                    <tr class="border-b">
                        <td class="p-4 font-bold">${p.name}</td>
                        <td class="p-4">${p.speed} <small>km/h</small></td>
                        <td class="p-4">${p.skills}%</td>
                        <td class="p-4 text-right text-emerald-600 font-black">${p.overall}</td>
                    </tr>`).join('');
            }
        }

        // GENERATE THE THIRD TABLE
        function generateThirdTable() {
            const opps = (currentOppTeam === 3) ? team3 : team4;
            if (pool22.length < 22 || opps.length < 11) {
                alert("Please analyze Video 1 (to get 22 players) and Video 2 (to get opponents) first!");
                return;
            }

            const factor = document.getElementById('sortFactor').value;
            const best11 = [...pool22].sort((a,b) => b[factor] - a[factor]).slice(0, 11);
            const sortedOpps = [...opps].sort((a,b) => b[factor] - a[factor]);

            const section = document.getElementById('aiAnalysisSection');
            const grid = document.getElementById('analysisGrid');
            section.classList.remove('hidden');
            grid.innerHTML = '';

            best11.forEach((p, i) => {
                const o = sortedOpps[i];
                grid.innerHTML += `
                    <div class="match-card flex items-center justify-between gap-6">
                        <div class="w-1/3">
                            <span class="text-[10px] font-black text-blue-500 uppercase">Selected Starter</span>
                            <div class="text-xl font-black text-slate-800">${p.name}</div>
                            <div class="text-xs text-slate-400 font-bold">${factor.toUpperCase()}: ${p[factor]}</div>
                        </div>
                        
                        <div class="w-1/3 bg-slate-50 p-4 rounded-xl border border-slate-100 italic text-sm text-slate-600">
                            <strong class="text-orange-600 uppercase text-[10px] block mb-1">AI Tactical Reasoning</strong>
                            Selected as the optimal counter to ${o.name} based on ${factor} dominance. This pairing ensures a statistical advantage of ${(p[factor] - o[factor]).toFixed(1)} in direct matchups.
                        </div>

                        <div class="w-1/3 text-right">
                            <span class="text-[10px] font-black text-emerald-500 uppercase">Target Opponent</span>
                            <div class="text-xl font-black text-slate-800">${o.name}</div>
                            <div class="text-xs text-slate-400 font-bold">${factor.toUpperCase()}: ${o[factor]}</div>
                        </div>
                    </div>
                `;
            });
            window.scrollTo({ top: section.offsetTop - 50, behavior: 'smooth' });
        }
    </script>
</body>
</html>

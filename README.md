<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>FC25 Guri Pro - Euro & National Edition</title>
    <style>
        :root { --gold: #f3ad0e; --dark: #05070a; --panel: #11151f; --accent: #1e2533; --green: #27ae60; --red: #e74c3c; }
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
        body { background: var(--dark); color: white; font-family: 'Segoe UI', sans-serif; margin: 0; padding: 10px; display: flex; flex-direction: column; align-items: center; }
        .tabs { display: flex; gap: 5px; width: 100%; position: sticky; top: 0; z-index: 100; background: var(--dark); padding: 10px 0; overflow-x: auto; }
        .tab-btn { background: var(--panel); color: white; border: 1px solid #333; padding: 12px 10px; cursor: pointer; border-radius: 8px; font-weight: bold; font-size: 0.75em; flex: 1; min-width: 80px; text-align: center; }
        .tab-btn.active { background: var(--gold); color: black; border-color: var(--gold); }
        .content-section { display: none; width: 100%; flex-direction: column; align-items: center; }
        .content-section.active { display: flex; }
        .card { background: var(--panel); border-radius: 12px; width: 100%; border: 1px solid #333; margin-bottom: 20px; padding: 15px; }
        .config-row { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-bottom: 10px; }
        input, select { background: #000; border: 1px solid #444; color: white; padding: 12px; border-radius: 8px; width: 100%; font-size: 16px; }
        table { border-collapse: collapse; width: 100%; }
        th, td { padding: 10px 5px; text-align: center; border-bottom: 1px solid #222; font-size: 0.85em; }
        th { background: var(--accent); color: var(--gold); }
        .logo-img { width: 25px; height: 25px; object-fit: contain; vertical-align: middle; }
        .jogos-grid { display: flex; flex-direction: column; gap: 10px; width: 100%; }
        .jogo { display: flex; flex-wrap: wrap; align-items: center; justify-content: center; background: var(--panel); padding: 15px; border-radius: 12px; border: 1px solid #333; gap: 10px; }
        .jogo.finalizado { border-color: var(--green); background: #0a1f14; }
        .score-input { width: 55px !important; text-align: center; color: var(--gold); font-weight: bold; }
        .btn-main { background: var(--gold); color: black; border: none; padding: 18px; border-radius: 10px; font-weight: bold; cursor: pointer; width: 100%; font-size: 1em; margin-top: 10px; }
        .btn-ok { background: var(--green); border: none; color: white; padding: 10px; border-radius: 6px; flex: 1; font-weight: bold; }
        .btn-undo { background: var(--red); border: none; color: white; padding: 10px; border-radius: 6px; flex: 1; font-weight: bold; display: none; }
        .hall-item { display: flex; align-items: center; justify-content: space-between; padding: 15px; border-bottom: 1px solid #333; }
        .campeao-box { text-align: center; padding: 40px 20px; border: 4px solid var(--gold); border-radius: 20px; width: 100%; background: radial-gradient(circle, #1a1a1a 0%, #05070a 100%); }
    </style>
</head>
<body>

    <div class="tabs">
        <button class="tab-btn active" onclick="openTab('aba-grupo')">GRUPOS</button>
        <button class="tab-btn" onclick="openTab('aba-matamata')">MATA-MATA</button>
        <button class="tab-btn" onclick="openTab('aba-hall')">🎖️ HALL DA FAMA</button>
    </div>

    <div id="aba-grupo" class="content-section active">
        <div class="card">
            <h3 style="margin: 0 0 15px 0; text-align: center; color: var(--gold);">CAMPEONATO DOS GURI</h3>
            <div id="lista-config"></div>
            <button class="btn-main" onclick="iniciarCampeonato()">COMEÇAR TORNEIO</button>
        </div>
        <div class="card" style="overflow-x: auto; padding:0;">
            <table>
                <thead><tr><th>Pos</th><th>Equipe</th><th>P</th><th>V</th><th>GP</th><th>SG</th></tr></thead>
                <tbody id="corpo-tabela"></tbody>
            </table>
        </div>
        <div class="jogos-grid" id="container-jogos"></div>
    </div>

    <div id="aba-matamata" class="content-section">
        <div class="card">
            <h2 style="color:var(--gold); text-align:center;">SEMIFINAIS</h2>
            <div id="semi-container"></div>
            <hr style="border:0; border-top:1px solid #333; margin:20px 0;">
            <h2 style="color:var(--gold); text-align:center;">GRANDE FINAL</h2>
            <div id="final-container"></div>
        </div>
    </div>

    <div id="aba-hall" class="content-section">
        <div class="card">
            <h2 style="color:var(--gold); text-align:center;">🎖️ GALERIA DE CAMPEÕES</h2>
            <div id="lista-hall"></div>
            <button class="btn-main" style="background:#444; color:white; margin-top:30px; height:40px; padding:0;" onclick="limparHistorico()">LIMPAR HISTÓRICO</button>
        </div>
    </div>

    <div id="aba-vencedor" class="content-section">
        <div class="campeao-box">
            <div id="img-vencedor"></div>
            <h1 id="nome-campeao" style="color: var(--gold); margin: 10px 0;">---</h1>
            <p style="font-weight: bold; letter-spacing: 2px;">CAMPEÃO DO CAMPEONATO DOS GURI</p>
            <button class="btn-main" onclick="openTab('aba-hall')">VER HALL DA FAMA</button>
        </div>
    </div>

    <script>
        const timesFC25 = {
            // SELEÇÕES
            "Brasil": "6", "Argentina": "26", "França": "2", "Alemanha": "9", "Portugal": "27", "Espanha": "5", 
            "Inglaterra": "10", "Itália": "7", "Holanda": "11", "Bélgica": "1", "Uruguai": "12", "Croácia": "3",
            // CLUBES EUROPA
            "Real Madrid": "541", "Barcelona": "529", "Man City": "50", "Liverpool": "40", "Bayern Munich": "157",
            "PSG": "85", "Inter Milan": "505", "AC Milan": "489", "Juventus": "496", "Arsenal": "42",
            "Chelsea": "49", "Atletico Madrid": "530", "Bayer Leverkusen": "168", "Dortmund": "165", 
            "Napoli": "492", "Tottenham": "47", "Benfica": "190", "Porto": "212", "Ajax": "194"
        };

        let duplas = JSON.parse(localStorage.getItem('guri_duplas')) || [
            { nome: "Guri 1", time: "Real Madrid" }, { nome: "Guri 2", time: "Man City" },
            { nome: "Guri 3", time: "Brasil" }, { nome: "Guri 4", time: "França" }, { nome: "Guri 5", time: "Portugal" }
        ];

        let stats = {}; let classificados = [];

        function openTab(id) {
            document.querySelectorAll('.content-section').forEach(s => s.classList.remove('active'));
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            if(id === 'aba-matamata') prepararMataMata();
            if(id === 'aba-hall') renderHall();
        }

        function renderConfig() {
            document.getElementById('lista-config').innerHTML = duplas.map((d, i) => `
                <div class="config-row">
                    <input type="text" id="nome-${i}" value="${d.nome}">
                    <select id="time-${i}">${Object.keys(timesFC25).map(t => `<option value="${t}" ${d.time==t?'selected':''}>${t}</option>`).join('')}</select>
                </div>`).join('');
        }

        function iniciarCampeonato() {
            stats = {};
            for(let i=0; i<5; i++){
                const n = document.getElementById(`nome-${i}`).value;
                const t = document.getElementById(`time-${i}`).value;
                duplas[i] = { nome: n, time: t };
                stats[n] = { pontos: 0, v: 0, gp: 0, gc: 0, sg: 0, timeID: timesFC25[t] };
            }
            localStorage.setItem('guri_duplas', JSON.stringify(duplas));
            gerarJogosGrupo(); atualizarTabela();
        }

        function gerarJogosGrupo() {
            let html = '';
            for (let i = 0; i < 5; i++) {
                for (let j = i + 1; j < 5; j++) {
                    html += `<div class="jogo" id="jg-${i}-${j}">
                        <div style="flex:1; text-align:right; font-size:0.85em;">${duplas[i].nome}</div>
                        <input type="number" class="score-input" value="0" id="g1-${i}-${j}">
                        <span>X</span>
                        <input type="number" class="score-input" value="0" id="g2-${i}-${j}">
                        <div style="flex:1; font-size:0.85em;">${duplas[j].nome}</div>
                        <div style="display:flex; width:100%; gap:5px; margin-top:5px;">
                            <button class="btn-ok" id="ok-${i}-${j}" onclick="lancar(${i},${j},true)">OK</button>
                            <button class="btn-undo" id="un-${i}-${j}" onclick="lancar(${i},${j},false)">↩</button>
                        </div></div>`;
                }
            }
            document.getElementById('container-jogos').innerHTML = html;
        }

        function lancar(i, j, somar) {
            const n1 = duplas[i].nome; const n2 = duplas[j].nome;
            const g1 = parseInt(document.getElementById(`g1-${i}-${j}`).value) || 0;
            const g2 = parseInt(document.getElementById(`g2-${i}-${j}`).value) || 0;
            const mult = somar ? 1 : -1;
            stats[n1].gp += (g1*mult); stats[n1].gc += (g2*mult);
            stats[n2].gp += (g2*mult); stats[n2].gc += (g1*mult);
            if (g1 > g2) { stats[n1].pontos += (3*mult); stats[n1].v += mult; }
            else if (g1 < g2) { stats[n2].pontos += (3*mult); stats[n2].v += mult; }
            else { stats[n1].pontos += mult; stats[n2].pontos += mult; }
            stats[n1].sg = stats[n1].gp - stats[n1].gc; stats[n2].sg = stats[n2].gp - stats[n2].gc;
            document.getElementById(`jg-${i}-${j}`).classList.toggle('finalizado', somar);
            document.getElementById(`ok-${i}-${j}`).style.display = somar ? 'none' : 'block';
            document.getElementById(`un-${i}-${j}`).style.display = somar ? 'block' : 'none';
            atualizarTabela();
        }

        function atualizarTabela() {
            classificados = Object.keys(stats).map(n => ({ n, ...stats[n] })).sort((a,b) => b.pontos - a.pontos || b.sg - a.sg);
            document.getElementById('corpo-tabela').innerHTML = classificados.map((t, i) => `
                <tr><td>${i+1}º</td><td style="text-align:left"><img class="logo-img" src="https://media.api-sports.io/football/teams/${t.timeID}.png"> ${t.n}</td>
                <td style="color:var(--gold)">${t.pontos}</td><td>${t.v}</td><td>${t.gp}</td><td>${t.sg}</td></tr>`).join('');
        }

        function prepararMataMata() {
            if(classificados.length < 4) return;
            const s = classificados;
            document.getElementById('semi-container').innerHTML = [0,1].map(i => {
                const idx1 = i === 0 ? 0 : 1; const idx2 = i === 0 ? 3 : 2;
                return `<div class="jogo" id="s${i}">
                    <div style="flex:1; text-align:right; font-size:0.8em;"><img class="logo-img" src="https://media.api-sports.io/football/teams/${s[idx1].timeID}.png"> ${s[idx1].n}</div>
                    <input type="number" class="score-input" id="gs${i}-1"> X <input type="number" class="score-input" id="gs${i}-2">
                    <div style="flex:1; font-size:0.8em;">${s[idx2].n} <img class="logo-img" src="https://media.api-sports.io/football/teams/${s[idx2].timeID}.png"></div>
                    <button class="btn-ok" id="oks${i}" onclick="venceuSemi(${i}, ${idx1}, ${idx2}, true)">OK</button>
                    <button class="btn-undo" id="uns${i}" onclick="venceuSemi(${i}, ${idx1}, ${idx2}, false)">↩</button>
                </div>`;
            }).join('');
        }

        function venceuSemi(num, idx1, idx2, somar) {
            const g1 = parseInt(document.getElementById(`gs${num}-1`).value) || 0;
            const g2 = parseInt(document.getElementById(`gs${num}-2`).value) || 0;
            const vencedor = g1 > g2 ? classificados[idx1] : classificados[idx2];
            document.getElementById(`oks${num}`).style.display = somar ? 'none' : 'block';
            document.getElementById(`uns${num}`).style.display = somar ? 'block' : 'none';
            if(somar) {
                if(!document.getElementById('final-jogo')) {
                    document.getElementById('final-container').innerHTML = `<div class="jogo" id="final-jogo">
                        <div id="f-t0-box" style="flex:1; text-align:right; font-size:0.8em;"></div>
                        <input type="number" class="score-input" id="fg1"> X <input type="number" class="score-input" id="fg2">
                        <div id="f-t1-box" style="flex:1; font-size:0.8em;"></div>
                        <button class="btn-main" onclick="coroar()">SALVAR CAMPEÃO</button></div>`;
                }
                document.getElementById(`f-t${num}-box`).innerHTML = `<img class="logo-img" src="https://media.api-sports.io/football/teams/${vencedor.timeID}.png"> <b id="finalist-${num}" data-id="${vencedor.timeID}">${vencedor.n}</b>`;
            }
        }

        function coroar() {
            const g1 = parseInt(document.getElementById('fg1').value) || 0;
            const g2 = parseInt(document.getElementById('fg2').value) || 0;
            const vNome = g1 > g2 ? document.getElementById('finalist-0').innerText : document.getElementById('finalist-1').innerText;
            const vID = g1 > g2 ? document.getElementById('finalist-0').dataset.id : document.getElementById('finalist-1').dataset.id;
            const historico = JSON.parse(localStorage.getItem('guri_hall')) || [];
            historico.unshift({ nome: vNome, timeID: vID, data: new Date().toLocaleDateString('pt-BR') });
            localStorage.setItem('guri_hall', JSON.stringify(historico));
            document.getElementById('nome-campeao').innerText = vNome;
            document.getElementById('img-vencedor').innerHTML = `<img src="https://media.api-sports.io/football/teams/${vID}.png" style="width:80px">`;
            openTab('aba-vencedor');
        }

        function renderHall() {
            const historico = JSON.parse(localStorage.getItem('guri_hall')) || [];
            const container = document.getElementById('lista-hall');
            if(historico.length === 0) { container.innerHTML = '<p style="text-align:center; color:#555;">Sem campeões.</p>'; return; }
            container.innerHTML = historico.map(h => `
                <div class="hall-item">
                    <img src="https://media.api-sports.io/football/teams/${h.timeID}.png" style="width:35px">
                    <div style="flex:1; margin-left:10px;"><b>${h.nome}</b><br><small style="color:#888">${h.data}</small></div>
                    <span>🏆</span>
                </div>`).join('');
        }

        function limparHistorico() { if(confirm("Limpar Hall da Fama?")) { localStorage.removeItem('guri_hall'); renderHall(); } }
        window.onload = () => { renderConfig(); iniciarCampeonato(); };
    </script>
</body>
</html>

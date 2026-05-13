<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Simulador de Earn-Out — Incorporadora</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --navy: #0f1f3d;
    --navy-mid: #1a3260;
    --navy-light: #243d72;
    --gold: #c9a84c;
    --gold-light: #e8c878;
    --gold-pale: #f5e9c8;
    --cream: #faf8f3;
    --white: #ffffff;
    --text-primary: #0f1f3d;
    --text-secondary: #4a5568;
    --text-muted: #8898aa;
    --green: #1a7a5e;
    --green-bg: #e8f5f0;
    --red: #b91c1c;
    --red-bg: #fef2f2;
    --border: #e2ddd4;
    --shadow: 0 1px 3px rgba(15,31,61,0.08), 0 4px 16px rgba(15,31,61,0.04);
  }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--cream);
    color: var(--text-primary);
    min-height: 100vh;
    font-size: 14px;
    line-height: 1.6;
  }

  header {
    background: var(--navy);
    padding: 2rem 2.5rem;
    border-bottom: 3px solid var(--gold);
  }

  .header-inner {
    max-width: 1100px;
    margin: 0 auto;
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    gap: 1.5rem;
    flex-wrap: wrap;
  }

  header h1 {
    font-family: 'DM Serif Display', serif;
    font-size: 1.9rem;
    color: var(--white);
    font-weight: 400;
    letter-spacing: -0.01em;
    line-height: 1.2;
  }

  header h1 span {
    color: var(--gold-light);
  }

  .header-formula {
    background: rgba(255,255,255,0.07);
    border: 1px solid rgba(201,168,76,0.35);
    border-radius: 8px;
    padding: 0.6rem 1rem;
    font-size: 12px;
    color: rgba(255,255,255,0.75);
    font-family: 'DM Sans', monospace;
    white-space: nowrap;
  }

  .header-formula strong {
    color: var(--gold-light);
    font-weight: 500;
  }

  main {
    max-width: 1100px;
    margin: 0 auto;
    padding: 2rem 2rem 4rem;
  }

  /* METRIC CARDS */
  .metrics {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
    gap: 12px;
    margin-bottom: 2rem;
  }

  .metric {
    background: var(--white);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 1.1rem 1.25rem;
    box-shadow: var(--shadow);
  }

  .metric.accent {
    background: var(--navy);
    border-color: var(--navy);
  }

  .metric-label {
    font-size: 11px;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: 0.06em;
    margin-bottom: 6px;
    font-weight: 500;
  }

  .metric.accent .metric-label { color: rgba(255,255,255,0.55); }

  .metric-value {
    font-family: 'DM Serif Display', serif;
    font-size: 1.5rem;
    color: var(--text-primary);
    line-height: 1.1;
    margin-bottom: 4px;
  }

  .metric.accent .metric-value { color: var(--gold-light); }

  .metric-sub {
    font-size: 11px;
    color: var(--text-muted);
  }

  .metric.accent .metric-sub { color: rgba(255,255,255,0.45); }

  .green { color: var(--green); }
  .red   { color: var(--red); }

  /* CONTROLS */
  .controls-section {
    background: var(--white);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.5rem;
    margin-bottom: 1.5rem;
    box-shadow: var(--shadow);
  }

  .controls-section h2 {
    font-family: 'DM Serif Display', serif;
    font-size: 1rem;
    font-weight: 400;
    color: var(--navy);
    margin-bottom: 1.25rem;
    padding-bottom: 0.75rem;
    border-bottom: 1px solid var(--border);
  }

  .controls-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
    gap: 1.25rem;
  }

  .ctrl-group label {
    display: block;
    font-size: 12px;
    font-weight: 500;
    color: var(--text-secondary);
    text-transform: uppercase;
    letter-spacing: 0.05em;
    margin-bottom: 8px;
  }

  .ctrl-row {
    display: flex;
    align-items: center;
    gap: 10px;
  }

  input[type=range] {
    flex: 1;
    -webkit-appearance: none;
    height: 4px;
    background: var(--border);
    border-radius: 2px;
    outline: none;
    cursor: pointer;
  }

  input[type=range]::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 16px;
    height: 16px;
    border-radius: 50%;
    background: var(--navy);
    border: 2px solid var(--gold);
    cursor: pointer;
    transition: transform 0.15s;
  }

  input[type=range]::-webkit-slider-thumb:hover { transform: scale(1.15); }

  .ctrl-val {
    font-size: 13px;
    font-weight: 500;
    color: var(--navy);
    min-width: 58px;
    text-align: right;
  }

  select {
    width: 100%;
    padding: 9px 12px;
    border: 1px solid var(--border);
    border-radius: 7px;
    background: var(--cream);
    color: var(--text-primary);
    font-family: 'DM Sans', sans-serif;
    font-size: 13px;
    cursor: pointer;
    outline: none;
    transition: border-color 0.15s;
  }

  select:focus { border-color: var(--navy); }

  /* CHART */
  .chart-section {
    background: var(--white);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.5rem;
    margin-bottom: 1.5rem;
    box-shadow: var(--shadow);
  }

  .chart-section h2 {
    font-family: 'DM Serif Display', serif;
    font-size: 1rem;
    font-weight: 400;
    color: var(--navy);
    margin-bottom: 1rem;
  }

  .chart-wrap {
    position: relative;
    width: 100%;
    height: 300px;
  }

  .legend {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
    margin-top: 12px;
    font-size: 12px;
    color: var(--text-muted);
  }

  .legend-item {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .legend-line {
    width: 20px;
    height: 2px;
    display: inline-block;
  }

  /* REFERÊNCIAS */
  .ref-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 12px;
    margin-bottom: 1.5rem;
  }

  .ref-card {
    border-radius: 10px;
    padding: 1rem 1.25rem;
    border: 1px solid var(--border);
    background: var(--white);
    box-shadow: var(--shadow);
  }

  .ref-card.gold { border-left: 4px solid var(--gold); }
  .ref-card.silver { border-left: 4px solid #aaa; }
  .ref-card.info { border-left: 4px solid var(--navy-light); }

  .ref-label { font-size: 11px; text-transform: uppercase; letter-spacing: 0.06em; color: var(--text-muted); font-weight: 500; margin-bottom: 4px; }
  .ref-value { font-family: 'DM Serif Display', serif; font-size: 1.4rem; color: var(--text-primary); line-height: 1.1; margin-bottom: 3px; }
  .ref-sub { font-size: 11px; color: var(--text-muted); }

  /* TABLE */
  .table-section {
    background: var(--white);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.5rem;
    box-shadow: var(--shadow);
  }

  .table-section h2 {
    font-family: 'DM Serif Display', serif;
    font-size: 1rem;
    font-weight: 400;
    color: var(--navy);
    margin-bottom: 1rem;
  }

  .table-wrap {
    overflow-x: auto;
    max-height: 380px;
    overflow-y: auto;
    border: 1px solid var(--border);
    border-radius: 8px;
  }

  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 12.5px;
  }

  thead th {
    background: var(--navy);
    color: rgba(255,255,255,0.8);
    text-align: right;
    padding: 9px 12px;
    font-weight: 500;
    font-size: 11px;
    text-transform: uppercase;
    letter-spacing: 0.04em;
    position: sticky;
    top: 0;
    z-index: 1;
  }

  thead th:first-child { text-align: left; border-radius: 0; }

  tbody td {
    padding: 8px 12px;
    text-align: right;
    border-bottom: 1px solid var(--border);
    color: var(--text-secondary);
  }

  tbody td:first-child { text-align: left; color: var(--text-primary); font-weight: 500; }

  tbody tr:hover { background: var(--cream); }
  tbody tr:last-child td { border-bottom: none; }

  .pill {
    display: inline-block;
    font-size: 10px;
    padding: 2px 8px;
    border-radius: 99px;
    font-weight: 500;
  }
  .pill-g { background: var(--green-bg); color: var(--green); }
  .pill-r { background: var(--red-bg); color: var(--red); }

  .badge-unid {
    font-size: 10px;
    background: #e8edf5;
    color: var(--navy);
    border-radius: 4px;
    padding: 1px 5px;
    margin-left: 4px;
    font-weight: 500;
  }

  footer {
    text-align: center;
    margin-top: 2.5rem;
    font-size: 11px;
    color: var(--text-muted);
    padding: 1rem;
    border-top: 1px solid var(--border);
  }

  @media (max-width: 600px) {
    header { padding: 1.5rem 1.25rem; }
    main { padding: 1.25rem 1rem 3rem; }
    header h1 { font-size: 1.4rem; }
    .header-formula { display: none; }
  }
</style>
</head>
<body>

<header>
  <div class="header-inner">
    <div>
      <h1>Simulador de <span>Earn-Out</span><br>Incorporadora</h1>
    </div>
    <div class="header-formula">
      Saldo = <strong>−PI × (1,30)^t + V − C</strong> &nbsp;|&nbsp; Earn-out = <strong>75% × max(0, Saldo)</strong>
    </div>
  </div>
</header>

<main>

  <!-- METRICS -->
  <div class="metrics">
    <div class="metric accent">
      <div class="metric-label">PI — Principal</div>
      <div class="metric-value">R$ 7,84M</div>
      <div class="metric-sub">19 unidades · correção 30% a.a.</div>
    </div>
    <div class="metric accent">
      <div class="metric-label">Valor de mercado unitário</div>
      <div class="metric-value">R$ 720k</div>
      <div class="metric-sub">Receita potencial: R$ 13,68M</div>
    </div>
    <div class="metric">
      <div class="metric-label">Saldo final da operação</div>
      <div class="metric-value" id="card-saldo">—</div>
      <div class="metric-sub" id="card-saldo-sub"></div>
    </div>
    <div class="metric">
      <div class="metric-label">Earn-out incorporadora (75%)</div>
      <div class="metric-value" id="card-earnout">—</div>
      <div class="metric-sub" id="card-earnout-sub"></div>
    </div>
  </div>

  <!-- CONTROLS -->
  <div class="controls-section">
    <h2>Parâmetros da simulação</h2>
    <div class="controls-grid">
      <div class="ctrl-group">
        <label>Prazo de vendas (meses)</label>
        <div class="ctrl-row">
          <input type="range" min="3" max="36" step="1" value="12" id="sl-meses">
          <span class="ctrl-val" id="out-meses">12 m</span>
        </div>
      </div>
      <div class="ctrl-group">
        <label>Preço médio / unidade</label>
        <div class="ctrl-row">
          <input type="range" min="500" max="900" step="10" value="720" id="sl-preco">
          <span class="ctrl-val" id="out-preco">R$ 720k</span>
        </div>
      </div>
      <div class="ctrl-group">
        <label>Custo fixo mensal</label>
        <div class="ctrl-row">
          <input type="range" min="5" max="50" step="1" value="10" id="sl-custo">
          <span class="ctrl-val" id="out-custo">R$ 10k</span>
        </div>
      </div>
      <div class="ctrl-group">
        <label>Padrão de vendas</label>
        <select id="sl-padrao">
          <option value="uniforme">Uniforme</option>
          <option value="acelerado">Acelerado no início</option>
          <option value="lento">Lento no início</option>
          <option value="bloco">Concentrado no final</option>
        </select>
      </div>
    </div>
  </div>

  <!-- CHART -->
  <div class="chart-section">
    <h2>Evolução do saldo da operação</h2>
    <div class="chart-wrap">
      <canvas id="chart-main" role="img" aria-label="Evolução mensal do saldo da operação e componentes"></canvas>
    </div>
    <div class="legend" id="chart-legend"></div>
  </div>

  <!-- REFERÊNCIAS -->
  <div class="ref-grid">
    <div class="ref-card gold">
      <div class="ref-label">Referência fundo — otimista</div>
      <div class="ref-value">R$ 3,2M</div>
      <div class="ref-sub">Earn-out projetado em 12 meses</div>
    </div>
    <div class="ref-card silver">
      <div class="ref-label">Referência fundo — base</div>
      <div class="ref-value">R$ 1,9M</div>
      <div class="ref-sub">Cenário conservador</div>
    </div>
    <div class="ref-card info">
      <div class="ref-label">Mês em que saldo fica positivo</div>
      <div class="ref-value" id="val-be">—</div>
      <div class="ref-sub">Ponto de earn-out</div>
    </div>
    <div class="ref-card info">
      <div class="ref-label">Custo total no período</div>
      <div class="ref-value" id="val-ctotal">—</div>
      <div class="ref-sub">R$ 10k × meses corridos</div>
    </div>
  </div>

  <!-- TABLE -->
  <div class="table-section">
    <h2>Apuração mensal detalhada</h2>
    <div class="table-wrap">
      <table>
        <thead><tr>
          <th>Mês</th>
          <th>t (anos)</th>
          <th>PI corrigido</th>
          <th>V acumulado</th>
          <th>C acumulado</th>
          <th>Saldo</th>
          <th>Earn-out (75%)</th>
          <th>Status</th>
        </tr></thead>
        <tbody id="tbody"></tbody>
      </table>
    </div>
  </div>

</main>

<footer>
  Saldo = −PI × (1+30%)^t + V − C &nbsp;|&nbsp; Earn-out = 75% × max(0, Saldo) &nbsp;|&nbsp; PI = R$ 7.840.000 · 19 unidades · Custo fixo mensal
</footer>

<script>
const PI = 7840000;
const N = 19;
const TAXA = 0.30;
const PART = 0.75;
const REF_OTI = 3200000;
const REF_BASE = 1900000;
let chart;

function fmtM(v) {
  const abs = Math.abs(v), s = v < 0 ? '−' : '';
  if (abs >= 1e6) return s + 'R$ ' + (abs/1e6).toFixed(2).replace('.',',') + 'M';
  if (abs >= 1e3) return s + 'R$ ' + Math.round(abs/1e3).toLocaleString('pt-BR') + 'k';
  return s + 'R$ ' + Math.round(abs).toLocaleString('pt-BR');
}
function fmtBRL(v) {
  const s = v < 0 ? '−' : '';
  return s + 'R$ ' + Math.round(Math.abs(v)).toLocaleString('pt-BR');
}

function getWeights(padrao) {
  const w = [];
  for (let i = 0; i < N; i++) {
    const x = i / (N - 1);
    if (padrao === 'uniforme') w.push(1);
    else if (padrao === 'acelerado') w.push(Math.max(0.1, 1 - x * 0.9));
    else if (padrao === 'lento') w.push(0.1 + x * 0.9);
    else w.push(x < 0.65 ? 0.05 : 1);
  }
  const s = w.reduce((a,b)=>a+b,0);
  return w.map(x=>x/s);
}

function simulate() {
  const totalMeses = parseInt(document.getElementById('sl-meses').value);
  const precoUnit = parseInt(document.getElementById('sl-preco').value) * 1000;
  const custoMensal = parseInt(document.getElementById('sl-custo').value) * 1000;
  const padrao = document.getElementById('sl-padrao').value;

  document.getElementById('out-meses').textContent = totalMeses + ' m';
  document.getElementById('out-preco').textContent = 'R$ ' + (precoUnit/1000) + 'k';
  document.getElementById('out-custo').textContent = 'R$ ' + (custoMensal/1000) + 'k';

  const custoTotal = custoMensal * totalMeses;
  document.getElementById('val-ctotal').textContent = fmtM(custoTotal);

  const weights = getWeights(padrao);
  const mesesUnid = [];
  for (let i = 0; i < N; i++) {
    let cumW = 0;
    for (let j = 0; j <= i; j++) cumW += weights[j];
    mesesUnid.push(Math.max(1, Math.min(totalMeses, Math.round(cumW * totalMeses))));
  }

  const rows = [];
  let vAcum = 0, cAcum = 0, breakeven = null;
  for (let m = 1; m <= totalMeses; m++) {
    const t = m / 12;
    const unids = mesesUnid.filter(x => x === m).length;
    vAcum += unids * precoUnit;
    cAcum += custoMensal;
    const piCorr = PI * Math.pow(1 + TAXA, t);
    const saldo = -piCorr + vAcum - cAcum;
    const earnout = Math.max(0, saldo) * PART;
    if (!breakeven && saldo > 0) breakeven = m;
    rows.push({ m, t, piCorr, vAcum, cAcum, saldo, earnout, unids });
  }

  const last = rows[rows.length - 1];
  const sf = last.saldo, eo = last.earnout;

  const csEl = document.getElementById('card-saldo');
  csEl.textContent = fmtM(sf);
  csEl.className = 'metric-value ' + (sf > 0 ? 'green' : 'red');
  document.getElementById('card-saldo-sub').textContent = sf > 0 ? 'positivo — earn-out gerado' : 'negativo — sem earn-out';

  const ceEl = document.getElementById('card-earnout');
  ceEl.textContent = fmtM(eo);
  ceEl.className = 'metric-value ' + (eo > 0 ? 'green' : 'red');
  const dO = eo - REF_OTI, dB = eo - REF_BASE;
  document.getElementById('card-earnout-sub').innerHTML =
    `<span class="${dO>=0?'green':'red'}">${dO>=0?'+':''}${fmtM(dO)} vs otimista</span> &nbsp; <span class="${dB>=0?'green':'red'}">${dB>=0?'+':''}${fmtM(dB)} vs base</span>`;

  const beEl = document.getElementById('val-be');
  beEl.textContent = breakeven ? 'Mês ' + breakeven : 'Não atingido';
  beEl.style.color = breakeven ? 'var(--green)' : 'var(--red)';

  renderTable(rows);
  renderChart(rows);
}

function renderTable(rows) {
  document.getElementById('tbody').innerHTML = rows.map(r => {
    const pos = r.saldo > 0;
    return `<tr>
      <td>Mês ${r.m}${r.unids > 0 ? `<span class="badge-unid">+${r.unids}u</span>` : ''}</td>
      <td>${r.t.toFixed(3)}</td>
      <td>${fmtBRL(r.piCorr)}</td>
      <td>${fmtBRL(r.vAcum)}</td>
      <td>${fmtBRL(r.cAcum)}</td>
      <td style="font-weight:600;color:${pos?'var(--green)':'var(--red)'};">${fmtBRL(r.saldo)}</td>
      <td style="font-weight:600;color:${r.earnout>0?'var(--green)':'var(--text-muted)'};">${fmtBRL(r.earnout)}</td>
      <td><span class="pill ${pos?'pill-g':'pill-r'}">${pos?'positivo':'negativo'}</span></td>
    </tr>`;
  }).join('');
}

function renderChart(rows) {
  const labels = rows.map(r => 'M'+r.m);
  const saldos = rows.map(r => r.saldo);
  const piNeg = rows.map(r => -r.piCorr);
  const vLine = rows.map(r => r.vAcum - r.cAcum);
  const refOti = rows.map(() => REF_OTI / PART);
  const refBase = rows.map(() => REF_BASE / PART);

  const ctx = document.getElementById('chart-main');
  if (chart) chart.destroy();
  chart = new Chart(ctx, {
    type: 'line',
    data: {
      labels,
      datasets: [
        { label: 'Saldo da operação', data: saldos, borderColor: '#1a7a5e', backgroundColor: 'rgba(26,122,94,0.07)', fill: true, borderWidth: 2.5, pointRadius: rows.length > 20 ? 0 : 3, tension: 0.35, yAxisID: 'y' },
        { label: '−PI corrigido', data: piNeg, borderColor: '#b91c1c', borderDash: [5,3], borderWidth: 1.5, pointRadius: 0, tension: 0, yAxisID: 'y' },
        { label: 'V − C acumulado', data: vLine, borderColor: '#1a3260', borderDash: [3,3], borderWidth: 1.5, pointRadius: 0, tension: 0, yAxisID: 'y' },
        { label: 'Saldo mín. ref. otimista', data: refOti, borderColor: '#c9a84c', borderDash: [3,4], borderWidth: 1.2, pointRadius: 0, yAxisID: 'y' },
        { label: 'Saldo mín. ref. base', data: refBase, borderColor: '#aaaaaa', borderDash: [3,4], borderWidth: 1, pointRadius: 0, yAxisID: 'y' }
      ]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      interaction: { mode: 'index', intersect: false },
      plugins: {
        legend: { display: false },
        tooltip: { callbacks: { label: c => c.dataset.label + ': R$ ' + Math.round(c.raw).toLocaleString('pt-BR') } }
      },
      scales: {
        x: { ticks: { font: { size: 11, family: 'DM Sans' }, maxTicksLimit: 13, maxRotation: 0 }, grid: { display: false } },
        y: { ticks: { font: { size: 11, family: 'DM Sans' }, callback: v => { const a=Math.abs(v),s=v<0?'−':''; return s+'R$'+(a/1e6).toFixed(1)+'M'; } }, grid: { color: 'rgba(0,0,0,0.05)' } }
      }
    }
  });

  document.getElementById('chart-legend').innerHTML = `
    <div class="legend-item"><span class="legend-line" style="background:#1a7a5e;"></span>Saldo da operação</div>
    <div class="legend-item"><span class="legend-line" style="background:#b91c1c;border-top:2px dashed #b91c1c;"></span>−PI corrigido</div>
    <div class="legend-item"><span class="legend-line" style="border-top:2px dashed #1a3260;"></span>V − C acumulado</div>
    <div class="legend-item"><span class="legend-line" style="border-top:1px dashed #c9a84c;"></span>Saldo mín. ref. otimista</div>
    <div class="legend-item"><span class="legend-line" style="border-top:1px dashed #aaa;"></span>Saldo mín. ref. base</div>
  `;
}

['sl-meses','sl-preco','sl-custo'].forEach(id => document.getElementById(id).addEventListener('input', simulate));
document.getElementById('sl-padrao').addEventListener('change', simulate);
simulate();
</script>
</body>
</html>

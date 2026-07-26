<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controle Financeiro — Wictor</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap');

  :root {
    --bg:        #0f1117;
    --bg2:       #171a23;
    --bg3:       #1e2130;
    --border:    #2a2e3f;
    --text:      #e8eaf0;
    --muted:     #6b7280;
    --green:     #22c55e;
    --red:       #ef4444;
    --blue:      #3b82f6;
    --purple:    #a855f7;
    --yellow:    #f59e0b;
    --green-dim: rgba(34,197,94,.12);
    --red-dim:   rgba(239,68,68,.12);
    --blue-dim:  rgba(59,130,246,.12);
    --radius:    12px;
    --mono:      'JetBrains Mono', monospace;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Inter', sans-serif;
    font-size: 14px;
    min-height: 100vh;
  }

  /* ── LAYOUT ── */
  .shell { display: flex; min-height: 100vh; }

  nav {
    width: 220px; min-width: 220px;
    background: var(--bg2);
    border-right: 1px solid var(--border);
    padding: 24px 0;
    display: flex; flex-direction: column;
    position: sticky; top: 0; height: 100vh;
    overflow-y: auto;
  }

  .nav-logo {
    padding: 0 20px 24px;
    font-size: 15px; font-weight: 700;
    letter-spacing: -.3px;
    border-bottom: 1px solid var(--border);
    margin-bottom: 12px;
  }
  .nav-logo span { color: var(--blue); }

  .nav-item {
    display: flex; align-items: center; gap: 10px;
    padding: 10px 20px;
    cursor: pointer; border: none; background: none;
    color: var(--muted); font-size: 13.5px; font-family: 'Inter', sans-serif;
    width: 100%; text-align: left;
    border-radius: 0;
    transition: color .15s, background .15s;
  }
  .nav-item:hover { color: var(--text); background: var(--bg3); }
  .nav-item.active { color: var(--blue); background: var(--blue-dim); font-weight: 500; }
  .nav-item svg { flex-shrink: 0; }

  main { flex: 1; padding: 32px; overflow-x: hidden; }

  .page { display: none; }
  .page.active { display: block; }

  /* ── HEADER ── */
  .page-header { margin-bottom: 28px; }
  .page-header h1 { font-size: 22px; font-weight: 700; }
  .page-header p { color: var(--muted); margin-top: 4px; }

  /* ── CARDS ── */
  .cards-row { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 16px; margin-bottom: 28px; }

  .card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
  }
  .card-label { font-size: 11px; font-weight: 500; text-transform: uppercase; letter-spacing: .06em; color: var(--muted); margin-bottom: 8px; }
  .card-value { font-size: 24px; font-weight: 700; font-family: var(--mono); }
  .card-sub { font-size: 11px; color: var(--muted); margin-top: 4px; }
  .card.green .card-value { color: var(--green); }
  .card.red .card-value   { color: var(--red); }
  .card.blue .card-value  { color: var(--blue); }
  .card.yellow .card-value{ color: var(--yellow); }

  /* ── GRID 2 COLS ── */
  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px; }
  .grid2.wide { grid-template-columns: 2fr 1fr; }
  @media(max-width:900px){ .grid2, .grid2.wide { grid-template-columns: 1fr; } }

  /* ── CHART CARD ── */
  .chart-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 22px;
  }
  .chart-title { font-size: 13px; font-weight: 600; margin-bottom: 18px; color: var(--text); }
  .chart-wrap { position: relative; }

  /* ── TABLE ── */
  .table-card {
    background: var(--bg2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
  }
  .table-head { padding: 16px 20px; border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; }
  .table-head h3 { font-size: 13px; font-weight: 600; }

  table { width: 100%; border-collapse: collapse; }
  th { font-size: 11px; font-weight: 500; text-transform: uppercase; letter-spacing: .05em; color: var(--muted); padding: 12px 20px; text-align: left; border-bottom: 1px solid var(--border); }
  td { padding: 11px 20px; border-bottom: 1px solid var(--border); font-size: 13px; }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--bg3); }
  .mono { font-family: var(--mono); font-size: 13px; }
  .neg { color: var(--red); }
  .pos { color: var(--green); }
  .badge {
    display: inline-block; padding: 2px 8px; border-radius: 999px;
    font-size: 11px; font-weight: 500;
  }
  .badge-blue   { background: var(--blue-dim);  color: var(--blue); }
  .badge-green  { background: var(--green-dim); color: var(--green); }
  .badge-red    { background: var(--red-dim);   color: var(--red); }

  /* ── FORM ── */
  .form-section { margin-bottom: 28px; }
  .form-section-title {
    font-size: 11px; font-weight: 600; text-transform: uppercase; letter-spacing: .07em;
    color: var(--muted); margin-bottom: 14px; padding-bottom: 8px;
    border-bottom: 1px solid var(--border);
  }

  .form-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 14px; }

  .field { display: flex; flex-direction: column; gap: 6px; }
  .field label { font-size: 12px; font-weight: 500; color: var(--muted); }
  .field input, .field select {
    background: var(--bg3);
    border: 1px solid var(--border);
    color: var(--text);
    border-radius: 8px;
    padding: 9px 12px;
    font-size: 13px;
    font-family: 'Inter', sans-serif;
    outline: none;
    transition: border-color .15s;
  }
  .field input:focus, .field select:focus { border-color: var(--blue); }
  .field input::placeholder { color: var(--muted); }

  .btn {
    display: inline-flex; align-items: center; gap: 7px;
    padding: 10px 20px; border-radius: 8px;
    font-size: 13px; font-weight: 600; font-family: 'Inter', sans-serif;
    cursor: pointer; border: none; transition: opacity .15s, transform .1s;
  }
  .btn:hover { opacity: .88; }
  .btn:active { transform: scale(.98); }
  .btn-primary { background: var(--blue); color: #fff; }
  .btn-danger  { background: var(--red);  color: #fff; }
  .btn-ghost   { background: var(--bg3);  color: var(--text); border: 1px solid var(--border); }

  /* ── SALDO HIGHLIGHT ── */
  .saldo-bar {
    display: flex; align-items: center; justify-content: space-between;
    background: var(--bg2); border: 1px solid var(--border);
    border-radius: var(--radius); padding: 18px 24px; margin-bottom: 24px;
  }
  .saldo-bar-label { font-size: 12px; color: var(--muted); }
  .saldo-bar-value { font-size: 28px; font-weight: 700; font-family: var(--mono); }

  /* ── MONTH SELECTOR ── */
  .month-select-row { display: flex; align-items: center; gap: 12px; margin-bottom: 28px; flex-wrap: wrap; }
  .month-btn { padding: 7px 14px; border-radius: 8px; font-size: 13px; cursor: pointer; border: 1px solid var(--border); background: var(--bg2); color: var(--muted); font-family: 'Inter', sans-serif; transition: all .15s; }
  .month-btn:hover { color: var(--text); }
  .month-btn.active { background: var(--blue-dim); border-color: var(--blue); color: var(--blue); font-weight: 600; }

  /* ── PILL TABS ── */
  .tabs { display: flex; gap: 4px; background: var(--bg3); border-radius: 10px; padding: 4px; width: fit-content; margin-bottom: 24px; }
  .tab-btn { padding: 7px 16px; border-radius: 7px; border: none; background: none; color: var(--muted); font-size: 13px; font-family: 'Inter',sans-serif; cursor: pointer; transition: all .15s; }
  .tab-btn.active { background: var(--bg2); color: var(--text); font-weight: 500; box-shadow: 0 1px 4px rgba(0,0,0,.3); }

  /* ── LANÇAMENTO form rows ── */
  .lancamento-item {
    display: grid; grid-template-columns: 2fr 1fr 120px 28px; gap: 10px; align-items: center; margin-bottom: 8px;
  }
  .lancamento-item input, .lancamento-item select {
    background: var(--bg3); border: 1px solid var(--border);
    color: var(--text); border-radius: 8px; padding: 8px 11px; font-size: 13px; font-family: 'Inter',sans-serif; outline: none;
  }
  .lancamento-item input:focus, .lancamento-item select:focus { border-color: var(--blue); }
  .remove-btn { background: none; border: none; color: var(--muted); cursor: pointer; font-size: 18px; line-height: 1; padding: 2px 6px; border-radius: 4px; }
  .remove-btn:hover { color: var(--red); background: var(--red-dim); }

  .total-display {
    background: var(--bg3); border: 1px solid var(--border); border-radius: 8px;
    padding: 12px 16px; display: flex; justify-content: space-between; align-items: center;
    margin-top: 10px;
  }
  .total-display span { font-size: 12px; color: var(--muted); font-weight: 500; text-transform: uppercase; letter-spacing: .05em; }
  .total-display strong { font-family: var(--mono); font-size: 16px; }

  /* ── TOAST ── */
  #toast {
    position: fixed; bottom: 28px; right: 28px;
    background: var(--green); color: #fff;
    padding: 12px 20px; border-radius: 10px;
    font-size: 13px; font-weight: 600;
    opacity: 0; transform: translateY(8px);
    transition: all .25s; pointer-events: none; z-index: 9999;
  }
  #toast.show { opacity: 1; transform: translateY(0); }

  /* ── RESUMO ── */
  .resumo-table td:not(:first-child) { font-family: var(--mono); font-size: 12.5px; }
  .resumo-row-total td { font-weight: 700; background: var(--bg3) !important; }
  .resumo-row-saldo td { font-weight: 700; }

  .progress-bar { height: 4px; background: var(--bg3); border-radius: 4px; overflow: hidden; margin-top: 6px; }
  .progress-fill { height: 100%; border-radius: 4px; transition: width .5s; }

  /* scrollbar */
  ::-webkit-scrollbar { width: 5px; height: 5px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
</style>
</head>
<body>

<div class="shell">

<!-- ── SIDEBAR ── -->
<nav>
  <div class="nav-logo">💰 Financeiro<span>.</span></div>
  <button class="nav-item active" onclick="showPage('dashboard')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/></svg>
    Dashboard
  </button>
  <button class="nav-item" onclick="showPage('lancamento')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 5v14M5 12h14"/></svg>
    Lançar Mês
  </button>
  <button class="nav-item" onclick="showPage('historico')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M3 12h18M3 6h18M3 18h18"/></svg>
    Histórico
  </button>
  <button class="nav-item" onclick="showPage('graficos')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M18 20V10M12 20V4M6 20v-6"/></svg>
    Gráficos
  </button>
  <button class="nav-item" onclick="showPage('parcelas')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="4" width="18" height="18" rx="2"/><path d="M16 2v4M8 2v4M3 10h18"/></svg>
    Parcelas
  </button>
</nav>

<!-- ── MAIN ── -->
<main>

<!-- ══════════════ DASHBOARD ══════════════ -->
<div class="page active" id="page-dashboard">
  <div class="page-header">
    <h1>Dashboard</h1>
    <p>Visão geral dos seus gastos — Fev/26 a Jul/26</p>
  </div>

  <div class="cards-row" id="dash-cards"></div>

  <div class="grid2 wide">
    <div class="chart-card">
      <div class="chart-title">Gastos mensais por categoria</div>
      <div class="chart-wrap" style="height:260px"><canvas id="chartBarDash"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title">Distribuição total</div>
      <div class="chart-wrap" style="height:260px"><canvas id="chartPieDash"></canvas></div>
    </div>
  </div>

  <div class="chart-card" style="margin-bottom:20px">
    <div class="chart-title">Entradas vs Saídas</div>
    <div class="chart-wrap" style="height:220px"><canvas id="chartLineDash"></canvas></div>
  </div>

  <div class="table-card">
    <div class="table-head"><h3>Resumo mensal</h3></div>
    <table>
      <thead><tr>
        <th>Mês</th><th>Entradas</th><th>Cartão</th><th>Aluguel</th><th>Outros</th><th>Total Saídas</th><th>Saldo</th>
      </tr></thead>
      <tbody id="dash-table-body"></tbody>
    </table>
  </div>
</div>

<!-- ══════════════ LANÇAMENTO ══════════════ -->
<div class="page" id="page-lancamento">
  <div class="page-header">
    <h1>Lançar Mês</h1>
    <p>Registre entradas, saídas e calcule o saldo automaticamente</p>
  </div>

  <div class="month-select-row">
    <span style="font-size:12px;color:var(--muted);font-weight:500">Mês:</span>
    <div id="month-btns"></div>
  </div>

  <!-- Entradas -->
  <div class="form-section">
    <div class="form-section-title">Entradas</div>
    <div id="entradas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addItem('entradas')">+ Adicionar entrada</button>
    <div class="total-display" style="border-color:rgba(34,197,94,.3)">
      <span>Total Entradas</span>
      <strong class="pos" id="total-entradas">R$ 0,00</strong>
    </div>
  </div>

  <!-- Saídas -->
  <div class="form-section">
    <div class="form-section-title">Saídas</div>
    <div id="saidas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addItem('saidas')">+ Adicionar saída</button>
    <div class="total-display" style="border-color:rgba(239,68,68,.3)">
      <span>Total Saídas</span>
      <strong class="neg" id="total-saidas">R$ 0,00</strong>
    </div>
  </div>

  <!-- Saldo -->
  <div class="saldo-bar">
    <div>
      <div class="saldo-bar-label">Saldo do mês</div>
      <div class="saldo-bar-value" id="saldo-mes">R$ 0,00</div>
    </div>
    <button class="btn btn-primary" onclick="salvarMes()">Salvar mês</button>
  </div>
</div>

<!-- ══════════════ HISTÓRICO ══════════════ -->
<div class="page" id="page-historico">
  <div class="page-header">
    <h1>Histórico Completo</h1>
    <p>Todos os lançamentos mês a mês</p>
  </div>
  <div class="month-select-row" id="hist-month-btns"></div>
  <div id="hist-content"></div>
</div>

<!-- ══════════════ GRÁFICOS ══════════════ -->
<div class="page" id="page-graficos">
  <div class="page-header">
    <h1>Gráficos</h1>
    <p>Análise visual dos seus dados financeiros</p>
  </div>

  <div class="grid2" style="margin-bottom:20px">
    <div class="chart-card">
      <div class="chart-title">Gastos por categoria (barras empilhadas)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartBarG"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title">Saldo mensal</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartSaldoG"></canvas></div>
    </div>
  </div>

  <div class="grid2">
    <div class="chart-card">
      <div class="chart-title">Distribuição dos gastos (%)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartPieG"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title">Entradas vs Saídas</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartVsG"></canvas></div>
    </div>
  </div>
</div>

<!-- ══════════════ PARCELAS ══════════════ -->
<div class="page" id="page-parcelas">
  <div class="page-header">
    <h1>Parcelas Futuras</h1>
    <p>Compromissos já lançados para os próximos meses</p>
  </div>
  <div class="table-card" style="margin-bottom:20px">
    <div class="table-head"><h3>Parcelas em aberto</h3></div>
    <table>
      <thead><tr>
        <th>Descrição</th><th>Jun/26</th><th>Jul/26</th><th>Ago/26</th><th>Set/26</th><th>Out/26</th><th>Nov/26</th><th>Dez/26</th><th>Jan/27</th>
      </tr></thead>
      <tbody id="parcelas-body"></tbody>
    </table>
  </div>
  <div class="chart-card">
    <div class="chart-title">Total de parcelas por mês</div>
    <div class="chart-wrap" style="height:220px"><canvas id="chartParcelasG"></canvas></div>
  </div>
</div>

</main>
</div>

<div id="toast">✓ Mês salvo com sucesso!</div>

<script>
// ═══════════════════════════════════════════════════════
//  DADOS
// ═══════════════════════════════════════════════════════
const MESES_LABELS = ['Fev/26','Mar/26','Abr/26','Mai/26','Jun/26','Jul/26','Ago/26','Set/26','Out/26','Nov/26','Dez/26','Jan/27'];
const MESES_KEYS   = ['fev26','mar26','abr26','mai26','jun26','jul26','ago26','set26','out26','nov26','dez26','jan27'];

// Dados históricos reais da planilha
let dados = {
  fev26: {
    entradas: [{desc:'Salário CLT/PJ', valor: 1677}],
    saidas:   [{desc:'Cartão de Crédito', cat:'cartao', valor:2407.54},{desc:'Aluguel', cat:'aluguel', valor:1750},{desc:'Outros Gastos', cat:'outros', valor:341.48}]
  },
  mar26: {
    entradas: [{desc:'Pró-labore',valor:2500},{desc:'Freelance',valor:1333.33}],
    saidas:   [{desc:'Cartão de Crédito', cat:'cartao', valor:2171.64},{desc:'Aluguel', cat:'aluguel', valor:1750},{desc:'Outros Gastos', cat:'outros', valor:210}]
  },
  abr26: {
    entradas: [],
    saidas:   [{desc:'Cartão de Crédito', cat:'cartao', valor:2348.39},{desc:'Aluguel', cat:'aluguel', valor:2100},{desc:'Outros Gastos', cat:'outros', valor:968.31}]
  },
  mai26: {
    entradas: [],
    saidas:   [{desc:'Cartão de Crédito', cat:'cartao', valor:3124.03},{desc:'Aluguel', cat:'aluguel', valor:2717},{desc:'Outros Gastos', cat:'outros', valor:800}]
  },
  jun26: {
    entradas: [],
    saidas:   [{desc:'Cartão de Crédito', cat:'cartao', valor:1126.55},{desc:'Aluguel', cat:'aluguel', valor:0},{desc:'Outros Gastos', cat:'outros', valor:2717}]
  },
  jul26: {
    entradas: [],
    saidas:   [{desc:'Cartão de Crédito', cat:'cartao', valor:722.66},{desc:'Aluguel', cat:'aluguel', valor:0},{desc:'Outros Gastos', cat:'outros', valor:0}]
  },
  ago26: { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:398.88},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
  set26: { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:332.63},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
  out26: { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:332.63},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
  nov26: { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:60.64},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
  dez26: { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:60.64},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
  jan27: { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:60.64},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
};

const PARCELAS = [
  {desc:'Airbnb (parcelado)',   vals:[271.99,271.99,271.99,271.99,271.99,0,0,0]},
  {desc:'Curso (12x)',          vals:[34.41,34.41,34.41,34.41,34.41,34.41,34.41,34.41]},
  {desc:'Mercado Livre',        vals:[51.36,51.36,0,0,0,0,0,0]},
  {desc:'Valor em parcelas XP', vals:[244.33,217.48,26.23,26.23,26.23,26.23,26.23,26.23]},
  {desc:'HAVAN (4x)',           vals:[66.25,66.25,66.25,0,0,0,0,0]},
  {desc:'Bike (2x)',            vals:[125,125,0,0,0,0,0,0]},
];

// ── Helpers ──────────────────────────────────────────────
const brl = v => 'R$ ' + (v||0).toLocaleString('pt-BR',{minimumFractionDigits:2,maximumFractionDigits:2});
const sumArr = arr => arr.reduce((a,b)=>a+(+b.valor||0),0);
const sumCat = (saidas, cat) => saidas.filter(s=>s.cat===cat).reduce((a,b)=>a+(+b.valor||0),0);

function getMes(key){
  const d = dados[key];
  const ent = sumArr(d.entradas);
  const cartao = sumCat(d.saidas,'cartao');
  const aluguel = sumCat(d.saidas,'aluguel');
  const outros = d.saidas.filter(s=>s.cat!=='cartao'&&s.cat!=='aluguel').reduce((a,b)=>a+(+b.valor||0),0);
  const saida = cartao+aluguel+outros;
  return {ent, cartao, aluguel, outros, saida, saldo: ent-saida};
}

// ── NAVEGAÇÃO ─────────────────────────────────────────────
function showPage(id){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  document.querySelector(`[onclick="showPage('${id}')"]`).classList.add('active');
  if(id==='graficos') setTimeout(buildGraficos,50);
  if(id==='historico') renderHistorico();
  if(id==='parcelas') renderParcelas();
}

// ═══════════════════════════════════════════════════════
//  DASHBOARD
// ═══════════════════════════════════════════════════════
function buildDashboard(){
  // Só meses com dados reais (fev–jul)
  const realKeys = MESES_KEYS.slice(0,6);
  const realLabels = MESES_LABELS.slice(0,6);
  let totEnt=0, totSai=0, totCartao=0, totAluguel=0, totOutros=0;
  realKeys.forEach(k=>{const m=getMes(k);totEnt+=m.ent;totSai+=m.saida;totCartao+=m.cartao;totAluguel+=m.aluguel;totOutros+=m.outros;});

  // Cards
  const cards = [
    {label:'Total gasto (6m)', value: brl(totSai), cls:'red', sub:'Fev – Jul/26'},
    {label:'Média mensal',     value: brl(totSai/6), cls:'yellow', sub:'de gastos'},
    {label:'Maior mês',        value: 'Maio/26', cls:'blue', sub: brl(getMes('mai26').saida)},
    {label:'Menor mês',        value: 'Jul/26',  cls:'green', sub: brl(getMes('jul26').saida)},
  ];
  document.getElementById('dash-cards').innerHTML = cards.map(c=>`
    <div class="card ${c.cls}">
      <div class="card-label">${c.label}</div>
      <div class="card-value">${c.value}</div>
      <div class="card-sub">${c.sub}</div>
    </div>`).join('');

  // Table
  const tbody = document.getElementById('dash-table-body');
  tbody.innerHTML = realKeys.map((k,i)=>{
    const m = getMes(k);
    return `<tr>
      <td>${realLabels[i]}</td>
      <td class="mono pos">${m.ent>0?brl(m.ent):'—'}</td>
      <td class="mono">${brl(m.cartao)}</td>
      <td class="mono">${brl(m.aluguel)}</td>
      <td class="mono">${brl(m.outros)}</td>
      <td class="mono neg">${brl(m.saida)}</td>
      <td class="mono ${m.saldo>=0?'pos':'neg'}">${brl(m.saldo)}</td>
    </tr>`;
  }).join('');

  // Bar chart
  const cartaoD = realKeys.map(k=>getMes(k).cartao);
  const aluguelD = realKeys.map(k=>getMes(k).aluguel);
  const outrosD = realKeys.map(k=>getMes(k).outros);

  new Chart(document.getElementById('chartBarDash'), {
    type:'bar',
    data:{
      labels: realLabels,
      datasets:[
        {label:'Cartão',  data:cartaoD,  backgroundColor:'rgba(59,130,246,.8)', borderRadius:4, stack:'s'},
        {label:'Aluguel', data:aluguelD, backgroundColor:'rgba(34,197,94,.8)',  borderRadius:4, stack:'s'},
        {label:'Outros',  data:outrosD,  backgroundColor:'rgba(168,85,247,.8)', borderRadius:4, stack:'s'},
      ]
    },
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:'#9ca3af',font:{size:12}}}},
      scales:{x:{stacked:true,ticks:{color:'#6b7280'},grid:{color:'rgba(255,255,255,.04)'}},
              y:{stacked:true,ticks:{color:'#6b7280',callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:'rgba(255,255,255,.06)'}}}}
  });

  // Pie
  new Chart(document.getElementById('chartPieDash'),{
    type:'doughnut',
    data:{
      labels:['Cartão','Aluguel','Outros'],
      datasets:[{data:[totCartao,totAluguel,totOutros],backgroundColor:['rgba(59,130,246,.85)','rgba(34,197,94,.85)','rgba(168,85,247,.85)'],borderWidth:0}]
    },
    options:{responsive:true,maintainAspectRatio:false,cutout:'62%',
      plugins:{legend:{position:'bottom',labels:{color:'#9ca3af',font:{size:12},padding:16}}}}
  });

  // Line ent vs saidas
  new Chart(document.getElementById('chartLineDash'),{
    type:'bar',
    data:{
      labels:realLabels,
      datasets:[
        {type:'bar', label:'Entradas', data:realKeys.map(k=>getMes(k).ent), backgroundColor:'rgba(34,197,94,.6)', borderRadius:4},
        {type:'line',label:'Saídas',   data:realKeys.map(k=>getMes(k).saida), borderColor:'#ef4444', backgroundColor:'rgba(239,68,68,.1)', tension:.35, fill:true, pointRadius:4, pointBackgroundColor:'#ef4444'},
      ]
    },
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{labels:{color:'#9ca3af',font:{size:12}}}},
      scales:{x:{ticks:{color:'#6b7280'},grid:{color:'rgba(255,255,255,.04)'}},
              y:{ticks:{color:'#6b7280',callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:'rgba(255,255,255,.06)'}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  LANÇAMENTO
// ═══════════════════════════════════════════════════════
let mesAtivo = 'ago26'; // mês padrão para novos lançamentos

function buildLancamento(){
  // Month buttons (somente futuros + alguns recentes)
  const btnContainer = document.getElementById('month-btns');
  btnContainer.innerHTML = '';
  const flex = document.createElement('div'); flex.style.display='flex'; flex.style.gap='8px'; flex.style.flexWrap='wrap';
  MESES_KEYS.forEach((k,i)=>{
    const btn = document.createElement('button');
    btn.className = 'month-btn' + (k===mesAtivo?' active':'');
    btn.textContent = MESES_LABELS[i];
    btn.onclick = ()=>{ mesAtivo=k; document.querySelectorAll('.month-btn').forEach(b=>b.classList.remove('active')); btn.classList.add('active'); loadMesForm(); };
    flex.appendChild(btn);
  });
  btnContainer.appendChild(flex);
  loadMesForm();
}

function loadMesForm(){
  const d = dados[mesAtivo];
  renderItems('entradas', d.entradas);
  renderItems('saidas', d.saidas);
  recalcTotais();
}

function renderItems(tipo, items){
  const list = document.getElementById(tipo+'-list');
  list.innerHTML = items.map((item,i)=>`
    <div class="lancamento-item" id="${tipo}-${i}">
      <input type="text" placeholder="${tipo==='entradas'?'Ex: Salário':'Ex: Aluguel'}" value="${item.desc||''}" oninput="updateItem('${tipo}',${i},'desc',this.value)">
      ${tipo==='saidas'?`<select onchange="updateItem('saidas',${i},'cat',this.value)">
        <option value="cartao" ${item.cat==='cartao'?'selected':''}>Cartão</option>
        <option value="aluguel" ${item.cat==='aluguel'?'selected':''}>Aluguel</option>
        <option value="celular" ${item.cat==='celular'?'selected':''}>Celular</option>
        <option value="alimentacao" ${item.cat==='alimentacao'?'selected':''}>Alimentação</option>
        <option value="outros" ${item.cat==='outros'?'selected':''}>Outros</option>
      </select>`:'<div></div>'}
      <input type="number" placeholder="0,00" value="${item.valor||''}" step="0.01" oninput="updateItem('${tipo}',${i},'valor',this.value)">
      <button class="remove-btn" onclick="removeItem('${tipo}',${i})">×</button>
    </div>`).join('');
}

function updateItem(tipo, idx, field, val){
  dados[mesAtivo][tipo][idx][field] = field==='valor'?parseFloat(val)||0:val;
  recalcTotais();
}

function addItem(tipo){
  const item = tipo==='entradas'?{desc:'',valor:0}:{desc:'',cat:'outros',valor:0};
  dados[mesAtivo][tipo].push(item);
  renderItems(tipo, dados[mesAtivo][tipo]);
  recalcTotais();
}

function removeItem(tipo, idx){
  dados[mesAtivo][tipo].splice(idx,1);
  renderItems(tipo, dados[mesAtivo][tipo]);
  recalcTotais();
}

function recalcTotais(){
  const ent = sumArr(dados[mesAtivo].entradas);
  const sai = sumArr(dados[mesAtivo].saidas);
  const saldo = ent - sai;
  document.getElementById('total-entradas').textContent = brl(ent);
  document.getElementById('total-saidas').textContent = brl(sai);
  const saldoEl = document.getElementById('saldo-mes');
  saldoEl.textContent = brl(saldo);
  saldoEl.style.color = saldo >= 0 ? 'var(--green)' : 'var(--red)';
}

function salvarMes(){
  const t = document.getElementById('toast');
  t.classList.add('show');
  setTimeout(()=>t.classList.remove('show'),2500);
}

// ═══════════════════════════════════════════════════════
//  HISTÓRICO
// ═══════════════════════════════════════════════════════
let histMes = 'fev26';

function renderHistorico(){
  // month selector
  const container = document.getElementById('hist-month-btns');
  container.innerHTML = '';
  const flex = document.createElement('div'); flex.style.display='flex'; flex.style.gap='8px'; flex.style.flexWrap='wrap';
  MESES_KEYS.forEach((k,i)=>{
    const btn = document.createElement('button');
    btn.className='month-btn'+(k===histMes?' active':'');
    btn.textContent=MESES_LABELS[i];
    btn.onclick=()=>{histMes=k;document.querySelectorAll('#hist-month-btns .month-btn').forEach(b=>b.classList.remove('active'));btn.classList.add('active');renderHistContent();};
    flex.appendChild(btn);
  });
  container.appendChild(flex);
  renderHistContent();
}

function renderHistContent(){
  const d = dados[histMes];
  const m = getMes(histMes);
  const label = MESES_LABELS[MESES_KEYS.indexOf(histMes)];

  document.getElementById('hist-content').innerHTML = `
    <div class="cards-row" style="margin-bottom:20px">
      <div class="card green"><div class="card-label">Entradas</div><div class="card-value">${brl(m.ent)}</div></div>
      <div class="card red"><div class="card-label">Saídas</div><div class="card-value">${brl(m.saida)}</div></div>
      <div class="card ${m.saldo>=0?'green':'red'}"><div class="card-label">Saldo</div><div class="card-value">${brl(m.saldo)}</div></div>
    </div>
    <div class="grid2">
      <div class="table-card">
        <div class="table-head"><h3>Entradas — ${label}</h3></div>
        <table><thead><tr><th>Descrição</th><th>Valor</th></tr></thead>
        <tbody>${d.entradas.length?d.entradas.map(e=>`<tr><td>${e.desc}</td><td class="mono pos">${brl(e.valor)}</td></tr>`).join(''):'<tr><td colspan="2" style="color:var(--muted);text-align:center;padding:20px">Sem entradas lançadas</td></tr>'}
        <tr style="border-top:1px solid var(--border)"><td style="font-weight:600">Total</td><td class="mono pos" style="font-weight:700">${brl(m.ent)}</td></tr>
        </tbody></table>
      </div>
      <div class="table-card">
        <div class="table-head"><h3>Saídas — ${label}</h3></div>
        <table><thead><tr><th>Descrição</th><th>Categoria</th><th>Valor</th></tr></thead>
        <tbody>${d.saidas.filter(s=>s.valor>0).map(s=>`<tr><td>${s.desc}</td><td><span class="badge badge-blue">${s.cat}</span></td><td class="mono neg">${brl(s.valor)}</td></tr>`).join('')}
        <tr style="border-top:1px solid var(--border)"><td style="font-weight:600" colspan="2">Total</td><td class="mono neg" style="font-weight:700">${brl(m.saida)}</td></tr>
        </tbody></table>
      </div>
    </div>`;
}

// ═══════════════════════════════════════════════════════
//  GRÁFICOS
// ═══════════════════════════════════════════════════════
let graficosBuilt = false;
function buildGraficos(){
  if(graficosBuilt) return; graficosBuilt=true;
  const realKeys = MESES_KEYS.slice(0,6);
  const realLabels = MESES_LABELS.slice(0,6);

  const cartaoD = realKeys.map(k=>getMes(k).cartao);
  const aluguelD= realKeys.map(k=>getMes(k).aluguel);
  const outrosD = realKeys.map(k=>getMes(k).outros);
  const saldoD  = realKeys.map(k=>getMes(k).saldo);
  const totEnt  = realKeys.reduce((a,k)=>a+getMes(k).ent,0);
  const totCart = realKeys.reduce((a,k)=>a+getMes(k).cartao,0);
  const totAlug = realKeys.reduce((a,k)=>a+getMes(k).aluguel,0);
  const totOut  = realKeys.reduce((a,k)=>a+getMes(k).outros,0);

  const gridColor = 'rgba(255,255,255,.05)';
  const tickColor = '#6b7280';

  // Barras empilhadas
  new Chart(document.getElementById('chartBarG'),{type:'bar',
    data:{labels:realLabels,datasets:[
      {label:'Cartão', data:cartaoD, backgroundColor:'rgba(59,130,246,.8)', borderRadius:3, stack:'s'},
      {label:'Aluguel',data:aluguelD,backgroundColor:'rgba(34,197,94,.8)',  borderRadius:3, stack:'s'},
      {label:'Outros', data:outrosD, backgroundColor:'rgba(168,85,247,.8)', borderRadius:3, stack:'s'},
    ]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{labels:{color:'#9ca3af',font:{size:12}}}},
      scales:{x:{stacked:true,ticks:{color:tickColor},grid:{color:gridColor}},
              y:{stacked:true,ticks:{color:tickColor,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gridColor}}}}
  });

  // Saldo (linha + barras negativas coloridas)
  new Chart(document.getElementById('chartSaldoG'),{type:'bar',
    data:{labels:realLabels,datasets:[{
      label:'Saldo', data:saldoD,
      backgroundColor:saldoD.map(v=>v>=0?'rgba(34,197,94,.7)':'rgba(239,68,68,.7)'),
      borderRadius:4
    }]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false}},
      scales:{x:{ticks:{color:tickColor},grid:{color:gridColor}},
              y:{ticks:{color:tickColor,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gridColor}}}}
  });

  // Pizza
  new Chart(document.getElementById('chartPieG'),{type:'doughnut',
    data:{labels:['Cartão','Aluguel','Outros'],
      datasets:[{data:[totCart,totAlug,totOut],backgroundColor:['#3b82f6','#22c55e','#a855f7'],borderWidth:0}]
    },
    options:{responsive:true,maintainAspectRatio:false,cutout:'58%',
      plugins:{legend:{position:'bottom',labels:{color:'#9ca3af',font:{size:12},padding:16}},
        tooltip:{callbacks:{label:ctx=>{const tot=totCart+totAlug+totOut;return ` ${brl(ctx.raw)} (${(ctx.raw/tot*100).toFixed(1)}%)`;}}}}
    }
  });

  // Entradas vs Saídas
  new Chart(document.getElementById('chartVsG'),{type:'bar',
    data:{labels:realLabels,datasets:[
      {label:'Entradas',data:realKeys.map(k=>getMes(k).ent), backgroundColor:'rgba(34,197,94,.7)', borderRadius:4},
      {label:'Saídas',  data:realKeys.map(k=>getMes(k).saida),backgroundColor:'rgba(239,68,68,.7)', borderRadius:4},
    ]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{labels:{color:'#9ca3af',font:{size:12}}}},
      scales:{x:{ticks:{color:tickColor},grid:{color:gridColor}},
              y:{ticks:{color:tickColor,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gridColor}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  PARCELAS
// ═══════════════════════════════════════════════════════
function renderParcelas(){
  const totais = Array(8).fill(0);
  const body = document.getElementById('parcelas-body');
  body.innerHTML = PARCELAS.map(p=>{
    p.vals.forEach((v,i)=>totais[i]+=v);
    return `<tr>
      <td style="font-weight:500">${p.desc}</td>
      ${p.vals.map(v=>`<td class="mono ${v?'':''}' style="color:${v?'var(--text)':'var(--muted)'}">${v?brl(v):'—'}</td>`).join('')}
    </tr>`;
  }).join('') + `<tr style="border-top:2px solid var(--border);background:var(--bg3)">
    <td style="font-weight:700">Total por mês</td>
    ${totais.map(v=>`<td class="mono" style="font-weight:700;color:var(--yellow)">${brl(v)}</td>`).join('')}
  </tr>`;

  const mesesP = ['Jun/26','Jul/26','Ago/26','Set/26','Out/26','Nov/26','Dez/26','Jan/27'];
  if(window._parcelasChart) window._parcelasChart.destroy();
  window._parcelasChart = new Chart(document.getElementById('chartParcelasG'),{type:'bar',
    data:{labels:mesesP,datasets:[{label:'Total parcelas',data:totais,backgroundColor:'rgba(245,158,11,.75)',borderRadius:6}]},
    options:{responsive:true,maintainAspectRatio:false,
      plugins:{legend:{display:false}},
      scales:{x:{ticks:{color:'#6b7280'},grid:{color:'rgba(255,255,255,.05)'}},
              y:{ticks:{color:'#6b7280',callback:v=>'R$'+v.toFixed(0)},grid:{color:'rgba(255,255,255,.05)'}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  INIT
// ═══════════════════════════════════════════════════════
buildDashboard();
buildLancamento();
</script>
</body>
</html>

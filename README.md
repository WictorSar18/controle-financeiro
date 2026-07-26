<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controle Financeiro — Wictor</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap');

  :root {
    --bg:        #e4e7ec;
    --bg2:       #f2f4f7;
    --bg3:       #e9ebf0;
    --border:    #c8cdd8;
    --text:      #0d1117;
    --text2:     #1a2232;
    --muted:     #3d4a5c;
    --green:     #14532d;
    --red:       #991b1b;
    --blue:      #1e40af;
    --purple:    #5b21b6;
    --yellow:    #92400e;
    --teal:      #155e75;
    --green-dim: rgba(20,83,45,.12);
    --red-dim:   rgba(153,27,27,.12);
    --blue-dim:  rgba(30,64,175,.12);
    --radius:    12px;
    --mono:      'JetBrains Mono', monospace;
    --shadow:    0 1px 4px rgba(0,0,0,.08);
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { background: var(--bg); color: var(--text); font-family: 'Inter', sans-serif; font-size: 14px; min-height: 100vh; }

  .shell { display: flex; min-height: 100vh; }

  nav {
    width: 224px; min-width: 224px;
    background: #dde0e8;
    border-right: 1px solid var(--border);
    padding: 24px 0;
    display: flex; flex-direction: column;
    position: sticky; top: 0; height: 100vh;
    overflow-y: auto;
    box-shadow: var(--shadow);
  }
  .nav-logo { padding: 0 20px 20px; font-size: 15px; font-weight: 700; letter-spacing: -.3px; border-bottom: 1px solid var(--border); margin-bottom: 12px; color: var(--text); }
  .nav-logo span { color: var(--blue); }

  .nav-item {
    display: flex; align-items: center; gap: 10px;
    padding: 10px 20px; cursor: pointer; border: none; background: none;
    color: var(--muted); font-size: 13.5px; font-family: 'Inter', sans-serif;
    width: 100%; text-align: left; transition: color .15s, background .15s;
  }
  .nav-item:hover { color: var(--text); background: var(--bg3); }
  .nav-item.active { color: var(--blue); background: var(--blue-dim); font-weight: 600; }
  .nav-item svg { flex-shrink: 0; }

  main { flex: 1; padding: 32px; overflow-x: hidden; }
  .page { display: none; }
  .page.active { display: block; }

  .page-header { margin-bottom: 28px; }
  .page-header h1 { font-size: 22px; font-weight: 700; color: var(--text); }
  .page-header p { color: var(--muted); margin-top: 4px; font-size: 13px; }

  /* CARDS */
  .cards-row { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 16px; margin-bottom: 28px; }
  .card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 20px; box-shadow: var(--shadow); }
  .card-label { font-size: 11px; font-weight: 600; text-transform: uppercase; letter-spacing: .06em; color: var(--muted); margin-bottom: 8px; }
  .card-value { font-size: 22px; font-weight: 700; font-family: var(--mono); color: var(--text); }
  .card-sub { font-size: 11px; color: var(--muted); margin-top: 4px; }
  .card.green .card-value { color: #15803d; }
  .card.red   .card-value { color: #b91c1c; }
  .card.blue  .card-value { color: #1e40af; }
  .card.yellow .card-value{ color: #92400e; }
  .card.teal  .card-value { color: #155e75; }

  /* GRID */
  .grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin-bottom: 20px; }
  .grid2.wide { grid-template-columns: 2fr 1fr; }
  @media(max-width:900px){ .grid2, .grid2.wide { grid-template-columns: 1fr; } }

  /* CHART CARD */
  .chart-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 22px; box-shadow: var(--shadow); }
  .chart-title { font-size: 13px; font-weight: 600; margin-bottom: 18px; color: var(--text); }
  .chart-wrap { position: relative; }

  /* TABLE */
  .table-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); overflow: hidden; box-shadow: var(--shadow); }
  .table-head { padding: 16px 20px; border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; }
  .table-head h3 { font-size: 13px; font-weight: 600; color: var(--text); }

  table { width: 100%; border-collapse: collapse; }
  th { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: .05em; color: #374151; padding: 11px 20px; text-align: left; border-bottom: 1px solid var(--border); background: #dfe2ea; }
  td { padding: 11px 20px; border-bottom: 1px solid var(--border); font-size: 13px; color: var(--text2); font-weight: 450; }
  td:first-child { color: var(--text); font-weight: 500; }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: var(--bg3); }
  .mono { font-family: var(--mono); font-size: 12.5px; }
  .neg { color: #b91c1c !important; font-weight: 700; }
  .pos { color: #15803d !important; font-weight: 700; }

  .badge { display: inline-block; padding: 2px 9px; border-radius: 999px; font-size: 11px; font-weight: 600; }
  .badge-blue   { background: var(--blue-dim);  color: var(--blue); }
  .badge-green  { background: var(--green-dim); color: var(--green); }
  .badge-red    { background: var(--red-dim);   color: var(--red); }
  .badge-purple { background: rgba(124,58,237,.1); color: var(--purple); }
  .badge-teal   { background: rgba(8,145,178,.1);  color: var(--teal); }
  .badge-yellow { background: rgba(217,119,6,.1);  color: var(--yellow); }

  /* FORM */
  .form-section { margin-bottom: 28px; }
  .form-section-title { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: .07em; color: var(--muted); margin-bottom: 14px; padding-bottom: 8px; border-bottom: 2px solid var(--border); }

  .field { display: flex; flex-direction: column; gap: 6px; }
  .field label { font-size: 12px; font-weight: 600; color: var(--text2); }
  .field input, .field select {
    background: var(--bg3); border: 1px solid var(--border);
    color: var(--text); border-radius: 8px; padding: 9px 12px;
    font-size: 13px; font-family: 'Inter', sans-serif; outline: none; transition: border-color .15s;
  }
  .field input:focus, .field select:focus { border-color: var(--blue); background: #fff; }
  .field input::placeholder { color: #adb5bd; }

  .btn { display: inline-flex; align-items: center; gap: 7px; padding: 10px 20px; border-radius: 8px; font-size: 13px; font-weight: 600; font-family: 'Inter', sans-serif; cursor: pointer; border: none; transition: opacity .15s, transform .1s; }
  .btn:hover { opacity: .88; }
  .btn:active { transform: scale(.98); }
  .btn-primary { background: var(--blue); color: #fff; }
  .btn-danger  { background: var(--red);  color: #fff; }
  .btn-ghost   { background: var(--bg3);  color: var(--text2); border: 1px solid var(--border); }
  .btn-purple  { background: var(--purple); color: #fff; }
  .btn-teal    { background: var(--teal); color: #fff; }

  /* SALDO BAR */
  .saldo-bar { display: flex; align-items: center; justify-content: space-between; background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 18px 24px; margin-bottom: 24px; box-shadow: var(--shadow); }
  .saldo-bar-label { font-size: 12px; color: var(--muted); font-weight: 500; }
  .saldo-bar-value { font-size: 28px; font-weight: 700; font-family: var(--mono); }

  /* ANO/MÊS SELECTOR */
  .period-selector { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 16px 20px; margin-bottom: 24px; box-shadow: var(--shadow); }
  .period-row { display: flex; align-items: center; gap: 12px; flex-wrap: wrap; }
  .period-label { font-size: 12px; font-weight: 600; color: var(--muted); text-transform: uppercase; letter-spacing: .04em; min-width: 36px; }
  .period-btn { padding: 6px 14px; border-radius: 7px; font-size: 13px; cursor: pointer; border: 1px solid var(--border); background: var(--bg3); color: var(--muted); font-family: 'Inter', sans-serif; font-weight: 500; transition: all .15s; }
  .period-btn:hover { color: var(--text); border-color: #c0c6d4; }
  .period-btn.active { background: var(--blue); border-color: var(--blue); color: #fff; font-weight: 600; }
  .period-divider { width: 100%; height: 0; border-top: 1px solid var(--border); margin: 10px 0 4px; }

  /* LANCAMENTO ROWS */
  .lancamento-item { display: grid; gap: 8px; align-items: center; margin-bottom: 8px; }
  .lancamento-item.entradas  { grid-template-columns: 2fr 1fr 28px; }
  .lancamento-item.saidas    { grid-template-columns: 2fr 1.4fr 1fr 28px; }
  .lancamento-item.investimentos { grid-template-columns: 1fr 1.5fr 1fr 28px; }
  .lancamento-item input, .lancamento-item select {
    background: var(--bg3); border: 1px solid var(--border);
    color: var(--text); border-radius: 8px; padding: 8px 11px; font-size: 13px; font-family: 'Inter',sans-serif; outline: none;
    transition: border-color .15s;
  }
  .lancamento-item input:focus, .lancamento-item select:focus { border-color: var(--blue); background: #fff; }
  .lancamento-item input::placeholder { color: #adb5bd; }
  .remove-btn { background: none; border: 1px solid transparent; color: var(--muted); cursor: pointer; font-size: 18px; line-height: 1; padding: 4px 7px; border-radius: 6px; }
  .remove-btn:hover { color: var(--red); background: var(--red-dim); border-color: var(--red-dim); }

  .total-display { background: var(--bg3); border: 1px solid var(--border); border-radius: 8px; padding: 12px 16px; display: flex; justify-content: space-between; align-items: center; margin-top: 10px; }
  .total-display span { font-size: 12px; color: var(--muted); font-weight: 600; text-transform: uppercase; letter-spacing: .05em; }
  .total-display strong { font-family: var(--mono); font-size: 16px; color: var(--text); }

  /* COL HEADERS */
  .col-headers { display: grid; gap: 8px; padding: 0 0 4px; }
  .col-headers.entradas    { grid-template-columns: 2fr 1fr 28px; }
  .col-headers.saidas      { grid-template-columns: 2fr 1.4fr 1fr 28px; }
  .col-headers.investimentos { grid-template-columns: 1fr 1.5fr 1fr 28px; }
  .col-headers span { font-size: 11px; font-weight: 600; color: var(--muted); text-transform: uppercase; letter-spacing: .04em; }

  /* TOAST */
  #toast { position: fixed; bottom: 28px; right: 28px; background: var(--green); color: #fff; padding: 12px 20px; border-radius: 10px; font-size: 13px; font-weight: 600; opacity: 0; transform: translateY(8px); transition: all .25s; pointer-events: none; z-index: 9999; box-shadow: 0 4px 16px rgba(0,0,0,.15); }
  #toast.show { opacity: 1; transform: translateY(0); }

  /* LOADING */
  #loading-overlay { position:fixed; inset:0; background:rgba(228,231,236,.85); backdrop-filter:blur(4px); display:flex; flex-direction:column; align-items:center; justify-content:center; z-index:99999; gap:16px; }
  .spinner { width:40px; height:40px; border:3px solid var(--border); border-top-color:var(--blue); border-radius:50%; animation:spin .8s linear infinite; }
  @keyframes spin { to { transform:rotate(360deg); } }
  #loading-overlay p { color:var(--muted); font-size:13px; font-weight:500; }
  .sync-badge { display:inline-flex; align-items:center; gap:5px; font-size:11px; font-weight:600; padding:3px 10px; border-radius:999px; background:rgba(14,116,144,.1); color:#155e75; margin-left:8px; }
  .sync-dot { width:6px; height:6px; border-radius:50%; background:#155e75; animation:pulse 1.5s infinite; }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:.3} }

  ::-webkit-scrollbar { width: 5px; height: 5px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 4px; }
</style>
</head>
<body>
<div id="loading-overlay">
  <div class="spinner"></div>
  <p>Carregando dados do Supabase...</p>
</div>
<div class="shell">

<!-- SIDEBAR -->
<nav>
  <div class="nav-logo">💰 Financeiro<span>.</span><br><span class="sync-badge" id="sync-badge"><span class="sync-dot"></span>Supabase</span></div>
  <button class="nav-item active" onclick="showPage('dashboard')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/></svg>
    Dashboard
  </button>
  <button class="nav-item" onclick="showPage('lancamento')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 5v14M5 12h14"/></svg>
    Lançar Mês
  </button>
  <button class="nav-item" onclick="showPage('investimentos')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M3 17l4-8 4 4 4-6 4 4"/><circle cx="21" cy="11" r="2"/></svg>
    Investimentos
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

<main>

<!-- ══ DASHBOARD ══ -->
<div class="page active" id="page-dashboard">
  <div class="page-header"><h1>Dashboard</h1><p>Visão geral dos seus gastos</p></div>
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
    <table><thead><tr><th>Mês</th><th>Entradas</th><th>Cartão</th><th>Aluguel</th><th>Outros</th><th>Total Saídas</th><th>Saldo</th></tr></thead>
    <tbody id="dash-table-body"></tbody></table>
  </div>
</div>

<!-- ══ LANÇAMENTO ══ -->
<div class="page" id="page-lancamento">
  <div class="page-header"><h1>Lançar Mês</h1><p>Registre entradas e saídas do mês selecionado</p></div>

  <!-- Seletor Ano/Mês -->
  <div class="period-selector">
    <div class="period-row">
      <span class="period-label">Ano</span>
      <div id="ano-btns-lanc" style="display:flex;gap:6px;flex-wrap:wrap"></div>
    </div>
    <div class="period-divider"></div>
    <div class="period-row">
      <span class="period-label">Mês</span>
      <div id="mes-btns-lanc" style="display:flex;gap:6px;flex-wrap:wrap"></div>
    </div>
  </div>

  <!-- Entradas -->
  <div class="form-section">
    <div class="form-section-title">Entradas</div>
    <div class="col-headers entradas" style="padding:0 0 6px 2px">
      <span>Descrição</span><span>Valor (R$)</span><span></span>
    </div>
    <div id="entradas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addItem('entradas')">+ Adicionar entrada</button>
    <div class="total-display" style="border-left:3px solid var(--green);margin-top:12px">
      <span>Total Entradas</span><strong class="pos" id="total-entradas">R$ 0,00</strong>
    </div>
  </div>

  <!-- Saídas -->
  <div class="form-section">
    <div class="form-section-title">Saídas</div>
    <div class="col-headers saidas" style="padding:0 0 6px 2px">
      <span>Descrição</span><span>Categoria</span><span>Valor (R$)</span><span></span>
    </div>
    <div id="saidas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addItem('saidas')">+ Adicionar saída</button>
    <div class="total-display" style="border-left:3px solid var(--red);margin-top:12px">
      <span>Total Saídas</span><strong class="neg" id="total-saidas">R$ 0,00</strong>
    </div>
  </div>

  <!-- Saldo -->
  <div class="saldo-bar">
    <div>
      <div class="saldo-bar-label">Saldo do mês</div>
      <div class="saldo-bar-value" id="saldo-mes">R$ 0,00</div>
    </div>
    <button class="btn btn-primary" onclick="salvarMes()">✓ Salvar mês</button>
  </div>
</div>

<!-- ══ INVESTIMENTOS ══ -->
<div class="page" id="page-investimentos">
  <div class="page-header"><h1>Investimentos</h1><p>Registre e acompanhe seus investimentos</p></div>

  <!-- Seletor período -->
  <div class="period-selector">
    <div class="period-row">
      <span class="period-label">Ano</span>
      <div id="ano-btns-inv" style="display:flex;gap:6px;flex-wrap:wrap"></div>
    </div>
    <div class="period-divider"></div>
    <div class="period-row">
      <span class="period-label">Mês</span>
      <div id="mes-btns-inv" style="display:flex;gap:6px;flex-wrap:wrap"></div>
    </div>
  </div>

  <div class="form-section">
    <div class="form-section-title">Lançar investimento</div>
    <div class="col-headers investimentos" style="padding:0 0 6px 2px">
      <span>Data</span><span>Nome do investimento</span><span>Valor (R$)</span><span></span>
    </div>
    <div id="investimentos-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addInvestimento()">+ Adicionar investimento</button>
    <div class="total-display" style="border-left:3px solid var(--teal);margin-top:12px">
      <span>Total Investido no Mês</span><strong id="total-inv" style="color:var(--teal)">R$ 0,00</strong>
    </div>
  </div>

  <div style="display:flex;gap:8px;margin-bottom:20px">
    <button class="btn btn-teal" onclick="salvarInvestimentos()">✓ Salvar investimentos</button>
  </div>

  <!-- Resumo agrupado -->
  <div class="grid2">
    <div class="table-card">
      <div class="table-head"><h3>Por investimento (total acumulado)</h3></div>
      <table><thead><tr><th>Investimento</th><th>Total</th><th>%</th></tr></thead>
      <tbody id="inv-resumo-body"></tbody></table>
    </div>
    <div class="chart-card">
      <div class="chart-title">Distribuição por investimento</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartInvPie"></canvas></div>
    </div>
  </div>
</div>

<!-- ══ HISTÓRICO ══ -->
<div class="page" id="page-historico">
  <div class="page-header"><h1>Histórico</h1><p>Consulte qualquer mês lançado</p></div>
  <div class="period-selector">
    <div class="period-row">
      <span class="period-label">Ano</span>
      <div id="ano-btns-hist" style="display:flex;gap:6px;flex-wrap:wrap"></div>
    </div>
    <div class="period-divider"></div>
    <div class="period-row">
      <span class="period-label">Mês</span>
      <div id="mes-btns-hist" style="display:flex;gap:6px;flex-wrap:wrap"></div>
    </div>
  </div>
  <div id="hist-content"></div>
</div>

<!-- ══ GRÁFICOS ══ -->
<div class="page" id="page-graficos">
  <div class="page-header"><h1>Gráficos</h1><p>Análise visual dos seus dados</p></div>
  <div class="grid2" style="margin-bottom:20px">
    <div class="chart-card">
      <div class="chart-title">Gastos por categoria (empilhadas)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartBarG"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title">Saldo mensal</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartSaldoG"></canvas></div>
    </div>
  </div>
  <div class="grid2">
    <div class="chart-card">
      <div class="chart-title">Distribuição dos gastos</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartPieG"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title">Entradas vs Saídas</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartVsG"></canvas></div>
    </div>
  </div>
</div>

<!-- ══ PARCELAS ══ -->
<div class="page" id="page-parcelas">
  <div class="page-header"><h1>Parcelas Futuras</h1><p>Compromissos já lançados nos próximos meses</p></div>
  <div class="table-card" style="margin-bottom:20px">
    <div class="table-head"><h3>Parcelas em aberto</h3></div>
    <table><thead><tr><th>Descrição</th><th>Jun/26</th><th>Jul/26</th><th>Ago/26</th><th>Set/26</th><th>Out/26</th><th>Nov/26</th><th>Dez/26</th><th>Jan/27</th></tr></thead>
    <tbody id="parcelas-body"></tbody></table>
  </div>
  <div class="chart-card">
    <div class="chart-title">Total de parcelas por mês</div>
    <div class="chart-wrap" style="height:220px"><canvas id="chartParcelasG"></canvas></div>
  </div>
</div>

</main>
</div>

<div id="toast">✓ Salvo com sucesso!</div>

<script>
// ═══════════════════════════════════════════════════════
//  SUPABASE
// ═══════════════════════════════════════════════════════
const SUPABASE_URL = 'https://itsxvvhqhilsvauqqeax.supabase.co'
const SUPABASE_KEY = 'sb_publishable_Wq_LI_05HqS7Sg26wxYkQA_hZam-MBO'
const sb = supabase.createClient(SUPABASE_URL, SUPABASE_KEY)

// ═══════════════════════════════════════════════════════
//  ESTRUTURA DE DADOS
// ═══════════════════════════════════════════════════════
const ANOS  = ['2025','2026','2027'];
const MESES_NOMES = ['Jan','Fev','Mar','Abr','Mai','Jun','Jul','Ago','Set','Out','Nov','Dez'];

// Dados iniciais históricos
let dados = {
  '2026': {
    '1': { entradas:[{desc:'Salário',valor:1677}], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:2407.54},{desc:'Aluguel',cat:'aluguel',valor:1750},{desc:'Outros',cat:'outros',valor:341.48}] },
    '2': { entradas:[{desc:'Pró-labore',valor:2500},{desc:'Freelance',valor:1333.33}], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:2171.64},{desc:'Aluguel',cat:'aluguel',valor:1750},{desc:'Outros',cat:'outros',valor:210}] },
    '3': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:2348.39},{desc:'Aluguel',cat:'aluguel',valor:2100},{desc:'Outros',cat:'outros',valor:968.31}] },
    '4': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:3124.03},{desc:'Aluguel',cat:'aluguel',valor:2717},{desc:'Outros',cat:'outros',valor:800}] },
    '5': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:1126.55},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:2717}] },
    '6': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:722.66},{desc:'Aluguel',cat:'aluguel',valor:0},{desc:'Outros',cat:'outros',valor:0}] },
    '7': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:398.88}] },
    '8': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:332.63}] },
    '9': { entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:332.63}] },
    '10':{ entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:60.64}] },
    '11':{ entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:60.64}] },
    '12':{ entradas:[], saidas:[{desc:'Cartão de Crédito',cat:'cartao',valor:60.64}] },
  }
};

// Investimentos: { '2026': { '1': [{data, nome, valor}] } }
let investimentos = {
  '2026': {
    '6': [{data:'10/06/2026',nome:'Tesouro Direto',valor:500},{data:'15/06/2026',nome:'CDB Nubank',valor:300}],
    '7': [{data:'05/07/2026',nome:'Tesouro Direto',valor:500},{data:'20/07/2026',nome:'Ações PETR4',valor:200}],
  }
};

const PARCELAS_DATA = [
  {desc:'Airbnb (parcelado)',   vals:[271.99,271.99,271.99,271.99,271.99,0,0,0]},
  {desc:'Curso (12x)',          vals:[34.41,34.41,34.41,34.41,34.41,34.41,34.41,34.41]},
  {desc:'Mercado Livre',        vals:[51.36,51.36,0,0,0,0,0,0]},
  {desc:'Valor em parcelas XP', vals:[244.33,217.48,26.23,26.23,26.23,26.23,26.23,26.23]},
  {desc:'HAVAN (4x)',           vals:[66.25,66.25,66.25,0,0,0,0,0]},
  {desc:'Bike (2x)',            vals:[125,125,0,0,0,0,0,0]},
];

const CAT_SAIDAS = [
  {value:'cartao',      label:'Cartão de crédito'},
  {value:'aluguel',     label:'Aluguel'},
  {value:'alimentacao', label:'Alimentação / Mercado'},
  {value:'delivery',    label:'Delivery / iFood'},
  {value:'transporte',  label:'Transporte / Uber / 99'},
  {value:'combustivel', label:'Combustível'},
  {value:'celular',     label:'Celular / Internet'},
  {value:'saude',       label:'Saúde / Farmácia'},
  {value:'educacao',    label:'Educação / Cursos'},
  {value:'lazer',       label:'Lazer / Entretenimento'},
  {value:'vestuario',   label:'Roupas / Vestuário'},
  {value:'impostos',    label:'Impostos / Contador'},
  {value:'assinaturas', label:'Assinaturas / Streaming'},
  {value:'pet',         label:'Pet'},
  {value:'viagem',      label:'Viagem / Hospedagem'},
  {value:'outros',      label:'Outros'},
];

const CAT_COLORS = {
  cartao:'#2563eb',aluguel:'#16a34a',alimentacao:'#d97706',delivery:'#ea580c',
  transporte:'#7c3aed',combustivel:'#64748b',celular:'#0891b2',saude:'#db2777',
  educacao:'#059669',lazer:'#8b5cf6',vestuario:'#f59e0b',impostos:'#374151',
  assinaturas:'#6366f1',pet:'#ec4899',viagem:'#0d9488',outros:'#9ca3af'
};

// ── Estado da navegação de período ────────────────────────
let anoAtivo = { lanc:'2026', inv:'2026', hist:'2026' };
let mesAtivo = { lanc:'1',    inv:'6',    hist:'1' };

// ── Helpers ───────────────────────────────────────────────
const brl = v => 'R$ ' + (v||0).toLocaleString('pt-BR',{minimumFractionDigits:2,maximumFractionDigits:2});
const sumArr = arr => arr.reduce((a,b)=>a+(+b.valor||0),0);
const getMesLabel = (ano, mes) => `${MESES_NOMES[+mes-1]}/${String(ano).slice(2)}`;

function getMesData(ano, mes) {
  const d = (dados[ano]||{})[mes] || {entradas:[],saidas:[]};
  const ent = sumArr(d.entradas);
  const cartao  = d.saidas.filter(s=>s.cat==='cartao').reduce((a,b)=>a+(+b.valor||0),0);
  const aluguel = d.saidas.filter(s=>s.cat==='aluguel').reduce((a,b)=>a+(+b.valor||0),0);
  const outros  = d.saidas.filter(s=>s.cat!=='cartao'&&s.cat!=='aluguel').reduce((a,b)=>a+(+b.valor||0),0);
  const saida = cartao+aluguel+outros;
  return {ent, cartao, aluguel, outros, saida, saldo: ent-saida, raw: d};
}

function ensureMes(ano, mes, tipo) {
  if(!dados[ano]) dados[ano]={};
  if(!dados[ano][mes]) dados[ano][mes]={entradas:[],saidas:[]};
  if(!dados[ano][mes][tipo]) dados[ano][mes][tipo]=[];
}
function ensureInv(ano, mes) {
  if(!investimentos[ano]) investimentos[ano]={};
  if(!investimentos[ano][mes]) investimentos[ano][mes]=[];
}

// ── Navegação ─────────────────────────────────────────────
function showPage(id) {
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  document.querySelector(`[onclick="showPage('${id}')"]`).classList.add('active');
  if(id==='lancamento')   buildPeriodSelector('lanc', ()=>loadMesForm());
  if(id==='investimentos'){ buildPeriodSelector('inv', ()=>loadInvestimentos()); renderInvResumo(); }
  if(id==='historico')    { buildPeriodSelector('hist', ()=>renderHistContent()); }
  if(id==='graficos')     setTimeout(buildGraficos,50);
  if(id==='parcelas')     renderParcelas();
}

// ── Seletor de período genérico ────────────────────────────
function buildPeriodSelector(ctx, onChange) {
  const suffix = ctx==='lanc'?'lanc': ctx==='inv'?'inv':'hist';
  const anoCont = document.getElementById(`ano-btns-${suffix}`);
  const mesCont = document.getElementById(`mes-btns-${suffix}`);

  anoCont.innerHTML = '';
  ANOS.forEach(a => {
    const btn = document.createElement('button');
    btn.className = 'period-btn' + (a===anoAtivo[ctx]?' active':'');
    btn.textContent = a;
    btn.onclick = () => {
      anoAtivo[ctx] = a;
      anoCont.querySelectorAll('.period-btn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      buildMesBtns(ctx, mesCont, onChange);
      onChange();
    };
    anoCont.appendChild(btn);
  });
  buildMesBtns(ctx, mesCont, onChange);
}

function buildMesBtns(ctx, mesCont, onChange) {
  mesCont.innerHTML = '';
  MESES_NOMES.forEach((m,i) => {
    const key = String(i+1);
    const btn = document.createElement('button');
    btn.className = 'period-btn' + (key===mesAtivo[ctx]?' active':'');
    btn.textContent = m;
    btn.onclick = () => {
      mesAtivo[ctx] = key;
      mesCont.querySelectorAll('.period-btn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      onChange();
    };
    mesCont.appendChild(btn);
  });
}

// ═══════════════════════════════════════════════════════
//  DASHBOARD
// ═══════════════════════════════════════════════════════
function buildDashboard() {
  const ano = '2026';
  const allKeys = Object.keys(dados[ano]||{}).sort((a,b)=>+a-+b);
  const realKeys = allKeys.filter(k=>getMesData(ano,k).saida>0);
  if(!realKeys.length) return;

  let totEnt=0,totSai=0,totCart=0,totAlug=0,totOut=0;
  realKeys.forEach(k=>{const m=getMesData(ano,k);totEnt+=m.ent;totSai+=m.saida;totCart+=m.cartao;totAlug+=m.aluguel;totOut+=m.outros;});

  const cards = [
    {label:'Total gasto', value:brl(totSai), cls:'red', sub:'Meses com lançamento'},
    {label:'Média mensal', value:brl(totSai/realKeys.length), cls:'yellow', sub:'de gastos'},
    {label:'Maior mês',    value:getMesLabel(ano, realKeys.reduce((a,b)=>getMesData(ano,a).saida>getMesData(ano,b).saida?a:b)), cls:'blue', sub: brl(Math.max(...realKeys.map(k=>getMesData(ano,k).saida)))},
    {label:'Total entradas', value:brl(totEnt), cls:'green', sub:'Receitas lançadas'},
  ];
  document.getElementById('dash-cards').innerHTML = cards.map(c=>`
    <div class="card ${c.cls}">
      <div class="card-label">${c.label}</div>
      <div class="card-value">${c.value}</div>
      <div class="card-sub">${c.sub}</div>
    </div>`).join('');

  const labels = realKeys.map(k=>getMesLabel(ano,k));
  const cartaoD = realKeys.map(k=>getMesData(ano,k).cartao);
  const aluguelD= realKeys.map(k=>getMesData(ano,k).aluguel);
  const outrosD = realKeys.map(k=>getMesData(ano,k).outros);

  // Table
  document.getElementById('dash-table-body').innerHTML = realKeys.map((k,i)=>{
    const m=getMesData(ano,k);
    return `<tr>
      <td>${labels[i]}</td>
      <td class="mono pos">${m.ent>0?brl(m.ent):'—'}</td>
      <td class="mono">${brl(m.cartao)}</td>
      <td class="mono">${brl(m.aluguel)}</td>
      <td class="mono">${brl(m.outros)}</td>
      <td class="mono neg">${brl(m.saida)}</td>
      <td class="mono ${m.saldo>=0?'pos':'neg'}">${brl(m.saldo)}</td>
    </tr>`;
  }).join('');

  const gc = 'rgba(0,0,0,.04)'; const tc = '#6b7280';
  new Chart(document.getElementById('chartBarDash'),{type:'bar',
    data:{labels,datasets:[
      {label:'Cartão', data:cartaoD, backgroundColor:'rgba(37,99,235,.75)', borderRadius:3, stack:'s'},
      {label:'Aluguel',data:aluguelD,backgroundColor:'rgba(22,163,74,.75)', borderRadius:3, stack:'s'},
      {label:'Outros', data:outrosD, backgroundColor:'rgba(124,58,237,.75)',borderRadius:3, stack:'s'},
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:12}}}},
      scales:{x:{stacked:true,ticks:{color:tc},grid:{color:gc}},y:{stacked:true,ticks:{color:tc,callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:gc}}}}
  });

  new Chart(document.getElementById('chartPieDash'),{type:'doughnut',
    data:{labels:['Cartão','Aluguel','Outros'],datasets:[{data:[totCart,totAlug,totOut],backgroundColor:['#2563eb','#16a34a','#7c3aed'],borderWidth:2,borderColor:'#fff'}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'62%',
      plugins:{legend:{position:'bottom',labels:{color:tc,font:{size:12},padding:16}},
        tooltip:{callbacks:{label:ctx=>{const t=totCart+totAlug+totOut;return ` ${brl(ctx.raw)} (${(ctx.raw/t*100).toFixed(1)}%)`;}}}}
    }
  });

  new Chart(document.getElementById('chartLineDash'),{type:'bar',
    data:{labels,datasets:[
      {type:'bar',label:'Entradas',data:realKeys.map(k=>getMesData(ano,k).ent),backgroundColor:'rgba(22,163,74,.65)',borderRadius:4},
      {type:'line',label:'Saídas',data:realKeys.map(k=>getMesData(ano,k).saida),borderColor:'#dc2626',backgroundColor:'rgba(220,38,38,.08)',tension:.35,fill:true,pointRadius:4,pointBackgroundColor:'#dc2626'},
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:12}}}},
      scales:{x:{ticks:{color:tc},grid:{color:gc}},y:{ticks:{color:tc,callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:gc}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  LANÇAMENTO
// ═══════════════════════════════════════════════════════
function loadMesForm() {
  const ano = anoAtivo.lanc; const mes = mesAtivo.lanc;
  ensureMes(ano, mes, 'entradas');
  ensureMes(ano, mes, 'saidas');
  renderItems('entradas', dados[ano][mes].entradas);
  renderItems('saidas',   dados[ano][mes].saidas);
  recalcTotais();
}

function renderItems(tipo, items) {
  const list = document.getElementById(tipo+'-list');
  list.innerHTML = items.map((item,i)=>`
    <div class="lancamento-item ${tipo}">
      <input type="text" placeholder="${tipo==='entradas'?'Ex: Salário, Freelance...':'Ex: iFood, Mercado...'}" value="${item.desc||''}" oninput="updateItem('${tipo}',${i},'desc',this.value)">
      ${tipo==='saidas'?`<select onchange="updateItem('saidas',${i},'cat',this.value)">
        ${CAT_SAIDAS.map(c=>`<option value="${c.value}" ${item.cat===c.value?'selected':''}>${c.label}</option>`).join('')}
      </select>`:''}
      <input type="number" placeholder="0,00" value="${item.valor||''}" step="0.01" min="0" oninput="updateItem('${tipo}',${i},'valor',this.value)">
      <button class="remove-btn" onclick="removeItem('${tipo}',${i})">×</button>
    </div>`).join('');
}

function updateItem(tipo,idx,field,val) {
  const ano=anoAtivo.lanc; const mes=mesAtivo.lanc;
  dados[ano][mes][tipo][idx][field] = field==='valor'?parseFloat(val)||0:val;
  recalcTotais();
}
function addItem(tipo) {
  const ano=anoAtivo.lanc; const mes=mesAtivo.lanc;
  ensureMes(ano,mes,tipo);
  dados[ano][mes][tipo].push(tipo==='entradas'?{desc:'',valor:0}:{desc:'',cat:'outros',valor:0});
  renderItems(tipo, dados[ano][mes][tipo]);
  recalcTotais();
}
function removeItem(tipo,idx) {
  const ano=anoAtivo.lanc; const mes=mesAtivo.lanc;
  dados[ano][mes][tipo].splice(idx,1);
  renderItems(tipo, dados[ano][mes][tipo]);
  recalcTotais();
}
function recalcTotais() {
  const ano=anoAtivo.lanc; const mes=mesAtivo.lanc;
  const d=(dados[ano]||{})[mes]||{entradas:[],saidas:[]};
  const ent=sumArr(d.entradas); const sai=sumArr(d.saidas);
  const saldo=ent-sai;
  document.getElementById('total-entradas').textContent=brl(ent);
  document.getElementById('total-saidas').textContent=brl(sai);
  const el=document.getElementById('saldo-mes');
  el.textContent=brl(saldo); el.style.color=saldo>=0?'var(--green)':'var(--red)';
}
async function salvarMes() {
  const ano=anoAtivo.lanc; const mes=mesAtivo.lanc;
  const d=(dados[ano]||{})[mes]||{entradas:[],saidas:[]};
  const btn=document.querySelector('[onclick="salvarMes()"]');
  btn.disabled=true; btn.textContent='Salvando...';

  try {
    // Apaga registros antigos do mês
    await sb.from('lancamentos').delete().eq('ano',ano).eq('mes',mes);

    const rows=[
      ...d.entradas.filter(e=>e.desc||e.valor).map(e=>({ano,mes,tipo:'entrada',descricao:e.desc,categoria:null,valor:+e.valor||0})),
      ...d.saidas.filter(s=>s.desc||s.valor).map(s=>({ano,mes,tipo:'saida',descricao:s.desc,categoria:s.cat,valor:+s.valor||0}))
    ];
    if(rows.length){
      const {error}=await sb.from('lancamentos').insert(rows);
      if(error) throw error;
    }
    showToast('✓ Mês salvo no Supabase!');
    graficosBuilt=false; // força rebuild dos gráficos
  } catch(e) {
    showToast('❌ Erro: '+e.message);
    console.error(e);
  } finally {
    btn.disabled=false; btn.textContent='✓ Salvar mês';
  }
}

// ═══════════════════════════════════════════════════════
//  INVESTIMENTOS
// ═══════════════════════════════════════════════════════
function loadInvestimentos() {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  ensureInv(ano,mes);
  renderInvestimentos();
  renderInvResumo();
}

function renderInvestimentos() {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  ensureInv(ano,mes);
  const list = document.getElementById('investimentos-list');
  const items = investimentos[ano][mes];
  list.innerHTML = items.map((item,i)=>`
    <div class="lancamento-item investimentos">
      <input type="text" placeholder="DD/MM/AAAA" value="${item.data||''}" oninput="updateInv(${i},'data',this.value)">
      <input type="text" placeholder="Ex: Tesouro Direto, CDB..." value="${item.nome||''}" oninput="updateInv(${i},'nome',this.value)">
      <input type="number" placeholder="0,00" value="${item.valor||''}" step="0.01" min="0" oninput="updateInv(${i},'valor',this.value)">
      <button class="remove-btn" onclick="removeInv(${i})">×</button>
    </div>`).join('');
  recalcInv();
}

function updateInv(idx,field,val) {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  investimentos[ano][mes][idx][field]=field==='valor'?parseFloat(val)||0:val;
  recalcInv(); renderInvResumo();
}
function addInvestimento() {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  ensureInv(ano,mes);
  investimentos[ano][mes].push({data:'',nome:'',valor:0});
  renderInvestimentos();
}
function removeInv(idx) {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  investimentos[ano][mes].splice(idx,1);
  renderInvestimentos(); renderInvResumo();
}
function recalcInv() {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  const items=(investimentos[ano]||{})[mes]||[];
  document.getElementById('total-inv').textContent=brl(sumArr(items));
}

async function salvarInvestimentos() {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv;
  const items=(investimentos[ano]||{})[mes]||[];
  const btn=document.querySelector('[onclick="salvarInvestimentos()"]');
  btn.disabled=true; btn.textContent='Salvando...';

  try {
    await sb.from('investimentos').delete().eq('ano',ano).eq('mes',mes);
    const rows=items.filter(i=>i.nome||i.valor).map(i=>({ano,mes,data_invest:i.data||null,nome:i.nome||'',valor:+i.valor||0}));
    if(rows.length){
      const {error}=await sb.from('investimentos').insert(rows);
      if(error) throw error;
    }
    showToast('✓ Investimentos salvos no Supabase!');
    renderInvResumo();
  } catch(e) {
    showToast('❌ Erro: '+e.message);
    console.error(e);
  } finally {
    btn.disabled=false; btn.textContent='✓ Salvar investimentos';
  }
}

function renderInvResumo() {
  // Agrupa todos os investimentos por nome (somando)
  const agrupado = {};
  Object.values(investimentos).forEach(porAno=>{
    Object.values(porAno).forEach(lista=>{
      lista.forEach(item=>{
        if(!item.nome||!item.valor) return;
        const key = item.nome.trim().toLowerCase();
        if(!agrupado[key]) agrupado[key]={nome:item.nome.trim(),total:0};
        agrupado[key].total += +item.valor||0;
      });
    });
  });

  const items = Object.values(agrupado).sort((a,b)=>b.total-a.total);
  const grandTotal = items.reduce((a,b)=>a+b.total,0);
  const PALETTE = ['#2563eb','#16a34a','#7c3aed','#d97706','#0891b2','#dc2626','#8b5cf6','#059669','#ea580c','#6366f1'];

  document.getElementById('inv-resumo-body').innerHTML = items.length
    ? items.map((item,i)=>`<tr>
        <td><span style="display:inline-block;width:10px;height:10px;border-radius:2px;background:${PALETTE[i%PALETTE.length]};margin-right:8px;vertical-align:middle"></span>${item.nome}</td>
        <td class="mono" style="color:var(--teal);font-weight:600">${brl(item.total)}</td>
        <td class="mono" style="color:var(--muted)">${grandTotal?(item.total/grandTotal*100).toFixed(1)+'%':'—'}</td>
      </tr>`).join('')
    : '<tr><td colspan="3" style="text-align:center;color:var(--muted);padding:20px">Nenhum investimento lançado ainda</td></tr>';

  // Gráfico pizza
  if(window._invChart) window._invChart.destroy();
  if(!items.length) return;
  window._invChart = new Chart(document.getElementById('chartInvPie'),{
    type:'doughnut',
    data:{
      labels:items.map(i=>i.nome),
      datasets:[{data:items.map(i=>i.total),backgroundColor:PALETTE.slice(0,items.length),borderWidth:2,borderColor:'#fff'}]
    },
    options:{responsive:true,maintainAspectRatio:false,cutout:'55%',
      plugins:{legend:{position:'bottom',labels:{color:'#6b7280',font:{size:12},padding:12}},
        tooltip:{callbacks:{label:ctx=>` ${brl(ctx.raw)} (${(ctx.raw/grandTotal*100).toFixed(1)}%)`}}}
    }
  });
}

// ═══════════════════════════════════════════════════════
//  HISTÓRICO
// ═══════════════════════════════════════════════════════
function renderHistContent() {
  const ano=anoAtivo.hist; const mes=mesAtivo.hist;
  const m=getMesData(ano,mes);
  const d=m.raw; const label=getMesLabel(ano,mes);
  document.getElementById('hist-content').innerHTML=`
    <div class="cards-row" style="margin-bottom:20px">
      <div class="card green"><div class="card-label">Entradas</div><div class="card-value">${brl(m.ent)}</div></div>
      <div class="card red"><div class="card-label">Saídas</div><div class="card-value">${brl(m.saida)}</div></div>
      <div class="card ${m.saldo>=0?'green':'red'}"><div class="card-label">Saldo</div><div class="card-value">${brl(m.saldo)}</div></div>
    </div>
    <div class="grid2">
      <div class="table-card">
        <div class="table-head"><h3>Entradas — ${label}</h3></div>
        <table><thead><tr><th>Descrição</th><th>Valor</th></tr></thead>
        <tbody>${(d.entradas||[]).length?(d.entradas||[]).map(e=>`<tr><td>${e.desc}</td><td class="mono pos">${brl(e.valor)}</td></tr>`).join(''):'<tr><td colspan="2" style="color:var(--muted);text-align:center;padding:20px">Sem entradas lançadas</td></tr>'}
        <tr style="background:var(--bg3)"><td style="font-weight:700">Total</td><td class="mono pos">${brl(m.ent)}</td></tr>
        </tbody></table>
      </div>
      <div class="table-card">
        <div class="table-head"><h3>Saídas — ${label}</h3></div>
        <table><thead><tr><th>Descrição</th><th>Categoria</th><th>Valor</th></tr></thead>
        <tbody>${(d.saidas||[]).filter(s=>s.valor>0).map(s=>{
          const catLabel = (CAT_SAIDAS.find(c=>c.value===s.cat)||{label:s.cat}).label;
          return `<tr><td>${s.desc}</td><td><span class="badge badge-blue">${catLabel}</span></td><td class="mono neg">${brl(s.valor)}</td></tr>`;
        }).join('')}
        <tr style="background:var(--bg3)"><td style="font-weight:700" colspan="2">Total</td><td class="mono neg">${brl(m.saida)}</td></tr>
        </tbody></table>
      </div>
    </div>`;
}

// ═══════════════════════════════════════════════════════
//  GRÁFICOS
// ═══════════════════════════════════════════════════════
let graficosBuilt=false;
function buildGraficos() {
  if(graficosBuilt) return; graficosBuilt=true;
  const ano='2026';
  const realKeys=Object.keys(dados[ano]||{}).sort((a,b)=>+a-+b).filter(k=>getMesData(ano,k).saida>0);
  const labels=realKeys.map(k=>getMesLabel(ano,k));
  const cartaoD=realKeys.map(k=>getMesData(ano,k).cartao);
  const aluguelD=realKeys.map(k=>getMesData(ano,k).aluguel);
  const outrosD=realKeys.map(k=>getMesData(ano,k).outros);
  const saldoD=realKeys.map(k=>getMesData(ano,k).saldo);
  const totCart=cartaoD.reduce((a,b)=>a+b,0);
  const totAlug=aluguelD.reduce((a,b)=>a+b,0);
  const totOut=outrosD.reduce((a,b)=>a+b,0);
  const gc='rgba(0,0,0,.04)'; const tc='#6b7280';

  new Chart(document.getElementById('chartBarG'),{type:'bar',
    data:{labels,datasets:[
      {label:'Cartão',data:cartaoD,backgroundColor:'rgba(37,99,235,.75)',borderRadius:3,stack:'s'},
      {label:'Aluguel',data:aluguelD,backgroundColor:'rgba(22,163,74,.75)',borderRadius:3,stack:'s'},
      {label:'Outros',data:outrosD,backgroundColor:'rgba(124,58,237,.75)',borderRadius:3,stack:'s'},
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:12}}}},
      scales:{x:{stacked:true,ticks:{color:tc},grid:{color:gc}},y:{stacked:true,ticks:{color:tc,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gc}}}}
  });
  new Chart(document.getElementById('chartSaldoG'),{type:'bar',
    data:{labels,datasets:[{label:'Saldo',data:saldoD,backgroundColor:saldoD.map(v=>v>=0?'rgba(22,163,74,.7)':'rgba(220,38,38,.7)'),borderRadius:4}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
      scales:{x:{ticks:{color:tc},grid:{color:gc}},y:{ticks:{color:tc,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gc}}}}
  });
  new Chart(document.getElementById('chartPieG'),{type:'doughnut',
    data:{labels:['Cartão','Aluguel','Outros'],datasets:[{data:[totCart,totAlug,totOut],backgroundColor:['#2563eb','#16a34a','#7c3aed'],borderWidth:2,borderColor:'#fff'}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'58%',
      plugins:{legend:{position:'bottom',labels:{color:tc,font:{size:12},padding:16}},
        tooltip:{callbacks:{label:ctx=>{const t=totCart+totAlug+totOut;return ` ${brl(ctx.raw)} (${(ctx.raw/t*100).toFixed(1)}%)`;}}}}
    }
  });
  new Chart(document.getElementById('chartVsG'),{type:'bar',
    data:{labels,datasets:[
      {label:'Entradas',data:realKeys.map(k=>getMesData(ano,k).ent),backgroundColor:'rgba(22,163,74,.7)',borderRadius:4},
      {label:'Saídas',data:realKeys.map(k=>getMesData(ano,k).saida),backgroundColor:'rgba(220,38,38,.7)',borderRadius:4},
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:12}}}},
      scales:{x:{ticks:{color:tc},grid:{color:gc}},y:{ticks:{color:tc,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gc}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  PARCELAS
// ═══════════════════════════════════════════════════════
function renderParcelas() {
  const totais=Array(8).fill(0);
  document.getElementById('parcelas-body').innerHTML=PARCELAS_DATA.map(p=>{
    p.vals.forEach((v,i)=>totais[i]+=v);
    return `<tr><td>${p.desc}</td>${p.vals.map(v=>`<td class="mono" style="color:${v?'var(--text2)':'var(--muted)'}">${v?brl(v):'—'}</td>`).join('')}</tr>`;
  }).join('')+`<tr style="background:var(--bg3)"><td style="font-weight:700">Total por mês</td>${totais.map(v=>`<td class="mono" style="font-weight:700;color:var(--yellow)">${brl(v)}</td>`).join('')}</tr>`;

  const mesesP=['Jun/26','Jul/26','Ago/26','Set/26','Out/26','Nov/26','Dez/26','Jan/27'];
  if(window._parcelasChart) window._parcelasChart.destroy();
  window._parcelasChart=new Chart(document.getElementById('chartParcelasG'),{type:'bar',
    data:{labels:mesesP,datasets:[{label:'Total parcelas',data:totais,backgroundColor:'rgba(217,119,6,.75)',borderRadius:6}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
      scales:{x:{ticks:{color:'#6b7280'},grid:{color:'rgba(0,0,0,.04)'}},y:{ticks:{color:'#6b7280',callback:v=>'R$'+v.toFixed(0)},grid:{color:'rgba(0,0,0,.04)'}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  TOAST
// ═══════════════════════════════════════════════════════
function showToast(msg='✓ Salvo!') {
  const t=document.getElementById('toast');
  t.textContent=msg; t.classList.add('show');
  setTimeout(()=>t.classList.remove('show'),2500);
}

// ═══════════════════════════════════════════════════════
//  INIT + CARREGAR SUPABASE
// ═══════════════════════════════════════════════════════
async function carregarDados() {
  try {
    // Lançamentos
    const {data:lanc, error:e1} = await sb.from('lancamentos').select('*');
    if(e1) throw e1;
    if(lanc && lanc.length) {
      // Limpa dados default apenas se há dados no Supabase
      dados = {};
      lanc.forEach(row => {
        if(!dados[row.ano]) dados[row.ano]={};
        if(!dados[row.ano][row.mes]) dados[row.ano][row.mes]={entradas:[],saidas:[]};
        if(row.tipo==='entrada') dados[row.ano][row.mes].entradas.push({desc:row.descricao||'',valor:+row.valor||0});
        else dados[row.ano][row.mes].saidas.push({desc:row.descricao||'',cat:row.categoria||'outros',valor:+row.valor||0});
      });
    }

    // Investimentos
    const {data:inv, error:e2} = await sb.from('investimentos').select('*');
    if(e2) throw e2;
    if(inv && inv.length) {
      investimentos = {};
      inv.forEach(row => {
        if(!investimentos[row.ano]) investimentos[row.ano]={};
        if(!investimentos[row.ano][row.mes]) investimentos[row.ano][row.mes]=[];
        investimentos[row.ano][row.mes].push({data:row.data_invest||'',nome:row.nome||'',valor:+row.valor||0});
      });
    }

    document.getElementById('sync-badge').innerHTML = '<span class="sync-dot" style="background:#15803d;animation:none"></span>Conectado';
    document.getElementById('sync-badge').style.background = 'rgba(20,83,45,.1)';
    document.getElementById('sync-badge').style.color = '#15803d';
  } catch(err) {
    console.error('Supabase erro:', err);
    document.getElementById('sync-badge').innerHTML = '⚠ Offline';
    document.getElementById('sync-badge').style.background = 'rgba(153,27,27,.1)';
    document.getElementById('sync-badge').style.color = '#991b1b';
  } finally {
    document.getElementById('loading-overlay').style.display = 'none';
    buildDashboard();
    buildPeriodSelector('lanc', ()=>loadMesForm());
  }
}

carregarDados();
</script>
</body>
</html>

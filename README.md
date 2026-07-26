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
  .table-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); overflow: auto; box-shadow: var(--shadow); }
  .table-head { padding: 16px 20px; border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between; }
  .table-head h3 { font-size: 13px; font-weight: 600; color: var(--text); }

  table { width: 100%; border-collapse: collapse; min-width: 600px; }
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

  .btn { display: inline-flex; align-items: center; gap: 7px; padding: 10px 20px; border-radius: 8px; font-size: 13px; font-weight: 600; font-family: 'Inter', sans-serif; cursor: pointer; border: none; transition: opacity .15s, transform .1s; }
  .btn:hover { opacity: .88; }
  .btn:active { transform: scale(.98); }
  .btn-primary { background: var(--blue); color: #fff; }
  .btn-danger  { background: var(--red);  color: #fff; }
  .btn-ghost   { background: var(--bg3);  color: var(--text2); border: 1px solid var(--border); }
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
  .remove-btn { background: none; border: 1px solid transparent; color: var(--muted); cursor: pointer; font-size: 18px; line-height: 1; padding: 4px 7px; border-radius: 6px; }
  .remove-btn:hover { color: var(--red); background: var(--red-dim); border-color: var(--red-dim); }

  .total-display { background: var(--bg3); border: 1px solid var(--border); border-radius: 8px; padding: 12px 16px; display: flex; justify-content: space-between; align-items: center; margin-top: 10px; }
  .total-display span { font-size: 12px; color: var(--muted); font-weight: 600; text-transform: uppercase; letter-spacing: .05em; }
  .total-display strong { font-family: var(--mono); font-size: 16px; color: var(--text); }

  .col-headers { display: grid; gap: 8px; padding: 0 0 4px; }
  .col-headers.entradas    { grid-template-columns: 2fr 1fr 28px; }
  .col-headers.saidas      { grid-template-columns: 2fr 1.4fr 1fr 28px; }
  .col-headers.investimentos { grid-template-columns: 1fr 1.5fr 1fr 28px; }
  .col-headers span { font-size: 11px; font-weight: 600; color: var(--muted); text-transform: uppercase; letter-spacing: .04em; }

  /* PARCELAS FORM */
  .parcela-form { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 20px; display: grid; grid-template-columns: 2fr 1fr 1fr 1fr 1fr auto; gap: 12px; align-items: end; margin-bottom: 24px; box-shadow: var(--shadow); }
  @media(max-width:900px){ .parcela-form { grid-template-columns: 1fr; } }

  #toast { position: fixed; bottom: 28px; right: 28px; background: var(--green); color: #fff; padding: 12px 20px; border-radius: 10px; font-size: 13px; font-weight: 600; opacity: 0; transform: translateY(8px); transition: all .25s; pointer-events: none; z-index: 9999; box-shadow: 0 4px 16px rgba(0,0,0,.15); }
  #toast.show { opacity: 1; transform: translateY(0); }

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

<nav>
  <div class="nav-logo">💰 Financeiro<span>.</span><br><span class="sync-badge" id="sync-badge"><span class="sync-dot"></span>Supabase</span></div>
  <button class="nav-item active" onclick="showPage('dashboard')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/></svg> Dashboard
  </button>
  <button class="nav-item" onclick="showPage('lancamento')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 5v14M5 12h14"/></svg> Lançar Mês
  </button>
  <button class="nav-item" onclick="showPage('investimentos')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M3 17l4-8 4 4 4-6 4 4"/><circle cx="21" cy="11" r="2"/></svg> Investimentos
  </button>
  <button class="nav-item" onclick="showPage('historico')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M3 12h18M3 6h18M3 18h18"/></svg> Histórico
  </button>
  <button class="nav-item" onclick="showPage('graficos')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M18 20V10M12 20V4M6 20v-6"/></svg> Gráficos
  </button>
  <button class="nav-item" onclick="showPage('parcelas')">
    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="4" width="18" height="18" rx="2"/><path d="M16 2v4M8 2v4M3 10h18"/></svg> Parcelas
  </button>
</nav>

<main>
<!-- ══ DASHBOARD ══ -->
<div class="page active" id="page-dashboard">
  <div class="page-header"><h1>Dashboard</h1><p>Visão geral dos seus gastos</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-dash" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-dash" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div class="cards-row" id="dash-cards"></div>
  <div class="grid2 wide">
    <div class="chart-card">
      <div class="chart-title" id="title-bar-dash">Evolução no ano</div>
      <div class="chart-wrap" style="height:260px"><canvas id="chartBarDash"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title" id="title-pie-dash">Distribuição no mês selecionado</div>
      <div class="chart-wrap" style="height:260px"><canvas id="chartPieDash"></canvas></div>
    </div>
  </div>
  <div class="table-card">
    <div class="table-head"><h3>Resumo anual</h3></div>
    <table><thead><tr><th>Mês</th><th>Entradas</th><th>Saídas</th><th>Saldo</th></tr></thead>
    <tbody id="dash-table-body"></tbody></table>
  </div>
</div>

<!-- ══ LANÇAMENTO ══ -->
<div class="page" id="page-lancamento">
  <div class="page-header"><h1>Lançar Mês</h1><p>Registre entradas e saídas do mês selecionado</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-lanc" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-lanc" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div class="form-section">
    <div class="form-section-title">Entradas</div>
    <div class="col-headers entradas"><span>Descrição</span><span>Valor (R$)</span><span></span></div>
    <div id="entradas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addItem('entradas')">+ Adicionar entrada</button>
    <div class="total-display" style="border-left:3px solid var(--green);margin-top:12px"><span>Total Entradas</span><strong class="pos" id="total-entradas">R$ 0,00</strong></div>
  </div>
  <div class="form-section">
    <div class="form-section-title">Saídas</div>
    <div class="col-headers saidas"><span>Descrição</span><span>Categoria</span><span>Valor (R$)</span><span></span></div>
    <div id="saidas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addItem('saidas')">+ Adicionar saída</button>
    <div class="total-display" style="border-left:3px solid var(--red);margin-top:12px"><span>Total Saídas</span><strong class="neg" id="total-saidas">R$ 0,00</strong></div>
  </div>
  <div class="saldo-bar">
    <div><div class="saldo-bar-label">Saldo do mês</div><div class="saldo-bar-value" id="saldo-mes">R$ 0,00</div></div>
    <button class="btn btn-primary" onclick="salvarMes()">✓ Salvar mês</button>
  </div>
</div>

<!-- ══ INVESTIMENTOS ══ -->
<div class="page" id="page-investimentos">
  <div class="page-header"><h1>Investimentos</h1><p>Registre e acompanhe seus investimentos</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-inv" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-inv" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div class="form-section">
    <div class="form-section-title">Lançar investimento</div>
    <div class="col-headers investimentos"><span>Data</span><span>Nome do investimento</span><span>Valor (R$)</span><span></span></div>
    <div id="investimentos-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:7px 14px" onclick="addInvestimento()">+ Adicionar investimento</button>
    <div class="total-display" style="border-left:3px solid var(--teal);margin-top:12px"><span>Total Investido no Mês</span><strong id="total-inv" style="color:var(--teal)">R$ 0,00</strong></div>
  </div>
  <div style="display:flex;gap:8px;margin-bottom:20px"><button class="btn btn-teal" onclick="salvarInvestimentos()">✓ Salvar investimentos</button></div>
  <div class="grid2">
    <div class="table-card">
      <div class="table-head"><h3>Por investimento (total acumulado)</h3></div>
      <table><thead><tr><th>Investimento</th><th>Total</th><th>%</th></tr></thead><tbody id="inv-resumo-body"></tbody></table>
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
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-hist" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-hist" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div id="hist-content"></div>
</div>

<!-- ══ GRÁFICOS ══ -->
<div class="page" id="page-graficos">
  <div class="page-header"><h1>Gráficos</h1><p>Análise visual dos seus dados</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-graf" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-graf" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div class="grid2" style="margin-bottom:20px">
    <div class="chart-card">
      <div class="chart-title" id="title-g-bar">Gastos por categoria (Ano)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartBarG"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title" id="title-g-saldo">Saldo mensal (Ano)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartSaldoG"></canvas></div>
    </div>
  </div>
  <div class="grid2">
    <div class="chart-card">
      <div class="chart-title" id="title-g-pie">Distribuição dos gastos (Mês)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartPieG"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-title" id="title-g-vs">Entradas vs Saídas (Ano)</div>
      <div class="chart-wrap" style="height:280px"><canvas id="chartVsG"></canvas></div>
    </div>
  </div>
</div>

<!-- ══ PARCELAS ══ -->
<div class="page" id="page-parcelas">
  <div class="page-header"><h1>Parcelas</h1><p>Gerencie compras parceladas sem poluir os lançamentos fixos mensais.</p></div>
  
  <div class="parcela-form">
    <div class="field"><label>Descrição da compra</label><input type="text" id="parc-desc" placeholder="Ex: Geladeira"></div>
    <div class="field"><label>Valor Parcela (R$)</label><input type="number" id="parc-valor" placeholder="0,00" step="0.01"></div>
    <div class="field"><label>Qtd. Parcelas</label><input type="number" id="parc-qtd" placeholder="10"></div>
    <div class="field"><label>Mês Início</label><select id="parc-mes"></select></div>
    <div class="field"><label>Ano Início</label><select id="parc-ano"></select></div>
    <button class="btn btn-primary" style="height:40px" onclick="adicionarParcela()">+ Adicionar</button>
  </div>

  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano para visualização</span><div id="ano-btns-parc" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>

  <div class="table-card" style="margin-bottom:20px">
    <div class="table-head"><h3>Cronograma de parcelas (<span id="label-ano-parc"></span>)</h3></div>
    <table id="table-parcelas">
      <thead><tr id="head-parcelas"></tr></thead>
      <tbody id="body-parcelas"></tbody>
    </table>
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
const sb = window.supabase.createClient(SUPABASE_URL, SUPABASE_KEY)

// ═══════════════════════════════════════════════════════
//  ESTRUTURA DE DADOS E CATEGORIAS ATUALIZADAS
// ═══════════════════════════════════════════════════════
const ANOS  = ['2025','2026','2027'];
const MESES_NOMES = ['Jan','Fev','Mar','Abr','Mai','Jun','Jul','Ago','Set','Out','Nov','Dez'];

let dados = {};
let investimentos = {};
let parcelas = []; // {id, desc, valor_parcela, qtd, mes_inicio, ano_inicio}

const CAT_SAIDAS = [
  {value:'aluguel_agua_luz', label:'Aluguel + Água + Luz'},
  {value:'alimentacao',      label:'Alimentação / Mercado'},
  {value:'delivery',         label:'Delivery / iFood'},
  {value:'transporte',       label:'Transporte / Uber / 99'},
  {value:'celular',          label:'Celular / Internet'},
  {value:'saude',            label:'Saúde / Farmácia'},
  {value:'educacao',         label:'Educação / Cursos'},
  {value:'lazer',            label:'Lazer / Entretenimento'},
  {value:'roupas',           label:'Roupas / Vestuário'},
  {value:'impostos',         label:'Impostos / Contador'},
  {value:'assinaturas',      label:'Assinaturas / Streaming'},
  {value:'viagem',           label:'Viagem / Hospedagem'},
  {value:'investimentos',    label:'Investimentos'},
  {value:'outros',           label:'Outros'}
];

const CAT_COLORS = {
  aluguel_agua_luz: '#16a34a', alimentacao: '#d97706', delivery: '#ea580c',
  transporte: '#7c3aed', celular: '#0891b2', saude: '#db2777', educacao: '#059669',
  lazer: '#8b5cf6', roupas: '#f59e0b', impostos: '#374151', assinaturas: '#6366f1',
  viagem: '#0d9488', investimentos: '#14532d', outros: '#9ca3af'
};

// ── Estado da navegação ────────────────────────
let anoAtivo = { lanc:'2026', inv:'2026', hist:'2026', dash:'2026', graf:'2026', parc:'2026' };
let mesAtivo = { lanc:'1',    inv:'6',    hist:'1', dash:'1', graf:'1' };

// ── Helpers ───────────────────────────────────────────────
const brl = v => 'R$ ' + (v||0).toLocaleString('pt-BR',{minimumFractionDigits:2,maximumFractionDigits:2});
const sumArr = arr => arr.reduce((a,b)=>a+(+b.valor||0),0);
const getMesLabel = (ano, mes) => `${MESES_NOMES[+mes-1]}/${String(ano).slice(2)}`;

// Extrai e soma categorias de forma dinâmica
function getMesData(ano, mes) {
  const d = (dados[ano]||{})[mes] || {entradas:[],saidas:[]};
  const ent = sumArr(d.entradas);
  const saida = sumArr(d.saidas);
  
  const categorias = {};
  d.saidas.forEach(s => {
    const c = s.cat || 'outros';
    categorias[c] = (categorias[c]||0) + (+s.valor||0);
  });

  return {ent, saida, saldo: ent-saida, categorias, raw: d};
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

// ── Navegação e Construção de Seletores ────────────────────
function showPage(id) {
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  document.querySelector(`[onclick="showPage('${id}')"]`).classList.add('active');
  
  if(id==='dashboard')    { buildPeriodSelector('dash', ()=>buildDashboard(), true); buildDashboard(); }
  if(id==='lancamento')   { buildPeriodSelector('lanc', ()=>loadMesForm()); loadMesForm(); }
  if(id==='investimentos'){ buildPeriodSelector('inv', ()=>loadInvestimentos()); loadInvestimentos(); }
  if(id==='historico')    { buildPeriodSelector('hist', ()=>renderHistContent()); renderHistContent(); }
  if(id==='graficos')     { buildPeriodSelector('graf', ()=>buildGraficos(), true); setTimeout(buildGraficos,50); }
  if(id==='parcelas')     { buildPeriodSelectorParc(); renderParcelas(); preencherSelectsParcela(); }
}

function buildPeriodSelector(ctx, onChange, includeMonth=true) {
  const anoCont = document.getElementById(`ano-btns-${ctx}`);
  const mesCont = document.getElementById(`mes-btns-${ctx}`);
  
  anoCont.innerHTML = '';
  ANOS.forEach(a => {
    const btn = document.createElement('button');
    btn.className = 'period-btn' + (a===anoAtivo[ctx]?' active':'');
    btn.textContent = a;
    btn.onclick = () => {
      anoAtivo[ctx] = a;
      anoCont.querySelectorAll('.period-btn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      if(includeMonth) buildMesBtns(ctx, mesCont, onChange);
      onChange();
    };
    anoCont.appendChild(btn);
  });
  if(includeMonth && mesCont) buildMesBtns(ctx, mesCont, onChange);
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
// Variáveis para guardar instância dos gráficos para destruí-los ao recriar
let chartBarD, chartPieD, chartLineD;

function buildDashboard() {
  const ano = anoAtivo.dash;
  const mes = mesAtivo.dash;
  const realKeys = Object.keys(dados[ano]||{}).sort((a,b)=>+a-+b).filter(k=>getMesData(ano,k).saida>0);
  
  // Cards do MÊS específico
  const m = getMesData(ano, mes);
  document.getElementById('dash-cards').innerHTML = `
    <div class="card ${m.saldo>=0?'green':'red'}"><div class="card-label">Saldo de ${MESES_NOMES[mes-1]}</div><div class="card-value">${brl(m.saldo)}</div><div class="card-sub">No mês selecionado</div></div>
    <div class="card green"><div class="card-label">Entradas</div><div class="card-value">${brl(m.ent)}</div></div>
    <div class="card red"><div class="card-label">Saídas</div><div class="card-value">${brl(m.saida)}</div></div>
  `;

  // Tabela anual (Resumo)
  document.getElementById('dash-table-body').innerHTML = realKeys.map((k)=>{
    const mk=getMesData(ano,k);
    return `<tr>
      <td>${getMesLabel(ano,k)}</td>
      <td class="mono pos">${brl(mk.ent)}</td>
      <td class="mono neg">${brl(mk.saida)}</td>
      <td class="mono ${mk.saldo>=0?'pos':'neg'}">${brl(mk.saldo)}</td>
    </tr>`;
  }).join('');

  // Prepara dados dos gráficos
  const labels = realKeys.map(k=>getMesLabel(ano,k));
  const tc = '#6b7280'; const gc = 'rgba(0,0,0,.04)';

  // Gráfico Evolução (Barra Empilhada Dinâmica)
  const allCatsFound = new Set();
  realKeys.forEach(k => Object.keys(getMesData(ano,k).categorias).forEach(c=>allCatsFound.add(c)));
  
  const datasetsBar = Array.from(allCatsFound).map(catKey => {
    const catInfo = CAT_SAIDAS.find(c=>c.value===catKey) || {label:catKey};
    return {
      label: catInfo.label,
      data: realKeys.map(k => getMesData(ano,k).categorias[catKey] || 0),
      backgroundColor: CAT_COLORS[catKey] || '#9ca3af',
      stack: 's', borderRadius: 3
    };
  });

  if(chartBarD) chartBarD.destroy();
  chartBarD = new Chart(document.getElementById('chartBarDash'),{
    type:'bar', data:{labels, datasets:datasetsBar},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
      scales:{x:{stacked:true,grid:{color:gc}},y:{stacked:true,grid:{color:gc}}}}
  });

  // Gráfico Pizza (Mês Selecionado)
  const catMes = Object.keys(m.categorias).map(c => ({
    label: (CAT_SAIDAS.find(x=>x.value===c)||{label:c}).label,
    val: m.categorias[c], color: CAT_COLORS[c]||'#9ca3af'
  })).filter(x=>x.val>0);

  if(chartPieD) chartPieD.destroy();
  chartPieD = new Chart(document.getElementById('chartPieDash'),{
    type:'doughnut',
    data:{labels:catMes.map(c=>c.label), datasets:[{data:catMes.map(c=>c.val), backgroundColor:catMes.map(c=>c.color), borderWidth:2,borderColor:'#fff'}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'62%',
      plugins:{legend:{position:'bottom',labels:{color:tc}}, tooltip:{callbacks:{label:ctx=>` ${brl(ctx.raw)}`}}}
    }
  });

  // Linha vs Barras (Entradas e Saídas do Ano)
  if(chartLineD) chartLineD.destroy();
  chartLineD = new Chart(document.getElementById('chartLineDash'),{
    type:'bar',
    data:{labels,datasets:[
      {type:'bar',label:'Entradas',data:realKeys.map(k=>getMesData(ano,k).ent),backgroundColor:'rgba(22,163,74,.65)',borderRadius:4},
      {type:'line',label:'Saídas',data:realKeys.map(k=>getMesData(ano,k).saida),borderColor:'#dc2626',backgroundColor:'rgba(220,38,38,.08)',tension:.35,fill:true,pointRadius:4,pointBackgroundColor:'#dc2626'}
    ]},
    options:{responsive:true,maintainAspectRatio:false,scales:{x:{grid:{color:gc}},y:{grid:{color:gc}}}}
  });
}

// ═══════════════════════════════════════════════════════
//  LANÇAMENTO
// ═══════════════════════════════════════════════════════
function loadMesForm() {
  const ano = anoAtivo.lanc; const mes = mesAtivo.lanc;
  ensureMes(ano, mes, 'entradas'); ensureMes(ano, mes, 'saidas');
  renderItems('entradas', dados[ano][mes].entradas);
  renderItems('saidas',   dados[ano][mes].saidas);
  recalcTotais();
}

function renderItems(tipo, items) {
  document.getElementById(tipo+'-list').innerHTML = items.map((item,i)=>`
    <div class="lancamento-item ${tipo}">
      <input type="text" placeholder="${tipo==='entradas'?'Ex: Salário':'Ex: Mercado'}" value="${item.desc||''}" oninput="updateItem('${tipo}',${i},'desc',this.value)">
      ${tipo==='saidas'?`<select onchange="updateItem('saidas',${i},'cat',this.value)">
        ${CAT_SAIDAS.map(c=>`<option value="${c.value}" ${item.cat===c.value?'selected':''}>${c.label}</option>`).join('')}
      </select>`:''}
      <input type="number" placeholder="0,00" value="${item.valor||''}" step="0.01" min="0" oninput="updateItem('${tipo}',${i},'valor',this.value)">
      <button class="remove-btn" onclick="removeItem('${tipo}',${i})">×</button>
    </div>`).join('');
}

function updateItem(tipo,idx,field,val) { dados[anoAtivo.lanc][mesAtivo.lanc][tipo][idx][field] = field==='valor'?parseFloat(val)||0:val; recalcTotais(); }
function addItem(tipo) { ensureMes(anoAtivo.lanc,mesAtivo.lanc,tipo); dados[anoAtivo.lanc][mesAtivo.lanc][tipo].push(tipo==='entradas'?{desc:'',valor:0}:{desc:'',cat:'outros',valor:0}); loadMesForm(); }
function removeItem(tipo,idx) { dados[anoAtivo.lanc][mesAtivo.lanc][tipo].splice(idx,1); loadMesForm(); }
function recalcTotais() {
  const d=(dados[anoAtivo.lanc]||{})[mesAtivo.lanc]||{entradas:[],saidas:[]};
  const ent=sumArr(d.entradas); const sai=sumArr(d.saidas);
  document.getElementById('total-entradas').textContent=brl(ent);
  document.getElementById('total-saidas').textContent=brl(sai);
  const el=document.getElementById('saldo-mes'); el.textContent=brl(ent-sai); el.style.color=(ent-sai)>=0?'var(--green)':'var(--red)';
}

async function salvarMes() {
  const ano=anoAtivo.lanc; const mes=mesAtivo.lanc;
  const d=(dados[ano]||{})[mes]||{entradas:[],saidas:[]};
  const btn=document.querySelector('[onclick="salvarMes()"]'); btn.disabled=true; btn.textContent='Salvando...';
  try {
    await sb.from('lancamentos').delete().eq('ano',ano).eq('mes',mes);
    const rows=[
      ...d.entradas.filter(e=>e.desc||e.valor).map(e=>({ano,mes,tipo:'entrada',descricao:e.desc,categoria:null,valor:+e.valor||0})),
      ...d.saidas.filter(s=>s.desc||s.valor).map(s=>({ano,mes,tipo:'saida',descricao:s.desc,categoria:s.cat,valor:+s.valor||0}))
    ];
    if(rows.length) { const {error}=await sb.from('lancamentos').insert(rows); if(error) throw error; }
    showToast('✓ Mês salvo!'); buildDashboard(); buildGraficos();
  } catch(e) { showToast('❌ Erro!'); console.error(e); } finally { btn.disabled=false; btn.textContent='✓ Salvar mês'; }
}

// ═══════════════════════════════════════════════════════
//  INVESTIMENTOS
// ═══════════════════════════════════════════════════════
function loadInvestimentos() {
  const ano=anoAtivo.inv; const mes=mesAtivo.inv; ensureInv(ano,mes);
  document.getElementById('investimentos-list').innerHTML = investimentos[ano][mes].map((item,i)=>`
    <div class="lancamento-item investimentos">
      <input type="text" placeholder="DD/MM/AAAA" value="${item.data||''}" oninput="investimentos['${ano}']['${mes}'][${i}].data=this.value">
      <input type="text" placeholder="Ex: Tesouro..." value="${item.nome||''}" oninput="investimentos['${ano}']['${mes}'][${i}].nome=this.value;renderInvResumo()">
      <input type="number" placeholder="0,00" value="${item.valor||''}" oninput="investimentos['${ano}']['${mes}'][${i}].valor=parseFloat(this.value)||0;document.getElementById('total-inv').textContent=brl(sumArr(investimentos['${ano}']['${mes}']));renderInvResumo()">
      <button class="remove-btn" onclick="investimentos['${ano}']['${mes}'].splice(${i},1);loadInvestimentos()">×</button>
    </div>`).join('');
  document.getElementById('total-inv').textContent=brl(sumArr(investimentos[ano][mes])); renderInvResumo();
}
function addInvestimento(){ ensureInv(anoAtivo.inv,mesAtivo.inv); investimentos[anoAtivo.inv][mesAtivo.inv].push({data:'',nome:'',valor:0}); loadInvestimentos(); }
async function salvarInvestimentos(){
  const ano=anoAtivo.inv; const mes=mesAtivo.inv; const items=(investimentos[ano]||{})[mes]||[];
  try {
    await sb.from('investimentos').delete().eq('ano',ano).eq('mes',mes);
    const rows=items.filter(i=>i.nome||i.valor).map(i=>({ano,mes,data_invest:i.data||null,nome:i.nome||'',valor:+i.valor||0}));
    if(rows.length) await sb.from('investimentos').insert(rows);
    showToast('✓ Investimentos salvos!'); renderInvResumo();
  } catch(e) { showToast('❌ Erro!'); }
}
function renderInvResumo(){
  const agr={}; Object.values(investimentos).forEach(y=>Object.values(y).forEach(m=>m.forEach(i=>{ if(i.nome&&i.valor) { const k=i.nome.trim(); if(!agr[k]) agr[k]={n:k,t:0}; agr[k].t+=(+i.valor||0); }})));
  const items=Object.values(agr).sort((a,b)=>b.t-a.t); const gt=items.reduce((a,b)=>a+b.t,0); const PAL=['#2563eb','#16a34a','#7c3aed','#d97706','#0891b2','#dc2626','#8b5cf6'];
  document.getElementById('inv-resumo-body').innerHTML=items.length?items.map((i,idx)=>`<tr><td><span style="display:inline-block;width:10px;height:10px;background:${PAL[idx%PAL.length]};margin-right:8px"></span>${i.n}</td><td class="mono" style="color:var(--teal)">${brl(i.t)}</td><td class="mono">${gt?(i.t/gt*100).toFixed(1)+'%':'-'}</td></tr>`).join(''):'<tr><td colspan="3">Vazio</td></tr>';
  if(window._invChart) window._invChart.destroy();
  if(items.length) window._invChart=new Chart(document.getElementById('chartInvPie'),{type:'doughnut',data:{labels:items.map(i=>i.n),datasets:[{data:items.map(i=>i.t),backgroundColor:PAL}]},options:{responsive:true,maintainAspectRatio:false,cutout:'55%'}});
}

// ═══════════════════════════════════════════════════════
//  HISTÓRICO
// ═══════════════════════════════════════════════════════
function renderHistContent() {
  const m=getMesData(anoAtivo.hist,mesAtivo.hist); const d=m.raw;
  document.getElementById('hist-content').innerHTML=`
    <div class="cards-row"><div class="card green"><div class="card-label">Entradas</div><div class="card-value">${brl(m.ent)}</div></div><div class="card red"><div class="card-label">Saídas</div><div class="card-value">${brl(m.saida)}</div></div><div class="card ${m.saldo>=0?'green':'red'}"><div class="card-label">Saldo</div><div class="card-value">${brl(m.saldo)}</div></div></div>
    <div class="grid2">
      <div class="table-card"><div class="table-head"><h3>Entradas</h3></div><table><tbody>${(d.entradas||[]).map(e=>`<tr><td>${e.desc}</td><td class="mono pos">${brl(e.valor)}</td></tr>`).join('')}</tbody></table></div>
      <div class="table-card"><div class="table-head"><h3>Saídas</h3></div><table><tbody>${(d.saidas||[]).map(s=>`<tr><td>${s.desc}</td><td><span class="badge badge-blue">${(CAT_SAIDAS.find(c=>c.value===s.cat)||{label:s.cat}).label}</span></td><td class="mono neg">${brl(s.valor)}</td></tr>`).join('')}</tbody></table></div>
    </div>`;
}

// ═══════════════════════════════════════════════════════
//  GRÁFICOS
// ═══════════════════════════════════════════════════════
let gBar, gSaldo, gPie, gVs;
function buildGraficos() {
  const ano=anoAtivo.graf; const mes=mesAtivo.graf;
  const realKeys=Object.keys(dados[ano]||{}).sort((a,b)=>+a-+b).filter(k=>getMesData(ano,k).saida>0);
  const labels=realKeys.map(k=>getMesLabel(ano,k));
  const tc='#6b7280'; const gc='rgba(0,0,0,.04)';

  // 1. Bar Chart (Ano) Dinâmico
  const allCats = new Set(); realKeys.forEach(k => Object.keys(getMesData(ano,k).categorias).forEach(c=>allCats.add(c)));
  const dsBar = Array.from(allCats).map(cKey => ({
    label: (CAT_SAIDAS.find(c=>c.value===cKey)||{label:cKey}).label,
    data: realKeys.map(k => getMesData(ano,k).categorias[cKey]||0),
    backgroundColor: CAT_COLORS[cKey]||'#9ca3af', stack:'s'
  }));
  if(gBar) gBar.destroy();
  gBar = new Chart(document.getElementById('chartBarG'),{type:'bar',data:{labels,datasets:dsBar},options:{responsive:true,maintainAspectRatio:false,scales:{x:{stacked:true},y:{stacked:true}}}});

  // 2. Saldo (Ano)
  if(gSaldo) gSaldo.destroy();
  const saldos = realKeys.map(k=>getMesData(ano,k).saldo);
  gSaldo = new Chart(document.getElementById('chartSaldoG'),{type:'bar',data:{labels,datasets:[{label:'Saldo',data:saldos,backgroundColor:saldos.map(v=>v>=0?'rgba(22,163,74,.7)':'rgba(220,38,38,.7)')}]},options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}}}});

  // 3. Pizza (MÊS selecionado)
  const mM = getMesData(ano, mes);
  const mCats = Object.keys(mM.categorias).map(c=>({l:(CAT_SAIDAS.find(x=>x.value===c)||{label:c}).label, v:mM.categorias[c], col:CAT_COLORS[c]||'#9c'})).filter(x=>x.v>0);
  if(gPie) gPie.destroy();
  gPie = new Chart(document.getElementById('chartPieG'),{type:'doughnut',data:{labels:mCats.map(x=>x.l),datasets:[{data:mCats.map(x=>x.v),backgroundColor:mCats.map(x=>x.col)}]},options:{responsive:true,maintainAspectRatio:false}});

  // 4. Entradas vs Saídas (Ano)
  if(gVs) gVs.destroy();
  gVs = new Chart(document.getElementById('chartVsG'),{type:'bar',data:{labels,datasets:[{label:'Entradas',data:realKeys.map(k=>getMesData(ano,k).ent),backgroundColor:'rgba(22,163,74,.7)'},{label:'Saídas',data:realKeys.map(k=>getMesData(ano,k).saida),backgroundColor:'rgba(220,38,38,.7)'}]},options:{responsive:true,maintainAspectRatio:false}});
}

// ═══════════════════════════════════════════════════════
//  PARCELAS
// ═══════════════════════════════════════════════════════
function buildPeriodSelectorParc() {
  const cont = document.getElementById('ano-btns-parc');
  cont.innerHTML='';
  ANOS.forEach(a => {
    const btn = document.createElement('button');
    btn.className='period-btn'+(a===anoAtivo.parc?' active':''); btn.textContent=a;
    btn.onclick=()=>{ anoAtivo.parc=a; buildPeriodSelectorParc(); renderParcelas(); };
    cont.appendChild(btn);
  });
}
function preencherSelectsParcela() {
  document.getElementById('parc-mes').innerHTML = MESES_NOMES.map((m,i)=>`<option value="${i+1}">${m}</option>`).join('');
  document.getElementById('parc-ano').innerHTML = ANOS.map(a=>`<option value="${a}">${a}</option>`).join('');
}

async function adicionarParcela() {
  const desc = document.getElementById('parc-desc').value;
  const val = parseFloat(document.getElementById('parc-valor').value);
  const qtd = parseInt(document.getElementById('parc-qtd').value);
  const mIni = parseInt(document.getElementById('parc-mes').value);
  const aIni = parseInt(document.getElementById('parc-ano').value);

  if(!desc || !val || !qtd) return showToast('Preencha os campos corretamente!');
  const nova = { descricao: desc, valor_parcela: val, qtd: qtd, mes_inicio: mIni, ano_inicio: aIni };
  
  try {
    const {data, error} = await sb.from('parcelas').insert([nova]).select();
    if(error) throw error;
    if(data) parcelas.push(data[0]);
    showToast('✓ Parcela adicionada!');
    document.getElementById('parc-desc').value=''; document.getElementById('parc-valor').value=''; document.getElementById('parc-qtd').value='';
    renderParcelas();
  } catch(e) { console.error(e); showToast('❌ Erro ao adicionar parcela!'); }
}

async function deletarParcela(id) {
  try {
    await sb.from('parcelas').delete().eq('id', id);
    parcelas = parcelas.filter(p => p.id !== id);
    renderParcelas();
    showToast('Parcela removida');
  } catch(e) { console.error(e); }
}

function renderParcelas() {
  const ano = parseInt(anoAtivo.parc);
  document.getElementById('label-ano-parc').textContent = ano;
  
  // Cria cabeçalho (Meses do ano selecionado)
  document.getElementById('head-parcelas').innerHTML = `<th>Descrição</th>` + MESES_NOMES.map(m=>`<th>${m}/${String(ano).slice(2)}</th>`).join('') + `<th></th>`;
  
  let totais = Array(12).fill(0);
  let html = '';

  parcelas.forEach(p => {
    let tdHTML = '';
    let rowHasValue = false;
    
    const startAbs = p.ano_inicio * 12 + p.mes_inicio - 1;
    const endAbs = startAbs + p.qtd - 1;

    for(let m = 1; m <= 12; m++) {
      const currentAbs = ano * 12 + m - 1;
      if(currentAbs >= startAbs && currentAbs <= endAbs) {
        tdHTML += `<td class="mono">${brl(p.valor_parcela)} <span style="font-size:10px;color:var(--muted)">(${currentAbs-startAbs+1}/${p.qtd})</span></td>`;
        totais[m-1] += parseFloat(p.valor_parcela);
        rowHasValue = true;
      } else {
        tdHTML += `<td class="mono" style="color:var(--border)">—</td>`;
      }
    }
    
    // Mostra a parcela apenas se houver algum valor no ano selecionado
    if(rowHasValue) {
      html += `<tr><td>${p.descricao}</td>${tdHTML}<td><button class="remove-btn" onclick="deletarParcela('${p.id}')">×</button></td></tr>`;
    }
  });

  html += `<tr style="background:var(--bg3);font-weight:700"><td>Total de Parcelas (Mês)</td>` + totais.map(t=>`<td class="mono pos" style="color:var(--yellow)">${brl(t)}</td>`).join('') + `<td></td></tr>`;
  document.getElementById('body-parcelas').innerHTML = html;
}

// ═══════════════════════════════════════════════════════
//  INIT + CARREGAR SUPABASE
// ═══════════════════════════════════════════════════════
async function carregarDados() {
  try {
    const {data:lanc} = await sb.from('lancamentos').select('*');
    if(lanc) {
      dados = {};
      lanc.forEach(row => {
        if(!dados[row.ano]) dados[row.ano]={};
        if(!dados[row.ano][row.mes]) dados[row.ano][row.mes]={entradas:[],saidas:[]};
        if(row.tipo==='entrada') dados[row.ano][row.mes].entradas.push({desc:row.descricao,valor:row.valor});
        else dados[row.ano][row.mes].saidas.push({desc:row.descricao,cat:row.categoria,valor:row.valor});
      });
    }

    const {data:inv} = await sb.from('investimentos').select('*');
    if(inv) {
      investimentos = {};
      inv.forEach(row => {
        if(!investimentos[row.ano]) investimentos[row.ano]={};
        if(!investimentos[row.ano][row.mes]) investimentos[row.ano][row.mes]=[];
        investimentos[row.ano][row.mes].push({data:row.data_invest,nome:row.nome,valor:row.valor});
      });
    }

    // Carrega Parcelas (se a tabela não existir ainda ele não trava o resto, cai no catch silencioso)
    const {data:parc, error: e3} = await sb.from('parcelas').select('*');
    if(parc) parcelas = parc;

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
    showPage('dashboard'); // inicia navegando pro dash chamando todas funções
  }
}

carregarDados();
</script>
</body>
</html>

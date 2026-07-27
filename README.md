<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Controle Financeiro</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap');
:root{
  --bg:#e4e7ec;--bg2:#f2f4f7;--bg3:#e9ebf0;--nav:#dde0e8;
  --border:#c8cdd8;--text:#0d1117;--text2:#1a2232;--muted:#4b5563;
  --green:#15803d;--red:#b91c1c;--blue:#1e40af;--teal:#155e75;--yellow:#92400e;--purple:#5b21b6;
  --green-dim:rgba(21,128,61,.12);--red-dim:rgba(185,28,28,.12);--blue-dim:rgba(30,64,175,.12);
  --radius:12px;--mono:'JetBrains Mono',monospace;--shadow:0 1px 4px rgba(0,0,0,.08);
}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{background:var(--bg);color:var(--text);font-family:'Inter',sans-serif;font-size:14px;min-height:100vh}
.shell{display:flex;min-height:100vh}
/* NAV */
nav{width:224px;min-width:224px;background:var(--nav);border-right:1px solid var(--border);padding:24px 0;display:flex;flex-direction:column;position:sticky;top:0;height:100vh;overflow-y:auto;box-shadow:var(--shadow)}
.nav-logo{padding:0 20px 20px;font-size:15px;font-weight:700;border-bottom:1px solid var(--border);margin-bottom:12px}
.nav-logo span{color:var(--blue)}
.nav-item{display:flex;align-items:center;gap:10px;padding:10px 20px;cursor:pointer;border:none;background:none;color:var(--muted);font-size:13.5px;font-family:'Inter',sans-serif;width:100%;text-align:left;transition:all .15s}
.nav-item:hover{color:var(--text);background:var(--bg3)}
.nav-item.active{color:var(--blue);background:var(--blue-dim);font-weight:600}
.nav-item svg{flex-shrink:0}
/* MAIN */
main{flex:1;padding:32px;overflow-x:hidden}
.page{display:none}.page.active{display:block}
.page-header{margin-bottom:28px}
.page-header h1{font-size:22px;font-weight:700}
.page-header p{color:var(--muted);margin-top:4px;font-size:13px}
/* CARDS */
.cards-row{display:grid;grid-template-columns:repeat(auto-fit,minmax(170px,1fr));gap:16px;margin-bottom:24px}
.card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:20px;box-shadow:var(--shadow)}
.card-label{font-size:11px;font-weight:600;text-transform:uppercase;letter-spacing:.06em;color:var(--muted);margin-bottom:8px}
.card-value{font-size:21px;font-weight:700;font-family:var(--mono)}
.card-sub{font-size:11px;color:var(--muted);margin-top:4px}
.card.green .card-value{color:var(--green)}.card.red .card-value{color:var(--red)}
.card.blue .card-value{color:var(--blue)}.card.teal .card-value{color:var(--teal)}
.card.yellow .card-value{color:var(--yellow)}.card.purple .card-value{color:var(--purple)}
/* GRID */
.grid2{display:grid;grid-template-columns:1fr 1fr;gap:20px;margin-bottom:20px}
.grid2.wide{grid-template-columns:2fr 1fr}
@media(max-width:900px){.grid2,.grid2.wide{grid-template-columns:1fr}}
/* CHART */
.chart-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:22px;box-shadow:var(--shadow);margin-bottom:20px}
.chart-title{font-size:13px;font-weight:600;margin-bottom:16px;display:flex;align-items:center;justify-content:space-between}
/* TABLE */
.table-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);overflow:hidden;box-shadow:var(--shadow);margin-bottom:20px}
.table-head{padding:14px 20px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between}
.table-head h3{font-size:13px;font-weight:600}
table{width:100%;border-collapse:collapse}
th{font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.05em;color:#374151;padding:10px 16px;text-align:left;border-bottom:1px solid var(--border);background:#dfe2ea}
td{padding:10px 16px;border-bottom:1px solid var(--border);font-size:13px;color:var(--text2)}
td:first-child{color:var(--text);font-weight:500}
tr:last-child td{border-bottom:none}
tr:hover td{background:var(--bg3)}
.mono{font-family:var(--mono);font-size:12.5px}
.neg{color:var(--red)!important;font-weight:700}.pos{color:var(--green)!important;font-weight:700}
.badge{display:inline-block;padding:2px 9px;border-radius:999px;font-size:11px;font-weight:600}
.badge-blue{background:var(--blue-dim);color:var(--blue)}.badge-green{background:var(--green-dim);color:var(--green)}
.badge-red{background:var(--red-dim);color:var(--red)}.badge-teal{background:rgba(21,94,117,.12);color:var(--teal)}
/* PERIOD */
.period-selector{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:14px 18px;margin-bottom:20px;box-shadow:var(--shadow)}
.period-row{display:flex;align-items:center;gap:10px;flex-wrap:wrap}
.period-label{font-size:11px;font-weight:700;color:var(--muted);text-transform:uppercase;letter-spacing:.04em;min-width:32px}
.period-divider{width:100%;height:0;border-top:1px solid var(--border);margin:8px 0 4px}
.period-btn{padding:5px 13px;border-radius:7px;font-size:12.5px;cursor:pointer;border:1px solid var(--border);background:var(--bg3);color:var(--muted);font-family:'Inter',sans-serif;font-weight:500;transition:all .15s}
.period-btn:hover{color:var(--text);border-color:#b0b8cc}
.period-btn.active{background:var(--blue);border-color:var(--blue);color:#fff;font-weight:600}
.period-btn.todos{border-color:var(--blue);color:var(--blue);background:var(--blue-dim)}
.period-btn.todos.active{background:var(--blue);color:#fff}
/* FORM */
.form-section{margin-bottom:24px}
.form-section-title{font-size:11px;font-weight:700;text-transform:uppercase;letter-spacing:.07em;color:var(--muted);margin-bottom:12px;padding-bottom:8px;border-bottom:2px solid var(--border)}
.field{display:flex;flex-direction:column;gap:5px}
.field label{font-size:12px;font-weight:600;color:var(--text2)}
.field input,.field select{background:var(--bg3);border:1px solid var(--border);color:var(--text);border-radius:8px;padding:9px 12px;font-size:13px;font-family:'Inter',sans-serif;outline:none;transition:border-color .15s}
.field input:focus,.field select:focus{border-color:var(--blue);background:#fff}
.field input::placeholder{color:#adb5bd}
/* BTN */
.btn{display:inline-flex;align-items:center;gap:7px;padding:9px 18px;border-radius:8px;font-size:13px;font-weight:600;font-family:'Inter',sans-serif;cursor:pointer;border:none;transition:opacity .15s,transform .1s}
.btn:hover{opacity:.88}.btn:active{transform:scale(.98)}
.btn-primary{background:var(--blue);color:#fff}.btn-ghost{background:var(--bg3);color:var(--text2);border:1px solid var(--border)}
.btn-teal{background:var(--teal);color:#fff}.btn-red{background:var(--red);color:#fff}
/* LANCAMENTO ROWS */
.lancamento-item{display:grid;gap:8px;align-items:center;margin-bottom:7px}
.lancamento-item.e-row{grid-template-columns:2fr 1fr 28px}
.lancamento-item.s-row{grid-template-columns:2fr 1.5fr 1fr 28px}
.lancamento-item.i-row{grid-template-columns:1.5fr 1fr 28px}
.lancamento-item input,.lancamento-item select{background:var(--bg3);border:1px solid var(--border);color:var(--text);border-radius:8px;padding:8px 11px;font-size:13px;font-family:'Inter',sans-serif;outline:none;transition:border-color .15s}
.lancamento-item input:focus,.lancamento-item select:focus{border-color:var(--blue);background:#fff}
.lancamento-item input::placeholder{color:#adb5bd}
.remove-btn{background:none;border:1px solid transparent;color:var(--muted);cursor:pointer;font-size:18px;line-height:1;padding:4px 7px;border-radius:6px}
.remove-btn:hover{color:var(--red);background:var(--red-dim);border-color:var(--red-dim)}
.col-hdrs{display:grid;gap:8px;padding:0 0 5px 2px;margin-bottom:2px}
.col-hdrs.e-row{grid-template-columns:2fr 1fr 28px}
.col-hdrs.s-row{grid-template-columns:2fr 1.5fr 1fr 28px}
.col-hdrs.i-row{grid-template-columns:1.5fr 1fr 28px}
.col-hdrs span{font-size:11px;font-weight:600;color:var(--muted);text-transform:uppercase;letter-spacing:.04em}
.total-display{background:var(--bg3);border:1px solid var(--border);border-radius:8px;padding:11px 16px;display:flex;justify-content:space-between;align-items:center;margin-top:10px}
.total-display span{font-size:11px;color:var(--muted);font-weight:600;text-transform:uppercase;letter-spacing:.05em}
.total-display strong{font-family:var(--mono);font-size:16px}
/* SALDO BAR */
.saldo-bar{display:flex;align-items:center;justify-content:space-between;background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:16px 22px;margin-bottom:22px;box-shadow:var(--shadow)}
.saldo-bar-label{font-size:12px;color:var(--muted);font-weight:500}
.saldo-bar-value{font-size:26px;font-weight:700;font-family:var(--mono)}
/* PARCELAS */
.divida-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius);padding:18px 20px;margin-bottom:12px;box-shadow:var(--shadow)}
.divida-header{display:flex;align-items:center;justify-content:space-between;margin-bottom:12px}
.divida-nome{font-size:15px;font-weight:700}
.divida-info{font-size:12px;color:var(--muted);margin-top:2px}
.parc-row{display:flex;align-items:center;gap:10px;padding:7px 0;border-bottom:1px solid var(--border)}
.parc-row:last-child{border-bottom:none}
.parc-check{width:16px;height:16px;cursor:pointer;accent-color:var(--green)}
.parc-mes{font-size:12.5px;color:var(--text2);min-width:80px}
.parc-val{font-family:var(--mono);font-size:13px;font-weight:600;min-width:110px}
.parc-val.pago{text-decoration:line-through;color:var(--muted)}
.parc-badge{font-size:11px;padding:2px 8px;border-radius:999px}
.parc-badge.aberta{background:var(--red-dim);color:var(--red)}
.parc-badge.paga{background:var(--green-dim);color:var(--green)}
.parc-total-box{background:var(--bg3);border:1px solid var(--border);border-radius:8px;padding:12px 16px;margin-top:10px;display:flex;justify-content:space-between;align-items:center}
/* TOAST */
#toast{position:fixed;bottom:24px;right:24px;background:var(--green);color:#fff;padding:11px 18px;border-radius:10px;font-size:13px;font-weight:600;opacity:0;transform:translateY(8px);transition:all .25s;pointer-events:none;z-index:9999;box-shadow:0 4px 16px rgba(0,0,0,.15)}
#toast.show{opacity:1;transform:translateY(0)}
/* LOADING */
#loading-overlay{position:fixed;inset:0;background:rgba(228,231,236,.9);backdrop-filter:blur(4px);display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:99999;gap:14px}
.spinner{width:38px;height:38px;border:3px solid var(--border);border-top-color:var(--blue);border-radius:50%;animation:spin .8s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}
#loading-overlay p{color:var(--muted);font-size:13px;font-weight:500}
.sync-badge{display:inline-flex;align-items:center;gap:5px;font-size:11px;font-weight:600;padding:3px 9px;border-radius:999px;background:rgba(21,94,117,.12);color:var(--teal);margin-top:6px}
.sync-dot{width:6px;height:6px;border-radius:50%;background:var(--teal);animation:pulse 1.5s infinite}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.3}}
::-webkit-scrollbar{width:5px;height:5px}::-webkit-scrollbar-thumb{background:var(--border);border-radius:4px}
</style>
</head>
<body>
<div id="loading-overlay"><div class="spinner"></div><p>Carregando dados...</p></div>
<div class="shell">
<nav>
  <div class="nav-logo">💰 Financeiro<span>.</span><div class="sync-badge" id="sync-badge"><span class="sync-dot"></span>Supabase</div></div>
  <button class="nav-item active" onclick="showPage('dashboard')"><svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/></svg>Dashboard</button>
  <button class="nav-item" onclick="showPage('lancamento')"><svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 5v14M5 12h14"/></svg>Lançar Mês</button>
  <button class="nav-item" onclick="showPage('investimentos')"><svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/><polyline points="17 6 23 6 23 12"/></svg>Investimentos</button>
  <button class="nav-item" onclick="showPage('historico')"><svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M3 12h18M3 6h18M3 18h18"/></svg>Histórico</button>
  <button class="nav-item" onclick="showPage('graficos')"><svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M18 20V10M12 20V4M6 20v-6"/></svg>Gráficos</button>
  <button class="nav-item" onclick="showPage('parcelas')"><svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="4" width="18" height="18" rx="2"/><path d="M16 2v4M8 2v4M3 10h18"/></svg>Parcelas / Dívidas</button>
</nav>
<main>

<!-- DASHBOARD -->
<div class="page active" id="page-dashboard">
  <div class="page-header"><h1>Dashboard</h1><p id="dash-subtitle">Visão geral — últimos 3 meses</p></div>
  <div class="cards-row" id="dash-cards"></div>
  <div class="grid2 wide">
    <div class="chart-card" style="margin-bottom:0">
      <div class="chart-title">Gastos por categoria</div>
      <div style="height:240px"><canvas id="chartBarDash"></canvas></div>
    </div>
    <div class="chart-card" style="margin-bottom:0">
      <div class="chart-title">Distribuição</div>
      <div style="height:240px"><canvas id="chartPieDash"></canvas></div>
    </div>
  </div>
  <div style="margin-bottom:20px"></div>
  <div class="chart-card">
    <div class="chart-title">Entradas vs Saídas</div>
    <div style="height:200px"><canvas id="chartLineDash"></canvas></div>
  </div>
  <div class="table-card">
    <div class="table-head"><h3>Resumo dos meses</h3></div>
    <table><thead><tr><th>Mês</th><th>Entradas</th><th>Saídas</th><th>Invest.</th><th>Saldo</th></tr></thead>
    <tbody id="dash-table-body"></tbody></table>
  </div>
</div>

<!-- LANÇAMENTO -->
<div class="page" id="page-lancamento">
  <div class="page-header"><h1>Lançar Mês</h1><p>Registre entradas e saídas</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-lanc" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-lanc" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div class="form-section">
    <div class="form-section-title">Entradas</div>
    <div class="col-hdrs e-row"><span>Descrição</span><span>Valor (R$)</span><span></span></div>
    <div id="entradas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:6px 13px" onclick="addItem('entradas')">+ Adicionar entrada</button>
    <div class="total-display" style="border-left:3px solid var(--green);margin-top:10px">
      <span>Total Entradas</span><strong class="pos" id="total-entradas">R$ 0,00</strong>
    </div>
  </div>
  <div class="form-section">
    <div class="form-section-title">Saídas</div>
    <div class="col-hdrs s-row"><span>Descrição</span><span>Categoria</span><span>Valor (R$)</span><span></span></div>
    <div id="saidas-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:6px 13px" onclick="addItem('saidas')">+ Adicionar saída</button>
    <div class="total-display" style="border-left:3px solid var(--red);margin-top:10px">
      <span>Total Saídas</span><strong class="neg" id="total-saidas">R$ 0,00</strong>
    </div>
  </div>
  <div class="saldo-bar">
    <div><div class="saldo-bar-label">Saldo do mês</div><div class="saldo-bar-value" id="saldo-mes">R$ 0,00</div></div>
    <button class="btn btn-primary" onclick="salvarMes()">✓ Salvar mês</button>
  </div>
</div>

<!-- INVESTIMENTOS -->
<div class="page" id="page-investimentos">
  <div class="page-header"><h1>Investimentos</h1><p>Registre e acompanhe seus investimentos</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-inv" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-inv" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div class="form-section">
    <div class="form-section-title">Lançar investimento</div>
    <div class="col-hdrs i-row"><span>Nome do investimento</span><span>Valor (R$)</span><span></span></div>
    <div id="investimentos-list"></div>
    <button class="btn btn-ghost" style="margin-top:8px;font-size:12px;padding:6px 13px" onclick="addInvestimento()">+ Adicionar</button>
    <div class="total-display" style="border-left:3px solid var(--teal);margin-top:10px">
      <span>Total Investido no Mês</span><strong style="color:var(--teal);font-family:var(--mono);font-size:16px" id="total-inv">R$ 0,00</strong>
    </div>
  </div>
  <div style="display:flex;gap:8px;margin-bottom:20px">
    <button class="btn btn-teal" onclick="salvarInvestimentos()">✓ Salvar investimentos</button>
  </div>
  <div class="cards-row" style="grid-template-columns:repeat(auto-fit,minmax(200px,1fr))">
    <div class="card teal"><div class="card-label">Total acumulado (todos)</div><div class="card-value" id="inv-total-geral">R$ 0,00</div></div>
    <div class="card blue"><div class="card-label">Tipos de investimento</div><div class="card-value" id="inv-tipos">0</div></div>
    <div class="card green"><div class="card-label">Maior investimento</div><div class="card-value" id="inv-maior">—</div><div class="card-sub" id="inv-maior-sub"></div></div>
  </div>
  <div class="grid2">
    <div class="table-card" style="margin-bottom:0">
      <div class="table-head"><h3>Por investimento (acumulado)</h3></div>
      <table><thead><tr><th>Investimento</th><th>Total</th><th>%</th></tr></thead><tbody id="inv-resumo-body"></tbody></table>
    </div>
    <div class="chart-card" style="margin-bottom:0">
      <div class="chart-title">Distribuição por investimento</div>
      <div style="height:260px"><canvas id="chartInvPie"></canvas></div>
    </div>
  </div>
</div>

<!-- HISTÓRICO -->
<div class="page" id="page-historico">
  <div class="page-header"><h1>Histórico</h1><p>Consulte qualquer mês lançado</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-hist" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row"><span class="period-label">Mês</span><div id="mes-btns-hist" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
  </div>
  <div id="hist-content"></div>
</div>

<!-- GRÁFICOS -->
<div class="page" id="page-graficos">
  <div class="page-header"><h1>Gráficos</h1><p>Análise visual dos dados</p></div>
  <div class="period-selector">
    <div class="period-row"><span class="period-label">Ano</span><div id="ano-btns-graf" style="display:flex;gap:6px;flex-wrap:wrap"></div></div>
    <div class="period-divider"></div>
    <div class="period-row">
      <span class="period-label">Mês</span>
      <div id="mes-btns-graf" style="display:flex;gap:6px;flex-wrap:wrap"></div>
      <button class="period-btn todos active" id="graf-todos-btn" onclick="grafSetTodos()" style="margin-left:8px">Todos</button>
    </div>
  </div>
  <div class="grid2">
    <div class="chart-card" style="margin-bottom:0"><div class="chart-title">Por categoria</div><div style="height:260px"><canvas id="chartBarG"></canvas></div></div>
    <div class="chart-card" style="margin-bottom:0"><div class="chart-title">Saldo mensal</div><div style="height:260px"><canvas id="chartSaldoG"></canvas></div></div>
  </div>
  <div style="margin-bottom:20px"></div>
  <div class="grid2">
    <div class="chart-card" style="margin-bottom:0"><div class="chart-title">Distribuição (%)</div><div style="height:260px"><canvas id="chartPieG"></canvas></div></div>
    <div class="chart-card" style="margin-bottom:0"><div class="chart-title">Entradas vs Saídas</div><div style="height:260px"><canvas id="chartVsG"></canvas></div></div>
  </div>
</div>

<!-- PARCELAS -->
<div class="page" id="page-parcelas">
  <div class="page-header"><h1>Parcelas / Dívidas</h1><p>Cadastre dívidas parceladas e acompanhe os pagamentos</p></div>
  <!-- Formulário nova dívida -->
  <div class="chart-card">
    <div class="chart-title">Adicionar nova dívida parcelada</div>
    <div style="display:grid;grid-template-columns:2fr 1fr 1fr 1fr 1fr;gap:12px;align-items:end">
      <div class="field"><label>Nome da dívida</label><input type="text" id="parc-desc" placeholder="Ex: Notebook, Curso..."></div>
      <div class="field"><label>Valor total (R$)</label><input type="number" id="parc-valor" placeholder="0,00" min="0" step="0.01" oninput="parcPreview()"></div>
      <div class="field"><label>Nº de parcelas</label><input type="number" id="parc-qtd" placeholder="Ex: 12" min="1" max="60" oninput="parcPreview()"></div>
      <div class="field"><label>Mês início</label>
        <select id="parc-mes-inicio">
          <option value="1">Jan</option><option value="2">Fev</option><option value="3">Mar</option><option value="4">Abr</option><option value="5">Mai</option><option value="6" selected>Jun</option><option value="7">Jul</option><option value="8">Ago</option><option value="9">Set</option><option value="10">Out</option><option value="11">Nov</option><option value="12">Dez</option>
        </select>
      </div>
      <div class="field"><label>Ano início</label>
        <select id="parc-ano-inicio"><option value="2025">2025</option><option value="2026" selected>2026</option><option value="2027">2027</option><option value="2028">2028</option></select>
      </div>
    </div>
    <div style="margin-top:12px;display:flex;gap:10px;align-items:center">
      <button class="btn btn-primary" onclick="adicionarDivida()">+ Adicionar dívida</button>
      <span id="parc-preview" style="font-size:12px;color:var(--muted)"></span>
    </div>
  </div>
  <!-- Total geral -->
  <div class="cards-row" style="grid-template-columns:repeat(auto-fit,minmax(180px,1fr))">
    <div class="card red"><div class="card-label">Total em dívidas (em aberto)</div><div class="card-value" id="parc-total-aberto">R$ 0,00</div></div>
    <div class="card green"><div class="card-label">Total já pago</div><div class="card-value" id="parc-total-pago">R$ 0,00</div></div>
    <div class="card blue"><div class="card-label">Dívidas cadastradas</div><div class="card-value" id="parc-qtd-dividas">0</div></div>
  </div>
  <!-- Lista de dívidas -->
  <div id="dividas-lista"></div>
  <!-- Gráfico -->
  <div class="chart-card">
    <div class="chart-title">Parcelas por mês (próximos meses)</div>
    <div style="height:200px"><canvas id="chartParcelasG"></canvas></div>
  </div>
</div>

</main>
</div>
<div id="toast"></div>
<script>
// ═══════════════════════════════════
// SUPABASE
// ═══════════════════════════════════
const SUPABASE_URL='https://itsxvvhqhilsvauqqeax.supabase.co';
const SUPABASE_KEY='sb_publishable_Wq_LI_05HqS7Sg26wxYkQA_hZam-MBO';
const sb=supabase.createClient(SUPABASE_URL,SUPABASE_KEY);

// ═══════════════════════════════════
// CONSTANTES
// ═══════════════════════════════════
const MESES_NOMES=['Jan','Fev','Mar','Abr','Mai','Jun','Jul','Ago','Set','Out','Nov','Dez'];
const ANOS=['2024','2025','2026','2027'];

const CAT_SAIDAS=[
  {value:'moradia',     label:'Aluguel + Água + Luz'},
  {value:'alimentacao', label:'Alimentação / Mercado'},
  {value:'delivery',    label:'Delivery / iFood'},
  {value:'transporte',  label:'Transporte / Uber / 99'},
  {value:'celular',     label:'Celular / Internet'},
  {value:'saude',       label:'Saúde / Farmácia'},
  {value:'educacao',    label:'Educação / Cursos'},
  {value:'lazer',       label:'Lazer / Entretenimento'},
  {value:'vestuario',   label:'Roupas / Vestuário / Presentes'},
  {value:'impostos',    label:'Impostos / Contador'},
  {value:'assinaturas', label:'Assinaturas / Streaming'},
  {value:'viagem',      label:'Viagem / Hospedagem'},
  {value:'investimentos',label:'Investimentos'},
  {value:'outros',      label:'Outros'},
];

const CAT_COLORS={
  moradia:'#16a34a',alimentacao:'#d97706',delivery:'#ea580c',transporte:'#7c3aed',
  celular:'#0891b2',saude:'#db2777',educacao:'#059669',lazer:'#8b5cf6',
  vestuario:'#f59e0b',impostos:'#374151',assinaturas:'#6366f1',viagem:'#0d9488',
  investimentos:'#155e75',outros:'#9ca3af'
};

const INV_COLORS=['#1e40af','#15803d','#b45309','#7c3aed','#0e7490','#be185d','#065f46','#92400e','#1d4ed8','#166534'];

// ═══════════════════════════════════
// ESTADO
// ═══════════════════════════════════
let dados={};        // dados[ano][mes] = {entradas:[],saidas:[]}
let investimentos={};// investimentos[ano][mes] = [{nome,valor}]
let dividasLista=[];

// período ativo por seção
let periodoAtivo={
  lanc:{ano:'2026',mes:'6'},
  inv: {ano:'2026',mes:'6'},
  hist:{ano:'2026',mes:'6'},
  graf:{ano:'2026',mes:null},
};

// hoje
const hoje=new Date();
const anoHoje=String(hoje.getFullYear());
const mesHoje=String(hoje.getMonth()+1);

// ═══════════════════════════════════
// HELPERS
// ═══════════════════════════════════
const brl=v=>'R$ '+(+v||0).toLocaleString('pt-BR',{minimumFractionDigits:2,maximumFractionDigits:2});
const sumArr=arr=>arr.reduce((a,b)=>a+(+b.valor||0),0);

function getMesData(ano,mes){
  const d=(dados[ano]||{})[mes]||{entradas:[],saidas:[]};
  const ent=sumArr(d.entradas);
  const catTots={};
  d.saidas.forEach(s=>{if(!catTots[s.cat])catTots[s.cat]=0;catTots[s.cat]+=(+s.valor||0);});
  const saida=Object.values(catTots).reduce((a,b)=>a+b,0);
  const inv=sumArr(((investimentos[ano]||{})[mes])||[]);
  return{ent,saida,inv,saldo:ent-saida-inv,catTots};
}

function getMesLabel(ano,mes){return `${MESES_NOMES[+mes-1]}/${String(ano).slice(2)}`;}

function getAnosMesComDados(){
  const anos=new Set([...Object.keys(dados),...Object.keys(investimentos)]);
  ANOS.forEach(a=>anos.add(a));
  return [...anos].sort();
}

function getMesesDoAno(ano){
  const meses=new Set();
  [1,2,3,4,5,6,7,8,9,10,11,12].forEach(m=>meses.add(String(m)));
  return [...meses].sort((a,b)=>+a-+b);
}

// ═══════════════════════════════════
// NAVEGAÇÃO
// ═══════════════════════════════════
function showPage(id){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  document.querySelector(`[onclick="showPage('${id}')"]`).classList.add('active');
  if(id==='dashboard')    buildDashboard();
  if(id==='lancamento')   {buildPeriodSel('lanc');loadMesForm();}
  if(id==='investimentos'){buildPeriodSel('inv');loadInvForm();}
  if(id==='historico')    {buildPeriodSel('hist');renderHistorico();}
  if(id==='graficos')     {buildPeriodSel('graf');buildGraficos();}
  if(id==='parcelas')     renderParcelas();
}

// ═══════════════════════════════════
// PERIOD SELECTOR
// ═══════════════════════════════════
function buildPeriodSel(ctx){
  const anosCont=document.getElementById(`ano-btns-${ctx}`);
  const mesCont=document.getElementById(`mes-btns-${ctx}`);
  if(!anosCont||!mesCont) return;

  const anos=getAnosMesComDados();
  anosCont.innerHTML='';
  anos.forEach(ano=>{
    const btn=document.createElement('button');
    btn.className='period-btn'+(periodoAtivo[ctx].ano===ano?' active':'');
    btn.textContent=ano;
    btn.onclick=()=>{
      periodoAtivo[ctx].ano=ano;
      anosCont.querySelectorAll('.period-btn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      buildMesBtns(ctx);
      onPeriodChange(ctx);
    };
    anosCont.appendChild(btn);
  });
  buildMesBtns(ctx);
}

function buildMesBtns(ctx){
  const mesCont=document.getElementById(`mes-btns-${ctx}`);
  if(!mesCont) return;
  const meses=getMesesDoAno(periodoAtivo[ctx].ano);
  mesCont.innerHTML='';
  meses.forEach(mes=>{
    const btn=document.createElement('button');
    const isActive=periodoAtivo[ctx].mes===mes;
    btn.className='period-btn'+(isActive?' active':'');
    btn.textContent=MESES_NOMES[+mes-1];
    btn.onclick=()=>{
      periodoAtivo[ctx].mes=mes;
      mesCont.querySelectorAll('.period-btn').forEach(b=>b.classList.remove('active'));
      btn.classList.add('active');
      if(ctx==='graf'){
        const tb=document.getElementById('graf-todos-btn');
        if(tb) tb.classList.remove('active');
      }
      onPeriodChange(ctx);
    };
    mesCont.appendChild(btn);
  });
}

function onPeriodChange(ctx){
  if(ctx==='lanc')  loadMesForm();
  if(ctx==='inv')   loadInvForm();
  if(ctx==='hist')  renderHistorico();
  if(ctx==='graf')  {buildGraficos();}
}

// ═══════════════════════════════════
// DASHBOARD — últimos 3 meses + invest
// ═══════════════════════════════════
function buildDashboard(){
  // descobrir mês atual e pegar os 3 últimos com dados
  const allPeriodos=[];
  getAnosMesComDados().forEach(ano=>{
    getMesesDoAno(ano).forEach(mes=>{
      const m=getMesData(ano,mes);
      if(m.ent>0||m.saida>0||m.inv>0) allPeriodos.push({ano,mes});
    });
  });
  // ordenar por ano+mes
  allPeriodos.sort((a,b)=>(+a.ano*100+ +a.mes)-(+b.ano*100+ +b.mes));

  // pegar os últimos 3
  const recentes=allPeriodos.slice(-3);
  if(!recentes.length){document.getElementById('dash-cards').innerHTML='<p style="color:var(--muted);padding:8px">Nenhum dado lançado ainda.</p>';return;}

  const labels=recentes.map(p=>getMesLabel(p.ano,p.mes));
  document.getElementById('dash-subtitle').textContent=`Últimos ${recentes.length} meses com dados`;

  let totEnt=0,totSai=0,totInv=0,totSaldo=0;
  const catTotaisGeral={};
  recentes.forEach(p=>{
    const m=getMesData(p.ano,p.mes);
    totEnt+=m.ent;totSai+=m.saida;totInv+=m.inv;totSaldo+=m.saldo;
    Object.entries(m.catTots).forEach(([k,v])=>{if(!catTotaisGeral[k])catTotaisGeral[k]=0;catTotaisGeral[k]+=v;});
  });

  // Total geral investimentos (todos os períodos)
  let totInvGeral=0;
  Object.values(investimentos).forEach(ano=>Object.values(ano).forEach(mes=>mes.forEach(i=>totInvGeral+=(+i.valor||0))));

  const cards=[
    {label:'Total de saídas',value:brl(totSai),cls:'red',sub:`${recentes.length} meses`},
    {label:'Total entradas',value:brl(totEnt),cls:'green',sub:'Receitas'},
    {label:'Saldo do período',value:brl(totSaldo),cls:totSaldo>=0?'green':'red',sub:'Entradas − Saídas − Inv.'},
    {label:'Total investido',value:brl(totInvGeral),cls:'teal',sub:'Acumulado geral'},
  ];
  document.getElementById('dash-cards').innerHTML=cards.map(c=>`
    <div class="card ${c.cls}"><div class="card-label">${c.label}</div><div class="card-value">${c.value}</div><div class="card-sub">${c.sub}</div></div>`).join('');

  const gc='rgba(0,0,0,.04)';const tc='#4b5563';

  // Tabela
  document.getElementById('dash-table-body').innerHTML=recentes.map(p=>{
    const m=getMesData(p.ano,p.mes);
    return `<tr>
      <td>${getMesLabel(p.ano,p.mes)}</td>
      <td class="mono pos">${m.ent>0?brl(m.ent):'—'}</td>
      <td class="mono neg">${brl(m.saida)}</td>
      <td class="mono" style="color:var(--teal)">${m.inv>0?brl(m.inv):'—'}</td>
      <td class="mono ${m.saldo>=0?'pos':'neg'}">${brl(m.saldo)}</td>
    </tr>`;
  }).join('');

  // Barras por categoria
  const catKeys=Object.keys(catTotaisGeral).filter(k=>catTotaisGeral[k]>0);
  const datasets=CAT_SAIDAS.filter(cat=>catKeys.includes(cat.value)).map(cat=>({
    label:cat.label,
    data:recentes.map(p=>{const d=(dados[p.ano]||{})[p.mes]||{saidas:[]};return d.saidas.filter(s=>s.cat===cat.value).reduce((a,s)=>a+(+s.valor||0),0);}),
    backgroundColor:CAT_COLORS[cat.value]||'#9ca3af',borderRadius:3,stack:'s'
  }));
  if(window._dBar){try{window._dBar.destroy();}catch(e){}}
  window._dBar=new Chart(document.getElementById('chartBarDash'),{type:'bar',
    data:{labels,datasets},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:10},boxWidth:10,padding:8}}},
      scales:{x:{stacked:true,ticks:{color:tc},grid:{color:gc}},y:{stacked:true,ticks:{color:tc,callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:gc}}}}
  });

  // Pizza
  if(window._dPie){try{window._dPie.destroy();}catch(e){}}
  window._dPie=new Chart(document.getElementById('chartPieDash'),{type:'doughnut',
    data:{labels:catKeys.map(k=>(CAT_SAIDAS.find(c=>c.value===k)||{label:k}).label),
      datasets:[{data:catKeys.map(k=>catTotaisGeral[k]),backgroundColor:catKeys.map(k=>CAT_COLORS[k]||'#9ca3af'),borderWidth:2,borderColor:'#fff'}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'60%',
      plugins:{legend:{position:'bottom',labels:{color:tc,font:{size:10},padding:10,boxWidth:10}},
        tooltip:{callbacks:{label:ctx=>{const t=Object.values(catTotaisGeral).reduce((a,b)=>a+b,0);return ` ${brl(ctx.raw)} (${(ctx.raw/t*100).toFixed(1)}%)`;}}}}
    }
  });

  // Linha entradas vs saídas
  if(window._dLine){try{window._dLine.destroy();}catch(e){}}
  window._dLine=new Chart(document.getElementById('chartLineDash'),{type:'bar',
    data:{labels,datasets:[
      {type:'bar',label:'Entradas',data:recentes.map(p=>getMesData(p.ano,p.mes).ent),backgroundColor:'rgba(22,163,74,.65)',borderRadius:4},
      {type:'line',label:'Saídas',data:recentes.map(p=>getMesData(p.ano,p.mes).saida),borderColor:'#dc2626',backgroundColor:'rgba(220,38,38,.08)',tension:.35,fill:true,pointRadius:4,pointBackgroundColor:'#dc2626'},
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:12}}}},
      scales:{x:{ticks:{color:tc},grid:{color:gc}},y:{ticks:{color:tc,callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:gc}}}}
  });
}

// ═══════════════════════════════════
// LANÇAMENTO
// ═══════════════════════════════════
function ensureMes(ano,mes){
  if(!dados[ano]) dados[ano]={};
  if(!dados[ano][mes]) dados[ano][mes]={entradas:[],saidas:[]};
}

function loadMesForm(){
  const{ano,mes}=periodoAtivo.lanc;
  ensureMes(ano,mes);
  renderItems('entradas',dados[ano][mes].entradas);
  renderItems('saidas',dados[ano][mes].saidas);
  recalcTotais();
}

function catSelectHtml(selected='outros'){
  return `<select onchange="updateItemField(this,'cat')">${CAT_SAIDAS.map(c=>`<option value="${c.value}"${c.value===selected?' selected':''}>${c.label}</option>`).join('')}</select>`;
}

function renderItems(tipo,items){
  const list=document.getElementById(tipo+'-list');
  list.innerHTML=items.map((item,i)=>`
    <div class="lancamento-item ${tipo==='entradas'?'e-row':'s-row'}" data-tipo="${tipo}" data-idx="${i}">
      <input type="text" placeholder="${tipo==='entradas'?'Ex: Salário':'Ex: Conta de luz'}" value="${item.desc||''}" oninput="updateItemField(this,'desc')">
      ${tipo==='saidas'?catSelectHtml(item.cat||'outros'):''}
      <input type="number" placeholder="0,00" value="${item.valor||''}" step="0.01" oninput="updateItemField(this,'valor')">
      <button class="remove-btn" onclick="removeItem('${tipo}',${i})">×</button>
    </div>`).join('');
}

function updateItemField(el,field){
  const row=el.closest('.lancamento-item');
  const tipo=row.dataset.tipo; const idx=+row.dataset.idx;
  const{ano,mes}=periodoAtivo.lanc;
  dados[ano][mes][tipo][idx][field]=(field==='valor'?parseFloat(el.value)||0:el.value);
  recalcTotais();
}

function addItem(tipo){
  const{ano,mes}=periodoAtivo.lanc;
  ensureMes(ano,mes);
  const item=tipo==='entradas'?{desc:'',valor:0}:{desc:'',cat:'outros',valor:0};
  dados[ano][mes][tipo].push(item);
  renderItems(tipo,dados[ano][mes][tipo]);
  recalcTotais();
}

function removeItem(tipo,idx){
  const{ano,mes}=periodoAtivo.lanc;
  dados[ano][mes][tipo].splice(idx,1);
  renderItems(tipo,dados[ano][mes][tipo]);
  recalcTotais();
}

function recalcTotais(){
  const{ano,mes}=periodoAtivo.lanc;
  const d=dados[ano][mes]||{entradas:[],saidas:[]};
  const ent=sumArr(d.entradas);
  const sai=sumArr(d.saidas);
  const saldo=ent-sai;
  document.getElementById('total-entradas').textContent=brl(ent);
  document.getElementById('total-saidas').textContent=brl(sai);
  const el=document.getElementById('saldo-mes');
  el.textContent=brl(saldo);
  el.style.color=saldo>=0?'var(--green)':'var(--red)';
}

async function salvarMes(){
  const{ano,mes}=periodoAtivo.lanc;
  const d=dados[ano][mes]||{entradas:[],saidas:[]};
  const btn=document.querySelector('[onclick="salvarMes()"]');
  btn.disabled=true;btn.textContent='Salvando...';
  try{
    await sb.from('lancamentos').delete().eq('ano',ano).eq('mes',mes);
    const rows=[
      ...d.entradas.filter(e=>e.desc||e.valor).map(e=>({ano,mes,tipo:'entrada',descricao:e.desc,categoria:null,valor:+e.valor||0})),
      ...d.saidas.filter(s=>s.desc||s.valor).map(s=>({ano,mes,tipo:'saida',descricao:s.desc,categoria:s.cat,valor:+s.valor||0}))
    ];
    if(rows.length){const{error}=await sb.from('lancamentos').insert(rows);if(error)throw error;}
    showToast('✓ Mês salvo no Supabase!');
  }catch(e){showToast('❌ Erro: '+e.message);}
  finally{btn.disabled=false;btn.textContent='✓ Salvar mês';}
}

// ═══════════════════════════════════
// INVESTIMENTOS
// ═══════════════════════════════════
function ensureInv(ano,mes){
  if(!investimentos[ano]) investimentos[ano]={};
  if(!investimentos[ano][mes]) investimentos[ano][mes]=[];
}

function loadInvForm(){
  const{ano,mes}=periodoAtivo.inv;
  ensureInv(ano,mes);
  renderInvItems();
  recalcInv();
  renderInvResumo();
}

function renderInvItems(){
  const{ano,mes}=periodoAtivo.inv;
  const items=investimentos[ano][mes]||[];
  document.getElementById('investimentos-list').innerHTML=items.map((item,i)=>`
    <div class="lancamento-item i-row" data-idx="${i}">
      <input type="text" placeholder="Ex: Tesouro Direto, Ações..." value="${item.nome||''}" oninput="updateInvField(this,'nome')">
      <input type="number" placeholder="0,00" value="${item.valor||''}" step="0.01" oninput="updateInvField(this,'valor')">
      <button class="remove-btn" onclick="removeInv(${i})">×</button>
    </div>`).join('');
}

function updateInvField(el,field){
  const{ano,mes}=periodoAtivo.inv;
  const idx=+el.closest('.lancamento-item').dataset.idx;
  investimentos[ano][mes][idx][field]=(field==='valor'?parseFloat(el.value)||0:el.value);
  recalcInv();
}

function addInvestimento(){
  const{ano,mes}=periodoAtivo.inv;
  ensureInv(ano,mes);
  investimentos[ano][mes].push({nome:'',valor:0});
  renderInvItems();recalcInv();
}

function removeInv(idx){
  const{ano,mes}=periodoAtivo.inv;
  investimentos[ano][mes].splice(idx,1);
  renderInvItems();recalcInv();renderInvResumo();
}

function recalcInv(){
  const{ano,mes}=periodoAtivo.inv;
  const items=investimentos[ano][mes]||[];
  const tot=sumArr(items.map(i=>({valor:i.valor})));
  document.getElementById('total-inv').textContent=brl(tot);
}

function renderInvResumo(){
  // Agrupa todos os investimentos por nome (todos os períodos)
  const agrupado={};
  Object.entries(investimentos).forEach(([ano,meses])=>{
    Object.values(meses).forEach(items=>{
      items.forEach(i=>{
        const nome=(i.nome||'').trim()||'Sem nome';
        if(!agrupado[nome]) agrupado[nome]=0;
        agrupado[nome]+=(+i.valor||0);
      });
    });
  });
  const nomes=Object.keys(agrupado).filter(k=>agrupado[k]>0);
  const tot=Object.values(agrupado).reduce((a,b)=>a+b,0);
  const sorted=nomes.sort((a,b)=>agrupado[b]-agrupado[a]);

  // Cards
  document.getElementById('inv-total-geral').textContent=brl(tot);
  document.getElementById('inv-tipos').textContent=sorted.length;
  if(sorted.length){
    document.getElementById('inv-maior').textContent=sorted[0];
    document.getElementById('inv-maior-sub').textContent=brl(agrupado[sorted[0]]);
  }

  // Tabela
  document.getElementById('inv-resumo-body').innerHTML=sorted.map(nome=>`
    <tr>
      <td>${nome}</td>
      <td class="mono" style="color:var(--teal);font-weight:700">${brl(agrupado[nome])}</td>
      <td class="mono" style="color:var(--muted)">${tot?(agrupado[nome]/tot*100).toFixed(1):0}%</td>
    </tr>`).join('');

  // Pizza
  if(window._invPie){try{window._invPie.destroy();}catch(e){}}
  if(!sorted.length) return;
  window._invPie=new Chart(document.getElementById('chartInvPie'),{type:'doughnut',
    data:{labels:sorted,datasets:[{data:sorted.map(n=>agrupado[n]),backgroundColor:sorted.map((_,i)=>INV_COLORS[i%INV_COLORS.length]),borderWidth:2,borderColor:'#fff'}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'58%',
      plugins:{legend:{position:'bottom',labels:{color:'#4b5563',font:{size:11},padding:10,boxWidth:10}},
        tooltip:{callbacks:{label:ctx=>` ${brl(ctx.raw)} (${tot?(ctx.raw/tot*100).toFixed(1):0}%)`}}}}
  });
}

async function salvarInvestimentos(){
  const{ano,mes}=periodoAtivo.inv;
  const items=investimentos[ano][mes]||[];
  const btn=document.querySelector('[onclick="salvarInvestimentos()"]');
  btn.disabled=true;btn.textContent='Salvando...';
  try{
    await sb.from('investimentos').delete().eq('ano',ano).eq('mes',mes);
    const rows=items.filter(i=>i.nome||i.valor).map(i=>({ano,mes,data_invest:null,nome:i.nome||'',valor:+i.valor||0}));
    if(rows.length){const{error}=await sb.from('investimentos').insert(rows);if(error)throw error;}
    showToast('✓ Investimentos salvos!');
    renderInvResumo();
  }catch(e){showToast('❌ Erro: '+e.message);}
  finally{btn.disabled=false;btn.textContent='✓ Salvar investimentos';}
}

// ═══════════════════════════════════
// HISTÓRICO
// ═══════════════════════════════════
function renderHistorico(){
  const{ano,mes}=periodoAtivo.hist;
  const d=(dados[ano]||{})[mes]||{entradas:[],saidas:[]};
  const inv=((investimentos[ano]||{})[mes])||[];
  const m=getMesData(ano,mes);

  // Calcular totais por categoria para saídas
  const catTots={};
  d.saidas.forEach(s=>{if(!catTots[s.cat])catTots[s.cat]=0;catTots[s.cat]+=(+s.valor||0);});
  const catSorted=Object.entries(catTots).filter(([,v])=>v>0).sort(([,a],[,b])=>a-b);
  const totalSaidas=catSorted.reduce((a,[,v])=>a+v,0);

  document.getElementById('hist-content').innerHTML=`
    <div class="cards-row">
      <div class="card green"><div class="card-label">Entradas</div><div class="card-value">${brl(m.ent)}</div></div>
      <div class="card red"><div class="card-label">Saídas</div><div class="card-value">${brl(m.saida)}</div></div>
      <div class="card teal"><div class="card-label">Investimentos</div><div class="card-value">${brl(m.inv)}</div></div>
      <div class="card ${m.saldo>=0?'green':'red'}"><div class="card-label">Saldo</div><div class="card-value">${brl(m.saldo)}</div></div>
    </div>
    <div class="grid2">
      <div class="table-card" style="margin-bottom:0">
        <div class="table-head"><h3>Entradas</h3><span style="font-size:12px;color:var(--muted)">${getMesLabel(ano,mes)}</span></div>
        <table><thead><tr><th>Descrição</th><th>Valor</th></tr></thead>
        <tbody>
          ${d.entradas.length?d.entradas.map(e=>`<tr><td>${e.desc||'—'}</td><td class="mono pos">${brl(e.valor)}</td></tr>`).join(''):'<tr><td colspan="2" style="color:var(--muted);text-align:center;padding:18px">Sem entradas</td></tr>'}
          ${d.entradas.length?`<tr style="background:var(--bg3)"><td style="font-weight:700">Total</td><td class="mono pos" style="font-weight:700">${brl(m.ent)}</td></tr>`:''}
        </tbody></table>
      </div>
      <div class="table-card" style="margin-bottom:0">
        <div class="table-head"><h3>Saídas por categoria</h3><span style="font-size:12px;color:var(--muted)">crescente</span></div>
        <table><thead><tr><th>Categoria</th><th>Valor</th><th>%</th></tr></thead>
        <tbody>
          ${catSorted.map(([cat,val])=>`<tr><td>${(CAT_SAIDAS.find(c=>c.value===cat)||{label:cat}).label}</td><td class="mono neg">${brl(val)}</td><td class="mono" style="color:var(--muted)">${totalSaidas?(val/totalSaidas*100).toFixed(1):0}%</td></tr>`).join('')}
          ${catSorted.length?`<tr style="background:var(--bg3)"><td style="font-weight:700">Total</td><td class="mono neg" style="font-weight:700">${brl(totalSaidas)}</td><td class="mono" style="font-weight:700">100%</td></tr>`:'<tr><td colspan="3" style="color:var(--muted);text-align:center;padding:18px">Sem saídas</td></tr>'}
        </tbody></table>
      </div>
    </div>
    ${inv.length?`
    <div class="table-card">
      <div class="table-head"><h3>Investimentos</h3></div>
      <table><thead><tr><th>Investimento</th><th>Valor</th></tr></thead>
      <tbody>
        ${inv.map(i=>`<tr><td>${i.nome||'—'}</td><td class="mono" style="color:var(--teal);font-weight:700">${brl(i.valor)}</td></tr>`).join('')}
        <tr style="background:var(--bg3)"><td style="font-weight:700">Total</td><td class="mono" style="color:var(--teal);font-weight:700">${brl(m.inv)}</td></tr>
      </tbody></table>
    </div>`:''}`;
}

// ═══════════════════════════════════
// GRÁFICOS
// ═══════════════════════════════════
let graficosBuilt=false;

function grafSetTodos(){
  periodoAtivo.graf.mes=null;
  document.querySelectorAll('#mes-btns-graf .period-btn').forEach(b=>b.classList.remove('active'));
  const tb=document.getElementById('graf-todos-btn');if(tb)tb.classList.add('active');
  graficosBuilt=false;buildGraficos();
}

function buildGraficos(){
  const ano=periodoAtivo.graf.ano||anoHoje;
  const mesF=periodoAtivo.graf.mes;
  const gc='rgba(0,0,0,.04)';const tc='#4b5563';

  let allKeys=getMesesDoAno(ano).filter(mes=>{const m=getMesData(ano,mes);return m.ent>0||m.saida>0;});
  let realKeys=mesF?allKeys.filter(k=>k===mesF):allKeys;
  const labels=realKeys.map(k=>getMesLabel(ano,k));

  const catTotais={};
  realKeys.forEach(k=>{
    const d=(dados[ano]||{})[k]||{saidas:[]};
    d.saidas.forEach(s=>{if(!catTotais[s.cat])catTotais[s.cat]=0;catTotais[s.cat]+=(+s.valor||0);});
  });
  const catKeys=Object.keys(catTotais).filter(k=>catTotais[k]>0);

  ['_gBar','_gSaldo','_gPie','_gVs'].forEach(n=>{if(window[n]){try{window[n].destroy();}catch(e){}}});

  // Barras ou horizontal
  if(mesF){
    window._gBar=new Chart(document.getElementById('chartBarG'),{type:'bar',
      data:{labels:catKeys.map(k=>(CAT_SAIDAS.find(c=>c.value===k)||{label:k}).label),
        datasets:[{data:catKeys.map(k=>catTotais[k]),backgroundColor:catKeys.map(k=>CAT_COLORS[k]||'#9ca3af'),borderRadius:5}]},
      options:{responsive:true,maintainAspectRatio:false,indexAxis:'y',plugins:{legend:{display:false}},
        scales:{x:{ticks:{color:tc,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gc}},y:{ticks:{color:tc},grid:{display:false}}}}
    });
  } else {
    const datasets=CAT_SAIDAS.filter(cat=>catKeys.includes(cat.value)).map(cat=>({
      label:cat.label,
      data:realKeys.map(k=>{const d=(dados[ano]||{})[k]||{saidas:[]};return d.saidas.filter(s=>s.cat===cat.value).reduce((a,s)=>a+(+s.valor||0),0);}),
      backgroundColor:CAT_COLORS[cat.value]||'#9ca3af',borderRadius:3,stack:'s'
    }));
    window._gBar=new Chart(document.getElementById('chartBarG'),{type:'bar',
      data:{labels,datasets},
      options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:11},boxWidth:10,padding:8}}},
        scales:{x:{stacked:true,ticks:{color:tc},grid:{color:gc}},y:{stacked:true,ticks:{color:tc,callback:v=>'R$'+Math.round(v/1000)+'k'},grid:{color:gc}}}}
    });
  }

  const saldoD=realKeys.map(k=>getMesData(ano,k).saldo);
  window._gSaldo=new Chart(document.getElementById('chartSaldoG'),{type:'bar',
    data:{labels,datasets:[{label:'Saldo',data:saldoD,backgroundColor:saldoD.map(v=>v>=0?'rgba(22,163,74,.7)':'rgba(220,38,38,.7)'),borderRadius:4}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
      scales:{x:{ticks:{color:tc},grid:{color:gc}},y:{ticks:{color:tc,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gc}}}}
  });

  const tot=Object.values(catTotais).reduce((a,b)=>a+b,0);
  window._gPie=new Chart(document.getElementById('chartPieG'),{type:'doughnut',
    data:{labels:catKeys.map(k=>(CAT_SAIDAS.find(c=>c.value===k)||{label:k}).label),
      datasets:[{data:catKeys.map(k=>catTotais[k]),backgroundColor:catKeys.map(k=>CAT_COLORS[k]||'#9ca3af'),borderWidth:2,borderColor:'#fff'}]},
    options:{responsive:true,maintainAspectRatio:false,cutout:'58%',
      plugins:{legend:{position:'bottom',labels:{color:tc,font:{size:10},padding:8,boxWidth:10}},
        tooltip:{callbacks:{label:ctx=>` ${brl(ctx.raw)} (${tot?(ctx.raw/tot*100).toFixed(1):0}%)`}}}}
  });

  window._gVs=new Chart(document.getElementById('chartVsG'),{type:'bar',
    data:{labels,datasets:[
      {label:'Entradas',data:realKeys.map(k=>getMesData(ano,k).ent),backgroundColor:'rgba(22,163,74,.7)',borderRadius:4},
      {label:'Saídas',data:realKeys.map(k=>getMesData(ano,k).saida),backgroundColor:'rgba(220,38,38,.7)',borderRadius:4},
    ]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{labels:{color:tc,font:{size:12}}}},
      scales:{x:{ticks:{color:tc},grid:{color:gc}},y:{ticks:{color:tc,callback:v=>'R$'+v.toLocaleString('pt-BR')},grid:{color:gc}}}}
  });
}

// ═══════════════════════════════════
// PARCELAS / DÍVIDAS
// ═══════════════════════════════════
function parcPreview(){
  const v=parseFloat(document.getElementById('parc-valor').value)||0;
  const q=parseInt(document.getElementById('parc-qtd').value)||0;
  const el=document.getElementById('parc-preview');
  if(v&&q) el.textContent=`→ ${q}x de ${brl(v/q)} = ${brl(v)} total`;
  else el.textContent='';
}

// ── Salvar dívidas no Supabase ──────────────────────────────────────────────
async function salvarDividas(){
  try{
    // Busca IDs existentes e deleta
    const{data:existing}=await sb.from('dividas').select('id');
    if(existing&&existing.length){
      const ids=existing.map(r=>r.id);
      await sb.from('dividas').delete().in('id',ids);
    }
    if(!dividasLista.length) return;
    const rows=dividasLista.map(d=>({
      nome:d.nome,
      valor_total:d.valorTotal,
      qtd:d.qtd,
      parcelas:d.parcelas
    }));
    const{error}=await sb.from('dividas').insert(rows);
    if(error) throw error;
  }catch(e){
    console.error('Erro ao salvar dividas:',e);
    showToast('⚠ Erro ao salvar: '+e.message);
  }
}

function adicionarDivida(){
  const desc=document.getElementById('parc-desc').value.trim();
  const valor=parseFloat(document.getElementById('parc-valor').value)||0;
  const qtd=parseInt(document.getElementById('parc-qtd').value)||0;
  const mes=parseInt(document.getElementById('parc-mes-inicio').value);
  const ano=document.getElementById('parc-ano-inicio').value;
  if(!desc||!valor||!qtd){showToast('❌ Preencha todos os campos');return;}
  const vlrParc=+(valor/qtd).toFixed(2);
  const parcelas=[];
  let a=+ano;let m=mes;
  for(let i=0;i<qtd;i++){
    parcelas.push({ano:a,mes:m,valor:vlrParc,pago:false,num:i+1});
    m++;if(m>12){m=1;a++;}
  }
  dividasLista.push({nome:desc,valorTotal:valor,qtd,parcelas});
  document.getElementById('parc-desc').value='';
  document.getElementById('parc-valor').value='';
  document.getElementById('parc-qtd').value='';
  document.getElementById('parc-preview').textContent='';
  renderParcelas();
  salvarDividas();
  showToast('✓ Dívida adicionada e salva!');
}

function toggleParcela(dIdx,pIdx){
  dividasLista[dIdx].parcelas[pIdx].pago=!dividasLista[dIdx].parcelas[pIdx].pago;
  renderParcelas();
  salvarDividas();
}

function removerDivida(idx){
  if(!confirm('Remover esta dívida?')) return;
  dividasLista.splice(idx,1);
  renderParcelas();
  salvarDividas();
  showToast('✓ Dívida removida!');
}

function renderParcelas(){
  let totalAberto=0,totalPago=0;
  dividasLista.forEach(d=>{
    d.parcelas.forEach(p=>{
      if(p.pago) totalPago+=p.valor;
      else totalAberto+=p.valor;
    });
  });
  document.getElementById('parc-total-aberto').textContent=brl(totalAberto);
  document.getElementById('parc-total-pago').textContent=brl(totalPago);
  document.getElementById('parc-qtd-dividas').textContent=dividasLista.length;

  document.getElementById('dividas-lista').innerHTML=dividasLista.length?dividasLista.map((d,di)=>{
    const pagas=d.parcelas.filter(p=>p.pago).length;
    const vlrPago=d.parcelas.filter(p=>p.pago).reduce((a,p)=>a+p.valor,0);
    const vlrAberto=d.valorTotal-vlrPago;
    return `<div class="divida-card">
      <div class="divida-header">
        <div>
          <div class="divida-nome">${d.nome}</div>
          <div class="divida-info">${d.qtd}x de ${brl(d.valorTotal/d.qtd)} · Total: ${brl(d.valorTotal)} · ${pagas} de ${d.qtd} pagas</div>
        </div>
        <button class="btn btn-ghost" style="font-size:12px;padding:5px 10px;color:var(--red)" onclick="removerDivida(${di})">✕ Remover</button>
      </div>
      ${d.parcelas.map((p,pi)=>`
        <div class="parc-row">
          <input type="checkbox" class="parc-check" ${p.pago?'checked':''} onchange="toggleParcela(${di},${pi})">
          <span class="parc-mes">${MESES_NOMES[p.mes-1]}/${String(p.ano).slice(2)} — ${p.num}/${d.qtd}</span>
          <span class="parc-val ${p.pago?'pago':''}">${brl(p.valor)}</span>
          <span class="parc-badge ${p.pago?'paga':'aberta'}">${p.pago?'Pago':'Em aberto'}</span>
        </div>`).join('')}
      <div class="parc-total-box">
        <div style="display:flex;gap:24px">
          <div><div style="font-size:11px;color:var(--muted);font-weight:600;text-transform:uppercase">Pago</div><div style="font-family:var(--mono);font-weight:700;color:var(--green)">${brl(vlrPago)}</div></div>
          <div><div style="font-size:11px;color:var(--muted);font-weight:600;text-transform:uppercase">Em aberto</div><div style="font-family:var(--mono);font-weight:700;color:var(--red)">${brl(vlrAberto)}</div></div>
        </div>
        <div style="text-align:right"><div style="font-size:11px;color:var(--muted);font-weight:600;text-transform:uppercase">Total dívida</div><div style="font-family:var(--mono);font-weight:700;font-size:18px">${brl(d.valorTotal)}</div></div>
      </div>
    </div>`;
  }).join(''):'<div style="color:var(--muted);text-align:center;padding:32px;background:var(--bg2);border:1px solid var(--border);border-radius:var(--radius)">Nenhuma dívida cadastrada. Adicione uma acima.</div>';

  // Gráfico próximos meses
  const mesesFuturos=[];
  const hoje2=new Date();
  for(let i=0;i<8;i++){
    const d=new Date(hoje2.getFullYear(),hoje2.getMonth()+i,1);
    mesesFuturos.push({ano:d.getFullYear(),mes:d.getMonth()+1});
  }
  const totaisMeses=mesesFuturos.map(({ano,mes})=>{
    let tot=0;
    dividasLista.forEach(d=>{d.parcelas.forEach(p=>{if(!p.pago&&p.ano===ano&&p.mes===mes)tot+=p.valor;});});
    return tot;
  });
  const labelsM=mesesFuturos.map(({ano,mes})=>`${MESES_NOMES[mes-1]}/${String(ano).slice(2)}`);
  if(window._parcelasChart){try{window._parcelasChart.destroy();}catch(e){}}
  window._parcelasChart=new Chart(document.getElementById('chartParcelasG'),{type:'bar',
    data:{labels:labelsM,datasets:[{label:'Parcelas em aberto',data:totaisMeses,backgroundColor:totaisMeses.map(v=>v>0?'rgba(217,119,6,.75)':'rgba(200,205,216,.4)'),borderRadius:6}]},
    options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
      scales:{x:{ticks:{color:'#4b5563'},grid:{color:'rgba(0,0,0,.04)'}},y:{ticks:{color:'#4b5563',callback:v=>'R$'+v.toFixed(0)},grid:{color:'rgba(0,0,0,.04)'}}}}
  });
}

// ═══════════════════════════════════
// TOAST
// ═══════════════════════════════════
function showToast(msg){
  const t=document.getElementById('toast');
  t.textContent=msg;t.classList.add('show');
  setTimeout(()=>t.classList.remove('show'),2800);
}

// ═══════════════════════════════════
// SUPABASE — CARREGAR DADOS
// ═══════════════════════════════════
async function carregarDados(){
  try{
    const{data:lanc,error:e1}=await sb.from('lancamentos').select('*');
    if(e1) throw e1;
    if(lanc&&lanc.length){
      dados={};
      lanc.forEach(row=>{
        if(!dados[row.ano]) dados[row.ano]={};
        if(!dados[row.ano][row.mes]) dados[row.ano][row.mes]={entradas:[],saidas:[]};
        if(row.tipo==='entrada') dados[row.ano][row.mes].entradas.push({desc:row.descricao||'',valor:+row.valor||0});
        else dados[row.ano][row.mes].saidas.push({desc:row.descricao||'',cat:row.categoria||'outros',valor:+row.valor||0});
      });
    }
    const{data:inv,error:e2}=await sb.from('investimentos').select('*');
    if(e2) throw e2;
    if(inv&&inv.length){
      investimentos={};
      inv.forEach(row=>{
        if(!investimentos[row.ano]) investimentos[row.ano]={};
        if(!investimentos[row.ano][row.mes]) investimentos[row.ano][row.mes]=[];
        investimentos[row.ano][row.mes].push({nome:row.nome||'',valor:+row.valor||0});
      });
    }

    // Carregar dívidas
    const{data:div,error:e3}=await sb.from('dividas').select('*');
    if(!e3&&div&&div.length){
      dividasLista=div.map(row=>({
        nome:row.nome||'',
        valorTotal:+row.valor_total||0,
        qtd:+row.qtd||0,
        parcelas:typeof row.parcelas==='string'?JSON.parse(row.parcelas):(row.parcelas||[])
      }));
    }

    const badge=document.getElementById('sync-badge');
    badge.innerHTML='<span class="sync-dot" style="background:var(--green);animation:none"></span>Conectado';
    badge.style.background='rgba(21,128,61,.12)';badge.style.color='var(--green)';
  }catch(err){
    console.error('Supabase:',err);
    const badge=document.getElementById('sync-badge');
    badge.innerHTML='⚠ Offline';badge.style.background='var(--red-dim)';badge.style.color='var(--red)';
  }finally{
    document.getElementById('loading-overlay').style.display='none';
    buildDashboard();
    buildPeriodSel('lanc');
    periodoAtivo.lanc={ano:anoHoje,mes:mesHoje};
    buildPeriodSel('lanc');
    loadMesForm();
  }
}

// Dados históricos de exemplo para não começar vazio
dados={
  '2026':{
    '2':{entradas:[{desc:'Salário',valor:1677}],saidas:[{desc:'Cartão de crédito',cat:'outros',valor:2407.54},{desc:'Aluguel+Água+Luz',cat:'moradia',valor:1750},{desc:'Outros',cat:'outros',valor:341.48}]},
    '3':{entradas:[{desc:'Pró-labore',valor:2500},{desc:'Freelance',valor:1333.33}],saidas:[{desc:'Cartão',cat:'outros',valor:2171.64},{desc:'Aluguel+Água+Luz',cat:'moradia',valor:1750},{desc:'Outros',cat:'outros',valor:210}]},
    '4':{entradas:[],saidas:[{desc:'Cartão',cat:'outros',valor:2348.39},{desc:'Aluguel+Água+Luz',cat:'moradia',valor:2100},{desc:'Outros',cat:'outros',valor:968.31}]},
    '5':{entradas:[],saidas:[{desc:'Cartão',cat:'outros',valor:3124.03},{desc:'Aluguel+Água+Luz',cat:'moradia',valor:2717},{desc:'Outros',cat:'outros',valor:800}]},
    '6':{entradas:[],saidas:[{desc:'Cartão',cat:'outros',valor:1126.55},{desc:'Outros',cat:'outros',valor:2717}]},
    '7':{entradas:[],saidas:[{desc:'Cartão',cat:'outros',valor:722.66}]},
  }
};

carregarDados();
</script>
</body>
</html>

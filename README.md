<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>TriniFinBet</title>

<!-- Charts & html2canvas -->
<script src="https://cdn.jsdelivr.net/npm/apexcharts"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

<style>
  :root{
    --bg:#f6f8fb; --card:#ffffff; --text:#0b1220; --muted:#6b7280;
    --accent:#0ea5e9; --border:#e6edf3; --success:#059669; --danger:#ef4444;
    --maxwidth:980px;
  }
  html,body{height:100%;margin:0;font-family:Inter,system-ui,Arial;background:var(--bg);color:var(--text);-webkit-font-smoothing:antialiased}
  .app{max-width:var(--maxwidth);margin:8px auto;padding-bottom:110px}
  header.app-header{display:flex;align-items:center;justify-content:space-between;padding:8px}
  .brand{display:flex;gap:12px;align-items:center}
  .logo{width:44px;height:44px;border-radius:8px;background:linear-gradient(135deg,#0ea5e9,#60a5fa)}
  .title{font-weight:700;font-size:18px}
  .subtitle{font-size:12px;color:var(--muted)}
  .user-area{font-size:13px;color:var(--muted)}
  main.main{margin:6px}
  .card{background:var(--card);border-radius:10px;padding:12px;border:1px solid var(--border);margin-bottom:12px;box-shadow:0 6px 18px rgba(2,6,23,0.02)}
  label{display:block;font-size:13px;color:var(--muted);margin-bottom:6px}
  input,select,textarea,button{font-family:inherit}
  input,select,textarea{width:100%;padding:8px;border-radius:8px;border:1px solid var(--border);background:transparent;color:var(--text);box-sizing:border-box}
  .row{display:flex;gap:10px;flex-wrap:wrap;margin-bottom:10px}
  .row.two>div{flex:1}
  .btn{background:var(--accent);color:#fff;border:none;padding:8px 10px;border-radius:8px;cursor:pointer}
  .btn.ghost{background:transparent;border:1px solid var(--border);color:var(--text)}
  .btn.primary{background:var(--accent)}
  .small{font-size:12px;color:var(--muted)}
  .muted{color:var(--muted)}
  .actions{display:flex;gap:8px;align-items:center;justify-content:space-between}
  nav.bottom-nav{position:fixed;left:12px;right:12px;bottom:12px;background:var(--card);border-radius:12px;padding:6px;border:1px solid var(--border);display:flex;gap:6px;justify-content:space-around;box-shadow:0 8px 24px rgba(2,6,23,0.06)}
  .nav-btn{flex:1;padding:8px 6px;border-radius:8px;border:0;background:transparent;color:var(--muted);font-weight:600;cursor:pointer}
  .nav-btn.active{background:var(--accent);color:#fff}
  .hidden{display:none}
  .metrics{display:flex;gap:12px;flex-wrap:wrap}
  .metric{flex:1;min-width:160px;background:#fbfdff;padding:8px;border-radius:8px;border:1px solid var(--border)}
  .metric .label{font-size:12px;color:var(--muted)}
  .metric .big{font-weight:700;font-size:18px;margin-top:6px}
  .charts{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  @media(max-width:780px){ .charts{grid-template-columns:1fr} .metrics{flex-direction:column} nav.bottom-nav{padding:8px} }

  /* slips */
  .slips{display:flex;flex-direction:column;gap:8px}
  .slip{border:1px solid var(--border);border-radius:10px;background:#fff;overflow:hidden}
  .slip-head{padding:10px;display:flex;justify-content:space-between;align-items:center;cursor:pointer}
  .slip-head h4{margin:0;font-size:14px}
  .slip-body{padding:10px;border-top:1px solid var(--border);display:none}
  .slip-body.show{display:block}
  .slip-small{font-size:12px;color:var(--muted);margin-top:6px}

  /* logs */
  .logs-list{display:flex;flex-direction:column;gap:8px}
  .log-item{background:#fff;border-radius:8px;padding:10px;border:1px solid var(--border);display:flex;justify-content:space-between;gap:8px;align-items:center}
  .log-left{flex:1}
  .log-meta{font-size:13px;color:var(--muted);margin-bottom:6px}
  .log-amt{font-weight:700}

  /* settings */
  .ball-row{display:flex;gap:8px;align-items:center;margin-bottom:8px}
  .ball-row input[type="text"]{flex:1}
  .apply-note{font-size:12px;color:var(--muted);margin-top:8px}
</style>
</head>
<body>
<div class="app" id="app">
  <header class="app-header card" style="align-items:center">
    <div class="brand">
      <div class="logo" aria-hidden="true"></div>
      <div>
        <div class="title">TriniFinBet</div>
        <div class="subtitle">Betting finance planner</div>
      </div>
    </div>
    <div class="user-area" id="userArea">Local</div>
  </header>

  <main class="main">

    <!-- DRAW HEADER -->
    <section id="drawHeader" class="card">
      <div class="row">
        <div style="width:170px"><label>Date</label><input id="entry_date" type="date" /></div>
        <div style="width:140px"><label>Draw time</label><select id="entry_draw"><option>Morning</option><option>Midday</option><option>Afternoon</option><option>Evening</option></select></div>
        <div style="width:140px"><label>Draw # (optional)</label><input id="entry_drawnum" placeholder="optional" /></div>
        <div style="width:140px"><label>Tickets # (optional)</label><input id="entry_ticketnum" placeholder="optional" /></div>
      </div>
    </section>

    <!-- SLIPS -->
    <section id="screen-entry" class="card">
      <h2>Bet Slips (accordion)</h2>
      <div class="small muted">Add one or more bet slips for this draw. Save each slip individually, then press Save Draw to store the draw.</div>

      <div style="margin-top:12px">
        <div class="slips" id="slipsContainer"></div>

        <div style="display:flex;gap:8px;margin-top:10px">
          <button id="addSlipBtn" class="btn ghost">+ Add New Slip</button>
          <button id="saveAllSlips" class="btn">Save Draw (all slips)</button>
          <button id="clearSlips" class="btn ghost">Clear</button>
        </div>
        <div id="slipsMsg" class="small muted" style="margin-top:8px"></div>
      </div>

      <!-- Draw result -->
      <div class="card" style="margin-top:12px">
        <h3>Enter Draw Result (one winning number)</h3>
        <div class="row">
          <div style="width:160px"><label>Winning Number</label><input id="result_number" type="number" min="1" max="36" /></div>
          <div style="width:140px"><label>SureCall day?</label><input id="result_surecall" type="checkbox" /></div>
          <div style="flex:1"><label>Winning Balls (check all that applied)</label>
            <div id="winningBalls" class="small muted"></div>
          </div>
        </div>
        <div style="display:flex;gap:8px;margin-top:8px">
          <button id="applyResultBtn" class="btn primary">Apply Result (compute P/L)</button>
          <button id="clearResultBtn" class="btn ghost">Clear Result</button>
          <button id="exportDrawCsv" class="btn ghost">Export Draw CSV</button>
        </div>
        <div id="resultMsg" class="small muted" style="margin-top:8px"></div>
      </div>
    </section>

    <!-- ANALYTICS -->
    <section id="screen-analytics" class="card hidden">
      <h2>Analytics</h2>
      <div class="card" style="margin-bottom:12px">
        <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
          <div style="width:160px"><label>From</label><input id="global_from" type="date" /></div>
          <div style="width:160px"><label>To</label><input id="global_to" type="date" /></div>
          <div style="align-self:end"><button id="runGlobal" class="btn">Run</button></div>
          <div style="margin-left:auto"><button id="exportCsvAll" class="btn ghost">Export CSV</button> <button id="exportPngAll" class="btn ghost">Export PNG</button></div>
        </div>
      </div>

      <div class="card metrics">
        <div class="metric"><div class="label">Total Wager</div><div id="sumWager" class="big">$0.00</div></div>
        <div class="metric"><div class="label">Total Win</div><div id="sumWinning" class="big">$0.00</div></div>
        <div class="metric"><div class="label">Net P/L</div><div id="sumPL" class="big">$0.00</div></div>
        <div class="metric"><div class="label">Win Rate</div><div id="winRate" class="big">0%</div></div>
      </div>

      <div class="card charts">
        <div class="chart-card"><h4>Profit over time</h4><div id="chart_line" style="height:240px"></div></div>
        <div class="chart-card"><h4>Wins vs Losses</h4><div id="chart_pie" style="height:240px"></div></div>
      </div>

      <div class="card">
        <h3>Advice</h3>
        <div id="adviceBox" class="small muted">Run analysis to generate advice.</div>
      </div>
    </section>

    <!-- LOGS -->
    <section id="screen-logs" class="card hidden">
      <h2>Betting Logs</h2>
      <div class="card" style="margin-bottom:8px">
        <div class="row">
          <div style="flex:1"><label>From</label><input id="log_from" type="date" /></div>
          <div style="flex:1"><label>To</label><input id="log_to" type="date" /></div>
          <div style="width:120px;align-self:end"><button id="filterLogsBtn" class="btn ghost">Filter</button></div>
        </div>
      </div>

      <div id="logsList" class="logs-list"></div>
    </section>

    <!-- SETTINGS -->
    <section id="screen-settings" class="card hidden">
      <h2>Ball Payouts & Settings</h2>
      <div class="small muted">Edit default payouts (affects future draws). Base payout defaults to 26.00 (editable).</div>

      <div id="ballSettings" class="card" style="padding:12px"></div>

      <div class="row" style="margin-top:10px">
        <input id="newBallName" placeholder="New ball name" />
        <input id="newBallPayout" type="number" step="0.01" placeholder="Payout (multiplier)" />
        <button id="addBallBtnSettings" class="btn ghost">Add Ball</button>
        <button id="applyBallChanges" class="btn primary">Apply</button>
      </div>
      <div class="apply-note small muted">Default payout multipliers are editable. Use "Apply" to confirm changes for future draws.</div>
      <div id="settingsMsg" class="small muted" style="margin-top:8px"></div>
    </section>

  </main>

  <nav class="bottom-nav">
    <button class="nav-btn active" data-screen="entry">Entry</button>
    <button class="nav-btn" data-screen="analytics">Analytics</button>
    <button class="nav-btn" data-screen="logs">Logs</button>
    <button class="nav-btn" data-screen="settings">Settings</button>
  </nav>
</div>

<script>
(() => {
  const KEY = 'trinifinbet_prod_v1';
  const fmt = v => (Math.round((Number(v)||0)*100)/100).toFixed(2);
  const $ = s => document.querySelector(s);
  const $$ = s => Array.from(document.querySelectorAll(s));

  let store = { draws: [], balls: [], payouts:{ base:26.00, surecall:28.00 }, meta:{} };
  const defaultBalls = [
    { id:'b_mega', name:'Mega Ball', payout:50 },
    { id:'b_ultra', name:'Mega Ultra Ball', payout:100 },
    { id:'b_xtreme', name:'Mega Xtreme Ball', payout:200 },
    { id:'b_promo', name:'Promo Ball', payout:75 }
  ];

  // load/save
  function load(){
    try{
      const raw = localStorage.getItem(KEY);
      if(raw) store = JSON.parse(raw);
    }catch(e){}
    if(!store.balls || !store.balls.length) store.balls = defaultBalls.slice();
    if(!store.payouts) store.payouts = { base:26.00, surecall:28.00 };
  }
  function save(){ localStorage.setItem(KEY, JSON.stringify(store)); }

  // navigation screens
  function show(screen){
    $$('.screen').forEach(s=>s.classList.add('hidden'));
    $(`#screen-${screen}`).classList.remove('hidden');
    $$('.nav-btn').forEach(b=>b.classList.toggle('active', b.dataset.screen===screen));
  }
  $$('.nav-btn').forEach(b=> b.addEventListener('click', ()=> show(b.dataset.screen)));

  // slip factory
  function createSlipElement(slip = {}, idx=0){
    const el = document.createElement('div'); el.className = 'slip';
    const head = document.createElement('div'); head.className = 'slip-head';
    const title = document.createElement('h4'); title.textContent = slip.number ? `Bet Slip ${idx+1} — #${slip.number}` : `Bet Slip ${idx+1}`;
    head.appendChild(title);
    const meta = document.createElement('div'); meta.innerHTML = `<span class="small muted">Base $${fmt(slip.base||0)}</span>`;
    head.appendChild(meta);
    el.appendChild(head);

    const body = document.createElement('div'); body.className = 'slip-body';
    const ballsHtml = store.balls.map(b=>{
      const val = (slip.balls && slip.balls[b.name]) ? fmt(slip.balls[b.name]) : '';
      return `<div style="width:160px"><label><input type="checkbox" class="slip-ball-check" data-ball="${b.name}" ${val? 'checked':''} /> ${b.name}</label><input type="number" class="slip-ball-amt" data-ball="${b.name}" step="0.01" placeholder="0" value="${val}" /></div>`;
    }).join('');
    body.innerHTML = `
      <div class="row">
        <div style="width:110px"><label>Number</label><input type="number" min="1" max="36" class="slip-number" value="${slip.number||''}" /></div>
        <div style="width:140px"><label>Base TT$</label><input type="number" step="0.01" class="slip-base" value="${fmt(slip.base||0)}" /></div>
        <div style="width:110px"><label>Tickets</label><input type="number" min="1" class="slip-tickets" value="${slip.tickets||1}" /></div>
      </div>
      <div class="slip-small">Ball bets (amount per slip)</div>
      <div class="row" style="margin-top:6px">${ballsHtml}</div>
      <div class="row" style="margin-top:8px"><div style="flex:1"><label>Notes</label><input class="slip-notes" value="${(slip.notes||'')}" /></div></div>
      <div style="display:flex;gap:8px;justify-content:flex-end;margin-top:8px"><button class="btn ghost del-slip">Delete</button><button class="btn save-slip">Save Slip</button></div>
    `;
    el.appendChild(body);

    // toggle
    head.addEventListener('click', ()=> body.classList.toggle('show'));

    // delete
    body.querySelector('.del-slip').addEventListener('click', ()=> {
      if(!confirm('Delete this slip?')) return;
      el.remove();
      updateSlipsMsg();
    });

    // save slip (keep in DOM as pending until Save Draw)
    body.querySelector('.save-slip').addEventListener('click', ()=> {
      const number = parseInt(body.querySelector('.slip-number').value);
      const base = parseFloat(body.querySelector('.slip-base').value) || 0;
      const tickets = Math.max(1, parseInt(body.querySelector('.slip-tickets').value) || 1);
      const notes = body.querySelector('.slip-notes').value || '';
      const balls = {};
      body.querySelectorAll('.slip-ball-amt').forEach(inp=>{
        const ball = inp.dataset.ball; const val = parseFloat(inp.value)||0;
        if(val > 0) balls[ball] = val;
      });
      if(!number || number<1 || number>36) return alert('Enter a valid number 1-36 for this slip.');
      el.dataset.saved = '1';
      el.dataset.number = number;
      el.dataset.base = base;
      el.dataset.tickets = tickets;
      el.dataset.notes = notes;
      el.dataset.balls = JSON.stringify(balls);
      el.querySelector('h4').textContent = `Bet Slip — #${number}`;
      updateSlipsMsg();
      // small non-blocking notice
      setTimeout(()=> alert('Slip saved (still pending until you press "Save Draw").'), 10);
    });

    return el;
  }

  function updateSlipsMsg(){
    const saved = Array.from($('#slipsContainer').children).filter(c=>c.dataset.saved==='1').length;
    const total = $('#slipsContainer').children.length;
    $('#slipsMsg').textContent = `${saved}/${total} slips saved (press Save Draw to store the draw).`;
  }

  // add/clear slips
  $('#addSlipBtn').addEventListener('click', ()=> {
    $('#slipsContainer').appendChild(createSlipElement({}, $('#slipsContainer').children.length));
    updateSlipsMsg();
  });
  $('#clearSlips').addEventListener('click', ()=> { if(!confirm('Clear all current slips?')) return; $('#slipsContainer').innerHTML = ''; updateSlipsMsg(); });

  // Save Draw (persist all pending saved slips)
  $('#saveAllSlips').addEventListener('click', ()=>{
    const date = $('#entry_date').value; const draw = $('#entry_draw').value;
    if(!date) return alert('Pick a date for the draw.');
    const drawNum = ($('#entry_drawnum').value || '').trim();
    const ticketNum = ($('#entry_ticketnum').value || '').trim();
    const slipEls = Array.from($('#slipsContainer').children);
    if(!slipEls.length) return alert('Add at least one slip.');
    const bets = [];
    for(const s of slipEls){
      if(s.dataset.saved !== '1') return alert('Please save each slip using Save Slip before saving the draw.');
      bets.push({
        id: 'bet_' + Date.now() + '_' + Math.floor(Math.random()*9999),
        number: Number(s.dataset.number),
        base: Number(s.dataset.base),
        tickets: Number(s.dataset.tickets),
        balls: JSON.parse(s.dataset.balls || '{}'),
        notes: s.dataset.notes||'',
        totalWager: null,
        totalWin: null,
        pl: null
      });
    }
    store.draws.push({
      id: 'draw_' + Date.now() + '_' + Math.floor(Math.random()*9999),
      date, draw, drawNum, ticketNum, bets, result: null, computed: false
    });
    $('#slipsContainer').innerHTML = '';
    updateSlipsMsg();
    save(); renderWinningBalls(); renderLogs(); renderAnalytics();
    alert('Saved draw with ' + bets.length + ' bets. Now enter the draw result when available.');
  });

  // render winning balls with checkboxes
  function renderWinningBalls(){
    const container = $('#winningBalls'); container.innerHTML = '';
    store.balls.forEach(b=>{
      const id = 'winchk_'+b.id;
      const wrap = document.createElement('label');
      wrap.style.marginRight='10px';
      const cb = document.createElement('input');
      cb.type = 'checkbox';
      cb.id = id;
      cb.dataset.ball = b.name;
      wrap.appendChild(cb);
      const txt = document.createTextNode(' ' + b.name + ' (' + fmt(b.payout) + ')');
      wrap.appendChild(txt);
      container.appendChild(wrap);
    });
  }

  // apply result -> compute P/L for first pending draw matching date & draw
  $('#applyResultBtn').addEventListener('click', ()=>{
    const winNum = $('#result_number').value; const sure = !!$('#result_surecall').checked;
    if(!winNum) return $('#resultMsg').textContent = 'Enter winning number.';
    const date = $('#entry_date').value; const draw = $('#entry_draw').value;
    if(!date) return $('#resultMsg').textContent = 'Select the same date & draw you saved slips for.';
    const target = store.draws.find(d => d.date===date && d.draw===draw && !d.computed);
    if(!target) return $('#resultMsg').textContent = 'No pending draw found for this date/draw.';
    const winningBalls = [];
    store.balls.forEach(b=>{
      const el = document.querySelector(`#winchk_${b.id}`);
      if(el && el.checked) winningBalls.push(b.name);
    });
    const basePayout = sure ? (store.payouts.surecall || 28) : (store.payouts.base || 26);

    target.result = { winningNumber: String(winNum), sureCall: sure, winningBalls, computedAt: new Date().toISOString() };

    // compute each bet
    target.bets.forEach(bet=>{
      const tickets = bet.tickets || 1;
      const baseAmt = Number(bet.base || 0);
      const ballAmounts = bet.balls || {};
      const ballSumPerTicket = Object.values(ballAmounts).reduce((s,v)=>s + (Number(v)||0), 0);
      const totalWagerPerTicket = baseAmt + ballSumPerTicket;
      const totalWager = totalWagerPerTicket * tickets;

      let winPerTicket = 0;
      if(String(bet.number) === String(winNum)){
        // base win
        winPerTicket += baseAmt * basePayout;
        // balls that matched
        store.balls.forEach(ball=>{
          const amt = Number(ballAmounts[ball.name] || 0);
          if(amt > 0 && winningBalls.includes(ball.name)){
            winPerTicket += amt * (ball.payout || 0);
          }
        });
      }

      // tax: 10% per full $1000 in winPerTicket (floor(win/1000)*100)
      const thousands = Math.floor(winPerTicket / 1000);
      const taxPerTicket = thousands * 100;
      const netWinPerTicket = Math.max(0, winPerTicket - taxPerTicket);
      const totalWin = netWinPerTicket * tickets;
      const pl = totalWin - totalWager;

      bet.totalWager = totalWager;
      bet.totalWin = totalWin;
      bet.pl = pl;
    });

    target.computed = true;
    save();
    $('#resultMsg').textContent = 'Result applied and P/L computed.';
    renderLogs(); renderAnalytics();
  });

  $('#clearResultBtn').addEventListener('click', ()=> {
    if(!confirm('Clear result fields?')) return;
    $('#result_number').value=''; $('#result_surecall').checked=false; $('#resultMsg').textContent='Cleared';
  });

  // logs rendering & handlers
  function renderLogs(filterFrom, filterTo){
    const list = $('#logsList'); list.innerHTML = '';
    let rows = [];
    store.draws.slice().reverse().forEach(draw=>{
      draw.bets.forEach(bet=>{
        rows.push({
          drawId: draw.id, date: draw.date, draw: draw.draw, number: bet.number,
          base: bet.base, balls: bet.balls, tickets: bet.tickets, notes: bet.notes,
          totalWager: bet.totalWager, totalWin: bet.totalWin, pl: bet.pl, computed: draw.computed, betId: bet.id
        });
      });
    });
    if(filterFrom || filterTo){
      const f = filterFrom || '0000-00-00'; const t = filterTo || '9999-12-31';
      rows = rows.filter(r => r.date >= f && r.date <= t);
    }
    if(!rows.length){ list.innerHTML = '<div class="small muted">No logs yet</div>'; return; }
    rows.forEach(r=>{
      const itm = document.createElement('div'); itm.className='log-item';
      itm.innerHTML = `<div class="log-left">
        <div class="log-meta">${r.date} • ${r.draw} • #${r.number} • tickets:${r.tickets} • ${r.notes||''}</div>
        <div><span class="small muted">Wager:</span> <span class="log-amt">$${fmt(r.totalWager|| (Number(r.base||0) + Object.values(r.balls||{}).reduce((s,v)=>s+(Number(v)||0),0)))}</span> &nbsp; <span class="small muted">Win:</span> <span>$${fmt(r.totalWin||0)}</span></div>
      </div>
      <div style="text-align:right">
        <div style="font-weight:700; color:${(r.pl>=0)?'var(--success)':'var(--danger)'}">$${fmt(r.pl|| (-(Number(r.base||0) + Object.values(r.balls||{}).reduce((s,v)=>s+(Number(v)||0),0))))}</div>
        <div style="margin-top:8px"><button class="btn ghost edit-log" data-d="${r.drawId}" data-b="${r.betId}">Edit</button> <button class="btn ghost del-log" data-d="${r.drawId}" data-b="${r.betId}">Delete</button></div>
      </div>`;
      list.appendChild(itm);
    });

    // delete handlers
    $$('.del-log').forEach(btn=> btn.addEventListener('click', ()=>{
      const d = btn.dataset.d; const b = btn.dataset.b;
      if(!confirm('Delete this bet?')) return;
      const draw = store.draws.find(x=>x.id===d);
      if(!draw) return;
      draw.bets = draw.bets.filter(x=>x.id!==b);
      if(draw.bets.length===0) store.draws = store.draws.filter(x=>x.id!==d);
      save(); renderLogs(); renderAnalytics();
    }));

    // edit handlers - populate slips area with the bet for re-editing
    $$('.edit-log').forEach(btn=> btn.addEventListener('click', ()=>{
      const d = btn.dataset.d; const b = btn.dataset.b;
      const draw = store.draws.find(x=>x.id===d); if(!draw) return;
      const bet = draw.bets.find(x=>x.id===b); if(!bet) return;
      // populate slips area for editing: create a slip with bet values and mark it saved
      $('#slipsContainer').innerHTML = '';
      const slipEl = createSlipElement(bet, 0);
      // mark it as saved so user can press Save Draw after editing (or save-slip will re-save)
      slipEl.dataset.saved = '1';
      slipEl.dataset.number = bet.number;
      slipEl.dataset.base = bet.base;
      slipEl.dataset.tickets = bet.tickets;
      slipEl.dataset.notes = bet.notes;
      slipEl.dataset.balls = JSON.stringify(bet.balls||{});
      $('#slipsContainer').appendChild(slipEl);
      // remove original bet from storage (user will re-save)
      draw.bets = draw.bets.filter(x=>x.id!==b);
      if(draw.bets.length===0) store.draws = store.draws.filter(x=>x.id!==d);
      save(); updateSlipsMsg(); show('entry');
    }));
  }

  // analytics
  function computeRange(from,to){
    const res = [];
    store.draws.forEach(draw=>{
      if(from && draw.date < from) return;
      if(to && draw.date > to) return;
      draw.bets.forEach(bet=>{
        res.push(Object.assign({ date: draw.date, draw: draw.draw }, bet));
      });
    });
    return res;
  }

  function renderAnalytics(){
    const from = $('#global_from').value; const to = $('#global_to').value;
    const rows = computeRange(from,to);
    let totalWager=0,totalWin=0,totalPL=0,wins=0;
    const profitByDate = {};
    rows.forEach(r=>{
      totalWager += Number(r.totalWager||0);
      totalWin += Number(r.totalWin||0);
      totalPL += Number(r.pl|| (-(Number(r.base||0) + Object.values(r.balls||{}).reduce((s,v)=>s+(Number(v)||0),0))));
      if(Number(r.pl||0) >= 0) wins++;
      profitByDate[r.date] = (profitByDate[r.date]||0) + Number(r.pl||0);
    });
    $('#sumWager').textContent = `$${fmt(totalWager)}`;
    $('#sumWinning').textContent = `$${fmt(totalWin)}`;
    $('#sumPL').textContent = `$${fmt(totalPL)}`;
    $('#winRate').textContent = rows.length ? Math.round((wins/rows.length)*100) + '%' : '0%';

    try{
      const labels = Object.keys(profitByDate).sort();
      const data = labels.map(d=>profitByDate[d]);
      const lineEl = document.getElementById('chart_line'); if(lineEl){ lineEl.innerHTML=''; new ApexCharts(lineEl,{chart:{type:'line',height:240,toolbar:{show:false}},series:[{name:'PL',data:data}],xaxis:{categories:labels}}).render(); }
      const pieEl = document.getElementById('chart_pie'); if(pieEl){ pieEl.innerHTML=''; new ApexCharts(pieEl,{chart:{type:'pie',height:240},series:[wins, Math.max(0, rows.length-wins)], labels:['Wins','Losses']}).render(); }
    }catch(e){ console.warn(e); }

    buildAdvice(totalPL, rows);
  }

  function buildAdvice(totalPL, entries){
    const box = $('#adviceBox');
    if(!entries.length){ box.textContent = 'No data in range'; return; }
    if(totalPL >= 0){ box.innerHTML = `<div style="color:var(--success)"><strong>Up $${fmt(totalPL)}</strong> — consider gradual growth and set targets.</div>`; return; }
    const loss = Math.abs(totalPL);
    const medianBase = entries.map(e=>Number(e.base||0)).sort((a,b)=>a-b)[Math.floor(entries.length/2)] || 1;
    let html = `<div style="color:var(--danger)"><strong>Loss: $${fmt(loss)}</strong></div><div class="small muted">Median base: $${fmt(medianBase)}</div>`;
    const baseR = store.payouts.base || 26;
    const reqOne = loss / (baseR - 1);
    html += `<div style="margin-top:8px">To recover in one win at base payout (${fmt(baseR)}x): bet $${fmt(reqOne)} (estimate).</div>`;
    box.innerHTML = html;
  }

  // settings: render payouts and balls
  function renderBallSettings(){
    const container = $('#ballSettings'); container.innerHTML = '';
    // ensure default balls exist
    defaultBalls.forEach(def => { if(!store.balls.find(b=>b.name===def.name)) store.balls.unshift(Object.assign({},def)); });

    // payout editor
    const payoutHtml = document.createElement('div');
    payoutHtml.style.marginBottom = '8px';
    payoutHtml.innerHTML = `<div style="display:flex;gap:8px;align-items:center;margin-bottom:8px">
      <div style="width:200px"><label>Base payout (x $1)</label><input id="payout_base" type="number" step="0.01" value="${fmt(store.payouts.base)}" /></div>
      <div style="width:200px"><label>SureCall payout (x $1)</label><input id="payout_sure" type="number" step="0.01" value="${fmt(store.payouts.surecall)}" /></div>
    </div>`;
    container.appendChild(payoutHtml);

    store.balls.forEach(b=>{
      const row = document.createElement('div'); row.className='ball-row';
      row.innerHTML = `<input type="text" data-id="${b.id}" class="ball-name" value="${b.name}" /> <input type="number" step="0.01" class="ball-payout" data-id="${b.id}" value="${fmt(b.payout)}" style="width:120px" /> <button class="btn ghost del-ball" data-id="${b.id}">Delete</button>`;
      container.appendChild(row);
    });

    $$('.del-ball').forEach(btn=> btn.addEventListener('click', ()=>{
      const id = btn.dataset.id;
      if(defaultBalls.find(d=>d.id===id)) return alert('Default ball cannot be deleted; edit payout instead.');
      if(!confirm('Delete this custom ball?')) return;
      store.balls = store.balls.filter(bb=>bb.id!==id);
      save(); renderBallSettings(); renderWinningBalls();
    }));
  }

  $('#addBallBtnSettings').addEventListener('click', ()=>{
    const name = $('#newBallName').value.trim(); const p = parseFloat($('#newBallPayout').value) || 0;
    if(!name) return $('#settingsMsg').textContent = 'Enter ball name';
    if(p <= 0) return $('#settingsMsg').textContent = 'Enter valid payout';
    const id = 'b_' + Date.now();
    store.balls.push({ id, name, payout: p });
    $('#newBallName').value=''; $('#newBallPayout').value='';
    save(); renderBallSettings(); renderWinningBalls(); $('#settingsMsg').textContent='Added';
  });

  $('#applyBallChanges').addEventListener('click', ()=>{
    const baseVal = parseFloat($('#payout_base').value) || store.payouts.base;
    const sureVal = parseFloat($('#payout_sure').value) || store.payouts.surecall;
    store.payouts.base = baseVal; store.payouts.surecall = sureVal;
    $$('.ball-name').forEach(inp=>{ const id=inp.dataset.id; const b=store.balls.find(x=>x.id===id); if(b) b.name = inp.value.trim(); });
    $$('.ball-payout').forEach(inp=>{ const id=inp.dataset.id; const b=store.balls.find(x=>x.id===id); if(b) b.payout = parseFloat(inp.value)||b.payout; });
    save(); renderBallSettings(); renderWinningBalls(); $('#settingsMsg').textContent='Applied';
  });

  // exports
  $('#exportDrawCsv').addEventListener('click', ()=>{
    const date = $('#entry_date').value; const draw = $('#entry_draw').value;
    if(!date) return alert('Pick date');
    const target = store.draws.find(d=>d.date===date && d.draw===draw);
    if(!target) return alert('No saved draw bets for this date/draw');
    const rows = [['Number','Base','Balls','Tickets','Notes','TotalWager','TotalWin','PL']];
    target.bets.forEach(b=>{
      rows.push([b.number, fmt(b.base), Object.keys(b.balls||{}).map(k=>k+':'+fmt(b.balls[k])).join('|'), b.tickets, b.notes||'', fmt(b.totalWager||0), fmt(b.totalWin||0), fmt(b.pl||0)]);
    });
    const csv = rows.map(r=>r.map(c=>`"${String(c).replace(/"/g,'""')}"`).join(',')).join('\n');
    const blob=new Blob([csv],{type:'text/csv'}); const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download=`trini_draw_${date}_${draw}.csv`; a.click(); URL.revokeObjectURL(url);
  });

  $('#exportCsvAll').addEventListener('click', ()=>{
    const rows=[['Date','Draw','Number','Base','Balls','Tickets','TotalWager','TotalWin','PL']];
    store.draws.forEach(d=> d.bets.forEach(b=> rows.push([d.date,d.draw,b.number,fmt(b.base),Object.keys(b.balls||{}).map(k=>k+':'+fmt(b.balls[k])).join('|'),b.tickets,fmt(b.totalWager||0),fmt(b.totalWin||0),fmt(b.pl||0)])));
    const csv = rows.map(r=>r.map(c=>`"${String(c).replace(/"/g,'""')}"`).join(',')).join('\n');
    const blob=new Blob([csv],{type:'text/csv'}); const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download='trini_all_logs.csv'; a.click(); URL.revokeObjectURL(url);
  });

  $('#exportPngAll').addEventListener('click', ()=>{
    html2canvas(document.getElementById('app'),{scale:1.3}).then(canvas=>{ canvas.toBlob(blob=>{ const url=URL.createObjectURL(blob); const a=document.createElement('a'); a.href=url; a.download='trini_snapshot.png'; a.click(); URL.revokeObjectURL(url); },'image/png'); });
  });

  // filters
  $('#filterLogsBtn').addEventListener('click', ()=> renderLogs($('#log_from').value, $('#log_to').value));
  $('#runGlobal').addEventListener('click', ()=> renderAnalytics());

  // initial UI
  function initUI(){
    $('#slipsContainer').innerHTML = '';
    $('#addSlipBtn').addEventListener('click', ()=> {
      $('#slipsContainer').appendChild(createSlipElement({}, $('#slipsContainer').children.length));
      updateSlipsMsg();
    });
    $('#slipsContainer').appendChild(createSlipElement({}, 0));
    updateSlipsMsg();
  }

  function renderAll(){
    renderWinningBalls();
    renderBallSettings();
    renderLogs();
    renderAnalytics();
  }

  // start
  function init(){
    load();
    initUI();
    renderAll();
    show('entry');
  }
  init();

  // expose store for debugging if needed
  window.TriniFinBet = { store, save, load };
})();
</script>
</body>
</html>
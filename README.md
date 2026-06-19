# music
🎧 Sidetrack — a vibrant, full-featured mixtape player web app with accounts, theming, encrypted friend chat, listening parties, and generative (royalty-free) audio. Pure HTML/CSS/JS, no backend. A trial I was working on
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sidetrack — Mixtapes, Together</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,400;0,9..144,600;0,9..144,700;1,9..144,500&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<style>
  :root{
    --ink:#1A1025; --ink-soft:#241433; --ink-soft-2:#2E1A41;
    --cream:#FBEFFF; --cream-dim:#C9AFD9;
    --amber:#FF5E8A; --amber-dim:#C8456A; --mauve:#FFB454;
    --line: rgba(251,239,255,0.10); --line-strong: rgba(251,239,255,0.20);
  }
  *{box-sizing:border-box;}
  html,body{height:100%;}
  body{
    margin:0; min-height:100vh;
    background:
      radial-gradient(900px 520px at 88% -8%, color-mix(in srgb, var(--mauve) 22%, transparent), transparent 60%),
      radial-gradient(700px 420px at -5% 0%, color-mix(in srgb, var(--amber) 16%, transparent), transparent 55%),
      var(--ink);
    color:var(--cream); font-family:'Inter',sans-serif; -webkit-font-smoothing:antialiased;
    transition:background .4s ease, color .4s ease;
  }
  ::selection{ background:var(--amber); color:var(--ink); }
  button, input{ font-family:inherit; }
  .hidden{ display:none !important; }

  /* ===================== AUTH VIEW ===================== */
  #authView{
    min-height:100vh; display:flex; align-items:center; justify-content:center; padding:24px;
  }
  .auth-card{
    width:100%; max-width:380px; background:var(--ink-soft); border:1px solid var(--line);
    border-radius:18px; padding:32px 28px; box-shadow:0 30px 60px rgba(0,0,0,0.35);
  }
  .auth-logo{ font-family:'Fraunces',serif; font-weight:700; font-size:26px; text-align:center; margin-bottom:2px;}
  .auth-logo span{ color:var(--amber); font-style:italic; }
  .auth-sub{ text-align:center; font-size:12.5px; color:var(--cream-dim); margin-bottom:24px; }
  .auth-tabs{ display:flex; background:var(--ink-soft-2); border-radius:10px; padding:3px; margin-bottom:20px; }
  .auth-tab{
    flex:1; text-align:center; padding:8px; border-radius:8px; font-size:13px; cursor:pointer;
    color:var(--cream-dim); font-weight:600; transition:.15s;
  }
  .auth-tab.active{ background:var(--amber); color:var(--ink); }
  .field{ margin-bottom:14px; }
  .field label{ display:block; font-size:11px; letter-spacing:.06em; text-transform:uppercase; color:var(--cream-dim); margin-bottom:6px; }
  .field input{
    width:100%; background:var(--ink-soft-2); border:1px solid var(--line); border-radius:9px;
    padding:11px 12px; color:var(--cream); font-size:13.5px; outline:none; transition:border-color .15s;
  }
  .field input:focus{ border-color:var(--amber); }
  .btn-primary{
    width:100%; background:var(--amber); color:var(--ink); border:none; border-radius:10px;
    padding:12px; font-size:14px; font-weight:700; cursor:pointer; margin-top:6px; transition:transform .12s, opacity .15s;
  }
  .btn-primary:hover{ transform:translateY(-1px); }
  .auth-msg{ font-size:12px; color:#FF8A8A; margin-top:10px; min-height:14px; }
  .auth-note{ font-size:10.5px; color:var(--cream-dim); margin-top:18px; line-height:1.6; text-align:center; }

  /* ===================== APP SHELL ===================== */
  #appView{ display:grid; grid-template-columns:248px 1fr; min-height:100vh; padding-bottom:104px; }

  .sidebar{ border-right:1px solid var(--line); padding:24px 18px; display:flex; flex-direction:column; gap:22px; }
  .brand{ display:flex; align-items:baseline; gap:8px; }
  .brand .mark{ font-family:'Fraunces',serif; font-weight:700; font-size:21px; }
  .brand .mark span{ color:var(--amber); font-style:italic; }
  .nav-label{ font-size:10.5px; letter-spacing:.14em; text-transform:uppercase; color:var(--cream-dim); margin-bottom:4px; }
  .playlist-list{ display:flex; flex-direction:column; gap:2px; }
  .playlist-item{
    display:flex; align-items:center; gap:10px; padding:9px 10px; border-radius:9px; cursor:pointer;
    font-size:13.5px; color:var(--cream-dim); border:1px solid transparent; transition:.15s;
  }
  .playlist-item:hover{ background:var(--ink-soft); color:var(--cream); }
  .playlist-item.active{ background:var(--ink-soft-2); border-color:var(--line-strong); color:var(--cream); }
  .playlist-item .ic{ font-size:15px; width:18px; text-align:center; }
  .playlist-item .ct{ margin-left:auto; font-family:'JetBrains Mono',monospace; font-size:10.5px; color:var(--cream-dim); }

  .side-action{
    display:flex; align-items:center; gap:10px; padding:9px 10px; border-radius:9px; cursor:pointer;
    font-size:13px; color:var(--cream-dim); background:var(--ink-soft); transition:.15s;
  }
  .side-action:hover{ color:var(--cream); background:var(--ink-soft-2); }

  .sidebar-foot{ margin-top:auto; border-top:1px solid var(--line); padding-top:14px; display:flex; align-items:center; gap:10px; }
  .avatar{
    width:32px; height:32px; border-radius:50%; background:linear-gradient(135deg,var(--amber),var(--mauve));
    display:flex; align-items:center; justify-content:center; font-weight:700; font-size:13px; color:var(--ink); flex:none;
  }
  .who{ min-width:0; }
  .who .uname{ font-size:12.5px; font-weight:600; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  .premium-pill{
    font-size:9.5px; font-weight:700; letter-spacing:.04em; padding:1px 6px; border-radius:5px;
    background:var(--ink-soft-2); color:var(--cream-dim); margin-top:2px; display:inline-block;
  }
  .premium-pill.on{ background:var(--amber); color:var(--ink); }
  .icon-btn{
    background:none; border:none; color:var(--cream-dim); cursor:pointer; font-size:15px; padding:4px;
    border-radius:6px; transition:.15s;
  }
  .icon-btn:hover{ color:var(--cream); background:var(--ink-soft-2); }

  /* ===================== MAIN ===================== */
  .main{ padding:26px 34px 16px; min-width:0; }
  .topbar{ display:flex; align-items:center; gap:14px; margin-bottom:24px; }
  .topbar h1{ font-family:'Fraunces',serif; font-weight:700; font-size:26px; margin:0; }
  .topbar .sub{ font-size:12px; color:var(--cream-dim); margin-top:2px; }
  .top-right{ margin-left:auto; display:flex; align-items:center; gap:8px; }
  .search{ position:relative; width:230px; }
  .search input{
    width:100%; background:var(--ink-soft); border:1px solid var(--line); border-radius:9px;
    padding:9px 12px 9px 32px; color:var(--cream); font-size:13px; outline:none; transition:border-color .15s;
  }
  .search input:focus{ border-color:var(--amber-dim); }
  .search::before{ content:"⌕"; position:absolute; left:11px; top:50%; transform:translateY(-50%); color:var(--cream-dim); font-size:14px; }
  .upgrade-pill{
    background:linear-gradient(120deg,var(--amber),var(--mauve)); color:var(--ink); border:none; border-radius:8px;
    padding:8px 13px; font-size:12px; font-weight:700; cursor:pointer; white-space:nowrap;
  }

  .yt-card{
    display:flex; align-items:center; justify-content:space-between; gap:12px; background:var(--ink-soft);
    border:1px solid var(--line); border-radius:11px; padding:13px 16px; margin-bottom:16px;
  }
  .yt-card .yt-txt{ font-size:13px; color:var(--cream-dim); }
  .yt-card .yt-txt b{ color:var(--cream); }
  .yt-go{
    background:#FF0033; color:#fff; border:none; border-radius:8px; padding:8px 14px; font-size:12.5px;
    font-weight:700; cursor:pointer; white-space:nowrap; flex:none;
  }

  .track-table{ width:100%; border-collapse:collapse; }
  .track-table thead th{
    text-align:left; font-size:10.5px; letter-spacing:.1em; text-transform:uppercase; color:var(--cream-dim);
    font-weight:500; padding:0 10px 10px; border-bottom:1px solid var(--line);
  }
  .track-table thead th.num{ width:30px; } .track-table thead th.dur{ width:54px; text-align:right; }
  .track-table thead th.like, .track-table thead th.dl, .track-table thead th.shr{ width:30px; }
  .track-row{ cursor:pointer; transition:background .12s ease; }
  .track-row:hover{ background:var(--ink-soft); }
  .track-row.playing{ background:var(--ink-soft-2); }
  .track-row td{ padding:9px 10px; border-bottom:1px solid var(--line); vertical-align:middle; }
  .idxcell{ font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--cream-dim); width:30px; }
  .track-row.playing .idxcell{ color:var(--amber); }
  .idxcell .eq{ display:none; gap:2px; align-items:flex-end; height:13px; }
  .track-row.playing .idxcell .nm{ display:none; }
  .track-row.playing .idxcell .eq{ display:flex; }
  .eqbar{ width:2.5px; background:var(--amber); border-radius:1px; animation:eq .85s ease-in-out infinite; }
  .eqbar:nth-child(1){ height:5px; } .eqbar:nth-child(2){ height:11px; animation-delay:.15s; } .eqbar:nth-child(3){ height:7px; animation-delay:.3s; }
  @keyframes eq{ 0%,100%{transform:scaleY(.4);} 50%{transform:scaleY(1);} }
  .titlecell{ display:flex; align-items:center; gap:11px; }
  .art{ width:34px; height:34px; border-radius:6px; flex:none; box-shadow:inset 0 0 0 1px rgba(255,255,255,.08); }
  .meta .ttl{ font-size:13.5px; font-weight:600; } .meta .art-name{ font-size:11.5px; color:var(--cream-dim); margin-top:1px; }
  .durcell{ font-family:'JetBrains Mono',monospace; font-size:12px; color:var(--cream-dim); text-align:right; }
  .iconcell{ text-align:center; }
  .heart, .dlbtn, .shrbtn{ background:none; border:none; cursor:pointer; font-size:14px; color:var(--cream-dim); transition:.15s; padding:2px; }
  .heart.liked{ color:var(--amber); }
  .heart:hover, .dlbtn:hover, .shrbtn:hover{ transform:scale(1.15); color:var(--cream); }
  .dlbtn.locked{ opacity:.5; }
  .empty{ padding:50px 10px; text-align:center; color:var(--cream-dim); font-size:13.5px; }

  /* ===================== PLAYER BAR ===================== */
  .player{
    position:fixed; left:0; right:0; bottom:0; height:88px;
    background:color-mix(in srgb, var(--ink) 90%, transparent); backdrop-filter:blur(10px);
    border-top:1px solid var(--line); display:grid; grid-template-columns:280px 1fr 280px;
    align-items:center; padding:0 20px; gap:16px; z-index:10;
  }
  .deck{ display:flex; align-items:center; gap:12px; min-width:0; }
  .cassette{ width:58px; height:40px; border-radius:5px; flex:none; position:relative;
    background:linear-gradient(160deg, color-mix(in srgb, var(--ink-soft-2) 90%, white 4%), var(--ink)); box-shadow:inset 0 0 0 1px rgba(255,255,255,.07), 0 4px 10px rgba(0,0,0,.35); }
  .cassette .window{ position:absolute; left:7px; right:7px; top:8px; height:16px; background:rgba(0,0,0,.45); border-radius:3px; display:flex; align-items:center; justify-content:space-around; }
  .reel{ width:12px; height:12px; border-radius:50%; border:2px dashed var(--amber-dim); background:radial-gradient(circle, var(--ink-soft) 0 30%, transparent 31%); }
  .reel.spin{ animation:spin 2.2s linear infinite; } @keyframes spin{ to{transform:rotate(360deg);} }
  .cassette .label{ position:absolute; left:7px; right:7px; bottom:4px; height:8px; background:var(--cream); border-radius:2px; opacity:.85; }
  .deck-meta{ min-width:0; } .deck-meta .ttl{ font-size:13px; font-weight:700; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  .deck-meta .art-name{ font-size:11px; color:var(--cream-dim); white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  .transport{ display:flex; flex-direction:column; align-items:center; gap:7px; }
  .ctrl-row{ display:flex; align-items:center; gap:15px; }
  .ctrl-btn{ background:none; border:none; color:var(--cream-dim); cursor:pointer; font-size:15px; padding:4px; transition:.15s; }
  .ctrl-btn:hover{ color:var(--cream); } .ctrl-btn.on{ color:var(--amber); }
  .play-btn{ width:33px; height:33px; border-radius:50%; background:var(--amber); color:var(--ink); display:flex; align-items:center; justify-content:center; font-size:12px; border:none; cursor:pointer; transition:transform .12s; }
  .play-btn:hover{ transform:scale(1.06); }
  .progress-row{ display:flex; align-items:center; gap:8px; width:100%; max-width:500px; }
  .tcount{ font-family:'JetBrains Mono',monospace; font-size:10.5px; color:var(--cream-dim); width:34px; } .tcount.right{ text-align:right; }
  .bar{ flex:1; height:4px; border-radius:2px; background:var(--ink-soft-2); position:relative; cursor:pointer; }
  .bar .fill{ position:absolute; left:0; top:0; bottom:0; border-radius:2px; background:var(--amber); width:0%; }
  .bar .knob{ position:absolute; top:50%; width:9px; height:9px; border-radius:50%; background:var(--cream); transform:translate(-50%,-50%); left:0%; box-shadow:0 0 0 2px rgba(0,0,0,.2); }
  .right-controls{ display:flex; align-items:center; gap:10px; justify-content:flex-end; }
  .vol{ display:flex; align-items:center; gap:6px; width:96px; }
  .vol input[type=range]{ -webkit-appearance:none; width:100%; height:3px; background:var(--ink-soft-2); border-radius:2px; outline:none; }
  .vol input[type=range]::-webkit-slider-thumb{ -webkit-appearance:none; width:10px; height:10px; border-radius:50%; background:var(--cream); cursor:pointer; }
  .party-btn{ background:var(--ink-soft-2); border:1px solid var(--line); color:var(--cream-dim); border-radius:7px; padding:6px 9px; font-size:11px; cursor:pointer; }
  .party-btn.on{ background:var(--amber); color:var(--ink); border-color:var(--amber); }

  /* ===================== MODALS / DRAWER ===================== */
  .overlay{ position:fixed; inset:0; background:rgba(0,0,0,.55); display:flex; align-items:center; justify-content:center; z-index:50; }
  .modal{ width:100%; max-width:420px; background:var(--ink-soft); border:1px solid var(--line); border-radius:16px; padding:26px; margin:16px; }
  .modal h2{ font-family:'Fraunces',serif; margin:0 0 6px; font-size:21px; }
  .modal p{ font-size:13px; color:var(--cream-dim); line-height:1.6; }
  .modal-close{ float:right; background:none; border:none; color:var(--cream-dim); font-size:18px; cursor:pointer; }

  .theme-grid{ display:grid; grid-template-columns:1fr 1fr; gap:10px; margin:16px 0; }
  .theme-swatch{ border-radius:11px; padding:14px; cursor:pointer; border:2px solid transparent; position:relative; }
  .theme-swatch.active{ border-color:var(--cream); }
  .theme-swatch .dots{ display:flex; gap:6px; margin-bottom:8px; }
  .theme-swatch .dot{ width:14px; height:14px; border-radius:50%; }
  .theme-swatch .tname{ font-size:12px; font-weight:700; color:#fff; }

  .chat-drawer{
    position:fixed; top:0; right:0; bottom:0; width:340px; background:var(--ink-soft); border-left:1px solid var(--line);
    z-index:40; display:flex; flex-direction:column; transform:translateX(100%); transition:transform .25s ease;
  }
  .chat-drawer.open{ transform:translateX(0); }
  .chat-head{ padding:18px; border-bottom:1px solid var(--line); display:flex; align-items:center; gap:10px; }
  .chat-head h3{ margin:0; font-family:'Fraunces',serif; font-size:17px; }
  .chat-body{ flex:1; display:flex; min-height:0; }
  .friend-list{ width:120px; border-right:1px solid var(--line); overflow-y:auto; padding:8px; }
  .friend-item{ padding:8px; border-radius:8px; font-size:12px; cursor:pointer; color:var(--cream-dim); margin-bottom:2px; }
  .friend-item:hover{ background:var(--ink-soft-2); } .friend-item.active{ background:var(--ink-soft-2); color:var(--cream); font-weight:600; }
  .add-friend{ padding:8px; border-top:1px solid var(--line); }
  .add-friend input{ width:100%; background:var(--ink-soft-2); border:1px solid var(--line); border-radius:7px; padding:6px 8px; color:var(--cream); font-size:11px; outline:none; margin-bottom:4px; }
  .add-friend button{ width:100%; background:var(--amber); color:var(--ink); border:none; border-radius:7px; padding:6px; font-size:11px; font-weight:700; cursor:pointer; }
  .thread{ flex:1; display:flex; flex-direction:column; min-width:0; }
  .msgs{ flex:1; overflow-y:auto; padding:12px; display:flex; flex-direction:column; gap:8px; }
  .msg{ max-width:85%; padding:8px 11px; border-radius:11px; font-size:12.5px; line-height:1.4; }
  .msg.me{ align-self:flex-end; background:var(--amber); color:var(--ink); }
  .msg.them{ align-self:flex-start; background:var(--ink-soft-2); }
  .msg.track-msg{ display:flex; align-items:center; gap:8px; cursor:pointer; }
  .msg.track-msg .ta{ width:26px; height:26px; border-radius:5px; flex:none; }
  .thread-empty{ margin:auto; color:var(--cream-dim); font-size:12px; text-align:center; padding:20px; }
  .thread-input{ display:flex; gap:6px; padding:10px; border-top:1px solid var(--line); }
  .thread-input input{ flex:1; background:var(--ink-soft-2); border:1px solid var(--line); border-radius:8px; padding:8px 10px; color:var(--cream); font-size:12.5px; outline:none; }
  .thread-input button{ background:var(--amber); color:var(--ink); border:none; border-radius:8px; padding:0 12px; font-weight:700; cursor:pointer; }
  .enc-note{ font-size:9.5px; color:var(--cream-dim); text-align:center; padding:6px; border-top:1px solid var(--line); }

  .toast{
    position:fixed; bottom:104px; left:50%; transform:translateX(-50%) translateY(20px); opacity:0;
    background:var(--ink-soft-2); border:1px solid var(--line-strong); color:var(--cream); padding:10px 18px;
    border-radius:10px; font-size:12.5px; z-index:60; pointer-events:none; transition:.25s;
  }
  .toast.show{ opacity:1; transform:translateX(-50%) translateY(0); }

  @media (max-width:880px){
    #appView{ grid-template-columns:1fr; }
    .sidebar{ flex-direction:row; align-items:center; padding:12px 14px; border-right:none; border-bottom:1px solid var(--line); overflow-x:auto; gap:12px; }
    .sidebar-foot{ display:none; }
    .playlist-list{ flex-direction:row; } .nav-label{ display:none; } .side-action{ white-space:nowrap; }
    .main{ padding:18px; } .search{ width:130px; } .upgrade-pill{ padding:7px 9px; }
    .player{ grid-template-columns:auto 1fr; padding:8px 12px; height:auto; row-gap:6px; }
    .deck-meta{ display:none; } .right-controls{ display:none; }
    .progress-row{ grid-column:1 / -1; max-width:none; }
    .chat-drawer{ width:100%; }
  }
</style>
</head>
<body>

<!-- ===================== AUTH VIEW ===================== -->
<div id="authView">
  <div class="auth-card">
    <div class="auth-logo">side<span>track</span></div>
    <div class="auth-sub">mixtapes, made together</div>
    <div class="auth-tabs">
      <div class="auth-tab active" data-tab="signin">Sign In</div>
      <div class="auth-tab" data-tab="signup">Sign Up</div>
    </div>

    <div id="signinForm">
      <div class="field"><label>Username</label><input id="siUser" type="text" autocomplete="username"></div>
      <div class="field"><label>Password</label><input id="siPass" type="password" autocomplete="current-password"></div>
      <button class="btn-primary" id="siBtn">Sign In</button>
    </div>
    <div id="signupForm" class="hidden">
      <div class="field"><label>Choose a username</label><input id="suUser" type="text" autocomplete="username"></div>
      <div class="field"><label>Choose a password</label><input id="suPass" type="password" autocomplete="new-password"></div>
      <button class="btn-primary" id="suBtn">Create Account</button>
    </div>
    <div class="auth-msg" id="authMsg"></div>
    <div class="auth-note">Demo account system — stored only in this browser (hashed locally), not a real production auth service. Don't reuse a real password.</div>
  </div>
</div>

<!-- ===================== APP VIEW ===================== -->
<div id="appView" class="hidden">
  <aside class="sidebar">
    <div class="brand"><div class="mark">side<span>track</span></div></div>
    <div>
      <div class="nav-label">Mixtapes</div>
      <div class="playlist-list" id="playlistList"></div>
    </div>
    <div>
      <div class="nav-label">Social</div>
      <div class="playlist-list">
        <div class="side-action" id="openChatBtn"><span class="ic">💬</span><span>Friends &amp; Chat</span></div>
        <div class="side-action" id="partyToggleSide"><span class="ic">📡</span><span>Listening Party</span></div>
      </div>
    </div>
    <div class="sidebar-foot">
      <div class="avatar" id="avatarInitial">?</div>
      <div class="who">
        <div class="uname" id="sidebarUsername">—</div>
        <span class="premium-pill" id="premiumPill">Free</span>
      </div>
      <button class="icon-btn" id="settingsBtn" title="Settings">⚙</button>
      <button class="icon-btn" id="logoutBtn" title="Log out">⏻</button>
    </div>
  </aside>

  <main class="main">
    <div class="topbar">
      <div>
        <h1 id="pageTitle">Lo-Fi Drive</h1>
        <div class="sub" id="pageSub">12 tracks</div>
      </div>
      <div class="top-right">
        <div class="search"><input id="searchInput" type="text" placeholder="Search tracks, artists…"></div>
        <button class="upgrade-pill hidden" id="upgradeTopBtn">✦ Go Premium</button>
      </div>
    </div>

    <div class="yt-card hidden" id="ytCard">
      <div class="yt-txt">Can't find <b id="ytQueryLabel"></b> in your library?</div>
      <button class="yt-go" id="ytGoBtn">▶ Find on YouTube</button>
    </div>

    <table class="track-table">
      <thead><tr>
        <th class="num">#</th><th>Title</th><th class="dur">Time</th>
        <th class="shr">Share</th><th class="dl">Download</th><th class="like"></th>
      </tr></thead>
      <tbody id="trackBody"></tbody>
    </table>
    <div class="empty hidden" id="emptyState">No tracks match your search.</div>
  </main>
</div>

<!-- Player bar -->
<div class="player hidden" id="playerBar">
  <div class="deck">
    <div class="cassette"><div class="window"><div class="reel" id="reelL"></div><div class="reel" id="reelR"></div></div><div class="label"></div></div>
    <div class="deck-meta"><div class="ttl" id="nowTitle">Pick a track to start</div><div class="art-name" id="nowArtist">—</div></div>
  </div>
  <div class="transport">
    <div class="ctrl-row">
      <button class="ctrl-btn" id="shuffleBtn" title="Shuffle">⤨</button>
      <button class="ctrl-btn" id="prevBtn" title="Previous">⏮</button>
      <button class="play-btn" id="playBtn" title="Play / Pause">▶</button>
      <button class="ctrl-btn" id="nextBtn" title="Next">⏭</button>
      <button class="ctrl-btn" id="repeatBtn" title="Repeat">↻</button>
    </div>
    <div class="progress-row">
      <span class="tcount" id="curTime">0:00</span>
      <div class="bar" id="seekBar"><div class="fill" id="seekFill"></div><div class="knob" id="seekKnob"></div></div>
      <span class="tcount right" id="durTime">0:00</span>
    </div>
  </div>
  <div class="right-controls">
    <button class="party-btn" id="partyToggle">📡 Party</button>
    <div class="vol"><span style="font-size:12px;">🔉</span><input type="range" id="volSlider" min="0" max="100" value="65"></div>
  </div>
</div>

<!-- Settings modal -->
<div class="overlay hidden" id="settingsOverlay">
  <div class="modal">
    <button class="modal-close" id="settingsClose">✕</button>
    <h2>Settings</h2>
    <p>Pick a vibe. Saved to your account.</p>
    <div class="theme-grid" id="themeGrid"></div>
    <p style="margin-top:18px;font-size:11px;">Signed in as <b id="settingsUser"></b> · <span id="settingsPremiumLabel"></span></p>
  </div>
</div>

<!-- Upgrade modal -->
<div class="overlay hidden" id="upgradeOverlay">
  <div class="modal">
    <button class="modal-close" id="upgradeClose">✕</button>
    <h2>Go Premium</h2>
    <p>Premium unlocks track downloads (rendered as real .wav files from Sidetrack's generative engine) and unlimited listening parties.</p>
    <p style="font-size:11px;">This is a demo paywall — no real payment is processed. Clicking below just flips a local "premium" flag for this account.</p>
    <button class="btn-primary" id="activatePremiumBtn">Activate Premium (Demo)</button>
  </div>
</div>

<!-- Chat drawer -->
<div class="chat-drawer" id="chatDrawer">
  <div class="chat-head"><h3>Friends</h3><button class="icon-btn" id="chatCloseBtn" style="margin-left:auto;">✕</button></div>
  <div class="chat-body">
    <div class="friend-list" id="friendList"></div>
    <div class="thread" id="thread">
      <div class="thread-empty" id="threadEmpty">Add a friend by their username, then pick them to start chatting.</div>
      <div class="msgs hidden" id="msgs"></div>
      <div class="thread-input hidden" id="threadInputRow">
        <input id="msgInput" type="text" placeholder="Message…">
        <button id="sendMsgBtn">Send</button>
      </div>
    </div>
  </div>
  <div class="add-friend">
    <input id="addFriendInput" type="text" placeholder="Add friend by username">
    <button id="addFriendBtn">Add Friend</button>
  </div>
  <div class="enc-note">🔒 Messages are encrypted (AES-GCM) before being stored on this device.</div>
</div>

<div class="toast" id="toast"></div>

<script>
/* ===================== Themes ===================== */
const THEMES = {
  sunset:{ name:'Sunset Pop', bg:'#1A1025', bg2:'#241433', card:'#2E1A41', accent:'#FF5E8A', accentDim:'#C8456A', accent2:'#FFB454', text:'#FBEFFF', textDim:'#C9AFD9' },
  neon:{   name:'Neon Night', bg:'#0B0F19', bg2:'#121A2B', card:'#162038', accent:'#39E0C8', accentDim:'#23A793', accent2:'#FF3DCB', text:'#EAF6FF', textDim:'#8FA3C0' },
  citrus:{ name:'Citrus Punch', bg:'#1D2410', bg2:'#27300E', card:'#313D14', accent:'#D8FF3E', accentDim:'#A9C92E', accent2:'#FF8A3D', text:'#F7FFE0', textDim:'#B9C98F' },
  galaxy:{ name:'Galaxy', bg:'#0E0B22', bg2:'#161035', card:'#1F1846', accent:'#8A6CFF', accentDim:'#6A4FD1', accent2:'#36D2E0', text:'#ECE7FF', textDim:'#9C92C9' },
};
function applyTheme(key){
  const t = THEMES[key] || THEMES.sunset;
  const r = document.documentElement.style;
  r.setProperty('--ink', t.bg); r.setProperty('--ink-soft', t.bg2); r.setProperty('--ink-soft-2', t.card);
  r.setProperty('--cream', t.text); r.setProperty('--cream-dim', t.textDim);
  r.setProperty('--amber', t.accent); r.setProperty('--amber-dim', t.accentDim); r.setProperty('--mauve', t.accent2);
}

/* ===================== Track catalog (generative, royalty-free) ===================== */
const PLAYLISTS = [
  {id:'lofi',name:'Lo-Fi Drive',icon:'🚗'}, {id:'late',name:'Late Night',icon:'🌙'},
  {id:'study',name:'Study Beats',icon:'📚'}, {id:'throw',name:'Throwback Mix',icon:'📼'},
  {id:'all',name:'All Tracks',icon:'🗂️'},
];
const ARTISTS = ['Halden Vox','Mara Quist','The Lowtide','June Arbor','Static Pine','Coral Hause','Ennis Gray','Veda & The Tape','Rowan Field','Oslo Drip','Petra Lune','Dial Tone Six','Marsh Wren','The Echo Bins','Indigo Felt','Castor & Mae'];
const TITLES = ['Backroad Static','Porchlight Hum','Second Window','Slow Tape Loop','Velvet Curfew','Paper Moon Drive','Glasswork','Amber Hour','Low Beam','Quiet Static','After Hours Tint','Wax & Wane','Floor Lamp Glow','Soft Reel','Cassette Weather','Half Speed'];
function seeded(seed){ let s=seed%2147483647; if(s<=0) s+=2147483646; return ()=>{ s=(s*16807)%2147483647; return (s-1)/2147483646; }; }
function buildTracks(){
  const rows=[['lofi','minor'],['lofi','minor'],['lofi','major'],['lofi','minor'],['late','minor'],['late','minor'],['late','major'],
    ['study','major'],['study','major'],['study','minor'],['throw','major'],['throw','minor'],['throw','major'],['lofi','major'],['late','minor'],['study','major']];
  const hues={lofi:[330,75,68],late:[260,55,40],study:[170,55,45],throw:[28,80,55]};
  return rows.map((r,i)=>{
    const id=i+1, rand=seeded(id*97+13);
    const title=TITLES[i%TITLES.length], artist=ARTISTS[(id*3)%ARTISTS.length];
    const duration=150+Math.floor(rand()*90);
    const rootMidi=52+Math.floor(rand()*12);
    const [h,s,l]=hues[r[0]]; const hue2=(h+30+Math.floor(rand()*40))%360;
    return { id,title,artist,duration,playlist:r[0],scale:r[1],rootMidi,
      gradient:`linear-gradient(135deg, hsl(${h} ${s}% ${l}%), hsl(${hue2} ${s}% ${Math.max(l-12,22)}%))`, liked:false };
  });
}
const TRACKS = buildTracks();
function fmtTime(sec){ sec=Math.max(0,Math.floor(sec)); return Math.floor(sec/60)+':'+String(sec%60).padStart(2,'0'); }
function getTrack(id){ return TRACKS.find(t=>t.id===id); }
const SCALES = { major:[0,2,4,7,9,12,16], minor:[0,3,5,7,10,12,15] };
function midiToFreq(m){ return 440*Math.pow(2,(m-69)/12); }
function patternFor(track){
  const rand=seeded(track.id*31+7), degrees=SCALES[track.scale], pattern=[];
  for(let i=0;i<8;i++){ const deg=degrees[Math.floor(rand()*degrees.length)]; const dur=rand()>0.7?0.6:0.32;
    pattern.push({freq:midiToFreq(track.rootMidi+deg), dur}); }
  return pattern;
}

/* ===================== Storage helpers ===================== */
const LS = { get:(k,d)=>{ try{ return JSON.parse(localStorage.getItem(k)) ?? d; }catch(e){ return d; } },
             set:(k,v)=>localStorage.setItem(k, JSON.stringify(v)) };

function loadUsers(){ return LS.get('st_users', {}); }
function saveUsers(u){ LS.set('st_users', u); }
function prefsKey(u){ return 'st_prefs_'+u; }
function loadPrefs(u){ return LS.get(prefsKey(u), {theme:'sunset', premium:false, friends:[], liked:[]}); }
function savePrefs(u,p){ LS.set(prefsKey(u), p); }

async function sha256Hex(str){
  const data = new TextEncoder().encode(str);
  const hash = await crypto.subtle.digest('SHA-256', data);
  return [...new Uint8Array(hash)].map(b=>b.toString(16).padStart(2,'0')).join('');
}

/* ===================== App state ===================== */
let currentUsername = null;
let prefs = null;
const state = { activePlaylist:'lofi', query:'', currentTrackId:null, isPlaying:false, elapsed:0, shuffle:false, repeat:false, volume:0.65 };

function currentPrefs(){ return prefs; }

/* ===================== Audio engine ===================== */
let audioCtx=null, masterGain=null, stepTimer=null, progressTimer=null, playStartedAt=0, playStartedElapsed=0, stepIndex=0, currentPattern=[];
function ensureAudio(){
  if(!audioCtx){ audioCtx = new (window.AudioContext||window.webkitAudioContext)(); masterGain = audioCtx.createGain(); masterGain.gain.value = state.volume; masterGain.connect(audioCtx.destination); }
  if(audioCtx.state==='suspended') audioCtx.resume();
}
function playNote(freq,dur){
  const t0=audioCtx.currentTime, osc=audioCtx.createOscillator(), gain=audioCtx.createGain();
  osc.type='triangle'; osc.frequency.value=freq;
  gain.gain.setValueAtTime(0,t0); gain.gain.linearRampToValueAtTime(0.22,t0+0.03); gain.gain.exponentialRampToValueAtTime(0.0001,t0+dur);
  osc.connect(gain); gain.connect(masterGain); osc.start(t0); osc.stop(t0+dur+0.02);
}
function scheduleStep(){
  if(!state.isPlaying) return;
  const note=currentPattern[stepIndex%currentPattern.length];
  playNote(note.freq, note.dur*0.92);
  stepTimer=setTimeout(()=>{ stepIndex++; scheduleStep(); }, note.dur*1000);
}
function startStepLoop(track){ clearTimeout(stepTimer); currentPattern=patternFor(track); stepIndex=0; scheduleStep(); }
function stopStepLoop(){ clearTimeout(stepTimer); }

function visibleTracks(){
  let list = state.activePlaylist==='all' ? TRACKS.slice() : TRACKS.filter(t=>t.playlist===state.activePlaylist);
  if(state.query.trim()){ const q=state.query.toLowerCase(); list=list.filter(t=>t.title.toLowerCase().includes(q)||t.artist.toLowerCase().includes(q)); }
  return list;
}

function playTrack(id){
  ensureAudio();
  const track=getTrack(id); if(!track) return;
  state.currentTrackId=id; state.isPlaying=true; state.elapsed=0; playStartedAt=performance.now(); playStartedElapsed=0;
  startStepLoop(track); startProgressTimer();
  renderNowPlaying(); renderTrackRows(); updateTransportUI();
  broadcastParty();
}
function togglePlay(){
  if(!state.currentTrackId){ const list=visibleTracks(); if(list.length) playTrack(list[0].id); return; }
  ensureAudio(); state.isPlaying=!state.isPlaying;
  if(state.isPlaying){ playStartedAt=performance.now(); playStartedElapsed=state.elapsed; startStepLoop(getTrack(state.currentTrackId)); startProgressTimer(); }
  else { stopStepLoop(); stopProgressTimer(); }
  updateTransportUI(); renderTrackRows(); broadcastParty();
}
function startProgressTimer(){
  clearInterval(progressTimer);
  progressTimer=setInterval(()=>{
    const track=getTrack(state.currentTrackId); if(!track) return;
    state.elapsed = playStartedElapsed + (performance.now()-playStartedAt)/1000;
    if(state.elapsed>=track.duration){
      if(state.repeat){ playStartedAt=performance.now(); playStartedElapsed=0; state.elapsed=0; startStepLoop(track); }
      else { goNext(); return; }
    }
    updateProgressUI();
  },200);
}
function stopProgressTimer(){ clearInterval(progressTimer); }
function goNext(){
  const list=visibleTracks(); if(!list.length) return;
  const idx=list.findIndex(t=>t.id===state.currentTrackId);
  const nextIdx = state.shuffle ? Math.floor(Math.random()*list.length) : (idx+1)%list.length;
  playTrack(list[nextIdx].id);
}
function goPrev(){
  const list=visibleTracks(); if(!list.length) return;
  if(state.elapsed>4){ playTrack(state.currentTrackId); return; }
  const idx=list.findIndex(t=>t.id===state.currentTrackId);
  const prevIdx = state.shuffle ? Math.floor(Math.random()*list.length) : (idx-1+list.length)%list.length;
  playTrack(list[prevIdx].id);
}
function seekTo(frac){
  const track=getTrack(state.currentTrackId); if(!track) return;
  state.elapsed=Math.max(0,Math.min(track.duration-0.1, frac*track.duration));
  playStartedAt=performance.now(); playStartedElapsed=state.elapsed;
  if(state.isPlaying) startStepLoop(track);
  updateProgressUI();
}

/* ----- WAV export (premium download of generative tracks) ----- */
function bufferToWav(abuffer){
  const numCh=abuffer.numberOfChannels, length=abuffer.length*numCh*2+44;
  const bufferArr=new ArrayBuffer(length), view=new DataView(bufferArr);
  let pos=0;
  function setU16(d){ view.setUint16(pos,d,true); pos+=2; }
  function setU32(d){ view.setUint32(pos,d,true); pos+=4; }
  setU32(0x46464952); setU32(length-8); setU32(0x45564157);
  setU32(0x20746d66); setU32(16); setU16(1); setU16(numCh);
  setU32(abuffer.sampleRate); setU32(abuffer.sampleRate*2*numCh); setU16(numCh*2); setU16(16);
  setU32(0x61746164); setU32(length-pos-4);
  const ch=[]; for(let i=0;i<numCh;i++) ch.push(abuffer.getChannelData(i));
  let offset=0;
  while(pos<length){ for(let i=0;i<numCh;i++){ let sample=Math.max(-1,Math.min(1,ch[i][offset]));
      sample=(sample<0?sample*32768:sample*32767)|0; view.setInt16(pos,sample,true); pos+=2; } offset++; }
  return new Blob([bufferArr], {type:'audio/wav'});
}
async function renderTrackToWav(track){
  const sampleRate=44100, duration=track.duration;
  const offlineCtx=new OfflineAudioContext(1, Math.ceil(sampleRate*duration), sampleRate);
  const pattern=patternFor(track);
  let t=0, idx=0;
  while(t<duration){
    const note=pattern[idx%pattern.length];
    const osc=offlineCtx.createOscillator(), gain=offlineCtx.createGain();
    osc.type='triangle'; osc.frequency.value=note.freq;
    gain.gain.setValueAtTime(0,t); gain.gain.linearRampToValueAtTime(0.22,t+0.03); gain.gain.exponentialRampToValueAtTime(0.0001,t+note.dur*0.92);
    osc.connect(gain); gain.connect(offlineCtx.destination); osc.start(t); osc.stop(t+note.dur);
    t+=note.dur; idx++;
  }
  const rendered=await offlineCtx.startRendering();
  return bufferToWav(rendered);
}
async function downloadTrack(track){
  if(!prefs.premium){ openUpgrade(); return; }
  showToast('Rendering '+track.title+'…');
  const blob=await renderTrackToWav(track);
  const url=URL.createObjectURL(blob);
  const a=document.createElement('a'); a.href=url; a.download=track.title.replace(/[^a-z0-9]+/gi,'_')+'.wav';
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
  showToast('Downloaded '+track.title+'.wav');
}

/* ----- Share link ----- */
function shareTrack(track){
  const url = location.origin + location.pathname + '?track=' + track.id;
  if(navigator.clipboard && navigator.clipboard.writeText){
    navigator.clipboard.writeText(url).then(()=>showToast('Link copied — send it to a friend')).catch(()=>fallbackCopy(url));
  } else fallbackCopy(url);
}
function fallbackCopy(text){
  const ta=document.createElement('textarea'); ta.value=text; document.body.appendChild(ta); ta.select();
  try{ document.execCommand('copy'); showToast('Link copied — send it to a friend'); }catch(e){ showToast('Copy this link: '+text); }
  ta.remove();
}

/* ----- Listening Party (same-browser, multi-tab demo via BroadcastChannel) ----- */
let partyOn=false, partyChannel=null;
function setupParty(){
  if(!('BroadcastChannel' in window)) return;
  partyChannel = new BroadcastChannel('sidetrack_party');
  partyChannel.onmessage = (e)=>{
    if(!partyOn) return;
    const {trackId, isPlaying, elapsed} = e.data;
    if(trackId && trackId!==state.currentTrackId){ playTrack(trackId); }
    state.isPlaying = isPlaying; state.elapsed = elapsed;
    if(isPlaying){ playStartedAt=performance.now(); playStartedElapsed=elapsed; startStepLoop(getTrack(state.currentTrackId)); startProgressTimer(); }
    else { stopStepLoop(); stopProgressTimer(); }
    updateTransportUI(); updateProgressUI();
  };
}
function broadcastParty(){
  if(!partyOn || !partyChannel) return;
  partyChannel.postMessage({ trackId: state.currentTrackId, isPlaying: state.isPlaying, elapsed: state.elapsed });
}
function toggleParty(){
  partyOn=!partyOn;
  document.getElementById('partyToggle').classList.toggle('on', partyOn);
  showToast(partyOn ? 'Listening Party on — open this page in another tab to sync' : 'Listening Party off');
}

/* ===================== Rendering ===================== */
const trackBodyEl=document.getElementById('trackBody'), emptyStateEl=document.getElementById('emptyState');
const pageTitleEl=document.getElementById('pageTitle'), pageSubEl=document.getElementById('pageSub');

function renderPlaylists(){
  const el=document.getElementById('playlistList');
  el.innerHTML = PLAYLISTS.map(p=>{
    const count = p.id==='all' ? TRACKS.length : TRACKS.filter(t=>t.playlist===p.id).length;
    return `<div class="playlist-item ${p.id===state.activePlaylist?'active':''}" data-pl="${p.id}"><span class="ic">${p.icon}</span><span>${p.name}</span><span class="ct">${count}</span></div>`;
  }).join('');
  el.querySelectorAll('.playlist-item').forEach(item=>item.addEventListener('click', ()=>{
    state.activePlaylist=item.dataset.pl; renderPlaylists(); renderHeader(); renderTrackRows();
  }));
}
function renderHeader(){
  const pl=PLAYLISTS.find(p=>p.id===state.activePlaylist);
  pageTitleEl.textContent=pl.name;
  const count=visibleTracks().length;
  pageSubEl.textContent = `${count} track${count===1?'':'s'}`;
  const ytCard=document.getElementById('ytCard');
  if(state.query.trim() && count===0){
    ytCard.classList.remove('hidden');
    document.getElementById('ytQueryLabel').textContent = `"${state.query}"`;
  } else if(state.query.trim()){
    ytCard.classList.remove('hidden');
    document.getElementById('ytQueryLabel').textContent = `"${state.query}"`;
  } else ytCard.classList.add('hidden');
}
document.getElementById('ytGoBtn').addEventListener('click', ()=>{
  const q=encodeURIComponent(state.query.trim());
  window.open('https://www.youtube.com/results?search_query='+q, '_blank', 'noopener');
});

function renderTrackRows(){
  const list=visibleTracks();
  emptyStateEl.classList.toggle('hidden', list.length>0);
  trackBodyEl.innerHTML = list.map((t,i)=>{
    const isPlaying = t.id===state.currentTrackId;
    const liked = prefs.liked.includes(t.id);
    const locked = !prefs.premium;
    return `<tr class="track-row ${isPlaying?'playing':''}" data-id="${t.id}">
      <td class="idxcell"><span class="nm">${i+1}</span><span class="eq"><span class="eqbar"></span><span class="eqbar"></span><span class="eqbar"></span></span></td>
      <td><div class="titlecell"><div class="art" style="background:${t.gradient}"></div><div class="meta"><div class="ttl">${t.title}</div><div class="art-name">${t.artist}</div></div></div></td>
      <td class="durcell">${fmtTime(t.duration)}</td>
      <td class="iconcell"><button class="shrbtn" data-share="${t.id}" title="Copy share link">🔗</button></td>
      <td class="iconcell"><button class="dlbtn ${locked?'locked':''}" data-dl="${t.id}" title="${locked?'Premium required':'Download .wav'}">${locked?'🔒':'⬇'}</button></td>
      <td class="iconcell"><button class="heart ${liked?'liked':''}" data-like="${t.id}">${liked?'♥':'♡'}</button></td>
    </tr>`;
  }).join('');

  trackBodyEl.querySelectorAll('.track-row').forEach(row=>row.addEventListener('click',(e)=>{
    if(e.target.closest('button')) return;
    const id=Number(row.dataset.id);
    if(id===state.currentTrackId) togglePlay(); else playTrack(id);
  }));
  trackBodyEl.querySelectorAll('[data-like]').forEach(btn=>btn.addEventListener('click',(e)=>{
    e.stopPropagation(); const id=Number(btn.dataset.like);
    const i=prefs.liked.indexOf(id);
    if(i>=0) prefs.liked.splice(i,1); else prefs.liked.push(id);
    savePrefs(currentUsername, prefs); renderTrackRows();
  }));
  trackBodyEl.querySelectorAll('[data-share]').forEach(btn=>btn.addEventListener('click',(e)=>{
    e.stopPropagation(); shareTrack(getTrack(Number(btn.dataset.share)));
  }));
  trackBodyEl.querySelectorAll('[data-dl]').forEach(btn=>btn.addEventListener('click',(e)=>{
    e.stopPropagation(); downloadTrack(getTrack(Number(btn.dataset.dl)));
  }));
}
function renderNowPlaying(){
  const track=getTrack(state.currentTrackId);
  document.getElementById('nowTitle').textContent = track?track.title:'Pick a track to start';
  document.getElementById('nowArtist').textContent = track?track.artist:'—';
  document.getElementById('durTime').textContent = track?fmtTime(track.duration):'0:00';
}
function updateTransportUI(){
  document.getElementById('playBtn').textContent = state.isPlaying?'❚❚':'▶';
  document.getElementById('reelL').classList.toggle('spin', state.isPlaying);
  document.getElementById('reelR').classList.toggle('spin', state.isPlaying);
  document.getElementById('shuffleBtn').classList.toggle('on', state.shuffle);
  document.getElementById('repeatBtn').classList.toggle('on', state.repeat);
}
function updateProgressUI(){
  const track=getTrack(state.currentTrackId); if(!track) return;
  const frac=Math.min(1, state.elapsed/track.duration);
  document.getElementById('seekFill').style.width=(frac*100)+'%';
  document.getElementById('seekKnob').style.left=(frac*100)+'%';
  document.getElementById('curTime').textContent=fmtTime(state.elapsed);
}

/* ===================== Toast ===================== */
let toastTimer=null;
function showToast(msg){
  const t=document.getElementById('toast'); t.textContent=msg; t.classList.add('show');
  clearTimeout(toastTimer); toastTimer=setTimeout(()=>t.classList.remove('show'), 2400);
}

/* ===================== Settings / theme modal ===================== */
function renderThemeGrid(){
  const grid=document.getElementById('themeGrid');
  grid.innerHTML = Object.entries(THEMES).map(([key,t])=>`
    <div class="theme-swatch ${prefs.theme===key?'active':''}" data-theme="${key}" style="background:linear-gradient(135deg, ${t.bg}, ${t.bg2})">
      <div class="dots"><span class="dot" style="background:${t.accent}"></span><span class="dot" style="background:${t.accent2}"></span></div>
      <div class="tname">${t.name}</div>
    </div>`).join('');
  grid.querySelectorAll('.theme-swatch').forEach(sw=>sw.addEventListener('click', ()=>{
    prefs.theme = sw.dataset.theme; savePrefs(currentUsername, prefs); applyTheme(prefs.theme); renderThemeGrid();
  }));
}
document.getElementById('settingsBtn').addEventListener('click', ()=>{
  document.getElementById('settingsUser').textContent = currentUsername;
  document.getElementById('settingsPremiumLabel').textContent = prefs.premium ? 'Premium account' : 'Free account';
  renderThemeGrid();
  document.getElementById('settingsOverlay').classList.remove('hidden');
});
document.getElementById('settingsClose').addEventListener('click', ()=>document.getElementById('settingsOverlay').classList.add('hidden'));

/* ===================== Upgrade modal ===================== */
function openUpgrade(){ document.getElementById('upgradeOverlay').classList.remove('hidden'); }
document.getElementById('upgradeClose').addEventListener('click', ()=>document.getElementById('upgradeOverlay').classList.add('hidden'));
document.getElementById('upgradeTopBtn').addEventListener('click', openUpgrade);
document.getElementById('activatePremiumBtn').addEventListener('click', ()=>{
  prefs.premium=true; savePrefs(currentUsername, prefs);
  document.getElementById('upgradeOverlay').classList.add('hidden');
  refreshPremiumUI(); renderTrackRows();
  showToast('Premium activated (demo) ✦');
});
function refreshPremiumUI(){
  const pill=document.getElementById('premiumPill');
  pill.textContent = prefs.premium ? 'Premium' : 'Free';
  pill.classList.toggle('on', prefs.premium);
  document.getElementById('upgradeTopBtn').classList.toggle('hidden', prefs.premium);
}

/* ===================== Chat (encrypted local) ===================== */
let activeFriend = null;
async function deriveConvKey(convId){
  const enc=new TextEncoder();
  const baseKey = await crypto.subtle.importKey('raw', enc.encode(convId+'::sidetrack-demo'), {name:'PBKDF2'}, false, ['deriveKey']);
  return crypto.subtle.deriveKey({name:'PBKDF2', salt:enc.encode('st-salt-'+convId), iterations:100000, hash:'SHA-256'}, baseKey, {name:'AES-GCM', length:256}, false, ['encrypt','decrypt']);
}
async function encryptPayload(key, obj){
  const iv=crypto.getRandomValues(new Uint8Array(12));
  const data=new TextEncoder().encode(JSON.stringify(obj));
  const ct=await crypto.subtle.encrypt({name:'AES-GCM', iv}, key, data);
  return { iv:btoa(String.fromCharCode(...iv)), ct:btoa(String.fromCharCode(...new Uint8Array(ct))) };
}
async function decryptPayload(key, payload){
  const iv=Uint8Array.from(atob(payload.iv), c=>c.charCodeAt(0));
  const ct=Uint8Array.from(atob(payload.ct), c=>c.charCodeAt(0));
  const data=await crypto.subtle.decrypt({name:'AES-GCM', iv}, key, ct);
  return JSON.parse(new TextDecoder().decode(data));
}
function convId(a,b){ return [a,b].sort().join('__'); }
function chatKey(cid){ return 'st_chat_'+cid; }

function renderFriendList(){
  const el=document.getElementById('friendList');
  el.innerHTML = prefs.friends.map(f=>`<div class="friend-item ${f===activeFriend?'active':''}" data-friend="${f}">${f}</div>`).join('') || `<div style="font-size:11px;color:var(--cream-dim);padding:8px;">No friends yet</div>`;
  el.querySelectorAll('.friend-item').forEach(it=>it.addEventListener('click', ()=>{ activeFriend=it.dataset.friend; renderFriendList(); openThread(activeFriend); }));
}
async function openThread(friend){
  document.getElementById('threadEmpty').classList.add('hidden');
  document.getElementById('msgs').classList.remove('hidden');
  document.getElementById('threadInputRow').classList.remove('hidden');
  await renderThread(friend);
}
async function renderThread(friend){
  const cid=convId(currentUsername, friend);
  const key=await deriveConvKey(cid);
  const raw=LS.get(chatKey(cid), []);
  const msgsEl=document.getElementById('msgs');
  msgsEl.innerHTML='';
  for(const m of raw){
    let payload; try{ payload = await decryptPayload(key, m); }catch(e){ continue; }
    const mine = m.from===currentUsername;
    if(payload.type==='track'){
      const track=getTrack(payload.trackId);
      const div=document.createElement('div');
      div.className='msg track-msg '+(mine?'me':'them');
      div.innerHTML = track ? `<div class="ta" style="background:${track.gradient}"></div><div><b>${track.title}</b><br><span style="opacity:.8;font-size:11px;">${track.artist}</span></div>` : 'Shared a track';
      if(track) div.addEventListener('click', ()=>playTrack(track.id));
      msgsEl.appendChild(div);
    } else {
      const div=document.createElement('div'); div.className='msg '+(mine?'me':'them'); div.textContent=payload.text;
      msgsEl.appendChild(div);
    }
  }
  msgsEl.scrollTop = msgsEl.scrollHeight;
}
async function sendMessage(friend, payload){
  const cid=convId(currentUsername, friend);
  const key=await deriveConvKey(cid);
  const enc=await encryptPayload(key, payload);
  const raw=LS.get(chatKey(cid), []);
  raw.push({ from:currentUsername, iv:enc.iv, ct:enc.ct, ts:Date.now() });
  LS.set(chatKey(cid), raw);
  await renderThread(friend);
}
document.getElementById('sendMsgBtn').addEventListener('click', async ()=>{
  const input=document.getElementById('msgInput');
  const text=input.value.trim();
  if(!text || !activeFriend) return;
  input.value='';
  await sendMessage(activeFriend, {type:'text', text});
});
document.getElementById('msgInput').addEventListener('keydown', (e)=>{ if(e.key==='Enter') document.getElementById('sendMsgBtn').click(); });
document.getElementById('addFriendBtn').addEventListener('click', ()=>{
  const input=document.getElementById('addFriendInput');
  const name=input.value.trim();
  if(!name) return;
  const users=loadUsers();
  if(!users[name]){ showToast('No account found with that username'); return; }
  if(name===currentUsername){ showToast("That's you!"); return; }
  if(!prefs.friends.includes(name)){ prefs.friends.push(name); savePrefs(currentUsername, prefs); }
  input.value=''; renderFriendList(); activeFriend=name; openThread(name);
  showToast('Added '+name);
});

document.getElementById('openChatBtn').addEventListener('click', ()=>{
  document.getElementById('chatDrawer').classList.add('open'); renderFriendList();
});
document.getElementById('chatCloseBtn').addEventListener('click', ()=>document.getElementById('chatDrawer').classList.remove('open'));

/* ===================== Controls wiring ===================== */
document.getElementById('playBtn').addEventListener('click', togglePlay);
document.getElementById('nextBtn').addEventListener('click', goNext);
document.getElementById('prevBtn').addEventListener('click', goPrev);
document.getElementById('shuffleBtn').addEventListener('click', ()=>{ state.shuffle=!state.shuffle; updateTransportUI(); });
document.getElementById('repeatBtn').addEventListener('click', ()=>{ state.repeat=!state.repeat; updateTransportUI(); });
document.getElementById('partyToggle').addEventListener('click', toggleParty);
document.getElementById('partyToggleSide').addEventListener('click', toggleParty);
const seekBar=document.getElementById('seekBar');
seekBar.addEventListener('click', (e)=>{ const r=seekBar.getBoundingClientRect(); seekTo((e.clientX-r.left)/r.width); });
document.getElementById('volSlider').addEventListener('input', (e)=>{ state.volume=Number(e.target.value)/100; if(masterGain) masterGain.gain.value=state.volume; });
document.getElementById('searchInput').addEventListener('input', (e)=>{ state.query=e.target.value; renderHeader(); renderTrackRows(); });
document.addEventListener('keydown', (e)=>{ if(e.code==='Space' && document.activeElement.tagName!=='INPUT'){ e.preventDefault(); togglePlay(); } });

/* ===================== Auth flow ===================== */
const tabSignin=document.querySelector('[data-tab="signin"]'), tabSignup=document.querySelector('[data-tab="signup"]');
tabSignin.addEventListener('click', ()=>{ tabSignin.classList.add('active'); tabSignup.classList.remove('active'); document.getElementById('signinForm').classList.remove('hidden'); document.getElementById('signupForm').classList.add('hidden'); document.getElementById('authMsg').textContent=''; });
tabSignup.addEventListener('click', ()=>{ tabSignup.classList.add('active'); tabSignin.classList.remove('active'); document.getElementById('signupForm').classList.remove('hidden'); document.getElementById('signinForm').classList.add('hidden'); document.getElementById('authMsg').textContent=''; });

document.getElementById('suBtn').addEventListener('click', async ()=>{
  const user=document.getElementById('suUser').value.trim();
  const pass=document.getElementById('suPass').value;
  const msg=document.getElementById('authMsg');
  if(!user || !pass){ msg.textContent='Enter a username and password.'; return; }
  const users=loadUsers();
  if(users[user]){ msg.textContent='That username is taken.'; return; }
  users[user] = { passHash: await sha256Hex(pass), createdAt: Date.now() };
  saveUsers(users);
  savePrefs(user, {theme:'sunset', premium:false, friends:[], liked:[]});
  msg.textContent=''; loginAs(user);
});
document.getElementById('siBtn').addEventListener('click', async ()=>{
  const user=document.getElementById('siUser').value.trim();
  const pass=document.getElementById('siPass').value;
  const msg=document.getElementById('authMsg');
  const users=loadUsers();
  if(!users[user]){ msg.textContent='No account with that username.'; return; }
  const hash=await sha256Hex(pass);
  if(users[user].passHash!==hash){ msg.textContent='Incorrect password.'; return; }
  msg.textContent=''; loginAs(user);
});
document.getElementById('logoutBtn').addEventListener('click', ()=>{
  localStorage.removeItem('st_session');
  stopStepLoop(); stopProgressTimer();
  location.reload();
});

function loginAs(user){
  currentUsername=user;
  prefs=loadPrefs(user);
  localStorage.setItem('st_session', user);
  document.getElementById('authView').classList.add('hidden');
  document.getElementById('appView').classList.remove('hidden');
  document.getElementById('playerBar').classList.remove('hidden');
  document.getElementById('sidebarUsername').textContent=user;
  document.getElementById('avatarInitial').textContent=user[0].toUpperCase();
  applyTheme(prefs.theme);
  refreshPremiumUI();
  renderPlaylists(); renderHeader(); renderTrackRows(); renderNowPlaying(); updateTransportUI(); updateProgressUI();
  setupParty();

  const params=new URLSearchParams(location.search);
  const sharedTrack=params.get('track');
  if(sharedTrack){ const t=getTrack(Number(sharedTrack)); if(t){ state.activePlaylist = t.playlist; renderPlaylists(); renderHeader(); renderTrackRows(); showToast('Opened shared track: '+t.title); } }
}

/* ===================== Boot ===================== */
(function boot(){
  const session = localStorage.getItem('st_session');
  if(session){
    const users=loadUsers();
    if(users[session]) loginAs(session);
  }
})();
</script>
</body>
</html>

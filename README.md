<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Visibility Score — Cloud Studios</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@500;600;700&family=Inter:wght@400;500;600&family=IBM+Plex+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root{
    --sky-deep:#5B9BD8;
    --sky:#7CBAE8;
    --sky-pale:#CFE6F7;
    --bg-base:#F4F9FC;
    --card:#FFFFFF;
    --card-tint: linear-gradient(180deg, #FFFFFF 0%, #F3F9FD 100%);
    --border:#E2EAF2;
    --border-soft:#EDF3F8;
    --navy:#0F1B2E;
    --slate:#48566C;
    --faint:#8A97AC;
    --accent:#4A8FD4;
    --red:#D6564A;
    --orange:#D98A2B;
    --green:#279A6B;
  }
  *{box-sizing:border-box; margin:0; padding:0;}
  html{scroll-behavior:smooth;}
  body{
    background:var(--bg-base);
    color:var(--navy);
    font-family:'Inter', sans-serif;
    min-height:100vh;
  }

  .page{display:none; min-height:100vh;}
  .page.on{display:block; animation:pagein .35s ease;}
  @keyframes pagein{from{opacity:0; transform:translateY(6px);} to{opacity:1; transform:translateY(0);}}

  /* ---- hero band ---- */
  .hero{
    background:linear-gradient(180deg, var(--sky-deep) 0%, var(--sky) 45%, var(--sky-pale) 85%, var(--bg-base) 100%);
    padding:36px 24px 90px;
  }
  .hero-inner{max-width:600px; margin:0 auto;}
  .topbar{
    display:flex; align-items:center; justify-content:space-between;
    margin-bottom:38px;
  }
  .brand{
    display:flex; align-items:center; gap:9px;
    font-family:'Space Grotesk', sans-serif; font-weight:600; font-size:15px;
    color:#0F2440;
  }
  .brand .mark{
    width:9px; height:9px; border-radius:3px;
    background:#0F2440;
  }
  .company-pill{
    font-family:'IBM Plex Mono', monospace; font-size:11.5px;
    color:#0F2440; background:rgba(255,255,255,0.45);
    border:1px solid rgba(255,255,255,0.6); border-radius:20px;
    padding:5px 12px;
  }
  .eyebrow-line{
    font-family:'IBM Plex Mono', monospace; font-size:11px; letter-spacing:.1em;
    text-transform:uppercase; color:rgba(15,36,64,0.65); margin-bottom:10px;
  }
  .score-row{display:flex; align-items:flex-end; gap:14px; flex-wrap:wrap;}
  .score-num{
    font-family:'Space Grotesk', sans-serif;
    font-size:76px; font-weight:700; line-height:1; letter-spacing:-0.03em;
    color:#0F2440;
  }
  .score-max{font-size:22px; color:rgba(15,36,64,0.55); font-weight:600; padding-bottom:6px;}
  .delta{
    display:inline-flex; align-items:center; gap:6px;
    font-family:'IBM Plex Mono', monospace; font-size:12.5px; font-weight:600;
    padding:6px 11px; border-radius:7px; margin:14px 0 6px;
    background:rgba(255,255,255,0.55); color:#A83A2E;
  }
  .score-sub{color:rgba(15,36,64,0.6); font-size:13.5px;}

  /* ---- content card ---- */
  .content-wrap{max-width:600px; margin:-56px auto 0; padding:0 24px 80px;}
  .card{
    background:var(--card-tint);
    border:1px solid var(--border);
    border-radius:24px;
    padding:8px 28px 28px;
    box-shadow:0 20px 44px -20px rgba(20,60,110,0.16);
  }

  .section-label{
    font-family:'IBM Plex Mono', monospace; font-size:11px;
    letter-spacing:.08em; text-transform:uppercase; color:var(--faint);
    margin:30px 0 6px; padding-top:4px;
    display:flex; align-items:center; gap:10px;
  }
  .section-label .rule{flex:1; height:2px; background:var(--accent); max-width:22px; border-radius:2px;}

  .driver{border-bottom:1px solid var(--border-soft);}
  .driver:last-child{border-bottom:none;}
  .driver-head{
    display:flex; align-items:center; gap:14px;
    padding:16px 4px; cursor:pointer;
    border-radius:10px;
    transition:background .15s ease;
  }
  .driver-head:hover{background:#F1F7FC;}
  .dot{width:8px; height:8px; border-radius:50%; flex-shrink:0;}
  .dot.red{background:var(--red);}
  .dot.orange{background:var(--orange);}
  .dot.green{background:var(--green);}
  .driver-body{flex:1; min-width:0;}
  .driver-title{font-size:14.5px; font-weight:600; margin-bottom:2px; color:var(--navy);}
  .driver-desc{font-size:13px; color:var(--slate); line-height:1.4;}
  .driver-impact{font-family:'IBM Plex Mono', monospace; font-size:13.5px; font-weight:600; flex-shrink:0;}
  .driver-impact.neg{color:var(--red);}
  .driver-impact.pos{color:var(--green);}
  .chev{color:var(--faint); font-size:11px; flex-shrink:0; transition:transform .2s ease;}
  .driver-head.expanded .chev{transform:rotate(90deg); color:var(--accent);}

  .driver-detail{max-height:0; overflow:hidden; transition:max-height .25s ease; padding-left:22px;}
  .driver-detail.show{max-height:300px;}
  .driver-detail-inner{padding:2px 4px 20px;}
  .why-text{font-size:13.5px; color:var(--slate); line-height:1.6; margin-bottom:14px;}

  .action-box{
    background:#F6FAFD; border:1px solid var(--border);
    border-left:3px solid var(--accent);
    border-radius:10px; padding:14px 16px;
  }
  .action-label{
    font-family:'IBM Plex Mono', monospace; font-size:10.5px; letter-spacing:.06em;
    text-transform:uppercase; color:var(--accent); margin-bottom:6px;
  }
  .action-text{font-size:13.5px; color:var(--navy); line-height:1.5; margin-bottom:12px;}
  .tag-row{display:flex; gap:6px;}
  .tag{font-family:'IBM Plex Mono', monospace; font-size:10.5px; font-weight:600; padding:3px 8px; border-radius:6px;}
  .tag.impact-high{background:#E3F5EC; color:var(--green);}
  .tag.impact-med{background:#FCEFDC; color:var(--orange);}
  .tag.effort-low{background:#E7F1FB; color:var(--accent);}
  .tag.effort-med{background:#EEF1F5; color:var(--slate);}

  .footer-link{margin-top:20px; padding-top:22px; border-top:1px solid var(--border-soft); display:flex; justify-content:center;}
  .clouds-btn{
    display:inline-flex; align-items:center; gap:8px;
    font-family:'Inter', sans-serif; font-size:13px; font-weight:600;
    color:var(--slate); background:none; border:none; cursor:pointer;
    padding:8px 4px; transition:color .15s ease;
  }
  .clouds-btn:hover{color:var(--accent);}
  .clouds-btn svg{opacity:.6; transition:opacity .15s ease, transform .15s ease;}
  .clouds-btn:hover svg{opacity:1; transform:translateY(2px);}

  /* ================= PAGE 2 ================= */
  .p2-hero{
    background:linear-gradient(180deg, var(--sky-deep) 0%, var(--sky) 55%, var(--bg-base) 100%);
    padding:36px 24px 60px;
  }
  .p2-inner{max-width:760px; margin:0 auto;}
  .back-btn{
    display:inline-flex; align-items:center; gap:7px;
    font-size:13px; font-weight:600; color:#0F2440; background:none; border:none; cursor:pointer;
    margin-bottom:34px; opacity:.85; transition:opacity .15s ease;
  }
  .back-btn:hover{opacity:1;}
  .bts-eyebrow{
    font-family:'IBM Plex Mono', monospace; font-size:11px; letter-spacing:.1em;
    text-transform:uppercase; color:rgba(15,36,64,0.65); margin-bottom:14px;
  }
  .bts-title{
    font-family:'Space Grotesk', sans-serif; font-size:40px; font-weight:700;
    letter-spacing:-0.02em; margin-bottom:16px; line-height:1.12; color:#0F2440;
  }
  .bts-lead{font-size:15.5px; color:rgba(15,36,64,0.72); line-height:1.6; max-width:560px;}

  .p2-content{max-width:760px; margin:-24px auto 0; padding:0 24px 90px;}
  .bts-card{
    background:var(--card-tint); border:1px solid var(--border); border-radius:24px;
    padding:40px 36px; box-shadow:0 20px 44px -22px rgba(20,60,110,0.14);
  }
  .bts-section{margin-bottom:44px;}
  .bts-section:last-child{margin-bottom:0;}
  .bts-num{font-family:'IBM Plex Mono', monospace; font-size:11.5px; color:var(--accent); margin-bottom:10px; letter-spacing:.04em;}
  .bts-h{font-size:19px; font-weight:700; margin-bottom:12px; font-family:'Space Grotesk', sans-serif; color:var(--navy);}
  .bts-p{font-size:14.5px; color:var(--slate); line-height:1.65; max-width:640px;}
  .bts-p + .bts-p{margin-top:12px;}
  .bts-p b{color:var(--navy); font-weight:600;}
  .divider{width:36px; height:3px; background:var(--accent); border-radius:2px; margin:6px 0 22px;}

  .options-grid{display:grid; grid-template-columns:1fr 1fr; gap:16px; margin-top:8px;}
  .opt-card{
    background:#FBFDFE; border:1px solid var(--border); border-radius:16px;
    padding:18px; position:relative; overflow:hidden;
  }
  .opt-card::before{
    content:'NOT CHOSEN'; position:absolute; top:14px; right:-30px;
    background:#F7E3E0; color:var(--red);
    font-family:'IBM Plex Mono', monospace; font-size:9px; font-weight:600; letter-spacing:.08em;
    padding:3px 32px; transform:rotate(35deg);
  }
  .opt-tag{font-family:'IBM Plex Mono', monospace; font-size:10.5px; color:var(--faint); letter-spacing:.05em; margin-bottom:8px;}
  .opt-title{font-size:15px; font-weight:700; margin-bottom:12px; color:var(--navy);}
  .mini-mock{background:var(--bg-base); border:1px solid var(--border); border-radius:10px; padding:12px; margin-bottom:12px;}
  .mini-score{font-family:'Space Grotesk',sans-serif; font-size:22px; font-weight:700; color:var(--navy);}
  .mini-max{font-size:11px; color:var(--faint);}
  .mini-row{display:flex; align-items:center; gap:6px; margin-top:7px;}
  .mini-dot{width:5px; height:5px; border-radius:50%; flex-shrink:0;}
  .mini-line{height:5px; flex:1; border-radius:3px; background:var(--border);}
  .opt-why{font-size:12.5px; color:var(--slate); line-height:1.55;}

  .cut-list{margin-top:4px;}
  .cut-item{display:flex; gap:12px; padding:14px 0; border-bottom:1px solid var(--border-soft);}
  .cut-item:last-child{border-bottom:none; padding-bottom:0;}
  .cut-x{font-family:'IBM Plex Mono', monospace; font-size:12px; color:var(--faint); flex-shrink:0; width:16px; padding-top:2px;}
  .cut-body b{font-size:13.5px; color:var(--navy); font-weight:700; display:block; margin-bottom:3px;}
  .cut-body span{font-size:13px; color:var(--slate); line-height:1.5;}

  @media (max-width:640px){
    .score-num{font-size:54px;}
    .bts-title{font-size:28px;}
    .options-grid{grid-template-columns:1fr;}
    .bts-card{padding:28px 22px;}
    .content-wrap{margin-top:-40px;}
  }
</style>
</head>
<body>

<!-- ============ PAGE 1: THE PROTOTYPE ============ -->
<div class="page on" id="page-main">

  <div class="hero">
    <div class="hero-inner">
      <div class="topbar">
        <div class="brand"><span class="mark"></span>Cloud Studios</div>
        <div class="company-pill">Acme Co</div>
      </div>
      <div class="eyebrow-line">AI Visibility Score</div>
      <div class="score-row">
        <span class="score-num">62</span>
        <span class="score-max">/ 100</span>
      </div>
      <div class="delta">↓ 8 pts this month</div>
      <div class="score-sub">Your visibility across ChatGPT, Perplexity, Gemini &amp; Claude</div>
    </div>
  </div>

  <div class="content-wrap">
    <div class="card">

      <div class="section-label"><span class="rule"></span>Why 62</div>

      <div class="driver">
        <div class="driver-head" onclick="toggleDriver(this,'d1')">
          <div class="dot red"></div>
          <div class="driver-body">
            <div class="driver-title">Content coverage</div>
            <div class="driver-desc">6 common questions go unanswered</div>
          </div>
          <div class="driver-impact neg">−14</div>
          <div class="chev">▸</div>
        </div>
        <div class="driver-detail" id="d1">
          <div class="driver-detail-inner">
            <div class="why-text">Models can only cite what exists. Right now, Acme Co has no page that directly answers "what is it," "what does it cost," or "how is it different" — so when someone asks an AI model those questions, you're simply not part of the answer.</div>
            <div class="action-box">
              <div class="action-label">Do this next</div>
              <div class="action-text">Write short, direct pages for your 3 most-asked questions.</div>
              <div class="tag-row">
                <span class="tag impact-high">HIGH IMPACT</span>
                <span class="tag effort-low">LOW EFFORT</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="driver">
        <div class="driver-head" onclick="toggleDriver(this,'d2')">
          <div class="dot orange"></div>
          <div class="driver-body">
            <div class="driver-title">Brand association</div>
            <div class="driver-desc">Inconsistent category linking</div>
          </div>
          <div class="driver-impact neg">−8</div>
          <div class="chev">▸</div>
        </div>
        <div class="driver-detail" id="d2">
          <div class="driver-detail-inner">
            <div class="why-text">When models are asked about your category, they don't reliably bring up Acme Co by name — your brand appears in only a fraction of the relevant answers we tracked.</div>
            <div class="action-box">
              <div class="action-label">Do this next</div>
              <div class="action-text">Strengthen how third-party sources describe your category positioning.</div>
              <div class="tag-row">
                <span class="tag impact-med">MEDIUM IMPACT</span>
                <span class="tag effort-med">MEDIUM EFFORT</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="driver">
        <div class="driver-head" onclick="toggleDriver(this,'d3')">
          <div class="dot green"></div>
          <div class="driver-body">
            <div class="driver-title">Citations</div>
            <div class="driver-desc">Strong presence on trusted sources</div>
          </div>
          <div class="driver-impact pos">+12</div>
          <div class="chev">▸</div>
        </div>
        <div class="driver-detail" id="d3">
          <div class="driver-detail-inner">
            <div class="why-text">This one is working in your favor. You're already cited by sources these models trust — it's currently the biggest thing protecting your score. Nothing to fix here.</div>
          </div>
        </div>
      </div>

      <div class="footer-link">
        <button class="clouds-btn" onclick="goPage('page-thinking')">
          Behind the clouds — how this screen got made
          <svg width="12" height="12" viewBox="0 0 12 12" fill="none"><path d="M2 4L6 8L10 4" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/></svg>
        </button>
      </div>

    </div>
  </div>
</div>

<!-- ============ PAGE 2: BEHIND THE CLOUDS ============ -->
<div class="page" id="page-thinking">

  <div class="p2-hero">
    <div class="p2-inner">
      <button class="back-btn" onclick="goPage('page-main')">
        <svg width="12" height="12" viewBox="0 0 12 12" fill="none"><path d="M8 2L4 6L8 10" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/></svg>
        Back to the prototype
      </button>
      <div class="bts-eyebrow">Behind the clouds</div>
      <div class="bts-title">The thinking behind this screen</div>
      <div class="bts-lead">A score is a status. It isn't a plan. Here's the problem I was solving, what I chose to build, what I chose not to, and why.</div>
    </div>
  </div>

  <div class="p2-content">
    <div class="bts-card">

      <div class="bts-section">
        <div class="bts-num">01 · The problem</div>
        <div class="divider"></div>
        <div class="bts-h">A number with no next step</div>
        <div class="bts-p">A user opens their dashboard and sees a single score — 62. That number raises two questions it doesn't answer: <b>why is it 62</b>, and <b>what do I do about it</b>. As it stands, the score works as a status indicator, but not as a tool for actually improving anything.</div>
      </div>

      <div class="bts-section">
        <div class="bts-num">02 · What I built, and why</div>
        <div class="divider"></div>
        <div class="bts-h">One screen where the score explains itself</div>
        <div class="bts-p">The score, its breakdown, and the recommended fix all live in the same place. Click a driver and the reasoning and the next action unfold right there — nothing navigates away, the score itself never moves.</div>
        <div class="bts-p">I chose this over a multi-screen flow because it matches how Cloud Studios' own products are built: one thing to do on the main screen, nothing you have to go looking for. If the score can't explain itself in place, it hasn't actually solved the problem — it's just relocated it to a second screen.</div>
      </div>

      <div class="bts-section">
        <div class="bts-num">03 · What I considered and cut</div>
        <div class="divider"></div>
        <div class="bts-h">Two other directions, both working, both rejected</div>
        <div class="options-grid">

          <div class="opt-card">
            <div class="opt-tag">TRACK A</div>
            <div class="opt-title">Minimal diagnosis card</div>
            <div class="mini-mock">
              <div class="mini-score">62<span class="mini-max"> /100</span></div>
              <div class="mini-row"><div class="mini-dot" style="background:var(--red)"></div><div class="mini-line" style="width:70%"></div></div>
              <div class="mini-row"><div class="mini-dot" style="background:var(--orange)"></div><div class="mini-line" style="width:50%"></div></div>
              <div class="mini-row"><div class="mini-dot" style="background:var(--green)"></div><div class="mini-line" style="width:35%"></div></div>
            </div>
            <div class="opt-why">Score + breakdown, static, no interaction. Clean, but it stops right where the user needs it most — there's no path from "here's the problem" to "here's what to do."</div>
          </div>

          <div class="opt-card">
            <div class="opt-tag">TRACK B</div>
            <div class="opt-title">Full 3-screen loop</div>
            <div class="mini-mock">
              <div style="display:flex; gap:4px; margin-bottom:9px;">
                <div style="flex:1; height:20px; border-radius:5px; background:#DCEBF9; border:1px solid var(--accent);"></div>
                <div style="flex:1; height:20px; border-radius:5px; background:var(--border);"></div>
                <div style="flex:1; height:20px; border-radius:5px; background:var(--border);"></div>
              </div>
              <div class="mini-row"><div class="mini-line" style="width:90%"></div></div>
              <div class="mini-row"><div class="mini-line" style="width:60%"></div></div>
            </div>
            <div class="opt-why">Dashboard → Opportunity → Action plan, each its own screen. The full "Measure → Explain → Prioritise → Act" story — but it makes the user leave the score to understand the score. Better as a client-facing report than a first screen.</div>
          </div>

        </div>
      </div>

      <div class="bts-section">
        <div class="bts-num">04 · What I left out, on purpose</div>
        <div class="divider"></div>
        <div class="bts-h">Cut from the version you're looking at</div>
        <div class="cut-list">
          <div class="cut-item">
            <div class="cut-x">—</div>
            <div class="cut-body"><b>A 90-day trend graph</b><span>Real and useful eventually, but this screen is about explaining <i>this</i> score. A chart would compete with the explanation for attention before the core loop even proves itself.</span></div>
          </div>
          <div class="cut-item">
            <div class="cut-x">—</div>
            <div class="cut-body"><b>One-click content generation</b><span>Tempting, but it turns a diagnostic screen into a content tool overnight. That's a much bigger, separate product decision — it deserves its own scoping, not a bolt-on button here.</span></div>
          </div>
          <div class="cut-item">
            <div class="cut-x">—</div>
            <div class="cut-body"><b>Per-platform breakdown (ChatGPT vs Gemini vs Claude vs Perplexity)</b><span>Genuinely useful, but it adds a second axis — driver × platform — before the first axis, driver → action, has even been validated with real users. One layer of explanation at a time.</span></div>
          </div>
        </div>
      </div>

    </div>
  </div>
</div>

<script>
  function toggleDriver(headEl, detailId){
    const detail = document.getElementById(detailId);
    const isOpen = detail.classList.contains('show');
    document.querySelectorAll('.driver-detail').forEach(d=>d.classList.remove('show'));
    document.querySelectorAll('.driver-head').forEach(h=>h.classList.remove('expanded'));
    if(!isOpen){
      detail.classList.add('show');
      headEl.classList.add('expanded');
    }
  }
  function goPage(id){
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('on'));
    document.getElementById(id).classList.add('on');
    window.scrollTo({top:0, behavior:'instant'});
  }
</script>
</body>
</html>

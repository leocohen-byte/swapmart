<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SwapMart - Driftline Live Demo</title>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
:root{--bg:#08080f;--surface:#0f0f1a;--surface2:#161625;--border:#1c1c2e;--border2:#252538;--text:#e4e4f0;--text2:#9898b8;--text3:#50507a;--accent:#5b6ef5;--accent2:#7c8cff;--green:#0fd99e;--red:#ff3d5a;--orange:#ff8533;--yellow:#ffc94d;--purple:#a78bfa}
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:'Syne',sans-serif;background:var(--bg);color:var(--text);min-height:100vh;overflow-x:hidden}
.hdr{display:flex;align-items:center;justify-content:space-between;padding:14px 28px;border-bottom:1px solid var(--border);background:rgba(8,8,15,.96);position:sticky;top:0;z-index:200;backdrop-filter:blur(10px)}
.hdr-l{display:flex;align-items:center;gap:16px}
.brand{font-size:19px;font-weight:800;letter-spacing:-.3px}
.demo-chip{font-family:'IBM Plex Mono',monospace;font-size:10px;background:rgba(91,110,245,.14);color:var(--accent2);border:1px solid rgba(91,110,245,.25);padding:4px 10px;border-radius:4px;letter-spacing:.08em}
.hdr-r{display:flex;align-items:center;gap:20px;font-family:'IBM Plex Mono',monospace;font-size:11px}
.live-badge{display:flex;align-items:center;gap:6px;color:var(--green)}
.pulse{width:6px;height:6px;background:var(--green);border-radius:50%;animation:blink 2s infinite}
@keyframes blink{0%,100%{opacity:1}50%{opacity:.3}}
.api-status{color:var(--text3)}
.api-status.ok{color:var(--green)}
.api-status.err{color:var(--orange)}
.layout{display:grid;grid-template-columns:1fr 360px 360px;height:calc(100vh - 57px)}
.panel{border-right:1px solid var(--border);display:flex;flex-direction:column;overflow:hidden}
.panel-hdr{padding:16px 22px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;flex-shrink:0}
.panel-title{font-size:11px;font-weight:700;color:var(--text2);text-transform:uppercase;letter-spacing:.14em}
.panel-meta{font-family:'IBM Plex Mono',monospace;font-size:11px;color:var(--text3)}
.listings-scroll{overflow-y:auto;flex:1;padding:14px}
.listing{background:var(--surface);border:1px solid var(--border);border-radius:12px;padding:14px 16px;margin-bottom:8px;cursor:pointer;transition:all .18s;position:relative}
.listing:hover{border-color:var(--border2)}
.listing.sel{border-color:var(--accent);background:rgba(91,110,245,.05)}
.listing.flagged{border-color:rgba(255,61,90,.35);background:rgba(255,61,90,.04)}
.listing.suspicious{border-color:rgba(255,133,51,.3)}
.l-top{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:6px}
.l-name{font-size:14px;font-weight:700}
.l-price{font-family:'IBM Plex Mono',monospace;font-size:13px;color:var(--green);font-weight:600}
.l-seller{font-family:'IBM Plex Mono',monospace;font-size:11px;color:var(--text3);display:flex;align-items:center;gap:5px;margin-bottom:5px}
.s-dot{width:5px;height:5px;border-radius:50%;background:var(--green);flex-shrink:0}
.s-dot.red{background:var(--red)}
.s-dot.orange{background:var(--orange)}
.l-desc{font-size:12px;color:var(--text2);line-height:1.4}
.risk-tag{position:absolute;top:10px;right:10px;font-family:'IBM Plex Mono',monospace;font-size:9px;font-weight:600;padding:3px 8px;border-radius:3px;text-transform:uppercase;letter-spacing:.06em}
.rt-clean{background:rgba(15,217,158,.1);color:var(--green);border:1px solid rgba(15,217,158,.2)}
.rt-medium{background:rgba(255,201,77,.1);color:var(--yellow);border:1px solid rgba(255,201,77,.2)}
.rt-high{background:rgba(255,133,51,.15);color:var(--orange);border:1px solid rgba(255,133,51,.25)}
.rt-critical{background:rgba(255,61,90,.15);color:var(--red);border:1px solid rgba(255,61,90,.3)}
.chat-wrap{display:flex;flex-direction:column;height:100%}
.chat-hdr{padding:14px 20px;border-bottom:1px solid var(--border);flex-shrink:0}
.chat-seller{font-size:14px;font-weight:700}
.chat-item{font-size:11px;color:var(--text3);font-family:'IBM Plex Mono',monospace;margin-top:2px}
.chat-placeholder{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:10px;color:var(--text3)}
.chat-placeholder-icon{font-size:36px;opacity:.25}
.chat-placeholder p{font-size:13px}
.chat-placeholder small{font-size:11px;font-family:'IBM Plex Mono',monospace;opacity:.6}
.msgs{flex:1;overflow-y:auto;padding:14px;display:flex;flex-direction:column;gap:8px}
.msg{display:flex;flex-direction:column;max-width:82%;animation:msgIn .2s ease}
@keyframes msgIn{from{opacity:0;transform:translateY(5px)}to{opacity:1;transform:translateY(0)}}
.msg.you{align-self:flex-end}
.msg.them{align-self:flex-start}
.bubble{padding:9px 13px;border-radius:11px;font-size:13px;line-height:1.45}
.msg.you .bubble{background:var(--accent);color:#fff;border-bottom-right-radius:3px}
.msg.them .bubble{background:var(--surface2);border:1px solid var(--border2);border-bottom-left-radius:3px}
.msg.them.bot-msg .bubble{border-color:rgba(255,61,90,.3);background:rgba(255,61,90,.05)}
.msg-time{font-family:'IBM Plex Mono',monospace;font-size:9px;color:var(--text3);margin-top:3px;padding:0 3px}
.msg.you .msg-time{text-align:right}
.input-row{padding:14px;border-top:1px solid var(--border);display:flex;gap:8px;flex-shrink:0}
.chat-in{flex:1;background:var(--surface2);border:1px solid var(--border2);border-radius:9px;padding:9px 13px;font-size:13px;color:var(--text);font-family:'Syne',sans-serif;outline:none;transition:border-color .2s}
.chat-in:focus{border-color:var(--accent)}
.send-btn{background:var(--accent);color:#fff;border:none;border-radius:9px;padding:9px 16px;font-size:13px;font-weight:700;cursor:pointer;font-family:'Syne',sans-serif}
.send-btn:hover{background:var(--accent2)}
.drift-hdr{padding:16px 22px;border-bottom:1px solid var(--border);background:rgba(91,110,245,.03);flex-shrink:0}
.drift-brand{display:flex;align-items:center;gap:8px;margin-bottom:4px}
.drift-dot{width:8px;height:8px;background:var(--accent);border-radius:50%;box-shadow:0 0 8px rgba(91,110,245,.7)}
.drift-name{font-size:15px;font-weight:800}
.drift-sub{font-family:'IBM Plex Mono',monospace;font-size:10px;color:var(--text3)}
.stats-row{display:grid;grid-template-columns:repeat(4,1fr);border-bottom:1px solid var(--border);flex-shrink:0}
.stat{padding:12px 14px;border-right:1px solid var(--border)}
.stat:last-child{border-right:none}
.stat-val{font-size:22px;font-weight:800;line-height:1}
.stat-label{font-family:'IBM Plex Mono',monospace;font-size:9px;color:var(--text3);text-transform:uppercase;letter-spacing:.1em;margin-top:3px}
.feed{flex:1;overflow-y:auto;padding:10px}
.feed-empty{text-align:center;padding:48px 20px}
.feed-empty p{font-family:'IBM Plex Mono',monospace;font-size:12px;color:var(--text3);line-height:1.7}
.event-card{border-radius:10px;padding:11px 13px;margin-bottom:7px;border:1px solid var(--border);background:var(--surface);animation:slideR .25s ease;position:relative;overflow:hidden}
@keyframes slideR{from{opacity:0;transform:translateX(12px)}to{opacity:1;transform:translateX(0)}}
.event-card::before{content:'';position:absolute;left:0;top:0;bottom:0;width:3px;border-radius:2px 0 0 2px}
.event-card.critical::before{background:var(--red)}
.event-card.high::before{background:var(--orange)}
.event-card.medium::before{background:var(--yellow)}
.event-card.low::before{background:var(--green)}
.event-card.critical{border-color:rgba(255,61,90,.25);background:rgba(255,61,90,.04)}
.event-card.high{border-color:rgba(255,133,51,.2)}
.ec-top{display:flex;align-items:center;justify-content:space-between;margin-bottom:5px}
.ec-user{font-family:'IBM Plex Mono',monospace;font-size:12px;font-weight:600}
.ec-score{font-family:'IBM Plex Mono',monospace;font-size:18px;font-weight:800}
.ec-score.critical{color:var(--red)}
.ec-score.high{color:var(--orange)}
.ec-score.medium{color:var(--yellow)}
.ec-score.low{color:var(--green)}
.ec-signals{font-size:11px;color:var(--text2);margin-bottom:5px;line-height:1.4}
.ec-rec{display:inline-block;font-family:'IBM Plex Mono',monospace;font-size:9px;padding:2px 8px;border-radius:3px;text-transform:uppercase;letter-spacing:.05em}
.ec-rec.critical{background:rgba(255,61,90,.15);color:var(--red)}
.ec-rec.high{background:rgba(255,133,51,.12);color:var(--orange)}
.ec-rec.medium{background:rgba(255,201,77,.1);color:var(--yellow)}
.ec-rec.low{background:rgba(15,217,158,.1);color:var(--green)}
.ec-time{font-family:'IBM Plex Mono',monospace;font-size:9px;color:var(--text3);margin-top:4px}
.ec-src{float:right;font-family:'IBM Plex Mono',monospace;font-size:9px}
.ec-src.live{color:var(--accent2)}
.ec-src.local{color:var(--text3)}
.controls{padding:12px 16px;border-top:1px solid var(--border);background:rgba(8,8,15,.8);flex-shrink:0}
.ctrl-label{font-family:'IBM Plex Mono',monospace;font-size:9px;color:var(--text3);text-transform:uppercase;letter-spacing:.1em;margin-bottom:8px}
.ctrl-btns{display:flex;gap:7px;flex-wrap:wrap}
.ctrl-btn{padding:7px 13px;border-radius:7px;font-size:12px;font-weight:700;cursor:pointer;border:1px solid;font-family:'Syne',sans-serif;transition:all .18s}
.ctrl-btn:hover{filter:brightness(1.2);transform:scale(1.03)}
.btn-attack{background:rgba(255,61,90,.12);color:var(--red);border-color:rgba(255,61,90,.3)}
.btn-paste{background:rgba(255,133,51,.12);color:var(--orange);border-color:rgba(255,133,51,.3)}
.btn-normal{background:rgba(15,217,158,.08);color:var(--green);border-color:rgba(15,217,158,.2)}
.btn-reset{background:var(--surface2);color:var(--text3);border-color:var(--border2)}
.ticker{padding:7px 20px;border-top:1px solid var(--border);font-family:'IBM Plex Mono',monospace;font-size:10px;color:var(--text3);background:var(--surface);flex-shrink:0;white-space:nowrap;overflow:hidden}
.tk-ok{color:var(--green)}
.tk-api{color:var(--accent2)}
.toasts{position:fixed;top:70px;right:20px;z-index:999;display:flex;flex-direction:column;gap:7px;pointer-events:none}
.toast{background:var(--surface);border:1px solid var(--border2);border-radius:10px;padding:11px 15px;max-width:290px;animation:toastIn .3s ease;pointer-events:auto}
.toast.critical{border-color:rgba(255,61,90,.4);background:rgba(255,61,90,.07)}
.toast-title{font-size:13px;font-weight:700;margin-bottom:3px}
.toast.critical .toast-title{color:var(--red)}
.toast-body{font-size:11px;color:var(--text2)}
@keyframes toastIn{from{opacity:0;transform:translateX(16px)}to{opacity:1;transform:translateX(0)}}
::-webkit-scrollbar{width:3px}
::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px}
</style>
</head>
<body>
<div class="hdr">
  <div class="hdr-l">
    <div class="brand">SwapMart</div>
    <div class="demo-chip">DRIFTLINE LIVE DEMO</div>
  </div>
  <div class="hdr-r">
    <div class="live-badge"><div class="pulse"></div>Live</div>
    <div class="api-status" id="api-status">checking api...</div>
  </div>
</div>
<div class="layout">
  <div class="panel">
    <div class="panel-hdr">
      <div class="panel-title">Marketplace Listings</div>
      <div class="panel-meta" id="lst-count">6 active</div>
    </div>
    <div class="listings-scroll" id="listings"></div>
  </div>
  <div class="panel">
    <div class="panel-hdr">
      <div class="panel-title">Messages</div>
      <div class="panel-meta" id="chat-meta">pick a listing</div>
    </div>
    <div class="chat-wrap" id="chat-wrap">
      <div class="chat-placeholder">
        <div class="chat-placeholder-icon">&#x1F4AC;</div>
        <p>Select a listing to message the seller</p>
        <small>Every message is scored by Driftline in real time</small>
      </div>
    </div>
  </div>
  <div class="panel" style="border-right:none">
    <div class="drift-hdr">
      <div class="drift-brand">
        <div class="drift-dot"></div>
        <div class="drift-name">Driftline</div>
        <div class="live-badge" style="margin-left:auto"><div class="pulse"></div>scanning</div>
      </div>
      <div class="drift-sub">Real-time fraud detection &bull; metadata only, no content read</div>
    </div>
    <div class="stats-row">
      <div class="stat"><div class="stat-val" id="s-events" style="color:var(--accent2)">0</div><div class="stat-label">Scored</div></div>
      <div class="stat"><div class="stat-val" id="s-flagged" style="color:var(--red)">0</div><div class="stat-label">Flagged</div></div>
      <div class="stat"><div class="stat-val" id="s-blocked" style="color:var(--orange)">0</div><div class="stat-label">Blocked</div></div>
      <div class="stat"><div class="stat-val" id="s-clean" style="color:var(--green)">0</div><div class="stat-label">Clean</div></div>
    </div>
    <div class="feed" id="feed">
      <div class="feed-empty"><p>Waiting for activity...<br><br><span style="opacity:.5">Hit a button below to simulate fraud<br>or start chatting with a seller</span></p></div>
    </div>
    <div class="ticker" id="ticker">Ready &mdash; Driftline standing by</div>
    <div class="controls">
      <div class="ctrl-label">Simulate Attack</div>
      <div class="ctrl-btns">
        <button class="ctrl-btn btn-attack" onclick="runMassBot()">&#x1F916; Mass Spam Bot</button>
        <button class="ctrl-btn btn-paste" onclick="runCopyBot()">&#x1F4CB; Copy-Paste Scammer</button>
        <button class="ctrl-btn btn-normal" onclick="runNormalUser()">&#x1F464; Normal User</button>
        <button class="ctrl-btn btn-reset" onclick="resetAll()">&#x21BA; Reset</button>
      </div>
    </div>
  </div>
</div>
<div class="toasts" id="toasts"></div>
<script>
const API_URL='https://driftline-api-production.up.railway.app';
const API_KEY='dk_alpha_marketplace_001';
let apiLive=false;
const G={events:0,flagged:new Set(),blocked:new Set(),clean:new Set(),scores:{},history:{},messages:{},activeListing:null,botTimer:null};
const LISTINGS=[
  {id:'l1',name:"Vintage Levi's 501 Jacket",price:'$85',seller:'alex_moves',desc:'Size M, barely worn. Faded black, perfect condition. Can ship or meet in NYC.'},
  {id:'l2',name:'Sony WH-1000XM5 Headphones',price:'$210',seller:'techflip22',desc:'2 months old, original box. Noise cancelling works perfectly.'},
  {id:'l3',name:'MacBook Pro M2 13"',price:'$1,050',seller:'mike_tech',desc:'8GB/256GB, AppleCare until Dec 2025. No scratches.'},
  {id:'l4',name:'IKEA KALLAX 4x4 Shelf',price:'$40',seller:'moving_quick',desc:'White. Must pick up this weekend in Brooklyn. Great condition.'},
  {id:'l5',name:'Canon EOS R50 Body',price:'$650',seller:'shutterhaus',desc:'1,100 shutter count. Clean sensor, no marks.'},
  {id:'l6',name:'Patagonia Retro-X Fleece L',price:'$60',seller:'gear_re_use',desc:'Washed once. Cream colorway. No pilling.'},
];

function localScore(userId){
  const evts=G.history[userId]||[];
  if(!evts.length)return{score:0,level:'low',flags:[],rec:'No action required'};
  const now=Date.now()/1000;
  const h1=evts.filter(e=>e.ts>now-3600);
  const convos=new Set(h1.map(e=>e.conv));
  let s=0,flags=[];
  const mc=h1.length;
  if(mc>50){s+=40;flags.push('Extreme volume: '+mc+' messages in 1hr');}
  else if(mc>25){s+=22;flags.push('High volume: '+mc+' messages in 1hr');}
  else if(mc>12){s+=10;flags.push('Elevated volume: '+mc+' messages in 1hr');}
  const uc=convos.size;
  if(uc>25){s+=35;flags.push('Mass outreach: '+uc+' simultaneous conversations');}
  else if(uc>15){s+=20;flags.push('High spread: '+uc+' open conversations');}
  else if(uc>8){s+=8;flags.push('Elevated conversations: '+uc);}
  const speeds=h1.map(e=>e.speed).filter(x=>x>0&&x<600);
  if(speeds.length>=4){
    const avg=speeds.reduce((a,b)=>a+b,0)/speeds.length;
    const ultraFast=speeds.filter(x=>x<1.5).length;
    const pct=ultraFast/speeds.length;
    if(avg<1.5){s+=28;flags.push('Bot-like speed: avg '+avg.toFixed(1)+'s');}
    else if(avg<3.0){s+=15;flags.push('Very fast replies: avg '+avg.toFixed(1)+'s');}
    else if(pct>0.6){s+=18;flags.push(Math.round(pct*100)+'% replies under 1.5s');}
  }
  const lens=h1.map(e=>e.len).filter(x=>x>0);
  if(lens.length>=8){
    const avg=lens.reduce((a,b)=>a+b,0)/lens.length;
    const sd=Math.sqrt(lens.reduce((a,b)=>a+(b-avg)**2,0)/lens.length);
    const cv=sd/Math.max(avg,1);
    if(cv<0.08&&avg>20){s+=22;flags.push('Copy-paste detected: length variance '+Math.round(cv*100)+'%');}
    else if(cv<0.15&&avg>20){s+=10;flags.push('Suspiciously uniform message lengths');}
  }
  if(mc>=5&&uc>=3){
    const ratio=mc/uc;
    if(ratio<1.5){s+=20;flags.push('Spray pattern: '+mc+' msgs across '+uc+' convos');}
  }
  const last15=evts.filter(e=>e.ts>now-900);
  const before=h1.filter(e=>e.ts<=now-900);
  if(last15.length>10&&before.length<3){s+=15;flags.push('Sudden spike: '+last15.length+' msgs in 15 min');}
  s=Math.min(Math.round(s),99);
  let level,rec;
  if(s>=75){level='critical';rec='Suspend account immediately';}
  else if(s>=55){level='high';rec='Limit messaging — verify identity';}
  else if(s>=35){level='medium';rec='Monitor closely';}
  else{level='low';rec='No action required';}
  return{score:s,level,flags,rec};
}

async function pingAPI(){
  try{const r=await fetch(API_URL+'/',{signal:AbortSignal.timeout(4000)});apiLive=r.ok||r.status<500;}catch(e){apiLive=false;}
  const el=document.getElementById('api-status');
  el.textContent=apiLive?'API connected':'local scoring mode';
  el.className='api-status '+(apiLive?'ok':'err');
}

async function scoreEvent(userId,convId,msgLen,replySpeed){
  G.events++;
  document.getElementById('s-events').textContent=G.events;
  if(!G.history[userId])G.history[userId]=[];
  G.history[userId].push({ts:Date.now()/1000,conv:convId,len:msgLen,speed:replySpeed});
  const local=localScore(userId);
  let result={...local,source:'local'};
  setTicker('Scoring '+userId+' | speed='+replySpeed.toFixed(1)+'s | convos='+new Set(G.history[userId].map(e=>e.conv)).size);
  if(apiLive){
    try{
      const params=new URLSearchParams({user_id:userId,conversation_id:convId,platform:'swapmart_demo',reply_speed:replySpeed,message_length:msgLen});
      const r=await fetch(API_URL+'/event?'+params,{method:'POST',headers:{'X-API-Key':API_KEY},signal:AbortSignal.timeout(3000)});
      if(r.ok){const d=await r.json();if(typeof d.risk_score==='number'){result={score:d.risk_score,level:d.risk_level||local.level,flags:d.flags||local.flags,rec:d.recommendation||local.rec,source:'live'};}}
    }catch(e){}
  }
  G.scores[userId]=result.score;
  if(result.score>=35)G.flagged.add(userId);
  if(result.score>=75&&!G.blocked.has(userId)){G.blocked.add(userId);showToast(userId,result.score,result.flags[0]||'Suspicious behavior');}
  if(result.score<35)G.clean.add(userId);
  updateStats();addFeedCard(userId,result);renderListings();
  setTicker((result.source==='live'?'\u2713 API: ':'Local: ')+userId+' scored '+result.score+' ['+result.level.toUpperCase()+']',result.source==='live');
}

function updateStats(){
  document.getElementById('s-flagged').textContent=G.flagged.size;
  document.getElementById('s-blocked').textContent=G.blocked.size;
  document.getElementById('s-clean').textContent=G.clean.size;
}

function addFeedCard(userId,r){
  const feed=document.getElementById('feed');
  const empty=feed.querySelector('.feed-empty');
  if(empty)empty.remove();
  const now=new Date();
  const t=now.getHours()+':'+String(now.getMinutes()).padStart(2,'0')+':'+String(now.getSeconds()).padStart(2,'0');
  const el=document.createElement('div');
  el.className='event-card '+r.level;
  el.innerHTML='<div class="ec-top"><span class="ec-user">'+userId+'</span><span class="ec-score '+r.level+'">'+r.score+'</span></div>'
    +'<div class="ec-signals">'+(r.flags.length?r.flags.slice(0,2).join(' &bull; '):'No anomalies detected')+'</div>'
    +'<span class="ec-rec '+r.level+'">'+r.rec+'</span>'
    +'<div class="ec-time">'+t+' <span class="ec-src '+r.source+'">['+r.source+']</span></div>';
  feed.insertBefore(el,feed.firstChild);
  while(feed.children.length>25)feed.removeChild(feed.lastChild);
}

function setTicker(msg,success){
  const el=document.getElementById('ticker');
  if(success===true)el.innerHTML='<span class="tk-api">driftline</span> <span class="tk-ok">'+msg+'</span>';
  else el.innerHTML='<span class="tk-api">driftline</span> '+msg;
}

function showToast(userId,score,reason){
  const c=document.getElementById('toasts');
  const t=document.createElement('div');
  t.className='toast critical';
  t.innerHTML='<div class="toast-title">\u26a0\ufe0f BLOCKED: '+userId+'</div><div class="toast-body">Score '+score+' \u2014 '+reason+'</div>';
  c.appendChild(t);setTimeout(()=>t.remove(),5000);
}

function renderListings(){
  const area=document.getElementById('listings');
  area.innerHTML=LISTINGS.map(l=>{
    const sc=G.scores[l.seller]||0;
    const blocked=G.blocked.has(l.seller);
    let cls='',tagCls='rt-clean',tagTxt='Clean';
    if(blocked||sc>=75){cls='flagged';tagCls='rt-critical';tagTxt='BLOCKED';}
    else if(sc>=55){cls='suspicious';tagCls='rt-high';tagTxt='High Risk';}
    else if(sc>=35){cls='suspicious';tagCls='rt-medium';tagTxt='Suspicious';}
    const dotCls=blocked||sc>=75?'red':sc>=35?'orange':'';
    const scoreStr=sc>0?' <span style="margin-left:auto;font-family:\'IBM Plex Mono\',monospace;font-size:10px;color:'+(sc>=75?'var(--red)':sc>=35?'var(--orange)':'var(--text3)')+'">risk:'+sc+'</span>':'';
    return '<div class="listing '+(G.activeListing===l.id?'sel':'')+' '+cls+'" onclick="selectListing(\''+l.id+'\')">'
      +'<div class="risk-tag '+tagCls+'">'+tagTxt+'</div>'
      +'<div class="l-top"><div class="l-name">'+l.name+'</div><div class="l-price">'+l.price+'</div></div>'
      +'<div class="l-seller"><div class="s-dot '+dotCls+'"></div>'+l.seller+scoreStr+'</div>'
      +'<div class="l-desc">'+l.desc+'</div></div>';
  }).join('');
}

function selectListing(id){
  G.activeListing=id;
  const l=LISTINGS.find(x=>x.id===id);
  if(!G.messages[id])G.messages[id]=[];
  document.getElementById('chat-meta').textContent=l.seller;
  document.getElementById('chat-wrap').innerHTML='<div class="chat-hdr"><div class="chat-seller">'+l.seller+'</div><div class="chat-item">'+l.name+'</div></div>'
    +'<div class="msgs" id="msgs-'+id+'"></div>'
    +'<div class="input-row"><input class="chat-in" id="chat-in" placeholder="Ask about this item..." onkeydown="if(event.key===\'Enter\')sendMsg()"><button class="send-btn" onclick="sendMsg()">Send</button></div>';
  renderMsgs(id);renderListings();
}

function renderMsgs(id){
  const c=document.getElementById('msgs-'+id);
  if(!c)return;
  c.innerHTML=(G.messages[id]||[]).map(m=>'<div class="msg '+m.from+(m.bot?' bot-msg':'')+'"><div class="bubble">'+m.text+'</div><div class="msg-time">'+m.time+'</div></div>').join('');
  c.scrollTop=c.scrollHeight;
}

function addMsg(lid,from,text,bot){
  const t=new Date();
  const time=t.getHours()+':'+String(t.getMinutes()).padStart(2,'0');
  if(!G.messages[lid])G.messages[lid]=[];
  G.messages[lid].push({from,text,time,bot:!!bot});
  if(G.activeListing===lid)renderMsgs(lid);
}

function sendMsg(){
  const inp=document.getElementById('chat-in');
  if(!inp||!inp.value.trim()||!G.activeListing)return;
  const l=LISTINGS.find(x=>x.id===G.activeListing);
  const text=inp.value.trim();inp.value='';
  addMsg(G.activeListing,'you',text);
  scoreEvent('buyer_you','conv_'+G.activeListing,text.length,5+Math.random()*15);
  setTimeout(()=>{
    const replies=['Yes still available! When can you pick up?','Happy to answer any questions.','That works for me.','Can meet somewhere central if easier.','Feel free to ask anything.'];
    const reply=replies[Math.floor(Math.random()*replies.length)];
    addMsg(G.activeListing,'them',reply);
    scoreEvent(l.seller,'conv_'+G.activeListing,reply.length,20+Math.random()*60);
  },900+Math.random()*1100);
}

function stopBot(){if(G.botTimer){clearInterval(G.botTimer);G.botTimer=null;}}

function runMassBot(){
  stopBot();
  const uid='spambot_'+Math.floor(Math.random()*9999);
  const convs=Array.from({length:30},(_,i)=>'conv_spam_'+i);
  const msgs=['Is this still available?','Can you ship?','Lowest price?','Still available?','What condition?','Accept offers?','Need it today cash','Is this available?'];
  let i=0;
  G.botTimer=setInterval(async()=>{
    if(i>=42){stopBot();return;}
    const conv=convs[Math.floor(Math.random()*convs.length)];
    const msg=msgs[i%msgs.length];
    await scoreEvent(uid,conv,msg.length,0.6+Math.random()*1.0);
    if(G.activeListing)addMsg(G.activeListing,'them',msg,true);
    i++;
  },320);
}

function runCopyBot(){
  stopBot();
  const uid='scammer_'+Math.floor(Math.random()*9999);
  const convs=Array.from({length:18},(_,i)=>'conv_cp_'+i);
  const SCRIPT='Hi! I want to buy this item. Please send your PayPal or Zelle and I will pay immediately. Full asking price. Please respond ASAP.';
  let i=0;
  G.botTimer=setInterval(async()=>{
    if(i>=26){stopBot();return;}
    const conv=convs[Math.floor(Math.random()*convs.length)];
    await scoreEvent(uid,conv,SCRIPT.length,1.0+Math.random()*0.8);
    if(G.activeListing)addMsg(G.activeListing,'them',SCRIPT,true);
    i++;
  },480);
}

function runNormalUser(){
  stopBot();
  const uid='user_'+Math.floor(Math.random()*9999);
  const conv='conv_normal_'+Math.floor(Math.random()*99);
  const msgs=["Hey! Is this still available?","Could you do a little less?","That works. Can I pick up Saturday?","Perfect, I'll message you Saturday morning. Thanks!"];
  let i=0;
  G.botTimer=setInterval(async()=>{
    if(i>=msgs.length){stopBot();return;}
    await scoreEvent(uid,conv,msgs[i].length,45+Math.random()*220);
    if(G.activeListing)addMsg(G.activeListing,'them',msgs[i]);
    i++;
  },2400);
}

function resetAll(){
  stopBot();
  G.events=0;G.flagged=new Set();G.blocked=new Set();G.clean=new Set();G.scores={};G.history={};G.messages={};G.activeListing=null;
  ['s-events','s-flagged','s-blocked','s-clean'].forEach(id=>document.getElementById(id).textContent='0');
  document.getElementById('feed').innerHTML='<div class="feed-empty"><p>Waiting for activity...<br><br><span style="opacity:.5">Hit a button below to simulate fraud<br>or start chatting with a seller</span></p></div>';
  document.getElementById('chat-wrap').innerHTML='<div class="chat-placeholder"><div class="chat-placeholder-icon">&#x1F4AC;</div><p>Select a listing to message the seller</p><small>Every message is scored by Driftline in real time</small></div>';
  document.getElementById('chat-meta').textContent='pick a listing';
  document.getElementById('toasts').innerHTML='';
  setTicker('Reset \u2014 Driftline standing by');
  renderListings();
}

pingAPI();
renderListings();
</script>
</body>
</html>

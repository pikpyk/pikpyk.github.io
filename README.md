<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Save Editor · MPCM & PC</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:ital,wght@0,400;0,600;0,700;1,400&family=Unbounded:wght@700;900&display=swap');
:root{--bg:#080810;--s0:#0e0e18;--s1:#13131f;--s2:#1c1c2e;--s3:#242436;--border:#2e2e45;--border2:#3d3d5c;--c:#00e5ff;--c2:#7c3aed;--c3:#f59e0b;--c4:#10b981;--cdanger:#ef4444;--text:#dde4f0;--muted:#5a6480;--muted2:#7a84a0;}
*{margin:0;padding:0;box-sizing:border-box}html{scroll-behavior:smooth}
body{background:#060608;color:var(--text);font-family:'JetBrains Mono',monospace;font-size:13px;min-height:100vh;}
#bgCanvas{position:fixed;inset:0;width:100%;height:100%;pointer-events:none;z-index:0;}
.app{position:relative;z-index:1;display:flex;flex-direction:column;min-height:100vh;}
.topbar{display:flex;align-items:center;gap:16px;padding:0 24px;height:52px;background:rgba(8,8,16,.9);border-bottom:1px solid var(--border);backdrop-filter:blur(12px);position:sticky;top:0;z-index:50;}
.logo{font-family:'Unbounded',sans-serif;font-weight:900;font-size:15px;background:linear-gradient(120deg,var(--c),var(--c2));-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;flex-shrink:0;}
.topbar-sep{width:1px;height:20px;background:var(--border);flex-shrink:0;}
.file-pill{display:flex;align-items:center;gap:8px;background:var(--s2);border:1px solid var(--border);border-radius:6px;padding:5px 12px;font-size:11px;cursor:pointer;transition:all .2s;flex-shrink:0;}
.file-pill:hover{border-color:var(--c);color:var(--c);}
.file-pill-dot{width:6px;height:6px;border-radius:50%;background:var(--c4);box-shadow:0 0 6px var(--c4);flex-shrink:0;}
.topbar-spacer{flex:1;}
.adv-toggle{display:flex;align-items:center;gap:8px;background:var(--s2);border:1px solid var(--border);border-radius:6px;padding:6px 14px;font-size:11px;cursor:pointer;user-select:none;transition:all .2s;letter-spacing:.5px;}
.adv-toggle:hover{border-color:var(--c3);}
.adv-toggle.on{background:rgba(245,158,11,.1);border-color:rgba(245,158,11,.4);color:var(--c3);}

#dropScreen{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:60px 24px;}
.drop-hero{font-family:'Unbounded',sans-serif;font-size:clamp(32px,6vw,64px);font-weight:900;line-height:1;background:linear-gradient(135deg,var(--c) 0%,var(--c2) 45%,var(--c3) 100%);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;letter-spacing:-2px;margin-bottom:10px;text-align:center;}
.drop-sub{color:var(--muted2);font-size:12px;letter-spacing:2px;text-transform:uppercase;margin-bottom:48px;text-align:center;}
.drop-zone{width:100%;max-width:560px;border:2px dashed var(--border2);border-radius:16px;padding:56px 40px;text-align:center;cursor:pointer;transition:all .3s;background:var(--s0);position:relative;overflow:hidden;}
.drop-zone::after{content:'';position:absolute;inset:0;background:radial-gradient(circle at 50% 50%,rgba(0,229,255,.06) 0%,transparent 65%);opacity:0;transition:opacity .3s;}
.drop-zone:hover,.drop-zone.over{border-color:var(--c);box-shadow:0 0 0 1px rgba(0,229,255,.15),0 0 20px rgba(0,229,255,.1);}
.drop-zone:hover::after,.drop-zone.over::after{opacity:1;}
.drop-icon{font-size:52px;margin-bottom:18px;display:block;}
.drop-title{font-family:'Unbounded',sans-serif;font-size:15px;font-weight:700;margin-bottom:8px;}
.drop-hint{color:var(--muted2);font-size:11px;letter-spacing:1px;}
.drop-hint span{color:var(--c);}
#fileInput{display:none;}

#editorScreen{flex:1;display:none;flex-direction:column;}
.tabs{display:flex;gap:2px;padding:12px 24px 0;border-bottom:1px solid var(--border);background:var(--s0);}
.tab{padding:8px 18px 10px;font-size:11px;letter-spacing:1px;text-transform:uppercase;cursor:pointer;border-bottom:2px solid transparent;color:var(--muted2);transition:all .2s;user-select:none;}
.tab:hover{color:var(--text);}
.tab.active{color:var(--c);border-color:var(--c);}

.pane{display:none;padding:24px;flex:1;}
.pane.active{display:block;}

.sec{background:var(--s1);border:1px solid var(--border);border-radius:12px;margin-bottom:20px;overflow:hidden;}
.sec-head{display:flex;align-items:center;gap:10px;padding:12px 20px;border-bottom:1px solid var(--border);background:var(--s2);font-size:10px;font-weight:700;letter-spacing:2px;text-transform:uppercase;color:var(--muted2);}
.sec-head .pip{width:6px;height:6px;border-radius:50%;flex-shrink:0;}
.sec-badge{margin-left:auto;background:rgba(0,229,255,.1);border:1px solid rgba(0,229,255,.2);border-radius:4px;padding:2px 8px;font-size:10px;color:var(--c);}

.fgrid{display:grid;grid-template-columns:repeat(auto-fill,minmax(240px,1fr));gap:1px;background:var(--border);}
.field{background:var(--s1);padding:14px 18px;display:flex;flex-direction:column;gap:7px;}
.field-label{font-size:10px;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);display:flex;align-items:center;gap:6px;}
.adv-only{font-size:9px;padding:1px 5px;border-radius:3px;background:rgba(245,158,11,.1);border:1px solid rgba(245,158,11,.25);color:var(--c3);letter-spacing:.5px;}

.inp{background:var(--s3);border:1px solid var(--border);border-radius:6px;color:var(--text);font-family:'JetBrains Mono',monospace;font-size:13px;padding:8px 11px;width:100%;outline:none;transition:border-color .2s,box-shadow .2s;}
.inp:focus{border-color:var(--c);box-shadow:0 0 0 3px rgba(0,229,255,.08);}
.inp[readonly]{opacity:.45;cursor:default;}
.inp.dirty{border-color:var(--c3);color:var(--c3);}

.tog-wrap{display:flex;align-items:center;gap:10px;}
.tog{position:relative;width:40px;height:21px;flex-shrink:0;}
.tog input{display:none;}
.tog-track{position:absolute;inset:0;background:var(--s3);border:1px solid var(--border);border-radius:11px;cursor:pointer;transition:all .3s;}
.tog-track::before{content:'';position:absolute;left:3px;top:50%;transform:translateY(-50%);width:13px;height:13px;border-radius:50%;background:var(--muted);transition:all .3s;}
.tog input:checked+.tog-track{background:rgba(0,229,255,.12);border-color:var(--c);}
.tog input:checked+.tog-track::before{left:calc(100% - 16px);background:var(--c);box-shadow:0 0 8px var(--c);}
.tog-lbl{font-size:13px;}

.items-toolbar{display:flex;align-items:center;gap:10px;padding:12px 18px;border-bottom:1px solid var(--border);background:var(--s2);flex-wrap:wrap;}
.search-inp{background:var(--s3);border:1px solid var(--border);border-radius:6px;padding:7px 12px;font-family:'JetBrains Mono',monospace;font-size:12px;color:var(--text);outline:none;width:200px;transition:border-color .2s;}
.search-inp:focus{border-color:var(--c);}
.filter-btn{padding:6px 12px;border-radius:5px;font-size:10px;border:1px solid var(--border);background:var(--s3);cursor:pointer;color:var(--muted2);transition:all .2s;letter-spacing:.5px;text-transform:uppercase;font-family:inherit;}
.filter-btn:hover{color:var(--text);}
.filter-btn.on{background:rgba(239,68,68,.1);border-color:rgba(239,68,68,.35);color:var(--cdanger);}

.items-scroll{overflow-y:auto;max-height:calc(100vh - 290px);}
.items-scroll::-webkit-scrollbar{width:4px;}
.items-scroll::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px;}
.items-table{width:100%;border-collapse:collapse;}
.items-table th{padding:9px 14px;text-align:left;font-size:10px;letter-spacing:1.5px;text-transform:uppercase;color:var(--muted);border-bottom:1px solid var(--border);background:var(--s2);position:sticky;top:0;}
.items-table td{padding:9px 14px;border-bottom:1px solid var(--border);vertical-align:middle;font-size:12px;}
.items-table tr:last-child td{border-bottom:none;}
.items-table tr:hover td{background:var(--s2);}
.item-name-cell{color:var(--text);font-weight:600;}
.item-id-cell{color:var(--muted);font-size:11px;}
.item-coord{color:var(--muted2);font-size:11px;}
.badge{display:inline-flex;align-items:center;font-size:10px;padding:2px 8px;border-radius:4px;letter-spacing:.5px;}
.badge-ok{background:rgba(16,185,129,.1);border:1px solid rgba(16,185,129,.25);color:var(--c4);}
.badge-bad{background:rgba(239,68,68,.1);border:1px solid rgba(239,68,68,.3);color:var(--cdanger);}
.badge-glue{background:rgba(0,229,255,.08);border:1px solid rgba(0,229,255,.2);color:var(--c);}
.badge-num{background:var(--s3);border:1px solid var(--border);color:var(--muted2);}
.item-fix-btn{padding:4px 10px;border-radius:4px;font-size:10px;border:1px solid rgba(16,185,129,.3);background:rgba(16,185,129,.08);color:var(--c4);cursor:pointer;transition:all .2s;font-family:inherit;}
.item-fix-btn:hover{background:rgba(16,185,129,.2);}
.item-dmg-btn{padding:4px 10px;border-radius:4px;font-size:10px;border:1px solid rgba(239,68,68,.3);background:rgba(239,68,68,.08);color:var(--cdanger);cursor:pointer;transition:all .2s;font-family:inherit;}

.raw-area{background:var(--s2);border:1px solid var(--border);border-radius:10px;padding:18px;font-size:11px;line-height:1.9;max-height:70vh;overflow-y:auto;white-space:pre-wrap;word-break:break-all;color:var(--muted2);}
.raw-area::-webkit-scrollbar{width:4px;}
.raw-area::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px;}
.jk{color:#7dd3fc;}.js{color:#86efac;}.jn{color:var(--c3);}.jb{color:#c084fc;}

.footer-bar{padding:16px 24px;border-top:1px solid var(--border);background:rgba(8,8,16,.9);backdrop-filter:blur(12px);display:flex;align-items:center;gap:12px;flex-wrap:wrap;position:sticky;bottom:0;z-index:40;}
.changes-info{font-size:11px;color:var(--c3);display:none;align-items:center;gap:6px;}
.changes-info.on{display:flex;}
.changes-info::before{content:'●';font-size:8px;}
.spacer{flex:1;}
.btn{display:inline-flex;align-items:center;gap:7px;padding:10px 22px;border-radius:7px;font-family:'Unbounded',sans-serif;font-size:11px;font-weight:700;letter-spacing:.5px;text-transform:uppercase;cursor:pointer;border:none;transition:all .2s;}
.btn-dl{background:linear-gradient(135deg,var(--c),#0099bb);color:#000;box-shadow:0 4px 16px rgba(0,229,255,.25);}
.btn-dl:hover{transform:translateY(-1px);box-shadow:0 8px 24px rgba(0,229,255,.4);}
.btn-rst{background:var(--s2);border:1px solid var(--border);color:var(--muted2);}
.btn-rst:hover{border-color:var(--c);color:var(--c);}
.btn-fix{background:rgba(16,185,129,.12);border:1px solid rgba(16,185,129,.3);color:var(--c4);}
.btn-fix:hover{background:rgba(16,185,129,.22);}

.toast{position:fixed;bottom:80px;right:24px;background:var(--s1);border:1px solid var(--border);border-radius:10px;padding:13px 20px;display:flex;align-items:center;gap:10px;box-shadow:0 8px 32px rgba(0,0,0,.5);transform:translateX(120%);transition:transform .3s cubic-bezier(.34,1.56,.64,1);z-index:200;max-width:340px;font-size:12px;}
.toast.show{transform:translateX(0);}
.toast.ok{border-color:rgba(16,185,129,.4);}
.toast.err{border-color:rgba(239,68,68,.4);}
.toast.ok .ti{color:var(--c4);}
.toast.err .ti{color:var(--cdanger);}
.ti{font-size:16px;flex-shrink:0;}

.adv-field{display:none;}
body.advanced .adv-field{display:flex;}
body.advanced .items-table .adv-field{display:table-cell;}

.empty{padding:40px;text-align:center;color:var(--muted);}
@media(max-width:640px){.topbar{padding:0 14px;}.pane{padding:14px;}.tabs{padding:10px 14px 0;}.footer-bar{padding:12px 14px;}.fgrid{grid-template-columns:1fr;}}
</style>
</head>
<body>
<canvas id="bgCanvas"></canvas>
<div class="app">

<div class="topbar">
  <div class="logo">MPCM</div>
  <div class="topbar-sep"></div>
  <div class="file-pill" onclick="loadNew()" id="filePill" style="display:none">
    <div class="file-pill-dot"></div>
    <span id="pillName">—</span>
    <span style="color:var(--muted);margin-left:2px">✕</span>
  </div>
  <div class="topbar-spacer"></div>
  <div class="adv-toggle" id="advToggle" onclick="toggleAdv()">
    <span>⚙</span>
    <span id="advLabel">Advanced</span>
  </div>
</div>

<div id="dropScreen">
  <div class="drop-hero">SAVE<br>EDITOR</div>
  <div class="drop-sub">Unity 3D · .mpcm (XOR/DD) · .pc (XOR/81)</div>
  <div class="drop-zone" id="dropZone">
    <span class="drop-icon">🎮</span>
    <div class="drop-title">Перетащи .mpcm файл сюда</div>
    <div class="drop-hint">или кликни чтобы выбрать · <span>.mpcm</span> и <span>.pc</span></div>
    <input type="file" id="fileInput" accept=".mpcm,.pc">
  </div>
</div>

<div id="editorScreen">
  <div class="tabs">
    <div class="tab active" onclick="switchTab('general')">🏠 Общее</div>
    <div class="tab" onclick="switchTab('player')">🧍 Игрок</div>
    <div class="tab" onclick="switchTab('items')">📦 Предметы</div>
    <div class="tab" onclick="switchTab('raw')">{ } JSON</div>
  </div>

  <div class="pane active" id="pane-general">
    <div class="sec">
      <div class="sec-head"><div class="pip" style="background:var(--c);box-shadow:0 0 6px var(--c)"></div>Параметры игры</div>
      <div class="fgrid" id="fgrid-game"></div>
    </div>
    <div class="sec">
      <div class="sec-head"><div class="pip" style="background:var(--c2);box-shadow:0 0 6px var(--c2)"></div>Настройки комнаты</div>
      <div class="fgrid" id="fgrid-room"></div>
    </div>
  </div>

  <div class="pane" id="pane-player">
    <div class="sec">
      <div class="sec-head"><div class="pip" style="background:var(--c4);box-shadow:0 0 6px var(--c4)"></div>Позиция и поворот камеры</div>
      <div class="fgrid" id="fgrid-player"></div>
    </div>
    <div style="margin-top:12px;color:var(--muted);font-size:11px;text-align:center;">⚙ Поля позиции доступны только в режиме Advanced</div>
  </div>

  <div class="pane" id="pane-items">
    <div class="sec">
      <div class="items-toolbar">
        <input class="search-inp" id="itemSearch" placeholder="🔍  поиск по названию..." oninput="filterItems()">
        <button class="filter-btn" id="filterDmg" onclick="toggleFilter('dmg')">💔 повреждённые</button>
        <button class="filter-btn" id="filterGlue" onclick="toggleFilter('glue')">🔒 приклеенные</button>
        <div class="sec-badge" id="itemsBadge">0</div>
        <button class="btn btn-fix" onclick="fixAllItems()" style="padding:7px 14px;font-size:10px;margin-left:auto;">✓ Починить все</button>
      </div>
      <div class="items-scroll">
        <table class="items-table" id="itemsTable">
          <thead><tr>
            <th>#</th><th>Название</th><th>ID</th><th>Позиция</th><th>Статус</th>
            <th class="adv-field">Действия</th>
          </tr></thead>
          <tbody id="itemsTbody"></tbody>
        </table>
      </div>
    </div>
  </div>

  <div class="pane" id="pane-raw">
    <div class="sec">
      <div class="sec-head"><div class="pip" style="background:var(--muted)"></div>Строка 1 — заголовок</div>
      <div class="raw-area" id="raw1"></div>
    </div>
    <div class="sec" style="margin-top:20px">
      <div class="sec-head"><div class="pip" style="background:var(--muted)"></div>Строка 2 — данные мира (первые 4KB)</div>
      <div class="raw-area" id="raw2"></div>
    </div>
  </div>

  <div class="footer-bar">
    <div class="changes-info" id="changesInfo">Есть несохранённые изменения</div>
    <div class="spacer"></div>
    <button class="btn btn-rst" onclick="resetAll()">↩ Сброс</button>
    <button class="btn btn-dl" onclick="downloadSave()">⬇ Скачать сейв</button>
  </div>
</div>

</div>

<div class="toast" id="toast"><span class="ti" id="ti"></span><span id="tm"></span></div>

<script>
// Format table: file extension → { xor, xorHigh }
const FORMATS = {
  'mpcm': { xor: 0xDD, xorHigh: 0x480 },
  'pc':   { xor: 0x81, xorHigh: 0x481 },
};
let fmt = FORMATS.mpcm; // active format, detected on load

let origCp=null,origText=null,origFileName=null;
let parsedHeader={},parsedPlayer={},parsedItems=[];
let activeFilters={dmg:false,glue:false};
let advancedMode=false,toastTimer=null;

function detectFormat(filename){
  const ext = filename.split('.').pop().toLowerCase();
  return FORMATS[ext] || FORMATS.mpcm;
}

function decode(buf, f){
  const s=new TextDecoder('utf-8').decode(new Uint8Array(buf));
  const cp=[...s].map(c=>c.codePointAt(0));
  const bytes=cp.map(c=>((c>0xFF?(c^f.xorHigh):(c^f.xor))&0xFF));
  return{text:new TextDecoder('latin1').decode(new Uint8Array(bytes)),cp};
}

function patchCp(cp,text,key,newVal){
  const re=new RegExp('"'+key+'":([-\\d.]+)');
  const m=text.match(re);
  if(!m)return{error:'Поле "'+key+'" не найдено'};
  const old=m[1],nv=String(newVal);
  if(old.length!==nv.length)return{error:'Длина "'+old+'"('+old.length+') ≠ "'+nv+'"('+nv.length+'). Нужно одинаковое кол-во символов.'};
  const start=m.index+m[0].length-old.length;
  const nc=[...cp];
  for(let i=0;i<nv.length;i++){
    const o=nc[start+i];
    nc[start+i]=nv.charCodeAt(i)^(o>0xFF?fmt.xorHigh:fmt.xor);
  }
  return nc;
}

function patchBool(cp,text,key,newVal){
  const re=new RegExp('"'+key+'":(true|false)');
  const m=text.match(re);
  if(!m)return null;
  const old=m[1],nv=String(newVal);
  const start=m.index+('"'+key+'":').length;
  if(old.length===nv.length){
    const nc=[...cp];
    for(let i=0;i<nv.length;i++){
      const o=nc[start+i];
      nc[start+i]=nv.charCodeAt(i)^(o>0xFF?fmt.xorHigh:fmt.xor);
    }
    return nc;
  }
  toast('err','!','Смена '+key+' ('+old+'→'+nv+') — длины не совпадают, пропущено');
  return null;
}

function cpToBytes(cp){
  let s='';for(const c of cp)s+=String.fromCodePoint(c);
  return new TextEncoder().encode(s);
}

// ── File loading ──
const dz=document.getElementById('dropZone'),fi=document.getElementById('fileInput');
dz.addEventListener('click',()=>fi.click());
fi.addEventListener('change',e=>e.target.files[0]&&loadFile(e.target.files[0]));
dz.addEventListener('dragover',e=>{e.preventDefault();dz.classList.add('over');});
dz.addEventListener('dragleave',()=>dz.classList.remove('over'));
dz.addEventListener('drop',e=>{e.preventDefault();dz.classList.remove('over');e.dataTransfer.files[0]&&loadFile(e.dataTransfer.files[0]);});

function loadFile(file){
  origFileName=file.name;
  fmt=detectFormat(file.name);
  const fmtLabel=file.name.endsWith('.pc')?'PC':'MPCM';
  document.getElementById('pillName').textContent=file.name;
  document.getElementById('filePill').style.display='flex';
  const r=new FileReader();
  r.onload=e=>{
    try{
      const{text,cp}=decode(e.target.result,fmt);
      origCp=cp;origText=text;
      parseAll(text);renderAll();
      document.getElementById('dropScreen').style.display='none';
      document.getElementById('editorScreen').style.display='flex';
      toast('ok','✓','['+fmtLabel+'] Загружено · '+parsedItems.length+' предметов');
    }catch(ex){toast('err','✗','Ошибка: '+ex.message);}
  };
  r.readAsArrayBuffer(file);
}

function loadNew(){
  document.getElementById('dropScreen').style.display='flex';
  document.getElementById('editorScreen').style.display='none';
  document.getElementById('filePill').style.display='none';
  fi.value='';origCp=null;origText=null;
}

// ── Parsing ──
function parseAll(text){
  const nl=text.indexOf('\n');
  const line0=nl>=0?text.slice(0,nl):text;
  const line1=nl>=0?text.slice(nl+1):'';
  parsedHeader={};
  const fns=[
    {k:'version',re:/"version":"([^"]+)"/},
    {k:'roomName',re:/"roomName":"((?:[^"\\]|\\.)*)"/},
    {k:'coin',re:/"coin":(-?[\d.]+)/},
    {k:'room',re:/"room":(-?[\d.]+)/},
    {k:'gravity',re:/"gravity":(true|false)/},
    {k:'hardcore',re:/"hardcore":(true|false)/},
    {k:'playtime',re:/"playtime":(-?[\d.]+)/},
    {k:'temperature',re:/"temperature":(-?[\d.]+)/},
    {k:'ac',re:/"ac":(true|false)/},
    {k:'light',re:/"light":(true|false)/},
    {k:'sign',re:/"sign":"([^"]*)"/},
    {k:'autoSave',re:/"autoSave":(true|false)/},
  ];
  for(const{k,re}of fns){
    const m=line0.match(re);if(!m)continue;
    const v=m[1];
    if(v==='true')parsedHeader[k]=true;
    else if(v==='false')parsedHeader[k]=false;
    else if(/^-?[\d.]+$/.test(v))parsedHeader[k]=Number(v);
    else parsedHeader[k]=v;
  }
  parsedPlayer={};
  const pm=line1.match(/"playerData":\{"x":(-?[\d.]+),"y":(-?[\d.]+),"z":(-?[\d.]+),"ry":(-?[\d.]+),"rx":(-?[\d.]+)\}/);
  if(pm)parsedPlayer={x:+pm[1],y:+pm[2],z:+pm[3],ry:+pm[4],rx:+pm[5]};
  parsedItems=[];
  const ir=/"spawnId":"([^"]+)","id":(-?\d+),"pos":\{"x":(-?[\d.]+),"y":(-?[\d.]+),"z":(-?[\d.]+)\},"rot":\{"x":(-?[\d.]+),"y":(-?[\d.]+),"z":(-?[\d.]+),"w":(-?[\d.]+)\},"data":\{([^{}]*(?:\{[^{}]*\})*[^{}]*)\}/g;
  let im;
  while((im=ir.exec(line1))!==null){
    const dstr=im[10],data={};
    for(const[,dk,dv]of dstr.matchAll(/"(\w+)":(true|false|"[^"]*"|[-\d.]+)/g)){
      if(dv==='true')data[dk]=true;
      else if(dv==='false')data[dk]=false;
      else if(dv.startsWith('"'))data[dk]=dv.slice(1,-1);
      else data[dk]=Number(dv);
    }
    parsedItems.push({spawnId:im[1],id:+im[2],x:+im[3],y:+im[4],z:+im[5],data});
  }
}

// ── Rendering ──
function renderAll(){renderGame();renderRoom();renderPlayer();renderItems();renderRaw();checkChanges();}

const GMETA={
  version:{label:'Версия игры',editable:false,adv:false},
  coin:{label:'💰 Монеты',editable:true,adv:false},
  room:{label:'🏠 Комната №',editable:true,adv:false},
  roomName:{label:'🏷 Название комнаты',editable:true,adv:false},
  playtime:{label:'⏱ Время (сек)',editable:true,adv:true},
  temperature:{label:'🌡 Температура',editable:true,adv:true},
};

function renderGame(){
  const el=document.getElementById('fgrid-game');el.innerHTML='';
  for(const k of['version','coin','room','roomName','playtime','temperature']){
    if(!(k in parsedHeader))continue;
    const meta=GMETA[k]||{label:k,editable:true,adv:true};
    const v=parsedHeader[k];
    const div=document.createElement('div');
    div.className='field'+(meta.adv?' adv-field':'');
    div.innerHTML='<div class="field-label">'+meta.label+(meta.adv?'<span class="adv-only">ADV</span>':'')+'</div>'
      +'<input class="inp" id="f_'+k+'" data-key="'+k+'" data-orig="'+esc(String(v))+'" value="'+esc(String(v))+'"'+(meta.editable?'':' readonly')+'>';
    if(meta.editable)div.querySelector('input').addEventListener('input',onFieldChange);
    el.appendChild(div);
  }
}

function renderRoom(){
  const el=document.getElementById('fgrid-room');el.innerHTML='';
  const boolKeys=[
    {k:'gravity',label:'⬇ Гравитация',adv:false},
    {k:'hardcore',label:'💀 Хардкор',adv:false},
    {k:'ac',label:'❄ Кондиционер',adv:false},
    {k:'light',label:'💡 Свет',adv:false},
    {k:'autoSave',label:'💾 Автосохранение',adv:false},
  ];
  for(const{k,label,adv}of boolKeys){
    if(!(k in parsedHeader))continue;
    const v=parsedHeader[k];
    const div=document.createElement('div');
    div.className='field'+(adv?' adv-field':'');
    div.innerHTML='<div class="field-label">'+label+(adv?'<span class="adv-only">ADV</span>':'')+'</div>'
      +'<div class="tog-wrap"><label class="tog"><input type="checkbox" id="f_'+k+'" data-key="'+k+'" data-orig="'+v+'" '+(v?'checked':'')+'><div class="tog-track"></div></label>'
      +'<span class="tog-lbl" id="tl_'+k+'">'+(v?'Вкл':'Выкл')+'</span></div>';
    div.querySelector('input').addEventListener('change',function(){
      document.getElementById('tl_'+k).textContent=this.checked?'Вкл':'Выкл';
      this.classList.toggle('dirty',this.checked.toString()!==this.dataset.orig);
      checkChanges();
    });
    el.appendChild(div);
  }
  if('sign' in parsedHeader){
    const div=document.createElement('div');
    div.className='field adv-field';
    div.innerHTML='<div class="field-label">🪧 Текст вывески <span class="adv-only">ADV</span></div>'
      +'<input class="inp" id="f_sign" data-key="sign" data-orig="'+esc(String(parsedHeader.sign))+'" value="'+esc(String(parsedHeader.sign))+'">';
    div.querySelector('input').addEventListener('input',onFieldChange);
    el.appendChild(div);
  }
}

function renderPlayer(){
  const el=document.getElementById('fgrid-player');el.innerHTML='';
  const labels={x:'X позиция',y:'Y позиция (высота)',z:'Z позиция',ry:'Поворот Y (горизонт)',rx:'Поворот X (вертикаль)'};
  for(const[k,v]of Object.entries(parsedPlayer)){
    const div=document.createElement('div');
    div.className='field adv-field';
    div.innerHTML='<div class="field-label">'+(labels[k]||k)+' <span class="adv-only">ADV</span></div>'
      +'<input class="inp" id="fp_'+k+'" data-key="p_'+k+'" data-orig="'+v+'" value="'+v+'">';
    div.querySelector('input').addEventListener('input',onFieldChange);
    el.appendChild(div);
  }
  if(!Object.keys(parsedPlayer).length)el.innerHTML='<div class="empty">playerData не найден</div>';
}

function renderItems(filter){
  filter=filter||'';
  const tbody=document.getElementById('itemsTbody');tbody.innerHTML='';
  let shown=0;
  for(let i=0;i<parsedItems.length;i++){
    const item=parsedItems[i];
    const isDmg=item.data.damaged===true;
    const isGlue=item.data.glue===true;
    const name=item.spawnId||'???';
    if(filter&&!name.toLowerCase().includes(filter.toLowerCase()))continue;
    if(activeFilters.dmg&&!isDmg)continue;
    if(activeFilters.glue&&!isGlue)continue;
    shown++;
    const tr=document.createElement('tr');
    tr.innerHTML='<td><span class="badge badge-num">'+(i+1)+'</span></td>'
      +'<td class="item-name-cell">'+esc(name)+'</td>'
      +'<td class="item-id-cell">'+item.id+'</td>'
      +'<td class="item-coord">'+item.x.toFixed(1)+', '+item.y.toFixed(1)+', '+item.z.toFixed(1)+'</td>'
      +'<td>'+(isDmg?'<span class="badge badge-bad">💔 Сломан</span>':'<span class="badge badge-ok">✓ Цел</span>')
      +(isGlue?' <span class="badge badge-glue">🔒 Приклеен</span>':'')+'</td>'
      +'<td class="adv-field">'
      +(isDmg?'<button class="item-fix-btn" onclick="fixItem('+i+')">✓ Починить</button>'
             :'<button class="item-dmg-btn" onclick="damageItem('+i+')">💔 Сломать</button>')
      +'</td>';
    tbody.appendChild(tr);
  }
  document.getElementById('itemsBadge').textContent=shown+' / '+parsedItems.length;
}

function renderRaw(){
  const nl=origText.indexOf('\n');
  const line0=nl>=0?origText.slice(0,nl):origText;
  const line1=nl>=0?origText.slice(nl+1):'';
  try{
    const safe=line0.replace(/[\x00-\x1F]/g,c=>'\\u'+c.codePointAt(0).toString(16).padStart(4,'0'));
    document.getElementById('raw1').innerHTML=syntaxHL(JSON.stringify(JSON.parse(safe),null,2));
  }catch(e){document.getElementById('raw1').textContent=line0;}
  document.getElementById('raw2').innerHTML=syntaxHL(line1.slice(0,4096));
}

// ── Interaction ──
function switchTab(name){
  const names=['general','player','items','raw'];
  document.querySelectorAll('.tab').forEach((t,i)=>t.classList.toggle('active',names[i]===name));
  document.querySelectorAll('.pane').forEach(p=>p.classList.remove('active'));
  document.getElementById('pane-'+name).classList.add('active');
}

function toggleAdv(){
  advancedMode=!advancedMode;
  document.body.classList.toggle('advanced',advancedMode);
  document.getElementById('advToggle').classList.toggle('on',advancedMode);
  document.getElementById('advLabel').textContent=advancedMode?'Advanced ON':'Advanced';
  toast('ok','⚙',advancedMode?'Расширенный режим включён':'Расширенный режим отключён');
}

function onFieldChange(e){
  e.target.classList.toggle('dirty',e.target.value!==e.target.dataset.orig);
  checkChanges();
}

function checkChanges(){
  const dirty=document.querySelectorAll('.inp.dirty, input[type=checkbox].dirty').length>0;
  document.getElementById('changesInfo').classList.toggle('on',dirty);
}

function filterItems(){renderItems(document.getElementById('itemSearch').value);}

function toggleFilter(type){
  activeFilters[type]=!activeFilters[type];
  const id='filter'+type.charAt(0).toUpperCase()+type.slice(1);
  document.getElementById(id).classList.toggle('on',activeFilters[type]);
  filterItems();
}

function fixItem(idx){
  parsedItems[idx].data.damaged=false;
  filterItems();
  toast('ok','✓',parsedItems[idx].spawnId+' починен');
}

function damageItem(idx){
  parsedItems[idx].data.damaged=true;
  filterItems();
  toast('err','💔',parsedItems[idx].spawnId+' повреждён');
}

function fixAllItems(){
  let count=0;
  for(const item of parsedItems){if(item.data.damaged){item.data.damaged=false;count++;}}
  filterItems();
  toast('ok','✓','Починено предметов: '+count);
}

// ── Download ──
function downloadSave(){
  let cp=[...origCp];
  const text=origText;
  let ok=true;

  document.querySelectorAll('.inp[data-key]').forEach(inp=>{
    if(inp.readOnly||inp.value===inp.dataset.orig)return;
    const key=inp.dataset.key;
    if(key.startsWith('p_'))return;
    const res=patchCp(cp,text,key,inp.value);
    if(res&&res.error){toast('err','✗',res.error);ok=false;}
    else if(res)cp=res;
  });
  if(!ok)return;

  document.querySelectorAll('input[type=checkbox][data-key]').forEach(cb=>{
    if(cb.checked.toString()===cb.dataset.orig)return;
    const res=patchBool(cp,text,cb.dataset.key,cb.checked);
    if(res)cp=res;
  });

  const out=cpToBytes(cp);
  const a=document.createElement('a');
  a.href=URL.createObjectURL(new Blob([out],{type:'application/octet-stream'}));
  const ext=origFileName.split('.').pop();
  a.download=origFileName.replace('.'+ext,'_edited.'+ext);
  a.click();URL.revokeObjectURL(a.href);
  document.getElementById('changesInfo').classList.remove('on');
  toast('ok','⬇','Скачано: '+a.download);
}

function resetAll(){
  document.querySelectorAll('.inp[data-orig]').forEach(inp=>{inp.value=inp.dataset.orig;inp.classList.remove('dirty');});
  document.querySelectorAll('input[type=checkbox][data-orig]').forEach(cb=>{
    cb.checked=cb.dataset.orig==='true';cb.classList.remove('dirty');
    const lbl=document.getElementById('tl_'+cb.dataset.key);
    if(lbl)lbl.textContent=cb.checked?'Вкл':'Выкл';
  });
  parseAll(origText);filterItems();checkChanges();
  toast('ok','↩','Изменения сброшены');
}

// ── Helpers ──
function esc(s){return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');}
function syntaxHL(json){
  return String(json).replace(/("(\\u[\da-fA-F]{4}|\\[^u]|[^\\"])*"(\s*:)?|\b(true|false|null)\b|-?\d+(?:\.\d*)?(?:[eE][+\-]?\d+)?)/g,m=>{
    if(/^"/.test(m))return m.endsWith(':')?'<span class="jk">'+m+'</span>':'<span class="js">'+m+'</span>';
    if(/true|false/.test(m))return'<span class="jb">'+m+'</span>';
    return'<span class="jn">'+m+'</span>';
  });
}
let tt;
function toast(type,icon,msg){
  const el=document.getElementById('toast');
  document.getElementById('ti').textContent=icon;
  document.getElementById('tm').textContent=msg;
  el.className='toast '+type+' show';
  clearTimeout(tt);tt=setTimeout(()=>el.classList.remove('show'),3500);
}
</script>

<script>
// ── BACKGROUND SHAPES ANIMATION ──────────────────────────────────────
(function(){
  const cv = document.getElementById('bgCanvas');
  const cx = cv.getContext('2d');
  let W, H;

  function resize(){
    W = cv.width = window.innerWidth;
    H = cv.height = window.innerHeight;
  }
  resize();
  window.addEventListener('resize', resize);

  // Shape types and their local point generators
  function triPts(s){ return [[0,-s],[s*.866,s*.5],[-s*.866,s*.5]]; }
  function sqPts(s){ return [[-s,-s],[s,-s],[s,s],[-s,s]]; }
  function hexPts(s){
    const p=[];
    for(let i=0;i<6;i++){const a=i*Math.PI/3-Math.PI/6;p.push([Math.cos(a)*s,Math.sin(a)*s]);}
    return p;
  }
  function diaPts(s){ return [[0,-s*1.2],[s*.8,0],[0,s*1.2],[-s*.8,0]]; }
  const PTS = [triPts, sqPts, hexPts, diaPts];

  // Subtle palette — very low opacity so it stays background
  const PALETTE = [
    { stroke:'rgba(80,160,255,', dot:'rgba(80,160,255,' },
    { stroke:'rgba(255,175,55,',  dot:'rgba(255,175,55,' },
    { stroke:'rgba(55,210,140,',  dot:'rgba(55,210,140,' },
    { stroke:'rgba(200,80,220,',  dot:'rgba(200,80,220,' },
    { stroke:'rgba(255,90,90,',   dot:'rgba(255,90,90,'  },
    { stroke:'rgba(60,220,255,',  dot:'rgba(60,220,255,' },
  ];

  const DOT_R = 3;

  class BgShape {
    constructor(){
      this.reset(true);
    }
    reset(init){
      this.x = Math.random() * W;
      this.y = init ? Math.random() * H : -80;
      this.size = 22 + Math.random() * 40;
      this.angle = Math.random() * Math.PI * 2;
      // Very slow drift — no gravity
      this.vx = (Math.random() - 0.5) * 0.28;
      this.vy = (Math.random() - 0.5) * 0.28;
      this.av = (Math.random() - 0.5) * 0.004;
      this.ptsGen = PTS[Math.floor(Math.random() * PTS.length)];
      this.col = PALETTE[Math.floor(Math.random() * PALETTE.length)];
      // fade in/out
      this.life = 0;           // 0→1 fade in, stays 1, then 1→0 fade out
      this.maxLife = 600 + Math.random() * 800; // frames alive
      this.age = init ? Math.random() * this.maxLife : 0;
      this.dying = false;
    }
    getVerts(){
      const pts = this.ptsGen(this.size);
      const cos = Math.cos(this.angle), sin = Math.sin(this.angle);
      return pts.map(([lx,ly])=>[
        this.x + cos*lx - sin*ly,
        this.y + sin*lx + cos*ly
      ]);
    }
    update(){
      this.x += this.vx;
      this.y += this.vy;
      this.angle += this.av;
      this.age++;

      // Wrap horizontally, float vertically
      if(this.x < -100) this.x = W + 80;
      if(this.x > W+100) this.x = -80;
      if(this.y < -100) this.y = H + 80;
      if(this.y > H+100) this.y = -80;

      // Life alpha
      const fadeFrames = 90;
      if(this.age < fadeFrames){
        this.life = this.age / fadeFrames;
      } else if(this.age > this.maxLife - fadeFrames){
        this.life = Math.max(0, (this.maxLife - this.age) / fadeFrames);
      } else {
        this.life = 1;
      }
      if(this.age >= this.maxLife) this.reset(false);
    }
    draw(){
      const verts = this.getVerts();
      const a = this.life * 0.55; // max opacity — stays subtle as bg
      const sa = this.life * 0.22;

      cx.save();

      // Shape fill
      cx.beginPath();
      cx.moveTo(verts[0][0], verts[0][1]);
      for(let i=1;i<verts.length;i++) cx.lineTo(verts[i][0], verts[i][1]);
      cx.closePath();
      cx.fillStyle = this.col.stroke + (sa * 0.4).toFixed(3) + ')';
      cx.fill();

      // Stroke
      cx.lineWidth = 1;
      cx.strokeStyle = this.col.stroke + a.toFixed(3) + ')';
      cx.stroke();

      // Corner dots
      for(const [vx,vy] of verts){
        // outer ring
        cx.beginPath();
        cx.arc(vx, vy, DOT_R + 2.5, 0, Math.PI*2);
        cx.strokeStyle = this.col.dot + (a*0.35).toFixed(3) + ')';
        cx.lineWidth = 0.8;
        cx.stroke();
        // dot
        cx.beginPath();
        cx.arc(vx, vy, DOT_R, 0, Math.PI*2);
        cx.fillStyle = this.col.dot + a.toFixed(3) + ')';
        cx.fill();
      }

      cx.restore();
    }
  }

  // Subtle dot grid
  function drawGrid(){
    cx.clearRect(0, 0, W, H);
    cx.fillStyle = 'rgba(255,255,255,0.028)';
    const gs = 52;
    for(let x = gs; x < W; x += gs){
      for(let y = gs; y < H; y += gs){
        cx.beginPath();
        cx.arc(x, y, 1, 0, Math.PI*2);
        cx.fill();
      }
    }
  }

  // Init shapes — staggered across the screen
  const COUNT = 18;
  const shapes = Array.from({length: COUNT}, () => new BgShape());

  function loop(){
    drawGrid();
    for(const s of shapes){ s.update(); s.draw(); }
    requestAnimationFrame(loop);
  }
  loop();
})();
</script>
</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>RedRoll – Mini YouTube</title>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap" rel="stylesheet">
<style>
/* ---------- CSS VARIABLES ---------- */
:root{--bg:#fff;--bg2:#f9f9f9;--bg3:#f1f1f1;--text:#030303;--text2:#606060;--border:#e5e5e5;--accent:#ff0033;--ripple:rgba(255,0,51,.25);}
[data-theme="dark"]{--bg:#0f0f0f;--bg2:#181818;--bg3:#212121;--text:#fff;--text2:#aaa;--border:#303030;--accent:#ff0033;--ripple:rgba(255,50,80,.3);}
/* ---------- RESET ---------- */
*{box-sizing:border-box;margin:0;padding:0;font-family:Roboto,Arial;}
body{background:var(--bg);color:var(--text);}
button{border:none;background:none;color:inherit;cursor:pointer;position:relative;overflow:hidden;border-radius:4px;}
button:after{content:'';position:absolute;top:50%;left:50%;width:0;height:0;border-radius:50%;background:var(--ripple);transform:translate(-50%,-50%);transition:.4s;}
button:active:after{width:300px;height:300px;}
input,textarea{width:100%;padding:10px;border:1px solid var(--border);border-radius:4px;background:var(--bg3);color:var(--text);}
img{max-width:100%;display:block;}
a{color:var(--accent);}
/* ---------- HEADER ---------- */
header{position:fixed;top:0;left:0;right:0;height:56px;background:var(--bg2);border-bottom:1px solid var(--border);display:flex;align-items:center;padding:0 16px;z-index:1000;}
#brand{display:flex;align-items:center;gap:8px;font-weight:700;font-size:1.25rem;}
#brand svg{width:32px;height:32px;fill:var(--accent);}
.search-bar{flex:1;max-width:640px;margin:0 auto;display:flex;height:40px;}
.search-bar input{flex:1;border-radius:20px 0 0 20px;border-right:none;padding:0 16px;}
.search-bar button{width:64px;border-radius:0 20px 20px 0;background:var(--bg3);}
.user-box{display:flex;align-items:center;gap:12px;}
#userPic{width:32px;height:32px;border-radius:50%;display:none;}
/* ---------- MAIN ---------- */
main{padding:80px 24px 24px;max-width:1200px;margin:auto;}
#start{text-align:center;margin-top:20vh;}
#start h2{margin-bottom:12px;}
#feed{display:grid;grid-template-columns:repeat(auto-fill,minmax(300px,1fr));gap:16px;margin-top:24px;}
.card{background:var(--bg2);border-radius:12px;overflow:hidden;cursor:pointer;}
.card img{width:100%;aspect-ratio:16/9;object-fit:cover;}
.info{padding:12px;}
.info h3{font-size:1rem;margin-bottom:4px;}
.info p{font-size:.8rem;color:var(--text2);}
/* ---------- WATCH ---------- */
#watch{display:none;margin-top:24px;gap:24px;}
#watch.active{display:grid;grid-template-columns:1fr 400px;}
.player-wrap{position:relative;background:#000;border-radius:12px;overflow:hidden;}
#player{width:100%;aspect-ratio:16/9;}
.controls{position:absolute;bottom:0;left:0;right:0;height:48px;background:linear-gradient(transparent,#0006);display:flex;align-items:center;padding:0 12px;gap:12px;color:#fff;}
.controls button{color:#fff;}
.progress{flex:1;height:4px;background:#fff3;border-radius:2px;cursor:pointer;}
.progress-bar{height:100%;background:var(--accent);border-radius:2px;width:0%;}
#meta{margin-top:12px;}
#actions{display:flex;gap:12px;flex-wrap:wrap;margin:12px 0;}
#actions button{padding:6px 12px;border-radius:18px;background:var(--bg3);}
/* ---------- UPLOAD ---------- */
#uploadModal{display:none;position:fixed;inset:0;background:#0008;z-index:2000;}
#uploadModal>div{background:var(--bg2);max-width:480px;margin:10vh auto;padding:24px;border-radius:12px;}
/* ---------- MOBILE ---------- */
@media(max-width:600px){
  #watch.active{grid-template-columns:1fr;}
  .upnext{display:none;}
}
</style>
</head>
<body>
<!-- ---------- HEADER ---------- -->
<header>
  <div id="brand">
    <svg viewBox="0 0 64 64"><path d="M56 16s-4-4-8-4H16c-4 0-8 4-8 4v32s4 4 8 4h32c4 0 8-4 8-4V16zM26 44V20l20 12z"/></svg>
    <span>RedRoll</span>
  </div>
  <div class="search-bar">
    <input id="q" placeholder="Search videos…" />
    <button id="searchBtn">🔍</button>
  </div>
  <div class="user-box">
    <button id="loginBtn">Sign in</button>
    <img id="userPic" alt="user">
  </div>
</header>

<!-- ---------- MAIN ---------- -->
<main>
  <div id="start">
    <h2>Welcome to RedRoll</h2>
    <p>Sign in with Google and search for videos to begin.</p>
  </div>

  <div id="feed"></div>

  <section id="watch">
    <div>
      <div class="player-wrap">
        <video id="player" controls></video>
        <div class="controls">
          <button id="playBtn">▶</button>
          <div class="progress" id="progress"><div class="progress-bar"></div></div>
          <button id="muteBtn">🔊</button>
          <button id="fullBtn">⛶</button>
          <button id="downBtn">⬇ Download</button>
        </div>
      </div>
      <div id="meta">
        <h3 id="wTitle"></h3>
        <div id="actions">
          <button id="subBtn" style="background:var(--accent);color:#fff;">Subscribe</button>
          <button>👍 Like</button><button>👎 Dislike</button>
          <button id="uploadBtn">⬆ Re-upload this</button>
        </div>
        <p id="wMeta"></p>
        <p id="wDesc"></p>
      </div>
    </div>
    <aside class="upnext">
      <h3>Up next</h3>
      <div id="upnext"></div>
    </aside>
  </section>
</main>

<!-- ---------- UPLOAD MODAL ---------- -->
<div id="uploadModal">
  <div>
    <h3>Upload to YouTube</h3>
    <input id="upTitle" placeholder="Title">
    <textarea id="upDesc" placeholder="Description" rows="4"></textarea>
    <div style="display:flex;gap:12px;margin-top:12px;">
      <button id="upConfirm">Upload</button>
      <button id="upCancel">Cancel</button>
    </div>
  </div>
</div>

<!-- ---------- GOOGLE IDENTITY SERVICES ---------- -->
<script src="https://accounts.google.com/gsi/client"></script>
<script>
/* ========================= CONFIG ========================= */
const CLIENT_ID = "YOUR_GOOGLE_CLIENT_ID";          // ← create OAuth 2.0 ID (Web)
const API_KEY   = "AIzaSyAm1ZKw_C_hKPdQxQ8ArCwZnkWsIXDY1Kk";      // ← enable YouTube Data API v3
const SCOPES    = "https://www.googleapis.com/auth/youtube.upload https://www.googleapis.com/auth/youtube.readonly";
/* ========================= STATE ========================== */
let tokenClient, accessToken, userInfo, currentVideo={}, downloadUrl="";
/* ========================= AUTH =========================== */
function loadGIS(){
  tokenClient = google.accounts.oauth2.initTokenClient({
    client_id: CLIENT_ID,
    scope: SCOPES,
    callback: (tokenResponse)=>{
      accessToken = tokenResponse.access_token;
      fetch("https://www.googleapis.com/oauth2/v1/userinfo?alt=json",{headers:{Authorization:"Bearer "+accessToken}})
        .then(r=>r.json()).then(u=>{
          userInfo=u;
          document.getElementById("loginBtn").style.display="none";
          document.getElementById("userPic").style.display="block";
          document.getElementById("userPic").src=u.picture;
          document.getElementById("start").innerHTML="<h2>Search for anything</h2>";
        });
    },
  });
}
document.getElementById("loginBtn").onclick=()=>{
  if(!tokenClient)loadGIS();
  tokenClient.requestAccessToken();
};
/* ========================= SEARCH ========================= */
document.getElementById("searchBtn").onclick=()=>search();
document.getElementById("q").onkeyup=e=>{if(e.key==="Enter")search()};
function search(){
  if(!accessToken){alert("Sign in first");return;}
  const q=document.getElementById("q").value.trim();
  if(!q)return;
  fetch(`https://www.googleapis.com/youtube/v3/search?part=snippet&type=video&maxResults=20&q=${encodeURIComponent(q)}&key=${API_KEY}`,{
    headers:{Authorization:"Bearer "+accessToken}
  }).then(r=>r.json()).then(data=>{
    const feed=document.getElementById("feed");
    feed.innerHTML="";
    data.items.forEach(item=>feed.appendChild(makeCard(item)));
    document.getElementById("start").style.display="none";
  });
}
function makeCard(item){
  const card=document.createElement("div");card.className="card";
  card.innerHTML=`
    <img src="${item.snippet.thumbnails.medium.url}">
    <div class="info">
      <h3>${item.snippet.title}</h3>
      <p>${item.snippet.channelTitle} • ${new Date(item.snippet.publishedAt).toLocaleDateString()}</p>
    </div>`;
  card.onclick=()=>loadWatch(item.id.videoId,item.snippet);
  return card;
}
/* ========================= WATCH ========================== */
function loadWatch(id,snip){
  currentVideo={id,snip};
  fetch(`https://www.googleapis.com/youtube/v3/videos?part=contentDetails,statistics&id=${id}&key=${API_KEY}`,{
    headers:{Authorization:"Bearer "+accessToken}
  }).then(r=>r.json()).then(det=>{
    const stats=det.items[0].statistics;
    document.getElementById("wTitle").textContent=snip.title;
    document.getElementById("wMeta").textContent=`${parseInt(stats.viewCount).toLocaleString()} views • ${new Date(snip.publishedAt).toLocaleDateString()}`;
    document.getElementById("wDesc").textContent=snip.description;
    /* player src = highest mp4 url via cors-anywhere (or your own proxy) */
    downloadUrl=`https://www.youtube.com/watch?v=${id}`; // we’ll extract real url in download
    const proxy="https://cors-anywhere.herokuapp.com/";
    fetch(proxy+downloadUrl).then(r=>r.text()).then(html=>{
      const match=html.match(/"url":"([^"]*\.mp4[^"]*)"/);
      if(match){
        const url=match[1].replace(/\\/g,"");
        document.getElementById("player").src=url;
        downloadUrl=url;
      }else{
        alert("Could not extract MP4 – video may be restricted.");
      }
    });
    document.getElementById("feed").style.display="none";
    document.getElementById("watch").classList.add("active");
    loadUpnext();
  });
}
/* controls */
const player=document.getElementById("player");
document.getElementById("playBtn").onclick=()=>{if(player.paused){player.play();document.getElementById("playBtn").textContent="❚❚"}else{player.pause();document.getElementById("playBtn").textContent="▶"}};
player.ontimeupdate=()=>document.querySelector(".progress-bar").style.width=100*player.currentTime/player.duration+"%";
document.getElementById("progress").onclick=e=>{
  const r=e.currentTarget.getBoundingClientRect();
  player.currentTime=(e.clientX-r.left)/r.width*player.duration;
};
document.getElementById("muteBtn").onclick=()=>{player.muted=!player.muted;document.getElementById("muteBtn").textContent=player.muted?"🔇":"🔊"};
document.getElementById("fullBtn").onclick=()=>player.requestFullscreen();
/* download */
document.getElementById("downBtn").onclick=()=>{
  const a=document.createElement("a");
  a.href=downloadUrl;
  a.download=currentVideo.snip.title.replace(/[^a-z0-9]/gi,"_")+".mp4";
  a.click();
};
/* re-upload */
document.getElementById("uploadBtn").onclick=()=>{
  document.getElementById("upTitle").value="Re-upload: "+currentVideo.snip.title;
  document.getElementById("upDesc").value=currentVideo.snip.description+"\n\nOriginally from https://youtu.be/"+currentVideo.id;
  document.getElementById("uploadModal").style.display="block";
};
document.getElementById("upCancel").onclick=()=>document.getElementById("uploadModal").style.display="none";
document.getElementById("upConfirm").onclick=()=>{
  const title=document.getElementById("upTitle").value.trim();
  const desc=document.getElementById("upDesc").value.trim();
  if(!title){alert("Title required");return;}
  uploadVideo(title,desc,downloadUrl);
};
/* ========================= UPLOAD ========================= */
async function uploadVideo(title,description,fileUrl){
  document.getElementById("uploadModal").style.display="none";
  /* 1. download blob */
  const blob=await fetch(fileUrl).then(r=>r.blob());
  /* 2. create upload session */
  const metadata={
    snippet:{title,description},
    status:{privacyStatus:"unlisted"} // or "private" / "public"
  };
  const init=await fetch("https://www.googleapis.com/upload/youtube/v3/videos?uploadType=resumable&part=snippet,status",{
    method:"POST",
    headers:{
      Authorization:"Bearer "+accessToken,
      "Content-Type":"application/json",
      "X-Upload-Content-Length":blob.size,
      "X-Upload-Content-Type":"video/mp4"
    },
    body:JSON.stringify(metadata)
  });
  if(!init.ok){alert("Upload init failed");return;}
  const uploadUrl=init.headers.get("Location");
  /* 3. upload blob */
  const up=await fetch(uploadUrl,{
    method:"PUT",
    headers:{"Content-Type":"video/mp4"},
    body:blob
  });
  if(up.ok){
    const data=await up.json();
    alert("Upload complete! Video ID: "+data.id);
  }else{
    alert("Upload error");
  }
}
/* ========================= UP NEXT ======================== */
function loadUpnext(){
  fetch(`https://www.googleapis.com/youtube/v3/search?part=snippet&relatedToVideoId=${currentVideo.id}&type=video&maxResults=6&key=${API_KEY}`,{
    headers:{Authorization:"Bearer "+accessToken}
  }).then(r=>r.json()).then(data=>{
    const u=document.getElementById("upnext");u.innerHTML="";
    data.items.forEach(item=>{
      const d=document.createElement("div");d.className="mini";
      d.innerHTML=`<img src="${item.snippet.thumbnails.medium.url}">
        <div>
          <h4>${item.snippet.title}</h4>
          <p>${item.snippet.channelTitle}</p>
        </div>`;
      d.onclick=()=>loadWatch(item.id.videoId,item.snippet);
      u.appendChild(d);
    });
  });
}
/* ========================= ESC BACK ======================= */
window.addEventListener("keydown",e=>{
  if(e.key==="Escape"&&document.getElementById("watch").classList.contains("active")){
    document.getElementById("watch").classList.remove("active");
    document.getElementById("feed").style.display="grid";
    player.pause();
  }
});
/* ========================= ON LOAD ======================== */
loadGIS();
</script>
</body>
</html>

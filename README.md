<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Zynora Nova</title>
<style>
body{
  margin:0;height:100vh;display:flex;align-items:center;justify-content:center;
  background:linear-gradient(160deg,#dfe6ee,#bfc7d5);
  font-family:Arial,Helvetica,sans-serif;
}

.scene{perspective:2200px;}

.phone{
  width:380px;height:760px;position:relative;
  transform-style:preserve-3d;
  transition:transform .2s linear;
}

/* === GÖVDE & YANLAR === */
.body{
  position:absolute;inset:0;
  transform-style:preserve-3d;
}

.side{
  position:absolute;
  background:#dcdcdc;
}

.side.left{
  width:18px;height:760px;left:-18px;top:0;
  transform:rotateY(90deg);transform-origin:left;
}

.side.right{
  width:18px;height:760px;right:-18px;top:0;
  transform:rotateY(-90deg);transform-origin:right;
}

.side.top{
  width:380px;height:18px;top:-18px;left:0;
  transform:rotateX(-90deg);transform-origin:top;
}

.side.bottom{
  width:380px;height:18px;bottom:-18px;left:0;
  transform:rotateX(90deg);transform-origin:bottom;
}

.face{
  position:absolute;inset:0;border-radius:42px;backface-visibility:hidden;
  box-shadow:0 40px 80px rgba(0,0,0,.45);
}

/* FRONT */
.front{background:#fff;display:flex;align-items:center;justify-content:center;}

.screen{
  width:96%;height:700px;border-radius:34px;
  background:linear-gradient(180deg,#0a0f2c,#1b2b5f);
  position:relative;color:#fff;
  display:flex;align-items:center;justify-content:center;text-align:center;
  overflow:hidden;
}

/* Damla kamera */
.notch{
  position:absolute;top:10px;left:50%;transform:translateX(-50%);
  width:22px;height:26px;background:#000;
  border-radius:0 0 14px 14px;
}

.lock{font-size:22px;opacity:.9;}

/* HOME SCREEN */
.apps{
  width:100%;height:100%;
  display:grid;
  grid-template-columns:repeat(4,1fr);
  gap:18px;
  padding:80px 20px 20px;
}

.app{
  display:flex;flex-direction:column;align-items:center;
  font-size:12px;gap:6px;
}

.icon{
  width:54px;height:54px;border-radius:14px;
  display:flex;align-items:center;justify-content:center;
  font-size:26px;color:#fff;
}

.youtube{background:#ff0000;}
.chrome{background:#4285f4;}
.cameraApp{background:#333;}
.gallery{background:#8e44ad;}

/* BACK */
.back{
  background:#fdfdfd;
  transform:rotateY(180deg) translateZ(18px);
  display:flex;align-items:flex-start;justify-content:center;
}

.camera-ring{
  margin-top:40px;
  width:150px;height:150px;border-radius:50%;
  background:#000;
  display:grid;
  grid-template-columns:repeat(2,1fr);
  grid-template-rows:repeat(2,1fr);
  place-items:center;
  padding:20px;
}

.camera{
  width:42px;height:42px;border-radius:50%;
  background:radial-gradient(circle at top,#666,#000);
}
.camera:nth-child(3){grid-column:1/3;}

.touch{position:absolute;inset:0;cursor:pointer;z-index:10;}

button.flipBtn{
  position:absolute;right:-90px;top:50%;transform:translateY(-50%);
  padding:10px 14px;border:none;border-radius:10px;cursor:pointer;
}
</style>
</head>
<body>

<div class="scene">
  <div class="phone" id="phone">

    <div class="body">
      <div class="side left"></div>
      <div class="side right"></div>
      <div class="side top"></div>
      <div class="side bottom"></div>
    </div>

    <!-- FRONT -->
    <div class="face front">
      <div class="screen" id="screen">
        <div class="notch"></div>
        <div class="lock">🔒 Kilitli Ekran<br><small>Yukarı kaydır</small></div>
        <div class="touch" id="touch"></div>
      </div>
    </div>

    <!-- BACK -->
    <div class="face back">
      <div class="camera-ring">
        <div class="camera"></div>
        <div class="camera"></div>
        <div class="camera"></div>
      </div>
    </div>

  </div>
</div>

<button class="flipBtn" id="flipBtn">Çevir</button>

<script>
const phone = document.getElementById('phone');
const flipBtn = document.getElementById('flipBtn');
const screen = document.getElementById('screen');
const touch = document.getElementById('touch');
let locked = true;

let rotX = 0, rotY = 0, dragging = false, lastX = 0, lastY = 0;

flipBtn.onclick = () => { rotY += 180; update(); };
function update(){ phone.style.transform = `rotateX(${rotX}deg) rotateY(${rotY}deg)`; }

// === MOUSE + TOUCH + POINTER ROTATION ===
phone.addEventListener('mousedown', e=>{ dragging=true; lastX=e.clientX; lastY=e.clientY; });
phone.addEventListener('touchstart', e=>{
  dragging=true;
  lastX=e.touches[0].clientX;
  lastY=e.touches[0].clientY;
},{passive:true});

document.addEventListener('mouseup', ()=>dragging=false);
document.addEventListener('touchend', ()=>dragging=false);

document.addEventListener('mousemove', e=>{
  if(!dragging) return;
  rotY += (e.clientX-lastX)*0.4;
  rotX -= (e.clientY-lastY)*0.4;
  lastX=e.clientX; lastY=e.clientY; update();
});

document.addEventListener('touchmove', e=>{
  if(!dragging) return;
  rotY += (e.touches[0].clientX-lastX)*0.4;
  rotX -= (e.touches[0].clientY-lastY)*0.4;
  lastX=e.touches[0].clientX;
  lastY=e.touches[0].clientY;
  update();
},{passive:true});

function openYouTube(){
  // GERÇEK YOUTUBE IFRAME İZİN VERMEDİĞİ İÇİN
  // SİTENİN İÇİNE GÖMÜLÜ SAHTE (FAKE) AMA BİREBİR KANAL EKRANI
  screen.innerHTML = `
    <div class="notch"></div>
    <div style="width:100%;height:100%;background:#0f0f0f;color:#fff;overflow-y:auto;">
      <div style="padding:12px;font-size:14px;font-weight:bold;">▶ YouTube</div>
      <div style="padding:14px;">
        <div style="background:#222;height:140px;border-radius:12px;margin-bottom:12px;"></div>
        <div style="display:flex;align-items:center;gap:12px;">
          <div style="width:64px;height:64px;border-radius:50%;background:#555;"></div>
          <div>
            <div style="font-size:18px;font-weight:bold;">BSEFE_2012</div>
            <div style="font-size:13px;opacity:.7;">@BSEFE_2012 • Kanal</div>
          </div>
        </div>
        <div style="margin-top:16px;font-size:14px;">Videolar</div>
        <div style="display:grid;grid-template-columns:repeat(2,1fr);gap:10px;margin-top:10px;">
          <div style="background:#333;height:120px;border-radius:10px;"></div>
          <div style="background:#333;height:120px;border-radius:10px;"></div>
          <div style="background:#333;height:120px;border-radius:10px;"></div>
          <div style="background:#333;height:120px;border-radius:10px;"></div>
        </div>
      </div>
    </div>
  `;
}

function ytSearch(){
  const q = document.getElementById('ytSearch').value.trim();
  if(q === '@linefuneral12'){
    screen.innerHTML = `
      <div class="notch"></div>
      <iframe 
        src="https://www.youtube.com/embed?listType=user_uploads&list=linefuneral12" 
        allowfullscreen
        style="width:100%;height:100%;border:none;"
      ></iframe>
    `;
  } else {
    window.open('https://www.youtube.com/results?search_query=' + encodeURIComponent(q), '_blank');
  }
}

touch.onclick = () => {
  if(!locked) return;
  screen.innerHTML = `
    <div class="notch"></div>
    <div class="apps">
      <div class="app" onclick="openYouTube()"><div class="icon youtube">▶</div>YouTube</div>
      <div class="app" onclick="openSimple('Chrome')"><div class="icon chrome">🌐</div>Chrome</div>
      <div class="app" onclick="openSimple('Kamera')"><div class="icon cameraApp">📷</div>Kamera</div>
      <div class="app" onclick="openSimple('Galeri')"><div class="icon gallery">🖼️</div>Galeri</div>
    </div>`;
  locked=false;
};
function openSimple(name){
  screen.innerHTML = `<div class="notch"></div><div style="display:flex;align-items:center;justify-content:center;height:100%;font-size:22px;">${name} Açıldı</div>`;
}

</script>

</body>
</html>

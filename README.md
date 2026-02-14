<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>ICT project</title>
<style>
body {
    margin: 0;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: 'Segoe UI', sans-serif;
    overflow: hidden;
    background: linear-gradient(to top, #ff8fcf, #ffc3e6 40%, #ffe6f7 70%);
    position: relative;
}
.moon { position:absolute; top:60px; right:120px; font-size:70px; filter:drop-shadow(0 0 15px rgba(255,255,255,0.8)); }
.cloud { position:absolute; font-size:40px; opacity:0.4; animation: cloudMove 60s linear infinite; }
.cloud:nth-child(1) { top:120px; } .cloud:nth-child(2) { top:200px; animation-delay:20s; }
@keyframes cloudMove { from{transform:translateX(-200px);} to{transform:translateX(110vw);} }
.bg-star { position:absolute; font-size:14px; opacity:0.6; animation:twinkle 2s infinite alternate; }
.bg-star:nth-child(3){top:80px; left:30%;} .bg-star:nth-child(4){top:150px; left:70%;} .bg-star:nth-child(5){top:220px; left:50%;}
@keyframes twinkle{from{opacity:0.3;} to{opacity:1;}}
.shooting-star{position:absolute;width:120px;height:2px;background:linear-gradient(to right, white, transparent);animation:shoot 5s linear infinite;opacity:0.8;top:100px;left:0;}
@keyframes shoot{0%{transform:translate(-200px,-200px) rotate(45deg);opacity:1;}100%{transform:translate(100vw,100vh) rotate(45deg);opacity:0;}}
.star-string{position:absolute;top:0;width:100%;display:flex;justify-content:space-around;pointer-events:none;}
.star{display:flex;flex-direction:column;align-items:center;animation:sway 3s ease-in-out infinite alternate;}
.line{width:2px;height:90px;background:rgba(255,255,255,0.6);}
.star-icon{font-size:22px;animation:twinkle 2s infinite alternate;}
@keyframes sway{from{transform:rotate(-3deg);}to{transform:rotate(3deg);}}

/* Window container */
.container { width:420px; background:#ffe6f2; border:3px solid #ff66b2; border-radius:12px; box-shadow:0 15px 40px rgba(0,0,0,0.3); z-index:10; position:relative; display:flex; flex-direction:column;}
.window-header { background:#ff66b2; padding:8px; border-bottom:3px solid #ff4da6; display:flex; align-items:center; border-top-left-radius:8px; border-top-right-radius:8px;}
.circle { width:12px; height:12px; border-radius:50%; margin-right:6px;}
.red{background:#ff5f56;} .yellow{background:#ffbd2e;} .green{background:#27c93f;}
.window-body { text-align:center; padding:30px; }
.editable-text { font-size:26px; margin-bottom:25px; color:#cc0066; outline:none; min-height:40px; }
button { padding:12px 30px; font-size:18px; margin:10px; cursor:pointer; border-radius:30px; border:none; transition:0.3s; }
.yes-btn{background:#ff4da6;color:white;} .yes-btn:hover{transform:scale(1.1);}
.no-btn{background:white;color:#ff4da6;border:2px solid #ff4da6;} .no-btn:hover{transform:scale(1.05);}
.popup{display:none;position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);background:white;padding:30px;border-radius:20px;text-align:center;box-shadow:0 0 25px rgba(255,0,128,0.5); z-index:1000;}
.popup img{width:140px; image-rendering:pixelated; margin:15px 0;}
.overlay{display:none; position:fixed;width:100%;height:100%;background:rgba(0,0,0,0.4);top:0;left:0; z-index:999;}
.close-btn{padding:8px 20px;border-radius:20px;border:none;background:#ff4da6;color:white;cursor:pointer;}
</style>
</head>
<body>

<div class="moon">🌙</div>
<div class="cloud">☁</div>
<div class="cloud">☁</div>
<div class="shooting-star"></div>
<div class="bg-star">⭐</div>
<div class="bg-star">✨</div>
<div class="bg-star">⭐</div>

<div class="star-string">
    <div class="star"><div class="line"></div><div class="star-icon">⭐</div></div>
    <div class="star"><div class="line"></div><div class="star-icon">💖</div></div>
    <div class="star"><div class="line"></div><div class="star-icon">✨</div></div>
    <div class="star"><div class="line"></div><div class="star-icon">💗</div></div>
</div>

<!-- PAGE 1 -->
<div class="container" id="page1">
    <div class="window-header"><div class="circle red"></div><div class="circle yellow"></div><div class="circle green"></div></div>
    <div class="window-body">
        <div class="editable-text" id="text1"></div>
        <button class="yes-btn" onclick="handlePage1('yes')">Yes 💖</button>
        <button class="no-btn" onclick="handlePage1('no')">No 💔</button>
    </div>
</div>

<!-- PAGE 2 -->
<div class="container" id="page2" style="display:none; position:relative;">
    <div class="window-header"><div class="circle red"></div><div class="circle yellow"></div><div class="circle green"></div></div>
    <div class="window-body">
        <div class="editable-text" id="text2"></div>
        <button class="yes-btn" id="page2-yes">Yes 💖</button>
        <button class="no-btn" id="page2-no">No 💔</button>
    </div>
</div>

<!-- PAGE 3 -->
<div class="container" id="page3" style="display:none;">
    <div class="window-header"><div class="circle red"></div><div class="circle yellow"></div><div class="circle green"></div></div>
    <div class="window-body">
        <div class="editable-text" id="text3"></div>
    </div>
</div>

<div class="overlay"></div>
<div class="popup" id="popup">
    <div id="popup-message"></div>
    <img id="popup-sticker" src="">
    <button class="close-btn">Close</button>
</div>

<script>
// Typing function
function typeText(element, text, speed=80, callback=null){
    element.innerHTML="";
    let i=0;
    const interval=setInterval(()=>{
        element.innerHTML += text.charAt(i);
        i++;
        if(i>=text.length){
            clearInterval(interval);
            if(callback) callback();
        }
    }, speed);
}

// Start typing when pages show
function showPageWithTyping(pageId, textId, text){
    document.getElementById(pageId).style.display="flex";
    const el=document.getElementById(textId);
    typeText(el,text);
}

// PAGE 1 popup & next page
const popup=document.getElementById("popup");
const overlay=document.querySelector(".overlay");
popup.querySelector(".close-btn").addEventListener("click", ()=>{
    popup.style.display="none";
    overlay.style.display="none";
    if(popup.dataset.next) {
        if(popup.dataset.next=="page2") showPageWithTyping("page2","text2","Do you want to know what I feel?");
    }
});

// PAGE 1 handler
function handlePage1(choice){
    document.getElementById("page1").style.display="none";
    const message=document.getElementById("popup-message");
    const sticker=document.getElementById("popup-sticker");
    if(choice==='yes'){
        message.innerHTML="<h2>Great! Thank you, continue on. 💖</h2>";
        sticker.src="https://i.ibb.co/Fk7G7nxk/12a5093aada76d7b3ad638ab3f8674b8.gif";
    } else {
        message.innerHTML="<h2>Awh, why not? I'm sad... Continue on. 💔</h2>";
        sticker.src="https://i.ibb.co/gbk655Cz/images-8.jpg";
    }
    popup.dataset.next="page2";
    popup.style.display="block";
    overlay.style.display="block";
}

// PAGE 2 buttons
const yesBtn2=document.getElementById("page2-yes");
const noBtn2=document.getElementById("page2-no");
const page2=document.getElementById("page2");
const page3=document.getElementById("page3");

yesBtn2.addEventListener('click', ()=>{
    page2.style.display="none";
    showPageWithTyping("page3","text3","I feel something very special for you... 💝 I have for a while now and I hope you feel the same. I love you so much, I hope to see your face everyday :)");
});

// No button moves continuously
let moveInterval;
noBtn2.addEventListener('mouseenter', ()=>{
    if(moveInterval) return;
    moveInterval=setInterval(()=>{
        const containerRect=page2.getBoundingClientRect();
        const btnWidth=noBtn2.offsetWidth;
        const btnHeight=noBtn2.offsetHeight;
        const x=Math.random()*(containerRect.width - btnWidth);
        const y=Math.random()*(containerRect.height - btnHeight - 30);
        noBtn2.style.position='absolute';
        noBtn2.style.left=`${x}px`;
        noBtn2.style.top=`${y}px`;
    },150);
});
noBtn2.addEventListener('mouseleave', ()=>{
    clearInterval(moveInterval);
    moveInterval=null;
});

// Start typing page 1 on load
window.addEventListener('load', ()=>{
    showPageWithTyping("page1","text1","Do you think I'm cool?");
});
</script>
</body>
</html>

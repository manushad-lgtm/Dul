```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>For Dul 💌</title>
  <style>
    :root{
      --rose1:#ff4f7a;
      --rose2:#ff77a8;
      --rose3:#ffd1dc;
      --ink:#222;
    }

    *{ box-sizing:border-box; }

    body{
      margin:0;
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      min-height:100vh;
      display:grid;
      place-items:center;
      overflow:hidden;

      /* Rose gradient background */
      background:
        radial-gradient(1200px 800px at 18% 20%, rgba(255,255,255,.32), transparent 60%),
        radial-gradient(900px 700px at 82% 28%, rgba(255,255,255,.22), transparent 55%),
        radial-gradient(900px 700px at 50% 90%, rgba(255,255,255,.18), transparent 58%),
        linear-gradient(135deg, var(--rose1), var(--rose2), var(--rose3));
    }

    .card{
      width:min(560px, 92vw);
      background: rgba(255,255,255,.92);
      border: 1px solid rgba(255,255,255,.6);
      border-radius: 28px;
      box-shadow: 0 20px 60px rgba(0,0,0,.18);
      padding: 28px 22px;
      text-align:center;
      position:relative;
      z-index:5;
      backdrop-filter: blur(7px);
    }

    h1{
      margin: 6px 0 10px;
      font-size: clamp(22px, 3.2vw, 32px);
      color: var(--ink);
      letter-spacing: .2px;
    }

    p{
      margin: 0 0 18px;
      color: rgba(0,0,0,.68);
      line-height: 1.35;
      font-size: 15.5px;
    }

    .btnRow{
      display:flex;
      gap:12px;
      justify-content:center;
      flex-wrap:wrap;
      margin-top: 10px;
      position:relative;
      min-height: 64px;
    }

    button{
      border:0;
      padding: 12px 18px;
      border-radius: 14px;
      font-size: 16px;
      cursor:pointer;
      transition: transform .12s ease, filter .12s ease;
      user-select:none;
      -webkit-tap-highlight-color: transparent;
    }

    #yesBtn{
      background: #ff2f6d;
      color: #fff;
      box-shadow: 0 12px 24px rgba(255,47,109,.25);
    }
    #yesBtn:hover{ filter: brightness(1.03); transform: translateY(-1px); }
    #yesBtn:active{ transform: translateY(0px) scale(.99); }

    #noBtn{
      background: #f1f1f1;
      color: #333;
      position:relative;
      transform-origin: center;
    }
    #noBtn:hover{ filter: brightness(.98); }

    .result{
      margin-top: 18px;
      padding: 14px 14px;
      border-radius: 16px;
      background: rgba(255,47,109,.09);
      color: #b1003a;
      font-weight: 650;
      display:none;
    }

    .smallNote{
      margin-top: 10px;
      font-size: 12.5px;
      color: rgba(0,0,0,.55);
    }

    /* Floating hearts */
    .heart{
      position: absolute;
      left: 0;
      top: 0;
      pointer-events:none;
      opacity: 0;
      animation: floatUp linear forwards;
      z-index:1;
      filter: drop-shadow(0 6px 10px rgba(0,0,0,.12));
    }

    @keyframes floatUp{
      0%   { transform: translate(var(--x), 105vh) scale(.6) rotate(0deg); opacity: 0; }
      15%  { opacity: .95; }
      100% { transform: translate(var(--x), -15vh) scale(1.25) rotate(20deg); opacity: 0; }
    }

    /* Confetti canvas sits on top */
    #confettiCanvas{
      position:fixed;
      inset:0;
      z-index:999;
      pointer-events:none;
    }
  </style>
</head>

<body>
  <canvas id="confettiCanvas"></canvas>

  <div class="card">
    <h1>Will you be my Valentine, Dul? 💖</h1>
    <p>I made this just for you 🌹</p>

    <div class="btnRow" id="btnRow">
      <button id="yesBtn">Yes 💕</button>
      <button id="noBtn">No 🙈</button>
    </div>

    <div class="result" id="resultBox">
      YAY! 💗 Okay… now you have to pick: dessert or movie first? 😄
    </div>

    <div class="smallNote">(Tip: Tap the buttons if you’re on mobile)</div>

    <!-- Small soft instrumental music (starts only after clicking YES) -->
    <audio id="bgMusic" preload="auto" loop>
      <source src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_1b3f0e1c4f.mp3?filename=romantic-love-111903.mp3" type="audio/mpeg">
    </audio>
  </div>

  <script>
    // ========= Hearts animation =========
    const HEARTS = ["💖","💗","💘","💕","🌸"];
    function spawnHeart(){
      const heart = document.createElement("div");
      heart.className = "heart";
      heart.textContent = HEARTS[Math.floor(Math.random() * HEARTS.length)];

      const x = Math.random() * 100;       // vw
      const size = 14 + Math.random() * 22; // px
      const duration = 3.5 + Math.random() * 3.5; // s

      heart.style.setProperty("--x", x + "vw");
      heart.style.fontSize = size + "px";
      heart.style.animationDuration = duration + "s";

      document.body.appendChild(heart);
      requestAnimationFrame(() => heart.style.opacity = 1);

      setTimeout(() => heart.remove(), (duration * 1000) + 250);
    }
    setInterval(spawnHeart, 450);

    // ========= No button shrinking + playful dodge =========
    const noBtn = document.getElementById("noBtn");
    let noScale = 1.0;
    let noMoves = 0;

    function moveNoButton(){
      const pad = 18;
      const vw = window.innerWidth;
      const vh = window.innerHeight;

      // Ensure button stays inside viewport
      const bw = noBtn.offsetWidth;
      const bh = noBtn.offsetHeight;

      const x = pad + Math.random() * Math.max(1, (vw - pad*2 - bw));
      const y = pad + Math.random() * Math.max(1, (vh - pad*2 - bh));

      noBtn.style.position = "fixed";
      noBtn.style.left = x + "px";
      noBtn.style.top = y + "px";
    }

    function shrinkNo(){
      noMoves += 1;
      noScale = Math.max(0.35, noScale * 0.86);
      noBtn.style.transform = `scale(${noScale})`;

      if (noMoves === 2) noBtn.textContent = "No? 🥺";
      if (noMoves === 4) noBtn.textContent = "Are you sure? 🙈";
      if (noMoves === 6) noBtn.textContent = "Okay stop 😭";
      if (noMoves === 8) noBtn.textContent = "Fine… maybe? 😳";
    }

    // Desktop hover
    noBtn.addEventListener("mouseenter", () => {
      shrinkNo();
      moveNoButton();
    });

    // Mobile tap
    noBtn.addEventListener("click", (e) => {
      e.preventDefault();
      shrinkNo();
      moveNoButton();
    });

    // ========= Yes button: show result + play music + confetti =========
    const yesBtn = document.getElementById("yesBtn");
    const resultBox = document.getElementById("resultBox");
    const bgMusic = document.getElementById("bgMusic");

    yesBtn.addEventListener("click", async () => {
      resultBox.style.display = "block";
      noBtn.style.display = "none";
      yesBtn.textContent = "YESSS 💖";

      // Start music after user gesture
      try{
        bgMusic.volume = 0.75;
        await bgMusic.play();
      }catch(err){
        console.log("Music blocked by browser:", err);
      }

      // Confetti burst
      confettiBurst();
    });

    // ========= Confetti (no external libraries) =========
    const canvas = document.getElementById("confettiCanvas");
    const ctx = canvas.getContext("2d");
    let confettiPieces = [];
    let confettiRunning = false;

    function resizeCanvas(){
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }
    window.addEventListener("resize", resizeCanvas);
    resizeCanvas();

    function rand(min, max){ return Math.random() * (max - min) + min; }

    function makePiece(){
      const colors = ["#ffffff", "#ff2f6d", "#ffd1dc", "#ff7aa8", "#ffe6ef"];
      return {
        x: canvas.width / 2 + rand(-90, 90),
        y: canvas.height / 2 + rand(-50, 50),
        vx: rand(-9, 9),
        vy: rand(-14, -6),
        g:  rand(0.18, 0.35),
        size: rand(6, 12),
        rot: rand(0, Math.PI * 2),
        vr:  rand(-0.25, 0.25),
        life: rand(70, 110),
        color: colors[Math.floor(Math.random() * colors.length)],
        shape: Math.random() < 0.25 ? "circle" : "rect"
      };
    }

    function confettiBurst(){
      confettiPieces = [];
      for(let i=0;i<190;i++) confettiPieces.push(makePiece());
      confettiRunning = true;
      requestAnimationFrame(tickConfetti);
    }

    function tickConfetti(){
      if(!confettiRunning) return;

      ctx.clearRect(0,0,canvas.width,canvas.height);

      confettiPieces.forEach(p => {
        p.x += p.vx;
        p.y += p.vy;
        p.vy += p.g;
        p.rot += p.vr;
        p.life -= 1;

        ctx.save();
        ctx.translate(p.x, p.y);
        ctx.rotate(p.rot);
        ctx.fillStyle = p.color;

        if(p.shape === "circle"){
          ctx.beginPath();
          ctx.arc(0,0,p.size/2,0,Math.PI*2);
          ctx.fill();
        } else {
          ctx.fillRect(-p.size/2, -p.size/2, p.size, p.size*0.7);
        }
        ctx.restore();
      });

      confettiPieces = confettiPieces.filter(p => p.life > 0 && p.y < canvas.height + 90);

      if(confettiPieces.length > 0){
        requestAnimationFrame(tickConfetti);
      } else {
        confettiRunning = false;
        ctx.clearRect(0,0,canvas.width,canvas.height);
      }
    }
  </script>
</body>
</html>
```

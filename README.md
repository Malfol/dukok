<!DOCTYPE html>
<html lang="sk">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Dukok volá</title>
  <style>
    * { box-sizing: border-box; }
    body, html {
      margin: 0; padding: 0;
      width: 100%; height: 100%;
      font-family: Arial, sans-serif;
      background: white;
      overflow: hidden;
      display: flex; align-items: center; justify-content: center;
      flex-direction: column;
      color: black; user-select: none;
    }

    #easter-egg {
      position: fixed;
      top: 10px; right: 10px;
      opacity: 0.2;
      font-size: 24px;
      cursor: pointer;
      transition: opacity 0.3s ease;
      user-select: none;
    }
    #easter-egg:hover {
      opacity: 0.6;
    }

    #start-screen { text-align: center; }
    #start-screen button {
      padding: 20px 40px;
      font-size: 2rem;
      background: black;
      color: white;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      user-select: none;
    }

    #call-screen { text-align: center; z-index: 1; display: none; }
    #call-screen h1 { font-size: 4rem; margin-bottom: 20px; }
    #call-screen img { max-width: 300px; }
    .buttons {
      margin-top: 30px;
      display: flex; justify-content: center; gap: 40px;
    }
    button {
      width: 80px; height: 80px;
      border-radius: 50%; border: none;
      font-size: 32px; color: white;
      cursor: pointer;
      transition: background-color 0.3s ease, transform 0.3s ease;
      user-select: none;
    }
    .accept { background-color: #34c759; }
    .decline { background-color: #ff3b30; }

    #fullscreen-black, #fullscreen-dukok, #flashbang {
      position: fixed; top: 0; left: 0;
      width: 100vw; height: 100vh;
      display: none; align-items: center; justify-content: center;
      z-index: 999;
    }
    #fullscreen-black { background-color: black; z-index: 1000; }
    #fullscreen-dukok { background-color: black; z-index: 1001; flex-direction: column; }
    #fullscreen-dukok img {
      max-width: 90vw; max-height: 90vh;
      transform-origin: center center;
      position: relative;
      transition: transform 5s ease-in-out;
    }
    #flashbang {
      background: white;
      opacity: 0; pointer-events: none;
      z-index: 1100;
      filter: brightness(4);
      transition: opacity 0.3s ease;
    }

    #hint-text {
      position: fixed;
      bottom: 10px;
      left: 10px;
      font-size: 1.4rem;
      font-weight: bold;
      color: black;
      cursor: pointer;
      user-select: none;
      z-index: 1002;
    }

    #arrow {
      position: fixed;
      top: 5px;
      right: 40px;
      font-size: 40px;
      color: red;
      display: none;
      user-select: none;
      z-index: 1003;
      transform: rotate(45deg);
      /* Šípka ukazuje šikmo hore vpravo */
    }
  </style>
</head>
<body>

  <div id="easter-egg">😏</div>

  <div id="start-screen">
    <button id="start-btn">Odukokuj sa a nájdi easter egg</button>
  </div>

  <div id="call-screen">
    <h1>Zdvihni to</h1>
    <img src="dukok.png" alt="Dukok volá" />
    <div class="buttons">
      <button class="accept" id="accept-btn" title="Prijať hovor">📞</button>
      <button class="decline" id="decline-btn" title="Odmietnuť hovor">📴</button>
    </div>
  </div>

  <div id="fullscreen-black"></div>
  <div id="fullscreen-dukok">
    <img src="dukok.png" alt="Dukok fullscreen" id="dukok-img" />
  </div>
  <div id="flashbang"></div>

  <div id="hint-text">Nevieš nájsť easter egg? Klikni ma.</div>
  <div id="arrow">➚</div>

  <audio id="ringtone" loop>
    <source src="ringtone.mp3" type="audio/mpeg" />
  </audio>
  <audio id="scream">
    <source src="scream.mp3" type="audio/mpeg" />
  </audio>

<script>
  const startBtn = document.getElementById('start-btn');
  const startScreen = document.getElementById('start-screen');
  const acceptBtn = document.getElementById('accept-btn');
  const declineBtn = document.getElementById('decline-btn');
  const callScreen = document.getElementById('call-screen');
  const fullscreenBlack = document.getElementById('fullscreen-black');
  const fullscreenDukok = document.getElementById('fullscreen-dukok');
  const dukokImg = document.getElementById('dukok-img');
  const flashbang = document.getElementById('flashbang');
  const ringtone = document.getElementById('ringtone');
  const scream = document.getElementById('scream');
  const egg = document.getElementById('easter-egg');
  const hintText = document.getElementById('hint-text');
  const arrow = document.getElementById('arrow');

  // Easter egg klik
  egg.onclick = () => {
    document.body.innerHTML = '';
    window.open('https://www.youtube.com/watch?v=m09MmwK659Y&start=23', '_blank');
  };

  startBtn.onclick = () => {
    startScreen.style.display = 'none';
    callScreen.style.display = 'block';
    ringtone.play().catch(() => {
      document.body.addEventListener('click', () => ringtone.play(), { once: true });
    });
  };

  // Zväčšovanie tlačítka prijatia po kliknutí na odmietnuť
  declineBtn.onclick = () => {
    declineBtn.style.display = 'none';
    const grow = setInterval(() => {
      const cw = acceptBtn.offsetWidth, ch = acceptBtn.offsetHeight;
      if (cw >= window.innerWidth && ch >= window.innerHeight) {
        clearInterval(grow);
        Object.assign(acceptBtn.style, {
          width:'100vw', height:'100vh', borderRadius:'0', fontSize:'10vw'
        });
      } else {
        acceptBtn.style.width = (cw * 1.05) + 'px';
        acceptBtn.style.height = (ch * 1.05) + 'px';
        const fs = parseFloat(getComputedStyle(acceptBtn).fontSize) * 1.05;
        acceptBtn.style.fontSize = fs + 'px';
      }
    }, 50);
  };

  // Tlačidlo prijatia hovoru s animáciami a jump scare + po ňom otvorenie videa v novej karte
  acceptBtn.onclick = () => {
    ringtone.pause();
    ringtone.currentTime = 0;
    callScreen.style.display = 'none';

    if (document.documentElement.requestFullscreen) {
      document.documentElement.requestFullscreen();
    }

    fullscreenBlack.style.display = 'flex';

    setTimeout(() => {
      fullscreenBlack.style.display = 'none';
      fullscreenDukok.style.display = 'flex';

      dukokImg.style.transition = 'none';
      dukokImg.style.transform = 'scale(1) translateY(0)';
      flashbang.style.opacity = '0';

      setTimeout(() => {
        dukokImg.style.transition = 'transform 5s ease-in-out';
        dukokImg.style.transform = 'scale(1.5)';
      }, 50);

      setTimeout(() => {
        dukokImg.style.transform = 'scale(20) translateY(-120px)';
        flashbang.style.transition = 'opacity 2s ease';
        flashbang.style.opacity = '1';
        scream.play();

        setTimeout(() => {
          flashbang.style.opacity = '0';
          dukokImg.style.transition = 'transform 1.5s ease';
          dukokImg.style.transform = 'scale(0.4) translateY(0)';

          setTimeout(() => {
            fullscreenDukok.style.display = 'none';

            // Otvori video v novej karte
            window.open('https://www.youtube.com/watch?v=q-Y0bnx6Ndw&list=RDq-Y0bnx6Ndw&start_radio=1', '_blank');
          }, 1500);
        }, 2000);
      }, 5050);
    }, 5000);
  };

  // Hint text klik - ukáže červenú šípku k easter eggu
  hintText.onclick = () => {
    arrow.style.display = 'block';

    // Po 5 sekundách šípka zmizne
    setTimeout(() => {
      arrow.style.display = 'none';
    }, 5000);
  };
</script>

</body>
</html>


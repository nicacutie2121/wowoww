
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Happy Birthday KC!</title>
  <style>
    * {
      box-sizing: border-box;
    }
    body {
      margin: 0;
      background-color: #fff;
      font-family: 'Segoe UI', sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }
    .card {
      width: 300px;
      height: 500px;
      perspective: 1000px;
    }
    .inner-card {
      width: 100%;
      height: 100%;
      position: relative;
      transition: transform 1s;
      transform-style: preserve-3d;
    }
    .flipped {
      transform: rotateY(180deg);
    }
    .page {
      width: 100%;
      height: 100%;
      position: absolute;
      backface-visibility: hidden;
      background: white;
      border: 2px solid #8B0000;
      border-radius: 20px;
      padding: 20px;
      box-shadow: 0 0 20px rgba(139, 0, 0, 0.2);
    }
    .front {
      z-index: 2;
    }
    .back {
      transform: rotateY(180deg);
    }
    h1 {
      color: #8B0000;
      text-align: center;
      margin-top: 10px;
    }
    .cake {
      width: 100%;
      height: 200px;
      position: relative;
      margin-top: 40px;
    }
    .layer {
      width: 200px;
      height: 40px;
      background-color: #fff;
      border: 2px solid #8B0000;
      margin: 0 auto;
      margin-top: 10px;
      border-radius: 10px;
      display: none;
    }
    .candle {
      width: 10px;
      height: 30px;
      background-color: #8B0000;
      margin: 0 auto;
      margin-top: 5px;
      display: none;
    }
    .flame {
      width: 12px;
      height: 12px;
      background: gold;
      border-radius: 50%;
      margin: 0 auto;
      margin-top: 2px;
      animation: flicker 0.2s infinite alternate;
    }
    @keyframes flicker {
      from { opacity: 1; }
      to { opacity: 0.5; }
    }
    .pictures, .message {
      display: none;
    }
    .pictures img {
      width: 100%;
      border-radius: 10px;
      margin-bottom: 10px;
    }
    button {
      display: block;
      margin: 20px auto;
      padding: 10px 20px;
      background-color: #8B0000;
      color: white;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      font-size: 16px;
    }
    button:disabled {
      opacity: 0.5;
      cursor: not-allowed;
    }
  </style>
</head>
<body>

<div class="card">
  <div class="inner-card" id="cardInner">
    <!-- Front Side -->
    <div class="page front">
      <h1>Happy Birthday, KC!</h1>
      <div class="cake" id="cake">
        <div class="layer" id="layer1"></div>
        <div class="layer" id="layer2"></div>
        <div class="layer" id="layer3"></div>
        <div class="candle" id="candle">
          <div class="flame" id="flame"></div>
        </div>
      </div>
      <button id="startBtn">Start Cake!</button>
    </div>

    <!-- Back Side -->
    <div class="page back">
      <div class="pictures" id="pictureSection">
        <h1>🎉 Memories</h1>
        <!-- 🖼️ INSERT YOUR IMAGES HERE -->
        <img src="your-image1.jpg" alt="Memory 1">
        <img src="your-image2.jpg" alt="Memory 2">
        <button onclick="flipToMessage()">Next →</button>
      </div>

      <div class="message" id="messageSection" style="text-align: center;">
        <h1>🎁 Your Gift</h1>
        <div id="giftBox" style="margin: 40px auto; width: 150px; height: 150px; background-color: #8B0000; border-radius: 20px; position: relative; cursor: pointer;">
          <div style="position: absolute; width: 100%; height: 20px; background: white; top: 65px;"></div>
          <p style="color: white; font-weight: bold; position: absolute; width: 100%; top: 110px;">Tap to Open</p>
        </div>
        <div id="giftContents" style="margin-top: 30px; display: none;">
          <p style="color: #8B0000; font-size: 18px;">💋 Kiss</p>
          <p style="color: #8B0000; font-size: 18px;">🔜 Coming soon hehe</p>
          <p style="color: #8B0000; font-size: 18px;">🎁 Anything you want, just tell me</p>
        </div>
      </div>
    </div>
  </div>
</div>

<script>
  const layers = [layer1, layer2, layer3];
  const startBtn = document.getElementById("startBtn");
  const flame = document.getElementById("flame");
  const candle = document.getElementById("candle");
  const cardInner = document.getElementById("cardInner");

  let currentLayer = 0;

  startBtn.addEventListener("click", () => {
    startBtn.disabled = true;
    stackLayers();
  });

  function stackLayers() {
    if (currentLayer < layers.length) {
      layers[currentLayer].style.display = "block";
      currentLayer++;
      setTimeout(stackLayers, 700);
    } else {
      candle.style.display = "block";
      flame.style.display = "block";
      listenForBlow();
    }
  }

  function listenForBlow() {
    navigator.mediaDevices.getUserMedia({ audio: true }).then(stream => {
      const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      const source = audioCtx.createMediaStreamSource(stream);
      const analyser = audioCtx.createAnalyser();
      source.connect(analyser);
      analyser.fftSize = 256;
      const dataArray = new Uint8Array(analyser.frequencyBinCount);

      function checkBlow() {
        analyser.getByteFrequencyData(dataArray);
        const volume = dataArray.reduce((a, b) => a + b) / dataArray.length;

        if (volume > 50) {
          flame.style.display = "none";
          setTimeout(() => {
            cardInner.classList.add("flipped");
            showPictures();
          }, 800);
          stream.getTracks().forEach(track => track.stop());
        } else {
          requestAnimationFrame(checkBlow);
        }
      }

      checkBlow();
    }).catch(err => {
      alert("Mic access needed to blow out the candle!");
    });
  }

  function showPictures() {
    document.getElementById("pictureSection").style.display = "block";
  }

  function flipToMessage() {
    document.getElementById("pictureSection").style.display = "none";
    document.getElementById("messageSection").style.display = "block";

    const box = document.getElementById("giftBox");
    const contents = document.getElementById("giftContents");

    box.addEventListener("click", () => {
      box.style.transition = "all 0.5s";
      box.style.transform = "scale(0.9) rotateX(15deg)";
      setTimeout(() => {
        box.style.display = "none";
        contents.style.display = "block";
      }, 600);
    }, { once: true });
  }
</script>

</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Scrolling Text Centered</title>
  <style>
    html, body {
      height: 100%;
      margin: 0;
      font-family: Arial, sans-serif;
      overflow: hidden;
    }

    body {
      background-image: url('https://media.giphy.com/media/3ohA2ZD9EkeK2AyfdK/giphy.gif');
      background-size: cover;
      background-position: center;
      position: relative;
    }

    .container {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      text-align: center;
      width: 100%;
    }

    .scroll-container {
      width: 80%;
      margin: auto;
      overflow: hidden;
      white-space: nowrap;
      margin-bottom: 20px;
    }

    #scrollText {
      display: inline-block;
      color: red;
      font-size: 2.5em;
      font-weight: bold;
      padding-left: 100%;
      animation: scroll-left 10s linear infinite;
      text-shadow: 2px 2px 4px black;
    }

    @keyframes scroll-left {
      0% { transform: translateX(0%); }
      100% { transform: translateX(-100%); }
    }

    .buttons {
      display: flex;
      justify-content: center;
      gap: 20px;
    }

    button {
      padding: 12px 24px;
      font-size: 1.2em;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
    }

    #yesBtn {
      background-color: pink;
    }

    #noBtn {
      background-color: lightgray;
    }

    /* Hearts */
    .heart {
      position: absolute;
      color: red;
      font-size: 24px;
      animation: floatUp 3s linear forwards;
    }

    @keyframes floatUp {
      0% {
        transform: translateY(0) scale(1);
        opacity: 1;
      }
      100% {
        transform: translateY(-200px) scale(1.5);
        opacity: 0;
      }
    }

    #sadMessage {
      position: fixed;
      bottom: 80px;
      left: 50%;
      transform: translateX(-50%);
      color: white;
      font-size: 2em;
      font-weight: bold;
      text-shadow: 2px 2px 4px black;
      display: none;
      pointer-events: none;
    }

    #sadMessage2 {
      position: fixed;
      top: 80px;
      left: 50%;
      transform: translateX(-50%);
      color: white;
      font-size: 2em;
      font-weight: bold;
      text-shadow: 2px 2px 4px black;
      display: none;
      pointer-events: none;
    }
  </style>
</head>
<body>

  <div class="container">
    <div class="scroll-container">
      <div id="scrollText">
        Bati Na Tayo Please &nbsp;&nbsp;&nbsp;
        Bati Na Tayo Please &nbsp;&nbsp;&nbsp;
        Bati Na Tayo Please &nbsp;&nbsp;&nbsp;
        Bati Na Tayo Please
      </div>
    </div>

    <div class="buttons">
      <button id="yesBtn">Yes</button>
      <button id="noBtn">No</button>
    </div>
  </div>

  <!-- Audio -->
  <audio id="loveSong" src="Downloads/Arthur Nery - Di Nakakasawa (Official Lyric Video).mp3" preload="metadata"></audio>
  <div id="audioStatus" style="position: fixed; bottom: 12px; left: 12px; color: white; font-weight: bold; text-shadow: 0 0 5px black;"></div>
  <div id="sadMessage">Please say yes na bebi ☹️</div>
  <div id="sadMessage2">Dali na pooo! ☹️</div>

  <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@emailjs/browser@3/dist/index.min.js"></script>
  <script>
    emailjs.init("NOAT1O5nNBHPtBijvnTwA");
  </script>
  <script>
    const yesBtn = document.getElementById("yesBtn");
    const noBtn = document.getElementById("noBtn");
    const text = document.getElementById("scrollText");
    const song = document.getElementById("loveSong");
    const audioStatus = document.getElementById("audioStatus");
    const sadMessage = document.getElementById("sadMessage");
    const sadMessage2 = document.getElementById("sadMessage2");
    let noClickCount = 0;
    let noHoverCount = 0;

    const setStatus = (msg) => {
      audioStatus.textContent = msg;
    };

    song.addEventListener("error", (e) => {
      setStatus("Audio error: " + (song.error && song.error.message ? song.error.message : "unknown"));
      console.error("Audio error event", e, song.error);
    });

    yesBtn.addEventListener("click", () => {
      // Change background
      document.body.style.backgroundImage = "url('https://media.tenor.com/j3MVp-l8upYAAAAM/luvyou-love.gif')";

      // Change scrolling text (still repeating)
      text.innerHTML =
        "YESYESYES!! I LOVE YOU SO MUCH BEBII!! &nbsp;&nbsp;&nbsp;" +
        "YESYESYES!! I LOVE YOU SO MUCH BEBII!! &nbsp;&nbsp;&nbsp;" +
        "YESYESYES!! I LOVE YOU SO MUCH BEBII!!";

      // Play song starting at 42 seconds (snap to 42)
      const seekAndPlay = () => {
        if (song.duration > 42) {
          song.currentTime = 42;
        } else {
          song.currentTime = 0;
        }
        song.play();
      };

      if (song.readyState >= 1) {
        seekAndPlay();
      } else {
        song.addEventListener("loadedmetadata", seekAndPlay, { once: true });
      }

      // Start hearts effect
      setInterval(createHeart, 200);

      // Send email
      emailjs.send("service_xdbuadi", "template_8st1i2r", {
        name: "Yes Response",
        time: new Date().toString(),
        message: "She said YES! 🎉"
      }).then(() => {
        console.log("Email sent successfully!");
      }).catch((error) => {
        console.error("Email failed:", error);
      });
    });

    function seekAndPlay() {
      if (song.duration && song.duration > 42) {
        song.currentTime = 42;
        setStatus("Seeking to 42s and playing");
      } else {
        song.currentTime = 0;
        setStatus("Audio too short, playing from start");
      }
      const p = song.play();
      if (p !== undefined) {
        p.then(() => setStatus("Playing at " + song.currentTime.toFixed(1) + "s"))
         .catch((err) => {
           setStatus("Playback failed: " + err.message);
           console.error("Playback rejected", err);
         });
      }
    }

    function moveButton() {
      noBtn.style.position = "absolute";

      const centerX = window.innerWidth / 2;
      const centerY = window.innerHeight / 2;
      const maxDistance = 100;

      const x = centerX - noBtn.offsetWidth / 2 + (Math.random() - 0.5) * maxDistance;
      const y = centerY - noBtn.offsetHeight / 2 + (Math.random() - 0.5) * maxDistance;

      noBtn.style.left = Math.max(0, Math.min(x, window.innerWidth - noBtn.offsetWidth)) + "px";
      noBtn.style.top = Math.max(0, Math.min(y, window.innerHeight - noBtn.offsetHeight)) + "px";
    }

    noBtn.addEventListener("mouseover", () => {
      noHoverCount++;
      const totalAttempts = noHoverCount + noClickCount;
      if (totalAttempts >= 3 && totalAttempts < 6) {
        sadMessage.style.display = "block";
        sadMessage.style.fontSize = "2em";
      } else if (totalAttempts >= 6) {
        sadMessage.style.display = "block";
        sadMessage.style.fontSize = (2 + (totalAttempts - 6) * 0.3) + "em";
        sadMessage2.style.display = "block";
        sadMessage2.style.fontSize = (2 + (totalAttempts - 6) * 0.3) + "em";
      }
      moveButton();
    });

    noBtn.addEventListener("click", () => {
      noClickCount++;
      const totalAttempts = noHoverCount + noClickCount;
      if (totalAttempts >= 3 && totalAttempts < 6) {
        sadMessage.style.display = "block";
        sadMessage.style.fontSize = "2em";
      } else if (totalAttempts >= 6) {
        sadMessage.style.display = "block";
        sadMessage.style.fontSize = (2 + (totalAttempts - 6) * 0.3) + "em";
        sadMessage2.style.display = "block";
        sadMessage2.style.fontSize = (2 + (totalAttempts - 6) * 0.3) + "em";
      }
      moveButton();
    });



    function createHeart() {
      const heart = document.createElement("div");
      heart.classList.add("heart");
      heart.innerHTML = "❤";

      heart.style.left = Math.random() * window.innerWidth + "px";
      heart.style.top = window.innerHeight + "px";

      document.body.appendChild(heart);

      setTimeout(() => {
        heart.remove();
      }, 3000);
    }
  </script>

</body>
</html>

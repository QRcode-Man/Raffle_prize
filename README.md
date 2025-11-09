<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>抽選＆交換サイト</title>
  <style>
    body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background: #f0f8ff;
      margin: 0;
      font-family: Arial, sans-serif;
    }
    .container {
      text-align: center;
      background: #fff;
      padding: 2rem;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
      width: 90%;
      max-width: 400px;
    }
    .title {
      font-size: 2rem;
      margin-bottom: 1rem;
    }
    .result {
      font-size: 2rem;
      color: #0077cc;
      margin: 1rem 0;
    }
    .notice {
      font-size: 1rem;
      color: #d9534f;
      margin-top: 1rem;
      font-weight: bold;
    }
    .btn {
      font-size: 1rem;
      padding: 0.6rem 1.2rem;
      margin-top: 1rem;
      cursor: pointer;
      border: none;
      border-radius: 5px;
      background-color: #0077cc;
      color: white;
    }
    .btn:disabled {
      background-color: #aaa;
      cursor: not-allowed;
    }
    video.fullscreen {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      object-fit: cover;
      z-index: 9999;
      background: black;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="title">抽選結果はこちら！</div>
    <div class="result" id="result">↓下の抽選するを押してください↓</div>
    <div class="notice" id="notice"></div>
    <button class="btn" id="lotteryBtn">抽選する</button>
    <button class="btn" id="exchangeBtn" disabled>交換</button>
    <div id="closeContainer"></div>
  </div>

  <script>
    const resultDiv = document.getElementById('result');
    const noticeDiv = document.getElementById('notice');
    const lotteryBtn = document.getElementById('lotteryBtn');
    const exchangeBtn = document.getElementById('exchangeBtn');
    const closeContainer = document.getElementById('closeContainer');

    /* --- Cookie操作関数 --- */
    function setCookie(name, value, days = 1) {
      const date = new Date();
      date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
      document.cookie = `${name}=${encodeURIComponent(value)};expires=${date.toUTCString()};path=/`;
    }

    function getCookie(name) {
      const match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
      return match ? decodeURIComponent(match[2]) : null;
    }

    /* --- フルスクリーン動画再生 --- */
    function playVideoFullscreen(src) {
      const video = document.createElement('video');
      video.src = src;
      video.autoplay = true;
      video.className = 'fullscreen';
      video.controls = false;
      document.body.appendChild(video);

      if (video.requestFullscreen) {
        video.requestFullscreen().catch(() => {});
      }

      video.addEventListener('ended', () => {
        if (document.fullscreenElement) {
          document.exitFullscreen();
        }
        video.remove();
      });
    }

    /* --- 閉じるボタン --- */
    function createCloseButton() {
      const closeBtn = document.createElement('button');
      closeBtn.textContent = '3秒後に自動で閉じます（またはクリック）';
      closeBtn.className = 'btn';
      closeBtn.onclick = () => {
        window.location.href = 'https://www.instagram.com/scanwithme?igsh=MWJpZ3FzbXJrZjVrcg==';
      };
      closeContainer.appendChild(closeBtn);

      setTimeout(() => {
        if (getCookie('exchanged') === 'true') {
          window.location.href = 'https://www.instagram.com/scanwithme?igsh=MWJpZ3FzbXJrZjVrcg==';
        }
      }, 3000);
    }

    /* --- 抽選処理 --- */
    function runLottery() {
      const storedResult = getCookie('lottery_result');
      const exchanged = getCookie('exchanged');

      if (storedResult) {
        alert("今日はすでに抽選済みです。");
        resultDiv.textContent = storedResult;
        exchangeBtn.disabled = exchanged === 'true';
        lotteryBtn.style.display = 'none';
        noticeDiv.textContent = "本館6F江坂楽器までお越しください（3等の方はテラスのぽーる・りーど・炭すまでお越しください）。景品交換いたします！";
        return;
      }

      const rand = Math.random() * 1000;
      let prize, videoFile;

        if (rand < 150) {
        prize = "✨ 2等！すばらしい！";
        videoFile = "2等.mp4";
      } else {
        prize = "🥳 Sago賞！グレート！";
        videoFile = "Sago.mp4";
      }

      resultDiv.textContent = prize;
      noticeDiv.textContent = "本館6F江坂楽器までお越しください（3等の方はテラスのぽーる・りーど・炭すまでお越しください）。景品交換いたします！";
      exchangeBtn.disabled = false;

      setCookie('lottery_result', prize);
      setCookie('lottery_done', 'true');
      lotteryBtn.style.display = 'none';
      playVideoFullscreen(videoFile);
    }

    /* --- 交換処理 --- */
    exchangeBtn.addEventListener('click', () => {
      const confirmExchange = confirm("交換所以外で交換完了したら交換できなくなりますがよろしいですか？");
      if (!confirmExchange) return;

      resultDiv.textContent = "✅ 景品を交換しました！";
      exchangeBtn.disabled = true;
      setCookie('exchanged', 'true');
      createCloseButton();
    });

    /* --- 初期化 --- */
    window.addEventListener('DOMContentLoaded', () => {
      const storedResult = getCookie('lottery_result');
      const exchanged = getCookie('exchanged');
      const done = getCookie('lottery_done');

      if (storedResult) {
        resultDiv.textContent = storedResult;
        exchangeBtn.disabled = exchanged === 'true';
      }

      if (exchanged === 'true') {
        resultDiv.textContent = "✅ 景品を交換しました！";
        exchangeBtn.disabled = true;
        createCloseButton();
      }

      if (done === 'true') {
        lotteryBtn.style.display = 'none';
      }
    });

    /* --- ボタンイベント登録 --- */
    lotteryBtn.addEventListener('click', runLottery);
  </script>
</body>
</html>

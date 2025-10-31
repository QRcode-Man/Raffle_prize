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
      position: relative;
      z-index: 1;
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
    .rand {
      font-size: 1rem;
      color: #555;
      margin-top: 0.5rem;
    }
    .btn {
      font-size: 1rem;
      padding: 0.5rem 1rem;
      margin-top: 1rem;
      cursor: pointer;
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
    <div class="result" id="result">抽選ボタンを押してください！</div>
    <div class="rand" id="rand">乱数: ...</div>
    <button class="btn" id="lotteryBtn">抽選する</button>
    <button class="btn" id="exchangeBtn" disabled>交換する</button>
    <div id="closeContainer"></div>
  </div>

  <script>
    const resultDiv = document.getElementById('result');
    const randDiv = document.getElementById('rand');
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

      // フルスクリーン要求（クリックイベント内なら動く）
      if (video.requestFullscreen) {
        video.requestFullscreen().catch(() => {});
      } else if (video.webkitRequestFullscreen) {
        video.webkitRequestFullscreen();
      }

      // 再生終了で削除
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
        window.location.href = 'http://abehiroshi.la.coocan.jp';
      };
      closeContainer.appendChild(closeBtn);

      setTimeout(() => {
        window.location.href = 'http://abehiroshi.la.coocan.jp';
      }, 3000);
    }

    /* --- 抽選処理 --- */
    function runLottery() {
      // 既に抽選済みならCookieから復元
      const storedResult = getCookie('lottery_result');
      const storedRand = getCookie('lottery_rand');
      const exchanged = getCookie('exchanged');

      if (storedResult && storedRand) {
        resultDiv.textContent = storedResult;
        randDiv.textContent = `乱数: ${parseFloat(storedRand).toFixed(2)}`;
        exchangeBtn.disabled = exchanged === 'true';
        return alert("今日はすでに抽選済みです。");
      }

      // 新規抽選
      const rand = Math.random() * 1000;
      let prize, videoFile;

      if (rand < 10) {
        prize = "🎉 1等！おめでとう！";
        videoFile = "1等.mp4";
      } else if (rand < 40) {
        prize = "✨ 2等！すばらしい！";
        videoFile = "2等.mp4";
      } else if (rand < 80) {
        prize = "🎁 3等！感謝の気持ちを込めて！";
        videoFile = "3等.mp4";
      } else if (rand < 150) {
        prize = "4等！それなりに";
        videoFile = "4等.mp4";
      } else {
        prize = "残念！はずれ～";
        videoFile = "はずれ.mp4";
      }

      // 結果表示・保存
      resultDiv.textContent = prize;
      randDiv.textContent = `乱数: ${rand.toFixed(2)}`;
      exchangeBtn.disabled = false;
      setCookie('lottery_result', prize);
      setCookie('lottery_rand', rand);

      playVideoFullscreen(videoFile);
    }

    /* --- 交換処理 --- */
    exchangeBtn.addEventListener('click', () => {
      resultDiv.textContent = "✅ 景品を交換しました！";
      exchangeBtn.disabled = true;
      setCookie('exchanged', 'true');
      createCloseButton();
    });

    /* --- イベント登録 --- */
    lotteryBtn.addEventListener('click', runLottery);

    // ページ読み込み時にCookieがあれば復元
    window.addEventListener('DOMContentLoaded', () => {
      const storedResult = getCookie('lottery_result');
      const storedRand = getCookie('lottery_rand');
      const exchanged = getCookie('exchanged');

      if (storedResult && storedRand) {
        resultDiv.textContent = storedResult;
        randDiv.textContent = `乱数: ${parseFloat(storedRand).toFixed(2)}`;
        exchangeBtn.disabled = exchanged === 'true';
      }
    });
  </script>
</body>
</html>

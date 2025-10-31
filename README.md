<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>抽選＆交換サイト（制限なし）</title>
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
    }
    .btn {
      font-size: 1rem;
      padding: 0.5rem 1rem;
      margin-top: 1rem;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="title">抽選結果はこちら！</div>
    <div class="result" id="result">...</div>
    <button class="btn" id="exchangeBtn">交換する</button>
    <div id="closeContainer"></div>
  </div>

  <script>
    function playVideo(src) {
      const video = document.createElement('video');
      video.src = src;
      video.width = 560;
      video.height = 315;
      video.autoplay = true;
      video.controls = true;
      closeContainer.innerHTML = ''; 
      closeContainer.appendChild(video);
    }

    const resultDiv = document.getElementById('result');
    const exchangeBtn = document.getElementById('exchangeBtn');
    const closeContainer = document.getElementById('closeContainer');

    // 「サイトを閉じる」ボタンを作成する関数
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

    // 抽選処理（毎回新しく実行される）
    function runLottery() {
      const rand = Math.random() * 1000;
      let prize;

      if (rand < 10) {
        playVideo("1等.mp4");
        prize = "🎉 1等！おめでとう！";
      } else if (rand < 40) {
        playVideo("2等.mp4");
        prize = "✨ 2等！すばらしい！";
      } else if (rand < 80) {
        playVideo("3等.mp4");
        prize = "🎁 3等！感謝の気持ちを込めて！";
      } else if (rand < 150) {
        playVideo("4等.mp4");
        prize = "4等！それなりに";
      } else {
        playVideo("はずれ.mp4");
        prize = "残念！はずれ～";
      }

      resultDiv.textContent = prize;
    }

    // ページ読み込み時に毎回抽選
    window.addEventListener('DOMContentLoaded', runLottery);

    // 交換処理
    exchangeBtn.addEventListener('click', () => {
      resultDiv.textContent = "✅ 景品を交換しました！";
      exchangeBtn.disabled = true;
      createCloseButton();
    });
  </script>
</body>
</html>

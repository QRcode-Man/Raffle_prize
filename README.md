<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>抽選＆交換サイト（フルスクリーン対応）</title>
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
    <div class="result" id="result">...</div>
    <button class="btn" id="exchangeBtn">交換する</button>
    <div id="closeContainer"></div>
  </div>

  <script>
    const resultDiv = document.getElementById('result');
    const exchangeBtn = document.getElementById('exchangeBtn');
    const closeContainer = document.getElementById('closeContainer');

    // フルスクリーン動画再生関数
    function playVideoFullscreen(src) {
      const video = document.createElement('video');
      video.src = src;
      video.autoplay = true;
      video.className = 'fullscreen';
      video.controls = false;
      document.body.appendChild(video);

      // ブラウザのフルスクリーンAPIを使用
      if (video.requestFullscreen) {
        video.requestFullscreen().catch(() => {});
      } else if (video.webkitRequestFullscreen) {
        video.webkitRequestFullscreen();
      }

      // 再生終了時に自動で削除
      video.addEventListener('ended', () => {
        if (document.fullscreenElement) {
          document.exitFullscreen();
        }
        video.remove();
      });
    }

    // 「サイトを閉じる」ボタンを作成
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

      resultDiv.textContent = prize;
      playVideoFullscreen(videoFile);
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

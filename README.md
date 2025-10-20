<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>抽選＆交換サイト（乱数表示付き）</title>
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
    <div class="rand" id="rand">乱数: --</div>
    <button class="btn" id="exchangeBtn">交換する</button>
    <div id="closeContainer"></div>
  </div>

  <script>
    const resultDiv = document.getElementById('result');
    const randDiv = document.getElementById('rand');
    const exchangeBtn = document.getElementById('exchangeBtn');
    const closeContainer = document.getElementById('closeContainer');

    // Cookieを設定する関数（有効期限1日）
    function setCookie(name, value, days = 1) {
      const date = new Date();
      date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
      document.cookie = `${name}=${encodeURIComponent(value)};expires=${date.toUTCString()};path=/`;
    }

    // Cookieを取得する関数
    function getCookie(name) {
      const match = document.cookie.match(new RegExp('(^| )' + name + '=([^;]+)'));
      return match ? decodeURIComponent(match[2]) : null;
    }

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

    // 🎥 全画面で動画を再生し、終了後に自動で削除する関数
    function playVideo(src) {
      const video = document.createElement('video');
      video.src = src;
      video.autoplay = true;
      video.controls = true;
      video.muted = true;
      video.style.position = 'fixed';
      video.style.top = '0';
      video.style.left = '0';
      video.style.width = '100vw';
      video.style.height = '100vh';
      video.style.objectFit = 'cover';
      video.style.zIndex = '9999';

      video.addEventListener('ended', () => {
        video.remove();
      });

      document.body.appendChild(video);
    }

    // 初期処理
    window.addEventListener('DOMContentLoaded', () => {
      const storedResult = getCookie('lottery_result');
      const storedRand = getCookie('lottery_rand');
      const exchanged = getCookie('exchanged');

      if (storedResult && storedRand) {
        resultDiv.textContent = storedResult;
        randDiv.textContent = `乱数: ${parseFloat(storedRand).toFixed(2)}`;

        if (exchanged !== 'true') {
          // 保存された結果に応じて動画を再生
          if (storedResult.includes("1等")) {
            playVideo("1等.mp4");
          } else if (storedResult.incl

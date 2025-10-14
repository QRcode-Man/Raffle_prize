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

    /* モーダルのスタイル */
    #confirmModal {
      display: none;
      position: fixed;
      top: 0; left: 0;
      width: 100%; height: 100%;
      background-color: rgba(0, 0, 0, 0.5);
      justify-content: center;
      align-items: center;
      z-index: 9999;
    }
    #confirmModal .modal-content {
      background: white;
      padding: 2rem;
      border-radius: 8px;
      text-align: center;
      min-width: 300px;
    }
    #confirmModal .modal-content p {
      margin-bottom: 1.5rem;
    }
    #confirmModal .btn {
      margin: 0 0.5rem;
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

  <!-- 交換確認モーダル -->
  <div id="confirmModal">
    <div class="modal-content">
      <p>本当に景品と交換しますか？</p>
      <button id="confirmOk" class="btn">OK</button>
      <button id="confirmCancel" class="btn">キャンセル</button>
    </div>
  </div>

  <script>
    const resultDiv = document.getElementById('result');
    const randDiv = document.getElementById('rand');
    const exchangeBtn = document.getElementById('exchangeBtn');
    const closeContainer = document.getElementById('closeContainer');

    const confirmModal = document.getElementById('confirmModal');
    const confirmOk = document.getElementById('confirmOk');
    const confirmCancel = document.getElementById('confirmCancel');

    // Cookieを設定する関数（有効期限1日）
    function setCookie(name, value, days = 1) {
      const date = new Date();
      date.setTime(date.getTime() + (days*24*60*60*1000));
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
        window.location.href = 'http://abehiroshi.la.coocan.jp'; // 任意のリダイレクト先
      };
      closeContainer.appendChild(closeBtn);

      setTimeout(() => {
        window.location.href = 'http://abehiroshi.la.coocan.jp';
      }, 3000);
    }

    // 初期処理
    window.addEventListener('DOMContentLoaded', () => {
      const storedResult = getCookie('lottery_result');
      const storedRand = getCookie('lottery_rand');
      const exchanged = getCookie('exchanged');

      // 抽選結果がすでに存在する場合
      if (storedResult && storedRand) {
        resultDiv.textContent = storedResult;
        randDiv.textContent = `乱数: ${parseFloat(storedRand).toFixed(2)}`;

        if (exchanged === 'true') {
          resultDiv.textContent = "✅ 景品を交換しました！";
          exchangeBtn.disabled = true;
          createCloseButton();
        }
      } else {
        // 新規抽選処理
        const rand = Math.random() * 1000;
        randDiv.textContent = `乱数: ${rand.toFixed(2)}`;

        let prize;
        if (rand < 10) {
          prize = "🎉 1等！おめでとう！";
        } else if (rand < 40) {
          prize = "✨ 2等！すばらしい！";
        } else if (rand < 80) {
          prize = "🎁 3等！感謝の気持ちを込めて！";
        } else if (rand < 150) {
          prize = "4等！それなりに";
        } else {
          prize = "残念！はずれ～";
        }

        resultDiv.textContent = prize;
        setCookie('lottery_result', prize);
        setCookie('lottery_rand', rand);
      }
    });

    // 「交換する」ボタンクリックでモーダル表示
    exchangeBtn.addEventListener('click', () => {
      confirmModal.style.display = 'flex';
    });

    // モーダル「OK」ボタン処理
    confirmOk.addEventListener('click', () => {
      resultDiv.textContent = "✅ 景品を交換しました！";
      exchangeBtn.disabled = true;
      setCookie('exchanged', 'true');
      createCloseButton();
      confirmModal.style.display = 'none';
    });

    // モーダル「キャンセル」ボタン処理
    confirmCancel.addEventListener('click', () => {
      confirmModal.style.display = 'none';
    });
  </script>
</body>
</html>

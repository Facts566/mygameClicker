<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Игры с отзывами и оценками</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      max-width: 900px;
      margin: 0 auto;
      padding: 20px;
      background: #f5f5f5;
    }
    header {
      text-align: center;
      margin-bottom: 40px;
    }
    h1 {
      margin: 0;
      padding-bottom: 10px;
    }
    .games-list {
      display: flex;
      flex-wrap: wrap;
      gap: 20px;
      justify-content: center;
    }
    .game-card {
      background: white;
      border-radius: 8px;
      box-shadow: 0 2px 5px rgb(0 0 0 / 0.1);
      width: 200px;
      padding: 15px;
      cursor: pointer;
      transition: transform 0.2s;
    }
    .game-card:hover {
      transform: scale(1.05);
    }
    .game-card h2 {
      margin: 0 0 10px;
      font-size: 1.2em;
    }
    .back-button {
      margin-bottom: 15px;
      cursor: pointer;
      color: #007bff;
      text-decoration: underline;
      display: inline-block;
    }
    form {
      margin-top: 20px;
    }
    label {
      display: block;
      margin: 10px 0 5px;
    }
    textarea {
      width: 100%;
      height: 80px;
      padding: 8px;
      border-radius: 4px;
      border: 1px solid #ccc;
      resize: vertical;
    }
    select {
      padding: 6px;
    }
    button {
      margin-top: 10px;
      padding: 8px 16px;
      border: none;
      background-color: #007bff;
      color: white;
      cursor: pointer;
      border-radius: 4px;
    }
    button:hover {
      background-color: #0056b3;
    }
    .reviews {
      margin-top: 20px;
      background: #fff;
      padding: 10px;
      border-radius: 6px;
      max-height: 300px;
      overflow-y: auto;
    }
    .review-item {
      border-bottom: 1px solid #eee;
      padding: 8px 0;
    }
    .review-item:last-child {
      border-bottom: none;
    }
    .review-rating {
      font-weight: bold;
      color: #f39c12;
    }
    .no-reviews {
      color: #888;
      font-style: italic;
    }
    .play-button {
      background-color: #28a745;
      margin-bottom: 15px;
    }
    .play-button:hover {
      background-color: #1e7e34;
    }
  </style>
</head>
<body>
  <header>
    <h1>Игры с отзывами и оценками</h1>
  </header>

  <main id="main-content">
    <!-- Здесь будет либо список игр, либо страница игры -->
  </main>

  <script>
    // Данные игр (заглушки)
    const games = [
      { id: 1, title: "Игра 1", description: "Описание игры 1...", url: "https://github.com/Facts566/mygameClicker/blob/main/Click%20Circles.html" },
      { id: 2, title: "Игра 2", description: "Описание игры 2...", url: "https://example.com/game2" },
      { id: 3, title: "Игра 3", description: "Описание игры 3...", url: "https://example.com/game3" }
    ];

    const main = document.getElementById('main-content');

    function saveReviews(reviews) {
      localStorage.setItem('gameReviews', JSON.stringify(reviews));
    }

    function loadReviews() {
      const reviews = localStorage.getItem('gameReviews');
      return reviews ? JSON.parse(reviews) : {};
    }

    // Показать список игр
    function showGameList() {
      let html = '<div class="games-list">';
      games.forEach(game => {
        html += `
          <div class="game-card" onclick="showGame(${game.id})">
            <h2>${game.title}</h2>
            <p>${game.description}</p>
          </div>
        `;
      });
      html += '</div>';
      main.innerHTML = html;
    }

    // Показать страницу отдельной игры с формой отзывов
    function showGame(id) {
      const game = games.find(g => g.id === id);
      if (!game) {
        main.innerHTML = '<p>Игра не найдена</p>';
        return;
      }

      const reviewsData = loadReviews();
      const reviews = reviewsData[id] || [];

      let reviewsHtml = '';
      if(reviews.length === 0) {
        reviewsHtml = '<p class="no-reviews">Нет отзывов</p>';
      } else {
        reviewsHtml = reviews.map(r => `
          <div class="review-item">
            <div class="review-rating">Оценка: ${r.rating} / 5</div>
            <div class="review-text">${r.text}</div>
          </div>
        `).join('');
      }

      main.innerHTML = `
        <div class="back-button" onclick="showGameList()">← Назад к списку игр</div>
        <h2>${game.title}</h2>
        <p>${game.description}</p>

        <button class="play-button" onclick="playGame('${game.url}')">Играть</button>

        <form id="review-form">
          <label for="review-text">Ваш отзыв:</label>
          <textarea id="review-text" required></textarea>
          <label for="review-rating">Оценка:</label>
          <select id="review-rating" required>
            <option value="" disabled selected>Выберите оценку</option>
            <option value="1">1 - Плохо</option>
            <option value="2">2</option>
            <option value="3">3 - Средне</option>
            <option value="4">4</option>
            <option value="5">5 - Отлично</option>
          </select>
          <button type="submit">Отправить отзыв</button>
        </form>
        <div class="reviews">
          <h3>Отзывы</h3>
          ${reviewsHtml}
        </div>
      `;

      const form = document.getElementById('review-form');
      form.addEventListener('submit', function(e) {
        e.preventDefault();

        const text = document.getElementById('review-text').value.trim();
        const rating = document.getElementById('review-rating').value;

        if (!text || !rating) return alert('Пожалуйста, заполните отзыв и выберите оценку');

        const newReview = { text, rating: +rating };
        const allReviews = loadReviews();
        if (!allReviews[id]) allReviews[id] = [];
        allReviews[id].push(newReview);
        saveReviews(allReviews);

        // Обновляем список отзывов
        showGame(id);
      });
    }

    // Функция запуска игры
    function playGame(url) {
      // Вариант 1: открыть игру в новой вкладке
      if(url) {
        window.open(url, '_blank');
      } else {
        alert('Ссылка на игру отсутствует.');
      }
    }

    // Инициализация
    showGameList();
  </script>
</body>
</html>

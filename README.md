#<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Лаундж-бар Тмин</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.7.1/dist/leaflet.css" />
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@10/swiper-bundle.min.css" />
  <style>
    /* Сброс */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: 'Segoe UI', sans-serif;
      color: #eee;
      /* Добавлен фон с направлением 135deg, как у картинок меню */
      background:
        url('bar/images/background.jpg') no-repeat center center fixed,
        linear-gradient(135deg, #000000 0%, #4b006e 100%);
      background-size: cover, cover;
      background-attachment: fixed;
      overflow-x: hidden;
      position: relative;
      padding-top: 80px; /* чтобы контент не перекрывался шапкой */
    }
    /* Параллакс-фон */
    body::before {
      content: '';
      position: fixed; inset: 0;
      background: radial-gradient(circle at center, #6e2a7e88, transparent 70%);
      animation: pulse 15s ease-in-out infinite alternate;
      z-index: 0;
      pointer-events: none;
    }
    @keyframes pulse {
      from { opacity: 0.3; } to { opacity: 0.8; }
    }
    /* Шапка */
    header {
      position: fixed; top: 0; left: 0; right: 0;
      background: rgba(0, 0, 0, 0.85);
      padding: 15px 20px;
      z-index: 1000;
      display: flex; align-items: center; justify-content: space-between;
      backdrop-filter: blur(10px);
    }
    .logo {
      font-size: 1.8rem; color: #b678f3;
    }
    nav.desktop-nav {
      display: flex; gap: 20px;
    }
    nav.desktop-nav a {
      color: #ddd; text-decoration: none; padding-bottom: 2px;
      position: relative; transition: color 0.3s;
    }
    nav.desktop-nav a::after {
      content: ''; position: absolute; bottom: 0; left: 0;
      width: 0; height: 2px; background: #b678f3;
      transition: width 0.3s;
    }
    nav.desktop-nav a:hover {
      color: #fff;
    }
    nav.desktop-nav a:hover::after {
      width: 100%;
    }
    .burger {
      display: none;
      flex-direction: column;
      gap: 4px;
      cursor: pointer;
    }
    .burger div {
      width: 24px;
      height: 3px;
      background: #b678f3;
    }
    .mobile-nav {
      position: fixed;
      top: 60px; right: 0;
      width: 70%;
      max-width: 300px;
      background: rgba(0, 0, 0, 0.95);
      transform: translateX(100%);
      transition: transform 0.3s;
      display: flex;
      flex-direction: column;
      gap: 20px;
      padding: 20px;
      z-index: 999;
    }
    .mobile-nav.open {
      transform: translateX(0);
    }
    .mobile-nav a {
      color: #eee;
      font-size: 1.2rem;
      text-decoration: none;
    }
    /* Контент */
    main {
      max-width: 1000px;
      margin: 0 auto;
      padding: 20px;
      position: relative;
      z-index: 1;
    }
    .section {
      margin-bottom: 60px;
      text-align: center;
    }
    .section h2 {
      font-size: 2.4rem;
      color: #e0c3ff;
      margin-bottom: 20px;
      text-shadow: 0 0 15px #b678f3;
    }
    .section p {
      font-size: 1.15rem;
      color: #ddd;
      line-height: 1.6;
      text-shadow: 0 0 8px rgba(0, 0, 0, 0.6);
      max-width: 800px;
      margin: 0 auto 20px;
    }
    #menu-text a {
      color: #e3b0ff;
      text-decoration: underline;
    }
    /* Слайдер меню */
    .swiper {
      width: 100%;
      padding-bottom: 40px;
    }
    .swiper-slide {
      width: 320px !important;
      height: 320px !important;
      border-radius: 15px;
      background: #2a1a4b;
      position: relative;
      overflow: hidden;
      transition: transform 0.3s, box-shadow 0.3s;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    .swiper-slide:hover {
      transform: scale(1.08);
      box-shadow: 0 0 35px #b678f3;
    }
    .swiper-slide img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      border-radius: 15px;
    }
    .caption {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      padding: 14px;
      background: rgba(0, 0, 0, 0.7);
      font-weight: bold;
      color: #fff;
      font-size: 1.2rem;
    }
    /* Галерея */
    .gallery {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
      grid-auto-rows: 220px;
      gap: 20px;
      margin-top: 20px;
      margin-bottom: 40px;
    }
    .gallery a {
      display: block;
      position: relative;
      overflow: hidden;
      border-radius: 8px;
      transition: transform 0.3s, box-shadow 0.3s;
    }
    .gallery a:hover {
      transform: scale(1.03);
      box-shadow: 0 0 30px #b678f3;
      z-index: 2;
    }
    .gallery a img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transition: transform 0.5s;
    }
    /* Форма бронирования */
    .booking-form {
      max-width: 520px;
      margin: 0 auto;
      background: linear-gradient(135deg, #3d2a57, #5a3e8f);
      padding: 35px;
      border-radius: 15px;
      box-shadow: 0 0 25px #b678f3;
      transition: box-shadow 0.3s ease;
    }
    .booking-form:hover {
      box-shadow: 0 0 45px #e0c3ff;
    }
    .booking-form label {
      display: block;
      margin-bottom: 5px;
      color: #ddd;
      font-weight: 600;
    }
    .booking-form input {
      width: 100%;
      padding: 12px;
      margin-bottom: 20px;
      border: none;
      border-radius: 8px;
      background: #412756;
      color: #fff;
    }
    .booking-form button {
      width: 100%;
      padding: 14px;
      border: none;
      border-radius: 8px;
      background: #b678f3;
      color: #22002c;
      font-weight: bold;
      cursor: pointer;
      transition: background 0.3s, box-shadow 0.3s;
      box-shadow: 0 0 20px transparent;
    }
    .booking-form button:hover {
      background: #9e4ac7;
      box-shadow: 0 0 30px #e0c3ff;
    }
    /* Карта и соцсети */
    #map {
      width: 100%;
      height: 300px;
      border-radius: 12px;
      box-shadow: 0 0 20px rgba(182, 120, 243, 0.5);
      margin: 0 auto;
    }
    .socials {
      display: flex;
      justify-content: center;
      gap: 15px;
      margin-top: 20px;
    }
    .socials a {
      padding: 10px 20px;
      background: #412756;
      color: #fff;
      border-radius: 8px;
      text-decoration: none;
      transition: background 0.3s;
    }
    .socials a:hover {
      background: #5a3e8f;
    }
    /* Футер */
    footer {
      text-align: center;
      padding: 20px 0;
      color: #bbb;
    }
    /* Виджет */
    .feedback-widget {
      position: fixed;
      bottom: 20px; right: 20px;
      width: 60px; height: 60px;
      background: #4b006e;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      box-shadow: 0 0 12px #b678f3;
      transition: box-shadow 0.3s ease;
      z-index: 10000;
    }
    .feedback-widget:hover {
      box-shadow: 0 0 30px #e0c3ff;
    }
    .feedback-widget svg {
      fill: #fff;
      width: 30px;
      height: 30px;
    }
    /* Адаптив */
    @media (max-width: 860px) {
      nav.desktop-nav {
        display: none;
      }
      .burger {
        display: flex;
      }
      main {
        padding: 15px;
      }
      .swiper-slide {
        width: 260px !important;
        height: 260px !important;
      }
      .gallery {
        grid-template-columns: 1fr;
        grid-auto-rows: 180px;
      }
    }
  </style>
</head>
<body>
  <header>
    <div class="logo">Лаундж-бар «Тмин»</div>
    <nav class="desktop-nav">
      <a href="#about">О баре</a>
      <a href="#menu">Меню</a>
      <a href="#gallery">Интерьер</a>
      <a href="#booking">Бронирование</a>
      <a href="#contacts">Контакты</a>
    </nav>
    <div class="burger" id="burger">
      <div></div><div></div><div></div>
    </div>
  </header>

  <nav class="mobile-nav" id="mobile-nav">
    <a href="#about" onclick="closeMobileNav()">О баре</a>
    <a href="#menu" onclick="closeMobileNav()">Меню</a>
    <a href="#gallery" onclick="closeMobileNav()">Интерьер</a>
    <a href="#booking" onclick="closeMobileNav()">Бронирование</a>
    <a href="#contacts" onclick="closeMobileNav()">Контакты</a>
  </nav>

  <main>
    <section id="about" class="section">
      <h2>О баре «Тмин»</h2>
      <p>Наш лаундж-бар «Тмин» — это уютное место, где вы можете насладиться изысканными коктейлями, вкусной едой, ароматным кальяном, теплой атмосферой террасы и интересными настольными играми. Мы создаём для вас расслабленную обстановку с атмосферным музыкальным сопровождением и внимательным сервисом.</p>
    </section>

    <section id="menu" class="section">
      <h2>Меню</h2>
      <p id="menu-text">Откройте для себя наши популярные категории:</p>

      <div class="swiper">
        <div class="swiper-wrapper">
          <div class="swiper-slide">
            <img src="bar/images/asian.png" alt="Азиатская еда" />
            <div class="caption">Азиатская еда</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/breakfast.png" alt="Вкусные завтраки" />
            <div class="caption">Вкусные завтраки</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/tea.png" alt="Вкусный чай" />
            <div class="caption">Чай</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/pizza.png" alt="Римская пицца" />
            <div class="caption">Римская пицца</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/cocktails.png" alt="Коктейли" />
            <div class="caption">Коктейли</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/summer.png" alt="Летнее меню" />
            <div class="caption">Летнее меню</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/salads.png" alt="Салаты" />
            <div class="caption">Салаты</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/desserts.png" alt="Десерты" />
            <div class="caption">Десерты</div>
          </div>
          <div class="swiper-slide">
            <img src="bar/images/hookahs.png" alt="Кальяны" />
            <div class="caption">Кальяны</div>
          </div>
        </div>
        <!-- Добавим навигацию стрелками -->
        <div class="swiper-button-next" style="color:#b678f3"></div>
        <div class="swiper-button-prev" style="color:#b678f3"></div>
        <!-- И пагинацию -->
        <div class="swiper-pagination"></div>
      </div>
    </section>

    <section id="gallery" class="section">
      <h2>Галерея интерьера</h2>
      <div class="gallery">
        <a href="bar/images/interior1.jpg" target="_blank"><img src="bar/images/interior1.jpg" alt="Интерьер 1" /></a>
        <a href="bar/images/interior2.jpg" target="_blank"><img src="bar/images/interior2.jpg" alt="Интерьер 2" /></a>
        <a href="bar/images/interior3.jpg" target="_blank"><img src="bar/images/interior3.jpg" alt="Интерьер 3" /></a>
        <a href="bar/images/interior4.jpg" target="_blank"><img src="bar/images/interior4.jpg" alt="Интерьер 4" /></a>
      </div>
    </section>

    <section id="booking" class="section">
      <h2>Бронирование</h2>
      <form class="booking-form" onsubmit="event.preventDefault(); alert('Спасибо за вашу заявку! Мы свяжемся с вами.');">
        <label for="name">Имя</label>
        <input id="name" name="name" type="text" placeholder="Ваше имя" required />
        <label for="phone">Телефон</label>
        <input id="phone" name="phone" type="tel" placeholder="+7 (___) ___-__-__" required />
        <label for="date">Дата и время</label>
        <input id="date" name="date" type="datetime-local" required />
        <button type="submit">Забронировать</button>
      </form>
    </section>

    <section id="contacts" class="section">
      <h2>Контакты</h2>
      <div id="map"></div>
      <div class="socials">
        <a href="https://vk.com" target="_blank" rel="noopener noreferrer">VK</a>
        <a href="https://instagram.com" target="_blank" rel="noopener noreferrer">Instagram</a>
        <a href="https://t.me" target="_blank" rel="noopener noreferrer">Telegram</a>
      </div>
    </section>
  </main>

  <div class="feedback-widget" title="Обратная связь" onclick="openFeedback()">
    <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 22c5.421 0 9.828-4.516 9.828-10.086S17.422 1.83 12 1.83 2.172 6.346 2.172 11.914 6.579 22 12 22zm0-18.418c4.789 0 8.678 4.002 8.678 8.968 0 4.965-3.889 8.967-8.678 8.967-4.79 0-8.678-4.002-8.678-8.967 0-4.966 3.888-8.968 8.678-8.968zM12 7.5a1.5 1.5 0 110 3 1.5 1.5 0 010-3zm1.5 7.5h-3v-1.5h3v1.5z"/></svg>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/swiper@10/swiper-bundle.min.js"></script>
  <script src="https://unpkg.com/leaflet@1.7.1/dist/leaflet.js"></script>
  <script>
    // Мобильное меню
    const burger = document.getElementById('burger');
    const mobileNav = document.getElementById('mobile-nav');
    burger.addEventListener('click', () => {
      mobileNav.classList.toggle('open');
    });
    function closeMobileNav() {
      mobileNav.classList.remove('open');
    }

    // Инициализация слайдера Swiper
    const swiper = new Swiper('.swiper', {
      slidesPerView: 'auto',
      spaceBetween: 20,
      loop: true,
      navigation: {
        nextEl: '.swiper-button-next',
        prevEl: '.swiper-button-prev',
      },
      pagination: {
        el: '.swiper-pagination',
        clickable: true,
      },
    });

    // Инициализация карты Leaflet
    const map = L.map('map').setView([55.751244, 37.618423], 13);
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      maxZoom: 19,
      attribution: '&copy; OpenStreetMap contributors',
    }).addTo(map);
    L.marker([55.751244, 37.618423]).addTo(map)
      .bindPopup('Лаундж-бар «Тмин»')
      .openPopup();

    // Обратная связь
    function openFeedback() {
      alert('Спасибо за ваш отзыв! Мы скоро с вами свяжемся.');
    }
  </script>
</body>
</html>

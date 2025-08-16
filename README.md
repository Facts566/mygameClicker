<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Игра: Кликай и Зарабатывай</title>
<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
  }
  body, html {
    height: 100%;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: white;
    overflow: hidden;
    color: #333;
    transition: background 0.5s ease;
  }
  #game-container {
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }
  #moneyDisplay, #gemsDisplay {
    position: fixed;
    top: 10px;
    font-size: 20px;
    font-weight: bold;
    user-select: none;
    color: #333;
    background: rgba(255, 255, 255, 0.8);
    padding: 10px 15px;
    border-radius: 10px;
    border: 2px solid rgba(0, 0, 0, 0.2);
    text-shadow: 0 0 2px rgba(255, 255, 255, 0.8);
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    z-index: 100;
  }
  #moneyDisplay {
    left: 10px;
  }
  #gemsDisplay {
    top: 60px;
    left: 10px;
  }
  #topButtons {
    position: fixed;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 15px;
    z-index: 100;
  }
  .top-btn {
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    user-select: none;
    border: none;
    border-radius: 50px;
    color: white;
    transition: all 0.3s;
    font-weight: bold;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    background: linear-gradient(135deg, #6a11cb, #2575fc);
    text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
  }
  .top-btn:hover {
    transform: translateY(-3px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
  }
  #shopButton {
    background: linear-gradient(135deg, #2575fc, #6a11cb);
  }
  #resetButton {
    background: linear-gradient(135deg, #ff512f, #dd2476);
  }
  #exchangeButton {
    background: linear-gradient(135deg, #00b09b, #96c93d);
  }
  #mainButton {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 180px;
    height: 180px;
    font-size: 24px;
    font-weight: bold;
    border-radius: 50%;
    border: 4px solid rgba(0, 0, 0, 0.2);
    background: white;
    color: black;
    cursor: pointer;
    user-select: none;
    transition: transform 0.3s, box-shadow 0.3s;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1), 
                0 0 0 10px rgba(0, 0, 0, 0.05),
                inset 0 0 10px rgba(0, 0, 0, 0.1);
    text-shadow: 0 1px 2px rgba(0, 0, 0, 0.2);
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    z-index: 10;
  }
  #mainButton:active {
    transform: translate(-50%, -50%) scale(0.95);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.05), 
                0 0 0 8px rgba(0, 0, 0, 0.03),
                inset 0 0 5px rgba(0, 0, 0, 0.1);
  }
  #mainButton::after {
    content: '';
    position: absolute;
    top: -10px;
    left: -10px;
    right: -10px;
    bottom: -10px;
    border-radius: 50%;
    border: 2px solid rgba(0, 0, 0, 0.1);
    animation: pulse 2s infinite;
    z-index: -1;
  }
  @keyframes pulse {
    0% { transform: scale(0.8); opacity: 0.7; }
    50% { transform: scale(1.1); opacity: 0.3; }
    100% { transform: scale(0.8); opacity: 0.7; }
  }
  .click-effect {
    position: absolute;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: rgba(0, 0, 0, 0.2);
    pointer-events: none;
    animation: clickAnimation 0.5s forwards;
  }
  @keyframes clickAnimation {
    0% { transform: scale(0.5); opacity: 1; }
    100% { transform: scale(3); opacity: 0; }
  }
  #shopOverlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.8);
    display: none;
    justify-content: center;
    align-items: center;
    z-index: 1000;
    backdrop-filter: blur(5px);
  }
  #shop {
    background: linear-gradient(145deg, #2c3e50, #4a6491);
    padding: 30px;
    border-radius: 20px;
    width: 360px;
    max-width: 90vw;
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.5);
    max-height: 80vh;
    overflow-y: auto;
    position: relative;
    border: 2px solid #3498db;
  }
  #shop h2, #shop h3 {
    margin-top: 0;
    margin-bottom: 20px;
    text-align: center;
    color: #f1c40f;
    text-shadow: 0 2px 4px rgba(0, 0, 0, 0.5);
  }
  #shop h3 {
    margin-top: 25px;
    border-bottom: 2px solid #3498db;
    padding-bottom: 10px;
    color: #3498db;
  }
  .shop-item {
    display: flex;
    align-items: center;
    margin-bottom: 15px;
    padding: 12px;
    border-radius: 10px;
    background: rgba(255, 255, 255, 0.1);
    transition: all 0.3s;
    border: 1px solid rgba(255, 255, 255, 0.2);
  }
  .shop-item:hover {
    background: rgba(255, 255, 255, 0.15);
    transform: translateX(5px);
  }
  .shop-item.selected {
    border-color: #2ecc71;
    background: rgba(46, 204, 113, 0.15);
  }
  .colorBox {
    width: 50px;
    height: 50px;
    border-radius: 8px;
    margin-right: 15px;
    flex-shrink: 0;
    box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.5), 0 0 10px rgba(255, 255, 255, 0.3);
    border: 2px solid rgba(255, 255, 255, 0.5);
  }
  #closeShop {
    position: absolute;
    top: 15px;
    right: 15px;
    width: 35px;
    height: 35px;
    cursor: pointer;
    background: rgba(231, 76, 60, 0.8);
    border: none;
    border-radius: 50%;
    padding: 0;
    z-index: 1001;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.3);
    transition: all 0.3s;
  }
  #closeShop:hover {
    background: #e74c3c;
    transform: rotate(90deg);
  }
  #closeShop::before,
  #closeShop::after {
    content: '';
    position: absolute;
    width: 20px;
    height: 3px;
    background-color: white;
  }
  #closeShop::before {
    transform: rotate(45deg);
  }
  #closeShop::after {
    transform: rotate(-45deg);
  }
  .item-info {
    flex-grow: 1;
    font-weight: 600;
    font-size: 18px;
    color: white;
  }
  .item-price {
    font-weight: normal;
    font-size: 16px;
    color: #f1c40f;
    margin-top: 5px;
  }
  .shop-item.bought .item-price {
    color: #2ecc71;
  }
  #message {
    position: fixed;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(46, 204, 113, 0.9);
    color: white;
    padding: 12px 25px;
    border-radius: 30px;
    font-size: 16px;
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.5s;
    user-select: none;
    z-index: 1100;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
    text-align: center;
    max-width: 90%;
  }
  #rouletteOverlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.9);
    display: none;
    justify-content: center;
    align-items: center;
    z-index: 1200;
    backdrop-filter: blur(10px);
  }
  #rouletteContainer {
    background: linear-gradient(145deg, #1a1a2e, #16213e);
    padding: 30px;
    border-radius: 20px;
    width: 500px;
    max-width: 90vw;
    text-align: center;
    position: relative;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.7);
    border: 3px solid #6a0dad;
  }
  #rouletteWheel {
    width: 400px;
    height: 400px;
    border-radius: 50%;
    position: relative;
    margin: 30px auto;
    overflow: hidden;
    border: 15px solid #5D3FD3;
    background: #2c1a4a;
    transform: rotate(0deg);
    transition: transform 4s cubic-bezier(0.34, 1.56, 0.64, 1);
    box-shadow: 0 0 30px rgba(93, 63, 211, 0.7);
  }
  .roulette-sector {
    position: absolute;
    width: 50%;
    height: 50%;
    left: 50%;
    top: 0;
    transform-origin: left bottom;
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    border: 1px solid rgba(255, 255, 255, 0.2);
  }
  .sector-content {
    position: absolute;
    z-index: 2;
    color: white;
    padding: 5px;
    border-radius: 5px;
    background: rgba(0, 0, 0, 0.3);
    width: 80px;
    text-align: center;
    font-size: 16px;
    font-weight: bold;
    text-shadow: 0 1px 2px rgba(0,0,0,0.8);
    transform-origin: center center;
  }
  .roulette-pointer {
    position: absolute;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    width: 0;
    height: 0;
    border-left: 25px solid transparent;
    border-right: 25px solid transparent;
    border-top: 50px solid #e74c3c;
    z-index: 10;
    filter: drop-shadow(0 0 10px rgba(231, 76, 60, 0.8));
  }
  #spinButton {
    padding: 15px 40px;
    font-size: 20px;
    background: linear-gradient(to right, #00b09b, #96c93d);
    color: white;
    border: none;
    border-radius: 50px;
    cursor: pointer;
    margin-top: 20px;
    transition: all 0.3s;
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
    font-weight: bold;
    text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
  }
  #spinButton:hover {
    transform: translateY(-3px);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
  }
  #spinButton:disabled {
    background: linear-gradient(to right, #7f8c8d, #95a5a6);
    cursor: not-allowed;
    transform: none;
    box-shadow: none;
  }
  #rouletteTimer {
    margin-top: 15px;
    font-size: 18px;
    color: #f1c40f;
    font-weight: bold;
    min-height: 24px;
    text-shadow: 0 0 5px rgba(0, 0, 0, 0.8);
  }
  #closeRoulette {
    position: absolute;
    top: 20px;
    right: 20px;
    width: 40px;
    height: 40px;
    cursor: pointer;
    background: rgba(231, 76, 60, 0.8);
    border: none;
    border-radius: 50%;
    font-size: 24px;
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.3);
    transition: all 0.3s;
  }
  #closeRoulette:hover {
    background: #e74c3c;
    transform: rotate(90deg);
  }
  .coins-animation {
    position: absolute;
    font-size: 24px;
    pointer-events: none;
    animation: coinFloat 1.5s forwards;
    z-index: 5;
    color: #333;
  }
  @keyframes coinFloat {
    0% { transform: translate(0, 0); opacity: 1; }
    100% { transform: translate(0, -100px); opacity: 0; }
  }
  
  /* Кнопка рулетки в левом нижнем углу */
  #rouletteButton {
    position: fixed;
    left: 10px;
    bottom: 10px;
    width: 60px;
    height: 60px;
    padding: 0;
    border-radius: 50%;
    font-size: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(135deg, #834d9b, #d04ed6);
    z-index: 100;
    cursor: pointer;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    transition: all 0.3s;
    color: white;
    border: none;
  }
  
  #rouletteButton:hover {
    transform: translateY(-3px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
  }
  
  /* Стили для кнопки ежедневной награды */
  #dailyRewardButton {
    position: fixed;
    right: 10px;
    bottom: 10px;
    width: 60px;
    height: 60px;
    padding: 0;
    border-radius: 50%;
    font-size: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(135deg, #FFD700, #FFA500);
    z-index: 100;
    cursor: pointer;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    transition: all 0.3s;
    color: white;
    border: none;
  }
  
  #dailyRewardButton:disabled {
    background: linear-gradient(135deg, #b3b3b3, #808080);
    cursor: not-allowed;
  }
  
  #dailyRewardButton:hover:not(:disabled) {
    transform: translateY(-3px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.4);
  }

  /* Окно ежедневной награды */
  #dailyRewardOverlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.9);
    display: none;
    justify-content: center;
    align-items: center;
    z-index: 1300;
    backdrop-filter: blur(10px);
  }
  
  #dailyRewardContainer {
    background: linear-gradient(145deg, #1a1a2e, #16213e);
    padding: 30px;
    border-radius: 20px;
    width: 400px;
    max-width: 90vw;
    text-align: center;
    position: relative;
    box-shadow: 0 20px 40px rgba(0, 0, 0, 0.7);
    border: 3px solid #FFD700;
  }
  
  #dailyRewardTitle {
    color: #FFD700;
    margin-bottom: 20px;
    text-shadow: 0 0 10px rgba(255, 215, 0, 0.5);
  }
  
  #dailyRewardAmount {
    font-size: 48px;
    font-weight: bold;
    color: white;
    margin: 20px 0;
    text-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
  }
  
  #dailyRewardDay {
    font-size: 20px;
    color: #f1c40f;
    margin-bottom: 30px;
  }
  
  #closeDailyReward {
    position: absolute;
    top: 15px;
    right: 15px;
    width: 35px;
    height: 35px;
    cursor: pointer;
    background: rgba(231, 76, 60, 0.8);
    border: none;
    border-radius: 50%;
    padding: 0;
    z-index: 1001;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.3);
    transition: all 0.3s;
  }
  
  #closeDailyReward:hover {
    background: #e74c3c;
    transform: rotate(90deg);
  }
  
  #closeDailyReward::before,
  #closeDailyReward::after {
    content: '';
    position: absolute;
    width: 20px;
    height: 3px;
    background-color: white;
  }
  
  #closeDailyReward::before {
    transform: rotate(45deg);
  }
  
  #closeDailyReward::after {
    transform: rotate(-45deg);
  }
  
  #claimDailyReward {
    padding: 15px 40px;
    font-size: 20px;
    background: linear-gradient(to right, #FFD700, #FFA500);
    color: #333;
    border: none;
    border-radius: 50px;
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: 0 6px 15px rgba(0, 0, 0, 0.3);
    font-weight: bold;
    text-shadow: 0 1px 2px rgba(255, 255, 255, 0.5);
  }
  
  #claimDailyReward:hover {
    transform: translateY(-3px);
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
  }

  @media (max-width: 768px) {
    #topButtons {
      flex-wrap: wrap;
      justify-content: center;
      top: 5px;
    }
    .top-btn {
      padding: 8px 15px;
      font-size: 14px;
    }
    #mainButton {
      width: 150px;
      height: 150px;
      font-size: 20px;
    }
    #rouletteWheel {
      width: 300px;
      height: 300px;
    }
    #rouletteButton {
      width: 50px;
      height: 50px;
      font-size: 24px;
    }
    #dailyRewardButton {
      width: 50px;
      height: 50px;
      font-size: 24px;
    }
  }
</style>
</head>
<body>
<div id="game-container">
  <div id="moneyDisplay">💰Gold Coin💰: 0</div>
  <div id="gemsDisplay">💎Cosmo Coin💎: 0</div>
  
  <div id="topButtons">
    <button id="shopButton" class="top-btn" title="Открыть магазин">Магазин</button>
    <button id="exchangeButton" class="top-btn" title="Обменять золото на драгоценности">Обменник</button>
    <button id="resetButton" class="top-btn" title="Сбросить статистику">Сброс</button>
  </div>
  
  <!-- Кнопка рулетки в левом нижнем углу с иконкой -->
  <button id="rouletteButton" title="Крутить рулетку (доступно каждые 8 часов)">🎰</button>
  
  <!-- Кнопка ежедневной награды в правом нижнем углу -->
  <button id="dailyRewardButton" title="Ежедневная награда">📅</button>
  
  <button id="mainButton" title="Кликай и зарабатывай">Кликай<br>и Зарабатывай</button>
  
  <div id="shopOverlay" aria-hidden="true" style="display:none;">
    <div id="shop" role="dialog" aria-modal="true" aria-labelledby="shopTitle">
      <button id="closeShop" aria-label="Закрыть магазин"></button>
      <h2 id="shopTitle">Магазин скинов</h2>
      <div id="skinsList"></div>
      <h3>Магазин ящиков</h3>
      <div id="boxesList"></div>
      <h3>Магазин званий</h3>
      <div id="titlesList"></div>
      <h3>Магазин фонов</h3>
      <div id="backgroundsList"></div>
    </div>
  </div>
  
  <div id="rouletteOverlay" aria-hidden="true" style="display:none;">
    <div id="rouletteContainer" role="dialog" aria-modal="true" aria-labelledby="rouletteTitle">
      <button id="closeRoulette" aria-label="Закрыть рулетку">×</button>
      <h2 id="rouletteTitle">Рулетка</h2>
      <div id="rouletteWheel"></div>
      <div class="roulette-pointer"></div>
      <button id="spinButton">Крутить</button>
      <div id="rouletteTimer"></div>
    </div>
  </div>
  
  <!-- Окно ежедневной награды -->
  <div id="dailyRewardOverlay" aria-hidden="true" style="display:none;">
    <div id="dailyRewardContainer">
      <button id="closeDailyReward" aria-label="Закрыть"></button>
      <h2 id="dailyRewardTitle">Ежедневная Награда!</h2>
      <div id="dailyRewardAmount">+100💰</div>
      <div id="dailyRewardDay">День 1/7</div>
      <button id="claimDailyReward" class="top-btn">Забрать Награду</button>
    </div>
  </div>
  
  <div id="message" role="alert" aria-live="assertive" style="opacity:0;"></div>
</div>

<script>
(() => {
  const moneyDisplay = document.getElementById('moneyDisplay');
  const gemsDisplay = document.getElementById('gemsDisplay');
  const mainButton = document.getElementById('mainButton');
  const shopButton = document.getElementById('shopButton');
  const resetButton = document.getElementById('resetButton');
  const exchangeButton = document.getElementById('exchangeButton');
  const rouletteButton = document.getElementById('rouletteButton');
  const dailyRewardButton = document.getElementById('dailyRewardButton');
  const shopOverlay = document.getElementById('shopOverlay');
  const skinsList = document.getElementById('skinsList');
  const boxesList = document.getElementById('boxesList');
  const titlesList = document.getElementById('titlesList');
  const backgroundsList = document.getElementById('backgroundsList');
  const closeShop = document.getElementById('closeShop');
  const message = document.getElementById('message');
  const rouletteOverlay = document.getElementById('rouletteOverlay');
  const rouletteWheel = document.getElementById('rouletteWheel');
  const spinButton = document.getElementById('spinButton');
  const rouletteTimer = document.getElementById('rouletteTimer');
  const closeRoulette = document.getElementById('closeRoulette');
  const dailyRewardOverlay = document.getElementById('dailyRewardOverlay');
  const dailyRewardAmount = document.getElementById('dailyRewardAmount');
  const dailyRewardDay = document.getElementById('dailyRewardDay');
  const claimDailyReward = document.getElementById('claimDailyReward');
  const closeDailyReward = document.getElementById('closeDailyReward');
  const gameContainer = document.getElementById('game-container');

  let money = 0;
  let gems = 0;
  let currentSkin = null;
  let currentTitle = null;
  let currentBackground = null;
  
  // Переменные для ежедневных наград
  let lastDailyReward = null;
  let currentDailyDay = 1;
  const dailyRewards = [100, 500, 1000, 5000, 10000, 50000, 100000];

  const skins = [
    {name: 'Белый', color: '#FFFFFF', price: 0, bought: true},
    {name: 'Чёрный', color: '#000000', price: 100, bought: false},
    {name: 'Светло-Розовый', color: '#FDD4CE', price: 200, bought: false},
    {name: 'Бордовый', color: '#502423', price: 500, bought: false},
    {name: 'Оранжевый', color: '#FFB973', price: 1000, bought: false},
    {name: 'Тёмно-Оранжевый', color: '#472000', price: 2000, bought: false},
    {name: 'Жёлто-Пастельный', color: '#FFFFBF', price: 5000, bought: false},
    {name: 'Тёмно-Коричневый', color: '#503E2A', price: 10000, bought: false},
    {name: 'Светло-Зелёный', color: '#BFFF9F', price: 20000, bought: false},
    {name: 'Тёмно-Зелёный', color: '#004101', price: 50000, bought: false},
    {name: 'Нежно-Зелёный', color: '#C0FFE0', price: 100000, bought: false},
    {name: 'Тёмно-Бирюзовый', color: '#004040', price: 200000, bought: false},
    {name: 'Светло-Голубой', color: '#A0FFFF', price: 500000, bought: false},
    {name: 'Синий', color: '#223C55', price: 1000000, bought: false},
    {name: 'Светло-Фиолетовый', color: '#BDB5FE', price: 2000000, bought: false},
    {name: 'Тёмно-Фиолетовый', color: '#422B63', price: 5000000, bought: false},
    {name: 'Ярко-Пурпурный', color: '#FCB6FF', price: 10000000, bought: false},
    {name: 'Фиолетовый', color: '#5F315F', price: 20000000, bought: false},
    {name: 'Радужный', background: 'linear-gradient(to right, red, orange, yellow, green, blue, indigo, violet)', price: 0, bought: false, hidden: true},
    {name: 'Полосатый черно-белый', background: 'repeating-linear-gradient(45deg, black, black 10px, white 10px, white 20px)', price: 0, bought: false, hidden: true}
  ];

  const boxes = [
    {
      name: "Обычный ящик",
      price: 100,
      chances: { "Чёрный": 50, "Светло-Розовый": 20, "Бордовый": 10, "Оранжевый": 5, "Тёмно-Оранжевый": 2, "Жёлто-Пастельный": 1 }
    },
    {
      name: "Большой ящик",
      price: 1000,
      chances: { "Чёрный": 0, "Светло-Розовый": 50, "Бордовый": 20, "Оранжевый": 10, "Тёмно-Оранжевый": 5, "Жёлто-Пастельный": 2, "Тёмно-Коричневый": 1 }
    },
    {
      name: "Мега ящик",
      price: 10000,
      chances: { "Чёрный": 0, "Светло-Розовый": 0, "Бордовый": 0, "Оранжевый": 50, "Тёмно-Оранжевый": 20, "Жёлто-Пастельный": 10, "Тёмно-Коричневый": 5, "Светло-Зелёный": 2, "Тёмно-Зелёный": 1 }
    },
    {
        name: "Редкий ящик",
        price: 100000,
        chances: { "Чёрный": 0, "Светло-Розовый": 0, "Бордовый": 0, "Оранжевый": 0, "Тёмно-Оранжевый": 0, "Жёлто-Пастельный": 0, "Тёмно-Коричневый": 50, "Светло-Зелёный": 20, "Тёмно-Зелёный": 10, "Нежно-Зелёный": 5, "Тёмно-Бирюзовый": 2, "Светло-Голубой": 1 }
     },
     {
        name: "Супер-Редкий ящик",
        price: 1000000,
        chances: { "Чёрный": 0, "Светло-Розовый": 0, "Бордовый": 0, "Оранжевый": 0, "Тёмно-Оранжевый": 0, "Жёлто-Пастельный": 0, "Тёмно-Коричневый": 0, "Светло-Зелёный": 0, "Тёмно-Зелёный": 0, "Нежно-Зелёный": 0, "Тёмно-Бирюзовый": 50, "Светло-Голубой": 20, "Синий": 10, "Светло-Фиолетовый": 5, "Тёмно-Фиолетовый": 2, "Ярко-Пурпурный": 1 }
      }
  ];

  const titles = [
    {name: 'Барон', price: 39, bought: false},
    {name: 'Страж', price: 49, bought: false},
    {name: 'Герой', price: 129, bought: false},
    {name: 'Аспид', price: 249, bought: false},
    {name: 'Сквид', price: 349, bought: false},
    {name: 'Глава', price: 549, bought: false},
    {name: 'Элита', price: 879, bought: false},
    {name: 'Титан', price: 1299, bought: false},
    {name: 'Принц', price: 1799, bought: false},
    {name: 'Князь', price: 2799, bought: false},
    {name: 'Герцог', price: 7990, bought: false},
  ];

  const backgrounds = [
    {
      name: 'Кристальный',
      price: 1000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 40 40"><polygon fill="%23FCB6FF" stroke="%23FCB6FF" stroke-width="1" points="20,2 30,10 30,30 20,38 10,30 10,10"/></svg>')`,
      bought: false
    },
    {
      name: 'Черепной',
      price: 2000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="1000" viewBox="0 0 1000 1000" preserveAspectRatio="xMidYMid slice"><rect width="1000" height="1000" fill="%23222"/><text x="122" y="841" font-family="Arial" font-size="21" fill="white" opacity="0.68">💀</text><text x="356" y="479" font-family="Arial" font-size="27" fill="white" opacity="0.39">💀</text><text x="768" y="80" font-family="Arial" font-size="15" fill="white" opacity="0.55">💀</text><text x="599" y="301" font-family="Arial" font-size="23" fill="white" opacity="0.74">💀</text><text x="931" y="658" font-family="Arial" font-size="18" fill="white" opacity="0.33">💀</text><text x="83" y="120" font-family="Arial" font-size="28" fill="white" opacity="0.61">💀</text><text x="452" y="700" font-family="Arial" font-size="19" fill="white" opacity="0.42">💀</text><text x="240" y="342" font-family="Arial" font-size="25" fill="white" opacity="0.5">💀</text><text x="678" y="900" font-family="Arial" font-size="14" fill="white" opacity="0.77">💀</text><text x="850" y="500" font-family="Arial" font-size="22" fill="white" opacity="0.36">💀</text><text x="150" y="650" font-family="Arial" font-size="26" fill="white" opacity="0.45">💀</text><text x="720" y="350" font-family="Arial" font-size="16" fill="white" opacity="0.63">💀</text><text x="320" y="150" font-family="Arial" font-size="24" fill="white" opacity="0.52">💀</text><text x="580" y="750" font-family="Arial" font-size="20" fill="white" opacity="0.71">💀</text><text x="950" y="250" font-family="Arial" font-size="29" fill="white" opacity="0.58">💀</text></svg>')`,
      backgroundSize: 'cover',
      backgroundRepeat: 'no-repeat',
      bought: false
    },
    {
      name: 'Футбольно-Мячный',
      price: 5000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="1000" viewBox="0 0 1000 1000" preserveAspectRatio="xMidYMid slice"><rect width="1000" height="1000" fill="%23007F00"/><text x="80" y="120" font-family="Arial" font-size="28" fill="white" opacity="0.9">⚽</text><text x="220" y="340" font-family="Arial" font-size="22" fill="white" opacity="0.8">⚽</text><text x="380" y="90" font-family="Arial" font-size="38" fill="white" opacity="0.7">⚽</text><text x="500" y="300" font-family="Arial" font-size="30" fill="white" opacity="0.85">⚽</text><text x="650" y="120" font-family="Arial" font-size="25" fill="white" opacity="0.75">⚽</text><text x="780" y="350" font-family="Arial" font-size="20" fill="white" opacity="0.95">⚽</text><text x="150" y="550" font-family="Arial" font-size="26" fill="white" opacity="0.65">⚽</text><text x="320" y="700" font-family="Arial" font-size="19" fill="white" opacity="0.8">⚽</text><text x="480" y="550" font-family="Arial" font-size="33" fill="white" opacity="0.9">⚽</text><text x="620" y="750" font-family="Arial" font-size="27" fill="white" opacity="0.7">⚽</text><text x="800" y="600" font-family="Arial" font-size="21" fill="white" opacity="0.75">⚽</text><text x="50" y="800" font-family="Arial" font-size="24" fill="white" opacity="0.85">⚽</text><text x="250" y="900" font-family="Arial" font-size="16" fill="white" opacity="0.6">⚽</text><text x="420" y="800" font-family="Arial" font-size="29" fill="white" opacity="0.8">⚽</text><text x="580" y="950" font-family="Arial" font-size="18" fill="white" opacity="0.9">⚽</text><text x="750" y="850" font-family="Arial" font-size="25" fill="white" opacity="0.7">⚽</text><text x="900" y="700" font-family="Arial" font-size="22" fill="white" opacity="0.75">⚽</text><text x="950" y="450" font-family="Arial" font-size="20" fill="white" opacity="0.85">⚽</text><text x="850" y="200" font-family="Arial" font-size="26" fill="white" opacity="0.65">⚽</text><text x="680" y="400" font-family="Arial" font-size="19" fill="white" opacity="0.8">⚽</text><text x="520" y="200" font-family="Arial" font-size="23" fill="white" opacity="0.9">⚽</text><text x="380" y="450" font-family="Arial" font-size="27" fill="white" opacity="0.7">⚽</text><text x="200" y="200" font-family="Arial" font-size="21" fill="white" opacity="0.75">⚽</text><text x="100" y="400" font-family="Arial" font-size="24" fill="white" opacity="0.85">⚽</text><text x="300" y="600" font-family="Arial" font-size="16" fill="white" opacity="0.6">⚽</text><text x="450" y="650" font-family="Arial" font-size="29" fill="white" opacity="0.8">⚽</text><text x="600" y="500" font-family="Arial" font-size="18" fill="white" opacity="0.9">⚽</text><text x="750" y="650" font-family="Arial" font-size="25" fill="white" opacity="0.7">⚽</text><text x="900" y="550" font-family="Arial" font-size="22" fill="white" opacity="0.75">⚽</text><text x="950" y="300" font-family="Arial" font-size="20" fill="white" opacity="0.85">⚽</text></svg>')`,
      backgroundSize: 'cover',
      backgroundRepeat: 'no-repeat',
      bought: false
    },
    {
      name: 'Золотой',
      price: 10000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="1000" viewBox="0 0 1000 1000" preserveAspectRatio="xMidYMid slice"><defs><linearGradient id="goldGradient" x1="0%" y1="0%" x2="100%" y2="100%"><stop offset="0%" stop-color="%23FFD700"/><stop offset="40%" stop-color="%23D4AF37"/><stop offset="100%" stop-color="%23B8860B"/></linearGradient><radialGradient id="lightReflection" cx="15%" cy="15%" r="30%" fx="10%" fy="10%"><stop offset="0%" stop-color="%23FFF9C4" stop-opacity="0.9"/><stop offset="100%" stop-color="%23FFECB3" stop-opacity="0"/></radialGradient></defs><rect width="1000" height="1000" fill="url(%23goldGradient)"/><rect width="1000" height="1000" fill="url(%23lightReflection)"/></svg>')`,
      backgroundSize: 'cover',
      backgroundRepeat: 'no-repeat',
      bought: false
    },
    {
      name: 'Огненный',
      price: 20000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="1000" viewBox="0 0 1000 1000" preserveAspectRatio="xMidYMid slice"><rect width="1000" height="1000" fill="black"/><text x="80" y="120" font-family="Arial" font-size="28" fill="red" opacity="0.7">🔥</text><text x="220" y="340" font-family="Arial" font-size="22" fill="orange" opacity="0.8">🔥</text><text x="380" y="90" font-family="Arial" font-size="18" fill="yellow" opacity="0.9">🔥</text><text x="500" y="300" font-family="Arial" font-size="30" fill="red" opacity="0.6">🔥</text><text x="650" y="120" font-family="Arial" font-size="25" fill="orange" opacity="0.75">🔥</text><text x="780" y="350" font-family="Arial" font-size="20" fill="yellow" opacity="0.85">🔥</text><text x="150" y="550" font-family="Arial" font-size="26" fill="red" opacity="0.65">🔥</text><text x="320" y="700" font-family="Arial" font-size="19" fill="orange" opacity="0.8">🔥</text><text x="480" y="550" font-family="Arial" font-size="23" fill="yellow" opacity="0.9">🔥</text><text x="620" y="750" font-family="Arial" font-size="27" fill="red" opacity="0.7">🔥</text><text x="800" y="600" font-family="Arial" font-size="21" fill="orange" opacity="0.75">🔥</text><text x="50" y="800" font-family="Arial" font-size="24" fill="yellow" opacity="0.85">🔥</text><text x="250" y="900" font-family="Arial" font-size="16" fill="red" opacity="0.6">🔥</text><text x="420" y="800" font-family="Arial" font-size="29" fill="orange" opacity="0.8">🔥</text><text x="580" y="950" font-family="Arial" font-size="18" fill="yellow" opacity="0.9">🔥</text><text x="750" y="850" font-family="Arial" font-size="25" fill="red" opacity="0.7">🔥</text><text x="900" y="700" font-family="Arial" font-size="22" fill="orange" opacity="0.75">🔥</text><text x="950" y="450" font-family="Arial" font-size="20" fill="yellow" opacity="0.85">🔥</text><text x="850" y="200" font-family="Arial" font-size="26" fill="red" opacity="0.65">🔥</text><text x="680" y="400" font-family="Arial" font-size="19" fill="orange" opacity="0.8">🔥</text><text x="520" y="200" font-family="Arial" font-size="23" fill="yellow" opacity="0.9">🔥</text><text x="380" y="450" font-family="Arial" font-size="27" fill="red" opacity="0.7">🔥</text><text x="200" y="200" font-family="Arial" font-size="21" fill="orange" opacity="0.75">🔥</text><text x="100" y="400" font-family="Arial" font-size="24" fill="yellow" opacity="0.85">🔥</text><text x="300" y="600" font-family="Arial" font-size="16" fill="red" opacity="0.6">🔥</text><text x="450" y="650" font-family="Arial" font-size="29" fill="orange" opacity="0.8">🔥</text><text x="600" y="500" font-family="Arial" font-size="18" fill="yellow" opacity="0.9">🔥</text><text x="750" y="650" font-family="Arial" font-size="25" fill="red" opacity="0.7">🔥</text><text x="900" y="550" font-family="Arial" font-size="22" fill="orange" opacity="0.75">🔥</text><text x="950" y="300" font-family="Arial" font-size="20" fill="yellow" opacity="0.85">🔥</text></svg>')`,
      backgroundSize: 'cover',
      backgroundRepeat: 'no-repeat',
      bought: false
    },
    {
      name: 'Кровавый',
      price: 50000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="1000" height="1000" viewBox="0 0 1000 1000" preserveAspectRatio="xMidYMid slice"><rect width="1000" height="1000" fill="white"/><text x="80" y="120" font-family="Arial" font-size="28" fill="%23FF0000" opacity="0.7">🩸</text><text x="220" y="340" font-family="Arial" font-size="22" fill="%23B22222" opacity="0.8">🩸</text><text x="380" y="90" font-family="Arial" font-size="18" fill="%23FF0000" opacity="0.9">🩸</text><text x="500" y="300" font-family="Arial" font-size="30" fill="%23B22222" opacity="0.6">🩸</text><text x="650" y="120" font-family="Arial" font-size="25" fill="%23FF0000" opacity="0.75">🩸</text><text x="780" y="350" font-family="Arial" font-size="20" fill="%23B22222" opacity="0.85">🩸</text><text x="150" y="550" font-family="Arial" font-size="26" fill="%23FF0000" opacity="0.65">🩸</text><text x="320" y="700" font-family="Arial" font-size="19" fill="%23B22222" opacity="0.8">🩸</text><text x="480" y="550" font-family="Arial" font-size="23" fill="%23FF0000" opacity="0.9">🩸</text><text x="620" y="750" font-family="Arial" font-size="27" fill="%23B22222" opacity="0.7">🩸</text><text x="800" y="600" font-family="Arial" font-size="21" fill="%23FF0000" opacity="0.75">🩸</text><text x="50" y="800" font-family="Arial" font-size="24" fill="%23B22222" opacity="0.85">🩸</text><text x="250" y="900" font-family="Arial" font-size="16" fill="%23FF0000" opacity="0.6">🩸</text><text x="420" y="800" font-family="Arial" font-size="29" fill="%23B22222" opacity="0.8">🩸</text><text x="580" y="950" font-family="Arial" font-size="18" fill="%23FF0000" opacity="0.9">🩸</text><text x="750" y="850" font-family="Arial" font-size="25" fill="%23B22222" opacity="0.7">🩸</text><text x="900" y="700" font-family="Arial" font-size="22" fill="%23FF0000" opacity="0.75">🩸</text><text x="950" y="450" font-family="Arial" font-size="20" fill="%23B22222" opacity="0.85">🩸</text><text x="850" y="200" font-family="Arial" font-size="26" fill="%23FF0000" opacity="0.65">🩸</text><text x="680" y="400" font-family="Arial" font-size="19" fill="%23B22222" opacity="0.8">🩸</text><text x="520" y="200" font-family="Arial" font-size="23" fill="%23FF0000" opacity="0.9">🩸</text><text x="380" y="450" font-family="Arial" font-size="27" fill="%23B22222" opacity="0.7">🩸</text><text x="200" y="200" font-family="Arial" font-size="21" fill="%23FF0000" opacity="0.75">🩸</text><text x="100" y="400" font-family="Arial" font-size="24" fill="%23B22222" opacity="0.85">🩸</text><text x="300" y="600" font-family="Arial" font-size="16" fill="%23FF0000" opacity="0.6">🩸</text><text x="450" y="650" font-family="Arial" font-size="29" fill="%23B22222" opacity="0.8">🩸</text><text x="600" y="500" font-family="Arial" font-size="18" fill="%23FF0000" opacity="0.9">🩸</text><text x="750" y="650" font-family="Arial" font-size="25" fill="%23B22222" opacity="0.7">🩸</text><text x="900" y="550" font-family="Arial" font-size="22" fill="%23FF0000" opacity="0.75">🩸</text><text x="950" y="300" font-family="Arial" font-size="20" fill="%23B22222" opacity="0.85">🩸</text></svg>')`,
      backgroundSize: 'cover',
      backgroundRepeat: 'no-repeat',
      bought: false
    },
    {
      name: 'Звёздный',
      price: 100000000,
      svg: `url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="0 0 40 40"><polygon fill="%23FFFFE0" stroke="%23FFD700" stroke-width="1" points="20,3 24,15 36,15 26,22 30,34 20,27 10,34 14,22 4,15 16,15"/></svg>')`,
      bought: false
    }
  ];

  const roulettePrizes = [
    { type: 'money', amount: 1, weight: 75, color: '#FF9999', text: '1💰' },
    { type: 'money', amount: 20, weight: 50, color: '#99FF99', text: '20💰' },
    { type: 'money', amount: 500, weight: 30, color: '#9999FF', text: '500💰' },
    { type: 'title', name: 'Барон', weight: 25, color: '#FFFF99', text: 'Барон' },
    { type: 'title', name: 'Страж', weight: 20, color: '#FF99FF', text: 'Страж' },
    { type: 'title', name: 'Герой', weight: 10, color: '#99FFFF', text: 'Герой' },
    { type: 'skin', name: 'Полосатый черно-белый', weight: 5, color: '#CCCCFF', text: 'Чёрно-Белый' },
    { type: 'skin', name: 'Радужный', weight: 2, color: '#FFCC99', text: 'Радужный' }
  ];

  function formatNumber(value) {
    return value.toLocaleString();
  }

  function updateMoneyDisplay() {
    moneyDisplay.textContent = `💰Gold Coin💰: ${formatNumber(money)}`;
  }
  
  function updateGemsDisplay() {
    gemsDisplay.textContent = `💎Cosmo Coin💎: ${formatNumber(gems)}`;
  }

  function saveGame() {
    localStorage.setItem('money', money);
    localStorage.setItem('gems', gems);
    localStorage.setItem('skins', JSON.stringify(skins));
    localStorage.setItem('titles', JSON.stringify(titles));
    localStorage.setItem('backgrounds', JSON.stringify(backgrounds));
    if(currentSkin) localStorage.setItem('equippedSkin', currentSkin.name);
    else localStorage.removeItem('equippedSkin');
    if(currentTitle) localStorage.setItem('equippedTitle', currentTitle.name);
    else localStorage.removeItem('equippedTitle');
    if(currentBackground) localStorage.setItem('equippedBackground', currentBackground.name);
    else localStorage.removeItem('equippedBackground');
    localStorage.setItem('lastDailyReward', lastDailyReward);
    localStorage.setItem('currentDailyDay', currentDailyDay.toString());
  }

  function loadGame() {
    const savedMoney = localStorage.getItem('money');
    if (savedMoney !== null) money = parseFloat(savedMoney);
    const savedGems = localStorage.getItem('gems');
    if (savedGems !== null) gems = parseFloat(savedGems);
    const savedSkins = localStorage.getItem('skins');
    if (savedSkins !== null) {
      try {
        const parsedSkins = JSON.parse(savedSkins);
        skins.forEach(skin => {
          const savedSkin = parsedSkins.find(s => s.name === skin.name);
          if (savedSkin) {
            skin.bought = savedSkin.bought;
            if (savedSkin.hidden !== undefined) skin.hidden = savedSkin.hidden;
          }
        });
      } catch {}
    }
    const savedTitles = localStorage.getItem('titles');
    if (savedTitles !== null) {
      try {
        const parsedTitles = JSON.parse(savedTitles);
        titles.forEach(title => {
          const savedTitle = parsedTitles.find(t => t.name === title.name);
          if (savedTitle) title.bought = savedTitle.bought;
        });
      } catch {}
    }
    const savedBackgrounds = localStorage.getItem('backgrounds');
    if (savedBackgrounds !== null) {
      try {
        const parsedBackgrounds = JSON.parse(savedBackgrounds);
        backgrounds.forEach(bg => {
          const savedBg = parsedBackgrounds.find(b => b.name === bg.name);
          if (savedBg) bg.bought = savedBg.bought;
        });
      } catch {}
    }
    const equippedSkinName = localStorage.getItem('equippedSkin');
    if (equippedSkinName) {
      const skin = skins.find(s => s.name === equippedSkinName && s.bought);
      if (skin) currentSkin = skin;
    }
    const equippedTitleName = localStorage.getItem('equippedTitle');
    if (equippedTitleName) {
      const title = titles.find(t => t.name === equippedTitleName && t.bought);
      if (title) currentTitle = title;
    }
    const equippedBackgroundName = localStorage.getItem('equippedBackground');
    if (equippedBackgroundName) {
      const bg = backgrounds.find(b => b.name === equippedBackgroundName && b.bought);
      if (bg) {
        currentBackground = bg;
        document.body.style.backgroundImage = bg.svg;
      }
    }
    const savedLastDailyReward = localStorage.getItem('lastDailyReward');
    if (savedLastDailyReward) lastDailyReward = savedLastDailyReward;
    const savedCurrentDailyDay = localStorage.getItem('currentDailyDay');
    if (savedCurrentDailyDay) currentDailyDay = parseInt(savedCurrentDailyDay);
    
    // Проверяем, не прошло ли более 1 дня с последней награды
    const today = getToday();
    if (lastDailyReward) {
      const lastDate = new Date(lastDailyReward);
      const currentDate = new Date(today);
      const diffTime = currentDate - lastDate;
      const diffDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
      
      // Если прошло более 1 дня - сбрасываем прогресс
      if (diffDays > 1) {
        currentDailyDay = 1;
      }
    }
    
    updateDailyRewardButton();
  }

  let messageTimeout;
  function showMessage(text) {
    clearTimeout(messageTimeout);
    message.textContent = text;
    message.style.opacity = '1';
    messageTimeout = setTimeout(() => {
      message.style.opacity = '0';
    }, 3000);
  }

  function getMoneyMultiplier() {
    if (!currentTitle) return 1;
    switch (currentTitle.name) {
      case 'Барон': return 15;
      case 'Страж': return 27;
      case 'Герой': return 39;
      case 'Аспид': return 79;
      case 'Сквид': return 129;
      case 'Глава': return 249;
      case 'Элита': return 349;
      case 'Титан': return 549;
      case 'Принц': return 879;
      case 'Князь': return 1299;
      case 'Герцог': return 2379;
      default: return 1;
    }
  }

  // Обработчик клика по основной кнопке
  mainButton.addEventListener('click', function(e) {
    // Создаем эффект клика
    const clickEffect = document.createElement('div');
    clickEffect.className = 'click-effect';
    clickEffect.style.left = (e.clientX - mainButton.getBoundingClientRect().left) + 'px';
    clickEffect.style.top = (e.clientY - mainButton.getBoundingClientRect().top) + 'px';
    mainButton.appendChild(clickEffect);
    
    // Удаляем эффект после анимации
    setTimeout(() => {
      clickEffect.remove();
    }, 500);
    
    // Зарабатываем деньги
    const coinsEarned = 1 * getMoneyMultiplier();
    money += coinsEarned;
    updateMoneyDisplay();
    saveGame();
    
    // Анимация монет
    const coin = document.createElement('div');
    coin.className = 'coins-animation';
    coin.textContent = `+${coinsEarned}💰`;
    coin.style.left = e.clientX + 'px';
    coin.style.top = e.clientY + 'px';
    gameContainer.appendChild(coin);
    
    setTimeout(() => {
      coin.remove();
    }, 1500);
    
    // Перемещаем кнопку в случайное место
    moveButtonRandomly();
  });
  
  // Функция перемещения кнопки
  function moveButtonRandomly() {
    const buttonWidth = mainButton.offsetWidth;
    const buttonHeight = mainButton.offsetHeight;
    const maxX = window.innerWidth - buttonWidth - 20;
    const maxY = window.innerHeight - buttonHeight - 20;
    
    const newLeft = 20 + Math.random() * maxX;
    const newTop = 60 + Math.random() * maxY;
    
    mainButton.style.left = `${newLeft}px`;
    mainButton.style.top = `${newTop}px`;
  }

  shopButton.addEventListener('click', () => {
    renderSkins();
    renderBoxes();
    renderTitles();
    renderBackgrounds();
    shopOverlay.style.display = 'flex';
    shopOverlay.setAttribute('aria-hidden', 'false');
  });

  closeShop.addEventListener('click', () => {
    shopOverlay.style.display = 'none';
    shopOverlay.setAttribute('aria-hidden', 'true');
    shopButton.focus();
  });

  window.addEventListener('keydown', e => {
    if (e.key === 'Escape' && shopOverlay.style.display === 'flex') {
      shopOverlay.style.display = 'none';
      shopOverlay.setAttribute('aria-hidden', 'true');
      shopButton.focus();
    }
  });

  function renderSkins() {
    skinsList.innerHTML = '';
    skins.forEach(skin => {
      if (skin.hidden && !skin.bought) return;
      
      const skinDiv = document.createElement('div');
      skinDiv.className = 'shop-item';
      if (skin.bought) skinDiv.classList.add('bought');
      if (currentSkin && currentSkin.name === skin.name) skinDiv.classList.add('selected');
      
      const colorBox = document.createElement('div');
      colorBox.className = 'colorBox';
      if (skin.color) {
        colorBox.style.backgroundColor = skin.color;
      } else if (skin.background) {
        colorBox.style.background = skin.background;
      }
      
      const skinInfo = document.createElement('div');
      skinInfo.className = 'item-info';
      skinInfo.innerHTML = `${skin.name}<div class="item-price">${skin.price} 💰</div>`;
      
      skinDiv.appendChild(colorBox);
      skinDiv.appendChild(skinInfo);
      
      skinDiv.addEventListener('click', () => {
        if (!skin.bought) {
          if (money >= skin.price) {
            money -= skin.price;
            skin.bought = true;
            skin.hidden = false;
            equipSkin(skin);
            updateMoneyDisplay();
            saveGame();
            showMessage(`Вы купили скин "${skin.name}"!`);
            renderSkins();
          } else {
            showMessage('Недостаточно золота для покупки скина.');
          }
        } else {
          equipSkin(skin);
        }
      });
      
      skinsList.appendChild(skinDiv);
    });
  }

  function equipSkin(skin) {
    currentSkin = skin;
    if (skin.color) {
      mainButton.style.background = skin.color;
    } else if (skin.background) {
      mainButton.style.background = skin.background;
    }
    
    // Для всех скинов кроме начального - белый текст
    if (skin.name !== 'Белый') {
      mainButton.style.color = 'white';
    }
    
    saveGame();
    renderSkins();
  }

  function renderBoxes() {
    boxesList.innerHTML = '';
    boxes.forEach(box => {
      const boxDiv = document.createElement('div');
      boxDiv.className = 'shop-item';
      
      const boxIcon = document.createElement('div');
      boxIcon.className = 'colorBox';
      boxIcon.style.background = 'linear-gradient(135deg, #8e2de2, #4a00e0)';
      boxIcon.style.display = 'flex';
      boxIcon.style.justifyContent = 'center';
      boxIcon.style.alignItems = 'center';
      boxIcon.innerHTML = '🎁';
      
      const boxInfo = document.createElement('div');
      boxInfo.className = 'item-info';
      boxInfo.innerHTML = `${box.name}<div class="item-price">${box.price} 💰</div>`;
      
      boxDiv.appendChild(boxIcon);
      boxDiv.appendChild(boxInfo);
      
      boxDiv.addEventListener('click', () => {
        if (money < box.price) {
          showMessage('Недостаточно золота для покупки ящика.');
          return;
        }
        money -= box.price;
        updateMoneyDisplay();
        const possibleSkins = skins.filter(skin => box.chances[skin.name] > 0);
        if (possibleSkins.length === 0) {
          showMessage('В этом ящике нет доступных скинов');
          return;
        }
        let weightedSkins = [];
        possibleSkins.forEach(skin => {
          for (let i = 0; i < box.chances[skin.name]; i++) {
            weightedSkins.push(skin);
          }
        });
        const wonSkin = weightedSkins[Math.floor(Math.random() * weightedSkins.length)];
        if (wonSkin.bought) {
          showMessage(`Вы открыли ${box.name} и получили скин "${wonSkin.name}", но он у вас уже есть.`);
        } else {
          wonSkin.bought = true;
          wonSkin.hidden = false;
          equipSkin(wonSkin);
          saveGame();
          showMessage(`Поздравляем! Вы открыли ${box.name} и получили скин "${wonSkin.name}"!`);
          renderSkins();
        }
      });
      
      boxesList.appendChild(boxDiv);
    });
  }

  function renderTitles() {
    titlesList.innerHTML = '';
    titles.forEach(title => {
      const titleDiv = document.createElement('div');
      titleDiv.className = 'shop-item';
      if (title.bought) titleDiv.classList.add('bought');
      if (currentTitle && currentTitle.name === title.name) titleDiv.classList.add('selected');
      
      const iconDiv = document.createElement('div');
      iconDiv.className = 'colorBox';
      iconDiv.style.background = 'linear-gradient(135deg, #ff7e5f, #feb47b)';
      iconDiv.style.display = 'flex';
      iconDiv.style.justifyContent = 'center';
      iconDiv.style.alignItems = 'center';
      iconDiv.innerHTML = '👑';
      
      const titleInfo = document.createElement('div');
      titleInfo.className = 'item-info';
      titleInfo.innerHTML = `${title.name}<div class="item-price">${title.price} 💎</div>`;
      
      titleDiv.appendChild(iconDiv);
      titleDiv.appendChild(titleInfo);
      
      titleDiv.addEventListener('click', () => {
        if (!title.bought) {
          if (gems >= title.price) {
            gems -= title.price;
            title.bought = true;
            equipTitle(title);
            updateGemsDisplay();
            saveGame();
            showMessage(`Вы купили звание "${title.name}"!`);
            renderTitles();
          } else {
            showMessage('Недостаточно драгоценностей для покупки звания.');
          }
        } else {
          equipTitle(title);
        }
      });
      
      titlesList.appendChild(titleDiv);
    });
  }

  function equipTitle(title) {
    currentTitle = title;
    mainButton.title = `Кликай и зарабатывай\nЗвание: ${title.name}`;
    saveGame();
    renderTitles();
  }
  
  function renderBackgrounds() {
    backgroundsList.innerHTML = '';
    backgrounds.forEach(bg => {
      const bgDiv = document.createElement('div');
      bgDiv.className = 'shop-item';
      if (bg.bought) bgDiv.classList.add('bought');
      if (currentBackground && currentBackground.name === bg.name) bgDiv.classList.add('selected');
      
      const preview = document.createElement('div');
      preview.className = 'colorBox';
      preview.style.backgroundImage = bg.svg;
      preview.style.backgroundSize = bg.backgroundSize || 'contain';
      preview.style.backgroundRepeat = bg.backgroundRepeat || 'no-repeat';
      preview.style.backgroundPosition = 'center';
      
      const bgInfo = document.createElement('div');
      bgInfo.className = 'item-info';
      bgInfo.innerHTML = `${bg.name}<div class="item-price">${bg.price} 💰</div>`;
      
      bgDiv.appendChild(preview);
      bgDiv.appendChild(bgInfo);
      
      bgDiv.addEventListener('click', () => {
        if (!bg.bought) {
          if (money >= bg.price) {
            money -= bg.price;
            bg.bought = true;
            equipBackground(bg);
            updateMoneyDisplay();
            saveGame();
            showMessage(`Вы купили фон "${bg.name}"!`);
            renderBackgrounds();
          } else {
            showMessage('Недостаточно золота для покупки фона.');
          }
        } else {
          equipBackground(bg);
        }
      });
      
      backgroundsList.appendChild(bgDiv);
    });
  }
  
  function equipBackground(background) {
    currentBackground = background;
    document.body.style.backgroundImage = background.svg;
    document.body.style.backgroundColor = 'white';
    saveGame();
    renderBackgrounds();
  }

  resetButton.addEventListener('click', () => {
    if (confirm('Вы уверены, что хотите сбросить прогресс? Это действие нельзя отменить.')) {
      money = 0;
      gems = 0;
      skins.forEach(skin => {
        skin.bought = false;
        if (skin.hidden) skin.hidden = true;
      });
      titles.forEach(title => title.bought = false);
      backgrounds.forEach(bg => bg.bought = false);
      currentSkin = skins[0];
      currentTitle = null;
      currentBackground = null;
      lastDailyReward = null;
      currentDailyDay = 1;
      updateMoneyDisplay();
      updateGemsDisplay();
      equipSkin(skins[0]);
      mainButton.title = 'Кликай и Зарабатывай';
      document.body.style.backgroundImage = '';
      saveGame();
      showMessage('Прогресс сброшен. Игра началась заново.');
      shopOverlay.style.display = 'none';
      shopOverlay.setAttribute('aria-hidden', 'true');
      renderSkins();
      renderBoxes();
      renderTitles();
      renderBackgrounds();
      localStorage.removeItem('lastRouletteSpin');
      updateRouletteTimer();
      updateDailyRewardButton();
    }
  });

  exchangeButton.addEventListener('click', () => {
    if (money >= 100) {
      money -= 100;
      gems += 1;
      updateMoneyDisplay();
      updateGemsDisplay();
      saveGame();
      showMessage('Обменяли 100 золота на 1 драгоценность!');
    } else {
      showMessage('Недостаточно золота для обмена!');
    }
  });

  function createRouletteSectors() {
    rouletteWheel.innerHTML = '';
    const sectorAngle = 360 / roulettePrizes.length;
    
    roulettePrizes.forEach((prize, index) => {
      const sector = document.createElement('div');
      sector.className = 'roulette-sector';
      sector.style.transform = `rotate(${index * sectorAngle}deg)`;
      sector.style.backgroundColor = prize.color;
      sector.style.clipPath = 'polygon(0% 0%, 100% 0%, 50% 100%)';
      
      const contentDiv = document.createElement('div');
      contentDiv.className = 'sector-content';
      contentDiv.textContent = prize.text;
      
      // Рассчитываем угол для правильной ориентации текста
      const textRotation = sectorAngle/2;
      const isUpsideDown = (index * sectorAngle + sectorAngle/2) > 90 && 
                           (index * sectorAngle + sectorAngle/2) < 270;
      
      // Центрируем текст и корректируем ориентацию
      contentDiv.style.transform = `
        rotate(${textRotation}deg)
        translate(-50%, -50%)
        rotate(${isUpsideDown ? 180 : 0}deg)
      `;
      contentDiv.style.transformOrigin = 'center center';
      contentDiv.style.left = '50%';
      contentDiv.style.top = '35%';
      contentDiv.style.width = '80px';
      contentDiv.style.textAlign = 'center';
      contentDiv.style.fontSize = '16px';
      contentDiv.style.fontWeight = 'bold';
      contentDiv.style.textShadow = '0 1px 2px rgba(0,0,0,0.8)';
      
      sector.appendChild(contentDiv);
      rouletteWheel.appendChild(sector);
    });
  }

  function selectRoulettePrize() {
    let weightedPrizes = [];
    roulettePrizes.forEach(prize => {
      for (let i = 0; i < prize.weight; i++) {
        weightedPrizes.push(prize);
      }
    });
    return weightedPrizes[Math.floor(Math.random() * weightedPrizes.length)];
  }

  function canSpinRoulette() {
    const lastSpin = localStorage.getItem('lastRouletteSpin');
    if (!lastSpin) return true;
    
    const now = Date.now();
    const timeDiff = now - parseInt(lastSpin);
    const hoursDiff = timeDiff / (1000 * 60 * 60);
    
    return hoursDiff >= 8;
  }

  function updateRouletteTimer() {
    const lastSpin = localStorage.getItem('lastRouletteSpin');
    if (!lastSpin) {
      rouletteTimer.textContent = "Рулетка доступна!";
      spinButton.disabled = false;
      return;
    }
    
    const now = Date.now();
    const timeDiff = now - parseInt(lastSpin);
    const hoursDiff = timeDiff / (1000 * 60 * 60);
    
    if (hoursDiff >= 8) {
      rouletteTimer.textContent = "Рулетка доступна!";
      spinButton.disabled = false;
    } else {
      const hoursLeft = Math.floor(8 - hoursDiff);
      const minutesLeft = Math.floor((8 - hoursDiff - hoursLeft) * 60);
      rouletteTimer.textContent = `Доступно через: ${hoursLeft}ч ${minutesLeft}м`;
      spinButton.disabled = true;
    }
  }

  spinButton.addEventListener('click', () => {
    if (!canSpinRoulette()) {
      showMessage('Рулетка еще не доступна!');
      return;
    }
    
    spinButton.disabled = true;
    const prize = selectRoulettePrize();
    
    const sectorAngle = 360 / roulettePrizes.length;
    const prizeIndex = roulettePrizes.findIndex(p => 
      p.type === prize.type && 
      (p.type === 'money' ? p.amount === prize.amount : p.name === prize.name)
    );
    
    // Фиксим расчет угла для точного попадания на сектор
    const minSpins = 6;
    const fullSpins = minSpins + Math.floor(Math.random() * 2);
    const additionalAngle = 360 - (prizeIndex * sectorAngle) - (sectorAngle / 2);
    const spinAngle = 360 * fullSpins + additionalAngle;
    
    rouletteWheel.style.transition = 'none';
    rouletteWheel.style.transform = 'rotate(0deg)';
    
    setTimeout(() => {
      rouletteWheel.style.transition = 'transform 4s cubic-bezier(0.34, 1.56, 0.64, 1)';
      rouletteWheel.style.transform = `rotate(${spinAngle}deg)`;
      
      setTimeout(() => {
        awardPrize(prize);
        localStorage.setItem('lastRouletteSpin', Date.now());
        updateRouletteTimer();
      }, 4000);
    }, 10);
  });

  function awardPrize(prize) {
    switch (prize.type) {
      case 'money':
        money += prize.amount;
        updateMoneyDisplay();
        showMessage(`Вы выиграли ${prize.amount} Gold Coin!`);
        break;
        
      case 'title':
        const title = titles.find(t => t.name === prize.name);
        if (title) {
          if (!title.bought) {
            title.bought = true;
            equipTitle(title);
            showMessage(`Вы выиграли звание "${prize.name}"!`);
          } else {
            const compensation = Math.floor(title.price * 0.1);
            gems += compensation;
            updateGemsDisplay();
            showMessage(`Вы уже имеете звание "${prize.name}". В качестве компенсации вы получаете ${compensation} 💎`);
          }
        }
        break;
        
      case 'skin':
        const skin = skins.find(s => s.name === prize.name);
        if (skin) {
          if (!skin.bought) {
            skin.bought = true;
            skin.hidden = false;
            equipSkin(skin);
            showMessage(`Вы выиграли скин "${prize.name}"!`);
          } else {
            const compensation = Math.max(10000, skin.price * 0.1);
            money += compensation;
            updateMoneyDisplay();
            showMessage(`Вы уже имеете скин "${prize.name}". Компенсация: ${compensation}💰`);
          }
        }
        break;
    }
    saveGame();
  }

  rouletteButton.addEventListener('click', () => {
    createRouletteSectors();
    rouletteOverlay.style.display = 'flex';
    updateRouletteTimer();
  });

  closeRoulette.addEventListener('click', () => {
    rouletteOverlay.style.display = 'none';
  });

  window.addEventListener('keydown', e => {
    if (e.key === 'Escape' && rouletteOverlay.style.display === 'flex') {
      rouletteOverlay.style.display = 'none';
    }
  });

  // Функция для получения текущей даты в формате YYYY-MM-DD
  function getToday() {
    const now = new Date();
    return now.toISOString().split('T')[0];
  }

  // Проверка доступности ежедневной награды
  function canClaimDailyReward() {
    const today = getToday();
    return !lastDailyReward || lastDailyReward !== today;
  }

  // Обновление состояния кнопки ежедневной награды
  function updateDailyRewardButton() {
    const today = getToday();
    const canClaim = canClaimDailyReward();
    
    if (canClaim) {
      dailyRewardButton.title = `Ежедневная награда: ${dailyRewards[currentDailyDay-1]}💰 (День ${currentDailyDay}/7)`;
      dailyRewardButton.disabled = false;
    } else {
      dailyRewardButton.title = "Вы уже получили награду сегодня. Вернитесь завтра!";
      dailyRewardButton.disabled = true;
    }
  }

  // Показать окно ежедневной награды
  function showDailyReward() {
    const today = getToday();
    
    if (!canClaimDailyReward()) {
      showMessage("Вы уже получили награду сегодня!");
      return;
    }
    
    dailyRewardAmount.textContent = `+${dailyRewards[currentDailyDay-1]}💰`;
    dailyRewardDay.textContent = `День ${currentDailyDay}/7`;
    dailyRewardOverlay.style.display = 'flex';
  }

  // Забрать награду
  claimDailyReward.addEventListener('click', () => {
    const reward = dailyRewards[currentDailyDay-1];
    money += reward;
    lastDailyReward = getToday();
    
    // Переход к следующему дню (после 7 дня начинаем сначала)
    currentDailyDay = (currentDailyDay % 7) + 1;
    
    updateMoneyDisplay();
    saveGame();
    showMessage(`Вы получили ежедневную награду: ${reward}💰!`);
    dailyRewardOverlay.style.display = 'none';
    updateDailyRewardButton();
  });

  // Закрыть окно награды
  closeDailyReward.addEventListener('click', () => {
    dailyRewardOverlay.style.display = 'none';
  });

  // Обработчик кнопки ежедневной награды
  dailyRewardButton.addEventListener('click', showDailyReward);

  // Инициализация игры
  loadGame();
  updateMoneyDisplay();
  updateGemsDisplay();
  createRouletteSectors();
  updateRouletteTimer();
  updateDailyRewardButton();

  // Применяем текущий скин
  if (currentSkin) {
    equipSkin(currentSkin);
  } else {
    // Если скин не выбран, применяем белый
    equipSkin(skins[0]);
  }
  
  if (currentTitle) {
    equipTitle(currentTitle);
  }

})();
</script>

</body>
</html>

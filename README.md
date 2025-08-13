<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>بازی تخته‌نرد آموزشی</title>
<style>
  body {
    font-family: Tahoma, sans-serif;
    background: #f0f8ff;
    direction: rtl;
    margin: 0; padding: 0;
    display: flex; flex-direction: column; align-items: center;
  }
  h1 {
    margin: 20px 0;
    color: #333;
  }
  #board {
    width: 320px;
    height: 320px;
    border: 2px solid #444;
    display: grid;
    grid-template-columns: repeat(12, 1fr);
    grid-template-rows: repeat(2, 1fr);
    gap: 0;
    background: #deb887;
  }
  .point {
    border-left: 1px solid #654321;
    position: relative;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    overflow: visible;
    cursor: pointer;
  }
  .point:nth-child(6),
  .point:nth-child(12) {
    border-right: 1px solid #654321;
  }
  .triangle {
    width: 0;
    height: 0;
    border-style: solid;
    margin-top: 0;
  }
  .triangle.up {
    border-width: 0 16px 80px 16px;
    border-color: transparent transparent #654321 transparent;
  }
  .triangle.down {
    border-width: 80px 16px 0 16px;
    border-color: #654321 transparent transparent transparent;
  }
  #points-top {
    display: flex;
    justify-content: space-between;
  }
  #points-bottom {
    display: flex;
    justify-content: space-between;
  }
  .checkers {
    position: absolute;
    bottom: 5px;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .checker {
    width: 24px;
    height: 24px;
    border-radius: 50%;
    margin: 2px 0;
    box-shadow: 0 2px 4px rgba(0,0,0,0.3);
  }
  .checker.white {
    background: #fff;
    border: 1px solid #ccc;
  }
  .checker.black {
    background: #000;
    border: 1px solid #333;
  }
  #info {
    margin-top: 15px;
    width: 320px;
    background: #eee;
    padding: 10px;
    border-radius: 6px;
    box-shadow: inset 0 0 5px #aaa;
    font-size: 14px;
  }
  #log {
    margin-top: 10px;
    max-height: 120px;
    overflow-y: auto;
    background: #fff;
    padding: 8px;
    border-radius: 5px;
    font-family: monospace;
    font-size: 12px;
    box-shadow: inset 0 0 6px #ccc;
  }
  button {
    margin-top: 15px;
    padding: 8px 16px;
    font-size: 16px;
    border-radius: 6px;
    border: none;
    background: #4682b4;
    color: white;
    cursor: pointer;
    user-select: none;
  }
  button:hover {
    background: #315f86;
  }
</style>
</head>
<body>

<h1>بازی تخته‌نرد آموزشی</h1>

<div id="board">
  <!-- 12 خانه بالا -->
  <div class="point" data-point="12"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="11"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="10"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="9"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="8"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="7"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="6"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="5"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="4"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="3"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="2"><div class="triangle up"></div><div class="checkers"></div></div>
  <div class="point" data-point="1"><div class="triangle up"></div><div class="checkers"></div></div>

  <!-- 12 خانه پایین -->
  <div class="point" data-point="13"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="14"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="15"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="16"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="17"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="18"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="19"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="20"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="21"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="22"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="23"><div class="triangle down"></div><div class="checkers"></div></div>
  <div class="point" data-point="24"><div class="triangle down"></div><div class="checkers"></div></div>
</div>

<div id="info">
  <div>نوبت: <span id="turn">سفید</span></div>
  <div>تاس‌ها: <span id="dice"></span></div>
  <div>راهنما: <span id="hint"></span></div>
</div>

<div id="log"></div>

<button id="rollDice">تاس بینداز</button>

<script>
  // حالت اولیه مهره‌ها
  const initialPositions = {
    1: 2,    // خانه 1: 2 مهره سفید
    6: 5,    // خانه 6: 5 مهره سیاه
    8: 3,
    12: 5,
    13: 5,
    17: 3,
    19: 5,
    24: 2,
  };

  let turn = 'white'; // نوبت بازی: white یا black
  let dice = [];
  let movesLeft = 0;

  const points = document.querySelectorAll('.point');
  const turnEl = document.getElementById('turn');
  const diceEl = document.getElementById('dice');
  const hintEl = document.getElementById('hint');
  const logEl = document.getElementById('log');
  const rollDiceBtn = document.getElementById('rollDice');

  // مهره‌ها رو روی تخته بذار
  function setupBoard() {
    points.forEach(point => {
      const pos = parseInt(point.dataset.point);
      const checkersEl = point.querySelector('.checkers');
      checkersEl.innerHTML = '';
      let count = initialPositions[pos] || 0;
      let color = 'white';
      if ([6, 12, 13, 19].includes(pos)) color = 'black';
      for(let i=0; i<count; i++) {
        const checker = document.createElement('div');
        checker.classList.add('checker', color);
        checkersEl.appendChild(checker);
      }
    });
    turn = 'white';
    turnEl.textContent = 'سفید';
    dice = [];
    diceEl.textContent = '';
    hintEl.textContent = 'تاس بیندازید';
    logEl.textContent = '';
  }

  // تاس بنداز
  function rollDice() {
    dice = [randomDice(), randomDice()];
    movesLeft = dice.length;
    diceEl.textContent = dice.join(' , ');
    hintEl.textContent = 'مهره خود را حرکت دهید.';
    logEl.textContent += `\nتاس ریخته: ${dice.join(' , ')}`;
  }

  function randomDice() {
    return Math.floor(Math.random() * 6) + 1;
  }

  // فقط ساختار و پایه - تاکتیک‌ها به زودی اضافه میشن
  rollDiceBtn.addEventListener('click', () => {
    if (dice.length > 0) {
      logEl.textContent += '\nنوبت تمام شد، تاس مجدد بیندازید.';
      setupBoard();
    } else {
      rollDice();
    }
  });

  setupBoard();
</script>

</body>
</html>
 backgammon
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Game Onet</title>

  <style>
    body{
      margin:0;
      font-family:Arial, sans-serif;
      background:#121212;
      color:white;
      text-align:center;
    }

    h1{
      margin-top:20px;
    }

    #game{
      width:520px;
      margin:20px auto;
      display:grid;
      grid-template-columns:repeat(6, 80px);
      gap:6px;
    }

    .card{
      width:80px;
      height:80px;
      background:#1e1e1e;
      border:2px solid #333;
      border-radius:12px;
      font-size:40px;
      display:flex;
      justify-content:center;
      align-items:center;
      cursor:pointer;
      transition:0.2s;
      user-select:none;
    }

    .card:hover{
      transform:scale(1.05);
    }

    .selected{
      border:2px solid yellow;
      background:#333;
    }

    .hidden{
      visibility:hidden;
    }

    #info{
      margin-top:10px;
      font-size:20px;
    }

    button{
      margin-top:15px;
      padding:10px 20px;
      border:none;
      border-radius:10px;
      background:#00b894;
      color:white;
      font-size:16px;
      cursor:pointer;
    }

    button:hover{
      background:#00a383;
    }
  </style>
</head>
<body>

<h1>🎮 Game Onet</h1>

<div id="info">
  Score: <span id="score">0</span>
</div>

<div id="game"></div>

<button onclick="startGame()">Restart</button>

<script>
const emojis = [
  "🍎","🍌","🍇","🍒","🍉","🥝",
  "🍓","🥑","🍍","🥕","🍋","🍑"
];

let firstCard = null;
let secondCard = null;
let lockBoard = false;
let score = 0;

function shuffle(array){
  for(let i = array.length - 1; i > 0; i--){
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
}

function startGame(){
  const game = document.getElementById("game");
  game.innerHTML = "";

  score = 0;
  document.getElementById("score").innerText = score;

  let cards = [...emojis, ...emojis];
  shuffle(cards);

  cards.forEach((emoji)=>{
    const card = document.createElement("div");
    card.classList.add("card");
    card.dataset.emoji = emoji;
    card.innerHTML = emoji;

    card.addEventListener("click", ()=>{
      if(lockBoard) return;
      if(card === firstCard) return;
      if(card.classList.contains("hidden")) return;

      card.classList.add("selected");

      if(!firstCard){
        firstCard = card;
      } else {
        secondCard = card;
        checkMatch();
      }
    });

    game.appendChild(card);
  });
}

function checkMatch(){
  lockBoard = true;

  if(firstCard.dataset.emoji === secondCard.dataset.emoji){

    setTimeout(()=>{
      firstCard.classList.add("hidden");
      secondCard.classList.add("hidden");

      score += 10;
      document.getElementById("score").innerText = score;

      resetBoard();

      checkWin();

    }, 400);

  } else {

    setTimeout(()=>{
      firstCard.classList.remove("selected");
      secondCard.classList.remove("selected");

      resetBoard();
    }, 700);

  }
}

function resetBoard(){
  [firstCard, secondCard] = [null, null];
  lockBoard = false;
}

function checkWin(){
  const hiddenCards = document.querySelectorAll(".hidden");

  if(hiddenCards.length === 24){
    setTimeout(()=>{
      alert("🎉 Selamat Kamu Menang!");
    }, 300);
  }
}

startGame();
</script>

</body>
</html>

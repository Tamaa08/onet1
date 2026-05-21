<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Game Onet Deluxe</title>

<style>
*{
  margin:0;
  padding:0;
  box-sizing:border-box;
}

body{
  font-family:Arial, sans-serif;
  background:linear-gradient(135deg,#111,#222,#000);
  color:white;
  overflow:hidden;
}

.screen{
  position:absolute;
  width:100%;
  height:100%;
  display:flex;
  flex-direction:column;
  justify-content:center;
  align-items:center;
}

.hiddenScreen{
  display:none;
}

h1{
  font-size:55px;
  margin-bottom:20px;
  text-shadow:0 0 15px cyan;
}

p{
  margin-bottom:20px;
  font-size:20px;
}

button{
  padding:12px 25px;
  border:none;
  border-radius:12px;
  background:#00b894;
  color:white;
  font-size:18px;
  cursor:pointer;
  transition:0.2s;
}

button:hover{
  transform:scale(1.05);
  background:#00a383;
}

#gameContainer{
  padding-top:20px;
  text-align:center;
}

#info{
  margin-bottom:15px;
  font-size:22px;
}

#game{
  width:540px;
  margin:auto;
  display:grid;
  grid-template-columns:repeat(6, 80px);
  gap:8px;
}

.card{
  width:80px;
  height:80px;
  background:#1f1f1f;
  border:2px solid #444;
  border-radius:15px;
  font-size:42px;
  display:flex;
  justify-content:center;
  align-items:center;
  cursor:pointer;
  transition:0.2s;
}

.card:hover{
  transform:scale(1.08);
}

.selected{
  border:3px solid yellow;
  background:#333;
}

.hidden{
  visibility:hidden;
}

#winText{
  font-size:40px;
  margin-bottom:20px;
  color:#00ffcc;
  text-shadow:0 0 15px #00ffcc;
}

#stars{
  font-size:50px;
  margin-bottom:20px;
  animation:zoom 1s infinite alternate;
}

@keyframes zoom{
  from{
    transform:scale(1);
  }
  to{
    transform:scale(1.2);
  }
}

#levelText{
  margin-top:10px;
  font-size:20px;
  color:#00ffcc;
}
</style>
</head>

<body>

<!-- INTRO -->
<div id="intro" class="screen">
  <h1>🎮 ONET DELUXE</h1>
  <p>Cocokkan emoji yang sama untuk menang!</p>
  <button onclick="startIntro()">Mulai Game</button>
</div>

<!-- GAME -->
<div id="gameScreen" class="hiddenScreen">
  <div id="gameContainer">

    <h1 style="font-size:40px;">🧩 ONET GAME</h1>

    <div id="info">
      ⭐ Score: <span id="score">0</span>
      <div id="levelText">Level 1</div>
    </div>

    <div id="game"></div>

    <button onclick="restartGame()" style="margin-top:20px;">
      🔄 Restart
    </button>

  </div>
</div>

<!-- WIN SCREEN -->
<div id="winScreen" class="screen hiddenScreen">
  <div id="stars">⭐⭐⭐</div>
  <div id="winText">KAMU MENANG!</div>
  <p id="finalScore"></p>

  <button onclick="nextLevel()">
    ▶ Level Berikutnya
  </button>

  <button onclick="restartGame()" style="margin-top:15px;">
    🔁 Main Lagi
  </button>
</div>

<script>
const levelData = [
  {
    emojis:["🍎","🍌","🍇","🍉","🍓","🥝"]
  },
  {
    emojis:["🐶","🐱","🐭","🐹","🐰","🦊"]
  },
  {
    emojis:["⚽","🏀","🏈","⚾","🎾","🏐"]
  }
];

let currentLevel = 0;

let firstCard = null;
let secondCard = null;
let lockBoard = false;
let score = 0;

function shuffle(array){
  for(let i=array.length-1;i>0;i--){
    const j=Math.floor(Math.random()*(i+1));
    [array[i],array[j]]=[array[j],array[i]];
  }
}

function startIntro(){
  document.getElementById("intro").classList.add("hiddenScreen");
  document.getElementById("gameScreen").classList.remove("hiddenScreen");
  startGame();
}

function startGame(){

  const game = document.getElementById("game");
  game.innerHTML = "";

  document.getElementById("levelText").innerText =
    "Level " + (currentLevel + 1);

  let emojis = levelData[currentLevel].emojis;

  let cards = [...emojis, ...emojis, ...emojis, ...emojis];

  shuffle(cards);

  cards.forEach((emoji)=>{

    const card = document.createElement("div");

    card.classList.add("card");

    card.dataset.emoji = emoji;

    card.innerHTML = emoji;

    card.addEventListener("click",()=>{

      if(lockBoard) return;
      if(card === firstCard) return;
      if(card.classList.contains("hidden")) return;

      card.classList.add("selected");

      if(!firstCard){

        firstCard = card;

      }else{

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

    },400);

  }else{

    setTimeout(()=>{

      firstCard.classList.remove("selected");
      secondCard.classList.remove("selected");

      resetBoard();

    },700);

  }

}

function resetBoard(){
  firstCard = null;
  secondCard = null;
  lockBoard = false;
}

function checkWin(){

  const hiddenCards = document.querySelectorAll(".hidden");

  if(hiddenCards.length === 24){

    setTimeout(()=>{

      document.getElementById("gameScreen").classList.add("hiddenScreen");

      document.getElementById("winScreen").classList.remove("hiddenScreen");

      document.getElementById("finalScore").innerText =
        "Score Kamu: " + score;

    },500);

  }

}

function nextLevel(){

  currentLevel++;

  if(currentLevel >= levelData.length){

    alert("🎉 Semua Level Selesai!");

    currentLevel = 0;
    score = 0;
  }

  document.getElementById("score").innerText = score;

  document.getElementById("winScreen").classList.add("hiddenScreen");

  document.getElementById("gameScreen").classList.remove("hiddenScreen");

  startGame();
}

function restartGame(){

  currentLevel = 0;
  score = 0;

  document.getElementById("score").innerText = score;

  document.getElementById("winScreen").classList.add("hiddenScreen");

  document.getElementById("gameScreen").classList.remove("hiddenScreen");

  startGame();
}
</script>

</body>
</html>

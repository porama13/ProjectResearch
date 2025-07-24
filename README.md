<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8" />
<title>เกมการ์ดคิดวิเคราะห์ 4P</title>
<style>
  body {
    font-family: 'Prompt', sans-serif;
    background: #f0f8ff;
    text-align: center;
    padding: 20px;
  }
  .card, .start-screen, .end-screen {
    background: white;
    margin: 10px auto;
    padding: 50px 170px;
    border-radius: 12px;
    box-shadow: 0 0 15px rgba(0,0,0,0.15);
    max-width: 700px;
    position: relative;
  }
  button {
    margin: 10px;
    padding: 10px 22px;
    font-size: 16px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    background-color: #007bff;
    color: white;
  }
  button:hover {
    background-color: #0056b3;
  }
  input {
    padding: 10px;
    border-radius: 8px;
    width: 70%;
  }
  #timer {
    font-size: 18px;
    color: #d9534f;
    margin-top: 10px;
  }
  #score {
    font-size: 20px;
    color: #28a745;
  }
  #explosion {
    position: absolute;
    top: 0; left: 0; right: 0; bottom: 0;
    background: rgba(255, 0, 0, 0.8);
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 12px;
    font-size: 48px;
    color: yellow;
    font-weight: bold;
    z-index: 10;
    display: none;
    flex-direction: column;
  }
  #explosion img {
    width: 200px;
    margin-bottom: 20px;
  }
</style>
</head>
<body>

<div class="start-screen" id="startScreen">
  <h1>🎓 เกมการ์ดคิดวิเคราะห์ (หลักการตลาด 4P)</h1>
  <p>ป้อนชื่อผู้เล่น:</p>
  <input id="playerName" placeholder="เช่น นพพร" />
  <br><br>
  <button onclick="startGame()">เริ่มเกม</button>
</div>

<div class="card" id="questionCard" style="display:none;">
  <h1>🧩 สถานการณ์</h1>
  <h3 id="situation" style="white-space: pre-line;"></h3>
  <h1>🔎 ความต้องการ</h1>
  <h3 id="requirement" style="white-space: pre-line;"></h3>
  <h1>💡 แนวทางแก้ไข</h1>
  <h3 id="solution" style="white-space: pre-line;"></h3>
  <h4>แนวทางนี้เหมาะสมหรือไม่?</h4>
  <button onclick="checkAnswer(true)">✅ เหมาะสม</button>
  <button onclick="checkAnswer(false)">❌ ไม่เหมาะสม</button>
  <p id="feedback"></p>
  <p id="score">คะแนน: 0</p>
  <p id="timer"></p>

  <div id="explosion">
    <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/d7/Explosion_icon.svg/240px-Explosion_icon.svg.png" alt="Explosion" />
    <div>💥 หมดเวลาหมดเกม! 💥</div>
  </div>
</div>

<div class="end-screen" id="endScreen" style="display:none;">
  <h1>🎉 จบเกม!</h1>
  <p id="finalScore"></p>
  <h3>🏆 อันดับผู้เล่น</h3>
  <ol id="rankingList"></ol>
  <button onclick="startGameFromEnd()">🔁 เริ่มการแข่งขันใหม่</button>
  <button onclick="clearRanking()">🗑️ เคลียร์อันดับ</button>
</div>

<script>
  // คำถามระดับยาก (4P) มีรายละเอียดมากขึ้น
  const allQuestions = [
    {
      situation: `บริษัทผลิตเครื่องดื่มเพื่อสุขภาพแห่งหนึ่งพบว่าผลิตภัณฑ์ของตนไม่ค่อยได้รับความนิยมในกลุ่มวัยรุ่น 
เนื่องจากบรรจุภัณฑ์ดูธรรมดาและไม่ดึงดูดใจผู้บริโภคเป้าหมายที่ชอบดีไซน์ทันสมัยและแปลกใหม่`,
      requirement: `ปรับปรุงภาพลักษณ์ผลิตภัณฑ์เพื่อดึงดูดกลุ่มลูกค้าวัยรุ่น`,
      solution: `ออกแบบบรรจุภัณฑ์ใหม่ให้ทันสมัยและมีสีสันสดใส`,
      correct: true
    },
    {
      situation: `ร้านค้าออนไลน์ที่จำหน่ายเครื่องใช้ไฟฟ้าพบว่า ลูกค้าระบุว่าค่าจัดส่งสูงเกินไป และมีการเปรียบเทียบกับคู่แข่งที่คิดค่าจัดส่งถูกกว่า 
ทำให้ยอดขายสินค้าลดลงในช่วงเทศกาล`,
      requirement: `ปรับกลยุทธ์การตั้งราคาค่าจัดส่งเพื่อเพิ่มยอดขายในช่วงเทศกาล`,
      solution: `กำหนดค่าจัดส่งแบบเหมาแพงขึ้นในช่วงเทศกาลเพื่อเพิ่มกำไร`,
      correct: false
    },
    {
      situation: `บริษัทขายเสื้อผ้ากีฬาแห่งหนึ่งต้องการขยายตลาดไปยังต่างจังหวัดที่มีร้านค้าปลีกน้อย และลูกค้าส่วนใหญ่ชอบซื้อสินค้าผ่านร้านค้าท้องถิ่น 
ที่คุ้นเคยมากกว่า`,
      requirement: `เพิ่มช่องทางการจัดจำหน่ายเพื่อเข้าถึงลูกค้าในต่างจังหวัด`,
      solution: `เพิ่มตัวแทนจำหน่ายและจัดกิจกรรมส่งเสริมการขายร่วมกับร้านค้าท้องถิ่น`,
      correct: true
    },
    {
      situation: `ธุรกิจร้านกาแฟแห่งหนึ่งมีโปรโมชั่นลดราคากาแฟในช่วงเช้า แต่ลูกค้าส่วนใหญ่ยังไม่ทราบข้อมูลโปรโมชั่นนี้ จึงไม่เข้าร่วมโปรโมชั่นมากเท่าที่ควร`,
      requirement: `ประชาสัมพันธ์โปรโมชั่นลดราคาให้เข้าถึงกลุ่มเป้าหมายมากขึ้น`,
      solution: `แจกใบปลิวในร้านเพียงอย่างเดียวโดยไม่ใช้ช่องทางออนไลน์`,
      correct: false
    },
    {
      situation: `บริษัทผลิตอุปกรณ์กีฬาได้ออกสินค้าใหม่ที่มีคุณสมบัติพิเศษที่แตกต่างจากคู่แข่ง แต่ยังไม่มีการโปรโมทอย่างทั่วถึง 
ทำให้ยอดขายยังไม่เป็นไปตามเป้า`,
      requirement: `เพิ่มยอดขายโดยการสื่อสารจุดเด่นของสินค้าใหม่สู่ลูกค้า`,
      solution: `ใช้สื่อโซเชียลมีเดียและการตลาดแบบอินฟลูเอนเซอร์เพื่อโปรโมทสินค้า`,
      correct: true
    },
    {
      situation: `ร้านอาหารแห่งหนึ่งตั้งราคาอาหารสูงกว่าร้านคู่แข่งในพื้นที่เดียวกัน โดยไม่ได้ให้บริการที่แตกต่างหรือมีคุณภาพดีขึ้น จึงทำให้ลูกค้าหันไปใช้บริการร้านอื่นมากขึ้น`,
      requirement: `ตั้งราคาที่เหมาะสมกับคุณภาพและตลาดในพื้นที่`,
      solution: `ลดราคาลงโดยไม่ปรับปรุงคุณภาพหรือบริการ`,
      correct: false
    },
    {
      situation: `บริษัทจำหน่ายอุปกรณ์อิเล็กทรอนิกส์พบว่า ลูกค้าบางกลุ่มยังไม่สามารถเข้าถึงสินค้าได้สะดวก เพราะไม่มีสาขาในพื้นที่ห่างไกล`,
      requirement: `เพิ่มช่องทางจำหน่ายเพื่อเข้าถึงลูกค้าในพื้นที่ห่างไกล`,
      solution: `เปิดร้านค้าออนไลน์และจัดส่งสินค้าฟรีในพื้นที่เหล่านั้น`,
      correct: true
    },
    {
      situation: `แบรนด์เสื้อผ้าหนึ่งเลือกที่จะใช้โฆษณาเฉพาะทางทีวีในช่วงเวลาที่ไม่เหมาะสม เช่น เวลาตอนดึกซึ่งกลุ่มเป้าหมายไม่ดูทีวี`,
      requirement: `เพิ่มประสิทธิภาพในการประชาสัมพันธ์สินค้า`,
      solution: `เลือกช่องทางโฆษณาออนไลน์ที่กลุ่มเป้าหมายใช้งานบ่อยและเวลาที่เหมาะสม`,
      correct: true
    },
    {
      situation: `บริษัทผลิตสินค้าอุปโภคบริโภคมีการแจกคูปองส่วนลดมากเกินไปจนทำให้กำไรลดลงอย่างมาก แต่ยอดขายเพิ่มขึ้นเพียงเล็กน้อย`,
      requirement: `ปรับโปรโมชั่นเพื่อเพิ่มยอดขายโดยไม่กระทบกำไรอย่างรุนแรง`,
      solution: `แจกคูปองส่วนลดในจำนวนจำกัดและกำหนดเงื่อนไขการใช้คูปองให้ชัดเจน`,
      correct: true
    },
    {
      situation: `ธุรกิจอาหารพร้อมทานพบว่า ลูกค้าในเขตเมืองไม่ค่อยสนใจซื้อสินค้าราคาถูกที่ทำจากวัตถุดิบคุณภาพต่ำ 
ในขณะที่ลูกค้าในชนบทให้ความสำคัญกับราคาเป็นหลักมากกว่า คุณภาพ`,
      requirement: `ปรับกลยุทธ์การตั้งราคาและคุณภาพสินค้าให้เหมาะสมกับตลาดแต่ละกลุ่ม`,
      solution: `ตั้งราคาสูงขึ้นในเขตเมืองโดยใช้วัตถุดิบคุณภาพดี และตั้งราคาถูกในชนบทโดยลดต้นทุนวัตถุดิบ`,
      correct: true
    }
  ];

  let questions = [];
  let current = 0;
  let score = 0;
  let player = "";
  let timer = 90;
  let countdown;
  let gameOver = false;

  function startGame() {
    player = document.getElementById("playerName").value.trim();
    if (!player) {
      alert("กรุณาป้อนชื่อผู้เล่น");
      return;
    }
    questions = shuffle([...allQuestions]).slice(0, 10);
    document.getElementById("startScreen").style.display = "none";
    document.getElementById("endScreen").style.display = "none";
    document.getElementById("questionCard").style.display = "block";
    document.getElementById("explosion").style.display = "none";
    score = 0;
    current = 0;
    gameOver = false;
    timer = 90;
    updateScore();
    updateTimer();
    showQuestion();
    countdown = setInterval(updateTimer, 1000);
  }

  function startGameFromEnd() {
    document.getElementById("playerName").value = player;
    document.getElementById("endScreen").style.display = "none";
    document.getElementById("startScreen").style.display = "block";
  }

  function clearRanking() {
    if (confirm("คุณแน่ใจหรือไม่ที่จะลบอันดับคะแนนทั้งหมด?")) {
      localStorage.removeItem("ranking");
      alert("ลบอันดับคะแนนเรียบร้อยแล้ว");
      document.getElementById("rankingList").innerHTML = "";
    }
  }

  function shuffle(arr) {
    for (let i = arr.length - 1; i > 0; i--) {
      const j = Math.floor(Math.random() * (i + 1));
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
    return arr;
  }

  function showQuestion() {
    if (gameOver) return;
    const q = questions[current];
    document.getElementById("situation").innerText = q.situation;
    document.getElementById("requirement").innerText = q.requirement;
    document.getElementById("solution").innerText = q.solution;
    document.getElementById("feedback").innerText = "";
  }

  function checkAnswer(userAnswer) {
    if (gameOver) return;
    const q = questions[current];
    if (userAnswer === q.correct) {
      score++;
      document.getElementById("feedback").innerText = "✅ ตอบถูก!";
    } else {
      document.getElementById("feedback").innerText = "❌ ตอบผิด!";
    }
    updateScore();
    current++;
    if (current >= questions.length) {
      endGame();
    } else {
      setTimeout(showQuestion, 1000);
    }
  }

  function updateScore() {
    document.getElementById("score").innerText = "คะแนน: " + score;
  }

  function updateTimer() {
    if (gameOver) return;
    timer--;
    const mins = Math.floor(timer / 90);
    const secs = timer % 90;
    document.getElementById("timer").innerText = `⏱️ เวลา: ${mins}:${secs < 10 ? "0" + secs : secs}`;

    if (timer <= 0) {
      clearInterval(countdown);
      explode();
    }
  }

  function explode() {
    gameOver = true;
    document.getElementById("explosion").style.display = "flex";
    const audio = new Audio("https://actions.google.com/sounds/v1/explosions/explosion.mp3");
    audio.play().catch(() => {});
    setTimeout(() => {
      endGame();
    }, 3000);
  }

  function endGame() {
    gameOver = true;
    clearInterval(countdown);
    document.getElementById("questionCard").style.display = "none";
    document.getElementById("endScreen").style.display = "block";
    document.getElementById("finalScore").innerText = `${player} ได้คะแนน ${score} จาก ${questions.length}`;

    let rankings = JSON.parse(localStorage.getItem("ranking") || "[]");
    rankings.push({ name: player, score: score });
    rankings.sort((a, b) => b.score - a.score);
    if(rankings.length > 10) rankings = rankings.slice(0, 10);
    localStorage.setItem("ranking", JSON.stringify(rankings));

    const list = document.getElementById("rankingList");
    list.innerHTML = "";
    rankings.forEach((entry) => {
      const li = document.createElement("li");
      li.textContent = `${entry.name} - ${entry.score} คะแนน`;
      list.appendChild(li);
    });
  }
</script>

</body>
</html>

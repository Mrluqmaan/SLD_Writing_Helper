<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>SLD Writing Helper</title>
<style>
  * {
    box-sizing: border-box;
  }
  body {
    font-family: 'Poppins', sans-serif;
    background: linear-gradient(135deg, #a3d5ff, #ffffff);
    color: #222;
    margin: 0;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px 10px;
  }
  header {
    margin-bottom: 20px;
    text-align: center;
  }
  header h1 {
    font-size: 1.8rem;
    background: linear-gradient(135deg, #0078d7, #00d2ff);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    margin-bottom: 5px;
  }
  header p {
    font-size: 1rem;
    color: #555;
  }
  main {
    background: white;
    border-radius: 16px;
    box-shadow: 0 8px 16px rgba(0,0,0,0.1);
    max-width: 600px;
    width: 100%;
    padding: 20px;
    display: flex;
    flex-direction: column;
    gap: 15px;
  }
  #targetWordContainer {
    font-size: 3rem;
    font-weight: 700;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    gap: 10px;
    justify-content: center;
  }
  .label {
    font-weight: 600;
    font-size: 1.1rem;
    display: block;
    text-align: center;
  }
  .writing-area {
    border: 2px solid #ddd;
    border-radius: 12px;
    position: relative;
    margin-bottom: 10px;
  }
  #writingCanvas {
    width: 100%;
    height: 250px;
    display: block;
    background-color: white;
    border-radius: 10px;
    touch-action: none;
  }
  .button-container {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
  }
  button {
    padding: 12px;
    font-size: 1rem;
    border-radius: 10px;
    border: none;
    background: linear-gradient(135deg, #0078d7, #00d2ff);
    color: white;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
  }
  button:hover, button:focus {
    box-shadow: 0 0 8px #00d2ff;
    transform: translateY(-2px);
    outline: none;
  }
  #clearBtn {
    background: linear-gradient(135deg, #e74c3c, #ff7675);
  }
  #playAudioBtn {
    background-color: transparent;
    border: 2px solid #0078d7;
    padding: 6px 10px;
    font-size: 1.2rem;
    color: #0078d7;
    line-height: 1;
    min-width: 45px;
  }
  #checkBtn {
    background: linear-gradient(135deg, #27ae60, #2ecc71);
  }
  .mode-selector {
    display: flex;
    gap: 10px;
    justify-content: center;
    margin-bottom: 10px;
  }
  .mode-selector button {
    padding: 8px 12px;
    font-size: 0.9rem;
  }
  .mode-selector button.active {
    box-shadow: 0 0 0 2px white, 0 0 0 4px #0078d7;
  }
  .color-picker {
    display: flex;
    gap: 8px;
    margin-bottom: 10px;
    justify-content: center;
  }
  .color-option {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    border: 2px solid #ddd;
    cursor: pointer;
    transition: transform 0.2s;
  }
  .color-option:hover {
    transform: scale(1.1);
  }
  .toolbar {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }
  .brush-size {
    display: flex;
    gap: 8px;
    align-items: center;
  }
  .brush-size button {
    padding: 0;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  @media (max-width: 480px) {
    body {
      padding: 15px 5px;
    }
    header h1 {
      font-size: 1.5rem;
    }
    main {
      padding: 15px;
    }
    #writingCanvas {
      height: 200px;
    }
    #targetWordContainer {
      font-size: 2.5rem;
    }
  }
</style>
</head>
<body>
<header>
  <h1>SLD Writing Helper</h1>
  <p>Practice writing letters, numbers and words with audio support</p>
</header>
<main>
  <div class="mode-selector">
    <button id="lettersMode" class="active">Letters</button>
    <button id="numbersMode">Numbers</button>
    <button id="wordsMode">Words</button>
  </div>
  
  <div class="label">Practice writing:</div>
  <div id="targetWordContainer">
    <div id="targetText">A</div>
    <button id="playAudioBtn" title="Hear the letter/number/word">🔊</button>
  </div>
  
  <div class="toolbar">
    <div class="color-picker">
      <div class="color-option" style="background-color: #000000;" data-color="#000000"></div>
      <div class="color-option" style="background-color: #e74c3c;" data-color="#e74c3c"></div>
      <div class="color-option" style="background-color: #3498db;" data-color="#3498db"></div>
      <div class="color-option" style="background-color: #2ecc71;" data-color="#2ecc71"></div>
      <div class="color-option" style="background-color: #9b59b6;" data-color="#9b59b6"></div>
    </div>
    <div class="brush-size">
      <small>Size:</small>
      <button data-size="2">●</button>
      <button data-size="5">●</button>
      <button data-size="8" style="font-size: 1.2em;">●</button>
    </div>
  </div>
  
  <div class="writing-area">
    <canvas id="writingCanvas"></canvas>
  </div>
  
  <div class="button-container">
    <button id="clearBtn">Clear</button>
    <button id="checkBtn">Check</button>
    <button id="newItemBtn">New</button>
  </div>
</main>

<script>
(function(){
  // Character and word lists
  const letters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('');
  const numbers = '0,1,2,3,4,5,6,7,8,9,10'.split(',');
  const words = [
    'apple', 'ball', 'cat', 'dog', 'egg', 'fish', 'girl', 'hat', 'ice', 'jump',
    'kite', 'lion', 'milk', 'nest', 'orange', 'pig', 'queen', 'rain', 'sun', 'tree',
    'umbrella', 'van', 'water', 'box', 'yes', 'zoo', 'book', 'car', 'duck', 'eye',
    'frog', 'goat', 'hand', 'ink', 'jug', 'key', 'leg', 'moon', 'nose', 'owl',
    'pen', 'quilt', 'rat', 'shoe', 'toy', 'up', 'vet', 'wall', 'fox', 'yard'
  ];
  
  // DOM elements
  const targetTextElm = document.getElementById('targetText');
  const canvas = document.getElementById('writingCanvas');
  const ctx = canvas.getContext('2d');
  const playAudioBtn = document.getElementById('playAudioBtn');
  const clearBtn = document.getElementById('clearBtn');
  const checkBtn = document.getElementById('checkBtn');
  const newItemBtn = document.getElementById('newItemBtn');
  const lettersModeBtn = document.getElementById('lettersMode');
  const numbersModeBtn = document.getElementById('numbersMode');
  const wordsModeBtn = document.getElementById('wordsMode');
  const colorOptions = document.querySelectorAll('.color-option');
  const sizeButtons = document.querySelectorAll('.brush-size button');
  
  // Drawing variables
  let isDrawing = false;
  let currentColor = '#000000';
  let brushSize = 5;
  let lastX = 0;
  let lastY = 0;
  let currentMode = 'letters';
  
  // Initialize canvas
  function initCanvas() {
    ctx.lineCap = 'round';
    ctx.lineJoin = 'round';
    ctx.strokeStyle = currentColor;
    ctx.lineWidth = brushSize;
    
    function resizeCanvas() {
      const rect = canvas.getBoundingClientRect();
      canvas.width = rect.width;
      canvas.height = rect.height;
      
      // Redraw canvas content after resize (if any)
      if (canvas.dataset.hasContent === 'true') {
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        ctx.putImageData(imageData, 0, 0);
      }
    }
    
    // Set initial size and listen for window resize
    resizeCanvas();
    window.addEventListener('resize', resizeCanvas);
  }
  
  // Drawing functions
  function startDrawing(e) {
    isDrawing = true;
    [lastX, lastY] = getCursorPosition(e);
  }
  
  function draw(e) {
    if (!isDrawing) return;
    
    const [x, y] = getCursorPosition(e);
    
    ctx.beginPath();
    ctx.moveTo(lastX, lastY);
    ctx.lineTo(x, y);
    ctx.stroke();
    
    lastX = x;
    lastY = y;
    canvas.dataset.hasContent = 'true';
  }
  
  function stopDrawing() {
    isDrawing = false;
  }
  
  function getCursorPosition(e) {
    const rect = canvas.getBoundingClientRect();
    let clientX, clientY;
    
    if (e.type.includes('touch')) {
      clientX = e.touches[0].clientX;
      clientY = e.touches[0].clientY;
    } else {
      clientX = e.clientX;
      clientY = e.clientY;
    }
    
    return [
      clientX - rect.left,
      clientY - rect.top
    ];
  }
  
  function clearCanvas() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    canvas.dataset.hasContent = 'false';
  }
  
  // Speech functions
  function speakItem(item) {
    if ('speechSynthesis' in window) {
      speechSynthesis.cancel();
      const utterance = new SpeechSynthesisUtterance();
      
      if (currentMode === 'letters') {
        utterance.text = `The letter ${item}`;
      } else if (currentMode === 'numbers') {
        utterance.text = `The number ${item}`;
      } else {
        utterance.text = item; // For words, just say the word
      }
      
      utterance.lang = 'en-US';
      utterance.rate = 0.8; // Slightly slower for clarity
      speechSynthesis.speak(utterance);
    } else {
      alert('Speech synthesis not supported in your browser');
    }
  }
  
  // Item selection functions
  function getRandomLetter() {
    return letters[Math.floor(Math.random() * letters.length)];
  }
  
  function getRandomNumber() {
    return numbers[Math.floor(Math.random() * numbers.length)];
  }
  
  function getRandomWord() {
    return words[Math.floor(Math.random() * words.length)];
  }
  
  function setNewItem() {
    let item;
    if (currentMode === 'letters') {
      item = getRandomLetter();
    } else if (currentMode === 'numbers') {
      item = getRandomNumber();
    } else {
      item = getRandomWord();
    }
    targetTextElm.textContent = item;
    clearCanvas();
    speakItem(item);
  }
  
  function setMode(mode) {
    currentMode = mode;
    lettersModeBtn.classList.remove('active');
    numbersModeBtn.classList.remove('active');
    wordsModeBtn.classList.remove('active');
    
    if (mode === 'letters') {
      lettersModeBtn.classList.add('active');
    } else if (mode === 'numbers') {
      numbersModeBtn.classList.add('active');
    } else {
      wordsModeBtn.classList.add('active');
    }
    
    setNewItem();
  }
  
  // Event listeners
  function setupEventListeners() {
    // Canvas drawing events
    canvas.addEventListener('mousedown', startDrawing);
    canvas.addEventListener('mousemove', draw);
    canvas.addEventListener('mouseup', stopDrawing);
    canvas.addEventListener('mouseout', stopDrawing);
    
    canvas.addEventListener('touchstart', (e) => {
      e.preventDefault();
      startDrawing(e);
    });
    canvas.addEventListener('touchmove', (e) => {
      e.preventDefault();
      draw(e);
    });
    canvas.addEventListener('touchend', (e) => {
      e.preventDefault();
      stopDrawing();
    });
    
    // Buttons
    playAudioBtn.addEventListener('click', () => speakItem(targetTextElm.textContent));
    clearBtn.addEventListener('click', clearCanvas);
    newItemBtn.addEventListener('click', setNewItem);
    checkBtn.addEventListener('click', () => {
      alert("Great practice! Keep going.");
      clearCanvas();
    });
    
    // Mode selection
    lettersModeBtn.addEventListener('click', () => setMode('letters'));
    numbersModeBtn.addEventListener('click', () => setMode('numbers'));
    wordsModeBtn.addEventListener('click', () => setMode('words'));
    
    // Color picker
    colorOptions.forEach(option => {
      option.addEventListener('click', () => {
        currentColor = option.dataset.color;
        ctx.strokeStyle = currentColor;
      });
    });
    
    // Brush size
    sizeButtons.forEach(button => {
      button.addEventListener('click', () => {
        brushSize = parseInt(button.dataset.size);
        ctx.lineWidth = brushSize;
      });
    });
  }
  
  // Initialize app
  function init() {
    initCanvas();
    setupEventListeners();
    setMode('letters'); // Start with letters mode
  }
  
  // Start the app
  init();
})();
</script>
</body>
</html>

  

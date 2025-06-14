# SLD_Writing_Helper
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>SLD Writing Helper</title>
<style>
  /* Base reset and font */
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
    padding: 32px 16px;
  }
  header {
    margin-bottom: 32px;
    text-align: center;
  }
  header h1 {
    font-size: 2.5rem;
    background: linear-gradient(135deg, #0078d7, #00d2ff);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }
  main {
    background: white;
    border-radius: 16px;
    box-shadow: 0 8px 16px rgba(0,0,0,0.1);
    max-width: 480px;
    width: 100%;
    padding: 24px;
    display: flex;
    flex-direction: column;
    gap: 24px;
  }
  label {
    font-weight: 600;
    font-size: 1.2rem;
    margin-bottom: 8px;
    display: block;
  }
  #targetWordContainer {
    font-size: 2.2rem;
    font-weight: 700;
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    gap: 12px;
  }
  #targetText {
    user-select: none;
  }
  button {
    padding: 16px 24px;
    font-size: 1.2rem;
    border-radius: 12px;
    border: none;
    background: linear-gradient(135deg, #0078d7, #00d2ff);
    color: white;
    font-weight: 700;
    cursor: pointer;
    transition: box-shadow 0.3s ease;
  }
  button:hover, button:focus {
    box-shadow: 0 0 8px #00d2ff;
    outline: none;
  }
  .feedback {
    font-size: 1.4rem;
    font-weight: 600;
    min-height: 1.5em;
  }

  #playAudioBtn {
    background-color: transparent;
    border: 2px solid #0078d7;
    padding: 8px 12px;
    cursor: pointer;
    border-radius: 12px;
    font-size: 1.5rem;
    color: #0078d7;
    line-height: 1;
    transition: background-color 0.3s, color 0.3s;
  }
  #playAudioBtn:hover, #playAudioBtn:focus {
    background-color: #0078d7;
    color: white;
    outline: none;
  }

  @media (max-width: 480px) {
    body {
      padding: 24px 8px;
    }
    main {
      padding: 16px;
    }
    button, #playAudioBtn {
      font-size: 1rem;
      padding: 12px 18px;
    }
    #targetWordContainer {
      font-size: 1.6rem;
      gap: 8px;
    }
  }
</style>
</head>
<body>
<header>
  <h1>SLD Writing Helper</h1>
  <p>Practice writing letters and words here with gentle feedback.</p>
</header>
<main>
  <label for="practiceInput">Type the letter or word below:</label>
  <div id="targetWordContainer" aria-live="polite" aria-atomic="true">
    <div id="targetText">apple</div>
    <button id="playAudioBtn" aria-label="Play pronunciation of the word" title="Play pronunciation">🔊</button>
  </div>
  <input type="text" id="practiceInput" aria-describedby="feedback" autocomplete="off" autocorrect="off" autocapitalize="none" spellcheck="false" />
  <button id="checkBtn">Check</button>
  <div id="feedback" class="feedback" aria-live="polite" aria-atomic="true"></div>
  <button id="newWordBtn" style="background:#00b894; margin-top:12px;">New Word</button>
</main>

<script>
  (function(){
    const words = [
      'apple', 'book', 'cat', 'dog', 'elephant', 'flower', 'grape', 'house', 'ice', 'juice',
      'ball', 'car', 'bird', 'fish', 'tree', 'sun', 'moon', 'star', 'hat', 'pen',
      'cup', 'shoe', 'milk', 'egg', 'rain', 'snake', 'bee', 'cow', 'door',
      'fox', 'girl', 'hand', 'king', 'leaf', 'man', 'nest', 'pig', 'queen', 'rat',
      'toy', 'van', 'wall', 'yard', 'zoo', 'ant', 'bag', 'cap', 'duck',
      'ear', 'fan', 'goat', 'hill', 'ink', 'jar', 'kite', 'lion'
    ];
    const targetTextElm = document.getElementById('targetText');
    const inputElm = document.getElementById('practiceInput');
    const feedbackElm = document.getElementById('feedback');
    const checkBtn = document.getElementById('checkBtn');
    const newWordBtn = document.getElementById('newWordBtn');
    const playAudioBtn = document.getElementById('playAudioBtn');

    // Text-to-Speech Function
    function speakWord(word){
      if('speechSynthesis' in window){
        speechSynthesis.cancel(); // Cancel any previous speech
        const utterance = new SpeechSynthesisUtterance(word);
        utterance.lang = 'en-US';
        speechSynthesis.speak(utterance);
      } else {
        alert('Sorry, your browser does not support speech synthesis.');
      }
    }

    function pickRandomWord(){
      return words[Math.floor(Math.random()*words.length)];
    }
    function setNewWord(){
      const word = pickRandomWord();
      targetTextElm.textContent = word;
      inputElm.value = '';
      feedbackElm.textContent = '';
      inputElm.focus();
      // Automatically play audio on new word display
      speakWord(word);
    }

    // Check user input against target text (case-insensitive)
    function checkInput(){
      const userInput = inputElm.value.trim().toLowerCase();
      const target = targetTextElm.textContent.toLowerCase();

      if(userInput === ''){
        feedbackElm.textContent = 'Please type the word.';
        feedbackElm.style.color = '#e74c3c'; // Red
        return;
      }

      if(userInput === target){
        feedbackElm.textContent = 'Great job! You typed it correctly.';
        feedbackElm.style.color = '#27ae60'; // Green
      } else {
        feedbackElm.textContent = `Try again! You typed "${userInput}".`;
        feedbackElm.style.color = '#e67e22'; // Orange
      }
    }

    checkBtn.addEventListener('click', checkInput);
    inputElm.addEventListener('keydown', (e) => {
      if(e.key === 'Enter'){
        e.preventDefault();
        checkInput();
      }
    });
    newWordBtn.addEventListener('click', setNewWord);
    playAudioBtn.addEventListener('click', () => {
      speakWord(targetTextElm.textContent);
    });

    // Init
    setNewWord();
  })();
</script>
</body>
</html>


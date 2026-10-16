<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Quebra-Código: 30 Níveis de Lógica</title>
  <style>
    * {
      box-sizing: border-box;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }
    body {
      background-color: #0f172a;
      color: #f8fafc;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
      padding: 16px;
    }
    .card {
      background-color: #1e293b;
      border-radius: 12px;
      padding: 24px;
      max-width: 520px;
      width: 100%;
      box-shadow: 0 10px 25px rgba(0,0,0,0.5);
    }
    .header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 16px;
    }
    h1 {
      margin: 0;
      font-size: 1.5rem;
      color: #38bdf8;
    }
    .btn-secondary {
      background-color: #334155;
      color: #f8fafc;
      border: 1px solid #475569;
      padding: 6px 12px;
      border-radius: 6px;
      cursor: pointer;
      font-size: 0.85rem;
    }
    .btn-secondary:hover {
      background-color: #475569;
    }
    .level-info {
      background-color: #0f172a;
      padding: 12px;
      border-radius: 8px;
      font-size: 0.9rem;
      color: #cbd5e1;
      margin-bottom: 16px;
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 6px;
    }
    .inputs {
      display: flex;
      gap: 8px;
      justify-content: center;
      margin: 20px 0;
    }
    .digit-input {
      width: 46px;
      height: 50px;
      font-size: 1.4rem;
      text-align: center;
      border-radius: 8px;
      border: 2px solid #475569;
      background-color: #0f172a;
      color: #f8fafc;
      font-weight: bold;
    }
    .digit-input:focus {
      outline: none;
      border-color: #38bdf8;
    }
    .btn-primary {
      width: 100%;
      padding: 12px;
      border: none;
      border-radius: 8px;
      background-color: #0284c7;
      color: white;
      font-size: 1rem;
      font-weight: bold;
      cursor: pointer;
      transition: background 0.2s;
    }
    .btn-primary:hover {
      background-color: #0369a1;
    }
    .history {
      margin-top: 16px;
      max-height: 220px;
      overflow-y: auto;
    }
    .attempt {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 8px 12px;
      background-color: #334155;
      margin-bottom: 6px;
      border-radius: 6px;
    }
    .attempt-digits {
      font-weight: bold;
      letter-spacing: 4px;
      font-size: 1.1rem;
    }
    .feedback {
      display: flex;
      gap: 6px;
    }
    .badge {
      padding: 4px 8px;
      border-radius: 4px;
      font-size: 0.8rem;
      font-weight: bold;
    }
    .exact { background-color: #22c55e; color: #022c22; }
    .partial { background-color: #eab308; color: #422006; }
    .message {
      text-align: center;
      font-weight: bold;
      margin-top: 12px;
      min-height: 24px;
    }
    
    /* Grid de Níveis */
    .levels-modal {
      display: none;
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(15, 23, 42, 0.95);
      justify-content: center;
      align-items: center;
      padding: 20px;
      z-index: 100;
    }
    .levels-grid {
      display: grid;
      grid-template-columns: repeat(5, 1fr);
      gap: 10px;
      max-width: 400px;
      width: 100%;
      max-height: 80vh;
      overflow-y: auto;
      padding: 10px;
    }
    .level-btn {
      padding: 12px;
      border-radius: 8px;
      border: none;
      background-color: #334155;
      color: #f8fafc;
      font-weight: bold;
      cursor: pointer;
    }
    .level-btn.unlocked {
      background-color: #0284c7;
    }
    .level-btn.active {
      border: 2px solid #38bdf8;
      box-shadow: 0 0 10px #38bdf8;
    }
    .level-btn:disabled {
      opacity: 0.4;
      cursor: not-allowed;
    }
  </style>
</head>
<body>

  <div class="card">
    <div class="header">
      <h1 id="level-title">Nível 1</h1>
      <button class="btn-secondary" onclick="toggleLevelsMenu()">Escolher Nível 🗺️</button>
    </div>

    <div class="level-info">
      <span><b>Tamanho:</b> <span id="info-length">3</span> dígitos</span>
      <span><b>Números:</b> 1 a <span id="info-max">4</span></span>
      <span><b>Repetição:</b> <span id="info-repeat">Não</span></span>
      <span><b>Tentativas:</b> <span id="info-attempts">12</span></span>
    </div>

    <div class="inputs" id="inputs-container"></div>

    <button class="btn-primary" id="btn-action" onclick="makeGuess()">Tentar Palpite</button>

    <div class="message" id="message"></div>
    <div class="history" id="history"></div>
  </div>

  <!-- Modal Seletor de Níveis -->
  <div class="levels-modal" id="levels-modal">
    <div style="text-align: center; width: 100%; max-width: 420px;">
      <h2>Selecione o Nível</h2>
      <div class="levels-grid" id="levels-grid"></div>
      <br>
      <button class="btn-secondary" onclick="toggleLevelsMenu()">Fechar</button>
    </div>
  </div>

  <script>
    // Configuração dos 30 Níveis
    const levels = [];
    
    // Níveis 1-10: Fácil (3 a 4 dígitos, sem repetição)
    for (let i = 1; i <= 10; i++) {
      levels.push({
        level: i,
        length: i <= 5 ? 3 : 4,
        maxDigit: Math.min(4 + Math.floor((i - 1) / 2), 6),
        allowRepeat: false,
        attempts: Math.max(12 - Math.floor((i - 1) / 2), 8)
      });
    }

    // Níveis 11-20: Médio (4 a 5 dígitos, introdução de repetição)
    for (let i = 11; i <= 20; i++) {
      levels.push({
        level: i,
        length: i >= 19 ? 5 : 4,
        maxDigit: Math.min(5 + Math.floor((i - 11) / 3), 7),
        allowRepeat: i >= 16,
        attempts: Math.max(10 - Math.floor((i - 11) / 3), 7)
      });
    }

    // Níveis 21-30: Difícil / Mestre (5 dígitos, repetição e mais opções)
    for (let i = 21; i <= 30; i++) {
      levels.push({
        level: i,
        length: 5,
        maxDigit: Math.min(7 + Math.floor((i - 21) / 4), 9),
        allowRepeat: i >= 24,
        attempts: Math.max(10 - (i - 21), 6)
      });
    }

    let currentLevelIndex = 0;
    let unlockedLevel = parseInt(localStorage.getItem('unlockedLevel')) || 1;
    let secret = [];
    let attemptsLeft = 0;
    let gameOver = false;

    function initLevel(index) {
      currentLevelIndex = index;
      const config = levels[currentLevelIndex];

      document.getElementById('level-title').innerText = `Nível ${config.level}`;
      document.getElementById('info-length').innerText = config.length;
      document.getElementById('info-max').innerText = config.maxDigit;
      document.getElementById('info-repeat').innerText = config.allowRepeat ? 'Sim' : 'Não';
      document.getElementById('info-attempts').innerText = config.attempts;

      // Gerar Senha Secreta
      secret = [];
      if (config.allowRepeat) {
        for (let i = 0; i < config.length; i++) {
          secret.push(Math.floor(Math.random() * config.maxDigit) + 1);
        }
      } else {
        const pool = [];
        for (let i = 1; i <= config.maxDigit; i++) pool.push(i);
        while (secret.length < config.length) {
          const idx = Math.floor(Math.random() * pool.length);
          secret.push(pool.splice(idx, 1)[0]);
        }
      }

      attemptsLeft = config.attempts;
      gameOver = false;

      // Criar Caixas de Entrada Dinâmicas
      const container = document.getElementById('inputs-container');
      container.innerHTML = '';
      for (let i = 0; i < config.length; i++) {
        const input = document.createElement('input');
        input.type = 'number';
        input.min = '1';
        input.max = config.maxDigit;
        input.className = 'digit-input';
        input.id = `d${i}`;
        input.setAttribute('maxlength', '1');
        if (i === 0) input.autofocus = true;

        input.addEventListener('input', () => {
          if (input.value.length === 1 && i < config.length - 1) {
            document.getElementById(`d${i + 1}`).focus();
          }
        });

        container.appendChild(input);
      }

      document.getElementById('history').innerHTML = '';
      document.getElementById('message').innerText = `Tentativas restantes: ${attemptsLeft}`;
      document.getElementById('btn-action').innerText = 'Tentar Palpite';
      clearInputs();
    }

    function clearInputs() {
      const config = levels[currentLevelIndex];
      for (let i = 0; i < config.length; i++) {
        const el = document.getElementById(`d${i}`);
        if (el) el.value = '';
      }
      const first = document.getElementById('d0');
      if (first) first.focus();
    }

    function makeGuess() {
      if (gameOver) {
        initLevel(currentLevelIndex);
        return;
      }

      const config = levels[currentLevelIndex];
      const guess = [];

      for (let i = 0; i < config.length; i++) {
        const val = parseInt(document.getElementById(`d${i}`).value);
        guess.push(val);
      }

      // Validação
      if (guess.some(isNaN) || guess.some(n => n < 1 || n > config.maxDigit)) {
        alert(`Digite ${config.length} números entre 1 e ${config.maxDigit}.`);
        return;
      }

      if (!config.allowRepeat && new Set(guess).size !== config.length) {
        alert('Este nível não permite números repetidos!');
        return;
      }

      // Cálculo do Feedback (Mastermind Algoritmo Exato)
      let exact = 0;
      let partial = 0;
      const secretUsed = new Array(config.length).fill(false);
      const guessUsed = new Array(config.length).fill(false);

      for (let i = 0; i < config.length; i++) {
        if (guess[i] === secret[i]) {
          exact++;
          secretUsed[i] = true;
          guessUsed[i] = true;
        }
      }

      for (let i = 0; i < config.length; i++) {
        if (guessUsed[i]) continue;
        for (let j = 0; j < config.length; j++) {
          if (!secretUsed[j] && guess[i] === secret[j]) {
            partial++;
            secretUsed[j] = true;
            break;
          }
        }
      }

      attemptsLeft--;
      renderHistory(guess.join(' '), exact, partial);

      if (exact === config.length) {
        document.getElementById('message').innerText = '🎉 Excelente! Nível Concluído!';
        if (currentLevelIndex + 2 > unlockedLevel) {
          unlockedLevel = Math.min(currentLevelIndex + 2, 30);
          localStorage.setItem('unlockedLevel', unlockedLevel);
        }
        endGame(true);
      } else if (attemptsLeft === 0) {
        document.getElementById('message').innerText = `❌ Fim de jogo! A senha era: ${secret.join('')}`;
        endGame(false);
      } else {
        document.getElementById('message').innerText = `Tentativas restantes: ${attemptsLeft}`;
        clearInputs();
      }
    }

    function renderHistory(guessStr, exact, partial) {
      const historyDiv = document.getElementById('history');
      const item = document.createElement('div');
      item.className = 'attempt';
      item.innerHTML = `
        <span class="attempt-digits">${guessStr}</span>
        <div class="feedback">
          <span class="badge exact">${exact} exato(s)</span>
          <span class="badge partial">${partial} deslocado(s)</span>
        </div>
      `;
      historyDiv.prepend(item);
    }

    function endGame(won) {
      gameOver = true;
      const btn = document.getElementById('btn-action');
      if (won && currentLevelIndex < 29) {
        btn.innerText = 'Próximo Nível ➔';
        btn.onclick = () => {
          btn.onclick = makeGuess;
          initLevel(currentLevelIndex + 1);
        };
      } else {
        btn.innerText = 'Tentar Novamente';
        btn.onclick = () => {
          btn.onclick = makeGuess;
          initLevel(currentLevelIndex);
        };
      }
    }

    function toggleLevelsMenu() {
      const modal = document.getElementById('levels-modal');
      if (modal.style.display === 'flex') {
        modal.style.display = 'none';
      } else {
        renderLevelsGrid();
        modal.style.display = 'flex';
      }
    }

    function renderLevelsGrid() {
      const grid = document.getElementById('levels-grid');
      grid.innerHTML = '';
      levels.forEach((lvl, idx) => {
        const btn = document.createElement('button');
        btn.className = `level-btn ${lvl.level <= unlockedLevel ? 'unlocked' : ''} ${idx === currentLevelIndex ? 'active' : ''}`;
        btn.innerText = lvl.level;
        btn.disabled = lvl.level > unlockedLevel;
        btn.onclick = () => {
          initLevel(idx);
          toggleLevelsMenu();
        };
        grid.appendChild(btn);
      });
    }

    initLevel(0);
  </script>
</body>
</html>
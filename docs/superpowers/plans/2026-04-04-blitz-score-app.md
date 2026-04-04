# Blitz Score App - Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a mobile-friendly single-page scoring app for Dutch Blitz card game with persistence, history tracking, and global player rankings.

**Architecture:** Single HTML file with embedded CSS and JS. Four screen states (config, game, results, history) toggled via CSS class on a root container. All data persisted in localStorage under two keys: `blitzGame` (current game) and `blitzHistory` (past games array).

**Tech Stack:** HTML5, CSS3 (variables, Grid, Flexbox), vanilla JavaScript (ES6+), localStorage.

**Spec:** `docs/superpowers/specs/2026-04-04-blitz-score-app-design.md`

---

## File Structure

Single file:
- **Create:** `index.html` — contains all HTML, CSS (`<style>`), and JS (`<script>`)

---

## Task 1: HTML Skeleton + CSS Foundation

**Files:**
- Create: `index.html`

Sets up the HTML document, viewport meta, CSS variables, base layout, and screen containers.

- [ ] **Step 1: Create the HTML skeleton with all screen containers**

```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Blitz Score</title>
    <style>
        /* CSS goes here - added in next step */
    </style>
</head>
<body>
    <div id="app" class="screen-config">
        <!-- Config Screen -->
        <div id="screen-config" class="screen">
            <h1 class="app-title">&#9824; Blitz Score &#9829;</h1>
        </div>

        <!-- Game Screen -->
        <div id="screen-game" class="screen">
        </div>

        <!-- Results Screen -->
        <div id="screen-results" class="screen">
        </div>

        <!-- History Screen -->
        <div id="screen-history" class="screen">
        </div>

        <!-- Modal overlay -->
        <div id="modal-overlay" class="modal-overlay hidden">
            <div id="modal-content" class="modal-content">
            </div>
        </div>
    </div>
    <script>
        // JS goes here - added in later tasks
    </script>
</body>
</html>
```

- [ ] **Step 2: Add CSS reset, variables, and base styles**

Inside the `<style>` tag, add:

```css
*, *::before, *::after {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

:root {
    --green-dark: #1a3409;
    --green-mid: #2d5016;
    --green-light: #3d6b1e;
    --card-bg: #f5f0e8;
    --card-shadow: 0 4px 12px rgba(0,0,0,0.3);
    --red: #e74c3c;
    --blue: #3498db;
    --yellow: #f1c40f;
    --orange: #e67e22;
    --gold: #ffd700;
    --silver: #c0c0c0;
    --bronze: #cd7f32;
    --text-light: #ffffff;
    --text-dark: #2c2c2c;
    --radius: 12px;
    --touch-min: 44px;
}

html, body {
    height: 100%;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
    background: linear-gradient(135deg, var(--green-mid), var(--green-dark));
    color: var(--text-light);
    overflow-x: hidden;
}

#app {
    min-height: 100%;
    padding: 16px;
    max-width: 800px;
    margin: 0 auto;
}

.screen { display: none; }
.screen-config #screen-config,
.screen-game #screen-game,
.screen-results #screen-results,
.screen-history #screen-history { display: block; }

.app-title {
    text-align: center;
    font-size: 2rem;
    margin-bottom: 24px;
    text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
}

.card {
    background: var(--card-bg);
    border-radius: var(--radius);
    box-shadow: var(--card-shadow);
    color: var(--text-dark);
    padding: 20px;
    margin-bottom: 16px;
}

.btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    min-height: var(--touch-min);
    min-width: var(--touch-min);
    padding: 12px 24px;
    border: none;
    border-radius: var(--radius);
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: transform 0.1s, box-shadow 0.1s;
}
.btn:active { transform: scale(0.96); }

.btn-primary {
    background: var(--orange);
    color: white;
    box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

.btn-secondary {
    background: var(--green-light);
    color: white;
}

.btn-danger {
    background: var(--red);
    color: white;
}

.hidden { display: none !important; }

/* Modal */
.modal-overlay {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.6);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 100;
    padding: 16px;
}
.modal-content {
    background: var(--card-bg);
    color: var(--text-dark);
    border-radius: var(--radius);
    padding: 24px;
    width: 100%;
    max-width: 500px;
    max-height: 90vh;
    overflow-y: auto;
    box-shadow: 0 8px 32px rgba(0,0,0,0.4);
}

@media (max-width: 768px) {
    .modal-content {
        max-width: 100%;
        max-height: 100%;
        height: 100%;
        border-radius: 0;
    }
    .modal-overlay { padding: 0; }
}
```

- [ ] **Step 3: Open in browser and verify**

Run: `open index.html` (or open manually in browser)

Expected: Dark green gradient background, title "&#9824; Blitz Score &#9829;" centered in white text. No content errors in console.

- [ ] **Step 4: Commit**

```bash
git init
git add index.html
git commit -m "feat: HTML skeleton with CSS foundation and design system"
```

---

## Task 2: Config Screen (Player Setup)

**Files:**
- Modify: `index.html`

Build the player configuration form: player count selector, name inputs, start button.

- [ ] **Step 1: Add config screen HTML**

Replace the content of `#screen-config` with:

```html
<div id="screen-config" class="screen">
    <h1 class="app-title">&#9824; Blitz Score &#9829;</h1>
    <div class="card">
        <label class="config-label">Nombre de joueurs</label>
        <div class="player-count-selector" id="player-count-selector">
            <!-- Buttons 2-8 generated by JS -->
        </div>
        <div id="player-names" class="player-names">
            <!-- Name inputs generated by JS -->
        </div>
        <button class="btn btn-primary btn-block" id="btn-start">Commencer la partie</button>
    </div>
    <button class="btn btn-secondary btn-block" id="btn-show-history-config" style="margin-top:12px; width:100%;">
        &#128203; Historique des parties
    </button>
</div>
```

- [ ] **Step 2: Add config-specific CSS**

```css
.config-label {
    display: block;
    font-weight: 700;
    margin-bottom: 12px;
    font-size: 1.1rem;
}

.player-count-selector {
    display: flex;
    gap: 8px;
    margin-bottom: 20px;
    flex-wrap: wrap;
}

.player-count-btn {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    border: 3px solid var(--green-mid);
    background: white;
    color: var(--green-mid);
    font-size: 1.2rem;
    font-weight: 700;
    cursor: pointer;
    transition: all 0.15s;
}
.player-count-btn.active {
    background: var(--green-mid);
    color: white;
}

.player-names {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-bottom: 20px;
}

.player-name-input {
    width: 100%;
    padding: 12px 16px;
    border: 2px solid #ddd;
    border-radius: 8px;
    font-size: 1rem;
    outline: none;
    transition: border-color 0.15s;
}
.player-name-input:focus {
    border-color: var(--green-mid);
}

.btn-block { width: 100%; }
```

- [ ] **Step 3: Add config JS logic**

Inside the `<script>` tag, add the complete config logic:

```javascript
// ===== STATE =====
let state = {
    screen: 'config',
    playerCount: 4,
    players: [],
    rounds: [],
    inputMode: 'direct'
};

let history = [];

// ===== PERSISTENCE =====
function saveGame() {
    if (state.players.length > 0) {
        localStorage.setItem('blitzGame', JSON.stringify({
            players: state.players,
            rounds: state.rounds,
            inputMode: state.inputMode
        }));
    }
}

function loadGame() {
    const saved = localStorage.getItem('blitzGame');
    if (saved) {
        const data = JSON.parse(saved);
        state.players = data.players;
        state.rounds = data.rounds || [];
        state.inputMode = data.inputMode || 'direct';
        return true;
    }
    return false;
}

function clearGame() {
    localStorage.removeItem('blitzGame');
}

function saveHistory() {
    localStorage.setItem('blitzHistory', JSON.stringify(history));
}

function loadHistory() {
    const saved = localStorage.getItem('blitzHistory');
    history = saved ? JSON.parse(saved) : [];
}

// ===== SCREEN MANAGEMENT =====
function showScreen(name) {
    state.screen = name;
    const app = document.getElementById('app');
    app.className = 'screen-' + name;
}

// ===== CONFIG SCREEN =====
function renderConfigScreen() {
    const selector = document.getElementById('player-count-selector');
    selector.innerHTML = '';
    for (let i = 2; i <= 8; i++) {
        const btn = document.createElement('button');
        btn.className = 'player-count-btn' + (i === state.playerCount ? ' active' : '');
        btn.textContent = i;
        btn.addEventListener('click', () => {
            state.playerCount = i;
            renderConfigScreen();
        });
        selector.appendChild(btn);
    }

    const namesDiv = document.getElementById('player-names');
    namesDiv.innerHTML = '';
    for (let i = 0; i < state.playerCount; i++) {
        const input = document.createElement('input');
        input.type = 'text';
        input.className = 'player-name-input';
        input.placeholder = 'Joueur ' + (i + 1);
        input.maxLength = 20;
        input.dataset.index = i;
        namesDiv.appendChild(input);
    }
}

function startGame() {
    const inputs = document.querySelectorAll('.player-name-input');
    const names = [];
    inputs.forEach((input, i) => {
        const name = input.value.trim() || ('Joueur ' + (i + 1));
        names.push(name);
    });
    state.players = names;
    state.rounds = [];
    saveGame();
    showScreen('game');
    renderGameScreen();
}

// ===== INIT =====
function init() {
    loadHistory();

    if (loadGame()) {
        showScreen('game');
        renderGameScreen();
    } else {
        showScreen('config');
        renderConfigScreen();
    }

    document.getElementById('btn-start').addEventListener('click', startGame);
    document.getElementById('btn-show-history-config').addEventListener('click', () => {
        showScreen('history');
        renderHistoryScreen();
    });
}

// Placeholder - implemented in Task 3
function renderGameScreen() {}
function renderHistoryScreen() {}

document.addEventListener('DOMContentLoaded', init);
```

- [ ] **Step 4: Verify in browser**

Open `index.html`. Expected:
- Player count buttons 2-8, "4" highlighted by default
- 4 name input fields with placeholders "Joueur 1" through "Joueur 4"
- Clicking a different number updates the input count
- "Commencer la partie" button present
- "Historique des parties" button below

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: config screen with player count selector and name inputs"
```

---

## Task 3: Game Screen - Score Table

**Files:**
- Modify: `index.html`

Build the score table with player columns, round rows, totals row, leader highlight, and danger indicator.

- [ ] **Step 1: Add game screen HTML structure**

Replace `#screen-game` content with:

```html
<div id="screen-game" class="screen">
    <div class="game-header">
        <button class="btn btn-secondary btn-sm" id="btn-menu">&#9776;</button>
        <h1 class="app-title" style="margin-bottom:0; font-size:1.5rem;">&#9824; Blitz Score &#9829;</h1>
        <button class="btn btn-secondary btn-sm" id="btn-history-game">&#128203;</button>
    </div>
    <div class="card table-card">
        <div class="table-scroll" id="table-scroll">
            <table class="score-table" id="score-table">
                <!-- Rendered by JS -->
            </table>
        </div>
    </div>
    <div class="game-actions" id="game-actions">
        <!-- Menu buttons rendered by JS when open -->
    </div>
    <button class="fab" id="btn-add-round">+</button>
</div>
```

- [ ] **Step 2: Add game screen CSS**

```css
.game-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 16px;
    gap: 8px;
}

.btn-sm {
    padding: 8px 12px;
    font-size: 1.2rem;
    min-height: 40px;
    min-width: 40px;
}

.table-card { padding: 0; overflow: hidden; }

.table-scroll {
    overflow-x: auto;
    -webkit-overflow-scrolling: touch;
}

.score-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.95rem;
}

.score-table th,
.score-table td {
    padding: 10px 12px;
    text-align: center;
    border-bottom: 1px solid #e0d8cc;
    white-space: nowrap;
}

.score-table th {
    background: var(--green-mid);
    color: white;
    font-weight: 700;
    position: sticky;
    top: 0;
    z-index: 2;
}

.score-table th:first-child,
.score-table td:first-child {
    position: sticky;
    left: 0;
    z-index: 1;
    background: var(--card-bg);
    font-weight: 600;
    color: #888;
    font-size: 0.85rem;
}

.score-table th:first-child {
    z-index: 3;
    background: var(--green-mid);
    color: white;
}

.score-table td.clickable {
    cursor: pointer;
    transition: background 0.15s;
}
.score-table td.clickable:active {
    background: #e8e0d0;
}

.score-table .totals-row td {
    font-weight: 800;
    font-size: 1.2rem;
    background: #ede7d9;
    border-top: 3px solid var(--green-mid);
}

.score-table .totals-row td:first-child {
    background: #ede7d9;
}

.leader-cell {
    color: var(--green-mid);
    position: relative;
}
.leader-cell::before {
    content: '&#128081;';
    font-size: 0.7rem;
    position: absolute;
    top: 2px;
    right: 4px;
}

.danger-cell { color: var(--red); }

.fab {
    position: fixed;
    bottom: 24px;
    right: 24px;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    background: var(--orange);
    color: white;
    font-size: 2rem;
    font-weight: 700;
    border: none;
    box-shadow: 0 6px 16px rgba(0,0,0,0.3);
    cursor: pointer;
    z-index: 10;
    transition: transform 0.1s;
}
.fab:active { transform: scale(0.9); }

.game-actions {
    display: flex;
    flex-direction: column;
    gap: 8px;
    margin-bottom: 80px;
}
```

- [ ] **Step 3: Implement renderGameScreen() in JS**

Replace the `function renderGameScreen() {}` placeholder with:

```javascript
function getTotals() {
    const totals = new Array(state.players.length).fill(0);
    state.rounds.forEach(round => {
        round.forEach((score, i) => { totals[i] += score; });
    });
    return totals;
}

function getLeaderIndex(totals) {
    let minVal = Infinity, minIdx = 0;
    totals.forEach((t, i) => {
        if (t < minVal) { minVal = t; minIdx = i; }
    });
    return minIdx;
}

function renderGameScreen() {
    const table = document.getElementById('score-table');
    const totals = getTotals();
    const leaderIdx = state.rounds.length > 0 ? getLeaderIndex(totals) : -1;

    let html = '<thead><tr><th>#</th>';
    state.players.forEach(name => {
        html += '<th>' + escapeHtml(name) + '</th>';
    });
    html += '</tr></thead><tbody>';

    state.rounds.forEach((round, ri) => {
        html += '<tr><td>M' + (ri + 1) + '</td>';
        round.forEach((score, pi) => {
            html += '<td class="clickable" data-round="' + ri + '" data-player="' + pi + '">' + score + '</td>';
        });
        html += '</tr>';
    });

    // Totals row
    html += '<tr class="totals-row"><td>Total</td>';
    totals.forEach((total, i) => {
        let cls = '';
        if (i === leaderIdx && state.rounds.length > 0) cls += ' leader-cell';
        if (total >= 80) cls += ' danger-cell';
        html += '<td class="' + cls.trim() + '">' + total + '</td>';
    });
    html += '</tr></tbody>';

    table.innerHTML = html;

    // Click handler for editing cells
    table.querySelectorAll('td.clickable').forEach(td => {
        td.addEventListener('click', () => {
            const ri = parseInt(td.dataset.round);
            const pi = parseInt(td.dataset.player);
            openEditModal(ri, pi);
        });
    });
}

function escapeHtml(str) {
    const div = document.createElement('div');
    div.textContent = str;
    return div.innerHTML;
}

// Placeholder for Task 4
function openEditModal(roundIndex, playerIndex) {}
```

Also add the event listeners in `init()`:

```javascript
document.getElementById('btn-add-round').addEventListener('click', openAddRoundModal);
document.getElementById('btn-history-game').addEventListener('click', () => {
    showScreen('history');
    renderHistoryScreen();
});
document.getElementById('btn-menu').addEventListener('click', toggleGameMenu);
```

Add placeholder functions:

```javascript
function openAddRoundModal() {}
function toggleGameMenu() {
    const actions = document.getElementById('game-actions');
    if (actions.innerHTML) {
        actions.innerHTML = '';
    } else {
        actions.innerHTML = `
            <button class="btn btn-secondary btn-block" onclick="undoLastRound()">&#8617; Supprimer la dernière manche</button>
            <button class="btn btn-danger btn-block" onclick="newGame(true)">&#128260; Nouvelle partie (mêmes joueurs)</button>
            <button class="btn btn-danger btn-block" onclick="newGame(false)">&#128260; Nouvelle partie</button>
        `;
    }
}

function undoLastRound() {
    if (state.rounds.length === 0) return;
    if (!confirm('Supprimer la dernière manche ?')) return;
    state.rounds.pop();
    saveGame();
    renderGameScreen();
    document.getElementById('game-actions').innerHTML = '';
}

function newGame(keepPlayers) {
    if (!confirm('Commencer une nouvelle partie ?')) return;
    if (keepPlayers) {
        state.rounds = [];
        saveGame();
        showScreen('game');
        renderGameScreen();
    } else {
        clearGame();
        state.rounds = [];
        showScreen('config');
        renderConfigScreen();
    }
    document.getElementById('game-actions').innerHTML = '';
}
```

- [ ] **Step 4: Verify in browser**

Start a game with 3 players. Expected:
- Empty score table with player names in header
- Total row shows 0 for all players
- "+" FAB button visible bottom-right
- Menu button (&#9776;) toggles action buttons
- History button (&#128203;) present in header

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: game screen with score table, totals, leader and danger indicators"
```

---

## Task 4: Round Input Modal (Both Modes)

**Files:**
- Modify: `index.html`

Implement the modal for adding a round with toggle between "Score direct" and "Detailed" modes.

- [ ] **Step 1: Add modal CSS**

```css
.modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.modal-title {
    font-size: 1.3rem;
    font-weight: 700;
}

.modal-close {
    background: none;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
    padding: 4px 8px;
    color: #999;
}

.mode-toggle {
    display: flex;
    background: #e0d8cc;
    border-radius: 8px;
    margin-bottom: 20px;
    overflow: hidden;
}

.mode-toggle-btn {
    flex: 1;
    padding: 10px;
    border: none;
    background: transparent;
    font-weight: 600;
    cursor: pointer;
    font-size: 0.9rem;
    transition: background 0.15s;
}
.mode-toggle-btn.active {
    background: var(--green-mid);
    color: white;
}

.round-input-group {
    margin-bottom: 16px;
    padding: 12px;
    background: #f9f5ed;
    border-radius: 8px;
}

.round-input-label {
    font-weight: 700;
    margin-bottom: 8px;
    display: block;
}

.round-input-row {
    display: flex;
    gap: 8px;
    align-items: center;
}

.round-input-row input {
    width: 80px;
    padding: 10px;
    border: 2px solid #ddd;
    border-radius: 8px;
    font-size: 1rem;
    text-align: center;
    outline: none;
}
.round-input-row input:focus { border-color: var(--green-mid); }

.round-input-row .input-label {
    font-size: 0.85rem;
    color: #666;
    min-width: 60px;
}

.round-calc {
    font-weight: 600;
    color: var(--green-mid);
    margin-top: 6px;
    font-size: 0.9rem;
}

.modal-actions {
    display: flex;
    gap: 10px;
    margin-top: 20px;
}
.modal-actions .btn { flex: 1; }
```

- [ ] **Step 2: Implement openAddRoundModal()**

Replace the `openAddRoundModal` placeholder:

```javascript
function openAddRoundModal() {
    const modal = document.getElementById('modal-overlay');
    const content = document.getElementById('modal-content');

    let html = `
        <div class="modal-header">
            <span class="modal-title">Nouvelle manche</span>
            <button class="modal-close" id="modal-close-btn">&times;</button>
        </div>
        <div class="mode-toggle">
            <button class="mode-toggle-btn ${state.inputMode === 'direct' ? 'active' : ''}" data-mode="direct">Score direct</button>
            <button class="mode-toggle-btn ${state.inputMode === 'detailed' ? 'active' : ''}" data-mode="detailed">Détaillé</button>
        </div>
        <div id="round-inputs"></div>
        <div class="modal-actions">
            <button class="btn btn-secondary" id="modal-cancel-btn">Annuler</button>
            <button class="btn btn-primary" id="modal-validate-btn">Valider</button>
        </div>
    `;
    content.innerHTML = html;
    modal.classList.remove('hidden');

    renderRoundInputs();

    // Event listeners
    content.querySelectorAll('.mode-toggle-btn').forEach(btn => {
        btn.addEventListener('click', () => {
            state.inputMode = btn.dataset.mode;
            content.querySelectorAll('.mode-toggle-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            renderRoundInputs();
        });
    });

    document.getElementById('modal-close-btn').addEventListener('click', closeModal);
    document.getElementById('modal-cancel-btn').addEventListener('click', closeModal);
    document.getElementById('modal-validate-btn').addEventListener('click', validateRound);
}

function renderRoundInputs() {
    const container = document.getElementById('round-inputs');
    let html = '';

    state.players.forEach((name, i) => {
        html += '<div class="round-input-group">';
        html += '<label class="round-input-label">' + escapeHtml(name) + '</label>';

        if (state.inputMode === 'direct') {
            html += `<div class="round-input-row">
                <input type="number" class="score-input" data-player="${i}" placeholder="0">
                <span class="input-label">points</span>
            </div>`;
        } else {
            html += `<div class="round-input-row">
                <input type="number" class="center-input" data-player="${i}" placeholder="0" min="0">
                <span class="input-label">centre</span>
                <input type="number" class="remain-input" data-player="${i}" placeholder="0" min="0">
                <span class="input-label">restantes</span>
            </div>
            <div class="round-calc" id="calc-${i}">= 0 pts</div>`;
        }

        html += '</div>';
    });

    container.innerHTML = html;

    // Live calculation for detailed mode
    if (state.inputMode === 'detailed') {
        container.querySelectorAll('.center-input, .remain-input').forEach(input => {
            input.addEventListener('input', () => {
                const pi = parseInt(input.dataset.player);
                const center = parseInt(container.querySelector(`.center-input[data-player="${pi}"]`).value) || 0;
                const remain = parseInt(container.querySelector(`.remain-input[data-player="${pi}"]`).value) || 0;
                const score = center - (remain * 2);
                document.getElementById('calc-' + pi).textContent = '= ' + score + ' pts';
            });
        });
    }
}

function validateRound() {
    const container = document.getElementById('round-inputs');
    const scores = [];

    state.players.forEach((_, i) => {
        if (state.inputMode === 'direct') {
            const val = parseInt(container.querySelector(`.score-input[data-player="${i}"]`).value) || 0;
            scores.push(val);
        } else {
            const center = parseInt(container.querySelector(`.center-input[data-player="${i}"]`).value) || 0;
            const remain = parseInt(container.querySelector(`.remain-input[data-player="${i}"]`).value) || 0;
            scores.push(center - (remain * 2));
        }
    });

    state.rounds.push(scores);
    saveGame();
    closeModal();
    renderGameScreen();

    // Check for game end
    const totals = getTotals();
    const loserIdx = totals.findIndex(t => t >= 100);
    if (loserIdx !== -1) {
        endGame(loserIdx);
    }
}

function closeModal() {
    document.getElementById('modal-overlay').classList.add('hidden');
}
```

- [ ] **Step 3: Implement openEditModal()**

Replace the `openEditModal` placeholder:

```javascript
function openEditModal(roundIndex, playerIndex) {
    const modal = document.getElementById('modal-overlay');
    const content = document.getElementById('modal-content');
    const currentScore = state.rounds[roundIndex][playerIndex];
    const playerName = state.players[playerIndex];

    content.innerHTML = `
        <div class="modal-header">
            <span class="modal-title">Modifier - ${escapeHtml(playerName)}</span>
            <button class="modal-close" id="modal-close-btn">&times;</button>
        </div>
        <p style="margin-bottom:12px; color:#666;">Manche ${roundIndex + 1}</p>
        <div class="round-input-group">
            <div class="round-input-row">
                <input type="number" id="edit-score-input" value="${currentScore}">
                <span class="input-label">points</span>
            </div>
        </div>
        <div class="modal-actions">
            <button class="btn btn-secondary" id="modal-cancel-btn">Annuler</button>
            <button class="btn btn-primary" id="modal-save-btn">Enregistrer</button>
        </div>
    `;
    modal.classList.remove('hidden');

    document.getElementById('modal-close-btn').addEventListener('click', closeModal);
    document.getElementById('modal-cancel-btn').addEventListener('click', closeModal);
    document.getElementById('modal-save-btn').addEventListener('click', () => {
        const newScore = parseInt(document.getElementById('edit-score-input').value) || 0;
        state.rounds[roundIndex][playerIndex] = newScore;
        saveGame();
        closeModal();
        renderGameScreen();

        // Recheck game end after edit
        const totals = getTotals();
        const loserIdx = totals.findIndex(t => t >= 100);
        if (loserIdx !== -1) {
            endGame(loserIdx);
        }
    });
}
```

Add placeholder:

```javascript
function endGame(loserIndex) {}
```

- [ ] **Step 4: Verify in browser**

1. Start a game, click "+" -> modal opens with "Score direct" mode
2. Toggle to "Détaillé" -> shows centre/restantes inputs with live calculation
3. Enter scores, validate -> scores appear in table, totals update
4. Click a cell -> edit modal opens with current value
5. Modify and save -> table updates

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: round input modal with direct/detailed modes and score editing"
```

---

## Task 5: Game End - Results Screen with Podium

**Files:**
- Modify: `index.html`

When a player reaches 100+ points, show results with podium and rankings.

- [ ] **Step 1: Add results screen HTML**

Replace `#screen-results` content:

```html
<div id="screen-results" class="screen">
    <h1 class="app-title">&#127942; Fin de partie &#127942;</h1>
    <div id="results-content"></div>
</div>
```

- [ ] **Step 2: Add results CSS**

```css
.loser-banner {
    text-align: center;
    background: var(--red);
    color: white;
    padding: 16px;
    border-radius: var(--radius);
    margin-bottom: 20px;
    font-size: 1.1rem;
}

.podium {
    display: flex;
    align-items: flex-end;
    justify-content: center;
    gap: 8px;
    margin-bottom: 24px;
    padding: 20px 0;
}

.podium-place {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
}

.podium-bar {
    width: 80px;
    border-radius: 8px 8px 0 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: 12px 4px;
    color: white;
    font-weight: 800;
}

.podium-1 .podium-bar { background: var(--gold); height: 120px; color: #333; }
.podium-2 .podium-bar { background: var(--silver); height: 90px; color: #333; }
.podium-3 .podium-bar { background: var(--bronze); height: 70px; color: white; }

.podium-name {
    font-weight: 700;
    margin-top: 8px;
    font-size: 0.9rem;
    max-width: 80px;
    overflow: hidden;
    text-overflow: ellipsis;
}

.podium-score { font-size: 0.8rem; opacity: 0.8; }

.podium-medal { font-size: 1.5rem; }

.ranking-list {
    list-style: none;
    padding: 0;
}

.ranking-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 12px 16px;
    border-bottom: 1px solid #e0d8cc;
}

.ranking-position {
    font-weight: 800;
    width: 30px;
    color: #888;
}

.ranking-name { flex: 1; font-weight: 600; }

.ranking-score { font-weight: 700; font-size: 1.1rem; }

.results-actions {
    display: flex;
    flex-direction: column;
    gap: 10px;
    margin-top: 20px;
}
```

- [ ] **Step 3: Implement endGame()**

Replace the `endGame` placeholder:

```javascript
function endGame(loserIndex) {
    const totals = getTotals();

    // Build ranking: sort by score ascending (lowest = best)
    const ranking = state.players.map((name, i) => ({
        name: name,
        score: totals[i]
    }));
    ranking.sort((a, b) => a.score - b.score);

    // Save to history
    history.push({
        date: new Date().toISOString(),
        players: [...state.players],
        rounds: state.rounds.map(r => [...r]),
        totals: [...totals],
        loser: state.players[loserIndex],
        ranking: ranking.map(r => r.name)
    });
    saveHistory();
    clearGame();

    // Render results
    showScreen('results');

    const container = document.getElementById('results-content');
    let html = '';

    // Loser banner
    html += '<div class="loser-banner">&#128683; ' + escapeHtml(state.players[loserIndex]) + ' a atteint ' + totals[loserIndex] + ' points !</div>';

    // Podium (top 3)
    const medals = ['&#129351;', '&#129352;', '&#129353;'];
    const podiumOrder = [1, 0, 2]; // display order: 2nd, 1st, 3rd
    html += '<div class="podium">';
    podiumOrder.forEach(pos => {
        if (pos < ranking.length) {
            const r = ranking[pos];
            html += `<div class="podium-place podium-${pos + 1}">
                <div class="podium-bar">
                    <span class="podium-medal">${medals[pos]}</span>
                    <span>${r.score} pts</span>
                </div>
                <span class="podium-name">${escapeHtml(r.name)}</span>
            </div>`;
        }
    });
    html += '</div>';

    // Full ranking
    html += '<div class="card"><h3 style="margin-bottom:12px;">Classement</h3><ul class="ranking-list">';
    ranking.forEach((r, i) => {
        html += `<li class="ranking-item">
            <span class="ranking-position">${i + 1}.</span>
            <span class="ranking-name">${escapeHtml(r.name)}</span>
            <span class="ranking-score">${r.score} pts</span>
        </li>`;
    });
    html += '</ul></div>';

    // Actions
    html += `<div class="results-actions">
        <button class="btn btn-primary btn-block" onclick="restartSamePlayers()">&#128260; Rejouer (mêmes joueurs)</button>
        <button class="btn btn-secondary btn-block" onclick="restartNewPlayers()">&#128101; Nouvelle partie</button>
        <button class="btn btn-secondary btn-block" onclick="showScreen('history'); renderHistoryScreen();">&#128203; Voir l'historique</button>
    </div>`;

    container.innerHTML = html;
}

function restartSamePlayers() {
    state.rounds = [];
    saveGame();
    showScreen('game');
    renderGameScreen();
}

function restartNewPlayers() {
    clearGame();
    state.rounds = [];
    state.players = [];
    showScreen('config');
    renderConfigScreen();
}
```

- [ ] **Step 4: Verify in browser**

1. Start a game with 3 players
2. Enter rounds to push one player above 100 pts (e.g., enter 50 twice)
3. Expected: results screen shows with loser banner, podium (3 places), full ranking
4. "Rejouer" should start fresh game with same names
5. "Nouvelle partie" should go to config

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: results screen with loser banner, podium, and ranking"
```

---

## Task 6: History Screen + Global Rankings

**Files:**
- Modify: `index.html`

Build the history screen showing global player stats and past games list.

- [ ] **Step 1: Add history screen HTML**

Replace `#screen-history` content:

```html
<div id="screen-history" class="screen">
    <div class="game-header">
        <button class="btn btn-secondary btn-sm" id="btn-history-back">&#8592;</button>
        <h1 class="app-title" style="margin-bottom:0; font-size:1.5rem;">Historique</h1>
        <div style="width:40px;"></div>
    </div>
    <div id="history-content"></div>
</div>
```

- [ ] **Step 2: Add history CSS**

```css
.stats-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.85rem;
}

.stats-table th,
.stats-table td {
    padding: 8px 6px;
    text-align: center;
    border-bottom: 1px solid #e0d8cc;
}

.stats-table th {
    background: var(--green-mid);
    color: white;
    font-weight: 600;
    font-size: 0.8rem;
}

.stats-table td:first-child {
    text-align: left;
    font-weight: 600;
}

.history-game {
    padding: 14px;
    margin-bottom: 8px;
    cursor: pointer;
    border-radius: 8px;
    transition: background 0.15s;
}
.history-game:active { background: #ede7d9; }

.history-game-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.history-game-date {
    font-size: 0.85rem;
    color: #888;
}

.history-game-winner {
    font-weight: 700;
    color: var(--green-mid);
}

.history-game-players {
    font-size: 0.85rem;
    color: #666;
    margin-top: 4px;
}

.history-game-details {
    margin-top: 10px;
    font-size: 0.85rem;
    display: none;
}
.history-game.expanded .history-game-details {
    display: block;
}

.no-history {
    text-align: center;
    color: rgba(255,255,255,0.6);
    padding: 40px 20px;
    font-size: 1.1rem;
}
```

- [ ] **Step 3: Implement renderHistoryScreen()**

Replace the `renderHistoryScreen` placeholder:

```javascript
function renderHistoryScreen() {
    const container = document.getElementById('history-content');

    if (history.length === 0) {
        container.innerHTML = '<div class="no-history">Aucune partie terminée pour le moment.</div>';
        setupHistoryBack();
        return;
    }

    let html = '';

    // Global rankings
    const playerStats = {};
    history.forEach(game => {
        game.players.forEach((name, i) => {
            if (!playerStats[name]) {
                playerStats[name] = { games: 0, wins: 0, podiums: 0, totalScore: 0 };
            }
            playerStats[name].games++;
            playerStats[name].totalScore += game.totals[i];
            const rank = game.ranking.indexOf(name);
            if (rank === 0) playerStats[name].wins++;
            if (rank < 3) playerStats[name].podiums++;
        });
    });

    const statsArray = Object.entries(playerStats).map(([name, s]) => ({
        name,
        games: s.games,
        wins: s.wins,
        podiums: s.podiums,
        avgScore: Math.round(s.totalScore / s.games)
    }));
    statsArray.sort((a, b) => b.wins - a.wins || a.avgScore - b.avgScore);

    html += '<div class="card"><h3 style="margin-bottom:12px;">&#127942; Classement global</h3>';
    html += '<div class="table-scroll"><table class="stats-table"><thead><tr>';
    html += '<th>Joueur</th><th>Parties</th><th>&#129351;</th><th>Podiums</th><th>Moy.</th>';
    html += '</tr></thead><tbody>';
    statsArray.forEach(s => {
        html += `<tr>
            <td>${escapeHtml(s.name)}</td>
            <td>${s.games}</td>
            <td>${s.wins}</td>
            <td>${s.podiums}</td>
            <td>${s.avgScore}</td>
        </tr>`;
    });
    html += '</tbody></table></div></div>';

    // Past games
    html += '<div class="card"><h3 style="margin-bottom:12px;">&#128196; Parties passées</h3>';
    const sortedHistory = [...history].reverse();
    sortedHistory.forEach((game, idx) => {
        const date = new Date(game.date);
        const dateStr = date.toLocaleDateString('fr-FR', { day: 'numeric', month: 'short', year: 'numeric', hour: '2-digit', minute: '2-digit' });
        const winner = game.ranking[0];

        html += `<div class="history-game" data-idx="${idx}">
            <div class="history-game-header">
                <span class="history-game-winner">&#129351; ${escapeHtml(winner)}</span>
                <span class="history-game-date">${dateStr}</span>
            </div>
            <div class="history-game-players">${game.players.map(escapeHtml).join(', ')} - ${game.rounds.length} manches</div>
            <div class="history-game-details">
                <table class="stats-table"><thead><tr><th>#</th>`;

        game.players.forEach(p => { html += '<th>' + escapeHtml(p) + '</th>'; });
        html += '</tr></thead><tbody>';
        game.rounds.forEach((round, ri) => {
            html += '<tr><td>M' + (ri + 1) + '</td>';
            round.forEach(s => { html += '<td>' + s + '</td>'; });
            html += '</tr>';
        });
        html += '<tr style="font-weight:700;"><td>Total</td>';
        game.totals.forEach(t => { html += '<td>' + t + '</td>'; });
        html += '</tr></tbody></table></div></div>';
    });
    html += '</div>';

    // Delete history button
    html += '<button class="btn btn-danger btn-block" style="margin-top:12px;" id="btn-delete-history">Supprimer l\'historique</button>';

    container.innerHTML = html;

    // Expand/collapse games
    container.querySelectorAll('.history-game').forEach(el => {
        el.addEventListener('click', () => el.classList.toggle('expanded'));
    });

    // Delete history
    const deleteBtn = document.getElementById('btn-delete-history');
    if (deleteBtn) {
        deleteBtn.addEventListener('click', () => {
            if (confirm('Supprimer tout l\'historique ?') && confirm('Êtes-vous sûr ? Cette action est irréversible.')) {
                history = [];
                saveHistory();
                renderHistoryScreen();
            }
        });
    }

    setupHistoryBack();
}

function setupHistoryBack() {
    document.getElementById('btn-history-back').addEventListener('click', () => {
        if (state.players.length > 0 && state.rounds.length >= 0) {
            const saved = localStorage.getItem('blitzGame');
            if (saved) {
                showScreen('game');
                renderGameScreen();
            } else {
                showScreen('config');
                renderConfigScreen();
            }
        } else {
            showScreen('config');
            renderConfigScreen();
        }
    });
}
```

- [ ] **Step 4: Verify in browser**

1. Access history from config screen -> shows "Aucune partie terminée"
2. Play a full game to completion (one player reaches 100)
3. Go to history -> global ranking shows, past game appears
4. Click past game -> expands to show round details
5. Play another game -> ranking updates with cumulative stats
6. Back button returns to correct screen

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: history screen with global rankings, past games, and delete option"
```

---

## Task 7: Polish and Final Verification

**Files:**
- Modify: `index.html`

Add animations, fix edge cases, and do final responsive testing.

- [ ] **Step 1: Add CSS animations**

```css
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
}

@keyframes slideUp {
    from { opacity: 0; transform: translateY(100%); }
    to { opacity: 1; transform: translateY(0); }
}

.screen { animation: fadeIn 0.3s ease; }

.modal-overlay:not(.hidden) .modal-content {
    animation: slideUp 0.25s ease;
}

.score-table tbody tr:last-child {
    animation: fadeIn 0.3s ease;
}

.podium-place {
    animation: fadeIn 0.5s ease backwards;
}
.podium-place:nth-child(1) { animation-delay: 0.2s; }
.podium-place:nth-child(2) { animation-delay: 0s; }
.podium-place:nth-child(3) { animation-delay: 0.4s; }
```

- [ ] **Step 2: Fix edge case - close modal on overlay click**

Add inside the `init()` function:

```javascript
document.getElementById('modal-overlay').addEventListener('click', (e) => {
    if (e.target === document.getElementById('modal-overlay')) {
        closeModal();
    }
});
```

- [ ] **Step 3: Fix edge case - handle 2-player games on podium**

In `endGame()`, update the podium rendering to handle fewer than 3 players:

```javascript
// Replace the podiumOrder loop with:
podiumOrder.forEach(pos => {
    if (pos < ranking.length) {
        const r = ranking[pos];
        html += `<div class="podium-place podium-${pos + 1}">
            <div class="podium-bar">
                <span class="podium-medal">${medals[pos]}</span>
                <span>${r.score} pts</span>
            </div>
            <span class="podium-name">${escapeHtml(r.name)}</span>
        </div>`;
    }
});
```

(This is already handled with the `if (pos < ranking.length)` check, but verify it works visually for 2-player games.)

- [ ] **Step 4: Full end-to-end verification**

Run through the complete verification checklist from the spec:

1. Open `index.html` -> config screen
2. Set 4 players with custom names -> start game
3. Add round in direct mode -> scores in table
4. Add round in detailed mode (e.g., 14 centre, 6 restantes = 2 pts) -> verify calculation
5. Edit a past score by tapping a cell -> verify total recalculates
6. Close browser, reopen -> game state preserved
7. Delete last round -> verify
8. Push one player above 100 -> results screen with podium
9. Check "Rejouer" -> same players, empty scores
10. Play another game to completion -> check history shows both games
11. Check global rankings -> correct wins, average scores
12. Resize browser to 375px -> table scrolls horizontally, modal full screen
13. Test with 2 players -> podium shows only 2 places
14. Test "Nouvelle partie" from menu -> goes to config
15. Delete history -> double confirm, history clears

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: animations, edge case fixes, and responsive polish"
```

---

## Self-Review Checklist

- [x] **Spec coverage:** Config screen, game table, round input (both modes), score editing, results/podium, history/global rankings, persistence, responsive, animations — all covered in Tasks 1-7.
- [x] **Placeholder scan:** No TBD/TODO. All code blocks contain complete implementations.
- [x] **Type consistency:** `state.players`, `state.rounds`, `state.inputMode`, `history` used consistently. Functions `getTotals()`, `getLeaderIndex()`, `escapeHtml()` defined once and referenced correctly. `saveGame`/`loadGame`/`clearGame`/`saveHistory`/`loadHistory` match localStorage keys `blitzGame` and `blitzHistory`.

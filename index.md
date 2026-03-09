<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GOGO: The Game of Go</title>
    <style>
        :root {
            --color-bg-primary: #fcfcf9;
            --color-bg-secondary: #fffffd;
            --color-text-primary: #13343b;
            --color-text-secondary: #626c71;
            --color-accent: #21808d;
            --color-accent-hover: #1d7480;
            --color-border: rgba(94, 82, 64, 0.2);
            --color-board-light: #dcb35c;
            --color-board-dark: #8b6914;
            --color-line: rgba(0, 0, 0, 0.4);
            --color-stone-black: #1a1a1a;
            --color-stone-white: #f5f5f5;
            --color-star-point: rgba(0, 0, 0, 0.5);
        }

        body.theme-dark {
            --color-bg-primary: #1f2121;
            --color-bg-secondary: #262828;
            --color-text-primary: #f5f5f5;
            --color-text-secondary: #a7a9a9;
            --color-accent: #32b8c6;
            --color-accent-hover: #2da6b2;
            --color-border: rgba(119, 124, 124, 0.3);
            --color-board-light: #dcb35c;
            --color-board-dark: #8b6914;
        }

        body.theme-contrast {
            --color-bg-primary: #000000;
            --color-bg-secondary: #1a1a1a;
            --color-text-primary: #ffffff;
            --color-text-secondary: #cccccc;
            --color-accent: #00ff00;
            --color-accent-hover: #00cc00;
            --color-border: #ffffff;
            --color-board-light: #ffffff;
            --color-board-dark: #000000;
            --color-line: #00ff00;
            --color-stone-black: #000000;
            --color-stone-white: #ffffff;
            --color-star-point: #00ff00;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            background: var(--color-bg-primary);
            color: var(--color-text-primary);
            padding: 10px;
            transition: background 0.3s, color 0.3s;
            max-width: 100vw;
            overflow-x: hidden;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
        }

        header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            padding-bottom: 20px;
            border-bottom: 2px solid var(--color-border);
        }

        h1 {
            font-size: 2rem;
            color: var(--color-accent);
        }

        .controls {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        button {
            padding: 10px 20px;
            background: var(--color-accent);
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
            transition: background 0.2s;
        }

        button:hover {
            background: var(--color-accent-hover);
        }

        button.secondary {
            background: var(--color-bg-secondary);
            color: var(--color-text-primary);
            border: 1px solid var(--color-border);
        }

        button.secondary:hover {
            background: var(--color-border);
        }

        select {
            padding: 10px;
            background: var(--color-bg-secondary);
            color: var(--color-text-primary);
            border: 1px solid var(--color-border);
            border-radius: 6px;
            cursor: pointer;
            font-size: 14px;
        }

        .main-content {
            display: grid;
            grid-template-columns: 1fr 350px;
            gap: 30px;
        }

        .board-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .board-wrapper {
            background: var(--color-board-light);
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.15);
        }

        .board {
            display: grid;
            gap: 0;
            position: relative;
            background: var(--color-board-light);
        }

        .intersection {
            width: 40px;
            height: 40px;
            position: relative;
            cursor: pointer;
        }

        .intersection::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 2px;
            height: 100%;
            background: var(--color-line);
        }

        .intersection::after {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 100%;
            height: 2px;
            background: var(--color-line);
        }

        .intersection.edge-top::before {
            height: 50%;
            top: auto;
            bottom: 0;
            transform: translate(-50%, 0);
        }

        .intersection.edge-bottom::before {
            height: 50%;
            top: 0;
            transform: translate(-50%, 0);
        }

        .intersection.edge-left::after {
            width: 50%;
            left: auto;
            right: 0;
            transform: translate(0, -50%);
        }

        .intersection.edge-right::after {
            width: 50%;
            left: 0;
            transform: translate(0, -50%);
        }

        .intersection.star-point .star {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 8px;
            height: 8px;
            background: var(--color-star-point);
            border-radius: 50%;
            z-index: 1;
        }

        .stone {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 36px;
            height: 36px;
            border-radius: 50%;
            z-index: 2;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        .stone.black {
            background: radial-gradient(circle at 30% 30%, #404040, var(--color-stone-black));
        }

        .stone.white {
            background: radial-gradient(circle at 30% 30%, #ffffff, var(--color-stone-white));
        }

        .stone.last-move {
            box-shadow: 0 0 0 3px var(--color-accent);
        }

        .intersection:hover::after {
            background: var(--color-accent);
        }

        .intersection:hover::before {
            background: var(--color-accent);
        }

        .info-panel {
            background: var(--color-bg-secondary);
            padding: 25px;
            border-radius: 8px;
            border: 1px solid var(--color-border);
        }

        .mode-selector {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .mode-btn {
            flex: 1;
            min-width: 100px;
            padding: 12px;
            font-size: 13px;
        }

        .mode-btn.active {
            background: var(--color-accent);
            color: white;
        }

        .game-info {
            margin-top: 20px;
        }

        .info-section {
            margin-bottom: 20px;
        }

        .info-section h3 {
            font-size: 1rem;
            margin-bottom: 10px;
            color: var(--color-accent);
        }

        .info-section p {
            color: var(--color-text-secondary);
            line-height: 1.6;
            font-size: 14px;
        }

        .score-display {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 15px;
        }

        .score-box {
            padding: 15px;
            background: var(--color-bg-primary);
            border-radius: 6px;
            text-align: center;
        }

        .score-label {
            font-size: 12px;
            color: var(--color-text-secondary);
            margin-bottom: 5px;
        }

        .score-value {
            font-size: 24px;
            font-weight: bold;
        }

        .tutorial-content {
            display: none;
        }

        .tutorial-content.active {
            display: block;
        }

        .tutorial-step {
            margin-bottom: 20px;
            padding: 15px;
            background: var(--color-bg-primary);
            border-radius: 6px;
            border-left: 4px solid var(--color-accent);
        }

        .tutorial-step h4 {
            margin-bottom: 10px;
            color: var(--color-accent);
        }

        .tutorial-navigation {
            display: flex;
            gap: 10px;
            margin-top: 20px;
        }

        .training-term {
            margin-bottom: 15px;
            padding: 12px;
            background: var(--color-bg-primary);
            border-radius: 6px;
            cursor: pointer;
            transition: transform 0.2s;
        }

        .training-term:hover {
            transform: translateX(5px);
        }

        .training-term strong {
            color: var(--color-accent);
            display: block;
            margin-bottom: 5px;
        }

        .puzzle-info {
            text-align: center;
            margin-bottom: 20px;
        }

        .streak-display {
            font-size: 28px;
            font-weight: bold;
            color: var(--color-accent);
            margin: 10px 0;
        }

        .puzzle-objective {
            padding: 15px;
            background: var(--color-bg-primary);
            border-radius: 6px;
            margin-bottom: 15px;
            border: 2px solid var(--color-accent);
        }

        .board-actions {
            display: flex;
            gap: 10px;
            justify-content: center;
            flex-wrap: wrap;
        }

        .captured-stones {
            display: flex;
            justify-content: space-between;
            margin-top: 15px;
            padding: 10px;
            background: var(--color-bg-primary);
            border-radius: 6px;
        }

        .captured-group {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .captured-icon {
            width: 20px;
            height: 20px;
            border-radius: 50%;
        }

        .captured-icon.black {
            background: var(--color-stone-black);
        }

        .captured-icon.white {
            background: var(--color-stone-white);
        }

        @media (max-width: 1024px) {
            .main-content {
                grid-template-columns: 1fr;
            }

            .intersection {
                width: 30px;
                height: 30px;
            }

            .stone {
                width: 26px;
                height: 26px;
            }

            .board-wrapper {
                padding: 15px;
            }

            h1 {
                font-size: 1.5rem;
            }

            .controls {
                flex-wrap: wrap;
            }
        }

        @media (max-width: 480px) {
            body {
                padding: 5px;
            }

            .intersection {
                width: 24px;
                height: 24px;
            }

            .stone {
                width: 20px;
                height: 20px;
            }

            .board-wrapper {
                padding: 10px;
            }

            h1 {
                font-size: 1.2rem;
            }

            button {
                padding: 8px 12px;
                font-size: 12px;
            }

            select {
                padding: 8px;
                font-size: 12px;
            }

            .mode-btn {
                font-size: 11px;
                padding: 10px 8px;
            }
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: var(--color-bg-secondary);
            padding: 30px;
            border-radius: 12px;
            max-width: 500px;
            width: 90%;
            border: 2px solid var(--color-accent);
        }

        .modal-content h2 {
            color: var(--color-accent);
            margin-bottom: 15px;
        }

        .modal-actions {
            display: flex;
            gap: 10px;
            margin-top: 20px;
            justify-content: flex-end;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🎮 GOGO</h1>
            <div class="controls">
                <select id="themeSelector">
                    <option value="light">Light Theme</option>
                    <option value="dark">Dark Theme</option>
                    <option value="contrast">High Contrast</option>
                </select>
                <select id="boardSize">
                    <option value="9">9x9 Board</option>
                    <option value="13">13x13 Board</option>
                    <option value="19">19x19 Board</option>
                </select>
            </div>
        </header>

        <div class="main-content">
            <div class="board-container">
                <div class="board-wrapper">
                    <div id="board" class="board"></div>
                </div>
                <div class="board-actions">
                    <button id="passBtn" class="secondary">Pass</button>
                    <button id="undoBtn" class="secondary">Undo</button>
                    <button id="newGameBtn">New Game</button>
                </div>
            </div>

            <div class="info-panel">
                <div class="mode-selector">
                    <button class="mode-btn active" data-mode="play">Play</button>
                    <button class="mode-btn" data-mode="ai">AI Mode</button>
                    <button class="mode-btn" data-mode="tutorial">Tutorial</button>
                    <button class="mode-btn" data-mode="training">Training</button>
                    <button class="mode-btn" data-mode="puzzle">Daily Puzzle</button>
                </div>

                <div id="playMode" class="game-info">
                    <div class="info-section">
                        <h3>Current Turn</h3>
                        <p id="currentPlayer">Black to move</p>
                    </div>
                    <div class="score-display">
                        <div class="score-box">
                            <div class="score-label">Black Territory</div>
                            <div class="score-value" id="blackScore">0</div>
                        </div>
                        <div class="score-box">
                            <div class="score-label">White Territory</div>
                            <div class="score-value" id="whiteScore">0</div>
                        </div>
                    </div>
                    <div class="captured-stones">
                        <div class="captured-group">
                            <div class="captured-icon black"></div>
                            <span id="blackCaptured">0 captured</span>
                        </div>
                        <div class="captured-group">
                            <div class="captured-icon white"></div>
                            <span id="whiteCaptured">0 captured</span>
                        </div>
                    </div>
                    <div class="info-section">
                        <h3>Game Status</h3>
                        <p id="gameStatus">Game in progress</p>
                    </div>
                </div>

                <div id="tutorialMode" class="tutorial-content">
                    <div class="info-section">
                        <h3>Learn Go</h3>
                        <div id="tutorialSteps"></div>
                        <div class="tutorial-navigation">
                            <button id="prevStep" class="secondary">Previous</button>
                            <button id="nextStep">Next</button>
                        </div>
                    </div>
                </div>

                <div id="trainingMode" class="tutorial-content">
                    <div class="info-section">
                        <h3>Go Terminology</h3>
                        <div id="termsList"></div>
                    </div>
                </div>

                <div id="aiMode" class="tutorial-content">
                    <div class="info-section">
                        <h3>Play Against AI</h3>
                        <p style="color: var(--color-text-secondary); margin-bottom: 15px;">Practice your skills against an AI opponent</p>
                        <div class="score-display" style="margin-bottom: 15px;">
                            <div class="score-box">
                                <div class="score-label">AI Difficulty</div>
                                <select id="aiDifficulty" style="width: 100%; padding: 8px; background: var(--color-bg-primary); color: var(--color-text-primary); border: 1px solid var(--color-border); border-radius: 6px; margin-top: 8px;">
                                    <option value="easy">Easy</option>
                                    <option value="medium" selected>Medium</option>
                                    <option value="hard">Hard</option>
                                </select>
                            </div>
                            <div class="score-box">
                                <div class="score-label">You Play As</div>
                                <select id="playerColor" style="width: 100%; padding: 8px; background: var(--color-bg-primary); color: var(--color-text-primary); border: 1px solid var(--color-border); border-radius: 6px; margin-top: 8px;">
                                    <option value="black" selected>Black</option>
                                    <option value="white">White</option>
                                </select>
                            </div>
                        </div>
                        <button id="startAIGame" style="width: 100%; margin-bottom: 10px;">Start AI Game</button>
                        <div class="info-section" style="margin-top: 20px;">
                            <h3>AI Stats</h3>
                            <div class="captured-stones">
                                <div class="captured-group">
                                    <span>Games Won:</span>
                                    <strong id="aiWins" style="color: var(--color-accent);">0</strong>
                                </div>
                                <div class="captured-group">
                                    <span>Games Lost:</span>
                                    <strong id="aiLosses" style="color: var(--color-text-secondary);">0</strong>
                                </div>
                            </div>
                        </div>
                        <div class="info-section" style="margin-top: 15px; padding: 15px; background: var(--color-bg-primary); border-radius: 6px;">
                            <p style="color: var(--color-text-secondary); font-size: 13px; line-height: 1.6;">
                                <strong style="color: var(--color-accent);">Easy:</strong> AI makes random valid moves<br>
                                <strong style="color: var(--color-accent);">Medium:</strong> AI prefers territorial moves<br>
                                <strong style="color: var(--color-accent);">Hard:</strong> AI plays strategically with capture priority
                            </p>
                        </div>
                    </div>
                </div>

                <div id="puzzleMode" class="tutorial-content">
                    <div class="puzzle-info">
                        <h3>Daily Puzzle</h3>
                        <div class="streak-display" id="streakCount">🔥 0 Day Streak</div>
                        <p style="color: var(--color-text-secondary); font-size: 14px;" id="puzzleDate"></p>
                    </div>
                    <div class="puzzle-objective" id="puzzleObjective">
                        <strong>Objective:</strong>
                        <p>Black to play and capture white stones</p>
                    </div>
                    <button id="checkSolution" style="width: 100%; margin-bottom: 10px;">Check Solution</button>
                    <button id="nextPuzzle" class="secondary" style="width: 100%;">Next Puzzle</button>
                </div>
            </div>
        </div>
    </div>

    <div id="gameOverModal" class="modal">
        <div class="modal-content">
            <h2>Game Over</h2>
            <p id="winnerText"></p>
            <div class="score-display" style="margin: 20px 0;">
                <div class="score-box">
                    <div class="score-label">Black Final Score</div>
                    <div class="score-value" id="finalBlackScore">0</div>
                </div>
                <div class="score-box">
                    <div class="score-label">White Final Score</div>
                    <div class="score-value" id="finalWhiteScore">0</div>
                </div>
            </div>
            <div class="modal-actions">
                <button id="closeModal" class="secondary">Close</button>
                <button id="newGameFromModal">New Game</button>
            </div>
        </div>
    </div>

    <script>
        const state = {
            boardSize: 9,
            board: [],
            currentPlayer: 'black',
            moveHistory: [],
            capturedBlack: 0,
            capturedWhite: 0,
            passCount: 0,
            koPosition: null,
            mode: 'play',
            tutorialStep: 0,
            puzzleStreak: parseInt(localStorage.getItem('goStreak') || '0'),
            lastPuzzleDate: localStorage.getItem('lastPuzzleDate') || '',
            currentPuzzle: 0,
            aiMode: false,
            aiDifficulty: 'medium',
            playerColor: 'black',
            aiThinking: false,
            aiWins: parseInt(localStorage.getItem('aiWins') || '0'),
            aiLosses: parseInt(localStorage.getItem('aiLosses') || '0')
        };

        const tutorialSteps = [
            {
                title: "The Go Board",
                content: "Go is played on a board with intersecting lines. Stones are placed on intersections, not in squares. Standard sizes are 9x9, 13x13, and 19x19 lines."
            },
            {
                title: "Placing Stones",
                content: "Black moves first. Players alternate placing one stone per turn on any empty intersection. Once placed, stones don't move (unless captured)."
            },
            {
                title: "Capturing Stones",
                content: "Stones are captured by surrounding them completely - filling all adjacent empty points (called liberties). Captured stones are removed from the board."
            },
            {
                title: "Liberties",
                content: "A liberty is an empty point directly adjacent to a stone (horizontal or vertical, not diagonal). A stone or group needs at least one liberty to survive."
            },
            {
                title: "The Goal",
                content: "The goal is to control more territory than your opponent. Territory is empty space surrounded by your stones, plus the stones you've placed on the board."
            },
            {
                title: "Komi",
                content: "Since Black moves first, White receives compensation points (komi) - typically 5.5 or 6.5 points. This ensures fairness and prevents draws."
            },
            {
                title: "Ko Rule",
                content: "You cannot immediately recapture a stone if it would recreate the previous board position. This prevents infinite loops in the game."
            },
            {
                title: "Passing and Ending",
                content: "Players may pass instead of placing a stone. When both players pass consecutively, the game ends and territory is counted."
            }
        ];

        const goTerms = [
            { term: "Atari", definition: "When a stone or group has only one liberty remaining - under immediate threat of capture." },
            { term: "Joseki", definition: "Established sequences of moves in the corners that are considered optimal for both players." },
            { term: "Sente", definition: "A move that demands a response, maintaining the initiative in the game." },
            { term: "Gote", definition: "A move that doesn't require an immediate response, allowing the opponent to take initiative." },
            { term: "Tenuki", definition: "Ignoring your opponent's last move to play elsewhere on the board." },
            { term: "Life and Death", definition: "Whether a group of stones can survive (life) or will be captured (death)." },
            { term: "Eyes", definition: "Enclosed empty points within a group. A group with two separate eyes cannot be captured." },
            { term: "Territory", definition: "Empty intersections surrounded by one player's stones that count as points." },
            { term: "Influence", definition: "Control over a large area of the board, even if not fully enclosed." },
            { term: "Sacrifice", definition: "Deliberately allowing stones to be captured to gain strategic advantage elsewhere." }
        ];

        const puzzles = [
            {
                size: 9,
                setup: [[3,3,'white'],[3,4,'white'],[4,3,'black'],[4,4,'black'],[4,5,'white'],[5,4,'black']],
                solution: {x:3,y:5},
                objective: "Black to play and capture white stones",
                difficulty: "Easy"
            },
            {
                size: 9,
                setup: [[2,2,'black'],[2,3,'white'],[3,2,'white'],[3,3,'black'],[3,4,'white'],[4,3,'white']],
                solution: {x:2,y:4},
                objective: "Black to play and save the group",
                difficulty: "Medium"
            },
            {
                size: 9,
                setup: [[4,4,'white'],[3,4,'black'],[4,3,'black'],[4,5,'black'],[5,4,'black'],[5,5,'white'],[3,5,'white']],
                solution: {x:5,y:3},
                objective: "Black to play and capture white",
                difficulty: "Hard"
            }
        ];

        function initBoard(size) {
            state.boardSize = size;
            state.board = Array(size).fill(null).map(() => Array(size).fill(null));
            state.moveHistory = [];
            state.currentPlayer = 'black';
            state.capturedBlack = 0;
            state.capturedWhite = 0;
            state.passCount = 0;
            state.koPosition = null;
            renderBoard();
            updateGameInfo();
        }

        function renderBoard() {
            const boardEl = document.getElementById('board');
            boardEl.innerHTML = '';
            boardEl.style.gridTemplateColumns = `repeat(${state.boardSize}, 40px)`;
            boardEl.style.gridTemplateRows = `repeat(${state.boardSize}, 40px)`;

            const starPoints = getStarPoints(state.boardSize);

            for (let row = 0; row < state.boardSize; row++) {
                for (let col = 0; col < state.boardSize; col++) {
                    const intersection = document.createElement('div');
                    intersection.className = 'intersection';
                    intersection.dataset.row = row;
                    intersection.dataset.col = col;

                    if (row === 0) intersection.classList.add('edge-top');
                    if (row === state.boardSize - 1) intersection.classList.add('edge-bottom');
                    if (col === 0) intersection.classList.add('edge-left');
                    if (col === state.boardSize - 1) intersection.classList.add('edge-right');

                    if (starPoints.some(p => p.row === row && p.col === col)) {
                        intersection.classList.add('star-point');
                        const star = document.createElement('div');
                        star.className = 'star';
                        intersection.appendChild(star);
                    }

                    if (state.board[row][col]) {
                        const stone = document.createElement('div');
                        stone.className = `stone ${state.board[row][col]}`;
                        if (state.moveHistory.length > 0) {
                            const lastMove = state.moveHistory[state.moveHistory.length - 1];
                            if (lastMove.row === row && lastMove.col === col) {
                                stone.classList.add('last-move');
                            }
                        }
                        intersection.appendChild(stone);
                    }

                    intersection.addEventListener('click', () => makeMove(row, col));
                    boardEl.appendChild(intersection);
                }
            }
        }

        function getStarPoints(size) {
            if (size === 9) {
                return [
                    {row: 2, col: 2}, {row: 2, col: 6},
                    {row: 4, col: 4},
                    {row: 6, col: 2}, {row: 6, col: 6}
                ];
            } else if (size === 13) {
                return [
                    {row: 3, col: 3}, {row: 3, col: 9},
                    {row: 6, col: 6},
                    {row: 9, col: 3}, {row: 9, col: 9}
                ];
            } else if (size === 19) {
                return [
                    {row: 3, col: 3}, {row: 3, col: 9}, {row: 3, col: 15},
                    {row: 9, col: 3}, {row: 9, col: 9}, {row: 9, col: 15},
                    {row: 15, col: 3}, {row: 15, col: 9}, {row: 15, col: 15}
                ];
            }
            return [];
        }

        function makeMove(row, col) {
            if (state.mode !== 'play' && state.mode !== 'puzzle' && state.mode !== 'ai') return;
            if (state.board[row][col]) return;
            if (state.aiThinking) return;
            if (state.aiMode && state.currentPlayer !== state.playerColor) return;

            const testBoard = state.board.map(r => [...r]);
            testBoard[row][col] = state.currentPlayer;

            const captured = captureStones(testBoard, row, col);
            
            if (!hasLiberties(testBoard, row, col)) {
                if (captured.length === 0) return;
            }

            if (state.koPosition && state.koPosition.row === row && state.koPosition.col === col) {
                return;
            }

            state.board[row][col] = state.currentPlayer;
            state.moveHistory.push({row, col, player: state.currentPlayer});

            const actualCaptured = captureStones(state.board, row, col);
            
            if (actualCaptured.length === 1 && captured.length === 1) {
                state.koPosition = {row: actualCaptured[0].row, col: actualCaptured[0].col};
            } else {
                state.koPosition = null;
            }

            if (state.currentPlayer === 'black') {
                state.capturedWhite += actualCaptured.length;
            } else {
                state.capturedBlack += actualCaptured.length;
            }

            state.passCount = 0;

            if (state.mode === 'puzzle') {
                checkPuzzleSolution();
            } else {
                state.currentPlayer = state.currentPlayer === 'black' ? 'white' : 'black';
            }

            renderBoard();
            updateGameInfo();

            if (state.aiMode && state.currentPlayer !== state.playerColor && state.mode === 'ai') {
                setTimeout(() => makeAIMove(), 800);
            }
        }

        function captureStones(board, row, col) {
            const captured = [];
            const opponent = board[row][col] === 'black' ? 'white' : 'black';
            const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];

            directions.forEach(dir => {
                const newRow = row + dir.row;
                const newCol = col + dir.col;
                
                if (newRow >= 0 && newRow < board.length && newCol >= 0 && newCol < board.length) {
                    if (board[newRow][newCol] === opponent) {
                        if (!hasLiberties(board, newRow, newCol)) {
                            const group = getGroup(board, newRow, newCol);
                            group.forEach(pos => {
                                board[pos.row][pos.col] = null;
                                captured.push(pos);
                            });
                        }
                    }
                }
            });

            return captured;
        }

        function hasLiberties(board, row, col) {
            const color = board[row][col];
            if (!color) return false;

            const visited = new Set();
            const toCheck = [{row, col}];

            while (toCheck.length > 0) {
                const pos = toCheck.pop();
                const key = `${pos.row},${pos.col}`;
                
                if (visited.has(key)) continue;
                visited.add(key);

                const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];
                
                for (const dir of directions) {
                    const newRow = pos.row + dir.row;
                    const newCol = pos.col + dir.col;
                    
                    if (newRow < 0 || newRow >= board.length || newCol < 0 || newCol >= board.length) continue;
                    
                    if (board[newRow][newCol] === null) return true;
                    if (board[newRow][newCol] === color && !visited.has(`${newRow},${newCol}`)) {
                        toCheck.push({row: newRow, col: newCol});
                    }
                }
            }

            return false;
        }

        function getGroup(board, row, col) {
            const color = board[row][col];
            const group = [];
            const visited = new Set();
            const toCheck = [{row, col}];

            while (toCheck.length > 0) {
                const pos = toCheck.pop();
                const key = `${pos.row},${pos.col}`;
                
                if (visited.has(key)) continue;
                visited.add(key);
                group.push(pos);

                const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];
                
                directions.forEach(dir => {
                    const newRow = pos.row + dir.row;
                    const newCol = pos.col + dir.col;
                    
                    if (newRow >= 0 && newRow < board.length && newCol >= 0 && newCol < board.length) {
                        if (board[newRow][newCol] === color && !visited.has(`${newRow},${newCol}`)) {
                            toCheck.push({row: newRow, col: newCol});
                        }
                    }
                });
            }

            return group;
        }

        function calculateScore() {
            const territory = {black: 0, white: 0};
            const visited = Array(state.boardSize).fill(null).map(() => Array(state.boardSize).fill(false));

            for (let row = 0; row < state.boardSize; row++) {
                for (let col = 0; col < state.boardSize; col++) {
                    if (state.board[row][col]) {
                        if (state.board[row][col] === 'black') territory.black++;
                        else territory.white++;
                    } else if (!visited[row][col]) {
                        const region = findRegion(row, col, visited);
                        const owner = determineOwner(region);
                        if (owner === 'black') territory.black += region.length;
                        else if (owner === 'white') territory.white += region.length;
                    }
                }
            }

            territory.black += state.capturedWhite;
            territory.white += state.capturedBlack + 6.5;

            return territory;
        }

        function findRegion(startRow, startCol, visited) {
            const region = [];
            const toCheck = [{row: startRow, col: startCol}];

            while (toCheck.length > 0) {
                const pos = toCheck.pop();
                const {row, col} = pos;

                if (row < 0 || row >= state.boardSize || col < 0 || col >= state.boardSize) continue;
                if (visited[row][col] || state.board[row][col]) continue;

                visited[row][col] = true;
                region.push(pos);

                toCheck.push({row: row - 1, col});
                toCheck.push({row: row + 1, col});
                toCheck.push({row, col: col - 1});
                toCheck.push({row, col: col + 1});
            }

            return region;
        }

        function determineOwner(region) {
            const borders = new Set();

            region.forEach(pos => {
                const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];
                directions.forEach(dir => {
                    const newRow = pos.row + dir.row;
                    const newCol = pos.col + dir.col;
                    if (newRow >= 0 && newRow < state.boardSize && newCol >= 0 && newCol < state.boardSize) {
                        if (state.board[newRow][newCol]) {
                            borders.add(state.board[newRow][newCol]);
                        }
                    }
                });
            });

            if (borders.size === 1) {
                return borders.has('black') ? 'black' : 'white';
            }
            return null;
        }

        function pass() {
            state.passCount++;
            state.currentPlayer = state.currentPlayer === 'black' ? 'white' : 'black';
            
            if (state.passCount >= 2) {
                endGame();
            }
            
            updateGameInfo();
        }

        function endGame() {
            const scores = calculateScore();
            document.getElementById('finalBlackScore').textContent = scores.black.toFixed(1);
            document.getElementById('finalWhiteScore').textContent = scores.white.toFixed(1);
            
            const winner = scores.black > scores.white ? 'Black' : 'White';
            const margin = Math.abs(scores.black - scores.white).toFixed(1);
            document.getElementById('winnerText').textContent = `${winner} wins by ${margin} points!`;
            
            if (state.aiMode) {
                const playerWon = (state.playerColor === 'black' && winner === 'Black') || 
                                  (state.playerColor === 'white' && winner === 'White');
                if (playerWon) {
                    state.aiLosses++;
                    localStorage.setItem('aiLosses', state.aiLosses);
                } else {
                    state.aiWins++;
                    localStorage.setItem('aiWins', state.aiWins);
                }
                updateAIStats();
            }
            
            document.getElementById('gameOverModal').classList.add('active');
        }

        function undo() {
            if (state.moveHistory.length === 0) return;
            
            const lastMove = state.moveHistory.pop();
            state.board[lastMove.row][lastMove.col] = null;
            state.currentPlayer = lastMove.player;
            state.passCount = 0;
            
            renderBoard();
            updateGameInfo();
        }

        function updateGameInfo() {
            document.getElementById('currentPlayer').textContent = 
                `${state.currentPlayer.charAt(0).toUpperCase() + state.currentPlayer.slice(1)} to move`;
            
            const scores = calculateScore();
            document.getElementById('blackScore').textContent = Math.floor(scores.black);
            document.getElementById('whiteScore').textContent = Math.floor(scores.white);
            
            document.getElementById('blackCaptured').textContent = `${state.capturedBlack} captured`;
            document.getElementById('whiteCaptured').textContent = `${state.capturedWhite} captured`;
            
            document.getElementById('gameStatus').textContent = 
                state.passCount === 1 ? 'One player passed' : 'Game in progress';
        }

        function switchMode(mode) {
            state.mode = mode;
            state.aiMode = false;
            document.querySelectorAll('.mode-btn').forEach(btn => {
                btn.classList.toggle('active', btn.dataset.mode === mode);
            });

            document.getElementById('playMode').style.display = mode === 'play' ? 'block' : 'none';
            document.getElementById('aiMode').classList.toggle('active', mode === 'ai');
            document.getElementById('tutorialMode').classList.toggle('active', mode === 'tutorial');
            document.getElementById('trainingMode').classList.toggle('active', mode === 'training');
            document.getElementById('puzzleMode').classList.toggle('active', mode === 'puzzle');

            if (mode === 'tutorial') {
                renderTutorial();
            } else if (mode === 'training') {
                renderTraining();
            } else if (mode === 'puzzle') {
                loadPuzzle();
            } else if (mode === 'play') {
                initBoard(state.boardSize);
            } else if (mode === 'ai') {
                updateAIStats();
            }
        }

        function renderTutorial() {
            const container = document.getElementById('tutorialSteps');
            const step = tutorialSteps[state.tutorialStep];
            container.innerHTML = `
                <div class="tutorial-step">
                    <h4>${step.title}</h4>
                    <p>${step.content}</p>
                </div>
                <p style="text-align: center; color: var(--color-text-secondary);">
                    Step ${state.tutorialStep + 1} of ${tutorialSteps.length}
                </p>
            `;
        }

        function renderTraining() {
            const container = document.getElementById('termsList');
            container.innerHTML = goTerms.map(term => `
                <div class="training-term">
                    <strong>${term.term}</strong>
                    <span style="color: var(--color-text-secondary);">${term.definition}</span>
                </div>
            `).join('');
        }

        function loadPuzzle() {
            const puzzle = puzzles[state.currentPuzzle];
            initBoard(puzzle.size);
            
            puzzle.setup.forEach(([row, col, color]) => {
                state.board[row][col] = color;
            });
            
            renderBoard();
            document.getElementById('puzzleObjective').innerHTML = `
                <strong>Objective:</strong>
                <p>${puzzle.objective}</p>
                <p style="margin-top: 8px;"><em>Difficulty: ${puzzle.difficulty}</em></p>
            `;
            
            updateStreakDisplay();
        }

        function checkPuzzleSolution() {
            const puzzle = puzzles[state.currentPuzzle];
            const lastMove = state.moveHistory[state.moveHistory.length - 1];
            
            if (lastMove.row === puzzle.solution.x && lastMove.col === puzzle.solution.y) {
                const today = new Date().toDateString();
                if (state.lastPuzzleDate !== today) {
                    state.puzzleStreak++;
                    state.lastPuzzleDate = today;
                    localStorage.setItem('goStreak', state.puzzleStreak);
                    localStorage.setItem('lastPuzzleDate', today);
                }
                alert('Correct! Well done! 🎉');
                updateStreakDisplay();
            }
        }

        function updateStreakDisplay() {
            document.getElementById('streakCount').textContent = `🔥 ${state.puzzleStreak} Day Streak`;
            document.getElementById('puzzleDate').textContent = new Date().toLocaleDateString();
        }

        function makeAIMove() {
            state.aiThinking = true;
            const aiColor = state.playerColor === 'black' ? 'white' : 'black';
            
            let move = null;
            if (state.aiDifficulty === 'easy') {
                move = getRandomMove();
            } else if (state.aiDifficulty === 'medium') {
                move = getMediumAIMove(aiColor);
            } else {
                move = getHardAIMove(aiColor);
            }

            state.aiThinking = false;

            if (move) {
                makeMove(move.row, move.col);
            } else {
                pass();
            }
        }

        function getRandomMove() {
            const validMoves = [];
            for (let row = 0; row < state.boardSize; row++) {
                for (let col = 0; col < state.boardSize; col++) {
                    if (!state.board[row][col]) {
                        const testBoard = state.board.map(r => [...r]);
                        testBoard[row][col] = state.currentPlayer;
                        if (hasLiberties(testBoard, row, col) || captureStones(testBoard, row, col).length > 0) {
                            validMoves.push({row, col});
                        }
                    }
                }
            }
            return validMoves.length > 0 ? validMoves[Math.floor(Math.random() * validMoves.length)] : null;
        }

        function getMediumAIMove(aiColor) {
            const validMoves = [];
            for (let row = 0; row < state.boardSize; row++) {
                for (let col = 0; col < state.boardSize; col++) {
                    if (!state.board[row][col]) {
                        const testBoard = state.board.map(r => [...r]);
                        testBoard[row][col] = aiColor;
                        if (hasLiberties(testBoard, row, col) || captureStones(testBoard, row, col).length > 0) {
                            let score = 0;
                            const emptyNeighbors = countEmptyNeighbors(row, col);
                            score += emptyNeighbors * 2;
                            const centerDistance = Math.abs(row - state.boardSize / 2) + Math.abs(col - state.boardSize / 2);
                            score -= centerDistance;
                            validMoves.push({row, col, score});
                        }
                    }
                }
            }
            if (validMoves.length === 0) return null;
            validMoves.sort((a, b) => b.score - a.score);
            return validMoves[0];
        }

        function getHardAIMove(aiColor) {
            const opponent = aiColor === 'black' ? 'white' : 'black';
            let bestMove = null;
            let bestScore = -Infinity;

            for (let row = 0; row < state.boardSize; row++) {
                for (let col = 0; col < state.boardSize; col++) {
                    if (!state.board[row][col]) {
                        const testBoard = state.board.map(r => [...r]);
                        testBoard[row][col] = aiColor;
                        
                        if (!hasLiberties(testBoard, row, col) && captureStones(testBoard, row, col).length === 0) {
                            continue;
                        }

                        let score = 0;
                        const captured = captureStones(testBoard, row, col);
                        score += captured.length * 10;

                        const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];
                        directions.forEach(dir => {
                            const newRow = row + dir.row;
                            const newCol = col + dir.col;
                            if (newRow >= 0 && newRow < state.boardSize && newCol >= 0 && newCol < state.boardSize) {
                                if (state.board[newRow][newCol] === opponent) {
                                    const liberties = countLiberties(state.board, newRow, newCol);
                                    if (liberties === 1) score += 8;
                                    else if (liberties === 2) score += 4;
                                }
                            }
                        });

                        const emptyNeighbors = countEmptyNeighbors(row, col);
                        score += emptyNeighbors;

                        const centerDistance = Math.abs(row - state.boardSize / 2) + Math.abs(col - state.boardSize / 2);
                        score -= centerDistance * 0.5;

                        if (score > bestScore) {
                            bestScore = score;
                            bestMove = {row, col};
                        }
                    }
                }
            }

            return bestMove;
        }

        function countEmptyNeighbors(row, col) {
            let count = 0;
            const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];
            directions.forEach(dir => {
                const newRow = row + dir.row;
                const newCol = col + dir.col;
                if (newRow >= 0 && newRow < state.boardSize && newCol >= 0 && newCol < state.boardSize) {
                    if (!state.board[newRow][newCol]) count++;
                }
            });
            return count;
        }

        function countLiberties(board, row, col) {
            const color = board[row][col];
            if (!color) return 0;

            const group = getGroup(board, row, col);
            const liberties = new Set();

            group.forEach(pos => {
                const directions = [{row: -1, col: 0}, {row: 1, col: 0}, {row: 0, col: -1}, {row: 0, col: 1}];
                directions.forEach(dir => {
                    const newRow = pos.row + dir.row;
                    const newCol = pos.col + dir.col;
                    if (newRow >= 0 && newRow < board.length && newCol >= 0 && newCol < board.length) {
                        if (!board[newRow][newCol]) {
                            liberties.add(`${newRow},${newCol}`);
                        }
                    }
                });
            });

            return liberties.size;
        }

        function updateAIStats() {
            document.getElementById('aiWins').textContent = state.aiWins;
            document.getElementById('aiLosses').textContent = state.aiLosses;
        }

        function startAIGame() {
            state.aiMode = true;
            state.aiDifficulty = document.getElementById('aiDifficulty').value;
            state.playerColor = document.getElementById('playerColor').value;
            initBoard(state.boardSize);
            
            if (state.playerColor === 'white') {
                setTimeout(() => makeAIMove(), 500);
            }
        }

        document.getElementById('themeSelector').addEventListener('change', (e) => {
            document.body.className = e.target.value === 'light' ? '' : `theme-${e.target.value}`;
        });

        document.getElementById('boardSize').addEventListener('change', (e) => {
            initBoard(parseInt(e.target.value));
        });

        document.getElementById('passBtn').addEventListener('click', pass);
        document.getElementById('undoBtn').addEventListener('click', undo);
        document.getElementById('newGameBtn').addEventListener('click', () => initBoard(state.boardSize));

        document.querySelectorAll('.mode-btn').forEach(btn => {
            btn.addEventListener('click', () => switchMode(btn.dataset.mode));
        });

        document.getElementById('prevStep').addEventListener('click', () => {
            if (state.tutorialStep > 0) {
                state.tutorialStep--;
                renderTutorial();
            }
        });

        document.getElementById('nextStep').addEventListener('click', () => {
            if (state.tutorialStep < tutorialSteps.length - 1) {
                state.tutorialStep++;
                renderTutorial();
            }
        });

        document.getElementById('checkSolution').addEventListener('click', () => {
            alert('Make your move on the board to check if it solves the puzzle!');
        });

        document.getElementById('nextPuzzle').addEventListener('click', () => {
            state.currentPuzzle = (state.currentPuzzle + 1) % puzzles.length;
            loadPuzzle();
        });

        document.getElementById('startAIGame').addEventListener('click', startAIGame);

        document.getElementById('closeModal').addEventListener('click', () => {
            document.getElementById('gameOverModal').classList.remove('active');
        });

        document.getElementById('newGameFromModal').addEventListener('click', () => {
            document.getElementById('gameOverModal').classList.remove('active');
            initBoard(state.boardSize);
        });

        initBoard(9);
    </script>
</body>
</html>


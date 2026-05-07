<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plant Life: The Survival Simulator</title>
    <style>
        :root {
            --primary-green: #2e7d32;
            --accent-yellow: #fdd835;
            --danger-red: #e53935;
            --sky-blue: #e3f2fd;
            --text-color: #263238;
        }

        body {
            font-family: 'Segoe UI', Arial, sans-serif;
            background-color: var(--sky-blue);
            color: var(--text-color);
            display: flex; justify-content: center; align-items: center;
            min-height: 100vh; margin: 0;
        }

        #game-window {
            background: white;
            width: 90%; max-width: 650px;
            padding: 35px;
            border-radius: 25px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.1);
            text-align: center;
            border: 5px solid var(--primary-green);
        }

        .screen { display: none; }
        .active { display: block; animation: fadeIn 0.5s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        .btn {
            background: var(--primary-green);
            color: white; border: none;
            padding: 15px 30px; margin: 10px;
            border-radius: 50px; cursor: pointer;
            font-weight: bold; font-size: 1.1rem;
            transition: 0.3s;
        }
        .btn:hover { background: #1b5e20; transform: translateY(-3px); }

        .concept-card {
            background: #f1f8e9;
            padding: 15px; border-radius: 15px;
            margin: 20px 0; text-align: left;
            border-left: 8px solid var(--primary-green);
        }

        /* --- Minigame: Clone Defense --- */
        #clone-arena {
            height: 200px; background: #fafafa;
            border: 2px dashed #ccc; position: relative;
            margin-bottom: 15px; overflow: hidden;
            border-radius: 15px;
        }
        .bug {
            position: absolute; width: 30px; height: 30px;
            background: var(--danger-red); border-radius: 50%;
            cursor: pointer; display: flex; align-items: center;
            justify-content: center; color: white; font-weight: bold;
        }

        /* --- Minigame: Pollen Progress --- */
        .tube-container {
            width: 100%; height: 30px; background: #eee;
            border-radius: 15px; margin: 20px 0; overflow: hidden;
        }
        #tube-fill {
            height: 100%; width: 0%; background: var(--accent-yellow);
            transition: width 0.2s;
        }
    </style>
</head>
<body>

<div id="game-window">
    
    <!-- LEVEL 1: CHOICE -->
    <div id="screen-start" class="screen active">
        <h1>🌱 Plant Survival Simulator</h1>
        <p>How will you ensure your species survives for another generation?</p>
        <button class="btn" onclick="showScreen('screen-asexual')">Asexual (Fast & Identical)</button>
        <button class="btn" onclick="showScreen('screen-sexual')">Sexual (Diverse & Adaptive)</button>
    </div>

    <!-- PATH A: ASEXUAL -->
    <div id="screen-asexual" class="screen">
        <h1>The Clone Path</h1>
        <p>You use <strong>mitotic cell division</strong> to grow parts of yourself into new plants.</p>
        <div class="concept-card">
            <strong>Concept Check:</strong> No fusion of gametes means your offspring are <strong>clones</strong> (genetically identical). This is great for taking over an area quickly!
        </div>
        <button class="btn" onclick="startCloneGame()">Minigame: Disease Defense!</button>
    </div>

    <div id="screen-clone-game" class="screen">
        <h2>Protect the Clones!</h2>
        <p>Because you are all identical, a single disease can kill everyone. Click the <strong>red viruses</strong> before they reach your plants!</p>
        <div id="clone-arena"></div>
        <p>Viruses caught: <span id="clone-score">0</span>/8</p>
    </div>

    <!-- PATH B: SEXUAL -->
    <div id="screen-sexual" class="screen">
        <h1>The Genetic Mixer</h1>
        <p>You produce male and female gametes through <strong>meiosis</strong>. How will you move your pollen?</p>
        <button class="btn" onclick="pollinationInfo('Self')">Self-Pollination</button>
        <button class="btn" onclick="pollinationInfo('Cross')">Cross-Pollination</button>
    </div>

    <div id="screen-pollination-result" class="screen">
        <h2 id="pol-title"></h2>
        <div id="pol-text" class="concept-card"></div>
        <button class="btn" onclick="showScreen('screen-pollen-game')">Start Fertilization</button>
    </div>

    <!-- POLLEN TUBE GAME -->
    <div id="screen-pollen-game" class="screen">
        <h1>Pollen Tube Sprint! ⚡</h1>
        <p>A pollen grain landed on your stigma. Grow the tube down the style to reach the ovule!</p>
        <div class="tube-container"><div id="tube-fill"></div></div>
        <button class="btn" id="grow-btn">TAP TO GROW!</button>
    </div>

    <!-- FERTILIZATION -->
    <div id="screen-transformation" class="screen">
        <h1>Transformation!</h1>
        <p>The male gamete has fused with the ovum. Now, the flower changes:</p>
        <div class="concept-card">
            📍 <strong>Ovule</strong> &rarr; Seed<br>
            📍 <strong>Ovary</strong> &rarr; Fruit
        </div>
        <button class="btn" onclick="showScreen('screen-dispersal')">Disperse the Seeds</button>
    </div>

    <!-- DISPERSAL -->
    <div id="screen-dispersal" class="screen">
        <h1>Final Step: Dispersal! 🚀</h1>
        <p>Choose a method to send your seeds away from the parent plant:</p>
        <button class="btn" onclick="finish('Wind')">Wind 🌬️</button>
        <button class="btn" onclick="finish('Water')">Water 🌊</button>
        <button class="btn" onclick="finish('Animal')">Animal 🐾</button>
    </div>

    <!-- ENDING -->
    <div id="screen-end" class="screen">
        <h1>Mission Accomplished! 🏆</h1>
        <p>Your species survives! Why was that last step so important?</p>
        <div class="concept-card">
            <strong>Summary:</strong> Seed dispersal prevents <strong>overcrowding</strong> and <strong>competition</strong> for light and nutrients. It also lets your "children" colonize new territory!
        </div>
        <button class="btn" onclick="location.reload()">Start New Life</button>
    </div>

</div>

<script>
    function showScreen(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    /* --- Asexual Game Logic --- */
    let cScore = 0;
    function startCloneGame() {
        showScreen('screen-clone-game');
        cScore = 0;
        document.getElementById('clone-score').innerText = cScore;
        spawnBug();
    }

    function spawnBug() {
        if (cScore >= 8) {
            showScreen('screen-start');
            alert("Survival Success! But remember: without variation, clones are at risk if the environment changes.");
            return;
        }
        const arena = document.getElementById('clone-arena');
        const bug = document.createElement('div');
        bug.className = 'bug';
        bug.innerText = '👾';
        bug.style.left = Math.random() * 80 + 10 + '%';
        bug.style.top = Math.random() * 70 + 10 + '%';
        
        bug.onclick = () => {
            bug.remove();
            cScore++;
            document.getElementById('clone-score').innerText = cScore;
            spawnBug();
        };
        arena.appendChild(bug);
        setTimeout(() => { if(bug.parentNode) bug.remove(); }, 1500);
    }

    /* --- Pollination Logic --- */
    function pollinationInfo(type) {
        const title = document.getElementById('pol-title');
        const text = document.getElementById('pol-text');
        title.innerText = type + " Pollination";
        
        if(type === 'Self') {
            text.innerHTML = "<strong>Result: Less Genetic Variation.</strong> Reliable because you don't need a partner, but your offspring will struggle to adapt to new environments.";
        } else {
            text.innerHTML = "<strong>Result: Greater Genetic Variation.</strong> Your offspring will have unique traits, helping them survive through <strong>natural selection</strong>.";
        }
        showScreen('screen-pollination-result');
    }

    /* --- Pollen Tube Logic --- */
    let tubeWidth = 0;
    document.getElementById('grow-btn').onclick = () => {
        tubeWidth += 10;
        document.getElementById('tube-fill').style.width = tubeWidth + '%';
        if(tubeWidth >= 100) {
            showScreen('screen-transformation');
        }
    };

    function finish(method) {
        showScreen('screen-end');
    }
</script>

</body>
</html>

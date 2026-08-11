
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Racing Game Score Calculator</title>

<style>
html, body {
    margin: 0;
    padding: 0;
    height: 100%;
    overflow: hidden;
    font-family: Arial, sans-serif;
}

/* ---------------- ROAD SYSTEM ---------------- */

.road-wrapper {
    position: fixed;
    top: 0;
    left: 50%;
    transform: translateX(-50%);
    width: 400px; /* matches TRACK_WIDTH */
    height: 200%;
    z-index: 0;
    animation: scrollRoad 2.5s linear infinite;
}

/* ASPHALT BASE */
.road {
    position: relative;
    width: 100%;
    height: 50%;
    background: rgb(50,50,50);
}

/* DASHED LANE LINES */
.road::before {
    content: "";
    position: absolute;
    inset: 0;

    background:
        repeating-linear-gradient(
            to bottom,
            transparent 0px,
            transparent 40px,
            white 40px,
            white 80px
        );

    /* create 4 lane dividers (5 lanes total) */
    background-size: 20% 100%;
    background-repeat: repeat-x;

    /* shift them into correct positions */
    mask-image: linear-gradient(
        to right,
        transparent 0%,
        transparent 19%,
        white 19%, white 21%,
        transparent 21%, transparent 39%,
        white 39%, white 41%,
        transparent 41%, transparent 59%,
        white 59%, white 61%,
        transparent 61%, transparent 79%,
        white 79%, white 81%,
        transparent 81%, transparent 100%
    );
}

/* SIDE WHITE LINES */
.road::after {
    content: "";
    position: absolute;
    inset: 0;

    background:
        linear-gradient(white, white) left 40px top / 4px 100% no-repeat,
        linear-gradient(white, white) right 40px top / 4px 100% no-repeat;
}

/* BUMPERS (RED/WHITE STRIPES) */
.bumpers {
    position: absolute;
    inset: 0;

    background:
        repeating-linear-gradient(
            to bottom,
            red 0px,
            red 20px,
            white 20px,
            white 40px
        );

    background-size: 20px 40px;

    /* left + right placement */
    mask-image:
        linear-gradient(to right,
            white 0px, white 20px,
            transparent 20px, transparent calc(100% - 20px),
            white calc(100% - 20px), white 100%
        );
}

/* SCROLLING */
@keyframes scrollRoad {
    from { transform: translate(-50%, 0); }
    to   { transform: translate(-50%, -50%); }
}

/* ---------------- UI ---------------- */

body {
    display: flex;
    justify-content: center;
    align-items: center;
    background: rgb(0,255,0);
}

.container {
    width: 450px;
    background: rgba(255,255,255,0.95);
    padding: 25px;
    border-radius: 12px;
    box-shadow: 0 10px 25px rgba(0,0,0,0.3);
    position: relative;
    z-index: 2;
}

h1 {
    text-align: center;
}

label {
    display: block;
    margin-top: 15px;
    font-weight: bold;
}

input, button {
    width: 100%;
    padding: 8px;
    margin-top: 5px;
    font-size: 16px;
}

button {
    margin-top: 20px;
    background: #3498db;
    color: white;
    border: none;
    font-weight: bold;
    cursor: pointer;
}

button:hover {
    background: #2980b9;
}

.error {
    color: red;
    margin-top: 10px;
}

.score {
    margin-top: 15px;
    font-size: 20px;
    font-weight: bold;
}
</style>
</head>

<body>

<!-- ROAD -->
<div class="road-wrapper">
    <div class="road">
        <div class="bumpers"></div>
    </div>
    <div class="road">
        <div class="bumpers"></div>
    </div>
</div>

<!-- UI -->
<div class="container">
    <h1>Driving Game Score Calculator</h1>

    <label>Number of Collisions</label>
    <input type="text" id="collisions">

    <label>Playback Speed (1 ➡️ easy, 1.25 ➡️ medium, 1.5 ➡️ hard)</label>
    <input type="text" id="speed">

    <button onclick="calculateScore()">Calculate Score</button>

    <div class="error" id="error"></div>
    <div class="score" id="scoreDisplay"></div>
</div>

<script>
function calculateScore() {

    const c = document.getElementById("collisions").value.trim();
    const s = document.getElementById("speed").value.trim();

    const error = document.getElementById("error");
    const scoreDiv = document.getElementById("scoreDisplay");

    error.textContent = "";
    scoreDiv.innerHTML = "";

    if (!/^[0-9]+$/.test(c)) {
        error.textContent = "Collisions must be a positive integer.";
        return;
    }

    if (!/^(1(\.25|\.5)?)$/.test(s)) {
        error.textContent = "Playback speed must be 1, 1.25, or 1.5.";
        return;
    }

    const collisions = parseInt(c);
    const speed = parseFloat(s);

    let baseScore;

    if (speed === 1.5) {
      baseScore = 1000;
    } else if (speed === 1.25) {
      baseScore = 950;
    } else if (speed === 1) {
      baseScore = 900;
    }
    
    let score = Math.max(0, baseScore - collisions * 100 * (2 - speed));

    scoreDiv.innerHTML = `
        <div style="font-size:22px;">
            Your Final Score: <strong>${score}</strong> 🏁
        </div>
        <div style="margin-top:10px;">
            Go back to the video and comment your score!
        </div>
    `;
}
</script>

</body>
</html>

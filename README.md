
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Multi Cursor Portfolio</title>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #080808;
  color: white;
  font-family: Arial, sans-serif;
  overflow-x: hidden;
  min-height: 100vh;
}

nav {
  display: flex;
  justify-content: space-between;
  padding: 28px 7%;
  border-bottom: 1px solid #252525;
}

.logo {
  color: #ff3030;
  font-size: 22px;
  font-weight: bold;
}

nav a {
  color: white;
  text-decoration: none;
  margin-left: 25px;
}

.hero {
  min-height: 75vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 8%;
}

.hero small {
  color: #ff3939;
  letter-spacing: 5px;
}

.hero h1 {
  font-size: clamp(65px, 12vw, 160px);
  line-height: .95;
  margin: 25px 0;
  font-weight: 900;
  letter-spacing: -7px;
}

.hero h1 span {
  color: #ff3030;
}

.hero p {
  color: #999;
  max-width: 500px;
  line-height: 1.8;
}

button {
  margin-top: 30px;
  padding: 16px 30px;
  background: #ff3030;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 15px;
  transition: .3s;
  width: fit-content;
}

button:hover {
  background: white;
  color: black;
  transform: scale(1.08);
}

.projects {
  padding: 70px 8%;
  background: #101010;
}

.projects h2 {
  font-size: 45px;
  margin-bottom: 35px;
}

.project-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 25px;
}

.project {
  padding: 45px 30px;
  min-height: 180px;
  background: #191919;
  border: 1px solid #333;
  transition: .3s;
}

.project:hover {
  border-color: #ff3030;
  transform: translateY(-8px);
}

.project h3 {
  margin-bottom: 15px;
}

.project p {
  color: #999;
  line-height: 1.7;
}

/* SIMULATED MULTIPLE CURSORS */

.cursor {
  position: fixed;
  top: 0;
  left: 0;
  z-index: 9999;
  pointer-events: none;
  will-change: transform;
  transition: opacity .3s;
}

.cursor svg {
  width: 25px;
  height: 25px;
  filter: drop-shadow(0 0 5px currentColor);
}

.cursor-label {
  position: absolute;
  top: 20px;
  left: 15px;
  padding: 5px 10px;
  font-size: 10px;
  color: white;
  white-space: nowrap;
  border-radius: 3px;
  font-weight: bold;
}

.click-ring {
  position: fixed;
  width: 35px;
  height: 35px;
  border: 2px solid currentColor;
  border-radius: 50%;
  pointer-events: none;
  z-index: 9998;
  transform: translate(-50%, -50%) scale(.3);
  opacity: 0;
}

.click-ring.active {
  animation: clickPulse .5s ease-out;
}

@keyframes clickPulse {
  0% {
    transform: translate(-50%, -50%) scale(.3);
    opacity: 1;
  }
  100% {
    transform: translate(-50%, -50%) scale(2.5);
    opacity: 0;
  }
}

@media(max-width: 650px) {
  .project-grid {
    grid-template-columns: 1fr;
  }

  .hero h1 {
    letter-spacing: -3px;
  }
}
</style>
</head>

<body>

<nav>
  <div class="logo">SANJAY.DEV</div>
  <div>
    <a href="#home">Home</a>
    <a href="#projects">Projects</a>
  </div>
</nav>

<section class="hero" id="home">
  <small>WELCOME TO MY WORLD</small>

  <h1>
    CREATIVE<br>
    <span>DEVELOPER.</span>
  </h1>

  <p>
    Building futuristic digital experiences with
    creativity, technology and interactive animations.
  </p>

  <button id="explore">Explore My Work ↗</button>
</section>

<section class="projects" id="projects">
  <h2>My Projects.</h2>

  <div class="project-grid">
    <div class="project">
      <h3>01. AgriMind AI</h3>
      <p>Smart agriculture monitoring using IoT and AI.</p>
    </div>

    <div class="project">
      <h3>02. SolarMind AI</h3>
      <p>Solar energy monitoring and intelligent analysis.</p>
    </div>

    <div class="project">
      <h3>03. Digital Twin</h3>
      <p>Smart elevator modernization system.</p>
    </div>

    <div class="project">
      <h3>04. AI Projects</h3>
      <p>Exploring artificial intelligence and automation.</p>
    </div>
  </div>
</section>

<script>
const people = [
  { name: "Alex", color: "#ff4242" },
  { name: "Sarah", color: "#00e5ff" },
  { name: "Mike", color: "#baff35" },
  { name: "Emma", color: "#d88aff" },
  { name: "John", color: "#ffb52e" }
];

const cursors = [];

people.forEach((person, index) => {
  const cursor = document.createElement("div");
  cursor.className = "cursor";
  cursor.style.color = person.color;

  cursor.innerHTML = `
    <svg viewBox="0 0 24 24" fill="${person.color}"
         stroke="white" stroke-width="1">
      <path d="M3 2 L3 20 L8 15 L12 22 L15 20 L11 13
               L19 13 Z"/>
    </svg>
    <div class="cursor-label"
         style="background:${person.color}">
      ${person.name}
    </div>
  `;

  document.body.appendChild(cursor);

  const ring = document.createElement("div");
  ring.className = "click-ring";
  ring.style.color = person.color;
  document.body.appendChild(ring);

  cursors.push({
    element: cursor,
    ring: ring,
    person: person,
    index: index,
    x: 100 + index * 80,
    y: 150 + index * 60,
    targetX: 0,
    targetY: 0,
    nextClick: 0
  });
});

// Independent movement paths
function getPosition(index, time) {
  const w = window.innerWidth;
  const h = window.innerHeight;

  const paths = [
    [0.15, 0.25, 0.72, 0.38, 0.45, 0.82],
    [0.75, 0.18, 0.25, 0.62, 0.85, 0.75],
    [0.45, 0.75, 0.82, 0.25, 0.18, 0.48],
    [0.85, 0.55, 0.48, 0.15, 0.30, 0.85],
    [0.30, 0.48, 0.68, 0.82, 0.12, 0.25]
  ];

  const p = paths[index];

  const phase = time / 1000 + index * 2;
  const segment = Math.floor(phase / 3) % 3;
  const progress = (phase % 3) / 3;

  const ease = progress * progress * (3 - 2 * progress);

  const x1 = p[segment * 2];
  const y1 = p[segment * 2 + 1];
  const x2 = p[((segment + 1) % 3) * 2];
  const y2 = p[((segment + 1) % 3) * 2 + 1];

  return {
    x: (x1 + (x2 - x1) * ease) * w,
    y: (y1 + (y2 - y1) * ease) * h
  };
}

function animate(time) {
  cursors.forEach((c, i) => {
    const target = getPosition(i, time);

    // Smooth cursor movement
    c.x += (target.x - c.x) * 0.045;
    c.y += (target.y - c.y) * 0.045;

    c.element.style.transform =
      `translate3d(${c.x}px, ${c.y}px, 0)`;

    // Simulated clicking
    const cycle = Math.floor(time / 1000 + i * 1.7);

    if (cycle !== c.nextClick &&
        Math.sin(time / 1000 + i * 2) > 0.97) {

      c.nextClick = cycle;

      c.ring.style.left = c.x + "px";
      c.ring.style.top = c.y + "px";

      c.ring.classList.remove("active");
      void c.ring.offsetWidth;
      c.ring.classList.add("active");
    }
  });

  requestAnimationFrame(animate);
}

requestAnimationFrame(animate);

// Actual user cursor remains separate
document.getElementById("explore").addEventListener("click", () => {
  document.getElementById("projects").scrollIntoView({
    behavior: "smooth"
  });
});
</script>

</body>
</html>

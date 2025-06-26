# interactive-periodic-table
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Interactive Periodic Table</title>
  <style>
    body { font-family: Arial,sans-serif; padding:20px; background:#f4f4f4 }
    h1 { text-align:center }
    .table { display:grid; grid-template-columns:repeat(18,60px); gap:4px; justify-content:center }
    .element {
      position:relative; background:#fff; border:1px solid #ccc;
      text-align:center; padding:6px 0; border-radius:4px;
      cursor:pointer; transition:transform .1s, background .2s;
    }
    .element:hover { transform:scale(1.05); background:rgba(200,230,255,0.5) }
    .tooltip {
      position:absolute; bottom:110%; left:50%;
      transform:translateX(-50%); background:#333; color:#fff;
      padding:6px; font-size:12px; border-radius:4px;
      white-space:nowrap; opacity:0; pointer-events:none;
      transition:opacity .2s;
    }
    .element:hover .tooltip { opacity:1 }
    .overlay, .modal { display:none; position:fixed; }
    .overlay {
      top:0; left:0; width:100%; height:100%;
      background:rgba(0,0,0,0.5); z-index:999;
    }
    .modal {
      top:15%; left:50%; transform:translateX(-50%);
      background:#fff; padding:20px; border-radius:8px;
      box-shadow:0 0 12px rgba(0,0,0,0.3);
      z-index:1000; max-width:320px; width:90%;
    }
    .modal h2 { margin-top:0 }
    .modal img { width:100%; max-height:200px; object-fit:contain; margin:12px 0 }
    .modal button { margin-top:12px; padding:8px 12px; font-size:14px }

    /* category colors */
    .alkali { background:#ff9999 }
    .alkaline-earth { background:#ffdead }
    .transition-metal { background:#ffcc80 }
    .post-transition-metal { background:#ffff99 }
    .metalloid { background:#cccccc }
    .diatomic-nonmetal, .polyatomic-nonmetal { background:#b3e6b3 }
    .halogen { background:#99ccff }
    .noble-gas { background:#c2c2f0 }
    .lanthanoid { background:#ffb3e6 }
    .actinoid { background:#ff99ff }
  </style>
</head>
<body>
  <h1>Interactive Periodic Table</h1>
  <div class="table" id="periodic-table"></div>
  <div class="overlay" id="overlay"></div>
  <div class="modal" id="modal">
    <h2 id="element-name"></h2>
    <img id="element-img" src="" alt="Element image"/>
    <p><strong>Symbol:</strong> <span id="element-symbol"></span></p>
    <p><strong>Atomic #:</strong> <span id="element-number"></span></p>
    <p><strong>Mass:</strong> <span id="element-mass"></span></p>
    <p><strong>Category:</strong> <span id="element-category"></span></p>
    <button onclick="closeModal()">Close</button>
  </div>

  <script>
    // load Bowserinator dataset
    async function loadElements() {
      const resp = await fetch('https://cdn.jsdelivr.net/gh/Bowserinator/Periodic-Table-JSON@master/PeriodicTableJSON.json');
      const data = await resp.json();
      return data.elements.map(el=>({
        number: el.number,
        symbol: el.symbol,
        name: el.name,
        mass: el.atomic_mass,
        category: el.category.replace(/\s+/g,'-'),
        x: el.xpos,
        y: el.ypos,
        image: el.image && el.image.url ? el.image.url : null
      }));
    }

    const categoryNames = {
      "alkali-metal":"Alkali Metal", "alkaline-earth":"Alkaline Earth",
      "transition-metal":"Transition Metal", "post-transition-metal":"Post-transition Metal",
      metalloid:"Metalloid",
      "diatomic-nonmetal":"Nonmetal","polyatomic-nonmetal":"Nonmetal",
      halogen:"Halogen", "noble-gas":"Noble Gas",
      lanthanoid:"Lanthanoid", actinoid:"Actinoid"
    };

    function renderTable(elements) {
      const table = document.getElementById("periodic-table");
      table.innerHTML = '';
      elements.forEach(el => {
        const div = document.createElement("div");
        div.className = `element ${el.category}`;
        div.style.gridColumn = el.x;
        div.style.gridRow = el.y;
        div.innerHTML = `
          <strong>${el.symbol}</strong><br><small>${el.number}</small>
          <div class="tooltip">${el.name} (${categoryNames[el.category]||el.category})</div>`;
        div.addEventListener("click", () => showModal(el));
        table.appendChild(div);
      });
    }

    function showModal(el) {
      document.getElementById("element-name").textContent = el.name;
      document.getElementById("element-symbol").textContent = el.symbol;
      document.getElementById("element-number").textContent = el.number;
      document.getElementById("element-mass").textContent = el.mass;
      document.getElementById("element-category").textContent = categoryNames[el.category] || el.category;
      const img = document.getElementById("element-img");
      if(el.image) {
        img.src = el.image;
        img.style.display = 'block';
      } else img.style.display = 'none';
      document.getElementById("overlay").style.display = 'block';
      document.getElementById("modal").style.display = 'block';
    }

    function closeModal() {
      document.getElementById("overlay").style.display = 'none';
      document.getElementById("modal").style.display = 'none';
    }

    // initialize
    loadElements().then(els=>{
      renderTable(els);
    });
  </script>
</body>
</html>



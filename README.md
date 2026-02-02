<!--
author: Sayan Goswami
version: 0.2
comment: Global SVG annotation overlay for live teaching

@onload
(() => {
  if (window.__liaAnnotate) return; // avoid double load

  const NS = "http://www.w3.org/2000/svg";

  const eraserRadius = 10;

  /* ---------- DOM ---------- */

  const root = document.createElement("div");
  root.style.cssText = `
    position:fixed;
    inset:0;
    z-index:9999;
    pointer-events:none;
  `;

  const svg = document.createElementNS(NS, "svg");
  svg.style.cssText = "width:100%; height:100%;";
  root.appendChild(svg);
  document.body.appendChild(root);

  /* ---------- state ---------- */

  let active = false;
  let drawing = false;
  let path = null;
  let color = "red";
  let mode = "draw"; // "draw" or "erase"

  function resize() {
    svg.setAttribute("viewBox", `0 0 ${innerWidth} ${innerHeight}`);
  }

  function pt(e) {
    return { x: e.clientX, y: e.clientY };
  }

  function distance(p, q) {
    return Math.sqrt((p.x - q.x)**2 + (p.y - q.y)**2);
  }

  /* ---------- drawing ---------- */

  function down(e) {
    if (!active) return;
    drawing = true;

    const p = pt(e);
    path = document.createElementNS(NS, "path");
    path.setAttribute("fill", "none");
    path.setAttribute("stroke", color);
    path.setAttribute("stroke-width", "3");
    path.setAttribute("stroke-linecap", "round");
    path.setAttribute("stroke-linejoin", "round");
    path.setAttribute("d", `M ${p.x} ${p.y}`);
    svg.appendChild(path);

    e.preventDefault();
  }

  function move(e) {
    if (!drawing) return;
    const p = pt(e);
    path.setAttribute("d",
      path.getAttribute("d") + ` L ${p.x} ${p.y}`);
    e.preventDefault();
  }

  // override pointermove for eraser
  function eraseMove(e) {
    if (mode !== "erase") return;

    const p = pt(e); // current pointer
    const paths = Array.from(svg.querySelectorAll("path"));
    paths.forEach(path => {
        const d = path.getAttribute("d");
        const points = d.match(/M|L|[\d\.]+/g).slice(1); // crude split
        for (let i = 0; i < points.length; i+=2) {
        const x = parseFloat(points[i]);
        const y = parseFloat(points[i+1]);
        if (distance(p, {x, y}) < eraserRadius) {
            path.remove();
            break;
        }
        }
    });
  }

  function up() { drawing = false; }

  resize();
  addEventListener("resize", resize);
  svg.addEventListener("pointerdown", down);
  svg.addEventListener("pointermove", move);
  addEventListener("pointerup", up);

  // override pointermove
  svg.addEventListener("pointermove", e => {
    if (mode === "draw") move(e);
    else eraseMove(e);
  });

  /* ---------- API ---------- */

  window.__liaAnnotate = {
    toggle() {
      active = !active;
      root.style.pointerEvents = active ? "auto" : "none";
      svg.style.pointerEvents  = active ? "auto" : "none";
    },
    clear() { svg.innerHTML = ""; },
    color(c) { color = c; }
  };

  /* ---------- keyboard ---------- */

  document.addEventListener("keydown", e => {
    const A = window.__liaAnnotate;
    if (!A) return;

    switch (e.key) {
      case "`": A.toggle(); break;
      case "e": mode = (mode == "erase")? "draw" : "erase"; break;
      case "c": A.clear(); break;
      case "1": A.color("red"); break;
      case "2": A.color("blue"); break;
      case "3": A.color("green"); break;
      case "4": A.color("orange"); break;
      case "5": A.color("black"); break;
    }
  });
})();
@end
-->

# Drawing

    --{{0}}--
This is a simple example for a drawing tool, that can be used in classrooms.

## This is another slide

## Now you can draw on all slides
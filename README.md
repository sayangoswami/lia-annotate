<!--
author: LiaScript SVG Annotation
version: 0.1
-->

<!-- ===============================
     CSS
     =============================== -->
<style>
.lia-annotate-root {
  position: absolute;
  inset: 0;
  z-index: 999;
}

.lia-annotate-svg {
  width: 100%;
  height: 100%;
  pointer-events: none; /* disabled by default */
}
</style>

<!-- ===============================
     MACRO
     =============================== -->
<!--
define: Annotate
-->

<div class="lia-annotate-root">
  <svg class="lia-annotate-svg"></svg>
</div>

<script>
(() => {
  const root = document.currentScript.previousElementSibling;
  const svg  = root.querySelector("svg");

  let drawing = false;
  let path;
  let color = "red";
  let active = false;

  const NS = "http://www.w3.org/2000/svg";

  function resize() {
    const r = root.getBoundingClientRect();
    svg.setAttribute("viewBox", `0 0 ${r.width} ${r.height}`);
  }

  function pt(e) {
    const r = svg.getBoundingClientRect();
    return { x: e.clientX - r.left, y: e.clientY - r.top };
  }

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
    path.setAttribute(
      "d",
      path.getAttribute("d") + ` L ${p.x} ${p.y}`
    );
    e.preventDefault();
  }

  function up() {
    drawing = false;
  }

  function activate() {
    active = true;
    svg.style.pointerEvents = "auto";
  }

  function deactivate() {
    active = false;
    svg.style.pointerEvents = "none";
  }

  function clear() {
    svg.innerHTML = "";
  }

  resize();
  window.addEventListener("resize", resize);

  svg.addEventListener("pointerdown", down);
  svg.addEventListener("pointermove", move);
  window.addEventListener("pointerup", up);

  // expose slide-local API
  window.__liaAnnotate = {
    activate,
    deactivate,
    clear,
    setColor: c => color = c,
    toggle: () => active ? deactivate() : activate()
  };
})();
</script>

<!-- ===============================
     KEYBOARD CONTROLS
     =============================== -->
<script>
document.addEventListener("keydown", e => {
  const A = window.__liaAnnotate;
  if (!A) return;

  switch (e.key) {
    case "`": A.toggle();        break;
    case "c": A.clear();         break;
    case "1": A.setColor("red"); break;
    case "2": A.setColor("blue"); break;
    case "3": A.setColor("green"); break;
    case "4": A.setColor("orange"); break;
    case "5": A.setColor("black"); break;
  }
});
</script>

<script>
alert("LIA-ANNOTATE LOADED");
</script>
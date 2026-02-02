<!--
author: Sayan Goswami
version: 0.1
language: en
comment: Full-slide SVG annotation overlay for live teaching

link: style.css

@annotate: @annotate_(@uid)

@annotate_
<section class="lia-annotate-slide" id="annotate_@0">
  <svg class="lia-annotate-svg"></svg>

  <script style="display:block">
    setTimeout(() => {
      const root = document.getElementById("annotate_@0");
      const svg  = root.querySelector("svg");

      let active = false;
      let drawing = false;
      let path = null;
      let color = "red";

      const NS = "http://www.w3.org/2000/svg";

      function resize() {
        const r = root.getBoundingClientRect();
        svg.setAttribute("viewBox", `0 0 ${r.width} ${r.height}`);
      }

      function pt(e) {
        const r = svg.getBoundingClientRect();
        return {
          x: e.clientX - r.left,
          y: e.clientY - r.top
        };
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

      resize();
      window.addEventListener("resize", resize);

      svg.addEventListener("pointerdown", down);
      svg.addEventListener("pointermove", move);
      window.addEventListener("pointerup", up);

      // register this slide as the active annotator
      window.__liaAnnotate = {
        toggle() {
          active = !active;
          svg.style.pointerEvents = active ? "auto" : "none";
        },
        clear() {
          svg.innerHTML = "";
        },
        color(c) {
          color = c;
        }
      };
    }, 0);
  </script>
</section>
@end

<script style="display:block">
document.addEventListener("keydown", e => {
  const A = window.__liaAnnotate;
  if (!A) return;

  switch (e.key) {
    case "p": A.toggle(); break;
    case "c": A.clear(); break;
    case "1": A.color("red"); break;
    case "2": A.color("blue"); break;
    case "3": A.color("green"); break;
    case "4": A.color("orange"); break;
    case "5": A.color("black"); break;
  }
});
</script>

-->
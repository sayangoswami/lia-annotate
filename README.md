<!--
author: Sayan Goswami
version: 1.0
comment: Global annotation overlay with step-based replay

@onload
(function(){

  let enabled=false;
  let drawing=false;
  let path=null;
  let color="red";
  let step=0;
  let mode="draw";

  const NS="http://www.w3.org/2000/svg";

  const root=document.createElement("section");
  root.style="position:fixed; inset:0; z-index:10000; pointer-events:none";

  const svg=document.createElementNS(NS,"svg");
  svg.style="width:100%; height:100%; pointer-events:none";
  root.appendChild(svg);
  document.body.appendChild(root);

  function pt(e){
    const r=svg.getBoundingClientRect();
    return {x:e.clientX-r.left,y:e.clientY-r.top};
  }

  function down(e){
    if(!enabled) return;

    if(mode==="erase"){
      erase(e);
      return;
    }

    drawing=true;
    const p=pt(e);

    path=document.createElementNS(NS,"path");
    path.setAttribute("fill","none");
    path.setAttribute("stroke",color);
    path.setAttribute("stroke-width","3");
    path.dataset.step=step;
    path.setAttribute("d",`M ${p.x} ${p.y}`);
    svg.appendChild(path);
  }

  function move(e){
    if(!enabled||!drawing) return;
    const p=pt(e);
    path.setAttribute("d",path.getAttribute("d")+` L ${p.x} ${p.y}`);
  }

  function up(){
    if(drawing) step++;
    drawing=false;
  }

  function erase(e){
    const p=pt(e);
    const paths=[...svg.querySelectorAll("path")];
    paths.forEach(el=>{
      const box=el.getBBox();
      if(p.x>box.x-10&&p.x<box.x+box.width+10 &&
         p.y>box.y-10&&p.y<box.y+box.height+10){
           el.remove();
      }
    });
  }

  svg.addEventListener("pointerdown",down);
  svg.addEventListener("pointermove",move);
  window.addEventListener("pointerup",up);

  function saveSVG(){
    const NS="http://www.w3.org/2000/svg";

    // clone the drawing
    const clone=svg.cloneNode(true);

    // VERY IMPORTANT: ensure namespace exists
    clone.setAttribute("xmlns",NS);

    // replay script (NOT escaped)
    const replayCode = `
        (function(){
            const paths=[...document.querySelectorAll("path")];
            paths.forEach(p => p.style.visibility="hidden");

            let cur=-1;

            function show(n){
                paths.forEach(p=>{
                if(Number(p.dataset.step)<=n) p.style.visibility="visible";
                else p.style.visibility="hidden";
                });
            }

            document.addEventListener("keydown",e=>{
                if(e.key==="ArrowRight"){cur++;show(cur);}
                if(e.key==="ArrowLeft"){cur=Math.max(-1,cur-1);show(cur);}
            });
        })();
    `;

    // insert script into SVG safely
    const script=document.createElementNS(NS,"script");
    script.textContent=replayCode;
    clone.appendChild(script);

    // serialize properly
    const serializer=new XMLSerializer();
    const source=serializer.serializeToString(clone);

    // const blob=new Blob([clone.outerHTML],{type:"image/svg+xml"});
    const blob=new Blob([source],{type:"image/svg+xml;charset=utf-8"});
    
    const a=document.createElement("a");
    a.href=URL.createObjectURL(blob);
    a.download="annotated-"+Date.now()+".svg";
    a.click();
  }

  document.addEventListener("keydown",e=>{
    switch(e.key){
      case "`":
        enabled=!enabled;
        root.style.pointerEvents=enabled?"auto":"none";
        svg.style.pointerEvents=enabled?"auto":"none";
        break;

      case "c":
        if(enabled){saveSVG();svg.innerHTML="";}
        break;

      case "s":
        if(enabled) saveSVG();
        break;

      case "e":
        mode="erase";
        break;

      case "p":
        mode="draw";
        break;

      case "1": color="red"; break;
      case "2": color="blue"; break;
      case "3": color="green"; break;
      case "4": color="orange"; break;
      case "5": color="black"; break;
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
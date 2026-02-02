<!--
author: Sayan Goswami
version: 0.1
language: en
comment: Full-slide SVG annotation overlay for live teaching

<button onclick="setMode('pencil')" style="cursor:pointer; background:none; border:none; color:white; font-size:16px; margin-right:5px;">✏️ Pen</button>
  <input type="color" id="colorPicker" value="#ff0000" onchange="updateColor(this.value)" style="cursor:pointer; border:none; height:20px; width:25px; vertical-align:middle;">
  <button onclick="setMode('eraser')" style="cursor:pointer; background:none; border:none; color:white; font-size:16px; margin:0 5px;">🧼 Eraser</button>
  <button onclick="clearCanvas()" style="cursor:pointer; background:none; border:none; color:white; font-size:16px; margin:0 5px;">🗑️ Clear</button>
  <button onclick="saveDrawing()" style="cursor:pointer; background:none; border:1px solid #fff; border-radius:4px; color:white; font-size:12px; padding:2px 5px; margin-left:10px;">💾 Save</button>
  <div style="border-top:1px solid #555; margin-top:5px; padding-top:2px; font-size: 10px; text-align:center; color:#aaa;">Press [B] to Toggle</div>
</div>

<canvas id="liaCanvas" style="position: fixed; top: 0; left: 0; z-index: 10000; pointer-events: none;"></canvas>

<script src="https://cdnjs.cloudflare.com/ajax/libs/fabric.js/5.3.1/fabric.min.js"></script>

<script>
// Initialize Canvas
var canvas = new fabric.Canvas('liaCanvas', {
    isDrawingMode: false,
    selection: false,
    width: window.innerWidth,
    height: window.innerHeight
});

// Handle Window Resizing
window.addEventListener('resize', () => {
    canvas.setDimensions({ width: window.innerWidth, height: window.innerHeight });
});

// Toggle Logic (Key 'b')
document.addEventListener('keydown', (e) => {
    if (e.key.toLowerCase() === 'b') {
        toggleDrawingMode();
    }
});

function toggleDrawingMode() {
    canvas.isDrawingMode = !canvas.isDrawingMode;
    const isActive = canvas.isDrawingMode;
    const ui = document.getElementById('drawing-ui');
    const canvasEl = document.getElementById('liaCanvas');

    // Toggle Pointer Events so you can click slides when NOT drawing
    canvasEl.parentElement.style.pointerEvents = isActive ? 'all' : 'none';
    
    // Toggle UI visibility
    ui.style.display = isActive ? 'block' : 'none';

    if(isActive) {
        setMode('pencil');
    }
}

// Brush Modes
function setMode(mode) {
    if (mode === 'pencil') {
        canvas.freeDrawingBrush = new fabric.PencilBrush(canvas);
        canvas.freeDrawingBrush.color = document.getElementById('colorPicker').value;
        canvas.freeDrawingBrush.width = 4;
    } else if (mode === 'eraser') {
        // "Eraser" effectively paints white (or background color)
        // For true transparency erasing, we need globalCompositeOperation 'destination-out', 
        // but Fabric handles this via color matching easiest in this lightweight script.
        canvas.freeDrawingBrush = new fabric.PencilBrush(canvas);
        canvas.freeDrawingBrush.color = 'rgba(255,255,255,1)'; 
        canvas.freeDrawingBrush.width = 25;
    }
}

function updateColor(color) {
    canvas.freeDrawingBrush.color = color;
    // If they pick a color, assume they want to go back to pencil mode
    canvas.freeDrawingBrush.width = 4;
}

function clearCanvas() {
    canvas.clear();
    // Re-set background to transparent just in case
    canvas.setBackgroundColor('rgba(0,0,0,0)', canvas.renderAll.bind(canvas));
}

// Save Functionality
function saveDrawing() {
    // 1. Create a data URL from the canvas
    const dataURL = canvas.toDataURL({
        format: 'png',
        quality: 1,
        multiplier: 2 // Saves at double resolution for clarity
    });

    // 2. Create a temporary link to trigger download
    const link = document.createElement('a');
    link.download = 'lia-slide-annotation.png';
    link.href = dataURL;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
}
</script>
@end
-->


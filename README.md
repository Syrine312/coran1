
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
        }
        
        .controls {
            display: flex;
            padding: 10px;
            background-color: #f0f0f0;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
        }
        
        button {
            padding: 8px 15px;
            cursor: pointer;
            border: none;
            border-radius: 4px;
            background-color: #4a6fa5;
            color: white;
        }
        
        button:hover {
            background-color: #3a5a80;
        }
        
        #clearBtn {
            background-color: #dc3545;
        }
        
        #downloadBtn {
            background-color: #28a745;
        }
        
        .control-group {
            display: flex;
            align-items: center;
            gap: 5px;
            margin-right: 15px;
        }
        
        .canvas-container {
            flex-grow: 1;
            overflow: auto;
            position: relative;
        }
        
        canvas {
            display: block;
            background-color: white;
            cursor: crosshair;
            width: 100%;
            height: 100%;
        }
        
        label {
            font-weight: bold;
        }
    </style>
</head>
<body>
    <div class="controls">
        <div class="control-group">
            <label for="colorPicker">Couleur:</label>
            <input type="color" id="colorPicker" value="#000000">
        </div>
        
        <div class="control-group">
            <label for="opacityRange">Opacité:</label>
            <input type="range" id="opacityRange" min="1" max="100" value="100">
            <span id="opacityValue">100%</span>
        </div>
        
        <div class="control-group">
            <label for="brushSize">Pinceau:</label>
            <input type="range" id="brushSize" min="1" max="50" value="5">
            <span id="brushSizeValue">5px</span>
        </div>
        
        <button id="startBtn">Commencer à colorer</button>
        <button id="clearBtn">Effacer tout</button>
        <button id="downloadBtn">Télécharger l'image</button>
    </div>
    
    <div class="canvas-container">
        <canvas id="drawingCanvas"></canvas>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const canvas = document.getElementById('drawingCanvas');
            const ctx = canvas.getContext('2d');
            const colorPicker = document.getElementById('colorPicker');
            const opacityRange = document.getElementById('opacityRange');
            const opacityValue = document.getElementById('opacityValue');
            const brushSize = document.getElementById('brushSize');
            const brushSizeValue = document.getElementById('brushSizeValue');
            const startBtn = document.getElementById('startBtn');
            const clearBtn = document.getElementById('clearBtn');
            const downloadBtn = document.getElementById('downloadBtn');
            
            let isDrawing = false;
            let currentColor = colorPicker.value;
            let currentOpacity = parseInt(opacityRange.value) / 100;
            let currentBrushSize = parseInt(brushSize.value);
            
            // Charger une image par défaut (blanche)
            function initCanvas() {
                canvas.width = window.innerWidth;
                canvas.height = window.innerHeight - 50; // 50px pour les contrôles
                ctx.fillStyle = 'white';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
            }
            
            // Redimensionner le canvas quand la fenêtre change de taille
            window.addEventListener('resize', function() {
                const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                canvas.width = window.innerWidth;
                canvas.height = window.innerHeight - 50;
                ctx.putImageData(imageData, 0, 0);
            });
            
            initCanvas();
            
            // Événements pour les contrôles
            colorPicker.addEventListener('input', function() {
                currentColor = this.value;
            });
            
            opacityRange.addEventListener('input', function() {
                currentOpacity = parseInt(this.value) / 100;
                opacityValue.textContent = this.value + '%';
            });
            
            brushSize.addEventListener('input', function() {
                currentBrushSize = parseInt(this.value);
                brushSizeValue.textContent = this.value + 'px';
            });
            
            startBtn.addEventListener('click', function() {
                isDrawing = !isDrawing;
                this.textContent = isDrawing ? 'Arrêter le coloriage' : 'Commencer à colorer';
                this.style.backgroundColor = isDrawing ? '#dc3545' : '#4a6fa5';
            });
            
            clearBtn.addEventListener('click', function() {
                if (confirm('Voulez-vous vraiment tout effacer?')) {
                    ctx.fillStyle = 'white';
                    ctx.fillRect(0, 0, canvas.width, canvas.height);
                }
            });
            
            downloadBtn.addEventListener('click', function() {
                const link = document.createElement('a');
                link.download = 'mon-dessin.png';
                link.href = canvas.toDataURL('image/png');
                link.click();
            });
            
            // Fonctionnalité de dessin
            canvas.addEventListener('mousedown', startDrawing);
            canvas.addEventListener('mousemove', draw);
            canvas.addEventListener('mouseup', stopDrawing);
            canvas.addEventListener('mouseout', stopDrawing);
            
            function startDrawing(e) {
                if (!isDrawing) return;
                isDrawing = true;
                draw(e);
            }
            
            function draw(e) {
                if (!isDrawing) return;
                
                ctx.globalAlpha = currentOpacity;
                ctx.fillStyle = currentColor;
                
                const rect = canvas.getBoundingClientRect();
                const x = e.clientX - rect.left;
                const y = e.clientY - rect.top;
                
                ctx.beginPath();
                ctx.arc(x, y, currentBrushSize, 0, Math.PI * 2);
                ctx.fill();
            }
            
            function stopDrawing() {
                isDrawing = false;
                ctx.beginPath();
            }
        });
    </script>
</body>
</html>

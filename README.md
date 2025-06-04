
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
   
    <style>
      :root {
        --primary-color: #4a6fa5;
        --secondary-color: #ffc0cb;
        --accent-color: #ff8c94;
        --light-color: #f8f9fa;
        --dark-color: #343a40;
        --border-radius: 8px;
        --box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      }

      body {
        font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
        display: flex;
        flex-direction: column;
        align-items: center;
        margin: 0;
        padding: 20px;
        background-color: #f5f7fa;
        color: var(--dark-color);
        line-height: 1.6;
      }

      h2 {
        color: var(--primary-color);
        margin-bottom: 25px;
        text-align: center;
        font-size: 2rem;
        text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
      }

      canvas {
        border: 2px solid var(--primary-color);
        border-radius: var(--border-radius);
        cursor: crosshair;
        margin-top: 20px;
        box-shadow: var(--box-shadow);
        background-color: white;
        max-width: 100%;
      }

      .controls {
        display: flex;
        gap: 15px;
        align-items: center;
        margin: 20px 0;
        flex-wrap: wrap;
        justify-content: center;
        background-color: white;
        padding: 15px;
        border-radius: var(--border-radius);
        box-shadow: var(--box-shadow);
        width: 90%;
        max-width: 800px;
      }

      label {
        display: flex;
        flex-direction: column;
        align-items: center;
        font-weight: 500;
        color: var(--primary-color);
        gap: 5px;
      }

      input[type="color"] {
        width: 40px;
        height: 40px;
        border: 2px solid var(--primary-color);
        border-radius: 50%;
        cursor: pointer;
        padding: 0;
      }

      input[type="color"]::-webkit-color-swatch {
        border: none;
        border-radius: 50%;
      }

      input[type="range"] {
        width: 100px;
        cursor: pointer;
      }

      button {
        padding: 10px 20px;
        font-size: 14px;
        cursor: pointer;
        background-color: var(--primary-color);
        color: white;
        border: none;
        border-radius: var(--border-radius);
        transition: all 0.3s ease;
        font-weight: 500;
        box-shadow: var(--box-shadow);
      }

      button:hover {
        background-color: var(--accent-color);
        transform: translateY(-2px);
      }

      button:active {
        transform: translateY(0);
      }

      #clear {
        background-color: #dc3545;
      }

      #clear:hover {
        background-color: #c82333;
      }

      #download {
        background-color: #28a745;
      }

      #download:hover {
        background-color: #218838;
      }

      @media (max-width: 768px) {
        .controls {
          flex-direction: column;
          align-items: stretch;
        }

        label {
          flex-direction: row;
          justify-content: space-between;
          width: 100%;
        }
      }
    </style>
  </head>

  <body>
    <h2>تشجيع على حفظ القران</h2>

    <div class="controls">
      <label>
        Couleur :
        <input type="color" id="colorPicker" value="#ffc0cb" />
      </label>
      <label>
        Opacité :
        <input
          type="range"
          id="opacitySlider"
          min="0"
          max="1"
          step="0.01"
          value="0.2"
        />
      </label>
      <label>
        Taille pinceau :
        <input type="range" id="brushSize" min="1" max="100" value="15" />
      </label>
      <button id="clear">Effacer tout</button>
      <button id="download">Télécharger l'image</button>
    </div>

    <canvas id="canvas"></canvas>

    <script>
      const canvas = document.getElementById("canvas");
      const ctx = canvas.getContext("2d");

      const colorPicker = document.getElementById("colorPicker");
      const opacitySlider = document.getElementById("opacitySlider");
      const brushSize = document.getElementById("brushSize");
      const clearBtn = document.getElementById("clear");
      const downloadBtn = document.getElementById("download");

      let drawing = false;
      let img = new Image();

      // URL de l'image intégrée directement dans le code
      const imageUrl =
        "data:image/jpeg;base64,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"; // Remplacez par votre URL d'image

      // Charger l'image intégrée
      img.onload = function () {
        canvas.width = img.width;
        canvas.height = img.height;
        ctx.drawImage(img, 0, 0);
        saveCanvas(); // Sauvegarde auto après chargement image
      };
      img.src = imageUrl;

      // Charger image sauvegardée
      const savedImage = localStorage.getItem("savedCanvas");
      if (savedImage) {
        const tempImg = new Image();
        tempImg.onload = function () {
          canvas.width = tempImg.width;
          canvas.height = tempImg.height;
          ctx.drawImage(tempImg, 0, 0);
        };
        tempImg.src = savedImage;
      }

      // Sauvegarde automatique
      function saveCanvas() {
        const dataURL = canvas.toDataURL("image/png");
        localStorage.setItem("savedCanvas", dataURL);
      }

      // Dessin
      canvas.addEventListener("mousedown", () => (drawing = true));
      canvas.addEventListener("mouseup", () => {
        drawing = false;
        saveCanvas(); // Sauvegarde après dessin
      });
      canvas.addEventListener("mouseleave", () => {
        drawing = false;
        saveCanvas();
      });
      canvas.addEventListener("mousemove", draw);

      function draw(e) {
        if (!drawing) return;

        const rect = canvas.getBoundingClientRect();
        const x = e.clientX - rect.left;
        const y = e.clientY - rect.top;

        const color = colorPicker.value;
        const opacity = parseFloat(opacitySlider.value);
        const size = parseInt(brushSize.value);

        const r = parseInt(color.slice(1, 3), 16);
        const g = parseInt(color.slice(3, 5), 16);
        const b = parseInt(color.slice(5, 7), 16);

        ctx.fillStyle = `rgba(${r}, ${g}, ${b}, ${opacity})`;
        ctx.beginPath();
        ctx.arc(x, y, size, 0, Math.PI * 2);
        ctx.fill();
      }

      // Effacer tout
      clearBtn.addEventListener("click", () => {
        if (img.src) {
          ctx.clearRect(0, 0, canvas.width, canvas.height);
          ctx.drawImage(img, 0, 0);
        } else {
          ctx.clearRect(0, 0, canvas.width, canvas.height);
        }
        saveCanvas(); // Sauvegarde après effacement
      });

      // Télécharger l'image finale
      downloadBtn.addEventListener("click", () => {
        const link = document.createElement("a");
        link.download = "image_coloriee.png";
        link.href = canvas.toDataURL("image/png");
        link.click();
      });
    </script>
  </body>
</html>

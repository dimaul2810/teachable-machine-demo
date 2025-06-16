
---

## Detector `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Deteksi Botol dengan Teachable Machine</title>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
  <style>
    body { font-family: Arial, sans-serif; }
    #webcam-container { margin-top: 10px; }
    #label-container div { margin: 5px 0; font-size: 18px; }
  </style>
</head>
<body>
  <h2>Deteksi Botol vs Bukan Botol</h2>
  <button onclick="init()">Start Kamera</button>
  <div id="webcam-container"></div>
  <div id="label-container"></div>

  <script>
    const URL = "https://teachablemachine.withgoogle.com/models/TxwTiAFD4/";

    let model, webcam, labelContainer, maxPredictions;

    async function init() {
      const modelURL = URL + "model.json";
      const metadataURL = URL + "metadata.json";

      model = await tmImage.load(modelURL, metadataURL);
      maxPredictions = model.getTotalClasses();

      const flip = true;
      webcam = new tmImage.Webcam(200, 200, flip);
      await webcam.setup();
      await webcam.play();

      document.getElementById("webcam-container").appendChild(webcam.canvas);

      labelContainer = document.getElementById("label-container");
      labelContainer.innerHTML = "";
      for(let i = 0; i < maxPredictions; i++) {
        const div = document.createElement("div");
        labelContainer.appendChild(div);
      }

      window.requestAnimationFrame(loop);
    }

    async function loop() {
      webcam.update();
      await predict();
      window.requestAnimationFrame(loop);
    }

    async function predict() {
      const prediction = await model.predict(webcam.canvas);

      let highestProb = 0;
      let highestClass = "";

      for(let i = 0; i < maxPredictions; i++) {
        const classPrediction = `${prediction[i].className}: ${(prediction[i].probability * 100).toFixed(2)}%`;
        labelContainer.childNodes[i].innerHTML = classPrediction;

        if (prediction[i].probability > highestProb) {
          highestProb = prediction[i].probability;
          highestClass = prediction[i].className;
        }
      }

      // Kirim kelas dengan probabilitas tertinggi ke MIT App Inventor lewat WebViewString
      window.WebViewString = highestClass;
    }
  </script>
</body>
</html>

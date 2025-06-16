
---

## Contoh Isi `index.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Teachable Machine Demo</title>
    <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest/dist/tf.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest/dist/teachablemachine-image.min.js"></script>
</head>
<body>
    <h3>Teachable Machine Image Model</h3>
    <button onclick="init()">Start</button>
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

            webcam = new tmImage.Webcam(200, 200, true);
            await webcam.setup();
            await webcam.play();
            window.requestAnimationFrame(loop);

            document.getElementById("webcam-container").appendChild(webcam.canvas);
            labelContainer = document.getElementById("label-container");
            for (let i = 0; i < maxPredictions; i++) {
                labelContainer.appendChild(document.createElement("div"));
            }
        }

        async function loop() {
            webcam.update();
            await predict();
            window.requestAnimationFrame(loop);
        }

       async function predict() {
    const prediction = await model.predict(webcam.canvas);
    for (let i = 0; i < maxPredictions; i++) {
        const classPrediction = `${prediction[i].className}: ${prediction[i].probability.toFixed(2)}`;
        labelContainer.childNodes[i].innerHTML = classPrediction;
    }

    // Kirim hasil klasifikasi ke MIT App Inventor melalui WebViewString
    if (window.AppInventor) {
        window.AppInventor.setWebViewString(prediction[0].className);
    }
}

    </script>
</body>
</html>

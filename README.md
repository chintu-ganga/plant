#Plant identifier
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plant Identifier</title>
    <link rel="stylesheet" type="text/css" href="plant.css">
</head>
<body>
    <div class="container">
        <h1>Plant Identifier</h1>
        <input type="file" id="plantImage" accept="image/*">
        <button id="identifyButton">Identify Plant</button>
        <div id="result"></div>
    </div>
    <script src="plant.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #f0f0f0;
}

.container {
    text-align: center;
    background: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

button {
    margin-top: 10px;
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

button:hover {
    background-color: #45a049;
}

#result {
    margin-top: 20px;
}
document.getElementById('identifyButton').addEventListener('click', identifyPlant);

async function identifyPlant() {
    const fileInput = document.getElementById('plantImage');
    const file = fileInput.files[0];
    if (!file) {
        alert('Please upload an image.');
        return;
    }

    const formData = new FormData();
    formData.append('image', file);

    const response = await fetch('https://api.plant.id/v2/identify', {
        method: 'POST',
        headers: {
            'Api-Key': 'fxRagklNQeOtdPi26f3NYHVabmEmetbNi0tjky3TMbM3oLQgsZ'
        },
        body: formData
    });

    const result = await response.json();
    displayResult(result, file);
}

function displayResult(result, file) {
    const resultDiv = document.getElementById('result');
    if (result.suggestions && result.suggestions.length > 0) {
        const plant = result.suggestions[0].plant_name;
        const commonName = result.suggestions[0].common_names ? result.suggestions[0].common_names.join(', ') : "No common name available.";
        const probability = result.suggestions[0].probability;
        const characteristics = result.suggestions[0].characteristics || "No characteristics available.";
        const description = result.suggestions[0].description || "No description available.";
        const environment = result.suggestions[0].environment || "No environment information available.";
        const nature = result.suggestions[0].nature || "No nature information available.";

        const imageUrl = URL.createObjectURL(file);

        resultDiv.innerHTML = `
            <h2>Plant: ${plant}</h2>
            <p>Probability: ${(probability * 100).toFixed(2)}%</p>
            <img src="${imageUrl}" alt="Plant Image" style="max-width: 100%; height: auto;">
            <h3>Common Name:</h3>
            <p>${commonName}</p>
            <h3>Characteristics:</h3>
            <table>
                <tr><th>Characteristic</th><th>Details</th></tr>
                <tr><td>Description</td><td>${description}</td></tr>
                <tr><td>Nature</td><td>${nature}</td></tr>
                <tr><td>Environment</td><td>${environment}</td></tr>
                <tr><td>Other Characteristics</td><td>${characteristics}</td></tr>
            </table>
        `;
    } else {
        resultDiv.innerHTML = '<p>Plant not identified. Please try another image.</p>';
    }
}

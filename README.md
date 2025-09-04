<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Weather Now</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to bottom, #6fb1fc, #4364f7);
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      color: #fff;
    }
    .weather-app {
      background: rgba(0, 0, 0, 0.3);
      padding: 20px;
      border-radius: 15px;
      text-align: center;
      width: 350px;
      box-shadow: 0px 4px 10px rgba(0,0,0,0.3);
    }
    input {
      padding: 10px;
      width: 70%;
      border: none;
      border-radius: 8px;
      outline: none;
    }
    button {
      padding: 10px;
      border: none;
      background: #ff9800;
      color: #fff;
      border-radius: 8px;
      cursor: pointer;
      margin-left: 5px;
    }
    button:hover {
      background: #e68900;
    }
    .result {
      margin-top: 20px;
      font-size: 18px;
    }
  </style>
</head>
<body>
  <div class="weather-app">
    <h2>Weather Now</h2>
    <input type="text" id="city" placeholder="Enter city name">
    <button onclick="getWeather()">Check</button>
    <div class="result" id="result"></div>
  </div>

  <script>
    async function getWeather() {
      const city = document.getElementById("city").value;
      const resultDiv = document.getElementById("result");

      if (!city) {
        resultDiv.innerHTML = "⚠️ Please enter a city name.";
        return;
      }

      try {
        // Step 1: Get latitude & longitude from Open-Meteo Geocoding API
        const geoRes = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${city}`);
        const geoData = await geoRes.json();

        if (!geoData.results || geoData.results.length === 0) {
          resultDiv.innerHTML = "❌ City not found!";
          return;
        }

        const { latitude, longitude, name, country } = geoData.results[0];

        // Step 2: Fetch weather data
        const weatherRes = await fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`
        );
        const weatherData = await weatherRes.json();

        const weather = weatherData.current_weather;

        resultDiv.innerHTML = `
          <h3>${name}, ${country}</h3>
          🌡️ Temperature: ${weather.temperature}°C<br>
          💨 Windspeed: ${weather.windspeed} km/h<br>
          ⏱️ Time: ${weather.time}
        `;
      } catch (error) {
        resultDiv.innerHTML = "⚠️ Error fetching weather data.";
        console.error(error);
      }
    }
  </script>
</body>
</html>

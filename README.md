// Import required modules
const express = require("express");
const axios = require("axios");
const cors = require("cors");

const app = express();
const PORT = process.env.PORT || 5000;

app.use(cors());
app.use(express.json());

// Open-source Astrology API (Replace with a working one if needed)
const ASTROLOGY_API_URL = "https://json.astrologyapi.com/v1/sun_sign_prediction/daily";
const API_USER_ID = "your_user_id";
const API_KEY = "your_api_key";

// Numerology Calculation Function
function calculateNumerology(name) {
    let sum = 0;
    for (let char of name.toUpperCase()) {
        if (char >= 'A' && char <= 'Z') {
            sum += (char.charCodeAt(0) - 64); // Convert A-Z to 1-26
        }
    }
    return sum % 9 || 9; // Return a single-digit numerology number
}

// API Endpoint for Astrology, Numerology & Vastu Analysis
app.post("/analyze", async (req, res) => {
    try {
        const { name, birthDate, birthTime, birthPlace } = req.body;
        const numerologyNumber = calculateNumerology(name);
        
        // Fetch Astrology Prediction from API
        const astroResponse = await axios.post(ASTROLOGY_API_URL, {
            day: "today",
            month: new Date(birthDate).getMonth() + 1,
            year: new Date(birthDate).getFullYear(),
            hour: birthTime.split(":")[0],
            min: birthTime.split(":")[1],
            lat: 28.6139, // Default: New Delhi (Replace with real lat/lon lookup)
            lon: 77.2090,
        }, {
            headers: {
                "Authorization": `Basic ${Buffer.from(`${API_USER_ID}:${API_KEY}`).toString("base64")}`,
                "Content-Type": "application/json"
            }
        });

        res.json({
            astrology: astroResponse.data,
            numerology: numerologyNumber,
            vastu: "Ensure proper light in North-East for positive energy.",
        });
    } catch (error) {
        console.error("Error fetching astrology data:", error);
        res.status(500).json({ error: "Failed to fetch astrology data." });
    }
});

app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
});

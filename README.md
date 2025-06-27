<html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Draw the Verb</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: "Inter", sans-serif; /* Inter Font */
        }
        /* Simple spinner animation */
        .spinner {
            border: 4px solid rgba(0, 0, 0, 0.1);
            border-left-color: #3B82F6;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body class="bg-gradient-to-r from-blue-400 to-purple-500 min-h-screen flex items-center justify-center p-4">
    <div class="bg-white p-8 rounded-2xl shadow-xl max-w-md w-full text-center">
        <h1 class="text-4xl font-extrabold text-gray-800 mb-6">Draw the Verb</h1>

        <!-- Container for the flashcard and the verb -->
        <div class="flex flex-col items-center justify-center mb-8">
            <!-- Flashcard image or loading spinner -->
            <div id="imageContainer" class="relative w-full max-w-full h-auto rounded-xl shadow-lg mb-4" style="min-height: 250px;">
                <img src="https://placehold.co/400x250/E0E7FF/3B82F6?text=Loading..."
                     onerror="this.src='https://placehold.co/400x250/E0E7FF/3B82F6?text=Error'"
                     alt="Image representing the verb"
                     class="rounded-xl max-w-full h-auto object-cover"
                     id="verbImage">
                <div id="loadingSpinner" class="absolute inset-0 flex items-center justify-center bg-white bg-opacity-75 rounded-xl hidden">
                    <div class="spinner"></div>
                </div>
            </div>


            <div class="flex items-center space-x-4 mb-6 mt-4">
                <!-- Current verb -->
                <p id="currentVerb" class="text-6xl font-bold text-gray-900">Loading...</p>
                <!-- Pronounce button -->
                <button id="pronounceButton" class="bg-blue-600 hover:bg-blue-700 text-white p-3 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-blue-300">
                    <span class="text-3xl">🔊</span>
                </button>
                <!-- Pronunciation -->
                <p id="pronunciation" class="text-4xl text-gray-600">...</p>
            </div>
        </div>

        <!-- Instructions for the user -->
        <p class="text-xl text-gray-700 mb-8">
            Draw this action on a piece of paper or your tablet if it has a stylus.
        </p>

        <!-- Area for drawing upload or "I've Drawn It" button -->
        <div class="mb-8 flex flex-col items-center">
            <button id="drawnButton" class="bg-green-500 hover:bg-green-600 text-white font-bold py-3 px-8 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-green-300 mb-4">
                I've Drawn It!
            </button>
            <!-- This could be an image upload area in the future, for now it's just a placeholder -->
            <div id="drawingArea" class="border-2 border-dashed border-gray-300 rounded-lg p-6 w-full max-w-xs h-32 flex items-center justify-center text-gray-500 italic">
                (Optional drawing upload area)
            </div>
        </div>

        <!-- Continue button -->
        <button id="continueButton" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-10 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-purple-300">
            Continue
        </button>
    </div>

    <script>
        // Array of verbs with their pronunciation
        const verbs = [
            { verb: "sing", pronunciation: "/sɪŋ/" },
            { verb: "read", pronunciation: "/riːd/" },
            { verb: "dance", pronunciation: "/dæns/" },
            { verb: "run", pronunciation: "/rʌn/" },
            { verb: "jump", pronunciation: "/dʒʌmp/" }
        ];

        let currentVerbIndex = 0;

        const verbImage = document.getElementById('verbImage');
        const loadingSpinner = document.getElementById('loadingSpinner');
        const currentVerbElement = document.getElementById('currentVerb');
        const pronunciationElement = document.getElementById('pronunciation');
        const pronounceButton = document.getElementById('pronounceButton');
        const drawnButton = document.getElementById('drawnButton');
        const continueButton = document.getElementById('continueButton');

        /**
         * Generates an image for the given verb using the Imagen API.
         * @param {string} verb - The verb to generate an image for.
         * @returns {Promise<string>} A promise that resolves with the base64 image URL.
         */
        async function generateVerbImage(verb) {
            loadingSpinner.classList.remove('hidden'); // Show spinner
            verbImage.src = "https://placehold.co/400x250/E0E7FF/3B82F6?text=Generating..."; // Placeholder while generating

            const prompt = `A simple, clear, and cartoonish drawing of a person ${verb}ing. Focus on the action.`;
            const payload = { instances: { prompt: prompt }, parameters: { "sampleCount": 1 } };
            const apiKey = ""; // API key will be provided by the Canvas environment
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict?key=${apiKey}`;

            try {
                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });
                const result = await response.json();

                if (result.predictions && result.predictions.length > 0 && result.predictions[0].bytesBase64Encoded) {
                    const imageUrl = `data:image/png;base64,${result.predictions[0].bytesBase64Encoded}`;
                    return imageUrl;
                } else {
                    console.error('Image generation failed: No valid image data received.', result);
                    throw new Error('No image data.');
                }
            } catch (error) {
                console.error('Error generating image:', error);
                // Fallback image in case of API error
                return `https://placehold.co/400x250/FFCCCC/CC0000?text=Error+Loading+Image`;
            } finally {
                loadingSpinner.classList.add('hidden'); // Hide spinner
            }
        }

        /**
         * Displays the current verb and generates its image.
         */
        async function displayCurrentVerb() {
            const verbData = verbs[currentVerbIndex];
            currentVerbElement.textContent = verbData.verb;
            pronunciationElement.textContent = verbData.pronunciation;

            // Generate and display image for the current verb
            const imageUrl = await generateVerbImage(verbData.verb);
            verbImage.src = imageUrl;

            // Optional: preloading the next image for a smoother transition
            // Not preloading images generated by AI as they are dynamic.
            // Preloading would require generating the next image in advance which might be resource intensive.
        }

        /**
         * Plays the pronunciation of the current verb.
         */
        pronounceButton.addEventListener('click', () => {
            const verbToPronounce = currentVerbElement.textContent;
            console.log(`Pronouncing: ${verbToPronounce}`);
            const utterance = new SpeechSynthesisUtterance(verbToPronounce);
            utterance.lang = 'en-US'; // Set language for correct pronunciation
            window.speechSynthesis.speak(utterance);
        });

        /**
         * Handles the click on the "I've Drawn It!" button.
         */
        drawnButton.addEventListener('click', () => {
            console.log("User has finished drawing!");
            drawnButton.textContent = "Drawing Completed!";
            drawnButton.classList.remove('bg-green-500', 'hover:bg-green-600');
            drawnButton.classList.add('bg-gray-400');
            setTimeout(() => {
                drawnButton.textContent = "I've Drawn It!";
                drawnButton.classList.remove('bg-gray-400');
                drawnButton.classList.add('bg-green-500', 'hover:bg-green-600');
            }, 1500);
        });

        /**
         * Advances to the next verb or ends the activity.
         */
        continueButton.addEventListener('click', () => {
            currentVerbIndex++;
            if (currentVerbIndex < verbs.length) {
                displayCurrentVerb();
            } else {
                currentVerbElement.textContent = "Activity Complete!";
                pronunciationElement.textContent = "";
                verbImage.src = "https://placehold.co/400x250/E0E7FF/3B82F6?text=Finished";
                pronounceButton.disabled = true;
                drawnButton.disabled = true;
                continueButton.disabled = true;
                continueButton.textContent = "End";
                console.log("All verbs have been displayed.");
            }
        });

        // Initialize the activity when the page loads
        displayCurrentVerb();
    </script>
</body>
</html>

 ELABORADO POR: MARIA PAZ PERALTA

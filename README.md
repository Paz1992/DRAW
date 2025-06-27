<html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Draw the verb</title>
    <!-- Carga de Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body {
            font-family: "Inter", sans-serif; /* Fuente Inter */
        }
    </style>
</head>
<body class="bg-gradient-to-r from-blue-400 to-purple-500 min-h-screen flex items-center justify-center p-4">
    <div class="bg-white p-8 rounded-2xl shadow-xl max-w-md w-full text-center">
        <h1 class="text-4xl font-extrabold text-gray-800 mb-6">Draw the verb</h1>

        <!-- Contenedor para el flashcard y el verbo -->
        <div class="flex flex-col items-center justify-center mb-8">
            <!-- Imagen del flashcard -->
            <img src="https://placehold.co/400x250/E0E7FF/3B82F6?text=Image+of+Verb"
                 onerror="this.src='https://placehold.co/400x250/E0E7FF/3B82F6?text=Read'"
                 alt="Image representing the verb"
                 class="rounded-xl shadow-lg mb-4 max-w-full h-auto"
                 id="verbImage">

            <div class="flex items-center space-x-4 mb-6">
                <!-- Verbo actual -->
                <p id="currentVerb" class="text-6xl font-bold text-gray-900">read</p>
                <!-- Botón de reproducción de sonido (simulado) -->
                <button id="pronounceButton" class="bg-blue-600 hover:bg-blue-700 text-white p-3 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-blue-300">
                    <span class="text-3xl">🔊</span>
                </button>
                <!-- Pronunciación -->
                <p id="pronunciation" class="text-4xl text-gray-600">/riːd/</p>
            </div>
        </div>

        <!-- Indicaciones para el usuario -->
        <p class="text-xl text-gray-700 mb-8">
            Dibuja esta acción en una hoja o en tu tablet si tiene lápiz.
        </p>

        <!-- Área para cargar dibujo o botón "Ya dibujé" -->
        <div class="mb-8 flex flex-col items-center">
            <button id="drawnButton" class="bg-green-500 hover:bg-green-600 text-white font-bold py-3 px-8 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-green-300 mb-4">
                ¡Ya dibujé!
            </button>
            <!-- Esto podría ser un área de carga de imágenes en el futuro, por ahora es solo un placeholder -->
            <div id="drawingArea" class="border-2 border-dashed border-gray-300 rounded-lg p-6 w-full max-w-xs h-32 flex items-center justify-center text-gray-500 italic">
                (Área opcional para cargar dibujo)
            </div>
        </div>

        <!-- Botón de continuar -->
        <button id="continueButton" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-3 px-10 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-105 focus:outline-none focus:ring-4 focus:ring-purple-300">
            Continue / Continuar
        </button>
    </div>

    <script>
        // Array de verbos con su pronunciación y una imagen placeholder (idealmente, esto vendría de una API o base de datos)
        const verbs = [
            { verb: "sing", pronunciation: "/sɪŋ/", imageUrl: "https://placehold.co/400x250/E0E7FF/3B82F6?text=Sing" },
            { verb: "read", pronunciation: "/riːd/", imageUrl: "https://placehold.co/400x250/E0E7FF/3B82F6?text=Read" }, // This is the user's provided image
            { verb: "dance", pronunciation: "/dæns/", imageUrl: "https://placehold.co/400x250/E0E7FF/3B82F6?text=Dance" }, // Reemplazado el base64 incompleto por un placeholder
            { verb: "run", pronunciation: "/rʌn/", imageUrl: "https://placehold.co/400x250/E0E7FF/3B82F6?text=Run" },
            { verb: "jump", pronunciation: "/dʒʌmp/", imageUrl: "https://placehold.co/400x250/E0E7FF/3B82F6?text=Jump" }
        ];

        let currentVerbIndex = 0;

        const verbImage = document.getElementById('verbImage');
        const currentVerbElement = document.getElementById('currentVerb');
        const pronunciationElement = document.getElementById('pronunciation');
        const pronounceButton = document.getElementById('pronounceButton');
        const drawnButton = document.getElementById('drawnButton');
        const continueButton = document.getElementById('continueButton');

        /**
         * Muestra el verbo actual en la interfaz.
         */
        function displayCurrentVerb() {
            const verbData = verbs[currentVerbIndex];
            currentVerbElement.textContent = verbData.verb;
            pronunciationElement.textContent = verbData.pronunciation;
            verbImage.src = verbData.imageUrl;
            // Opcional: precargar la siguiente imagen para una transición más fluida
            if (currentVerbIndex + 1 < verbs.length) {
                const nextImage = new Image();
                nextImage.src = verbs[currentVerbIndex + 1].imageUrl;
            }
        }

        /**
         * Reproduce la pronunciación del verbo actual (simulado).
         */
        pronounceButton.addEventListener('click', () => {
            const verbToPronounce = currentVerbElement.textContent;
            console.log(`Simulando pronunciación de: ${verbToPronounce}`);
            // Aquí iría la lógica para reproducir un sonido real
            // Por ejemplo, usando la API Web Speech Synthesis:
            const utterance = new SpeechSynthesisUtterance(verbToPronounce);
            utterance.lang = 'en-US'; // Establece el idioma para una pronunciación correcta
            window.speechSynthesis.speak(utterance);
        });

        /**
         * Maneja el clic en el botón "¡Ya dibujé!".
         */
        drawnButton.addEventListener('click', () => {
            // En una aplicación real, aquí podrías habilitar la subida del dibujo o alguna otra interacción.
            console.log("¡El usuario ha terminado de dibujar!");
            // Podrías dar feedback visual aquí, por ejemplo:
            drawnButton.textContent = "¡Dibujo Completado!";
            drawnButton.classList.remove('bg-green-500', 'hover:bg-green-600');
            drawnButton.classList.add('bg-gray-400');
            setTimeout(() => {
                drawnButton.textContent = "¡Ya dibujé!";
                drawnButton.classList.remove('bg-gray-400');
                drawnButton.classList.add('bg-green-500', 'hover:bg-green-600');
            }, 1500);
        });

        /**
         * Avanza al siguiente verbo o finaliza la actividad.
         */
        continueButton.addEventListener('click', () => {
            currentVerbIndex++;
            if (currentVerbIndex < verbs.length) {
                displayCurrentVerb();
            } else {
                currentVerbElement.textContent = "¡Actividad Completa!";
                pronunciationElement.textContent = "";
                verbImage.src = "https://placehold.co/400x250/E0E7FF/3B82F6?text=Finished";
                pronounceButton.disabled = true;
                drawnButton.disabled = true;
                continueButton.disabled = true;
                continueButton.textContent = "Fin";
                console.log("Todos los verbos han sido mostrados.");
            }
        });

        // Inicializar la actividad al cargar la página
        displayCurrentVerb();
    </script>
</body>
</html>
 ELABORADO POR: MARIA PAZ PERALTA

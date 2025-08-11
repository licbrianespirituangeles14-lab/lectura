<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Juego de Teleprónter</title>
    <!-- Incluye Tailwind CSS para un estilo moderno y responsivo -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a202c; /* Fondo oscuro */
            color: #e2e8f0; /* Texto claro */
        }
        .teleprompter-container {
            position: relative;
            height: 300px;
            overflow: hidden;
            border: 2px solid #4a5568;
            background-color: #2d3748;
        }
        .reading-line {
            position: absolute;
            top: 50%;
            left: 0;
            right: 0;
            height: 2px;
            background-color: #f6ad55; /* Naranja para la línea */
            transform: translateY(-50%);
        }
        .story-text {
            position: absolute;
            top: 100%;
            width: 100%;
            text-align: center;
            line-height: 2;
            font-size: 1.5rem;
            padding: 0 1rem;
            color: #ffffff;
            will-change: transform;
        }
        .modal {
            background-color: rgba(0, 0, 0, 0.7);
        }
        .progress-bar {
            background-color: #f6ad55;
            height: 8px;
            transition: width 0.1s ease-in-out;
        }
    </style>
</head>
<body class="p-4 flex flex-col items-center justify-center min-h-screen">

    <div class="max-w-3xl w-full">
        <h1 class="text-4xl font-bold text-center mb-6">Juego de Lector Rápido</h1>
        <p class="text-center text-lg mb-8">Selecciona una historia, ajusta la velocidad y pon a prueba tu capacidad de lectura.</p>

        <!-- Contenedor de selección de historias -->
        <div id="story-selector" class="flex flex-wrap justify-center gap-4 mb-8">
            <!-- Botones de historias se generarán aquí -->
        </div>

        <!-- Controles del juego -->
        <div class="flex items-center justify-center gap-4 mb-8">
            <button id="speed-down-btn" class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full shadow-lg transition-colors">- Velocidad</button>
            <div class="text-xl font-bold">Velocidad: <span id="speed-display">3</span></div>
            <button id="speed-up-btn" class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full shadow-lg transition-colors">+ Velocidad</button>
        </div>

        <!-- Botón de inicio/pausa -->
        <div class="text-center mb-8">
            <button id="start-pause-btn" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-3 px-8 rounded-full shadow-lg text-lg transition-colors">Empezar a Leer</button>
        </div>

        <!-- Área del teleprónter -->
        <div class="teleprompter-container rounded-lg shadow-2xl">
            <div class="reading-line"></div>
            <div id="story-text" class="story-text"></div>
        </div>

        <!-- Barra de progreso -->
        <div class="w-full mt-4 bg-gray-700 rounded-full">
            <div id="progress-bar" class="progress-bar rounded-full" style="width: 0%;"></div>
        </div>

        <!-- Modal para las preguntas y resultados -->
        <div id="modal" class="modal fixed inset-0 flex items-center justify-center p-4 hidden">
            <div class="bg-gray-800 p-8 rounded-lg shadow-xl max-w-xl w-full text-center">
                <h2 id="modal-title" class="text-3xl font-bold mb-4"></h2>
                <div id="modal-content" class="text-lg mb-6 text-gray-300"></div>
                <div id="quiz-options" class="flex flex-col gap-3"></div>
                <div id="results-display" class="hidden">
                    <p id="final-score" class="text-2xl font-bold mb-2"></p>
                    <p id="final-rating" class="text-3xl font-bold mb-6"></p>
                    <button id="close-modal-btn" class="bg-blue-500 hover:bg-blue-600 text-white font-bold py-2 px-6 rounded-full transition-colors">Cerrar</button>
                </div>
            </div>
        </div>

    </div>

    <script>
        // Historias del juego con sus preguntas de comprensión
        const stories = [
            {
                id: 1,
                title: "El Fantasma del Reloj",
                text: "En un antiguo campanario de un pueblo olvidado, vivía un relojero solitario llamado Silas. Cada medianoche, un espectro luminoso aparecía junto al péndulo, deteniendo el tiempo por unos segundos. Silas, lejos de temer, había hecho amistad con el fantasma, quien resultó ser el antiguo guardián del reloj, atrapado entre dimensiones. Un día, Silas encontró un engranaje perdido, el 'Engranaje de la Eternidad', que el fantasma necesitaba. Al colocarlo, la figura luminosa sonrió, el péndulo del reloj se detuvo y el fantasma desapareció, dejando atrás un brillo dorado. El reloj, en su lugar, empezó a funcionar con una melodía que nadie había escuchado antes, más allá del tiempo.",
                questions: [
                    { q: "¿Quién era el relojero solitario?", options: ["Silas", "El guardián", "El espectro"], answer: "Silas" },
                    { q: "¿Qué apareció cada medianoche?", options: ["Un espectro luminoso", "Un nuevo engranaje", "Una sombra"], answer: "Un espectro luminoso" }
                ]
            },
            {
                id: 2,
                title: "La Ciudad de Cristal",
                text: "Debajo del mar, en una fosa abisal, se encontraba la Ciudad de Cristal, una metrópolis brillante habitada por seres de luz. Estos seres no hablaban, se comunicaban a través de patrones de colores que brillaban en sus cuerpos translúcidos. Un joven explorador, llamado Elara, descubrió un mapa antiguo en una botella. Este mapa mostraba un camino hacia la superficie, un lugar que ellos creían una leyenda. Con valentía, Elara siguió el mapa, navegando por túneles de lava y arrecifes de coral bioluminiscentes. Al llegar a la superficie, la luz del sol la llenó, y se dio cuenta de que la verdadera aventura no era el destino, sino el viaje y la valentía de explorar lo desconocido.",
                questions: [
                    { q: "¿Dónde se encontraba la Ciudad de Cristal?", options: ["En la superficie", "En una fosa abisal", "En el cielo"], answer: "En una fosa abisal" },
                    { q: "¿Cómo se comunicaban los seres de luz?", options: ["Con sonidos", "Con patrones de colores", "Con telepatía"], answer: "Con patrones de colores" }
                ]
            },
            {
                id: 3,
                title: "El Códice Olvidado",
                text: "En la Biblioteca de Alejandría, una joven bibliotecaria llamada Maya encontró un libro sin nombre. Sus páginas estaban en blanco. Maya, fascinada, dedicó su tiempo a descifrarlo. Un día, al exponer el libro a la luz de la luna llena, las páginas se llenaron de un texto brillante y antiguo. El códice narraba la historia de un viajero del tiempo que había visitado Alejandría y ocultado conocimientos del futuro en sus páginas. Cada historia era una clave, y si Maya las descifraba, podría encontrar la forma de viajar a través de las eras. El libro se cerró, volviendo a su estado en blanco, pero la primera clave de la historia permanecía en la mente de Maya, un desafío que la invitaba a la aventura.",
                questions: [
                    { q: "¿Quién encontró el códice olvidado?", options: ["Un historiador", "Maya", "El viajero del tiempo"], answer: "Maya" },
                    { q: "¿Cuándo se llenaron las páginas de texto?", options: ["Cuando llovió", "A la luz de la luna llena", "Al amanecer"], answer: "A la luz de la luna llena" }
                ]
            },
            {
                id: 4,
                title: "La Nebulosa Cantante",
                text: "El astronauta Kael orbitaba un planeta gaseoso cuando su nave se averió. Solo y sin esperanza, activó la radio de emergencia. En lugar de estática, escuchó una melodía celestial proveniente de una nebulosa cercana, conocida como la Nebulosa Cantante. La música no era solo sonido, eran vibraciones que reparaban las fallas de su nave. Kael, al ajustar el receptor de radio, descubrió que podía comunicarse con la nebulosa, que le enseñaba el lenguaje de las estrellas. La nebulosa era un ser vivo, una vasta inteligencia cósmica que existía en forma de gas y polvo estelar, y que compartía su conocimiento con aquellos que se perdían en el vacío. La música fue la clave de Kael para el regreso a casa.",
                questions: [
                    { q: "¿Qué le sucedió al astronauta Kael?", options: ["Su nave se averió", "Encontró un planeta", "Conoció un extraterrestre"], answer: "Su nave se averió" },
                    { q: "¿Qué hizo la Nebulosa Cantante?", options: ["Emitió estática", "Reparó la nave", "Enseñó un lenguaje"], answer: "Reparó la nave" }
                ]
            },
            {
                id: 5,
                title: "La Lanza de Luz",
                text: "En las profundidades de la jungla amazónica, la joven arqueóloga Elara buscaba la legendaria Lanza de Luz, un artefacto de una civilización perdida. Las leyendas decían que la lanza podía controlar el clima. Elara, siguiendo antiguos mapas, llegó a un templo oculto, donde la lanza descansaba. Pero al tomarla, la lanza no le dio poder. En cambio, le mostró visiones de la jungla herida por la deforestación. Elara entendió que el verdadero poder de la lanza era el de un guardián de la naturaleza, y que su deber no era controlar el clima, sino inspirar a otros a cuidar del planeta. Así, Elara decidió dedicar su vida a la conservación, usando su conocimiento para proteger la jungla.",
                questions: [
                    { q: "¿Qué buscaba la arqueóloga Elara?", options: ["Un templo oculto", "La Lanza de Luz", "Un mapa antiguo"], answer: "La Lanza de Luz" },
                    { q: "¿Qué le mostró la lanza al tomarla?", options: ["Un tesoro", "Una visión de la jungla herida", "Un nuevo camino"], answer: "Una visión de la jungla herida" }
                ]
            },
            {
                id: 6,
                title: "El Dragón del Viento",
                text: "En las altas montañas de un reino oriental, vivía un dragón de viento, un ser con escamas de jade y alas de seda, que controlaba el clima. El dragón nunca se dejaba ver, pero su presencia se sentía en las brisas y los huracanes. El joven príncipe, Kael, para salvar a su reino de una sequía, decidió buscar al dragón. Con valor, escaló la montaña más alta. Allí, en la cima, encontró un huevo de jade. Kael entendió que el dragón no era un ser poderoso, sino un guardián de la naturaleza que necesitaba ser protegido. Al tomar el huevo, una brisa suave sopló, y la lluvia comenzó a caer sobre el reino. La sequía terminó, y Kael se convirtió en el protector del huevo de jade, asegurando el futuro de su reino.",
                questions: [
                    { q: "¿Dónde vivía el dragón?", options: ["En el desierto", "En las altas montañas", "En un volcán"], answer: "En las altas montañas" },
                    { q: "¿Qué buscaba el príncipe Kael?", options: ["A su reino", "Un dragón", "Una sequía"], answer: "Un dragón" }
                ]
            },
            {
                id: 7,
                title: "El Viajero del Tiempo",
                text: "El profesor Léo inventó una máquina del tiempo con la única intención de visitar el pasado para aprender historia de primera mano. Al activarla, un error de cálculo lo envió al futuro lejano. En un mundo donde la tecnología había evolucionado de forma extraña, con ciudades flotantes y robots que actuaban como sirvientes, Léo se encontró con su bisnieto, un joven historiador llamado Elara. Elara, al escuchar su historia, lo ayudó a reparar la máquina del tiempo, pero no sin antes mostrarle el futuro. El profesor Léo se dio cuenta de que la historia no era solo el pasado, sino el futuro, y que la humanidad siempre se movería hacia adelante. Al regresar a su tiempo, Léo se dedicó a enseñar historia, pero con una nueva perspectiva, una que incluía el futuro.",
                questions: [
                    { q: "¿Qué inventó el profesor Léo?", options: ["Una máquina del tiempo", "Un robot", "Un coche volador"], answer: "Una máquina del tiempo" },
                    { q: "¿A dónde lo envió el error de cálculo?", options: ["Al pasado", "Al presente", "Al futuro lejano"], answer: "Al futuro lejano" }
                ]
            }
        ];

        // Elementos del DOM
        const storyTextDiv = document.getElementById('story-text');
        const storySelectorDiv = document.getElementById('story-selector');
        const startPauseBtn = document.getElementById('start-pause-btn');
        const speedUpBtn = document.getElementById('speed-up-btn');
        const speedDownBtn = document.getElementById('speed-down-btn');
        const speedDisplay = document.getElementById('speed-display');
        const modal = document.getElementById('modal');
        const modalTitle = document.getElementById('modal-title');
        const modalContent = document.getElementById('modal-content');
        const quizOptionsDiv = document.getElementById('quiz-options');
        const resultsDisplayDiv = document.getElementById('results-display');
        const finalScoreP = document.getElementById('final-score');
        const finalRatingP = document.getElementById('final-rating');
        const closeBtn = document.getElementById('close-modal-btn');
        const progressBar = document.getElementById('progress-bar');

        // Estado del juego
        let currentStoryId = 1;
        let animationFrameId = null;
        let gameSpeed = 3; // Velocidad inicial (se traduce a píxeles por segundo)
        let isRunning = false;
        let lastTimestamp = 0;
        let totalScrollDistance = 0;
        let storyHeight = 0;
        let timeReading = 0;

        // Generar botones para seleccionar historias
        function createStoryButtons() {
            stories.forEach(story => {
                const button = document.createElement('button');
                button.textContent = `Historia ${story.id}: ${story.title}`;
                button.className = 'story-btn bg-gray-600 hover:bg-gray-700 text-white font-bold py-2 px-4 rounded-full transition-colors';
                button.onclick = () => selectStory(story.id);
                storySelectorDiv.appendChild(button);
            });
            // Seleccionar la primera historia por defecto
            selectStory(currentStoryId);
        }

        // Cargar una historia seleccionada
        function selectStory(id) {
            currentStoryId = id;
            const story = stories.find(s => s.id === id);
            storyTextDiv.textContent = story.text;
            resetGame();
            updateStoryButtonState();
        }

        // Actualizar el estado visual de los botones de historia
        function updateStoryButtonState() {
            document.querySelectorAll('.story-btn').forEach(btn => {
                btn.classList.remove('bg-gray-400', 'hover:bg-gray-500');
                btn.classList.add('bg-gray-600', 'hover:bg-gray-700');
            });
            const selectedBtn = document.querySelector(`.story-btn:nth-child(${currentStoryId})`);
            if (selectedBtn) {
                selectedBtn.classList.remove('bg-gray-600', 'hover:bg-gray-700');
                selectedBtn.classList.add('bg-gray-400', 'hover:bg-gray-500');
            }
        }

        // Función principal del bucle de animación
        function animate(timestamp) {
            if (!isRunning) return;

            if (lastTimestamp === 0) lastTimestamp = timestamp;
            const deltaTime = (timestamp - lastTimestamp) / 1000; // Delta en segundos
            lastTimestamp = timestamp;

            const speedInPixelsPerSecond = gameSpeed * 10;
            const movement = speedInPixelsPerSecond * deltaTime;

            totalScrollDistance += movement;
            timeReading += deltaTime;

            const remainingDistance = storyHeight - totalScrollDistance;
            const progress = (totalScrollDistance / storyHeight) * 100;
            progressBar.style.width = `${Math.min(progress, 100)}%`;

            if (remainingDistance > -storyTextDiv.offsetHeight) {
                storyTextDiv.style.transform = `translateY(-${totalScrollDistance}px)`;
                animationFrameId = requestAnimationFrame(animate);
            } else {
                endGame();
            }
        }

        // Iniciar el juego
        function startGame() {
            if (isRunning) return;
            isRunning = true;
            startPauseBtn.textContent = "Pausa";
            storyHeight = storyTextDiv.offsetHeight + 300; // Altura total de la historia para que se desplace fuera de vista
            
            // Ocultar modal si está visible
            modal.classList.add('hidden');

            animationFrameId = requestAnimationFrame(animate);
        }

        // Pausar el juego
        function pauseGame() {
            isRunning = false;
            startPauseBtn.textContent = "Continuar";
            cancelAnimationFrame(animationFrameId);
        }

        // Reiniciar el juego
        function resetGame() {
            isRunning = false;
            cancelAnimationFrame(animationFrameId);
            startPauseBtn.textContent = "Empezar a Leer";
            storyTextDiv.style.transform = `translateY(0)`;
            totalScrollDistance = 0;
            lastTimestamp = 0;
            timeReading = 0;
            progressBar.style.width = `0%`;
        }

        // Finalizar el juego y mostrar preguntas
        function endGame() {
            pauseGame();
            
            const story = stories.find(s => s.id === currentStoryId);
            modalTitle.textContent = "¡Historia Terminada!";
            modalContent.textContent = "Responde las siguientes preguntas para obtener tu puntuación:";
            quizOptionsDiv.innerHTML = '';
            resultsDisplayDiv.classList.add('hidden');
            modal.classList.remove('hidden');

            story.questions.forEach((q, index) => {
                const questionContainer = document.createElement('div');
                questionContainer.className = 'mb-4 text-left';
                const questionTitle = document.createElement('p');
                questionTitle.className = 'font-bold mb-2';
                questionTitle.textContent = `${index + 1}. ${q.q}`;
                questionContainer.appendChild(questionTitle);

                q.options.forEach(option => {
                    const button = document.createElement('button');
                    button.textContent = option;
                    button.className = 'w-full text-left bg-gray-700 hover:bg-gray-600 text-white py-2 px-4 rounded-md mb-2 transition-colors';
                    button.onclick = () => checkAnswer(q.answer, option, story.questions.length);
                    questionContainer.appendChild(button);
                });
                quizOptionsDiv.appendChild(questionContainer);
            });
        }

        // Verificar respuestas y calcular la puntuación
        let correctAnswers = 0;
        function checkAnswer(correctAnswer, userSelection, totalQuestions) {
            if (userSelection === correctAnswer) {
                correctAnswers++;
            }
            if (quizOptionsDiv.children.length === 1) { // Última pregunta respondida
                showResults(totalQuestions);
            }
            // Eliminar la pregunta actual después de responderla
            quizOptionsDiv.removeChild(quizOptionsDiv.children[0]);
        }

        // Mostrar los resultados finales
        function showResults(totalQuestions) {
            quizOptionsDiv.classList.add('hidden');
            resultsDisplayDiv.classList.remove('hidden');

            const story = stories.find(s => s.id === currentStoryId);
            const score = calculateScore(totalQuestions);
            finalScoreP.textContent = `Respuestas correctas: ${correctAnswers}/${totalQuestions}`;
            finalRatingP.textContent = `Tu calificación: ${getRating(score)}`;
            
            // Restablecer respuestas correctas para la siguiente partida
            correctAnswers = 0;
        }

        // Calcular la puntuación total
        function calculateScore(totalQuestions) {
            // Puntuación base por respuestas correctas
            let score = correctAnswers * 50;
            // Puntuación extra por velocidad
            const maxScoreFromSpeed = 50;
            const minTimeForMaxSpeed = 10; // Segundos para obtener la máxima puntuación
            const speedBonus = Math.max(0, maxScoreFromSpeed - (timeReading - minTimeForMaxSpeed));
            score += Math.min(speedBonus * gameSpeed, maxScoreFromSpeed);

            return score;
        }

        // Asignar una calificación basada en la puntuación
        function getRating(score) {
            if (score > 150) return "Maestro de la lectura";
            if (score > 100) return "Lector veloz";
            if (score > 50) return "Lector intermedio";
            return "Novato";
        }
        
        // Event Listeners
        startPauseBtn.addEventListener('click', () => {
            if (isRunning) {
                pauseGame();
            } else {
                startGame();
            }
        });

        speedUpBtn.addEventListener('click', () => {
            if (gameSpeed < 10) {
                gameSpeed++;
                speedDisplay.textContent = gameSpeed;
            }
        });

        speedDownBtn.addEventListener('click', () => {
            if (gameSpeed > 1) {
                gameSpeed--;
                speedDisplay.textContent = gameSpeed;
            }
        });

        closeBtn.addEventListener('click', () => {
            modal.classList.add('hidden');
            quizOptionsDiv.classList.remove('hidden');
        });

        // Inicializar el juego
        window.onload = function() {
            createStoryButtons();
        }
    </script>
</body>
</html>

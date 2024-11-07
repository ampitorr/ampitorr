// Variables globales
let scene, camera, renderer, player, controls;
let laberinto, timer, score, isGameOver = false;
const speed = 0.1; // Velocidad del jugador
const mazeSize = 10; // Tamaño del laberinto
const gameDuration = 60; // Duración en segundos

// Inicialización del juego
function initGame() {
  // Creación de la escena
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x87CEEB); // Fondo azul cielo

  // Cámara
  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  camera.position.set(0, 1.5, 5);

  // Renderizador
  renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  // Creación del laberinto
  createMaze();

  // Jugador
  const geometry = new THREE.BoxGeometry(0.5, 1.5, 0.5);
  const material = new THREE.MeshBasicMaterial({ color: 0xFF6347 });
  player = new THREE.Mesh(geometry, material);
  player.position.set(0, 0.75, 0);
  scene.add(player);

  // Control de movimiento
  controls = new THREE.PointerLockControls(camera, renderer.domElement);
  
  // Temporizador y puntajes
  score = 0;
  timer = gameDuration;

  // Evento de inicio de juego
  document.getElementById('start-button').style.display = 'none';
  startGame();
}

// Creación del laberinto (simple)
function createMaze() {
  laberinto = new THREE.Group();
  const wallMaterial = new THREE.MeshBasicMaterial({ color: 0x228B22 });

  // Creación de paredes del laberinto (por simplicidad)
  for (let x = -mazeSize; x < mazeSize; x++) {
    for (let z = -mazeSize; z < mazeSize; z++) {
      if ((x + z) % 2 === 0) { // Paredes alternas
        const wall = new THREE.Mesh(
          new THREE.BoxGeometry(1, 3, 1),
          wallMaterial
        );
        wall.position.set(x, 1.5, z);
        laberinto.add(wall);
      }
    }
  }

  scene.add(laberinto);
}

// Lógica de movimiento del jugador
function movePlayer() {
  if (isGameOver) return;

  // Movimiento del jugador (con las teclas WASD)
  const moveSpeed = speed;
  if (controls.isLocked) {
    if (keyState['w']) player.position.z -= moveSpeed;
    if (keyState['s']) player.position.z += moveSpeed;
    if (keyState['a']) player.position.x -= moveSpeed;
    if (keyState['d']) player.position.x += moveSpeed;
  }
}

// Lógica para actualizar el temporizador y el puntaje
function updateGame() {
  if (isGameOver) return;
  timer -= 0.1;
  if (timer <= 0) {
    isGameOver = true;
    alert('¡Se acabó el tiempo! Tu puntaje es: ' + score);
    window.location.reload(); // Recargar página para reiniciar el juego
  }
}

// Animación y renderizado del juego
function animate() {
  requestAnimationFrame(animate);
  movePlayer();
  updateGame();

  // Renderizar la escena
  renderer.render(scene, camera);
}

// Función de inicio de juego
function startGame() {
  controls.lock(); // Bloquear el cursor para controlarlo
  animate();
}

// Variables para controlar las teclas presionadas
const keyState = {};
document.addEventListener('keydown', (event) => {
  keyState[event.key.toLowerCase()] = true;
});
document.addEventListener('keyup', (event) => {
  keyState[event.key.toLowerCase()] = false;
});

// Iniciar el juego
initGame();

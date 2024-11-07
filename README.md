
let scene, camera, renderer, player, controls;
let laberinto, timer, score, isGameOver = false;
const speed = 0.1; // Velocidad del jugador
const mazeSize = 10; // Tamaño del laberinto
const gameDuration = 60; // Duración en segundos


function initGame() {
  // Creación de la escena
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x87CEEB); // Fondo azul cielo


  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  camera.position.set(0, 1.5, 5);


  renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);


  createMaze();


  const geometry = new THREE.BoxGeometry(0.5, 1.5, 0.5);
  const material = new THREE.MeshBasicMaterial({ color: 0xFF6347 });
  player = new THREE.Mesh(geometry, material);
  player.position.set(0, 0.75, 0);
  scene.add(player);


  controls = new THREE.PointerLockControls(camera, renderer.domElement);
  

  score = 0;
  timer = gameDuration;


  document.getElementById('start-button').style.display = 'none';
  startGame();
}


function createMaze() {
  laberinto = new THREE.Group();
  const wallMaterial = new THREE.MeshBasicMaterial({ color: 0x228B22 });

  
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


function movePlayer() {
  if (isGameOver) return;


  const moveSpeed = speed;
  if (controls.isLocked) {
    if (keyState['w']) player.position.z -= moveSpeed;
    if (keyState['s']) player.position.z += moveSpeed;
    if (keyState['a']) player.position.x -= moveSpeed;
    if (keyState['d']) player.position.x += moveSpeed;
  }
}


function updateGame() {
  if (isGameOver) return;
  timer -= 0.1;
  if (timer <= 0) {
    isGameOver = true;
    alert('¡Se acabó el tiempo! Tu puntaje es: ' + score);
    window.location.reload(); // Recargar página para reiniciar el juego
  }
}


function animate() {
  requestAnimationFrame(animate);
  movePlayer();
  updateGame();


  renderer.render(scene, camera);
}


function startGame() {
  controls.lock(); // Bloquear el cursor para controlarlo
  animate();
}


const keyState = {};
document.addEventListener('keydown', (event) => {
  keyState[event.key.toLowerCase()] = true;
});
document.addEventListener('keyup', (event) => {
  keyState[event.key.toLowerCase()] = false;
});


initGame();

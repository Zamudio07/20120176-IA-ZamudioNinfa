```
const w = 800, h = 400;
let jugador, fondo, bala, nave, salto, menu, velocidadBala, despBala;
let balaD = false, modoAuto = false, eCompleto = false, cuadrante, estatusAire, estatuSuelo;
let datosEntrenamiento = [], datosEntrenamiento2 = [], datosEntrenamiento3 = [];
let nnNetwork, nnEntrenamiento, nnSalida;
let nnNetwork2, nnEntrenamiento2, nnSalida2;
let nnNetwork3, nnEntrenamiento3, nnSalida3;
let statusOriginal = 0, statusRight = 0, nul = 1, nulx = 1, nuly = 1;
let statusOriginal2 = 0, statusRight2 = 0;
let velocidadBala2, despBala2, bala2, nave2;
let bala2D = false;
let velocidadBala3x, velocidadBala3y, despBala3, bala3, nave3;
let bala3D = false;
let derecha, enter, izquierda, abajo, dir = false;
let distancia_euclidiana, distancia_euclidiana2;

const juego = new Phaser.Game(w, h, Phaser.CANVAS, '', { preload, create, update, render });

function preload() {
  juego.load.image('fondo', 'assets/game/fondo.jpg');
  juego.load.spritesheet('mono', 'assets/sprites/altair.png', 32, 48);
  juego.load.image('nave', 'assets/game/ufo.png');
  juego.load.image('bala', 'assets/sprites/purple_ball.png');
  juego.load.image('menu', 'assets/game/menu.png');
  juego.load.image('nave2', 'assets/game/ufo.png');
  juego.load.image('bala2', 'assets/sprites/purple_ball.png');
  juego.load.image('nave3', 'assets/game/ufo.png');
  juego.load.image('bala3', 'assets/sprites/purple_ball.png');
}

function create() {
  juego.physics.startSystem(Phaser.Physics.ARCADE);
  juego.time.desiredFps = 30;

  fondo = juego.add.tileSprite(0, 0, w, h, 'fondo');
  bala = juego.add.sprite(w - 100, h, 'bala');
  jugador = juego.add.sprite(w / 2, h / 2, 'mono');

  juego.physics.enable(jugador);
  jugador.body.collideWorldBounds = true;
  const corre = jugador.animations.add('corre', [8, 9, 10, 11]);
  jugador.animations.play('corre', 10, true);

  juego.physics.enable(bala);
  bala.body.collideWorldBounds = true;
  bala.body.bounce.set(1);

  const pausaL = juego.add.text(w - 100, 20, 'Pausa', { font: '20px Arial', fill: '#fff' });
  pausaL.inputEnabled = true;
  pausaL.events.onInputUp.add(pausa, self);
  juego.input.onDown.add(mPausa, self);

  salto = juego.input.keyboard.addKey(Phaser.Keyboard.UP);
  derecha = juego.input.keyboard.addKey(Phaser.Keyboard.RIGHT);
  izquierda = juego.input.keyboard.addKey(Phaser.Keyboard.LEFT);
  abajo = juego.input.keyboard.addKey(Phaser.Keyboard.DOWN);
  enter = juego.input.keyboard.addKey(Phaser.Keyboard.ENTER);

  nnNetwork = new synaptic.Architect.Perceptron(2, 6, 6, 4);
  nnEntrenamiento = new synaptic.Trainer(nnNetwork);
}

function enRedNeural() {
  nnEntrenamiento.train(datosEntrenamiento, { rate: 0.0003, iterations: 10000, shuffle: true });
}

function datosDeEntrenamiento(param_entrada) {
  nnSalida = nnNetwork.activate(param_entrada);
  const aire = Math.round(nnSalida[0] * 100);
  const piso = Math.round(nnSalida[1] * 100);
  console.log('Entrada 1 ', param_entrada);
  console.log('Valor aire ' + aire + ' valor piso ' + piso);
  return nnSalida;
}

function pausa() {
  juego.paused = true;
  menu = juego.add.sprite(w / 2, h / 2, 'menu');
  menu.anchor.setTo(0.5, 0.5);
}

function mPausa(event) {
  if (juego.paused) {
    const menu_x1 = w / 2 - 135, menu_x2 = w / 2 + 135, menu_y1 = h / 2 - 90, menu_y2 = h / 2 + 90;
    const { x: mouse_x, y: mouse_y } = event;

    if (mouse_x > menu_x1 && mouse_x < menu_x2 && mouse_y > menu_y1 && mouse_y < menu_y2) {
      if (mouse_y <= menu_y1 + 90) {
        eCompleto = false;
        datosEntrenamiento = [];
        datosEntrenamiento2 = [];
        datosEntrenamiento3 = [];
        modoAuto = false;
      } else if (mouse_y <= menu_y2) {
        if (!eCompleto) {
          console.log('Entrenamiento ' + datosEntrenamiento.length + ' valores');
          enRedNeural();
          eCompleto = true;
          console.log('entrenado');
        }
        jugador.position.set(w / 2, h / 2);
        modoAuto = true;
      }

      menu.destroy();
      resetVariables();
      juego.paused = false;
    }
  }
}

function resetVariables() {
  jugador.body.velocity.set(0);
  bala.body.velocity.set(0);
  bala.position.x = w - 100;
  balaD = false;
}

function saltar() {
  if (!modoAuto || jugador.position.y > h / 4) jugador.position.y -= 15;
}

function bajar() {
  if (!modoAuto || jugador.position.y < h * 3 / 4) jugador.position.y += 15;
}

const avanzar = () => {
  if (!modoAuto || jugador.position.x < w * 4 / 5) jugador.position.x += 15;
};

const regresar = () => {
  if (!modoAuto || jugador.position.x > w / 4) jugador.position.x -= 15;
};

function update() {
  juego.physics.arcade.collide(bala, jugador, colisionH, null, this);
  const dx = Math.abs(bala.position.x - jugador.position.x);
  const dy = Math.abs(bala.position.y - jugador.position.y);
  distancia_euclidiana = Math.floor(Math.sqrt(dx * dx + dy * dy));

  const x = w / 2, y = h / 2;

  if (jugador.position.y < y && jugador.position.x > x) {
    cuadrante = 1;
    estatuSuelo = 0;
    estatusAire = 1;
    statusRight = 1;
    statusOriginal = 0;
  } else if (bala.position.y < y && bala.position.x < x) {
    cuadrante = 2;
    estatuSuelo = 0;
    estatusAire = 1;
    statusRight = 0;
    statusOriginal = 1;
  } else if (bala.position.y > y && bala.position.x > x) {
    cuadrante = 3;
    estatuSuelo = 1;
    estatusAire = 0;
    statusRight = 1;
    statusOriginal = 0;
  } else if (bala.position.y > y && bala.position.x < x) {
    cuadrante = 4;
    estatuSuelo = 1;
    estatusAire = 0;
    statusRight = 0;
    statusOriginal = 1;
  }

  if (!modoAuto) {
    if (salto.isDown) { saltar(); nul = 0; nuly = 0; }
    if (abajo.isDown) { bajar(); nul = 0; nuly = 0; }
    if (derecha.isDown) { avanzar(); nul = 0; nulx = 0; }
    if (izquierda.isDown) { regresar(); nul = 0; nulx = 0; }
    datosEntrenamiento.push({ input: [distancia_euclidiana, cuadrante], output: [estatusAire, estatuSuelo, statusOriginal, statusRight] });
  } else {
    if (nul === 0) {
      const [salidaAire, salidaSuelo, salidaIzq, salidaDer] = datosDeEntrenamiento([distancia_euclidiana, cuadrante]);
      if (nuly !== 1) (salidaAire >= salidaSuelo ? saltar : bajar)();
      if (nulx === 0) (salidaIzq >= salidaDer ? regresar : avanzar)();
    }
  }

  if (!balaD) disparo();
}

function disparo() {
  velocidadBala = velocidadRandom(400, 600);
  bala.body.velocity.set(velocidadBala, velocidadRandom(400, 600));
  balaD = true;
}

function colisionH() {
  pausa();
}

function velocidadRandom(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function render() {}
```
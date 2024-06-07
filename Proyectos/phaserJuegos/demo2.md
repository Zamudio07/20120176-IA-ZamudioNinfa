```
var w = 800, h = 400;
var jugador, fondo, bala, bala2, bala3;
var balaD = false, bala2D = false, bala3D = false;
var salto, derecha, izquierda, enter;
var menu, modoAuto = false, eCompleto = false;
var nnNetwork, nnNetwork2, nnNetwork3;
var nnEntrenamiento, nnEntrenamiento2, nnEntrenamiento3;
var datosEntrenamiento = [], datosEntrenamiento2 = [], datosEntrenamiento3 = [];
var nnSalida, nnSalida2, nnSalida3;

var juego = new Phaser.Game(w, h, Phaser.CANVAS, "", {
  preload: preload,
  create: create,
  update: update,
  render: render,
});

function preload() {
  juego.load.image("fondo", "assets/game/fondo.jpg");
  juego.load.spritesheet("mono", "assets/sprites/altair.png", 32, 48);
  juego.load.image("nave", "assets/game/ufo.png");
  juego.load.image("bala", "assets/sprites/purple_ball.png");
  juego.load.image("menu", "assets/game/menu.png");
}

function create() {
  juego.physics.startSystem(Phaser.Physics.ARCADE);
  juego.physics.arcade.gravity.y = 800;

  fondo = juego.add.tileSprite(0, 0, w, h, "fondo");
  nave = juego.add.sprite(w - 100, h - 70, "nave");
  bala = juego.add.sprite(w - 100, h, "bala");
  jugador = juego.add.sprite(15, h, "mono");

  juego.physics.enable([jugador, bala]);

  nave2 = juego.add.sprite(50, 30, "nave");
  bala2 = juego.add.sprite(75, 30, "bala");
  juego.physics.enable(bala2);

  nave3 = juego.add.sprite(w - 100, 30, "nave");
  bala3 = juego.add.sprite(w - 75, 30, "bala");
  juego.physics.enable(bala3);

  salto = juego.input.keyboard.addKey(Phaser.Keyboard.SPACEBAR);
  derecha = juego.input.keyboard.addKey(Phaser.Keyboard.RIGHT);
  izquierda = juego.input.keyboard.addKey(Phaser.Keyboard.LEFT);
  enter = juego.input.keyboard.addKey(Phaser.Keyboard.ENTER);

  nnNetwork = new synaptic.Architect.Perceptron(2, 6, 6, 2);
  nnEntrenamiento = new synaptic.Trainer(nnNetwork);

  nnNetwork2 = new synaptic.Architect.Perceptron(4, 6, 6, 6, 2);
  nnEntrenamiento2 = new synaptic.Trainer(nnNetwork2);

  nnNetwork3 = new synaptic.Architect.Perceptron(4, 6, 6, 6, 2);
  nnEntrenamiento3 = new synaptic.Trainer(nnNetwork3);
}

function enRedNeural() {
  nnEntrenamiento.train(datosEntrenamiento, { rate: 0.0003, iterations: 10000, shuffle: true });
  nnEntrenamiento2.train(datosEntrenamiento2, { rate: 0.0003, iterations: 10000, shuffle: true });
  nnEntrenamiento3.train(datosEntrenamiento3, { rate: 0.0003, iterations: 10000, shuffle: true });
}

function datosDeEntrenamiento(param_entrada) {
  nnSalida = nnNetwork.activate(param_entrada);
  return nnSalida[0] >= nnSalida[1];
}

function datosDeEntrenamiento2(param_entrada) {
  nnSalida2 = nnNetwork2.activate(param_entrada);
  return nnSalida2[0] > nnSalida2[1] ? true : nnSalida2[0] == nnSalida2[1] ? 0 : false;
}

function datosDeEntrenamiento3(param_entrada) {
  nnSalida3 = nnNetwork3.activate(param_entrada);
  return nnSalida3[0] >= nnSalida3[1];
}

function pausa() {
  juego.paused = true;
  menu = juego.add.sprite(w / 2, h / 2, "menu");
  menu.anchor.setTo(0.5, 0.5);
}

function mPausa(event) {
  if (juego.paused) {
    var menu_x1 = w / 2 - 270 / 2,
      menu_x2 = w / 2 + 270 / 2,
      menu_y1 = h / 2 - 180 / 2,
      menu_y2 = h / 2 + 180 / 2;
    var mouse_x = event.x, mouse_y = event.y;

    if (mouse_x > menu_x1 && mouse_x < menu_x2 && mouse_y > menu_y1 && mouse_y < menu_y2) {
      if (mouse_x >= menu_x1 && mouse_x <= menu_x2 && mouse_y >= menu_y1 && mouse_y <= menu_y1 + 90) {
        eCompleto = false;
        datosEntrenamiento = [];
        datosEntrenamiento2 = [];
        datosEntrenamiento3 = [];
        modoAuto = false;
      } else if (mouse_x >= menu_x1 && mouse_x <= menu_x2 && mouse_y >= menu_y1 + 90 && mouse_y <= menu_y2) {
        if (!eCompleto) {
          enRedNeural();
          eCompleto = true;
        }
        jugador.position.x = 15;
        modoAuto = true;
      }

      menu.destroy();
      resetVariables();
      resetVariables2();
      resetVariables3();
      juego.paused = false;
    }
  }
}

function resetVariables() {
  jugador.body.velocity.x = 0;
  jugador.body.velocity.y = 0;
  bala.body.velocity.x = 0;
  bala.position.x = w - 100;
  balaD = false;
}

function resetVariables2() {
  bala2.body.velocity.x = 0;
  bala2.position.y = 30;
  bala2D = false;
}

function resetVariables3() {
  bala3.body.velocity.x = 0;
  bala3.position.y = 30;
  bala3D = false;
}

function update() {
  if (!balaD) {
    balaD = true;
    bala.position.y = Math.random() * (600 - 300) + 300;
    bala.body.velocity.x = -Math.random() * (400 - 100) + 100;
  }
  if (balaD && bala.position.x < 0) resetVariables();

  if (!bala2D) {
    bala2D = true;
    bala2.position.y = Math.random() * (600 - 300) + 300;
    bala2.body.velocity.x = Math.random() * (400 - 100) + 100;
  }
  if (bala2D && bala2.position.x > w) resetVariables2();

  if (!bala3D) {
    bala3D = true;
    bala3.position.y = Math.random() * (400 - 100) + 100;
    bala3.body.velocity.x = -Math.random() * (400 - 100) + 100;
    bala3.body.velocity.y = -Math.random() * (400 - 100) + 100;
  }
  if (bala3D && (bala3.position.x < 0 || bala3.position.y < 0)) resetVariables3();

  var estado1 = juego.physics.arcade.collide(jugador, bala);
  var estado2 = juego.physics.arcade.collide(jugador, bala2);
  var estado3 = juego.physics.arcade.collide(jugador, bala3);

  if (estado1 || estado2 || estado3) {
    if (datosEntrenamiento.length > 500) datosEntrenamiento.splice(0, 100);
    if (datosEntrenamiento2.length > 500) datosEntrenamiento2.splice(0, 100);
    datosEntrenamiento.push({ input: [jugador.position.y / h, bala.position.y / h], output: estado1 ? [1, 0] : [0, 1] });
    datosEntrenamiento2.push({
      input: [jugador.position.x / w, jugador.position.y / h, bala2.position.x / w, bala2.position.y / h],
      output: estado2 ? [1, 0] : [0, 1],
    });
    datosEntrenamiento3.push({
      input: [jugador.position.x / w, jugador.position.y / h, bala3.position.x / w, bala3.position.y / h],
      output: estado3 ? [1, 0] : [0, 1],
    });
    resetVariables();
    resetVariables2();
    resetVariables3();
  }

  if (modoAuto) {
    var statusOriginal = datosDeEntrenamiento([jugador.position.y / h, bala.position.y / h]);
    var statusRight = datosDeEntrenamiento2([jugador.position.x / w, jugador.position.y / h, bala2.position.x / w, bala2.position.y / h]);
    var statusOriginal3 = datosDeEntrenamiento3([jugador.position.x / w, jugador.position.y / h, bala3.position.x / w, bala3.position.y / h]);

    if (statusOriginal) jugador.position.y -= 10;
    if (statusRight) jugador.position.x += 5;
    else if (statusRight == 0) jugador.position.x -= 5;
  } else {
    if (salto.isDown) jugador.position.y -= 10;
    if (derecha.isDown) jugador.position.x += 5;
    if (izquierda.isDown) jugador.position.x -= 5;
    if (enter.isDown) {
      jugador.position.x += 5;
      jugador.position.y -= 10;
    }
  }
}

function render() {
  if (modoAuto) juego.debug.text("Red Neuronal Automática", 20, 20);
  juego.debug.text("Datos Entrenamiento 1: " + datosEntrenamiento.length, 20, 50);
  juego.debug.text("Datos Entrenamiento 2: " + datosEntrenamiento2.length, 20, 80);
  juego.debug.text("Datos Entrenamiento 3: " + datosEntrenamiento3.length, 20, 110);
}
```
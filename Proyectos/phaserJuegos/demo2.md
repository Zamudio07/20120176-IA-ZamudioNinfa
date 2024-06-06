
var w = 800;
var h = 400;
var jugador;
var fondo;

var bala,
  balaD = false,
  nave;

var salto;
var menu;

var velocidadBala;
var despBala;

var estatusAire;
var estatuSuelo;

var nnNetwork,
  nnEntrenamiento,
  nnSalida,
  datosEntrenamiento = [];
var modoAuto = false,
  eCompleto = false;

//Nuevas variables
var nnNetwork2,
  nnEntrenamiento2,
  nnSalida2,
  datosEntrenamiento2 = [];

var nnNetwork3,
  nnEntrenamiento3,
  nnSalida3,
  datosEntrenamiento3 = [];

//! status
let statusOriginal = 0,
  statusRight = 0;
let statusOriginal2 = 0,
  statusRight2 = 0;
let velocidadBala2, despBala2, bala2, nave2;
let bala2D = false;

let velocidadBala3x, velocidadBala3y, despBala3, bala3, nave3;
let bala3D = false;

let derecha, enter, izquierda;
let dir = false;
let distancia_euclidiana, distancia_euclidiana2;

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

  //segunda bala
  juego.load.image("nave2", "assets/game/ufo.png");
  juego.load.image("bala2", "assets/sprites/purple_ball.png");

  //tercer bala
  juego.load.image("nave3", "assets/game/ufo.png");
  juego.load.image("bala3", "assets/sprites/purple_ball.png");
}

function create() {
  juego.physics.startSystem(Phaser.Physics.ARCADE);
  juego.physics.arcade.gravity.y = 800;
  // juego.physics.arcade.gravity.x = -800
  juego.time.desiredFps = 30;

  fondo = juego.add.tileSprite(0, 0, w, h, "fondo");
  nave = juego.add.sprite(w - 100, h - 70, "nave");
  bala = juego.add.sprite(w - 100, h, "bala");
  jugador = juego.add.sprite(15, h, "mono");

  juego.physics.enable(jugador);
  jugador.body.collideWorldBounds = true;
  var corre = jugador.animations.add("corre", [8, 9, 10, 11]);
  jugador.animations.play("corre", 10, true);

  juego.physics.enable(bala);
  bala.body.collideWorldBounds = true;

  //bala y nave 2
  nave2 = juego.add.sprite(50, 30, "nave2");
  bala2 = juego.add.sprite(75, 30, "bala2");
  juego.physics.enable(bala2);

  //bala y nave 3
  nave3 = juego.add.sprite(w - 100, 30, "nave3");
  bala3 = juego.add.sprite(w - 75, 30, "bala3");
  juego.physics.enable(bala3);

  //
  pausaL = juego.add.text(w - 100, 20, "Pausa", {
    font: "20px Arial",
    fill: "#fff",
  });
  pausaL.inputEnabled = true;
  pausaL.events.onInputUp.add(pausa, self);
  juego.input.onDown.add(mPausa, self);

  salto = juego.input.keyboard.addKey(Phaser.Keyboard.SPACEBAR);

  derecha = juego.input.keyboard.addKey(Phaser.Keyboard.RIGHT);
  izquierda = juego.input.keyboard.addKey(Phaser.Keyboard.LEFT);

  enter = juego.input.keyboard.addKey(Phaser.Keyboard.ENTER);

  //2 entradas, 2 salidas, saltar, no saltar
  nnNetwork = new synaptic.Architect.Perceptron(2, 6, 6, 2);
  nnEntrenamiento = new synaptic.Trainer(nnNetwork);

  //avanzar, no avanzar
  //nnNetwork2 = new synaptic.Architect.Perceptron(4, 6, 8, 6, 2);
  nnNetwork2 = new synaptic.Architect.Perceptron(4, 6, 6, 6, 2);
  nnEntrenamiento2 = new synaptic.Trainer(nnNetwork2);
}

function enRedNeural() {
  nnEntrenamiento.train(datosEntrenamiento, {
    rate: 0.0003,
    iterations: 10000,
    shuffle: true,
  });

}

const enRedNeuronal2 = () => {
  nnEntrenamiento2.train(datosEntrenamiento2, {
    rate: 0.0003,
    iterations: 10000,
    shuffle: true,
  });
};


function datosDeEntrenamiento(param_entrada) {
  console.log("Entrada 1 ", param_entrada);

  nnSalida = nnNetwork.activate(param_entrada);

  var aire = Math.round(nnSalida[0] * 100);
  var piso = Math.round(nnSalida[1] * 100);

  console.log("Valor aire " + aire + " valor piso " + piso);
  return nnSalida[0] >= nnSalida[1];
}

function datosDeEntrenamiento2(param_entrada) {
  console.log("Entrada 2 ", param_entrada);

  nnSalida2 = nnNetwork2.activate(param_entrada);

  var aire = Math.round(nnSalida2[0] * 100);
  var piso = Math.round(nnSalida2[1] * 100);

  console.log("Valor derecha " + aire + " valor izq " + piso);
  return nnSalida2[0] > nnSalida2[1]
    ? true
    : nnSalida2[0] == nnSalida2[1]
    ? 0
    : false;
}

function datosDeEntrenamiento3(param_entrada) {
  //  console.log("Entrada2", param_entrada[0] + " " + param_entrada[1]);

  nnSalida3 = nnNetwork3.activate(param_entrada);

  var aire = Math.round(nnSalida3[0] * 100);
  var piso = Math.round(nnSalida3[1] * 100);

  //  console.log("Valor derecha " + aire + " valor izq " + piso);
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

    var mouse_x = event.x,
      mouse_y = event.y;

    //volver a iniciar
    if (
      mouse_x > menu_x1 &&
      mouse_x < menu_x2 &&
      mouse_y > menu_y1 &&
      mouse_y < menu_y2
    ) {
      if (
        mouse_x >= menu_x1 &&
        mouse_x <= menu_x2 &&
        mouse_y >= menu_y1 &&
        mouse_y <= menu_y1 + 90
      ) {
        eCompleto = false;
        datosEntrenamiento = [];
        datosEntrenamiento2 = [];
        datosEntrenamiento3 = [];
        modoAuto = false;
      } else if (
        mouse_x >= menu_x1 &&
        mouse_x <= menu_x2 &&
        mouse_y >= menu_y1 + 90 &&
        mouse_y <= menu_y2
      ) {
        if (!eCompleto) {
          console.log(
            "Entrenamiento " + datosEntrenamiento.length + " valores"
          );
          enRedNeural();
          enRedNeuronal2();
          eCompleto = true;
          console.log("entrenado");
        }
        jugador.position.x = 15;
        modoAuto = true;
      }

      menu.destroy();
      resetVariables();
      resetVariables2();
      juego.paused = false;
    }
  }
}

function resetVariables() {
  jugador.body.velocity.x = 0;
  jugador.body.velocity.y = 0;

  bala.body.velocity.x = 0;
  bala.position.x = w - 100;

  // jugador.position.x = 50;
  balaD = false;
}

const resetVariables2 = () => {
  //bala2
  bala2.body.velocity.x = 0;
  bala2.position.y = 30;
  bala2D = false;
  //jugador.position.x = 50;
};

const resetVariables3 = () => {
  //bala3
  bala3.body.velocity.x = 0;
  bala3.body.velocity.y = 0;
  bala3.position.y = 30;
  bala3.position.x = w - 75;
  bala3D = false;
  //jugador.position.x = 50;
};

function saltar() {
  jugador.body.velocity.y = -270;
}

const avanzar = () => {
  statusOriginal = 0;
  statusRight = 1;

  if (jugador.position.x >= w / 3) return;
  jugador.position.x += 15;
  //jugador.body.velocity.x = 270
};

const regresar = () => {
  statusOriginal = 1;
  statusRight = 0;

  if (jugador.position.x < 15) return;
  jugador.position.x -= 15;
};

const avanzar2 = () => {
  if (jugador.position.x >= w / 3) return;
  jugador.position.x += 15;
  //jugador.body.velocity.x = 270
};

const regresar2 = () => {
  if (jugador.position.x < 15) return;
  jugador.position.x -= 15;
};

function update() {
  fondo.tilePosition.x -= 1;

  if (nave2.position.x >= w / 2) dir = true;
  else if (nave2.position.x <= 1) dir = false;

  if (dir) {
    nave2.position.x -= 1;
    bala2.position.x -= 1;
  } else {
    nave2.position.x += 1;
    bala2.position.x += 1;
  }

  juego.physics.arcade.collide(bala, jugador, colisionH, null, this);

  //collision con bala2
  juego.physics.arcade.collide(bala2, jugador, colisionH, null, this);

  //collision con bala3
  juego.physics.arcade.collide(bala3, jugador, colisionH, null, this);

  estatuSuelo = 1;
  estatusAire = 0;

  if (!jugador.body.onFloor()) {
    estatuSuelo = 0;
    estatusAire = 1;
  }

  despBala = Math.floor(jugador.position.x - bala.position.x);

  //desplazamiento de bala 2
  despBala2 = Math.floor(jugador.position.y - bala2.position.y);

  //? distancia eucli bala2
  distancia_euclidiana2 =
    Math.floor(
      Math.sqrt(
        Math.abs(Math.pow(bala2.position.x - jugador.position.x, 2)) -
          Math.abs(Math.pow(bala2.position.y - jugador.position.y, 2))
      )
    ) ||
    Math.floor(
      Math.sqrt(
        Math.abs(
          Math.abs(Math.pow(bala2.position.x - jugador.position.x, 2)) -
            Math.abs(Math.pow(bala2.position.y - jugador.position.y, 2))
        )
      )
    );

  //? distancia eucli bala3
  distancia_euclidiana =
    Math.floor(
      Math.sqrt(
        Math.abs(Math.pow(bala3.position.x - jugador.position.x, 2)) -
          Math.abs(Math.pow(bala3.position.y - jugador.position.y, 2))
      )
    ) ||
    Math.floor(
      Math.sqrt(
        Math.abs(
          Math.abs(Math.pow(bala3.position.x - jugador.position.x, 2)) -
            Math.abs(Math.pow(bala3.position.y - jugador.position.y, 2))
        )
      )
    );

  //saltar
  if (modoAuto == false && salto.isDown && jugador.body.onFloor()) {
    saltar();
  }

  //desplzar a derecha
  if (modoAuto == false && derecha.isDown && jugador.body.onFloor()) {
    avanzar();
  }

  //desplzar a izquierda
  if (modoAuto == false && izquierda.isDown && jugador.body.onFloor()) {
    regresar();
  }

  //! Modo auto, hay una bala y el jugador está en la superficie
  if (
    modoAuto == true &&
    (bala.position.x > 0 || bala2.position.y < h) &&
    jugador.body.onFloor()
  ) {

    //? saltar si el modelo lo indica
    if (datosDeEntrenamiento([despBala, velocidadBala])) {
      saltar();
    }

    //? desplazarse a la derecha si el modelo lo indica
    if (
      datosDeEntrenamiento2([
        distancia_euclidiana2,
        dir ? 1 : 0,
        distancia_euclidiana,
        velocidadBala3x,
      ]) != 0
    ) {
      if (
        datosDeEntrenamiento2([
          distancia_euclidiana2,
          dir ? 1 : 0,
          distancia_euclidiana,
          velocidadBala3x,
        ])
      )
        avanzar();
      else regresar();
    }

  }

  if (balaD == false) {
    disparo();
  }

  if (bala2D == false) {
    disparo2();
  }

  if (bala3D == false) {
    disparo3();
  }

  if (bala.position.x <= 0) {
    resetVariables();
  }
  if (bala2.position.y > h) {
    resetVariables2();
  }

  if (bala3.position.y > h || bala3.position.x <= 0) {
    resetVariables3();
  }

  if (
    modoAuto == false &&
    (bala.position.x > 0 ||
      bala2.position.y < h ||
      bala3.position.x > 0 ||
      bala3.position.y < h)
  ) {

    datosEntrenamiento.push({
      input: [despBala, velocidadBala],
      output: [estatusAire, estatuSuelo],
    });

    datosEntrenamiento2.push({
      input: [
        distancia_euclidiana2,
        dir ? 1 : 0,
        distancia_euclidiana,
        velocidadBala3x,
      ],
      output: [statusRight, statusOriginal],
    });

  }
}

function disparo() {
  velocidadBala = -1 * velocidadRandom(400, 600);
  bala.body.velocity.y = 0;
  bala.body.velocity.x = velocidadBala;
  balaD = true;
}

const disparo2 = () => {
  velocidadBala2 = velocidadRandom(5, 10);

  //bala2

  bala2.body.velocity.y = velocidadBala2;
  bala2.body.velocity.x = 0;
  bala2D = true;
};

const disparo3 = () => {
  velocidadBala3x = -1 * velocidadRandom(300, 800);
  velocidadBala3y = velocidadRandom(5, 10);

  //bala2

  bala3.body.velocity.y = velocidadBala3y;
  bala3.body.velocity.x = velocidadBala3x;
  bala3D = true;
};

function colisionH() {
  pausa();
}

function velocidadRandom(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function render() {}
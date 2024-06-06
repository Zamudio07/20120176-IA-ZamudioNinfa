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
var cuadrante;

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
  statusRight = 0,
  nul = 1,
  nulx = 1,
  nuly = 1;
let statusOriginal2 = 0,
  statusRight2 = 0;
let velocidadBala2, despBala2, bala2, nave2;
let bala2D = false;
let abajo;

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
  //juego.physics.arcade.gravity.y = 800;
  // juego.physics.arcade.gravity.x = -800
  juego.time.desiredFps = 30;

  fondo = juego.add.tileSprite(0, 0, w, h, "fondo");
  //nave = juego.add.sprite(w - 100, h - 70, "nave");
  bala = juego.add.sprite(w - 100, h, "bala");
  jugador = juego.add.sprite(w / 2, h / 2, "mono");

  juego.physics.enable(jugador);
  jugador.body.collideWorldBounds = true;
  var corre = jugador.animations.add("corre", [8, 9, 10, 11]);
  jugador.animations.play("corre", 10, true);

  juego.physics.enable(bala);
  bala.body.collideWorldBounds = true;
  bala.body.bounce.set(1); // Esto hará que la bala rebote en los bordes

  pausaL = juego.add.text(w - 100, 20, "Pausa", {
    font: "20px Arial",
    fill: "#fff",
  });
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
  nnEntrenamiento.train(datosEntrenamiento, {
    rate: 0.0003,
    iterations: 10000,
    shuffle: true,
  });
}

function datosDeEntrenamiento(param_entrada) {
  console.log("Entrada 1 ", param_entrada);

  nnSalida = nnNetwork.activate(param_entrada);

  var aire = Math.round(nnSalida[0] * 100);
  var piso = Math.round(nnSalida[1] * 100);

  console.log("Valor aire " + aire + " valor piso " + piso);
  return nnSalida;
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
          eCompleto = true;
          console.log("entrenado");
        }
        jugador.position.x = w / 2;
        jugador.position.y = h / 2;
        modoAuto = true;
      }

      menu.destroy();
      resetVariables();
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

function saltar() {
  if (modoAuto) if (jugador.position.y <= h / 4) return;
  jugador.position.y -= 15;
}

function bajar() {
  if (modoAuto) if (jugador.position.y >= h * (3 / 4)) return;
  jugador.position.y += 15;
}

const avanzar = () => {
  if (modoAuto) if (jugador.position.x >= w * (4 / 5)) return;
  if (jugador.position.x >= w) return;
  jugador.position.x += 15;
};

const regresar = () => {
  if (modoAuto) if (jugador.position.x <= w / 4) return;
  if (jugador.position.x < 15) return;
  jugador.position.x -= 15;
};

function update() {
  juego.physics.arcade.collide(bala, jugador, colisionH, null, this);

  distancia_euclidiana =
    Math.floor(
      Math.sqrt(
        Math.abs(Math.pow(bala.position.x - jugador.position.x, 2)) -
          Math.abs(Math.pow(bala.position.y - jugador.position.y, 2))
      )
    ) ||
    Math.floor(
      Math.sqrt(
        Math.abs(
          Math.abs(Math.pow(bala.position.x - jugador.position.x, 2)) -
            Math.abs(Math.pow(bala.position.y - jugador.position.y, 2))
        )
      )
    );

  let x = w / 2;
  let y = h / 2;

  if (jugador.position.y < y && jugador.position.x > x) {
    cuadrante = 1;
    estatuSuelo = 0;
    estatusAire = 1;
    statusRight = 1;
    statusOriginal = 0;
  }
  if (bala.position.y < y && bala.position.x < x) {
    cuadrante = 2;
    estatuSuelo = 0;
    estatusAire = 1;
    statusRight = 0;
    statusOriginal = 1;
  }
  if (bala.position.y > y && bala.position.x > x) {
    cuadrante = 3;
    estatuSuelo = 1;
    estatusAire = 0;
    statusRight = 1;
    statusOriginal = 0;
  }
  if (bala.position.y > y && bala.position.x < x) {
    cuadrante = 4;
    estatuSuelo = 1;
    estatusAire = 0;
    statusRight = 0;
    statusOriginal = 1;
  }

  if (modoAuto == false && salto.isDown) {
    saltar();
    nul = 0;
    nuly = 0;
  }

  if (modoAuto == false && abajo.isDown) {
    bajar();
    nul = 0;
    nuly = 0;
  }

  if (modoAuto == false && derecha.isDown) {
    avanzar();
    nul = 0;
    nulx = 0;
  }

  if (modoAuto == false && izquierda.isDown) {
    regresar();
    nul = 0;
    nulx = 0;
  }

  if (modoAuto) {
    if (nul == 0) {
      if (nuly != 1) {
        if (
          datosDeEntrenamiento([distancia_euclidiana, cuadrante])[0] >=
          datosDeEntrenamiento([distancia_euclidiana, cuadrante])[1]
        ) {
          saltar();
        } else {
          bajar();
        }
      }

      if (nulx == 0) {
        if (
          datosDeEntrenamiento([distancia_euclidiana, cuadrante])[2] >=
          datosDeEntrenamiento([distancia_euclidiana, cuadrante])[3]
        ) {
          regresar();
        } else {
          avanzar();
        }
      }
    }
  }

  if (balaD == false) {
    disparo();
  }

  if (modoAuto == false) {
    datosEntrenamiento.push({
      input: [distancia_euclidiana, cuadrante],
      output: [estatusAire, estatuSuelo, statusOriginal, statusRight],
    });
  }
}

function disparo() {
  velocidadBala = velocidadRandom(400, 600);
  bala.body.velocity.y = velocidadRandom(400, 600);
  bala.body.velocity.x = velocidadBala;
  balaD = true;
}

function colisionH() {
  pausa();
}

function velocidadRandom(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function render() {}
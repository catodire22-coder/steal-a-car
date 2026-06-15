
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Steal Car Duel Online</title>
<style>
body { margin:0; overflow:hidden; font-family:Arial; }
#ui {
  position:absolute; top:10px; left:10px;
  background:rgba(0,0,0,0.5); color:white;
  padding:10px; border-radius:8px;
}
button,input { margin:4px; }
</style>
</head>
<body>

<div id="ui">
  <button onclick="hostGame()">HOST</button>
  <input id="room" placeholder="ROOM CODE">
  <button onclick="joinGame()">JOIN</button>
  <div id="status">Not connected</div>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.min.js"></script>
<script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>

<script>
let scene,camera,renderer;
let car, vipCar;
let peers = {};
let peer, conn;
let isHost = false;
let roomCode = "";
let players = {};

const speed = 0.2;

init();
animate();

function init(){
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x222222);

  camera = new THREE.PerspectiveCamera(75,innerWidth/innerHeight,0.1,1000);
  camera.position.set(0,5,10);

  renderer = new THREE.WebGLRenderer();
  renderer.setSize(innerWidth,innerHeight);
  document.body.appendChild(renderer.domElement);

  // ground
  let g = new THREE.Mesh(
    new THREE.PlaneGeometry(100,100),
    new THREE.MeshBasicMaterial({color:0x555555})
  );
  g.rotation.x = -Math.PI/2;
  scene.add(g);

  // player car
  car = makeCar(0xff0000);
  car.position.set(0,1,5);
  scene.add(car);

  // VIP car
  vipCar = makeCar(0x00ff00);
  vipCar.position.set(0,1,0);
  scene.add(vipCar);

  window.addEventListener("keydown",keyDown);
}

function makeCar(color){
  let c = new THREE.Mesh(
    new THREE.BoxGeometry(1,1,2),
    new THREE.MeshBasicMaterial({color})
  );
  return c;
}

function keyDown(e){
  if(e.key==="w") car.position.z -= speed;
  if(e.key==="s") car.position.z += speed;
  if(e.key==="a") car.position.x -= speed;
  if(e.key==="d") car.position.x += speed;

  sync();
  checkSteal();
}

function checkSteal(){
  let d = car.position.distanceTo(vipCar.position);
  if(d < 2){
    vipCar.position.copy(car.position);
    vipCar.position.y = 2;
    car.hasVIP = true;
  }
}

function animate(){
  requestAnimationFrame(animate);
  camera.position.x = car.position.x;
  camera.position.z = car.position.z + 8;
  camera.lookAt(car.position);
  renderer.render(scene,camera);
}

// ---------------- MULTIPLAYER ----------------

function hostGame(){
  peer = new Peer();
  peer.on('open', id=>{
    roomCode = id;
    isHost = true;
    document.getElementById("status").innerText = "HOST: " + id;
  });

  peer.on('connection', c=>{
    conn = c;
    setupConn(c);
  });
}

function joinGame(){
  let id = document.getElementById("room").value;
  peer = new Peer();
  conn = peer.connect(id);
  setupConn(conn);
  document.getElementById("status").innerText = "Connected";
}

function setupConn(c){
  c.on('data', data=>{
    if(data.type==="state"){
      if(!players[data.id]){
        let p = makeCar(0x0000ff);
        scene.add(p);
        players[data.id] = p;
      }
      players[data.id].position.set(
        data.x,1,data.z
      );
    }
  });
}

function sync(){
  if(conn && conn.open){
    conn.send({
      type:"state",
      id:peer.id,
      x:car.position.x,
      z:car.position.z
    });
  }
}
</script>

</body>
</html>

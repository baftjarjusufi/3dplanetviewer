
//Setup:

//get the DOM element in which you want to attach the scene
const container = document.querySelector('#container');


//create a WebGL renderer
const renderer = new THREE.WebGLRenderer();

//set the renderer size to window inner width and height
const WIDTH = window.innerWidth;
const HEIGHT = window.innerHeight;

//set the renderer size to window inner width and height
renderer.setSize(WIDTH, HEIGHT);

//Adding a Camera

//set camera attributes
const VIEW_ANGLE = 45;
const ASPECT = WIDTH / HEIGHT;
const NEAR = 0.1;
const FAR = 10000;

//create a camera
const camera =
new THREE.PerspectiveCamera(
    VIEW_ANGLE,
    ASPECT,
    NEAR,
    FAR
);

//set the camera position - x, y, z
camera.position.set( 0, 0, 500 );

// Create a scene
const scene = new THREE.Scene();

// load background image
var backgroundImage1 = new THREE.TextureLoader().load('bnight.jpg');
//set the scene background
scene.background = backgroundImage1;

// load an image as the background
var backgroundImage = new THREE.TextureLoader().load('universenight.jpg');


//add the camera to the scene.
scene.add(camera);


// Attach the renderer to the DOM element.
container.appendChild(renderer.domElement);


//Three.js uses geometric meshes to create primitive 3D shapes like spheres, cubes, etc. I’ll be using a sphere.

// Set up the sphere attributes
const RADIUS = 200;
const SEGMENTS = 50;
const RINGS = 50;

//Create a group (which will later include our sphere and its texture meshed together)
const globe = new THREE.Group();
//add it to the scene
scene.add(globe);

//load texture and create sphere

var raycaster = new THREE.Raycaster();
var mouse = new THREE.Vector2();
var earthdaytexture , earthnighttexture;
var isNight = false;
var sphere;


// Instantiate a loader
var loader = new THREE.TextureLoader();
var earth_bump = new THREE.TextureLoader().load("bump.jpg");
// Load the earth day texture
loader.load('earth.jpg', function (texture) {
    earthdaytexture = texture;
    //create the sphere
    sphere = new THREE.Mesh(
        new THREE.SphereGeometry( RADIUS, SEGMENTS, RINGS ),
        new THREE.MeshPhongMaterial( { map: earthdaytexture, overdraw: 0.2 ,shininess: 10 ,
            bumpScale: 0.6
        
        } )
    );
    //add sphere to globe group
    globe.add(sphere);
});

// Load the earth night texture
loader.load('earthnight.jpg', function (texture) {
    earthnighttexture = texture;
});

// create cloud spheres 
var cloud_texture = new THREE.TextureLoader().load('cloud.png');
    var cloud_geometry = new THREE.SphereGeometry(207, 51, 50);
    var cloud_material = new THREE.MeshPhongMaterial({ overdraw: 0.2,
        shininess: 10,
        map: cloud_texture,
        transparent: true,
        opacity: 0.3
    });
    cloud = new THREE.Mesh(cloud_geometry, cloud_material);
    //globe.add(cloud);


    var cloud_texture1 = new THREE.TextureLoader().load('cloud.png');
    var cloud_geometry1 = new THREE.SphereGeometry(207, 51, 50);
    var cloud_material1 = new THREE.MeshPhongMaterial({ overdraw: 0.9,
        shininess: 5,
        map: cloud_texture,
        transparent: true,
        opacity: 0.2
    });
    cloud1 = new THREE.Mesh(cloud_geometry1, cloud_material1);
    //globe.add(cloud);


// Set up the event listener for double click
container.addEventListener('dblclick', onDoubleClick, false);

function onDoubleClick(event) {
    // Calculate mouse position in normalized device coordinates
    // (-1 to +1) for both components
    mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
    mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

    // update the picking ray with the camera and mouse position
    raycaster.setFromCamera( mouse, camera );

    // calculate objects intersecting the picking ray
    var intersects = raycaster.intersectObjects( [sphere] );

    // Check if the double click was on the sphere
    if (intersects.length > 0) {
        // Switch the texture of the sphere
        if (isNight) {
            sphere.material.map = earthdaytexture;
            isNight = false;
            scene.background = backgroundImage1;
            //globe.add(cloud);

        } else {
            sphere.material.map = earthnighttexture;
            isNight = true;
            scene.background = backgroundImage;
            globe.add(cloud1);

        }
    }
}


// Move the sphere back (z) so we can see it.
globe.position.z = -300;

//Lighting

//create a point light 
// set the position of the light
// add the light to the scene
pointLight = new THREE.PointLight(0xffffff);
pointLight.position.set(-600, 600, 600);
scene.add(pointLight);
// add the light to the scene
ambientLight = new THREE.AmbientLight(0x222222);
scene.add(ambientLight);




//Update

var controls = new THREE.OrbitControls(camera, renderer.domElement);

function animate() {
    requestAnimationFrame(animate);
  

    globe.rotation.y -= 0.0014;
    cloud.rotation.y += 0.0014;
    cloud1.rotation.y += 0.0014;
    
    controls.update();
    renderer.render(scene, camera);
}

function update () {
    controls.update();
    renderer.render(scene, camera);
    requestAnimationFrame(update);
    // Rotate the sphere
//globe.rotation.x += 0.001;
//globe.rotation.y += 0.001;


}

controls.enableZoom = true;
controls.enableRotate = true;
controls.enablePan = true;
controls.zoom =1000;
controls.minDistance = 200;
controls.maxDistance = 2000;
controls.zoomSpeed = 0.5;

controls.keys = {
    LEFT: 37, //left arrow
    UP: 38, // up arrow
    RIGHT: 39, // right arrow
    BOTTOM: 40 // bottom arrow
  }
  
  document.addEventListener('keydown', function(event) {
    switch (event.keyCode) {
        case 65: //A
            controls.update();
            camera.position.x -= 10;
            break;
        case 68: //D
            controls.update();
            camera.position.x += 10;
            break;
        case 87: //W
            controls.update();
            camera.position.z -= 10;
            break;
        case 83: //S
            controls.update();
            camera.position.z += 10;
            break;
    }
});

document.addEventListener("keydown", function(event) {
    if (event.code === "KeyR") {
      controls.reset();
    }
  });

  
  
requestAnimationFrame(update);
requestAnimationFrame(animate);


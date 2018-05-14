---
layout: page
title:  "Dancing with WEBGL"
date:   2018-05-11 16:16:01 -0600
categories: WEBGL
---

WebGL is a low-level Javascript API giving you access to the power of a computer's graphic hardware. Normally when rendering dynamic images in the browser, the solution was using Canvas. And with the complexity in certain dynamic images, the Canvas used a lot of CPU power. WebGL however uses the GPU for computationally expensive processes.

WebGL device support is growing fast. In addition to running on all major desktop browsers, WebGL runs on iOS and Android devices. 

WebGL allows the render of interactive 3D graphics without having to use plug-ins. It is supported in all major browsers including IE11 as well as mobile platforms.

WebGL is incredibly fast and is able to utilize hard ware acceleration. With performance in mind, WebGL is performs better than other options such as CSS, Canvas, SVG, and plug-in based choices (Flash is dead).

My favorite part about WebGL is its ability to utilize "shaders" to produce complex visual effects. It's implementation can simulate water or flames!

Some instances when you should use WebGL:
-Data visualization -> some practical examples include viewing 3D images of MRI scans or survey data.
-Games -> Obvious; Unity Engine has HTML5 (via WebGL) export.
-Interactive Web pages-> this allows users to explore your product from different angles

Why use WebGL?
-It's easy to use! Any script kiddie can get up and rolling in a matter of days!
-WebGL frameworks/libraries also help to ease the learning curve


<script src="https://threejs.org/build/three.js"></script>
<script src="https://threejs.org/examples/js/libs/dat.gui.min.js"></script>
<script src="https://threejs.org/examples/js/Detector.js"></script>

<div id="secondthree">
  <script id="vertexShader" type="x-shader/x-vertex">
  	precision highp float;
  	uniform float sineTime;
  	uniform mat4 modelViewMatrix;
  	uniform mat4 projectionMatrix;
  	attribute vec3 position;
  	attribute vec3 offset;
  	attribute vec4 color;
  	attribute vec4 orientationStart;
  	attribute vec4 orientationEnd;
  	varying vec3 vPosition;
  	varying vec4 vColor;
  	void main(){
  		vPosition = offset * max( abs( sineTime * 2.0 + 1.0 ), 0.5 ) + position;
  		vec4 orientation = normalize( mix( orientationStart, orientationEnd, sineTime ) );
  		vec3 vcV = cross( orientation.xyz, vPosition );
  		vPosition = vcV * ( 2.0 * orientation.w ) + ( cross( orientation.xyz, vcV ) * 2.0 + vPosition );
  		vColor = color;
  		gl_Position = projectionMatrix * modelViewMatrix * vec4( vPosition, 1.0 );
  	}
  </script>

  <script id="fragmentShader" type="x-shader/x-fragment">
  	precision highp float;
  	uniform float time;
  	varying vec3 vPosition;
  	varying vec4 vColor;
  	void main() {
  		vec4 color = vec4( vColor );
  		color.r += sin( vPosition.x * 10.0 + time ) * 0.5;
  		gl_FragColor = color;
  	}
  </script>

  <script>
  	if ( !Detector.webgl ) Detector.addGetWebGLMessage();
  	var container;
  	var camera, scene, renderer;
  	init();
  	animate();
  	function init() {
  		container = document.getElementById( 'secondthree' );
  		camera = new THREE.PerspectiveCamera( 50, window.innerWidth / window.innerHeight, 1, 10 );
  		camera.position.z = 2;
  		scene = new THREE.Scene();
  		// geometry
  		var vector = new THREE.Vector4();
  		var triangles = 1;
  		var instances = 50000;
  		var positions = [];
  		var offsets = [];
  		var colors = [];
  		var orientationsStart = [];
  		var orientationsEnd = [];
  		positions.push( 0.025, -0.025, 0 );
  		positions.push( -0.025, 0.025, 0 );
  		positions.push( 0, 0, 0.025 );
  		// instanced attributes
  		for ( var i = 0; i < instances; i ++ ) {
  			// offsets
  			offsets.push( Math.random() - 0.5, Math.random() - 0.5, Math.random() - 0.5 );
  			// colors
  			colors.push( Math.random(), Math.random(), Math.random(), Math.random() );
  			// orientation start
  			vector.set( Math.random() * 2 - 1, Math.random() * 2 - 1, Math.random() * 2 - 1, Math.random() * 2 - 1 );
  			vector.normalize();
  			orientationsStart.push( vector.x, vector.y, vector.z, vector.w );
  			// orientation end
  			vector.set( Math.random() * 2 - 1, Math.random() * 2 - 1, Math.random() * 2 - 1, Math.random() * 2 - 1 );
  			vector.normalize();
  			orientationsEnd.push( vector.x, vector.y, vector.z, vector.w );
  		}
  		var geometry = new THREE.InstancedBufferGeometry();

  		geometry.addAttribute( 'position', new THREE.Float32BufferAttribute( positions, 3 ) );
  		geometry.addAttribute( 'offset', new THREE.InstancedBufferAttribute( new Float32Array( offsets ), 3 ) );
  		geometry.addAttribute( 'color', new THREE.InstancedBufferAttribute( new Float32Array( colors ), 4 ) );
  		geometry.addAttribute( 'orientationStart', new THREE.InstancedBufferAttribute( new Float32Array( orientationsStart ), 4 ) );
  		geometry.addAttribute( 'orientationEnd', new THREE.InstancedBufferAttribute( new Float32Array( orientationsEnd ), 4 ) );
  		// material
  		var material = new THREE.RawShaderMaterial( {
  			uniforms: {
  				time: { value: 1.0 },
  				sineTime: { value: 1.0 }
  			},
  			vertexShader: document.getElementById( 'vertexShader' ).textContent,
  			fragmentShader: document.getElementById( 'fragmentShader' ).textContent,
  			side: THREE.DoubleSide,
  			transparent: true
  		} );
  		//
  		var mesh = new THREE.Mesh( geometry, material );
  		scene.add( mesh );
  		//
  		renderer = new THREE.WebGLRenderer();
  		renderer.setPixelRatio( window.devicePixelRatio );
  		renderer.setSize( window.innerWidth , window.innerHeight );
  		container.appendChild( renderer.domElement );
  		if ( renderer.extensions.get( 'ANGLE_instanced_arrays' ) === null ) {
  			document.getElementById( 'notSupported' ).style.display = '';
  			return;
  		}

  		window.addEventListener( 'resize', onWindowResize, false );
  	}
  	function onWindowResize( event ) {
  		camera.aspect = window.innerWidth / window.innerHeight;
  		camera.updateProjectionMatrix();
  		renderer.setSize( window.innerWidth , window.innerHeight);
  	}
  	//
  	function animate() {
  		requestAnimationFrame( animate );
  		render();
  	}
  	function render() {
  		var time = performance.now();
  		var object = scene.children[ 0 ];
  		object.rotation.y = time * 0.0005;
  		object.material.uniforms.time.value = time * 0.005;
  		object.material.uniforms.sineTime.value = Math.sin( object.material.uniforms.time.value * 0.05 );
  		renderer.render( scene, camera );
  	}
  </script>
<div>

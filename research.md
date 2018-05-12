---
layout: page
title: R&D
subtitle: Research & Development
sitemap:
  priority: 0.9
---

<script src="https://threejs.org/build/three.js"></script>
<script src="https://threejs.org/examples/js/libs/dat.gui.min.js"></script>
<script src="https://threejs.org/examples/js/Detector.js"></script>


<div id="firstthree">
  <script>
    var camera, scene, renderer;
    var geometry, group;
    var mouseX = 0, mouseY = 0;
    var windowHalfX = window.innerWidth / 2;
    var windowHalfY = window.innerHeight / 2;
    init();
    animate();
    function init() {
      camera = new THREE.PerspectiveCamera( 60, window.innerWidth / window.innerHeight, 1, 10000 );
      camera.position.z = 500;
      scene = new THREE.Scene();
      scene.background = new THREE.Color( 0xffffff );
      scene.fog = new THREE.Fog( 0xffffff, 1, 10000 );
      var geometry = new THREE.BoxBufferGeometry( 100, 100, 100 );
      var material = new THREE.MeshNormalMaterial();
      group = new THREE.Group();
      for ( var i = 0; i < 1000; i ++ ) {
        var mesh = new THREE.Mesh( geometry, material );
        mesh.position.x = Math.random() * 2000 - 1000;
        mesh.position.y = Math.random() * 2000 - 1000;
        mesh.position.z = Math.random() * 2000 - 1000;
        mesh.rotation.x = Math.random() * 2 * Math.PI;
        mesh.rotation.y = Math.random() * 2 * Math.PI;
        mesh.matrixAutoUpdate = false;
        mesh.updateMatrix();
        group.add( mesh );
      }
      scene.add( group );
      //
      renderer = new THREE.WebGLRenderer( { antialias: true } );
      renderer.setPixelRatio( window.devicePixelRatio );
      renderer.setSize( window.innerWidth, window.innerHeight );
      document.getElementById("firstthree").appendChild( renderer.domElement );
      //
      document.addEventListener( 'mousemove', onDocumentMouseMove, false );
      //
      window.addEventListener( 'resize', onWindowResize, false );
    }
    function onWindowResize() {
      windowHalfX = window.innerWidth / 2;
      windowHalfY = window.innerHeight / 2;
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize( window.innerWidth, window.innerHeight );
    }
    function onDocumentMouseMove( event ) {
      mouseX = ( event.clientX - windowHalfX ) * 10;
      mouseY = ( event.clientY - windowHalfY ) * 10;
    }
    //
    function animate() {
      requestAnimationFrame( animate );
      render();
    }
    function render() {
      var time = Date.now() * 0.001;
      var rx = Math.sin( time * 0.7 ) * 0.5,
        ry = Math.sin( time * 0.3 ) * 0.5,
        rz = Math.sin( time * 0.2 ) * 0.5;
      camera.position.x += ( mouseX - camera.position.x ) * 0.05;
      camera.position.y += ( - mouseY - camera.position.y ) * 0.05;
      camera.lookAt( scene.position );
      group.rotation.x = rx;
      group.rotation.y = ry;
      group.rotation.z = rz;
      renderer.render( scene, camera );
    }
  </script>
</div>

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
  		geometry.maxInstancedCount = instances; // set so its initalized for dat.GUI, will be set in first draw otherwise
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
  		renderer.setSize( window.innerWidth, window.innerHeight );
  		container.appendChild( renderer.domElement );
  		if ( renderer.extensions.get( 'ANGLE_instanced_arrays' ) === null ) {
  			document.getElementById( 'notSupported' ).style.display = '';
  			return;
  		}
  		//
  		var gui = new dat.GUI( { width: 350 } );
  		gui.add( geometry, 'maxInstancedCount', 0, instances );
  		//
  		window.addEventListener( 'resize', onWindowResize, false );
  	}
  	function onWindowResize( event ) {
  		camera.aspect = window.innerWidth / window.innerHeight;
  		camera.updateProjectionMatrix();
  		renderer.setSize( window.innerWidth, window.innerHeight );
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

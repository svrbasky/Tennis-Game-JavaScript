<html>
<b><center>Tennis Game</center></b><br>
<center>By Krishna Vijay Samayamantri</center><br>
<center>based on tutorial by Chris DeLeon</center><br>
	
# Tennis-Game-JavaScript

This is my first game written in HTML5 Javascript canvas by following the walkthrough course by Chris DeLeon on Udemy.com

You may access the course at https://www.udemy.com/course/code-your-first-game/


<div style="text-align:center">

<canvas id = "gameCanvas" width="800" height="600"></canvas>

<script>
	var canvas;
	var canvasContext;

	// ball parameters
	var ballX = 50;
	var ballY = 50;
	var ballSpeedX = 10;
	var ballSpeedY = 4;


	// scores
	var player1Score = 0;
	var player2Score = 0;
	const WINNING_SCORE = 5;

	var showingWinScreen = false;

	// paddle parameters
	var paddle1Y = 250;
	var paddle2Y = 250;
	const PADDLE_THICKNESS = 10;
	const PADDLE_HEIGHT = 100;

	// this function calculates mouse position
	function calculateMousePos(evt) {
		var rect = canvas.getBoundingClientRect();
		var root = document.documentElement;
		var mouseX = evt.clientX - rect.left - root.scrollLeft;
		var mouseY = evt.clientY - rect.top - root.scrollTop;
		return {
			x:mouseX,
			y:mouseY
			};
		}

	function handleMouseClick(evt) {
		if(showingWinScreen) {
			player1Score = 0;
			player2Score = 0;
			showingWinScreen = false;
		}
	}

	window.onload = function() {
		canvas = document.getElementById('gameCanvas');
		canvasContext = canvas.getContext('2d');
		canvasContext.font = "30px Comic Sans MS";

		var framesPerSecond = 30;
		setInterval(function() { // inline function
			moveEverything();
			drawEverything();
		},  1000/framesPerSecond); 

		canvas.addEventListener('mousedown', handleMouseClick);

		// this code below moves paddle with mouse
		canvas.addEventListener('mousemove',
			function(evt) { // inline function
				var mousePos = calculateMousePos(evt);
				paddle1Y = mousePos.y - (PADDLE_HEIGHT/2);
			});
	}

	// this function resets ball position when the paddle misses it.
	function ballReset() {
		if(player1Score >= WINNING_SCORE ||
		   player2Score	>= WINNING_SCORE) {
			showingWinScreen = true;
		}

		ballSpeedX = -ballSpeedX;
		ballX = canvas.width/2;
		ballY = canvas.height/2;

	}

	// this function determines the AI for paddle2 aka COMPUTER
	function computerMovement() {
		var paddle2YCenter = paddle2Y + (PADDLE_HEIGHT/2);
		if(paddle2YCenter < ballY-35) {
			paddle2Y += 6;
		} else if(paddle2YCenter > ballY +35){
			paddle2Y -= 6;
		}
	}

	// this function determines the physics for the movement of the ball and paddle
	function moveEverything() {
		if(showingWinScreen){
			return;
		}
		computerMovement();

		ballX += ballSpeedX;
		ballY += ballSpeedY;

		// reverse the ball trajectory when it hits the PLAYER paddle, reset it if paddle misses.
		if(ballX < 2*PADDLE_THICKNESS) {
			if(ballY > paddle1Y && 
			   ballY < paddle1Y+PADDLE_HEIGHT) {
				ballSpeedX = -ballSpeedX;

				var deltaY = ballY
					-(paddle1Y+PADDLE_HEIGHT/2);
				ballSpeedY = deltaY * 0.35;

			} else {
				player2Score++; // must be BEFORE ballReset()
				ballReset();

			}

		}

		// reverse the ball trajectory when it hits the COMPUTER paddle, reset it if paddle misses.
		if(ballX > (canvas.width - 2*PADDLE_THICKNESS)) {
			if(ballY > paddle2Y && 
			   ballY < paddle2Y+PADDLE_HEIGHT) {
				ballSpeedX = -ballSpeedX;
				var deltaY = ballY
					-(paddle2Y+PADDLE_HEIGHT/2);
				ballSpeedY = deltaY * 0.35;

			} else {
				player1Score++; // must be BEFORE ballReset()
				ballReset();

			}

		}

		if(ballY < 0) {
			ballSpeedY = -ballSpeedY;
		}
		if(ballY > canvas.height) {
			ballSpeedY = -ballSpeedY;
		}

	}

	// this function draws a Net
	function drawNet() {
		for(var i = 0; i<canvas.height; i+=40){
			colorRect(canvas.width/2 - 1, i, 2, 20, 'white');
		}
	}

	// this function decides the shape and color of the objects on the canvas (drawn in the next function)
	function drawEverything() {
		// next line blanks out the screen with black
		colorRect(0, 0, canvas.width, canvas.height, 'black');

		// Showing Win Screen
		if(showingWinScreen){
			canvasContext.fillStyle = 'green';
			if(player1Score >= WINNING_SCORE){
				canvasContext.fillStyle = 'green';
				canvasContext.fillText("YOU Won!", canvas.width/3, canvas.height/3);
			} else if(player2Score >= WINNING_SCORE){
				canvasContext.fillStyle = 'red';
				canvasContext.fillText("COMPUTER Won!", canvas.width/3, canvas.height/3);

			}

			canvasContext.fillStyle = 'white';
			canvasContext.fillText("Click to Continue", canvas.width/3, 5*canvas.height/6);
			return;
		}

		// this is NET in between
		drawNet();

		// this is left player paddle
		colorRect(PADDLE_THICKNESS, paddle1Y, PADDLE_THICKNESS , PADDLE_HEIGHT, 'white');

		// this is right computer paddle
		colorRect(canvas.width-(PADDLE_THICKNESS*2) , paddle2Y, PADDLE_THICKNESS , PADDLE_HEIGHT, 'white');

		// next line draws the ball
		colorCircle(ballX, ballY, 10, 'white');

		canvasContext.fillText(player1Score, 100, 100);
		canvasContext.fillText(player2Score, canvas.width-100, 100);
	}


	// this function draws and colors the BALL objects on the canvas
	function colorCircle(circleCenterX, circleCenterY, circleRadius, drawColor) {
		canvasContext.fillStyle = drawColor;
		canvasContext.beginPath();

		canvasContext.arc(circleCenterX, circleCenterY, circleRadius, 0, Math.PI*2, true)
		// .arc arguments = (circleCenterX, circleCenterY, circleRadius, startAngleArcRadian, endAngleArcRadian, ArcMeasureClockwise? true/false)
		canvasContext.fill();
	}


	// this function draws and colors the RECT objects on the canvas
	function colorRect(leftX, topY, width, height, drawColor) {
		canvasContext.fillStyle = drawColor;
		canvasContext.fillRect(leftX, topY, width, height);
	}

</script>
</div>

</html>

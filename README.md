# Simple Pong Game in python
	import simplegui, random									#imports to use graphics and random modules

	#initialize the global varriables
	canvas_width = 800											#keeps track of the canvas width
	canvas_height = 400											#keeps track of the canvas height

	red_score = 0												#keeps track of the red score
	blue_score = 0												#keeps track of the blue score
	gutter_hitter = "NONE"										#keeps track of who hits the gutter to erase their score									

	#varriables for the timer
	interval= 1000												#1000 milliseconds or 1 second
	count= 0													#sets the count for the timer to 0 when the code first starts

	#varriables for the paddles
	pad_width = canvas_width/40									#keeps track of the paddle width
	pad_height = canvas_height/5								#keeps track of the paddle height

	red_pad_center = [pad_width/2, canvas_height/2] #10, 200    #keeps track of the center of the red paddle
	blue_pad_center = [canvas_width - pad_width/2,canvas_height/2] #790, 200 #keeps track of the center of the blue paddle
	red_pad_vel = [0, 0]										#keeps track of the velocity of the red paddle
	blue_pad_vel = [0, 0]										#keeps track of the velocity of the blue paddle

	#varriables for the ball
	radius = 15													#keeps track of radius
	line_width = 2												#keeps track of line width

	ball_pos = [(canvas_width/2), (canvas_height/2)]			#keeps track of ball position
	ball_vel_x = 0												#keeps track of velocity for x
	ball_vel_y = 0												#keeps track of velocity for y
	ball_vel = [ball_vel_x, ball_vel_y]							#keeps track of velocity for x and y

	y_movement = "down"											#keeps track of which direction y is moving
	x_movement = "right"										#keeps track of which direction x is moving

	#helper functions
	def update_ball():											#updates the ball position
	    global ball_pos											#must be global to update in the draw handler
	    ball_pos[0] = ball_pos[0] + ball_vel[0]					#adds the velocity for the x-position
	    ball_pos[1] = ball_pos[1] + ball_vel[1]					#adds the velocity for the y-position
	    bounce()												#calls the function bounce

	def update_paddles():										#updates the paddles possition
	    global red_pad_center, blue_pad_center
	    red_pad_center[1] = red_pad_center[1] + red_pad_vel[1]
	    blue_pad_center[1] = blue_pad_center[1] + blue_pad_vel[1]
	    keep_paddle_on_canvas()

	def keep_paddle_on_canvas():								#keeps the paddle on canvas
	    global red_pad_vel, blue_pad_vel
	    #keeps the red paddle on the canvas
	    if red_pad_center[1] >= canvas_height - pad_height/2:
		red_pad_vel[1] = 0
	    if red_pad_center[1] <= pad_height/2:
		red_pad_vel[1] = 0
	    #keeps the blue paddle on the canvas
	    if blue_pad_center[1] >= canvas_height - pad_height/2:
		blue_pad_vel[1] = 0
	    if blue_pad_center[1] <= pad_height/2:
		blue_pad_vel[1] = 0

	def bounce():												#function that makes the ball bounce on the walls
	    global ball_pos, ball_vel, random, x_movement, y_movement, blue_score, red_score, gutter_hitter
	    #bounce for x
	    if ball_pos[0] >= (canvas_width - pad_width - radius/2):#if the ball hits the right edge it bounces
		if blue_pad_center[1] - 40 - radius/2 < ball_pos[1] < blue_pad_center[1] + 40 + radius/2:
		    ball_vel[0] = - ball_vel[0]
		    x_movement = "left"
		    print(x_movement)
		    blue_score = blue_score + 1
		    print("blue score: ", blue_score)
		else:
		    spawn_ball()									#restarts if it does not hit the blue paddle
		    gutter_hitter = "BLUE"							#sees who hits the gutter
	    if ball_pos[0] <= (pad_width + radius/2):				#if the ball hits the left edge it bounces
		if red_pad_center[1] - 40 - radius/2 < ball_pos[1] < red_pad_center[1] + 40 + radius/2:
		    ball_vel[0] = - ball_vel[0]
		    x_movement = "right"
		    print(x_movement)
		    red_score = red_score + 1
		    print("red score: ", red_score)
		else:
		    spawn_ball()									#restarts if it does not hit the red paddle
		    gutter_hitter = "RED"							#sees who hits the gutter
	    #bounce for y
	    if ball_pos[1] >= (canvas_height - radius/2):			#if the ball hits the bottom edge it bounces
		ball_vel[1] = - ball_vel[1]
		y_movement = "up"
		print(x_movement)
	    if ball_pos[1] <= (0 + radius/2):						#if the ball hits the top edge it bounces
		ball_vel[1] = - ball_vel[1]
		y_movement = "down"
		print(y_movement)

	def random_vel():											#creates random numbers for the velocity
	    global ball_vel											#must be global to update in the draw handler
	    if x_movement == "right":								#sees if the ball is moving right
		ball_vel_x = random.choice([1, 2, 3, 4, 5])			#if the ball is moving right it chooses a random postive x velocity
		if y_movement == "up":								#sees if the ball is moving up
		    ball_vel_y = -ball_vel_x						#if the ball if moving right and up it sets the y velocity to the negative version of the x velocity
		if y_movement == "down":							#sees if the ball is moving down
		    ball_vel_y = ball_vel_x							#if the ball if moving right and down it sets the y velocity equal to the x velocity
	    if x_movement == "left":								#sees if the ball is moving left
		ball_vel_x = random.choice([-1, -2, -3, -4, -5]	)	#if the ball is moving left it chooses a random postive x velocity
		if y_movement == "up":								#sees if the ball is moving up
		    ball_vel_y = ball_vel_x							#if the ball if moving left and down it sets the y velocity equal to the x velocity
		if y_movement == "down":							#sees if the ball is moving down
		    ball_vel_y = -ball_vel_x						#if the ball if moving left and down it sets the y velocity to the negative version of the x velocity
	    print("ball_vel_x: ", ball_vel_x)
	    print("ball_vel_y: ", ball_vel_y)
	    ball_vel = [ball_vel_x, ball_vel_y]						#sets the new velocity of the ball
	    print(ball_vel)

	def timer():
	    global count
	    count= count+1
	    time_left = count
	    print("vel chang in: ", time_left)
	    if count >= 3:											#changes the velocity after three seconds and calls start timer
		timer.stop()
		random_vel()
		start_timer()

	def start_timer():											#function that starts the timer
	    timer.start()

	def spawn_ball():
	    global ball_pos, ball_vel_x, ball_vel_y, ball_vel, red_pad_center, blue_pad_center, gutter_hitter, red_score, blue_score, x_movement, y_movement
	    print(" ")
	    print("SPAWN BALL") 
	    timer.start()

	    #chooses a random direction for the ball to spawn
	    direction = random.choice(["RED", "BLUE"])
	    print("Direction: ", direction)
	    if direction == "RED":									#if the direction choosen is red then it makes it have negative velocity
		ball_vel = [-2,-2]
		x_movement = "left"
		y_movement = "up"
	    else:
		ball_vel = [2,2]									#if the choosen direction if blue than it makes it have positive velocity
		x_movement = "right"
		y_movement = "down"
	    print("ball velocity: ", ball_vel)
	    print(x_movement, y_movement) 

	    #chooses a random position for the ball to spawn
	    ball_pos = [(random.randint(canvas_width/4, 3*canvas_width/4)), (random.randint(canvas_height/4, 3*canvas_height/4))]
	    print(ball_pos)

	    red_pad_center = [pad_width/2, canvas_height/2] #10, 200    #keeps track of the center of the red paddle
		blue_pad_center = [canvas_width - pad_width/2,canvas_height/2] #790, 200 #keeps track of the center of the blue paddle

	    if gutter_hitter == "RED":								#if the red player hits the gutter their score is set to 0	
		red_score = 0
	    elif gutter_hitter == "BLUE":							#if the blue player hits the gutter their score is set to 0	
		blue_score = 0
	    gutter_hitter = "NONE"									#resets to find who hits the gutter next

	#define handlers
	def start_handler():										#starts the ball when the user clicks the start button
	    print("-----------------------START GAME-------------------------------")
	    spawn_ball()

	def restart_handler():										#function that restarts the game when restart button is pressed
	    global ball_pos, ball_vel_x, ball_vel_y, ball_vel, red_pad_center, blue_pad_center
	    print(" ")
	    print("-----------------RESTART--------------------")
	    ball_pos = [(canvas_width/2), (canvas_height/2)]		#keeps track of ball position
	    ball_vel_x = 1											#keeps track of velocity for x
	    ball_vel_y = 1											#keeps track of velocity for y
	    ball_vel = [ball_vel_x, ball_vel_y]						#keeps track of velocity for x and y

	    y_movement = "down"										#keeps track of which direction y is moving
	    x_movement = "right"									#keeps track of which direction x is moving

	    red_pad_center = [pad_width/2, 							#keeps track of the center of the red paddle
			  canvas_height/2] #10, 200
	    blue_pad_center = [canvas_width - pad_width/2, 			#keeps track of the center of the blue paddle
			   canvas_height/2] #790, 200

	    blue_score = 0											#resets the blue score
	    red_score = 0											#resets the red score

	def keydown(key):											#function that keeps track if keys are pressed down
	    global red_pad_vel, blue_pad_vel
	    #moves red paddle up and down
	    if key == simplegui.KEY_MAP["s"] and red_pad_center[1] != canvas_height - pad_height/2:
		red_pad_vel[1] = 2
	    if key == simplegui.KEY_MAP["w"] and red_pad_center[1] != pad_height/2:
		red_pad_vel[1] = -2
	    #moves blue paddle up and down
	    if key == simplegui.KEY_MAP["down"] and blue_pad_center[1] != canvas_height - pad_height/2:
		blue_pad_vel[1] = 2
	    if key == simplegui.KEY_MAP["up"] and blue_pad_center[1] != pad_height/2:
		blue_pad_vel[1] = -2

	def keyup(key):												#function that keeps track if keys are not pressed down
	    global red_pad_vel, blue_pad_vel
	    #changes the velocity of the red paddle
	    if key == simplegui.KEY_MAP["s"]:						#if "s" is not being pressed down it changes the velocity to 0 to stop movement for the red paddle
		red_pad_vel[1] = 0
	    if key == simplegui.KEY_MAP["w"]:						#if "w" is not being pressed down it changes the velocity to 0 to stop movement for the red paddle
		red_pad_vel[1] = 0
	    #changes the velocity of the blue paddle
	    if key == simplegui.KEY_MAP["down"]:					#if "down" is not being pressed down it changes the velocity to 0 to stop movement for the blue paddle
		blue_pad_vel[1] = 0
	    if key == simplegui.KEY_MAP["up"]:						#if "up" is not being pressed down it changes the velocity to 0 to stop movement for the blue paddle
		blue_pad_vel[1] = 0

	def draw(canvas):
	    #updates the paddles
	    update_paddles()
	    #center line of the table
	    canvas.draw_line([canvas_width/2, 0], [canvas_width/2, canvas_height], 3, "white")
	    #red gutterline
	    canvas.draw_line([pad_width, 0], [pad_width, canvas_height], 3, "red")
	    #red paddle
	    canvas.draw_line([pad_width/2, red_pad_center[1] - pad_height/2], [pad_width/2, red_pad_center[1] + pad_height/2], pad_width, "red")
	    #blue gutter line
	    canvas.draw_line([canvas_width - pad_width, 0], [canvas_width - pad_width, canvas_height], 3, "blue")
	    #blue paddle
	    canvas.draw_line([canvas_width - pad_width/2, blue_pad_center[1] - pad_height/2], [canvas_width - pad_width/2, blue_pad_center[1] + pad_height/2], pad_width, "blue")
	    #updates the ball and draws it
	    update_ball()
	    canvas.draw_circle(ball_pos, radius, line_width, "yellow", "lime")
	    #shows the users' their scores
	    canvas.draw_text(("Score: " + str(red_score)), [canvas_width/4, 20], 20, "red")
	    canvas.draw_text(("Score: " + str(blue_score)), [3*canvas_width/4-50, 20], 20, "blue")

	#set frame
	frame = simplegui.create_frame('Pong Game Testing', canvas_width, canvas_height)
	frame.set_keydown_handler(keydown)
	frame.set_keyup_handler(keyup)

	#create and set handlers
	frame.set_draw_handler(draw)
	frame.add_button("START GAME", start_handler, 80)			#start game button
	frame.add_button("RESTART", restart_handler, 80)			#restart button

	timer = simplegui.create_timer(interval, timer)				#sets the interval for the timer

	#starts the frame
	frame.start()

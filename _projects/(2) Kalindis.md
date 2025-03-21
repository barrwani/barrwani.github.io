---
name: Kalindis
tools: [Godot, Git]
image: /assets/InstaEscape.png
description: An infinite-scroller mobile game starring a little blue jellyfish escaping the rising depths. Released on the Google Play Store. 
---

# Kalindis

In Kalindis, you play as a cheerful jellyfish trying to escape the sea floor! You only have access to one jump which replenishes once you collect a STAR. Collecting MOONS will freeze the floor in place for you! Jumping into a STICKY WALL gives you some breathing room, letting you reangle and time your next jump! Hitting a BARREL will replenish your jump but knock you back down! Climb as high as possible!

<center>
<video src="/assets/Kalindis Gameplay.mp4" width="640" height="360" controls></video>
</center>

| Platform | Engine | Duration | Team Size | Roles |
| --------  | --------  | --------  | --------  | -------- |
| Android | Godot | 6 months | 8 People | Game Programming & Design, Project Management |



## Responsibilities 

- Designed, prototyped, and implemented player movement/basic mechanics (death, jumping, collecting stars and moons), environment interactions (sticky walls), obstacles (barrels, death walls), and UI

- Calculated and displayed player trajectory with projectile motion calculations

- Designed and implemented a gradual difficulty scaling system 

- Implemented Finite State Machine (FSM) to handle player animations, VFX/SFX, and environment interaction logic

- Integrated assets from artists, sound engineers, and UI designers 

- Implemented fully functional leaderboard system

- Polished game feel by implementing screenshake, particle effects, and responsive controls


## Features

The main mechanic of the game was a sort of cross between Doodle Jump and Angry Birds. You would pull back a virtual joystick in the same way you'd pull back the Angry Birds slingshot, then on release you'd jump as if you released that slingshot and were launched in the opposite direction. The catch here was you could only jump once, and you'd need to renew your jump by picking up a star. 


Below is a demonstration from the prototyping stages:

<center>
<img src="/assets/mvmtprototype.gif" width="640" height="360">
</center>

Calculating the direction to go in and applying that direction to the players velocity vectors was simple enough:

```swift
func _on_joystickbutton_released():
	sticking = false
	if is_on_floor() or jumpvail and !dead:

        //varying jump sfx
		playerjump += str(rng.randi_range(1,5))
		get_node(playerjump).play()

        //emit bubble particles and reset sfx node selection
		$Bubbles.set_emitting(true)
		playerjump = "Playerjump"
        
        //apply velocity in the required direction set jump to not available
		velocity = move_and_slide(joystick.get_value() * speed)
		emit_signal("jumping")
		jumpvail = false

        //grey out player when jump is not available for visual clarity
		$AnimationPlayer.play("PostJump")

    //no longer holding down joystick, hide trajectory line
	holding = false
	line.hide()
```


```swift
func update_trajectory(delta):
	line.clear_points()
	var pos = $Position2D.position
	var vel  = (joystick.get_value() * speed)
	for i in max_points:
		line.add_point(pos)
        //apply gravity to potential velocity
		vel.y += 800 * delta 
		pos += vel * delta
```


The obstacles themselves were quite simple, with the sticky wall just halting the `apply_gravity()` method of the player when the player stuck to it and killed any momentum built while giving back the players jump. Once the player jumped off the wall it re-initiated gravity as normal. The moon just paused the wave that was constantly moving up at a steadily increasing rate, and the barrel moved back and forth using a Tween, giving the player back a jump if collision occured so they could recover or use it to their advantage if they hit it right.


What was most difficult to program was actually the item/obstacle spawner. Since the game was an infinite scroller I had to make it get more difficult somehow, so making sure that the difficult curve was reasonable and rewarding was important. 

<center>
<img src="/assets/threshold.gif" width="640" height="360">
</center>

I used a collision area which constantly moves upwards when the player touches it to tally scores and gradually increase difficulty. The area slowly gets further each time to make getting more points more and more difficult, but the player also gains speed as the game goes on to keep it feeling fast paced and avoid a soft lock. 


```swift
func _on_DuplicateThresh_body_entered(body):
	if body.is_in_group("player"):
		stickyrate +=1
		spawner()
		threshcounter += 1
		$LWall/CollisionShape2D.position.y -= 300
		$RWall/CollisionShape2D.position.y -= 300
		$LWall/backbar2.position.y -= 300
		$RWall/backbar3.position.y -= 300
		$CollisionShape2D.position.y -= 300
```


Here, everytime you gain a point you tally up some counters and call the environment spawner. Since the player dies when it touches the edges of the screen (walls), I also had to move them up constantly with the player.



```swift
func spawner():
	var rand = RandomNumberGenerator.new()
	//starspawn
	for i in range(3):
		scales -= 350
		if threshcounter % 10 == 0 and threshcounter != 0:
			scales -= 150
		var spawnstar = star.instance()
		rand.randomize()
		var x = rand.randi_range(-200,200)
		spawnstar.position.x = x
		var y =  scales
		spawnstar.position.y = y
		call_deferred("add_child", spawnstar)
	//moonspawn
	if stickyrate == 2:
		var spawnmoon = moon.instance()
		rand.randomize()
		var xm =  rand.randi_range(-200,200)
		spawnmoon.position.x = xm
		var ym = scales
		spawnmoon.position.y = ym
		call_deferred("add_child",spawnmoon)

	if stickyrate % 2 == 0:
	//stickywallspawn
		var stickywallspawn = stickywall.instance()
		var wx = wallpos[randi() % wallpos.size()]
		stickywallspawn.position.x = wx
		var wy = scales
		stickywallspawn.position.y = wy
		call_deferred("add_child",stickywallspawn)

	//movingplatspawn
	if (stickyrate % 2 != 0 && threshcounter >= 4) :
		var movingplatspawn = movingplat.instance()
		movingplatspawn.position.x = -200
		movingplatspawn.position.y = scales
		call_deferred("add_child", movingplatspawn)

	if stickyrate == 4:
		stickyrate = 0
```




The spawner's code is a bit long but its really just spawning all the different objects at different rates, so we don't get a bunch of sticky coral walls and barrels after every point. Every item has its own spawn boundaries with a slight element of random so it doesn't feel repetitive. The only thing I regret not implementing here is not increasing spawn rate of obstacles past a certain point to increase difficulty in really late stages. 

## What I learned

A lot of the criticism we received from this game revolved around UX-related issues, with the camera and the player controller being the main ones. The camera is always centered on the player rather than looking upwards, which makes it more difficult to anticipate where the next star would be, and the player joystick was always fixed on one place, rather than moving to wherever the player tapped. This restricted how the player could play and didn't make use of the screen space you get from touchscreen devices. We also opted for a text-based tutorial, an approach that made no sense in retrospect. 

Enough with the bad though, this was our first major project and my first where I really felt like I was a gameplay programmer rather than just a coder making jam games. I gained a lot of useful knowledge about game feel, scope, and polish which I otherwise wouldn't have learned. Although there were flaws to it, for our first project we did pretty good and I'm happy with what we managed to achieve.  


For more detailed and general thoughts on what we learned as a team from this project, have a look at the [post-mortem](https://barwani.eu.org/blog/kalindis-postmortem)


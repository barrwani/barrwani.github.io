---
name: 3C's Unreal Engine 5 Parkour Demo
tools: [Unreal, C++]
image: /assets/oldman.png
description: A simple C++ movement demo made for a scrapped project.
---

# Parkour Demo

 A simple C++ movement demo made for a scrapped project. The player can sprint, slide, cling to walls, and wall jump. The goal of the demo was to demonstrate fast paced, high-speed movement gameplay while still being readable and easy to control for a player.

 To achieve this, special considerations were made to wall detection, jump features, and interactions with the camera. 
 
 For example, wall jumping while facing the ground gives a burst of movement towards the ground, and wall jumping (which occurs at a set angle) will rotate the camera towards the players jump direction automatically. 
 
<center>
<video src="/assets/MOVEMENT.mp4" width="640" height="360" controls></video>
</center>

| Platform | Engine | Duration | Team Size | Roles |
| --------  | --------  | --------  | --------  | -------- |
| N/A | Unreal Engine | 2 days | 1 person | Game Programming & Design|



## Responsibilities 

- Designed, prototyped, and implemented the parkour movement in Unreal Engine 5 C++
- Tested implementations and made necessary adjustments to meet the goals of the gameplay


## Feature Implementation

### Slide

Using FSM-adjacent Start, Stop, and Update functions, the slide operates using a lerp for capsule height and an animation montage with root motion for movement and visuals:


```cpp
void AShacknewsCharacter::UpdateSlide(const float DeltaTime)
{
	
		// Increment progress over time
		fSlideProgress += DeltaTime / fSlideDuration;

		// Ensure progress doesn't exceed 1.0
		fSlideProgress = FMath::Clamp(fSlideProgress, 0.0f, 1.0f);
	
		FVector NewMeshLocation;

		// Shrink Capsule Quickly
		if(fSlideProgress <0.25f)
		{
			fCurrentCapsuleH = FMath::Lerp(fCapsuleHeight, fCapsuleHeight / 3, fSlideProgress*3);
		}
		// Resize after a pause
		else if(fSlideProgress > 0.5f)
		{
			fCurrentCapsuleH = FMath::Lerp(fCapsuleHeight / 3, fCapsuleHeight, fSlideProgress);
		}

		// Smoothly Adjust Mesh for shrunken capsule
		if(fSlideProgress < 0.5f)
		{
			NewMeshLocation = FMath::Lerp(vStartMeshLocation, vTargetMeshLocation, fSlideProgress*2);
			
		}else
		{
			NewMeshLocation = FMath::Lerp(vTargetMeshLocation,  vStartMeshLocation, fSlideProgress);
		}

		// Apply the new location to the mesh
		GetMesh()->SetRelativeLocation(NewMeshLocation);

		// Restore the capsule height after sliding
		GetCapsuleComponent()->SetCapsuleHalfHeight(fCurrentCapsuleH);
	
		// Stop sliding when the slide is done (SlideProgress reaches 1.0)
		if (fSlideProgress >= 1.0f)
		{
			bIsSliding = false;
		}
}
```


### Wall-stuff

#### Wall Jump

Launches the character in the direction they are facing relative to the wall. 

Takes into account whether the player is facing downwards or not to test for a downwards jump, and whether or not the player is facing forwards or backwards relative to the wall so they always jump in the desired direction.


```cpp
void AShacknewsCharacter::WallJump()
{
	FRotator CameraRotation = GetController()->GetControlRotation();
	// Get the forward vector of the camera
	FVector CameraForward = CameraRotation.Vector();
	FVector WallNormal = WallHit.ImpactNormal;
	WallNormal.Z = 0.f;
	CameraForward.Z = 0.f;

	// Calculate the cross product to determine the side of the wall the camera is facing
	FVector CrossProduct = FVector::CrossProduct(CameraForward, WallNormal);	

	// Check Z component of cross product to see if its left or right
	float YawDirectionMultiplier = (CrossProduct.Z > 0.f) ? 1.f : -1.f;

	//Apply yaw-rotation so character jumps away from wall
	FRotator Rotation = FRotator(GetActorRotation().Pitch, GetActorRotation().Yaw + (120.f * YawDirectionMultiplier), GetActorRotation().Roll);

	// Store the desired rotation target for smooth interpolation
	FRotator TargetRotation = FRotator(CameraRotation.Pitch, WallNormal.Rotation().Yaw - (50.f * YawDirectionMultiplier), CameraRotation.Roll);


	// Rotate player in direction of jump
	SetActorRotation(Rotation);

	// Calculate how much the camera is looking downward
	FVector CameraWorldForward = GetController()->GetControlRotation().Vector();
	float DotWithDown = FVector::DotProduct(CameraWorldForward, FVector(0.f, 0.f, -1.f)); // Dot product with downward vector (world Z)	
	// Launch character away from wall in correct direction
	FVector LaunchVelocity = (GetActorForwardVector() * 2000.f);
	
	// If looking downward, launch towards the ground
	if (DotWithDown > 0.5f)
	{
		LaunchVelocity.Z = DownwardsJumpVelocity; // Launch downwards
	}
	else
	{
		LaunchVelocity.Z += GetCharacterMovement()->JumpZVelocity * 1.1; // Regular upward jump
		
		// Set a member variable to store this rotation
		TargetCameraRotation = TargetRotation;
		bIsCameraRotating = true;
	}
	
	LaunchCharacter(LaunchVelocity, true, true);

	// Stop wall-running
	bIsWallHanging = false;
		
}
```

#### Wall Detection
To detect a wall, I used a set of wall traces in front of and to the left and right of the player:

```cpp
void AShacknewsCharacter::WallHang()
{
	// Define the start point as the actor's current location
	FVector Start = GetActorLocation();
    
	// Define the end point by extending the actor's forward vector (100 units in front of the actor)
	FVector FrontEnd = Start + GetActorForwardVector() * 50.f;
	FVector LeftEnd = Start - GetActorRightVector() * 50.f;
	FVector RightEnd = Start + GetActorRightVector() * 50.f;
	
	// Perform a line trace (raycast) to detect if there is a wall directly in front of/to the side of the character
	// The trace checks along the ECC_Visibility channel to detect visible walls
	
	if(GetWorld()->LineTraceSingleByChannel(WallHit, Start, LeftEnd, ECC_GameTraceChannel1))
	{
		WallCling();
	}
	else if(GetWorld()->LineTraceSingleByChannel(WallHit, Start, RightEnd, ECC_GameTraceChannel1))
	{
		WallCling();
	}
	else if(GetWorld()->LineTraceSingleByChannel(WallHit, Start, FrontEnd, ECC_GameTraceChannel1))
	{
		WallCling();

	}else
	{
		// if wall traces all fail we're probably not near enough to a wall
		bIsWallHanging = false;
	}
}
```

This made wall clinging, which was a core part of the movement, easy without requiring the player to face the wall. The line traces only being 100 units ensured that wall hanging was almost always intentional. 

The wall cling function being called above is simple, slowing down gravity by interpolating downwards velocity towards 0 and posing the player towards the wall.

Using a custom ECC_Trace channel we can handpick which walls we want to allow to be wall-clingable, allowing for more customisable level design.

#### Camera 
The camera rotation is simple yet effective, interpolating the direction the camera is facing to match that of the player. 


```cpp
void AShacknewsCharacter::RotateCameraOnJump(float DeltaTime)
{
		// Get the current camera rotation
		FRotator CurrentRotation = GetController()->GetControlRotation();

		// Smoothly interpolate to the target rotation over time
		FRotator NewRotation = FMath::RInterpTo(CurrentRotation, TargetCameraRotation, DeltaTime, CameraInterpSpeed); 

		// Set the new interpolated rotation
		GetController()->SetControlRotation(NewRotation);

		// Check if we've reached the target rotation (within a tolerance)
		if (NewRotation.Equals(TargetCameraRotation, 5.0f)) 
		{
			bIsCameraRotating = false; // Stop rotating once the target is reached
		}
}
```

Taking into account that the camera interpolation would continue even if the player moved their mouse, I added the following to the `Look()` function:
```cpp
	if (Controller != nullptr)
	{
		// cancel camera rotation when mouse moves
		bIsCameraRotating = false;
    ...
  }  
```


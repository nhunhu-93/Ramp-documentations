# ME08: Ramp & Airborne Implementation Code

---

## 1. State Enum: EVehicleAirborneState

```cpp
UENUM(BlueprintType)
enum class EVehicleAirborneState : uint8
{
	Grounded UMETA(Display = "Grounded"),
	Airborne UMETA(Display = "Airborne"),
	Landing UMETA(Display = "Landing")
};
```

---

## 2. Control Settings Structs

### FAirControlSettings
```cpp
USTRUCT(BlueprintType)
struct FAirControlSettings
{
	GENERATED_BODY()

	UPROPERTY(EditAnywhere, Category = "Steering")
	float GroundSteeringSensitivity;

	UPROPERTY(EditAnywhere, Category = "Steering")
	float AirSteeringMultiplier; // 50% of ground

	UPROPERTY(EditAnywhere, Category = "Control")
	float YawStrength; // yaw control in air

	UPROPERTY(EditAnywhere, Category = "Control")
	float RollStrength; // roll control in air

	UPROPERTY(EditAnywhere, Category = "Control")
	float PitchStrength; // pitch control in air

	FAirControlSettings() : GroundSteeringSensitivity(1.0f), AirSteeringMultiplier(0.5f), YawStrength(4.0f), RollStrength(4.0f), PitchStrength(4.0f)
	{
	}
};
```

### FAntiRollInAir
```cpp
USTRUCT(BlueprintType)
struct FAntiRollInAir
{
	GENERATED_BODY()

	UPROPERTY(EditAnywhere, Category = "Thresholds", meta = (AllowPrivateAccess = "true", ClampMin = "0.0", ClampMax = "90.0"))
	float MaxRollAngle = 45.0f; // Degrees
	
	UPROPERTY(EditAnywhere, Category = "Thresholds", meta = (AllowPrivateAccess = "true", ClampMin = "0.0", ClampMax = "90.0"))
	float MaxPitchAngle = 60.0f; // Upside down
	
	UPROPERTY(EditAnywhere, Category = "Correction")
	float AntiRollPowerInAir = 3000.0;
	
	UPROPERTY(EditAnywhere, Category = "Correction")
	float AntiPitchPowerInAir = 2000.0f;
};
```

---

## 3. Member Variables

### 3.1 ARampZone Member Variables

#### // ===== RAMP BOOST PROPERTIES =====
```cpp
UPROPERTY(EditAnywhere, Category = "Boost")
float BoostForce = 800.0f;

UPROPERTY(EditAnywhere, Category = "Boost")
float RampBoostUpward = 0.3;
```

#### // ===== RAMP COMPONENTS =====
```cpp
UPROPERTY(VisibleAnywhere, Category = "Collision")
UBoxComponent* RampTrigger;

UPROPERTY(VisibleAnywhere, Category = "Mesh")
UStaticMeshComponent* RampMeshComponent;
```

---

### 3.2 ASimulatePhysicsCar Member Variables

#### // ===== AIRBORNE STATE CONTROL =====
```cpp
// Get current airborne state
UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
EVehicleAirborneState CurrentAirborneState = EVehicleAirborneState::Grounded;

// Time the car is airborne (s)
UPROPERTY(BlueprintReadOnly, Category = "SimulateCar|Airborne")
float TimeInAir = 0.0f;
```

#### // ===== AIRBORNE TIMING & HEIGHT =====
```cpp
// Maximum Height Car reached (cm)
UPROPERTY(BlueprintReadOnly, Category = "SimulateCar|Airborne")
float MaxHeightReached = 0.0f;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
float HeightResetThreshold = 550.0f; // cm (5.5 meters)
```

#### // ===== AUTO-BALANCE PROPERTIES =====
```cpp
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
FAntiRollInAir AntiRollInAir;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne", meta = (AllowPrivateAccess = "true", ClampMin = "0.0", ClampMax = "90.0"))
float ThresholdAngle = 15.0f;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
float AirCorrectionYawStrength = 100.0f;
```

#### // ===== AIR CONTROL PROPERTIES =====
```cpp
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
FAirControlSettings AirControlSettings;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne", meta = (AllowPrivateAccess = "true", ClampMin = "0.01", ClampMax = "1.0"))
float AntiAirBorneInRampMultiplier = 0.1;
```

#### // ===== LANDING STATE PROPERTIES =====
```cpp
UPROPERTY(BlueprintReadOnly, Category = "SimulateCar|Airborne")
float LandingTime = 0.0f;

// Whether the vehicle should remain in an "airborne" state,
// preventing suspension forces from being applied while the wheels are currently on the ground.
UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
bool bSimulateInAirState = false;

// Timer handle used to delay the reactivation of suspension forces
// after the vehicle has landed.
UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
FTimerHandle RemoveSuspensionForceTimer;

// Duration (in seconds) to wait before re-enabling suspension forces
// once the vehicle transitions from airborne to grounded state.
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
float RemoveSuspensionForceTime = 0.2f;

// Landing reset thresholds
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne", meta = (AllowPrivateAccess = "true", ClampMin = "0.0", ClampMax = "90.0"))
float LandingResetRollThreshold = 45.0f; // Degrees

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne", meta = (AllowPrivateAccess = "true", ClampMin = "0.0", ClampMax = "90.0"))
float LandingResetPitchThreshold = 70.0f; // Degrees (upside down check)

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
float LandingResetHeightOffset = 100.0f; // cm (1 meters)

// Height threshold used to detect a crash / hard landing.
UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
float LandingHeightThreshold = 150.f;
```

#### // ===== CRASH LANDING PROPERTIES =====
```cpp
// Whether the vehicle is performing a hard or crash landing
UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
bool bIsCrashLanding = false;

UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
float TimeSinceCrashLanding = 0.f;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
float CrashLandingMaxTime = 1.2f;

UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
bool bCrashSlideStarted = false;

UPROPERTY(BlueprintReadWrite, Category = "SimulateCar|Airborne")
FVector CrashStartLocation = FVector::ZeroVector;

UPROPERTY(EditAnywhere, Category="CrashLanding")
float CrashSlideForce = 3500.0f; 

UPROPERTY(EditAnywhere, Category="CrashLanding")
float TargetCrashSlideDistance = 900.0f;  
```

#### // ===== RAMP BOOST PROPERTIES =====
```cpp
// Maximum allowable boost speed (km/h)
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne")
float MaxSpeedForBoostCurve = 200.0f;
```

#### // ===== CURVE ASSETS =====
```cpp
// Curve for Boost force in ramp 
UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne|Curves")
UCurveFloat* BoostForceCurve;

UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "SimulateCar|Airborne|Curves")
UCurveFloat* GravityByHeightCurve;
```

---

## 4. Core Logic Implementation

### 4.1 Ramp Boost Logic (ARampZone)

#### ARampZone Constructor
```cpp
ARampZone::ARampZone()
{
	PrimaryActorTick.bCanEverTick = false; // We use overlap events, not Tick

	// Create mesh component (root)
	RampMeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Ramp Mesh"));
	RootComponent = RampMeshComponent;

	// Create trigger box and attach to mesh
	RampTrigger = CreateDefaultSubobject<UBoxComponent>(TEXT("RampTrigger"));
	RampTrigger->SetupAttachment(RampMeshComponent);
	// Setup trigger properties
	RampTrigger->SetCollisionResponseToAllChannels(ECR_Ignore);

	// Set box size (adjust in editor per ramp)
	RampTrigger->SetBoxExtent(FVector(300.0f, 200.0f, 150.0f));

	// Physics setup
	RampTrigger->SetSimulatePhysics(false);
	RampTrigger->SetGenerateOverlapEvents(true);
}
```

#### ARampZone::BeginPlay
```cpp
void ARampZone::BeginPlay()
{
	Super::BeginPlay();
	if (RampTrigger)
	{
		RampTrigger->OnComponentBeginOverlap.AddDynamic(this, &ARampZone::OnRampOverlapBegin);
		RampTrigger->OnComponentEndOverlap.AddDynamic(this, &ARampZone::OnRampOverlapEnd);
	}
}
```

#### ARampZone::OnRampOverlapBegin (Boost Application)
```cpp
void ARampZone::OnRampOverlapBegin(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult)
{
	ASimulatePhysicsCar* Car = Cast<ASimulatePhysicsCar>(OtherActor);
	if (!Car)
	{
		return;
	}

	if (Car->IsDrifting())
	{
		Car->CancelDrift();
	}
	FVector CurrentVelocity = UAsyncTickFunctions::ATP_GetLinearVelocity(Car->GetPrimitiveRoot());
	float CurrentSpeed = CurrentVelocity.Length() * 0.036f;

	float NormalizedSpeed = FMath::Clamp(CurrentSpeed / Car->MaxSpeedForBoostCurve, 0.0f, 1.0f);

	// Sample boost force curve based on current speed
	float BoostMultiplier = 1.0f;
	if (Car->BoostForceCurve)
	{
		BoostMultiplier = Car->BoostForceCurve->GetFloatValue(NormalizedSpeed);
	}
	float FinalBoostForce = BoostForce * BoostMultiplier;

	Car->bIsRampBoostActive = true;
	Car->RampBoostTimeRemaining = Car->RampBoostDuration;
	Car->RampAccelBoost = FinalBoostForce;

	// // Calculate final boost vector
	// FVector BoostVector = BoostDirection * FinalBoostForce;
	// // Apply as impulse (single frame impact)
	// UAsyncTickFunctions::ATP_AddImpulse(Car->GetPrimitiveRoot(), BoostVector, true);

	// Call On RampBoost event
	Car->OnRampBoost(true);
};
```

#### ARampZone::OnRampOverlapEnd
```cpp
void ARampZone::OnRampOverlapEnd(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp, int32 OtherBodyIndex)
{
	ASimulatePhysicsCar* Car = Cast<ASimulatePhysicsCar>(OtherActor);
	if (!Car)
	{
		return;
	}

	Car->TriggerAllowFlying();
}
```

---

### 4.2 Airborne State: Auto-Balance & Control (ASimulatePhysicsCar)

#### UpdateAirborneState
```cpp
void ASimulatePhysicsCar::UpdateAirborneState(float DeltaTime)
{
	if (bFlipFlyingMode)
	{
		if (CurrentAirborneState == EVehicleAirborneState::Grounded)
		{
			// Just became airborne
			CurrentAirborneState = EVehicleAirborneState::Airborne;

			UpdateAirCar();
			TimeInAir = 0.0f;
			MaxHeightReached = FMath::Max(
				MaxHeightReached,
				GetActorLocation().Z - GetGroundHeight(GetActorLocation())
			);
			
			RemoveSuspensionForce();
		}
		else if(CurrentAirborneState == EVehicleAirborneState::Airborne)
		{
			const float MaxUpPitch = 3.0f;
			const float MaxUpRoll  = 5.0f;
			FRotator CurrentRot = PrimitiveComponent->GetComponentRotation();
			FRotator TargetRot  = CurrentRot;

			// Clamp pitch
			if (FMath::Abs(TargetRot.Pitch) > MaxUpPitch)
			{
				UE_LOG(LogTemp, Display, TEXT("RotPitch: %f"), CurrentRot.Pitch);
				TargetRot.Pitch = MaxUpPitch;
				// Debug Ramp
				DebugAutoBalance();
			}

			// Clamp roll
			if (FMath::Abs(TargetRot.Roll) > MaxUpRoll)
			{
				UE_LOG(LogTemp, Display, TEXT("RotRoll: %f"), CurrentRot.Roll);
				TargetRot.Roll = MaxUpRoll;
				// Debug Ramp
				DebugAutoBalance();
			}

			const FRotator NewRot = UKismetMathLibrary::RInterpTo(
				CurrentRot,
				TargetRot,
				DeltaTime,
				2.0f
			);

			PrimitiveComponent->SetWorldRotation(NewRot);
			
			UE_LOG(LogTemp, Display, TEXT("AfterRot Pitch: %f, Roll: %f"),
			       NewRot.Pitch, NewRot.Roll);

			// Still airborne
			TimeInAir += DeltaTime;
			MaxHeightReached = FMath::Max(
				MaxHeightReached,
				GetActorLocation().Z - GetGroundHeight(GetActorLocation())
			);

			// Slightly boost if vehicle is nearly landing
			NearlyLandingAcceleration =FMath::Clamp(DistanceToRoadForAntiAir < 700.0f ? 50000.f / DistanceToRoadForAntiAir : 0.f, 0.f, 150.f) ;
			if (NearlyLandingAcceleration > 0.f && TimeInAir > 0.5f)
			{
				for (UCustomSuspensionComponent* const& Suspension : SuspensionComponents)
				{
					if (Suspension->IsSteeringWheel())
					{
						UAsyncTickFunctions::ATP_AddForce(PrimitiveComponent, 1000.f * NearlyLandingAcceleration * PhysicsTransform.GetRotation().GetForwardVector() * DeltaTime, true);
					}
				}
			}
		}
	}
	else
	{
		TimeInAir = 0.0f;
	}

	CheckGroundContact();
}
```

#### Auto-Balance in UpdatePhysicsCore (Anti-Roll Region)
```cpp
// In UpdatePhysicsCore:
#pragma region Anti_Roll
    //Adaptive Anti-Roll for Ground + Air
	const bool bUseAirSettings = bInAir || bSimulateInAirState || bFlipFlyingMode;

	// Select appropriate thresholds and powers based on air state
	const float RollLimit = bUseAirSettings ? AntiRollInAir.MaxRollAngle : AntiRollLimitAngle;
	const float RollPower = bUseAirSettings ? AntiRollInAir.AntiRollPowerInAir : AntiRollPower;
	const float PitchLimit = bUseAirSettings ? AntiRollInAir.MaxPitchAngle : AntiPitchLimitAngle;
	const float PitchPower = bUseAirSettings ? AntiRollInAir.AntiPitchPowerInAir : AntiPitchPower;

	// Calculate local rotation (existing code)
	const FMatrix AntiRollRotationMatrix = FRotationMatrix::MakeFromXZ(fwd, LastRoadNormal);
	const FRotator AntiRollStandardRotation = AntiRollRotationMatrix.Rotator();
	const FTransform AntiRollStandardTransform = FTransform(AntiRollStandardRotation, PhysicsTransform.GetLocation());
	const FRotator LocalAntiRollRotationData = AntiRollStandardTransform.InverseTransformRotation(PhysicsTransform.GetRotation()).Rotator();

	// Apply roll correction with adaptive threshold
	if (FMath::Abs(LocalAntiRollRotationData.Roll) > RollLimit)
	{
		const float Roll = LocalAntiRollRotationData.Roll;
		for (const auto& SuspensionPair : SuspensionPairs)
		{
			const float AntiRollForce = -Roll * RollPower;
			UAsyncTickFunctions::ATP_AddForceAtPosition(PrimitiveComponent,
			                                            SuspensionPair.FirstSuspension->GetComponentLocation(),
			                                            GetActorUpVector() * AntiRollForce);
			UAsyncTickFunctions::ATP_AddForceAtPosition(PrimitiveComponent,
			                                            SuspensionPair.SecondSuspension->GetComponentLocation(),
			                                            GetActorUpVector() * -AntiRollForce);
		}
	}

	// Apply pitch correction with adaptive threshold
	if (FMath::Abs(LocalAntiRollRotationData.Pitch) > PitchLimit)
	{
		const float Pitch = LocalAntiRollRotationData.Pitch;
		const FSuspensionPair& FrontPair = SuspensionPairs[0];
		const FSuspensionPair& RearPair = SuspensionPairs[1];
		const float AntiPitchForce = -Pitch * PitchPower;
		UAsyncTickFunctions::ATP_AddForceAtPosition(PrimitiveComponent,
		                                            FrontPair.FirstSuspension->GetComponentLocation(),
		                                            GetActorUpVector() * AntiPitchForce);
		UAsyncTickFunctions::ATP_AddForceAtPosition(PrimitiveComponent,
		                                            FrontPair.SecondSuspension->GetComponentLocation(),
		                                            GetActorUpVector() * AntiPitchForce);
		UAsyncTickFunctions::ATP_AddForceAtPosition(PrimitiveComponent,
		                                            RearPair.FirstSuspension->GetComponentLocation(),
		                                            GetActorUpVector() * -AntiPitchForce);
		UAsyncTickFunctions::ATP_AddForceAtPosition(PrimitiveComponent,
		                                            RearPair.SecondSuspension->GetComponentLocation(),
		                                            GetActorUpVector() * -AntiPitchForce);
	}
#pragma endregion
```

---

### 4.3 Dynamic Gravity System

#### UpdateDynamicGravity
```cpp
void ASimulatePhysicsCar::UpdateDynamicGravity()
{
	if (!bFlipFlyingMode || !IsValid(GravityByHeightCurve)) return;
	
	// Height values are scaled 100 times for editor readability. Sampling uses the same scale factor,
	// so the evaluated Gravity value remains identical to the original curve.
	const float CurrentHeightFromGround = DistanceToRoadForAntiAir / 100.f; 
	AdditionalGravityMultiplier = GravityByHeightCurve->GetFloatValue(CurrentHeightFromGround);
}
```

#### AntiAirborne Gravity Application (in UpdatePhysicsCore)
```cpp
#pragma region AntiAirborne

	UpdateDynamicGravity();
	float AdditionalGravity = bInAir || bSimulateInAirState? AdditionalGravityMultiplier : 0.0f;

	if (bInAir && bShouldApplyAntiAir)
	{
		CurrentAntiAirGravitate = FMath::Min(
			CurrentAntiAirGravitate + AntiAirBorneAdditionalGravityAccel * DeltaTime,
			AntiAirBorneAdditionalGravity
		);
	}
	else
	{
		if (CurrentAirborneState == EVehicleAirborneState::Airborne || bSimulateInAirState)
		{
			float AntiAirBorneAdditionalGravityAccelInRamp =
				AntiAirBorneAdditionalGravityAccel * AntiAirBorneInRampMultiplier;

			CurrentAntiAirGravitate = FMath::Min(
				CurrentAntiAirGravitate + AntiAirBorneAdditionalGravityAccelInRamp * DeltaTime,
				AntiAirBorneAdditionalGravity  
			);
		}
		else
		{
			CurrentAntiAirGravitate = FMath::Max(
				CurrentAntiAirGravitate - AntiAirBorneAdditionalGravityAccel * DeltaTime * 5,
				0.0f
			);
		}
	}

	UAsyncTickFunctions::ATP_AddForce(
		PrimitiveComponent,
		(AdditionalGravity + CurrentAntiAirGravitate) * PrimitiveComponent->GetMass() * FVector::DownVector,
		true
	);
#pragma endregion
```

---

### 4.4 Air Control (Reduced Steering)

#### UpdateAirControl
```cpp
void ASimulatePhysicsCar::UpdateAirControl()
{
	FVector Forward = GetActorForwardVector();
	FVector Up = GetActorUpVector();
	
	float ReducedSteeringInput = SteeringInput * AirControlSettings.AirSteeringMultiplier;
	
	FVector YawTorque = Up * ReducedSteeringInput * AirControlSettings.YawStrength;
	
	FVector RollTorque = Forward * ReducedSteeringInput * AirControlSettings.RollStrength;
    
	FVector FinalTorque = YawTorque + RollTorque;

	// Apply torque
	UAsyncTickFunctions::ATP_AddTorque(PrimitiveComponent, FinalTorque, true);

	float AirControlForceStrength = 500.0f;
	float CurrentSpeed = GetSpeedKPH();
	
	FVector LateralForce = Right * ReducedSteeringInput * AirControlForceStrength;
	
	FVector ForwardForce = Forward * YawInput * AirControlForceStrength * 0.3f;
    
	FVector TotalAirForce = LateralForce + ForwardForce;
	
	float SpeedFactor = FMath::GetMappedRangeValueClamped(
		FVector2D(0.0f, 100.0f), 
		FVector2D(0.3f, 1.0f), 
		CurrentSpeed
	);
    
	UAsyncTickFunctions::ATP_AddForce(
		PrimitiveComponent, 
		TotalAirForce * SpeedFactor, 
		true
	);
}
```

#### AirController Region (in UpdatePhysicsCore)
```cpp
#pragma region AirController
	if (CurrentAirborneState == EVehicleAirborneState::Airborne)
	{
		UpdateAirControl();
	}
#pragma endregion
```

---

### 4.5 Ground Contact Detection & Landing

#### CheckGroundContact
```cpp
void ASimulatePhysicsCar::CheckGroundContact()
{
	FVector ActorLocation = GetActorLocation();
	float GroundHeight = GetGroundHeight(ActorLocation);
	float HeightAboveGround = ActorLocation.Z - GroundHeight;

	const FRotator LocalRotation = GetActorRotation();
	// Check tilt angles
	const float AbsRoll = FMath::Abs(LocalRotation.Roll);
	const float AbsPitch = FMath::Abs(LocalRotation.Pitch);

	const bool bIsNearlyOnGround = HeightAboveGround <= LandingHeightThreshold && CurrentAirborneState == EVehicleAirborneState::Airborne && MaxHeightReached > HeightResetThreshold;
	const bool bIsCrash = HeightAboveGround <= LandingHeightThreshold && (AbsRoll > LandingResetRollThreshold || AbsPitch > LandingResetPitchThreshold) && DistanceToRoadForAntiAir == UE_MAX_FLT;
	if (bIsCrash || bIsNearlyOnGround)
	{
		bIsCrashLanding = bIsCrash;
		OnLanding();
	}
}
```

#### OnLanding
```cpp
void ASimulatePhysicsCar::OnLanding()
{
	// Broadcast ramp boost end
	CurrentAirborneState = EVehicleAirborneState::Landing;

	if (!bIsCrashLanding)
	{
		OnRampBoost(false);
	}

	GetWorldTimerManager().SetTimer(RemoveSuspensionForceTimer, this, &ASimulatePhysicsCar::RestoreSuspensionForce, RemoveSuspensionForceTime, false);
}
```

---

### 4.6 Landing State: Recovery & Stabilization

#### UpdateLandingState
```cpp
void ASimulatePhysicsCar::UpdateLandingState(float DeltaTime)
{
	if (CurrentAirborneState != EVehicleAirborneState::Landing)
	{
		return;
	}
	
	const FRotator LocalRotation = GetActorRotation();
	// Check tilt angles
	const float AbsRoll = FMath::Abs(LocalRotation.Roll);
	const float AbsPitch = FMath::Abs(LocalRotation.Pitch);
	if (AbsRoll > LandingResetRollThreshold || AbsPitch > LandingResetPitchThreshold)
	{
		bIsCrashLanding = true;
	}
	
	if (bIsCrashLanding)
	{
		TimeSinceCrashLanding += DeltaTime;
		FTransform PhysicCaTransform = UAsyncTickFunctions::ATP_GetTransform(GetPrimitiveRoot());
		FVector PhysicCarLocation = PhysicCaTransform.GetLocation();
		FVector GuideDirection;
		GuideLineSubsystem->GetDirection(PhysicCarLocation, GuideDirection);
		
		FVector SlideForce = GuideDirection * CrashSlideForce * DeltaTime * 100.0f;
		UAsyncTickFunctions::ATP_AddForce(PrimitiveComponent, SlideForce, true);
	
		UE_LOG(LogTemp, Display, TEXT("CrashSlide | Force=%s"), *SlideForce.ToString());
	}
	
	if (TimeSinceCrashLanding > CrashLandingMaxTime)
	{
		bIsCrashLanding = false;
		TimeSinceCrashLanding = 0.f;
		CurrentAirborneState = EVehicleAirborneState::Grounded;
		
		FVector PointToReset = GetActorLocation() + FVector(0,0, LandingResetHeightOffset);
		FRotator RotationToReset = GetCurrentGuideRotation();
		// Auto-reset car
		ResetState(PointToReset, RotationToReset);
	}
}
```

---

### 4.7 Helper Functions

#### Suspension Force Control
```cpp
void ASimulatePhysicsCar::RemoveSuspensionForce()
{
	if (!IsValid(this) || IsActorBeingDestroyed()) return;

	//Switch to in-air simulation
	bSimulateInAirState = true;
	Acceleration *= LandingAccelerationMultiplier;
	AntiAirBorneAdditionalGravity = 0.0f;

	//Enlarge anti-roll effect
	AntiRollLimitAngle /= 100.0f;
	AntiPitchLimitAngle /= 100.0f;
	
	// Reduce suspension force
	for (int i = 0; i < SuspensionComponents.Num(); i++)
	{
		SuspensionComponents[i]->bIsSuspensionEnabled = false;
	}
}

void ASimulatePhysicsCar::RestoreSuspensionForce()
{
	if (!IsValid(this) || IsActorBeingDestroyed()) return;

	//Quit in-air simulation
	bSimulateInAirState = false;
	AntiAirBorneAdditionalGravity = 5.0f;
	Acceleration /= LandingAccelerationMultiplier;
	NearlyLandingAcceleration = 0.f;
	MaxHeightReached = 0.f;

	//Restore anti-roll effect
	AntiRollLimitAngle *= 100.0f;
	AntiPitchLimitAngle *= 100.0f;

	// Restore suspension force
	for (int i = 0; i < SuspensionComponents.Num(); i++)
	{
		SuspensionComponents[i]->bIsSuspensionEnabled = true;
	}
	
	GetWorldTimerManager().ClearTimer(RemoveSuspensionForceTimer);
}
```

#### GetGroundHeight
```cpp
float ASimulatePhysicsCar::GetGroundHeight(const FVector& Position)
{
	FHitResult Hit;
	FVector Start = Position;
	FVector End = Position - FVector(0, 0, 5000.f);

	FCollisionQueryParams Params;
	Params.AddIgnoredActor(this);

	if (GetWorld()->LineTraceSingleByChannel(Hit, Start, End, CollisionChannel, Params))
	{
		return Hit.ImpactPoint.Z;
	}
	return Position.Z;
}
```

---

## 5. Main Physics Loop Integration

#### Tick Function
```cpp
void ASimulatePhysicsCar::Tick(float DeltaTime)
{
    //... existed logics

	if (bIsRampBoostActive && RampBoostTimeRemaining > 0.f)
	{
		RampBoostTimeRemaining -= DeltaTime;
		if (RampBoostTimeRemaining <= 0.f)
		{
			bIsRampBoostActive = false;
		}
		else
		{
			const FVector ForwardDir = GetActorForwardVector().GetSafeNormal();
			const float Mass = PrimitiveComponent->GetMass();
			const FVector Force = ForwardDir * RampAccelBoost * Mass; // F = m * a
			UAsyncTickFunctions::ATP_AddForce(PrimitiveComponent, Force, true);
		}
	}
	
    UpdateAirborneState(DeltaTime);
	UpdateLandingState(DeltaTime);

    //... existed logics
}
```

#### UpdatePhysicsCore (Complete Flow)
```cpp
void ASimulatePhysicsCar::UpdatePhysicsCore(float DeltaTime)
{
    //... existed logics

    // ===== AUTO-BALANCE (Anti-Roll Region) =====
    #pragma region Anti_Roll
    // ... (See section 4.2)
    #pragma endregion

    // ===== DYNAMIC GRAVITY SYSTEM (AntiAirborne Region) =====
    #pragma region AntiAirborne
    // ... (See section 4.3)
    #pragma endregion

    // ===== AIR CONTROL (AirController Region) =====
    #pragma region AirController
    if (CurrentAirborneState == EVehicleAirborneState::Airborne)
    {
        UpdateAirControl();
    }
    #pragma endregion

    //... existed logics
}
```

---

## Summary Table

| Category | Variables | Functions |
|----------|-----------|-----------|
| **State Enum** | `EVehicleAirborneState` | - |
| **Control Structs** | `FAirControlSettings`, `FAntiRollInAir` | - |
| **Airborne State Control** | `CurrentAirborneState`, `TimeInAir` | `UpdateAirborneState()` |
| **Airborne Timing & Height** | `MaxHeightReached`, `HeightResetThreshold` | - |
| **Auto-Balance** | `AntiRollInAir`, `ThresholdAngle`, `AirCorrectionYawStrength` | Anti-Roll region in `UpdatePhysicsCore()` |
| **Air Control** | `AirControlSettings`, `AntiAirBorneInRampMultiplier` | `UpdateAirControl()` |
| **Dynamic Gravity** | `GravityByHeightCurve`, `AdditionalGravityMultiplier` | `UpdateDynamicGravity()` |
| **Ramp Boost** | `BoostForce`, `RampBoostUpward`, `MaxSpeedForBoostCurve`, `BoostForceCurve` | `OnRampOverlapBegin()`, `OnRampOverlapEnd()` |
| **Landing State** | `LandingTime`, `LandingHeightThreshold`, `LandingReset*` properties | `OnLanding()`, `UpdateLandingState()` | 
| **Crash Landing** | `bIsCrashLanding`, `TimeSinceCrashLanding`, `CrashSlide*` properties | Crash handling in `UpdateLandingState()` |
| **Helper Functions** | `RemoveSuspensionForceTimer`, `bSimulateInAirState` | `RemoveSuspensionForce()`, `RestoreSuspensionForce()`, `GetGroundHeight()`, `CheckGroundContact()` |
| **Curve Assets** | `BoostForceCurve`, `GravityByHeightCurve` | - |

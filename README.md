# ArmA3_Wasteland_VectorBldg.Altis

## Installation
These files modify the original R3F built into A3Wasteland as of February 22, 2017.
Merge these files into your mission.
####.

## 1. Changes made to "missionFile >> globalcompile.sqf"
#### A3W_fnc_eXpochVectorBuilding = [_clientFunc, "fn_eXpochVectorBuilding.sqf"] call mf_compile;
#### A3W_fnc_eXpochVectorDirandUp = [_clientFunc, "fn_eXpochVectorDirandUp.sqf"] call mf_compile;
####.

## 2. Added the 2 new functions to the "missionFile >> client >> functions folder"
#### fn_eXpochVectorBuilding.sqf
#### fn_eXpochVectorDirandUp.sqf
####.

## 3. Changes to "missionFile >> addons >> R3F_ARTY_AND_LOG >> init.sqf"
    R3F_ARTY_AND_LOG_FNCT_PUBVAR_setDirAndUp =
		{
			private ["_objet", "_attachedTo", "_newDirAndUp", "_buildPosX", "_buildPosY", "_buildPosZ"];
			_objet = _this select 1 select 0;
			_attachedTo = _this select 1 select 1;
			_newDirAndUp = _this select 1 select 2;
			_buildPosX = _this select 1 select 3;
			_buildPosY =_this select 1 select 4;
			_buildPosZ =_this select 1 select 5;
			
			_objet attachTo [_attachedTo,[_buildPosX,_buildPosY,_buildPosZ]];
			_objet setVectorDirAndUp _newDirAndUp;
		};
		"R3F_ARTY_AND_LOG_PUBVAR_setDirAndUp" addPublicVariableEventHandler R3F_ARTY_AND_LOG_FNCT_PUBVAR_setDirAndUp;
    
## 4. Changes to "missionFile >> addons >> R3F_ARTY_AND_LOG >> R3F_LOG >> objet_deplacable >> deplacer.sqf"
#### Many changes were made to this file and it is suggested to just replace the old file with this new file
## 4a.
#### Lines 96-101
      _objet attachTo [player,
			[
				0,
				1 + (_corner1 max _corner2 max _corner3 max _corner4),
				0.1 - (_objectMinBB select 2)
			]];
#### Becomes
      BuildPosX = 0;
			BuildPosY = 1 + (_corner1 max _corner2 max _corner3 max _corner4);
			BuildPosZ = 0.1 - (_objectMinBB select 2);
			
			_objet attachTo [player,[BuildPosX, BuildPosY, BuildPosZ]];

			//DonkeyPunch eXpoch Vector Building Lite for WasteLand courtesy of DirtySanchez
			BuildVecYaw = 0;BuildVecPitch = 0;BuildVecRoll = 0;
			[_objet] call A3W_fnc_eXpochVectorBuilding;
## 4b.
#### Add this to Line 159
      _objet enableSimulation false;
			BuildPosX = 0;BuildPosY = 5;BuildPosZ = 2;BuildVecYaw = 0;BuildVecPitch = 0;BuildVecRoll = 0;R3FObjAttachedTo = objNull;
			(findDisplay 46) displayRemoveEventHandler ["KeyDown", keyDownEHId];
			(findDisplay 46) displayRemoveEventHandler ["KeyUp", keyUpEHId];
## 4c.
#### Lines 199-204
      else
			{
				_objectPos = _objet call fn_getPos3D;
				_objectPos set [2, ((player call fn_getPos3D) select 2) + _zOffset];
				_objet setPos _objectPos;
			};
#### Becomes
      else
			{
				_objectPos = _objet call fn_getPos3D;
				_newDirAndUp = [[ sin BuildVecYaw * cos BuildVecPitch,cos BuildVecYaw * cos BuildVecPitch,sin BuildVecPitch],	[[ sin BuildVecRoll,-sin BuildVecPitch,cos BuildVecRoll * cos BuildVecPitch],-BuildVecYaw] call BIS_fnc_rotateVector2D];
				R3F_LOG_joueur_deplace_objet setVectorDirAndUp _newDirAndUp;
			};
## 4d.
#### Add this to Line 213
      R3F_LOG_joueur_deplace_objet = objNull;R3FObjAttachedTo = objNull;
			BuildPosX = 0;BuildPosY = 5;BuildPosZ = 0;BuildVecYaw = 0;BuildVecPitch = 0;BuildVecRoll = 0;

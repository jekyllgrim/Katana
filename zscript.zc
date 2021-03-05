version "4.0.0"

Class JGPKatana : Weapon {
	private bool side;
	bool atkblocked;
	int blockstamina;
	property stamina : blockstamina;
	Default {
		JGPKatana.stamina 100;
		weapon.SlotNumber 1;
		+WEAPON.AMMO_OPTIONAL
		+WEAPON.NOAUTOFIRE
		weapon.bobstyle 'InverseSmooth';
		weapon.bobrangex 0.4;
		weapon.bobrangey 0.4;
		weapon.bobspeed 1.8;
		weapon.kickback 0;
	}
	override void DoEffect() {
		super.DoEffect();
		if (!owner)
			return;
		let psp = owner.player.FindPSprite(PSP_WEAPON);
		let ps2 = owner.player.FindPSprite(200);
		if (!psp)
			return;
		if (!ps2 && (owner.player.readyweapon == self))
			owner.player.SetPSprite(200,FindState("BlockTint"));
		if (InStateSequence(psp.curstate,FindState("Ready")) && (level.time % 8 == 0))
			blockstamina = Clamp(blockstamina+1,0, default.blockstamina);
		if (InStateSequence(psp.curstate,FindState("Block")) || InStateSequence(psp.curstate,FindState("BlockedAttack")))
			owner.speed = owner.default.speed * 0.5;
		else
			owner.speed = owner.default.speed;
		Console.Printf("Stamina: %d",blockstamina);
	}
	states {
		Select:
			TNT1 A 8 {
				A_PlaySound("sword/unsheathe");
				A_Overlay(100,"Draw");
				A_OverlayFlags(100,PSPF_ADDWEAPON,false);
				A_WeaponOffset(96,48);
			}				
			KATA CCCC 2 {
				A_WeaponOffset(-16,4,WOF_ADD);
				A_WeaponReady();
			}			
			goto ready;
		Draw:
			KATA DDDDDDDD 1 A_OverlayOffset(100,30,-32,WOF_ADD);
			stop;
		Deselect:	
			//KATA CCCC 1 A_WeaponOffset(16,-4,WOF_ADD);
			TNT1 A 8 {
				TakeInventory("ParryProtection",1);
				TakeInventory("BlockProtection",1);
				invoker.atkblocked = false;
				if (player.health > 0) {
					A_PlaySound("sword/sheathe");
					A_Overlay(100,"Sheathe");
					A_OverlayFlags(100,PSPF_ADDWEAPON,false);
					A_OverlayOffset(100,240,-224);
				}
				A_WeaponOffset(0,32);
			}
			TNT1 A 0 A_Lower();
			wait;
		Sheathe:
			KATA DDDDDDDD 1 A_OverlayOffset(100,-30,32,WOF_ADD);
			stop;
		Ready:
			TNT1 A 0 {
				A_WeaponOffset(0,32,WOF_INTERPOLATE);
				TakeInventory("ParryProtection",1);
				TakeInventory("BlockProtection",1);
				invoker.atkblocked = false;
			}
			KATA C 1 {
				A_Weaponready();
			}
			wait;
		Fire:
			TNT1 A 4 {
				A_PlaySound("sword/swing");
				if (random(0,1) == 1)
					A_Overlay(100,"Attack1");
				else
					A_Overlay(100,"Attack2");
				if (invoker.side) {
					A_OverlayFlags(100,PSPF_MIRROR|PSPF_FLIP,true);
					invoker.side = false;
				}
				else {
					A_OverlayFlags(100,PSPF_MIRROR|PSPF_FLIP,false);
					invoker.side = true;
				}
				A_OverlayFlags(100,PSPF_ADDWEAPON,false);
				A_WeaponOffset(96,48);
			}
			TNT1 A 6 {
				A_CustomPunch(30,norandom:true,flags:CPF_NOTURN,pufftype:"SwordPuff");
			}
			KATA CCCCCCCC 1 {
				A_WeaponOffset(-12,-2,WOF_ADD);
				if ((player.cmd.buttons & BT_ATTACK) && !(player.oldbuttons & BT_ATTACK))
					return ResolveState("Fire");
				else if (player.cmd.buttons & BT_ALTATTACK)
					return ResolveState("AltFire");
				return ResolveState(null);
			}
			goto ready;
		Attack1:			
			KATA AAAAA 2 A_OverlayOffset(100,-56+frandom(-6,1),42+frandom(-4,4),WOF_ADD);
			stop;
		Attack2:
			KATA BBBBB 2 A_OverlayOffset(100,-120+frandom(0,40),16+frandom(-4,8),WOF_ADD);
			stop;
		Load:
			KATA EFG 0;
		AltFire:
			TNT1 E 0 {
				A_WeaponOffset(-22,80);
			}
			KATA E 1 {
				A_WeaponOffset(7,-14,WOF_ADD);
			}
			KATA EEEEE 1 {
				A_WeaponOffset(7,-14,WOF_ADD);
				A_WeaponOffset(0,32,WOF_INTERPOLATE);
				if (!(player.cmd.buttons & BT_ALTATTACK))
					return ResolveState("Parry");
				return ResolveState(null);
			}
			#### # 0 A_WeaponOffset(0,32);
			goto block;
		BlockTint:
			KATP # 1 {
				let psp = Player.FindPsprite(PSP_Weapon);
				let ps2 = Player.FindPsprite(200);
				let ps100 = Player.FindPsprite(100);
				if (psp && ps2) {
					ps2.frame = psp.frame;
					A_OverlayRenderstyle(200,STYLE_ADD);
					A_OverlayFlags(200,PSPF_ALPHA|PSPF_FORCEALPHA,true);
					A_OverlayAlpha(200,(100 - invoker.blockstamina) * 0.01);
					//A_OverlayFlags(200,PSPF_ADDWEAPON,false);
					if (ps100) {
						//A_OverlayOffset(200,ps100.x+frandom(-1,1),ps100.y+frandom(-1,1),WOF_INTERPOLATE);
						ps2.bMIRROR = ps100.bMIRROR;
						ps2.bFLIP = ps100.bFLIP;
					}
					else {						
						//A_OverlayOffset(200,psp.x+frandom(-1,1),psp.y+frandom(-1,1),WOF_INTERPOLATE);
						ps2.bMIRROR = false;
						ps2.bFLIP = false;
					}
				}
			}
			wait;
		loadsprites:
			KATP EFGHIJ 0;			
		Block:
			KATA # 1 {
				if (!FindInventory("BlockProtection"))
					GiveInventory("BlockProtection",1);
				if (invoker.atkblocked)
					return ResolveState("BlockedAttack");
				return ResolveState(null);
			}
			#### # 0 {
				if ((player.cmd.buttons & BT_ALTATTACK) && (player.oldbuttons & BT_ALTATTACK))
					return ResolveState("Block");
				TakeInventory("BlockProtection",1);
				return ResolveState("BlockEnd");
			}
		BlockedAttack:
			#### # 0 {
				A_WeaponOffset(-5,5,WOF_ADD);
				A_PlaySound("sword/hitwall",CHAN_7);
				invoker.atkblocked = false;
				for (int i = random(25,30); i > 0; i --)
					A_SpawnParticle("FF3030",SPF_FULLBRIGHT|SPF_RELATIVE,20,2,xoff:24,yoff:frandom(-22,18),zoff:frandom(40,52),velx:frandom(-0.3,0.3),velz:-1,accelz:0.05);
			}
			#### ##### 1 A_WeaponOffset(1,-1,WOF_ADD);
			goto Block+1;
		Parry:	
			#### # 0 {
				if (!FindInventory("ParryProtection"))
					GiveInventory("ParryProtection",1);
			}
			KATA EFGHIJ 1 {
				A_WeaponOffset(-12,10,WOF_ADD);
			}
			KATA ####### 1 {
				A_WeaponOffset(50,25,WOF_ADD);
			}
			#### # 0 A_TakeInventory("ParryProtection",1);
		BlockEnd:
			KATA ###### 1 {
				A_WeaponOffset(-8,20,WOF_ADD);
				if ((player.cmd.buttons & BT_ALTATTACK) && !(player.oldbuttons & BT_ALTATTACK))
					return ResolveState("AltFire");
				if ((player.cmd.buttons & BT_ATTACK) && !(player.oldbuttons & BT_ATTACK))
					return ResolveState("Fire");
				return ResolveState(null);
			}
			#### # 0 A_WeaponOffset(96,48);
			KATA CCCCCCCC 1 {
				A_WeaponOffset(-12,-2,WOF_ADD);
				if ((player.cmd.buttons & BT_ALTATTACK))
					return ResolveState("AltFire");
				else if (player.cmd.buttons & BT_ATTACK)
					return ResolveState("Fire");
				return ResolveState(null);
			}
			goto ready;			
	}
}

Class BlockProtection : Inventory {
	Default {
		inventory.maxamount 1;
		+INVENTORY.UNDROPPABLE
		+INVENTORY.UNTOSSABLE
	}
	override void ModifyDamage (int damage, Name damageType, out int newdamage, bool passive, Actor inflictor, Actor source, int flags) {
		//check that both player and damage source exist
		if (inflictor && owner) {
			//get absolute (non-negative) angle at which the attack hit us:
			let adiff = abs(owner.DeltaAngle(owner.angle,owner.AngleTo(inflictor)));
			//do the rest if the angle was under 49 degrees (we were hit from the front)
			if (adiff <= 48) {
				let weap = JGPKatana(owner.player.readyweapon);		//get pointer to Katana
				if (weap && (weap is "JGPKatana")) {
					//Console.Printf("%f",adiff);
					weap.atkblocked = true;							//this bool will make it enter BlockedAttack state
					if (weap.blockstamina > 0)						//if stamina is over 0, received damage is nullified
						newdamage = 0;
					else												//otherwise it's cut by 50%
						newdamage = damage*0.5;
					weap.blockstamina = Clamp(weap.blockstamina - damage,0,weap.default.blockstamina);	//reduce the stamina by the received damage amount
				}				
			}
		}
	}
}

Class ParryProtection : Inventory {
	Default {
		inventory.maxamount 1;
		+INVENTORY.UNDROPPABLE
		+INVENTORY.UNTOSSABLE
	}
	override void ModifyDamage (int damage, Name damageType, out int newdamage, bool passive, Actor inflictor, Actor source, int flags) {
		if (inflictor && owner && passive) {
			let adiff = abs(owner.DeltaAngle(owner.angle,owner.AngleTo(inflictor)));
			if (adiff <= 48) {
				newdamage = 0;	//nullify damage
				owner.A_PlaySound("sword/parry",volume:0.6,pitch:frandom(0.8,0.9));	//play parry sound from the player
				//Console.Printf("%s - %s",inflictor.GetClassName(),source.GetClassName());
				let weap = JGPKatana(owner.player.readyweapon);	//get access to player's weapon
				if (weap && (weap is "JGPKatana")) {
					weap.blockstamina = Clamp(weap.blockstamina + 20,0,weap.default.blockstamina);	//parrying increases stamina (as opposed to blocking)
				}
				if (inflictor.bMISSILE) {	//if we were hit by a projectile...
					inflictor.destroy();	//destroy it
					let mis = owner.A_SpawnProjectile(inflictor.GetClassName(),angle:owner.angle,flags:CMF_AIMDIRECTION|CMF_ABSOLUTEANGLE|CMF_ABSOLUTEPITCH,pitch:owner.pitch); //spawn identical projectile
					if (mis) {	//change the spawned projectiles properties:
						mis.A_SetTranslation("Reflected");		//it gets a special greenish hue
						mis.speed = mis.default.speed * 2;			//it'll fly twice as fast
						mis.SetDamage(mis.default.damage * 2);		//it'll deal double the damage
						mis.bFORCEPAIN = true;						//it'll always cause victims to enter Pain state
					}
					//spawn some green particles in front of the player (parry effect)
					for (int i = random(30,40); i > 0; i --)
						owner.A_SpawnParticle("30FF30",SPF_FULLBRIGHT|SPF_RELATIVE,20,2,xoff:24,yoff:frandom(-18,18),zoff:frandom(30,52),velz:-1,accelz:0.05);
				}
				//otherwise do this if the attack was dealt by a monster:
				else if (inflictor.bISMONSTER && !inflictor.bFRIENDLY && inflictor.health > 0) {	//make sure it's a non-friendly monster who's still alive
					inflictor.A_FaceTarget();											//make it face us
					inflictor.vel += AngletoVector (owner.AngleTo (inflictor), 8.0);	//push it away
					if (inflictor.FindState("Pain")) {									//check if it has a Pain state
						inflictor.SetStateLabel("Pain");								//make it enter Pain state if so
						inflictor.DamageMobj(owner,owner,10,"normal");				//deal 10 damage to it
					}
				}
			}
		}
	}
}

/*Class ParriedColorOverlay : Actor {
	Default {
		+NOINTERACTION
	}
	override void Tick() {
		super.Tick();
		if (!master) {
			destroy();
			return;
		}*/
		

Class SwordPuff : Actor {
	Default {
		+PUFFGETSOWNER
		+HITTRACER
		+PUFFONACTORS
		+NOBLOCKMAP
		+NOGRAVITY
		seesound "sword/hit";
		attacksound "sword/hitwall";
		damagetype "sword";
	}
	states {
		Spawn: 
			TNT1 A 1;
			stop;
		/*Melee:
			TNT1 A 1 {
				if (tracer) {
					let a = DecapImp(tracer);
					if (a)
						a.decaps*/
	}
}


datatype Point {
	x: int
	y: int
}

datatype GameMode {
	gameMode: string
}

datatype WorldModel {
	gameMode: GameMode
	isKickable: boolean
	ball: Point
}

interface UpdateWorldI {
	event updateWorldModel: WorldModel
}



interface MovementI {
	doMove( pos : Point )
	stop ( )
	doDribble ( )
}

interface ShootI {
	doShoot()
}

controller Kicker {
	uses UpdateWorldI 

	sref stm_ref0 = KickerStm
	cycleDef cycle == 1

	connection Kicker on updateWorldModel to stm_ref0 on updateWorldModel
requires MovementI requires ShootI }

stm KickerStm {
	var wm : WorldModel
	var canShoot : boolean
	input context {  uses UpdateWorldI }
	output context { requires ShootI requires MovementI }
	cycleDef cycle == 1
	initial i0
	final f0

	state SGoToBall {
		entry $ doMove ( wm.ball )
	}

	junction j0
	state SShoot {
		entry canShoot = canShootToGoal ( ) ; if canShoot then $ doShoot ( ) end
	}

	state SDribble {
		entry $ doDribble ( )
	}
	state sUpdateWorldModel {
	}
	junction j2
	junction j1
	junction j3

	transition t4 {
		from j0
		to SShoot
	condition wm . isKickable
	}
	transition t0 {
		from i0
		to sUpdateWorldModel
	}
	transition t11 {
		from sUpdateWorldModel
		to j3
		condition 
	$  updateWorldModel ? wm
	}
	transition t12 {
		from sUpdateWorldModel
		to sUpdateWorldModel
		condition not $ updateWorldModel
		action exec
	}
	transition t9 {
		from SDribble
		to j1
	}
	transition t1 {
		from SGoToBall
		to j0
	}
transition t2 {
		from j2
		to SDribble
		condition not canShoot
	}
	transition t6 {
		from j2
		to j1
		condition canShoot
	}
	transition t5 {
		from SShoot
		to j2
	}
	transition t7 {
		from j1
		to sUpdateWorldModel
		action exec
	}
transition t8 {
		from j3
		to SGoToBall
		condition wm . gameMode.gameMode == "Play on"
	}
	transition t10 {
		from j3
		to f0
		condition wm . gameMode . gameMode != "Play on"
	}
transition t3 {
		from j0
		to sUpdateWorldModel
		condition not wm . isKickable
		action exec
	}
}

function canShootToGoal(): boolean { }

module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides ShootI
		
	uses UpdateWorldI 
	}

	cref ctrl_ref0 = Kicker
	cycleDef cycle == 1

	connection Servidor on updateWorldModel to ctrl_ref0 on updateWorldModel ( _async )
}


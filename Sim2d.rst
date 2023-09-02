datatype Point {
	x: int
	y: int
}

datatype GameMode {
	gameMode: string
}

datatype WorldModel {
	gameMode: GameMode
	canShoot: boolean
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
canKickToGoal ( )
	var 
shoot : boolean
}

interface WorldModelI {
	var ball: Point
	var kickable: boolean
	var canShoot : boolean
}

controller Kicker {
	uses UpdateWorldI 
	
	sref stm_ref0 = KickerStm
	cycleDef cycle == 1

	connection Kicker on updateWorldModel to stm_ref0 on updateWorldModel
requires MovementI requires ShootI requires WorldModelI }

stm KickerStm {
	var wm : WorldModel
	input context {  uses UpdateWorldI requires WorldModelI }
	output context { requires ShootI requires MovementI }
	cycleDef cycle == 1
	initial i0
	final f0

	state SGoToBall {
		entry $ doMove ( wm.ball )
	}

	junction j0
	state SShoot {
	entry $ doShoot ( )
	}

	state SDribble {
		entry $ doDribble ( )
	}
	state ScheckGameMode {
	}
	junction j2
	junction j1

	transition t3 {
		from j0
		to ScheckGameMode
	condition 
	not $ kickable
		action exec
	}
	transition t4 {
		from j0
		to j2
	condition wm . isKickable
	}
	transition t0 {
		from i0
		to ScheckGameMode
	}
	transition t11 {
		from ScheckGameMode
		to SGoToBall
		condition 
	$  updateWorldModel ? wm
	}
	transition t12 {
		from ScheckGameMode
		to ScheckGameMode
		condition not $ updateWorldModel
		action exec
	}
	transition t13 {
		from ScheckGameMode
		to f0
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
		condition not wm . canShoot
	}
	transition t6 {
		from j2
		to SShoot
		condition wm . canShoot
	}
	transition t5 {
		from SShoot
		to j1
	}
	transition t7 {
		from j1
		to ScheckGameMode
		action exec
	}
}

module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides ShootI
		
	uses UpdateWorldI provides WorldModelI }

	cref ctrl_ref0 = Kicker
	cycleDef cycle == 1

	connection Servidor on updateWorldModel to ctrl_ref0 on updateWorldModel ( _async )
}


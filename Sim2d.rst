datatype Point {
	x: int
	y: int
}
interface UpdateWorldI {
	event updateWorldModel
}

interface MovementI {
	doMove( pos : Point )
	stop ( )
	doDribble ( )
}

interface KickI {
	doKick()
canKickToGoal ( )
	var kicked : boolean
}

interface WorldModelI {
	var ball: Point
	var kickable: boolean
}

controller Kicker {
	uses UpdateWorldI 
	
	sref stm_ref0 = KickerStm
	cycleDef cycle == 1

	connection Kicker on updateWorldModel to stm_ref0 on updateWorldModel
requires MovementI requires KickI requires WorldModelI }

stm KickerStm {
	input context {  uses UpdateWorldI requires WorldModelI }
	output context { requires KickI requires MovementI }
	cycleDef cycle == 1
	initial i0
	final f0

	state SGoToBall {
		entry $ doMove ( $ ball )
	}
	state DMovingToBall {
	}
	junction j0
	state SKick {
	entry $ doKick ( )
	}
	state DKicking {
	}
	junction j1
	state SDribble {
		entry $ doDribble ( )
	}
	junction j2

	transition t0 {
		from i0
		to SGoToBall
	}
	transition t1 {
		from SGoToBall
		to DMovingToBall
	}
	transition t2 {
		from DMovingToBall
		to j0
		exec
	}
	transition t3 {
		from j0
		to DMovingToBall
	condition not $ kickable
	}
	transition t4 {
		from j0
		to SKick
	condition $ kickable
	}
	transition t5 {
		from SKick
		to DKicking
	}
	transition t6 {
		from DKicking
		to j1
		exec
	}
	transition t7 {
		from j1
		to f0
	}
	transition t8 {
		from j1
		to SDribble
	}
	transition t9 {
		from SDribble
		to j2
		exec
	}
	transition t10 {
		from j2
		to SGoToBall
	}
}

module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides KickI
		
	uses UpdateWorldI provides WorldModelI }

	cref ctrl_ref0 = Kicker
	cycleDef cycle == 1

	connection Servidor on updateWorldModel to ctrl_ref0 on updateWorldModel ( _async )
}


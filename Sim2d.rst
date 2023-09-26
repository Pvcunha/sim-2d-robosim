package server

datatype Point {
	x: int
	y: int
}


datatype WorldModel {
	gameMode: string
	isKickable: boolean
	ball: Point
}

interface UpdateWorldModelGoalieI {
	event updateWorldModelGoalie: WorldModel
}

interface UpdateWorldModelKickerI {
	event updateWorldModelKicker: WorldModel
}


interface MovementI {
	doMove( pos : Point )
	stop ( )
	doDribble ( )
}

interface ShootI {
	doShoot()
}


module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides ShootI
		
		uses UpdateWorldModelKickerI 
		uses UpdateWorldModelGoalieI
	}

	cref ctrl_ref0 = kicker::Kicker
	cycleDef cycle == 1

	connection Servidor on updateWorldModelKicker to ctrl_ref0 on updateWorldModelKicker ( _async )
}


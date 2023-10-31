package server

datatype Point {
	x: int
	y: int
}


datatype WorldModel {
	gameMode: string
	isKickable: boolean
	ball: Point
	goalCenter: Point
	inGoalCenter : boolean
	catchable : boolean
	isInOurPenaltyArea : boolean
	tackleProbability : real
	goalPosition : Point
	
	cyclesToGoalieReachBall : int
	cyclesToOpponentReachBall : int
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

interface CatchI {
	doCatch()
}

interface TackleI {
	doTackle()
}

interface ClearBallI {
	doClearBall()
}

interface BodyInterceptI {
	doBodyIntercept()
}


module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides ShootI
		provides CatchI
		provides TackleI
		provides ClearBallI
		
		uses UpdateWorldModelKickerI 
		uses UpdateWorldModelGoalieI
	}

	cref ctrl_ref0 = kicker::Kicker
	cycleDef cycle == 1

	connection Servidor on updateWorldModelKicker to ctrl_ref0 on updateWorldModelKicker ( _async )
connection Servidor on updateWorldModelGoalie to ctrl_ref1 on updateWorldModelGoalie ( _async )
	cref ctrl_ref1 = Goalie::Goalie
}


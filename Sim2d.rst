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

controller Controller {
	uses UpdateWorldModelKickerI
	uses UpdateWorldModelGoalieI
	
	
	requires MovementI 
	requires CatchI 
	requires TackleI 
	requires ClearBallI 
	requires BodyInterceptI
	requires ShootI 
	
	sref stm_ref0 = kicker::KickerStm
	sref stm_ref1 = Goalie::GoalieStm
	
	connection Controller on updateWorldModelGoalie to stm_ref1 on updateWorldModelGoalie
	connection Controller on updateWorldModelKicker to stm_ref0 on updateWorldModelKicker
	
	cycleDef cycle == 1
}


module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides ShootI
		provides CatchI
		provides TackleI
		provides ClearBallI
		provides BodyInterceptI
		
		uses UpdateWorldModelKickerI 
		uses UpdateWorldModelGoalieI
	}
	
	cref ctrl_ref = Controller
	cycleDef cycle == 1
	connection Servidor on updateWorldModelKicker to ctrl_ref on updateWorldModelKicker ( _async )
	connection Servidor on updateWorldModelGoalie to ctrl_ref on updateWorldModelGoalie ( _async )
	

}


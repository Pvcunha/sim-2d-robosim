package Goalie

datatype Point {
	x: int
	y: int
}

datatype GameMode {
	gameMode: string
}

datatype WorldModel {
	gameMode: GameMode
	goalCenter: Point
	inGoalCenter : boolean
	catchable : boolean
	isInOurPenaltyArea : boolean
	kickable : boolean
	tackleProbability : int
	cyclesToGoalieReachBall : int
	cyclesToOpponentReachBall : int
}

interface UpdateWorldI {
	event updateWorldModel: WorldModel
}

interface MovementI {
	doMove( pos : Point )
	stop ( )
}

interface CatchI {
	doCatch()
}

interface TackleI {
	doTackle()
}

controller Goalie {
	uses UpdateWorldI 
	
	sref stm_ref0 = GoalieStm
	cycleDef cycle == 1
	connection Goalie on updateWorldModel to stm_ref0 on updateWorldModel
	requires MovementI requires CatchI requires TackleI
}


stm GoalieStm {
	var wm : WorldModel
	input context {  uses UpdateWorldI }
	output context { requires MovementI requires CatchI requires TackleI }
	cycleDef cycle == 1
	
	initial i0
	final f0
state SUpdateWorldModel {
	}
	junction j0
	state SGoToGoal {
		entry $ doMove ( wm . goalCenter )
	}
	junction j1
	state SdoCatch {
	}
	junction j2
	state SClearBall {
	}
	state SdoTackle {
	}
	state sBodyIntercept {
	}
	transition t0 {
		from i0
		to SUpdateWorldModel
	}
	transition t1 {
		from SUpdateWorldModel
		to SUpdateWorldModel
		condition not $ updateWorldModel
		action exec
	}
	transition t2 {
		from SUpdateWorldModel
		to j0
		condition $ updateWorldModel ? wm
	}
	transition t3 {
		from j0
		to f0
		condition wm . gameMode . gameMode != "Play on"
	}
	transition t4 {
		from j0
		to SGoToGoal
		condition wm . gameMode . gameMode == "Penalty Setup" \/ wm . gameMode . gameMode == "Penalty Ready" \/ wm . gameMode . gameMode == "Penalty Score" \/ wm . gameMode . gameMode == "Penalty Miss"
	}
transition t5 {
		from SGoToGoal
		to j1
	}
	transition t6 {
		from j1
		to SUpdateWorldModel
		condition not wm . inGoalCenter
		action exec
	}
transition t7 {
		from j1
		to SdoCatch
		condition wm . inGoalCenter /\ wm . catchable /\ wm . isInOurPenaltyArea
	}
	transition t8 {
		from SdoCatch
		to SUpdateWorldModel
	}
	transition t9 {
		from j1
		to j2
		condition not wm . catchable \/ not wm . isInOurPenaltyArea
	}
	transition t10 {
		from j2
		to SClearBall
	condition wm . kickable
	}
	transition t11 {
		from j2
		to SdoTackle
	condition wm . tackleProbability >= 30
	}
transition t12 {
		from j2
		to sBodyIntercept
		condition wm . cyclesToGoalieReachBall - 1 <= wm . cyclesToOpponentReachBall
	}
} 


module Sim2DModule {
	robotic platform Servidor {
		provides MovementI
		provides CatchI
		provides TackleI
		uses UpdateWorldI 
	}
	
	cref ctrl_ref0 = Goalie
	cycleDef cycle == 1
	connection Servidor on updateWorldModel to ctrl_ref0 on updateWorldModel ( _async )
}














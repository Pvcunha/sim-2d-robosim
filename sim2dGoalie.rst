package Goalie


stm GoalieStm {
	var wm : server::WorldModel
	var insideGoalieArea : boolean
	var bodyInterceptAct : boolean
	var isTacklePossible : boolean
	var blockPoint : server::Point
	input context {  uses server::UpdateWorldModelGoalieI  }
	output context { requires server::MovementI requires server::CatchI requires server::TackleI requires server::ClearBallI requires server::BodyInterceptI }
	cycleDef cycle == 1
	
	initial i0
	final f0
state SUpdateWorldModel {
	}
	junction j0

	state SdoCatch {
		entry insideGoalieArea = isInOurPenaltyArea ( wm . ball )
	}

	state SClearBall {
	}
	state SdoTackle {
		entry isTacklePossible = checkTackle (wm . tackleProbability)
	}
	state sBodyIntercept {
		entry bodyInterceptAct = checkBody (wm.ball); blockPoint = calculateBlockPoint(wm.ball, wm.goalPosition)
	}
	junction j3
	junction j4
	junction j5
	junction j6
	junction j7
	transition t0 {
		from i0
		to SUpdateWorldModel
	}
	transition t1 {
		from SUpdateWorldModel
		to SUpdateWorldModel
		condition not $ updateWorldModelGoalie
		action exec
	}
	transition t2 {
		from SUpdateWorldModel
		to j0
		condition $ updateWorldModelGoalie ? wm
	}
	transition t3 {
		from j0
		to f0
		condition wm . gameMode != "Play on"
	}
	transition t4 {
		from j0
		to SdoCatch
		condition wm . gameMode == "Play on"
	}
transition t8 {
		from j3
		to SUpdateWorldModel
		action exec
	}
	transition t5 {
		from SdoCatch
		to j4
	}
	transition t13 {
		from j4
		to j3
		condition wm . catchable /\ insideGoalieArea
		action $ doCatch ( )
	}
transition t14 {
		from j4
		to SClearBall
		condition not ( wm . catchable /\ insideGoalieArea )
	}
transition t15 {
		from SClearBall
		to j5
	}
	transition t16 {
		from j5
		to j3
		condition wm . isKickable
		action $ doClearBall ( )
	}
	transition t17 {
		from j5
		to SdoTackle
		condition not wm . isKickable
	}
	transition t6 {
		from SdoTackle
		to j6
	}
	transition t10 {
		from j6
		to j3
		condition isTacklePossible
		action $ doTackle ( )
	}
	transition t18 {
		from j6
		to sBodyIntercept
		condition 
	not isTacklePossible
	}
	transition t7 {
		from sBodyIntercept
		to j7
	}
	transition t11 {
		from j7
		to j3
		condition bodyInterceptAct
		action $ doBodyIntercept ( )
	}
	transition t9 {
		from j7
		to j3
		condition 
		not bodyInterceptAct
		action 
	$  doMove ( blockPoint )
	}
}

function checkBody(ball : server::Point) : boolean {}
function isInOurPenaltyArea(ball : server::Point) : boolean { }
function checkTackle(prob : real) : boolean { }
function calculateBlockPoint(ball : server::Point , goalPos : server::Point) : server::Point { }










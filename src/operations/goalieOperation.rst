package operations

operation DoGoalie ( wm: server::WorldModel) {
	var insideGoalieArea : boolean
	var bodyInterceptAct : boolean
	var isTacklePossible : boolean
	var blockPoint : server::Point
	initial i0
	final f0

	state SdoCatch {
		entry insideGoalieArea = isInOurPenaltyArea ( wm . ball )
	}
	
	state SClearBall {
		
	}

	junction j0
	junction j1
	
	junction j2
	state SdoTackle {
		entry isTacklePossible = checkTackle ( wm . tackleProbability )
	}
	junction j3
	state doMove {
		entry blockPoint = calculateBlockPoint ( wm . ball , wm . goalPosition )
	}
	
	transition t0 {
		from i0
		to SdoCatch
	}
	
	transition t1 {
		from SdoCatch
		to j0
	}
	
	transition t2 {
		from j0
		to j1
	condition wm . catchable /\ insideGoalieArea
		action $ doCatch ( )
	}
	transition t3 {
		from j0
		to SClearBall
		condition not ( wm . catchable /\ insideGoalieArea )
	}
	transition t4 {
		from j1
		to f0
		action exec
	}
	transition t5 {
		from SClearBall
		to j2
	}
	transition t6 {
		from j2
		to j1
		condition wm . isKickable
		action $ doClearBall ( )
	}
	transition t7 {
		from j2
		to SdoTackle
		condition not wm . isKickable
	}
	transition t8 {
		from SdoTackle
		to j3
	}
	transition t9 {
		from j3
		to j1
		condition isTacklePossible
		action $ doTackle ( )
	}
	transition t10 {
		from j3
		to doMove
		condition not isTacklePossible
	}
	transition t11 {
		from doMove
		to j1
		action $ doMove ( blockPoint )
	}
	
	input context {  uses server::UpdateWorldModelGoalieI  }
	output context { requires server::MovementI requires server::CatchI requires server::TackleI requires server::ClearBallI requires server::BodyInterceptI }
}

function checkBody(ball : server::Point) : boolean {}
function isInOurPenaltyArea(ball : server::Point) : boolean { }
function checkTackle(prob : real) : boolean { }
function calculateBlockPoint(ball : server::Point , goalPos : server::Point) : server::Point { }

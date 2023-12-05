package kicker

stm KickerStm {
	var wm : server::WorldModel
	var canShoot : boolean
	input context {  uses server::UpdateWorldModelKickerI }
	output context { requires server::ShootI requires server::MovementI }
	cycleDef cycle == 1
	initial i0
	final f0

	state SGoToBall {
	}

	junction j0
	state SShoot {
		entry canShoot = canShootToGoal ( )
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
	
	$   updateWorldModelKicker ? wm
	}
	transition t12 {
		from sUpdateWorldModel
		to sUpdateWorldModel
		condition 
		not $ updateWorldModelKicker
		action 
	exec
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
		condition wm . gameMode == "Play on"
	}
	transition t10 {
		from j3
		to f0
		condition wm . gameMode != "Play on"
	}
transition t3 {
		from j0
		to j1
		condition 
		
		not wm . isKickable
		action $ doMove ( wm . ball )
	}
transition t6 {
		from j2
		to j1
		condition canShoot
		action $ doShoot ( )
	}
}

function canShootToGoal(): boolean { }

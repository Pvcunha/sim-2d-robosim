interface WorldModelI {
}

robotic platform rcssserver {
}

controller Goalie {
	uses WorldModelI 
	event worldModel
	sref stm_ref0 = GoalieSTM
	cycleDef cycle == 1
connection Goalie on worldModel to stm_ref0 on worldModel
}

stm GoalieSTM {
	input context { uses WorldModelI event worldModel }
	output context {}
	cycleDef cycle == 1
	initial i0
	final f0
	transition t0 {
		from i0
		to f0
	}
}


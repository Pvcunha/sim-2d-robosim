interface WorldModelI {
}

robotic platform rcssserver {
}

controller Goalie {
	uses WorldModelI event UpdateCycle
	sref stm_ref0 = GoalieSTM
	cycleDef cycle == 1
}

stm GoalieSTM {
	input context { uses WorldModelI }
	output context {}
	cycleDef cycle == 1
	initial i0
	final f0
	transition t0 {
		from i0
		to f0
	}
}


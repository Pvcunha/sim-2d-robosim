package Router


stm RouterStm {
	input context {  uses server::UpdateWorldModelI  }
	output context { uses server::UpdateWorldModelKickerI uses server::UpdateWorldModelGoalieI }
	cycleDef cycle == 1
	
	initial i0
	final f0
	
	state sUpdateWorldModel {
			
	}
	
	state sSendKickerWorldModel {  }

	state sSendGoalieWorldModel { }
	
	transition t0 {
		from i0
		to sUpdateWorldModel
	}
	
	transition t1 {
		from sUpdateWorldModel
		to sUpdateWorldModel
		condition not $ updateWorldModel
		action exec
	}
	
	transition t2 {
		from sUpdateWorldModel
		to sSendKickerWorldModel
		action $ updateWorldModelKicker
	}
	
	transition t3 {
		from sSendKickerWorldModel
		to sSendGoalieWorldModel
		action $ updateWorldModelGoalie
	}
	
	transition t4 {
		from sSendGoalieWorldModel
		to f0
		action exec
	}
	
}











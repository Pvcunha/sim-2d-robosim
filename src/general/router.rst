package general

interface iOperations {
	doGoalie(wm: server::WorldModel)
	doKicker(wm: server::WorldModel)
}

interface UpdateWorldModelI {
	event updateWorldModel : server::WorldModel
}

stm Router {
	var foo : string
	var wm : server::WorldModel
	input context { uses server::UpdateWorldModelI }
	output context { requires iOperations }
	cycleDef cycle == 1
	initial i0

	state receiveWorldModel {
	}

	transition t0 {
		from i0
		to receiveWorldModel
	}
	transition t1 {
		from receiveWorldModel
		to receiveWorldModel
		condition 
	$  updateWorldModel ? wm
		action if wm . playerType == 0 then operations::doGoalie(wm) else operations::doKicker( wm ) end
	}
}


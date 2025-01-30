package general

interface iOperations {
	doGoalie(agent: server::PlayerAgent)
	doKicker(agent: server::PlayerAgent)
}
 
interface rcvMessage {
	event updatePlayerAgent : server::PlayerAgent
}

stm SamplePlayer {
	var agent: server::PlayerAgent
	input context { uses rcvMessage }
	output context { requires iOperations }
	cycleDef cycle == 1
	initial i0

	state receivePlayerAgent {
	}
	state CallAgent {
		entry if agent . goalie then operations::doGoalie ( agent ) else operations::doKicker ( agent ) end
	}

	transition t0 {
		from i0
		to receivePlayerAgent
	}
	transition t1 {
		from receivePlayerAgent
		to CallAgent
		condition 
	$  updatePlayerAgent ? agent
		
	}
transition t2 {
		from receivePlayerAgent
		to receivePlayerAgent
		condition not $  updatePlayerAgent
		action exec
	}
transition t3 {
		from CallAgent
		to receivePlayerAgent
	action exec
	}
}


package operations

operation doKicker ( wm : server::WorldModel ) {
	input context {  }
	output context { requires server::MovementI requires server::CatchI requires server::TackleI requires server::ClearBallI requires server::BodyInterceptI }
}

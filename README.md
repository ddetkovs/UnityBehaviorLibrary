Unity Behavior Library
================

UnityBehaviorLibrary is a framework for creating behavior trees for game AI. 

Changes
-------

RootSelector has been refactored to IndexSelector. It works exactly the same, just has a more appropriate name.

Behavior has had an additional constructor added that allows BehaviorComponent objects to be used rather than just RootSelector/IndexSelector objects.

Merged in new Repeater, Succeeder, RepeatUntilFail Decorators.


Utilities
---------

Added Utility components, see Utility Test cases for test examples.

The basic point is to use a vector of floating numbers representing weights/values that will be paired with a BehaviorComponent object. When a UtilitySelector is called, it will execute a function that returns a UtilityVector that will then be compared against the BehaviorComponents' paired vectors (via a dot product) and select the pair that best matches and execute its Behavior. 


Example
-------

It is a fork of https://github.com/NetGnome/BehaviorLibrary

It is simple to use and with that simplicity comes performance.

This means that it makes use of .NET 2.0 and so some adjustments have been made.

	//setup all coniditionals and their delegate functions
	Conditional tooClose = new Conditional(isTooClose);
	Conditional targetMoved = new Conditional(hasTargetMoved);
	Conditional pathFound = new Conditional(hasPathBeenFound);
	Conditional reachedCell = new Conditional(hasReachedCell);
	Conditional reachedTarget = new Conditional(hasReachedTarget);
	Conditional isNewPath = new Conditional(hasNewPath);

	//setup all actions and their delegate functions
	BehaviorAction moveToCell = new BehaviorAction(moveTowardsCell);
	BehaviorAction calcPath = new BehaviorAction(calculatePath);
	BehaviorAction initPathfinder = new BehaviorAction(initializePathfinder);
	BehaviorAction getNextCell = new BehaviorAction(getNextPathCell);
	BehaviorAction setPath = new BehaviorAction(setNewPath);
	BehaviorAction getPath = new BehaviorAction(getCurrentPath);
	BehaviorAction updatePosition = new BehaviorAction(updateTargetPosision);
	BehaviorAction reset = new BehaviorAction(resetPathfinder);
	BehaviorAction animate = new BehaviorAction(updateAnimation);

	//setup an initilization branch
	Sequence initialize = new Sequence(initPathfinder, calcPath);

	//if the target has moved, reset and calculate a new path
	Selector ifMovedCreateNewPath = new Selector(new Inverter(targetMoved), new Inverter(reset), calcPath);
	Selector ifPathFoundGetPath = new Selector(new Inverter(pathFound), getPath);
	Selector ifPathNewUseIt = new Selector(new Inverter(isNewPath), setPath);
	Selector ifReachedCellGetNext = new Selector(new Inverter(reachedCell), getNextCell);
	Selector ifNotReachedTargetMoveTowardsCell = new Selector(reachedTarget, moveToCell);
            
	//follow target so long as you're not too close and then animate
	Sequence follow = new Sequence(new Inverter(tooClose), updatePosition, ifMovedCreateNewPath, ifPathFoundGetPath, ifPathIsNewUseIt, ifReachedCellGetNext, ifNotReachedTargetMoveTowardsCell, animate);

	//setup root node, choose initialization phase or pathing/movement phase
	IndexSelector root = new IndexSelector(switchBehaviors, initialize, follow);

	//set a reference to the root
	Behavior behavior = new Behavior(root);
	
	//to execute the behavior
	behavior.Behave();
		


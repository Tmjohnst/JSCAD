function Tool(){
	//tool constructor
	this.finished = false;
	this.hovering = false;
	this.dim = false;
	this.parents = new Array();
	this.children = new Array();
	this.deleted = false;

	this.constraints = {
		horizontal: false,
		horizontalTo: {},
		vertical: false,
		verticalTo: {},
		radius: false,
		radiusTo: {},
		radiusDim: 0,
		length: false,
		lengthTo: {},
		lengthDim: 0,
		fixed: false,
		fixedTo: {x:0, y:0}
	}
	
	this.addParent = function(p){
		var alreadyParent = false;
		for (var i=0; i<this.parents.length; i++) {
			if (p === this.parents[i]) { alreadyParent = true; }
		}
		if (!alreadyParent) { this.parents.push(p); }
	}

	this.checkChildren = function(){
		for (var i=0; i<this.children.length; i++) {
			if (this.children[i].deleted) { removeChild(this.children[i]); }
		}
	}

	this.checkParents = function(){
		//should be a toggleble option
		if (this.parents.length == 0) { this.delete(); }
	}

	this.notifyParentsToCheckChildren = function(){
		for (var i=0; i<this.parents.length; i++) {
			this.parents[i].checkChildren();
		}
	}

	this.addChild = function(c){
		var alreadyChild = false;
		for (var i=0; i<this.children.length; i++) {
			if (c === this.children[i]) { alreadyChild = true; }
		}
		if (!alreadyChild) { this.children.push(c); }
	}

	this.getChildren = function(){
		return this.children;
	}

	this.getParents = function(){
		return this.parents;
	}

	this.printChildren = function(){
		childrenPrint = "Children: ";
		for (var i=0; i<this.children.length; i++) {
			childrenPrint += " [" + this.children[i].constructor.name + "], ";
		}
		console.log(childrenPrint);
	}

	this.printParents = function(){
		parentsPrint = "Parents: ";
		for (var i=0; i<this.parents.length; i++) {
			parentsPrint += " [" + this.parents[i].constructor.name + "], ";
		}
		console.log(parentsPrint);
	}

	this.removeParent = function(p){
		for (var i=0; i<this.parents.length; i++) {
			if (p === this.parents[i]) { this.parents.splice(i, 1); }
		}
		
		this.checkParents();
	}

	this.removeChild = function(c){
		for (var i=0; i<this.children.length; i++) {
			if (c === this.children[i]) { this.children.splice(i, 1); }
		}
	}

	this.removeChildren = function(){
		for (var i=0; i<this.children.length; i++) {
			this.children.splice(i, 1);
		}
	}

	this.removeParents = function(){
		for (var i=0; i<this.parents.length; i++) {
			this.parents.splice(i, 1);
		}
		
		//should be a toggleble option
		if (this.parents.length == 0) { this.delete(); }
	}

	this.between = function(a,b,c){
		if (a > b) {
			return (c <= a && c >= b);
		} else {
			return (c <= b && c >= a);
		}
	}

	this.hover = function(){ this.hovering = true; }
	this.unhover = function(){ this.hovering = false; }
	
	//Functions specific to the tool
	this.show = function(){}
	this.isNear = function(){}
	this.select = function(){}
	this.deselect = function(){}
	this.isSelected = function(){}
	this.delete = function(){ this.deleted = true; console.log("base Tool delete"); }
	this.getVertexes = function(){}

	this.start = function(x,y){}
	this.step = function(x,y){}
	this.end = function(x,y){}
	
	this.hover = function(){ this.hovering = true; }
	this.unhover = function(){ this.hovering = false; }
}
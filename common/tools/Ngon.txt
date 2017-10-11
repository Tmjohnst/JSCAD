function Ngon(v1, v2){
	Tool.call(this);
	var vertex1Set = false;
	var vertex2Set = false;
	this.vertex1 = v1 || {};
	this.vertex2 = v2 || {};
	this.sides = new Array();
	this.numSides = 5;
	this.offset = 0;

	if (v1) { this.vertex1Set = true; this.vertex1.addParent(this); }
	if (v2) { this.vertex2Set = true; this.vertex2.addParent(this); }

	this.show = function(){	
		//if(scrollup) { this.numSides++; }
		//if(scrolldown && this.numSides > 3) { this.numSides--; }
		
		if (this.vertex1Set && this.vertex2Set) {
			//have to recalculate if the offset is different
			var newOff = Math.atan2(this.vertex2.x - this.vertex1.x, this.vertex2.y - this.vertex1.y);
			if (this.offset != newOff) { 
				this.offset = newOff;
				this.generatePoints(this.vertex1.x, this.vertex1.y, this.numSides, this.vertex1.dist(new Vector(this.vertex2.x, this.vertex2.y)), this.offset);
			}

			if (this.vertex1.selected && this.vertex2.selected){
			} else if (this.hovering) {
			}

			for (var i=0; i<this.sides.length; i++){ this.sides[i].show(); }

		} else if(this.vertex1Set && !this.vertex2Set){
			this.vertex2.set(getMousePos().x, getMousePos().y);
			this.offset = Math.atan2(this.vertex2.x - this.vertex1.x, this.vertex2.y - this.vertex1.y);
			this.generatePoints(this.vertex1.x, this.vertex1.y, this.numSides, this.vertex1.dist(new Vector(this.vertex2.x, this.vertex2.y)), this.offset);
			for (var i=0; i<this.sides.length; i++){ this.sides[i].show(); }
			this.vertex2.show();
		}
	}
	
	this.checkChildren = function(){
		//this is redundant code, but I don't know how to use a "super()" function
		for (var i=0; i<this.children.length; i++) {
			if (this.children[i].deleted) { removeChild(this.children[i]); }
		}
		
		for (var i=0; i<this.sides.length; i++){
			if (this.sides[i].deleted){
				this.sides[i].removeParent(this);
				this.sides.splice(i, 1);
			}
		}
	}

	this.generatePoints = function(x, y, sides, radius, offset){      
    		var angle = 2 * Math.PI / sides;
    		for (var i=0; i<sides; i++) {
			if(this.sides[i]){
        			this.sides[i].vertex1.x = x + radius * Math.sin((i * angle) + offset);
        			this.sides[i].vertex1.y = y + radius * Math.cos((i * angle) + offset);
        		} else {
				var sx = x + radius * Math.sin((i * angle) + offset);
				var sy = y + radius * Math.cos((i * angle) + offset);
				var v1 = new Vertex(sx, sy);
				pushVertex(v1);
				var v2 = {};
				if (i>0) { v2 = this.sides[i-1].vertex1; }
				else { v2 = this.vertex2; }
				var l = new Line(v1, v2);
				l.addParent(this);
				this.sides.push(l);
			}
    		}

		for (var i=this.sides.length-1; i>sides; i--) {
			this.sides[i].delete();
			this.sides.splice(i,1);
		}

		if(!this.sides[sides]){
			var l = new Line(this.sides[sides-1].vertex1, this.vertex2);
			l.addParent(this);
			this.sides.push(l);
		}
	}

	this.hover = function(){ 
		this.hovering = true;
		for (var i=0; i<this.sides.length; i++){
			this.sides[i].hover();
		} 
	}

	this.unhover = function(){ 
		this.hovering = false;
		for (var i=0; i<this.sides.length; i++){
			this.sides[i].unhover();
		} 
	}

	this.isNear = function(x,y,dist){
		for (var i=0; i<this.sides.length; i++){
			if (this.sides[i].isNear(x,y,dist)) { return true; }
		}
		return false;
	}

	this.dist = function(x,y){
		//TODO: calculate distance
	}
	
	this.select = function(){
		this.vertex1.select();
		for (var i=0; i<this.sides.length; i++){
			this.sides[i].select();
		}
	}

	this.delete = function(){
		//TODO: properly delete all segments
		this.vertex1.removeParent(this);
		this.vertex2.removeParent(this);
		deleteElement(this);
	}
		
	this.isSelected = function(){
		return (this.vertex1.isSelected() && this.vertex2.isSelected());
	}

	this.deselect = function(){
		this.vertex1.deselect();
		for (var i=0; i<this.sides.length; i++){
			this.sides[i].deselect();
		}
	}

	this.getVertexes = function(){
		var vertexes = new Array();
		if (this.vertex1Set) {vertexes.push(this.vertex1);}
		if (this.vertex2Set) {vertexes.push(this.vertex2);}
		return vertexes;
	}

	this.v1 = function() { return this.vertex1; }
	this.v2 = function() { return this.vertex2; }

	this.start = function(x,y){
		if (nearestVertex(x,y)) {
			this.vertex1 = nearestVertex(x,y);
		} else {
			this.vertex1 = new Vertex(x,y);
			pushVertex(this.vertex1);
		}

		this.vertex2 = new Vertex(x,y);

		this.vertex1.addParent(this);
		this.vertex1Set = true;
		this.vertex1.select();
	}

	this.end = function(x,y){
		this.v = nearestVertex(x,y);
		if (this.v) {
			this.vertex2 = this.v;
			this.vertex2Set = true;
		} else {
			this.vertex2 = new Vertex(x,y);
			//pushVertex(this.vertex2);
		}

		this.vertex2.addParent(this);
		this.vertex2Set = true;
		this.vertex1.deselect();
		this.vertex2.deselect();
		this.finished = true;
	}

	this.step = function(x,y){ //lines end after 1 step
		this.end(x,y);
	}
}
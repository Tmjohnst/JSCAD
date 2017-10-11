function Polygon(){
	Tool.call(this);
	this.polyLines = new Array();
	var v1;

	this.show = function(){
		if (mouseClick.rightDown && !this.finished) { 
			this.end(); 
		}
		//if (this.polyLines.length == 0) { this.v1.show(); }
		for (var i = 0; i < this.polyLines.length; i++) {
			this.polyLines[i].show();
		}
	}

	this.checkChildren = function(){
		//this is redundant code, but I don't know how to use a "super()" function
		for (var i=0; i<this.children.length; i++) {
			if (this.children[i].deleted) { removeChild(this.children[i]); }
		}
		
		for (var i=0; i<this.polyLines.length; i++){
			if (this.polyLines[i].deleted){
				this.polyLines[i].removeParent(this);
				this.polyLines.splice(i, 1);
			}
		}
	}

	this.isSelected = function(){
		var isSelected = false;
		for (var i=0; i<this.polyLines.length; i++){
			if (this.polyLines[i].isSelected()) { return true; }
		}
		return false;
	}

	this.select = function(){
		for (var i=0; i<this.polyLines.length; i++){
			this.polyLines[i].select();
		}
	}

	this.deselect = function(){
		for (var i=0; i<this.polyLines.length; i++){
			this.polyLines[i].deselect();
		}
	}

	this.isNear = function(x,y,dist){
		for (var i=0; i<this.polyLines.length; i++){
			if (this.polyLines[i].isNear(x,y,dist)) { return true; }
		}
		return false;
	}

	this.delete = function(){
		for (var i=0; i<this.polyLines.length; i++){
			this.polyLines[i].removeParent(this);
			this.polyLines[i].delete();
		}
		deleteElement(this);
	}

	this.hover = function(){ 
		this.hovering = true;
		for (var i=0; i<this.polyLines.length; i++){
			this.polyLines[i].hover();
		} 
	}

	this.unhover = function(){ 
		this.hovering = false;
		for (var i=0; i<this.polyLines.length; i++){
			this.polyLines[i].unhover();
		} 
	}

	this.getVertexes = function(){
		var vertexes = new Array();
		vertexes.push(this.v1);
		for (var i = 0; i<this.polyLines.length; i++) {
			vertexes.push(this.polyLines[i].v2());
		}
		return vertexes;
	}

	this.start = function(x,y){
		if (nearestVertex(x,y)) {
			this.v1 = nearestVertex(x,y);
		} else {
			this.v1 = new Vertex(x,y);
			pushVertex(this.v1);
		}
		this.v1.select();
	}

	this.end = function(x,y){
		var d = new Line(this.polyLines[this.polyLines.length-1].v2(), this.v1);
		d.addParent(this);
		this.polyLines.push(d);
		this.polyLines[this.polyLines.length-1].deselect();
		this.finished = true;
	}

	this.step = function(x,y){
		this.a = {};
		if (nearestVertex(x,y)) {
			this.a = nearestVertex(x,y);
		} else {
			this.a = new Vertex(x,y);
			pushVertex(this.a);
		}

		if (this.polyLines.length == 0) {
			var d  = new Line(this.v1, this.a);
			d.addParent(this);
			this.polyLines.push(d);
			this.v1.deselect();
			this.a.select();
		} else {
			var d = new Line(this.polyLines[this.polyLines.length-1].v2(), this.a);
			d.addParent(this);
			this.polyLines.push(d);
			this.polyLines[this.polyLines.length-1].deselect();
			this.a.select();
		}
	}
}
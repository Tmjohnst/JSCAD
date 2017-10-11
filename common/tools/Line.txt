function Line(v1, v2){
	Tool.call(this);
	var vertex1Set = false;
	var vertex2Set = false;
	this.vertex1 = v1 || {};
	this.vertex2 = v2 || {};

	if (v1) { this.vertex1Set = true; this.vertex1.addParent(this); }
	if (v2) { this.vertex2Set = true; this.vertex2.addParent(this); }

	this.show = function(){	
		if (this.vertex1Set && this.vertex2Set) {
			if (this.vertex1.selected && this.vertex2.selected){
				ctx.beginPath();
				ctx.moveTo(this.vertex1.x, this.vertex1.y);
				ctx.lineTo(this.vertex2.x, this.vertex2.y);
				ctx.lineWidth = 5;
				ctx.strokeStyle = '#2BD1FF';
				ctx.globalAlpha = 0.5;
				ctx.stroke();
				ctx.globalAlpha = 1;
			} else if (this.hovering) {
				ctx.beginPath();
				ctx.moveTo(this.vertex1.x, this.vertex1.y);
				ctx.lineTo(this.vertex2.x, this.vertex2.y);
				ctx.lineWidth = 8;
				ctx.strokeStyle = '#2BD1FF';
				ctx.globalAlpha = 0.25;
				ctx.stroke();
				ctx.globalAlpha = 1;
			}

			ctx.beginPath();
			ctx.moveTo(this.vertex1.x, this.vertex1.y);
			ctx.lineTo(this.vertex2.x, this.vertex2.y);
			ctx.lineWidth = 1;
			ctx.strokeStyle = '#000000';
			ctx.stroke();
		} else if(this.vertex1Set && !this.vertex2Set){
			this.vertex2.set(getMousePos().x, getMousePos().y);
			ctx.beginPath();
			ctx.moveTo(this.vertex1.x, this.vertex1.y);
			ctx.lineTo(this.vertex2.x, this.vertex2.y);
			ctx.lineWidth = 1;
			ctx.strokeStyle = '#000000';
			ctx.stroke();

			this.vertex2.show();
		}
	}

	this.getSlope = function(){
		return (this.vertex2.y-this.vertex1.y)/(this.vertex2.x-this.vertex1.x);
	}

	this.isNear = function(x,y,dist){
		var distAC = Math.sqrt((x-this.vertex1.x)*(x-this.vertex1.x) + (y-this.vertex1.y)*(y-this.vertex1.y));
		var distAB = Math.sqrt((this.vertex2.x-this.vertex1.x)*(this.vertex2.x-this.vertex1.x) + (this.vertex2.y-this.vertex1.y)*(this.vertex2.y-this.vertex1.y));
		var distBC = Math.sqrt((x-this.vertex2.x)*(x-this.vertex2.x) + (y-this.vertex2.y)*(y-this.vertex2.y));
		return distAC + distBC - distAB < dist;
	}

	this.dist = function(x,y){
		var distAC = Math.sqrt((x-this.vertex1.x)*(x-this.vertex1.x) + (y-this.vertex1.y)*(y-this.vertex1.y));
		var distAB = Math.sqrt((this.vertex2.x-this.vertex1.x)*(this.vertex2.x-this.vertex1.x) + (this.vertex2.y-this.vertex1.y)*(this.vertex2.y-this.vertex1.y));
		var distBC = Math.sqrt((x-this.vertex2.x)*(x-this.vertex2.x) + (y-this.vertex2.y)*(y-this.vertex2.y));
		return distAC + distBC - distAB;
	}
	
	this.select = function(){
		this.vertex1.select();
		this.vertex2.select();
	}

	this.delete = function(){
		this.deleted = true;
		this.notifyParentsToCheckChildren();
		
		if (this.isSelected()){
			if (!this.vertex1.deleted) { this.vertex1.delete(); }
			if (!this.vertex2.deleted) { this.vertex2.delete(); }
		} else {
			this.vertex1.removeParent(this);
			this.vertex2.removeParent(this);
		}
		
		
		deleteElement(this);
	}
		
	this.isSelected = function(){
		return (this.vertex1.isSelected() && this.vertex2.isSelected());
	}

	this.deselect = function(){
		this.vertex1.deselect();
		this.vertex2.deselect();
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
			pushVertex(this.vertex2);
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
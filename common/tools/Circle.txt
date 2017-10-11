function Circle(v1, v2){
	Tool.call(this);
	var vertex1Set = false;
	var vertex2Set = false;
	this.vertex1 = v1 || {};
	this.vertex2 = v2 || {};

	if (v1) { this.vertex1Set = true; this.vertex1.addParent(this); }
	if (v2) { this.vertex2Set = true; this.vertex2.addParent(this); }

	var radius;

	this.show = function(){
		if (this.vertex1Set && this.vertex2Set) {
			if (this.vertex1.selected && this.vertex2.selected){ 
				ctx.beginPath();
				ctx.strokeStyle = '#2BD1FF';
				this.radius = Math.hypot(this.vertex1.x - this.vertex2.x, this.vertex1.y - this.vertex2.y);
				ctx.arc(this.vertex1.x,this.vertex1.y,this.radius,0,2*Math.PI, false);
				ctx.lineWidth = 5;
				ctx.globalAlpha = 0.5;
				ctx.stroke();
				ctx.globalAlpha = 1;
			} else if(this.hovering) {
				ctx.beginPath();
				this.radius = Math.hypot(this.vertex1.x - this.vertex2.x, this.vertex1.y - this.vertex2.y);
				ctx.arc(this.vertex1.x,this.vertex1.y,this.radius,0,2*Math.PI, false);
				ctx.lineWidth = 8;
				ctx.strokeStyle = '#2BD1FF';
				ctx.globalAlpha = 0.25;
				ctx.stroke();
				ctx.globalAlpha = 1;
			}

			ctx.beginPath();
			ctx.strokeStyle = '#000000';
			this.radius = Math.hypot(this.vertex1.x - this.vertex2.x, this.vertex1.y - this.vertex2.y);
			ctx.arc(this.vertex1.x,this.vertex1.y,this.radius,0,2*Math.PI, false);
			ctx.lineWidth = 1;
			ctx.stroke();
		} else if(this.vertex1Set && !this.vertex2Set){

			var compX = Math.round(getMousePos().x/gridSize)*gridSize;
			if (!snap) { compX = getMousePos().x; }
			var compY = Math.round(getMousePos().y/gridSize)*gridSize;
			if (!snap) { compY = getMousePos().y; }

			this.radius = Math.hypot(this.vertex1.x - compX, this.vertex1.y - compY);
			ctx.beginPath();
			ctx.strokeStyle = '#000000';
			ctx.arc(this.vertex1.x,this.vertex1.y,this.radius,0,2*Math.PI, false);
			ctx.lineWidth = 1;
			ctx.stroke();

			this.vertex2.set(getMousePos().x, getMousePos().y);
			this.vertex2.show();
		}
	}

	this.isNear = function(x,y,dist){
		var d = Math.sqrt((this.vertex1.x - x)*(this.vertex1.x - x) + (this.vertex1.y - y)*(this.vertex1.y - y));
		return (d > (this.radius - dist))&&(d < (this.radius + dist));
	}

	this.isSelected = function(){
		return (this.vertex1.isSelected() && this.vertex2.isSelected());
	}
	
	this.delete = function(){
		this.vertex1.removeParent(this);
		this.vertex2.removeParent(this);
		deleteElement(this);
	}

	this.deselect = function(){
		this.vertex1.deselect();
		this.vertex2.deselect();
	}

	this.select = function(){
		this.vertex1.select();
		this.vertex2.select();
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
		}

		var compX = Math.round(x/gridSize)*gridSize;
		if (!snap) { compX = x; }
		var compY = Math.round(y/gridSize)*gridSize;
		if (!snap) { compY = y; }

		if(!(this.vertex1.x == compX && this.vertex1.y == compY) && !this.vertex2Set){
			this.vertex2 = new Vertex(x,y)
			this.radius = Math.hypot(this.vertex1.x - this.vertex2.x, this.vertex1.y - this.vertex2.y);
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
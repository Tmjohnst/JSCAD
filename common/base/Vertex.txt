function Vertex(x, y){
	Tool.call(this); //vertex is a base, but the Tool has all the helper functions that a Vertex needs
	this.selected = false;
	this.vertexSize = 2;

	this.x = 0;
	this.y = 0;

	this.worldX = 0; //TODO: proper translation from grid coords to world coords (for zooming, saving, scaling, etc)
	this.worldY = 0; //TODO

	this.constraints = {
		horizontal: false,
		vertical: false,
		radius: false,
		radiusDim: 0,
		length: false,
		lengthDim: 0,
		fixed: false,
		fixedDim: {x:0, y:0}
	}

	if (snap) { this.x = Math.round(x/gridSize)*gridSize || 0; }
	else { this.x = x || 0; }

	if (snap) { this.y = Math.round(y/gridSize)*gridSize || 0; }
	else { this.y = y || 0; }

	this.selectX = x;
	this.selectY = y;
	this.lineWidth = 1;
	this.hovering = false; //shows when the mouse is hovering over the vertex
	this.parents = new Array();
	
	this.getX = function(){
		return this.x;
	}
	this.getY = function(){
		return this.y;
	}

	this.isNear = function(x,y,snapDist){
		if (Math.hypot(this.x - x, this.y - y) < snapDist) {
			return true;
		} else {
			return false;
		}
	}

	this.isContained = function(xa,ya,xb,yb){
		return (this.between(xa, xb, this.x) && this.between(ya, yb, this.y));
	}
	
	this.delete = function(){
		if (this.parents.length != 0) { 
			for (var i=this.parents.length-1; i>=0; i--) {
				if (!this.parents[i].deleted) { this.parents[i].delete(); }
				this.parents.splice(i, 1);
				console.log("told parent " + i + " to go delete themselves.");
			}
		}
		this.deleted = true;
		deleteVertex(this);
	}

	this.show = function(){
		ctx.beginPath();
		
		if (this.hovering){
			var r = this.vertexSize*3;
			ctx.globalAlpha = 0.25;
			ctx.beginPath();
			ctx.arc(this.x,this.y,r,0,2*Math.PI, false);
			ctx.fillStyle = '#2BD1FF';
			ctx.fill();
			ctx.stroke();
			ctx.globalAlpha = 1;
		}

		if (this.selected) {
			var r1 = this.vertexSize*3;
			ctx.globalAlpha = 0.5;
			ctx.beginPath();
			ctx.arc(this.x,this.y,r1,0,2*Math.PI, false);
			ctx.fillStyle = '#FF2A2A';
			ctx.fill();
			ctx.stroke();
			ctx.globalAlpha = 1;

			var r = this.vertexSize;
			ctx.beginPath();
			ctx.strokeStyle = '#424242';
			ctx.arc(this.x,this.y,r,0,2*Math.PI, false);
			ctx.lineStyle = 'red';
			ctx.lineWidth = this.lineWidth;
			ctx.fillStyle = '#55C8E8';
			ctx.fill();
			ctx.stroke();
		} else {
			var r = this.vertexSize;
			ctx.beginPath();
			ctx.strokeStyle = '#424242';
			ctx.arc(this.x,this.y,r,0,2*Math.PI, false);
			ctx.lineWidth = this.lineWidth;
			ctx.stroke();
		}
		ctx.stroke();
	}

	this.select = function(){
		if (!this.selected){
			SELECTED_VERTEXES++;
			this.selected = true;
		}
		this.selectX = this.x; //holds the spot where the vertex was first selected
		this.selectY = this.y;
	}

	this.setSelect = function(x, y){
		this.setX(this.selectX+x);
		this.setY(this.selectY+y);
	}

	this.isSelected = function(){
		return this.selected;
	}

	this.deselect = function(){
		if (this.selected) {
			SELECTED_VERTEXES--;
			this.selected = false;
		}
		this.selectX = this.x; //resets the spot where the vertex was first selected
		this.selectY = this.y;
	}

	this.toString = function(){
		return "(" + this.x + ", " + this.y + ") selected: " + this.selected; 
	}

	this.between = function(a,b,c){
		if (a > b) {
			return (c <= a && c >= b);
		} else {
			return (c <= b && c >= a);
		}
	}

	this.setX = function(x){
		if (snap) { this.x = Math.round(x/gridSize)*gridSize;}
		else { this.x = x;}
	}
	
	this.setY = function(y){
		if (snap) { this.y = Math.round(y/gridSize)*gridSize; }
		else { this.y = y; }
	}

	this.set = function(x,y){
		this.setX(x);
		this.setY(y);
	}

	this.dist = function(v){
		var x2 = (v.x - this.x) * (v.x - this.x);
		var y2 = (v.y - this.y) * (v.y - this.y);
		return Math.sqrt(x2+y2);
	}
}
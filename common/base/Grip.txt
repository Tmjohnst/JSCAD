function Grip(x, y){
	//Grip consists of a Vertex and 2 handles
	Vertex.call(this); //Grip is a base, but the Vertex has all the functions that a Grip needs

	var handles = new Array();
	handles[0] = {x:0, y:0, selectX:0, selectY:0}; //Each Grip has exactly 2 handles
	handles[1] = {x:0, y:0, selectX:0, selectY:0};

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


			//TODO: Grip hovering
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

			//TODO: Grip selected
		} else {
			var r = this.vertexSize;
			ctx.beginPath();
			ctx.strokeStyle = '#424242';
			ctx.arc(this.x,this.y,r,0,2*Math.PI, false);
			ctx.lineWidth = this.lineWidth;
			ctx.stroke();

			//TODO: Grip unselected
		}
		ctx.stroke();
	}

	this.select = function(){
		//TODO: Determine which part of the Grip was selected (if Vertex is selected, the vert and handles both move)

		if (!this.selected){
			SELECTED_VERTEXES++; //Grips can be counted as vertexes for selection I guess
			this.selected = true;
		}
		

		this.selectX = this.x; //holds the spot where the vertex was first selected
		this.selectY = this.y;
	}

	this.setSelect = function(x, y){
		//TODO: Figure out which part is selected, then set their X and Y

		this.setX(this.selectX+x);
		this.setY(this.selectY+y);
	}

	this.isSelected = function(){
		//TODO: Determine what constitutes "selected" (just the vertex? any part?)

		return this.selected;
	}

	this.deselect = function(){
		//TODO: Deselect all parts and reset the select X and Y

		if (this.selected) {
			SELECTED_VERTEXES--;
			this.selected = false;
		}
		this.selectX = this.x; //resets the spot where the vertex was first selected
		this.selectY = this.y;
	}

	this.toString = function(){
		//TODO: Add the grips to the string

		return "(" + this.x + ", " + this.y + ") selected: " + this.selected; 
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
		//TODO: Dist to Grip should return the dist to the nearest grip or vert

		var x2 = (v.x - this.x) * (v.x - this.x);
		var y2 = (v.y - this.y) * (v.y - this.y);
		return Math.sqrt(x2+y2);
	}
}
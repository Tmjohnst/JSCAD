function Dimension(){
	Tool.call(this);
	var element = {};
	var elementSet = false;
	var dimSet = false;
	this.selected = false;
	this.dimPos = {x:0, y:0};

	this.show = function(){	
		if (this.elementSet && this.dimSet) {
			switch (this.element.constructor.name) {
				case 'Line':
					var midX = (this.element.v1().x + this.element.v2().x)/2;
					var midY = (this.element.v1().y + this.element.v2().y)/2;
					ctx.moveTo(midX, midY);
					ctx.lineTo(this.dimPos.x, this.dimPos.y);
					ctx.stroke();
					break;
				default: break;
			}
		} else if (this.elementSet && !this.dimSet) {
			switch (this.element.constructor.name) {
				case 'Line':
					var m = -1/(this.element.getSlope());
					var midX = (this.element.v1().x + this.element.v2().x)/2;
					var midY = (this.element.v1().y + this.element.v2().y)/2;
					var b = midY - (m*midX);
					ctx.moveTo(midX, midY);

					ctx.lineTo(getMousePos().x, (getMousePos().y-b)/m);
					this.dimPos.x = getMousePos().x;
					this.dimPos.y = getMousePos().x*m+b;
					ctx.stroke();
					break;
				default: break;
			}
		}
	}

	this.isNear = function(x,y,dist){
		if (this.elementSet) {
			//var distAC = Math.sqrt((x-this.vertex1.x)*(x-this.vertex1.x) + (y-this.vertex1.y)*(y-this.vertex1.y));
			//var distAB = Math.sqrt((this.vertex2.x-this.vertex1.x)*(this.vertex2.x-this.vertex1.x) + (this.vertex2.y-this.vertex1.y)*(this.vertex2.y-this.vertex1.y));
			//var distBC = Math.sqrt((x-this.vertex2.x)*(x-this.vertex2.x) + (y-this.vertex2.y)*(y-this.vertex2.y));
			//return distAC + distBC - distAB < dist;
		} 
		return false;
	}

	this.pointNearest = function(x1, y1, x2, y2, x, y){
		var px = 0;
		var py = 0;
		
		return false;
	}
	
	this.select = function(){
		this.select = true;
	}

	this.delete = function(){
		deleteElement(this);
	}
		
	this.isSelected = function(){
		return this.select;
	}

	this.deselect = function(){
		this.select = false;
		this.element.deselect();
	}

	this.start = function(x,y){
		if (nearestElement(x,y)) {
			this.element = nearestElement(x,y);
			this.addParent(this.element); //make the dim the child of the element
			this.element.select();
			this.elementSet = true;
		}
	}

	this.end = function(x,y){
		this.element.deselect();
		this.dimSet = true;
		this.finished = true;
		console.log("end dim");
	}

	this.step = function(x,y){
		if (this.elementSet) { this.end(x,y); }
		else { this.start(x,y); console.log("start again");}
	}
}
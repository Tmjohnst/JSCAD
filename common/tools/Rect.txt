function Rect(){
	Tool.call(this);
	this.vertex1Set = false;
	this.vertex2Set = false;
	this.vertex1; //first click
	this.vertex2; //last click
	this.vertex3; //corner 1
	this.vertex4; //corner 2

	this.line1; //line v1 to v3
	this.line2; //line v1 to v4
	this.line3; //line v2 to v3
	this.line4; //line v2 to v4

	this.show = function(){
		if (this.vertex1Set && this.vertex2Set) { //drawing completed rect
			if(this.hovering){
				this.line1.hover();
				this.line2.hover();
				this.line3.hover();
				this.line4.hover();
			} else {
				this.line1.unhover();
				this.line2.unhover();
				this.line3.unhover();
				this.line4.unhover();
			}

			this.line1.show();
			this.line2.show();
			this.line3.show();
			this.line4.show();
		} else if(this.vertex1Set && !this.vertex2Set){ //drawing incomplete rect
			this.vertex2.set(getMousePos().x, getMousePos().y);
			this.vertex3.set(this.vertex1.x, getMousePos().y);
			this.vertex4.set(getMousePos().x, this.vertex1.y);

			//have to manually show the next 3 vertexes because they are not pushed into the vertex list yet
			this.vertex2.show();
			this.vertex3.show();
			this.vertex4.show();

			this.line1.show();
			this.line2.show();
			this.line3.show();
			this.line4.show();
		}
	}

	this.isNear = function(x,y,dist){
		return (this.line1.isNear(x,y,dist)||this.line2.isNear(x,y,dist)||this.line3.isNear(x,y,dist)||this.line4.isNear(x,y,dist));
	}

	this.delete = function(){
		this.line1.removeParent(this);
		this.line2.removeParent(this);
		this.line3.removeParent(this);
		this.line4.removeParent(this);
		deleteElement(this);
	}

	this.getVertexes = function(){
		var vertexes = new Array();
		vertexes.push(this.vertex1);
		vertexes.push(this.vertex2);
		vertexes.push(this.vertex3);
		vertexes.push(this.vertex4);
		return vertexes;
	}

	this.isSelected = function(){
		return (this.line1.isSelected()&&this.line2.isSelected()&&this.line3.isSelected()&&this.line4.isSelected());
	}

	this.select = function(){
		this.line1.select();
		this.line2.select();
		this.line3.select();
		this.line4.select();
	}

	this.deselect = function(){
		this.line1.deselect();
		this.line2.deselect();
		this.line3.deselect();
		this.line4.deselect();
	}


	this.start = function(x,y){
		//only the point for v1 is determined here, so we'll check the others later (they might combine)
		if (nearestVertex(x,y)) {
			this.vertex1 = nearestVertex(x,y);
		} else {
			this.vertex1 = new Vertex(x,y);
			pushVertex(this.vertex1);
		}		
		
		this.vertex2 = new Vertex();
		this.vertex3 = new Vertex();
		this.vertex4 = new Vertex();

		//temp set these lines to be reset later once they're set
		this.line1 = new Line(this.vertex1, this.vertex3); //vertical
		this.line2 = new Line(this.vertex1, this.vertex4); //horizontal
		this.line3 = new Line(this.vertex2, this.vertex3); //vertical
		this.line4 = new Line(this.vertex2, this.vertex4); //horizontal
		
		this.vertex1Set = true;
		this.vertex1.select();
	}

	this.end = function(x,y){
		//first, set all the vertexes (check for overlaps and use the overlapped vertex if it exists)
		if (nearestVertex(x,y)) {
			this.vertex2 = nearestVertex(x,y);
			this.vertex2Set = true;
		}

		//if it's not an overlap, it must be a new vertex
		if (!this.vertex2Set){
			this.vertex2 = new Vertex(x,y);
			pushVertex(this.vertex2);
		}

		//check vert3
		if (nearestVertex(this.vertex1.x,this.vertex2.y)) { 
			this.vertex3 = nearestVertex(this.vertex1.x,this.vertex2.y);
		} else {
			this.vertex3.set(this.vertex1.x,this.vertex2.y);
			pushVertex(this.vertex3);
		}
		
		//check vert4
		if (nearestVertex(this.vertex2.x,this.vertex1.y)) { 
			this.vertex4 = nearestVertex(this.vertex2.x,this.vertex1.y); 
		} else {
			this.vertex4.set(this.vertex2.x, this.vertex1.y);
			pushVertex(this.vertex4);
		}

		//reset all lines now that we know where they land - time to set up a big family
		this.line1 = new Line(this.vertex1, this.vertex3);
		this.line1.addParent(this);
		this.vertex1.addParent(this.line1)
		this.vertex3.addParent(this.line1)
		this.line2 = new Line(this.vertex1, this.vertex4);
		this.line2.addParent(this);
		this.vertex1.addParent(this.line2)
		this.vertex4.addParent(this.line2)
		this.line3 = new Line(this.vertex2, this.vertex3);
		this.line3.addParent(this);
		this.vertex2.addParent(this.line3)
		this.vertex3.addParent(this.line3)
		this.line4 = new Line(this.vertex2, this.vertex4);
		this.line4.addParent(this);
		this.vertex2.addParent(this.line4)
		this.vertex4.addParent(this.line4)

		//setting vertex2Set signals that the tool is done
		this.vertex2Set = true;
		this.vertex1.deselect();
		this.vertex2.deselect();
		this.finished = true;
	}

	this.step = function(x,y){ //rect ends after 1 step
		console.log("step");
		this.end(x,y);
	}
}
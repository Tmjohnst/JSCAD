function Arc(v1, v2, v3){
	//this is a 3-point arc, you place v1, then v2, then set the radius with v3
	Tool.call(this);
	var vertex1Set = false;
	var vertex2Set = false;
	var vertex3Set = false;

	this.vertex1 = v1 || {};
	this.vertex2 = v2 || {};
	this.vertex3 = v3 || {};

	if (v1) { this.vertex1Set = true; this.vertex1.addParent(this); }
	if (v2) { this.vertex2Set = true; this.vertex2.addParent(this); }
	if (v3) { this.vertex3Set = true; this.vertex3.addParent(this); }
	
	this.radius = 0;
	this.arcDraw = {x:0, y:0, r:0, sa:0, ea:0};

	this.show = function(){	
		if (this.vertex1Set && this.vertex2Set && this.vertex3Set) {
			this.arcDraw = this.arcCalc(this.vertex1, this.vertex2, this.vertex3);

			if (this.vertex1.selected && this.vertex2.selected && this.vertex3.selected){
				//selected and completed arc drawing selection blue
				ctx.beginPath();
				ctx.arc(this.arcDraw.x, this.arcDraw.y, this.arcDraw.r, this.arcDraw.sa, this.arcDraw.ea, this.arcDraw.cw);
				ctx.lineWidth = 5;
				ctx.strokeStyle = '#2BD1FF';
				ctx.globalAlpha = 0.5;
				ctx.stroke();
				ctx.globalAlpha = 1;

			} else if (this.hovering) {
				ctx.beginPath();
				ctx.arc(this.arcDraw.x, this.arcDraw.y, this.arcDraw.r, this.arcDraw.sa, this.arcDraw.ea, this.arcDraw.cw);
				ctx.lineWidth = 8;
				ctx.strokeStyle = '#2BD1FF';
				ctx.globalAlpha = 0.25;
				ctx.stroke();
				ctx.globalAlpha = 1;
			}
			
			ctx.beginPath();
			ctx.strokeStyle = '#000000';
			ctx.arc(this.arcDraw.x, this.arcDraw.y, this.arcDraw.r, this.arcDraw.sa, this.arcDraw.ea, this.arcDraw.cw);
			ctx.lineWidth = 1;
			ctx.stroke();

		} else if(this.vertex1Set && !this.vertex2Set && !this.vertex3Set){
			//drawing no arc, just second point and ghost line to it
			this.vertex2.set(getMousePos().x, getMousePos().y);
			ctx.beginPath();
			ctx.moveTo(this.vertex1.x, this.vertex1.y);
			ctx.lineTo(this.vertex2.x, this.vertex2.y);
			ctx.lineWidth = 2;
			ctx.globalAlpha = 0.25;
			ctx.stroke();
			ctx.globalAlpha = 1;
			this.vertex2.show();

		} else if(this.vertex1Set && this.vertex2Set && !this.vertex3Set){
			//drawing the addition of the last vertex
			this.vertex3.set(getMousePos().x, getMousePos().y);
			this.arcDraw = this.arcCalc(this.vertex1, this.vertex2, this.vertex3);
			ctx.beginPath();
			ctx.strokeStyle = '#000000';
			ctx.arc(this.arcDraw.x, this.arcDraw.y, this.arcDraw.r, this.arcDraw.sa, this.arcDraw.ea, this.arcDraw.cw);
			ctx.lineWidth = 1;
			ctx.stroke();
			this.vertex3.show();
			console.log("problem");
		}
	}

	this.isNear = function(x,y,dist){
		var arcFudge = 2; //fudging the distance because arcs are hard to click
		var dx = x - this.arcDraw.x;
		var dy = y - this.arcDraw.y;
		var d = Math.sqrt(dx*dx + dy*dy);
		var withinRadius = (d > (this.arcDraw.r - dist - arcFudge))&&(d < (this.arcDraw.r + dist + arcFudge));
		var angle = Math.atan2(dy,dx);
		var withinAngle = this.between(this.arcDraw.sa, this.arcDraw.ea, angle);
		return withinAngle && withinRadius;
	}

	this.delete = function(){
		this.vertex1.removeParent(this);
		this.vertex2.removeParent(this);
		this.vertex3.removeParent(this);
		deleteElement(this);
	}
	
	this.select = function(){
		this.vertex1.select();
		this.vertex2.select();
		this.vertex3.select();
	}
		
	this.isSelected = function(){
		return (this.vertex1.isSelected() && this.vertex2.isSelected() && this.vertex3.isSelected());
	}

	this.deselect = function(){
		this.vertex1.deselect();
		this.vertex2.deselect();
		this.vertex3.deselect();
	}

	this.getVertexes = function(){
		var vertexes = new Array();
		if (this.vertex1Set) {vertexes.push(this.vertex1);}
		if (this.vertex2Set) {vertexes.push(this.vertex2);}
		if (this.vertex3Set) {vertexes.push(this.vertex3);}
		return vertexes;
	}

	this.v1 = function() { return this.vertex1; }
	this.v2 = function() { return this.vertex2; }
	this.v3 = function() { return this.vertex3; }

	this.arcCalc = function(A, B, C){
		//calculate the centerpoint and radius of arc going through all 3 verts
		//use the determinant of the circle function
		//http://www.ambrsoft.com/TrigoCalc/Circle3D.htm

		var a = A.x*(B.y-C.y)-A.y*(B.x-C.x)+B.x*C.y-C.x*B.y,
		    b = (A.x*A.x+A.y*A.y)*(C.y-B.y)+(B.x*B.x+B.y*B.y)*(A.y-C.y)+(C.x*C.x+C.y*C.y)*(B.y-A.y),
		    c = (A.x*A.x+A.y*A.y)*(B.x-C.x)+(B.x*B.x+B.y*B.y)*(C.x-A.x)+(C.x*C.x+C.y*C.y)*(A.x-B.x),
		    d = (A.x*A.x+A.y*A.y)*(C.x*B.y-B.x*C.y)+(B.x*B.x+B.y*B.y)*(A.x*C.y-C.x*A.y)+(C.x*C.x+C.y*C.y)*(B.x*A.y-A.x*B.y),
		    x = -b/(2*a), //centerpoint x
		    y = -c/(2*a), //centerpoint y
		    r = Math.sqrt((b*b+c*c-4*a*d)/(4*a*a)),
		    sa = Math.atan2(A.y-y, A.x-x), // starting angle
		    ea = Math.atan2(B.y-y, B.x-x), // ending angle
		
		//this part taken from https://stackoverflow.com/a/33963353
		    cp = (C.x-A.x)*(B.y-A.y) - (C.y-A.y)*(B.x-A.x); 
		    cw = (cp < 0);
		
		return {x:x, y:y, r:r, sa: sa, ea: ea, cw:cw};
	}

	this.start = function(x,y){
		if (nearestVertex(x,y)) {
			this.vertex1 = nearestVertex(x,y);
		} else {
			this.vertex1 = new Vertex(x,y);
			pushVertex(this.vertex1);
		}

		this.vertex2 = new Vertex(x,y);
		this.vertex3 = new Vertex(x,y);

		this.vertex1.addParent(this);
		this.vertex1Set = true;
		this.vertex1.select();
	}

	this.step = function(x,y){
		if (this.vertex2Set) { 
			this.end(x,y);
		} else { //if the first 2 vertexes are already set, end with 1 more vert
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
				this.vertex2 = new Vertex(x,y);
				pushVertex(this.vertex2);
			}

			this.vertex2.addParent(this);
			this.vertex2Set = true;
		}
	}

	this.end = function(x,y){
		this.v = nearestVertex(x,y);
		if (this.v) {
			this.vertex3 = this.v;
			this.vertex3Set = true;
		}
		var compX = Math.round(x/gridSize)*gridSize;
		if (!snap) { compX = x; }
		var compY = Math.round(y/gridSize)*gridSize;
		if (!snap) { compY = y; }
		
		var onVert1 = (this.vertex1.x == compX && this.vertex1.y == compY);
		var onVert2 = (this.vertex2.x == compX && this.vertex2.y == compY);

		if(!(onVert1 && vert2) && !this.vertex3Set){
			this.vertex3 = new Vertex(x,y);
			pushVertex(this.vertex3);
		}

		this.vertex3.addParent(this);
		this.vertex3Set = true;
		
		this.vertex1.deselect();
		this.vertex2.deselect();
		this.vertex3.deselect();		

		this.finished = true;
		this.arcDraw = this.arcCalc(this.vertex1, this.vertex2, this.vertex3);
	}
}
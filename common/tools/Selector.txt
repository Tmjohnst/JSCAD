function Selector(){
	Tool.call(this);
	var selectedVertexes = new Array();

	var windowSet = false;
	var windowDrawing = false;

	this.vertex1Set = false;
	this.vertex1 = new Vertex();

	var refX = getMousePos().x;
	var refY = getMousePos().y;
	
	var moveVertexes = false;

	this.show = function(){
		for (var i=0;i<vertexes.length;i++){
			if (dragging && !ctrl){
				var contained = vertexes[i].isContained(this.vertex1.x, this.vertex1.y, getMousePos().x, getMousePos().y);
				if (contained){
					vertexes[i].select();
				} else if (shift) {
					var alreadyInSelectionSet = false;
					for (var j=0; j<selectedVertexes.length; j++){
						if (vertexes[i] == selectedVertexes[j]) {alreadyInSelectionSet = true}
					}
					if (!alreadyInSelectionSet) { vertexes[i].deselect(); }
				}
			} 
			if (vertexes[i].isSelected() && !dragging){ 
				vertexes[i].select(); 
			} //resets the selection position if dragging has happened

			if (dragging && SELECTED_VERTEXES > 0 && ctrl) {
				var dx = getMousePos().x - mouseClick.x;
				var dy = getMousePos().y - mouseClick.y;
				if (vertexes[i].isSelected()){ vertexes[i].setSelect(dx, dy); }
			}
		}
		
		if (ctrl && dblclick && SELECTED_VERTEXES > 0){
			var checkVerts = new Array();
			for (var j=0;j<vertexes.length;j++){
				if (vertexes[j].isSelected()){ checkVerts.push(vertexes[j]); }
			}

			for (var i=0;j<checkVerts.length;i++){
				var parents = checkVerts[i].getParents();
				for (var k=0;k<parents.length;k++){ parents[k].select; }
			}
		}

		if (enter) {
			for (var j=0;j<vertexes.length;j++){
				if (vertexes[j].isSelected()){ 
					vertexes[j].printParents();
					vertexes[j].deselect();
				}
			}
		}

		if (deleteKey) {
			for (var i=0;i<elements.length;i++){
				if (elements[i].isSelected()){ 
					elements[i].delete();
				}
			}
			for (var j=0;j<vertexes.length;j++){
				if (vertexes[j].isSelected()){ 
					vertexes[j].delete();
				}
			}
		}
		
		if (dragging && !ctrl) {
			ctx.beginPath();
			ctx.globalAlpha = 0.25;
			ctx.fillStyle = '#17bec1';
			ctx.fillRect(this.vertex1.x,this.vertex1.y,getMousePos().x-this.vertex1.x,getMousePos().y-this.vertex1.y);
			ctx.globalAlpha = 1;
			ctx.rect(this.vertex1.x,this.vertex1.y,getMousePos().x-this.vertex1.x,getMousePos().y-this.vertex1.y);
			ctx.lineWidth = 1;
			ctx.stroke();
		}
	}

	this.getVertexes = function(){}

	this.deselect = function(){}

	this.start = function(x,y){}

	this.end = function(x,y){
		this.finished = true;
	}

	this.step = function(x,y){
		this.vertex1.set(x,y);
		var nearAnything = false;

		if (!shift && !ctrl) {
			for (i=0;i<vertexes.length;i++){
				vertexes[i].deselect();
			}
		} else if (!ctrl) {
			for (i=0;i<vertexes.length;i++){
				if (vertexes[i].isSelected()) {
					selectedVertexes.push(vertexes[i]);
				}
			}
		}
		
		//-----------------
		//TODO: This part needs some work to make selection more predictable...
		for (var i=0;i<vertexes.length;i++) {
			var vert = vertexes[i];
			if (vert.isNear(getMousePos().x, getMousePos().y, selectRangeVert)){
				if (vert.isSelected() && ctrl) {
					vert.deselect();
				} else if (!vert.isSelected()) {
					vert.select();
					selectedVertexes.push(vert);
				}
					nearAnything = true;
			} else if(!shift && !ctrl && vert.isSelected()) {
				vert.deselect();
			}
		}

		for (var i=0;i<elements.length;i++){
			var elem = elements[i];
			if (elem.isNear(getMousePos().x, getMousePos().y, selectRangeElem)){
				elem.select();
				var elemVerts = elem.getVertexes();
				for (var i=0;i<elemVerts.length;i++) { selectedVertexes.push(elem[i]); }
				nearAnything = true;
			} else if(!shift && !ctrl && elem.isSelected()) {
				elem.deselect();
			}
		}
		//-----------------
	}
}
function Cursor(){
	this.visible = true;
	this.size = 10;
	this.thickness = 1;
	var pos;
	this.show = function(){
		this.pos = getMousePos(); //we use this lots of times, so a local variable is faster
		for (var i=0;i<vertexes.length;i++){
			if (vertexes[i].isNear(this.pos.x, this.pos.y, selectRangeVert)){
				vertexes[i].hover();
			} else {
				vertexes[i].unhover();
			}
		}

		for (var i=0;i<elements.length;i++){
			if (elements[i].finished){
				if (elements[i].isNear(this.pos.x, this.pos.y, selectRangeElem)){
					elements[i].hover();
				} else {
					elements[i].unhover();
				}
			}
		}

		if (snapOverride) {
			var tempX = this.pos.x;
			var tempY = this.pos.y;
			this.pos.x = Math.round(tempX/gridSize)*gridSize;
			this.pos.y = Math.round(tempY/gridSize)*gridSize;
		}
		if(this.visible){
			ctx.beginPath();
			ctrl ? ctx.strokeStyle = '#C90A0A' : ctx.strokeStyle = '#000000';
			ctx.moveTo(this.pos.x+this.size,this.pos.y);
			ctx.lineTo(this.pos.x-this.size,this.pos.y);
			ctrl ? ctx.lineWidth = this.thickness*5 : ctx.lineWidth = this.thickness;
			ctx.globalAlpha = 1;
			ctx.moveTo(this.pos.x, this.pos.y+this.size);
			ctx.lineTo(this.pos.x, this.pos.y-this.size);
			ctrl ? ctx.lineWidth = this.thickness*5 : ctx.lineWidth = this.thickness;
			ctx.globalAlpha = 1;
			ctx.lineWidth = this.thickness
			ctx.stroke();

			ctx.font = "10px Arial";
			ctx.fillStyle = '#000000';
			var tempTextX = (this.pos.x/gridSize-3).toFixed(0);
			var tempTextY = (this.pos.y/gridSize).toFixed(0);
			var tempX = this.pos.x+5;
			var tempY = 0;
			if (this.pos.y-15 <= 0) { //move the x,y indicator if off screen
				tempY = this.pos.y+10;
				tempX = this.pos.x+15;
			} else {
				tempY = this.pos.y-5;
			}

			if (!snap){
				tempX = Math.round(tempX);
				tempY = Math.round(tempY);
			}
			ctx.fillText(tempTextX + ", " + tempTextY,tempX,tempY);
		}
	}
}
function PaneButton(x,y,width,height,text){
	this.x = x;
	this.y = y;
	this.width = width;
	this.height = height;
	this.text = text;
	this.selectedTool;

	this.down = false;
	this.selected = false;
	this.hovering = false;

	this.border = 2;
	
	this.select = function(){ this.selected = true; }
	this.deselect = function(){ this.selected = false; }
	this.hover = function(){ this.hovering = true; this.show();}
	this.unhover = function(){ this.hovering = false; this.show();}
	
	this.show = function(){
		ctxUI.lineWidth = this.border;
		ctxUI.beginPath();
		ctxUI.lineWidth = this.border;
		ctxUI.rect(this.x+this.border/2, this.y+this.border/2, this.width-this.border, this.height-this.border);
		if (this.selected) { ctxUI.strokeStyle = '#ff0000'; } else { ctxUI.strokeStyle = '#000000'; }
		if (this.hovering && !this.selected) { ctxUI.strokeStyle = '#686868'; } else if (!this.selected) { ctxUI.strokeStyle = '#000000'; }

		ctxUI.stroke();
		if (this.text == "circle") {
			var r = this.width/3.5;
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';
			ctxUI.arc(x + this.width/2,y + this.height/2,r,0,2*Math.PI, false);
			ctxUI.lineWidth = this.border;
		} else if(this.text == "line") {
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';
			ctxUI.moveTo(this.x+this.width/5,this.y+this.width/5);
			ctxUI.lineTo(this.x+this.width - this.width/5,this.y+this.height - this.width/5);
			ctxUI.lineWidth = this.border;
		} else if(this.text == "dimension") {
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';
			var scl = 4;
			ctxUI.moveTo(this.x+this.width/scl,this.y+this.width/scl);
			ctxUI.lineTo(this.x+this.width - this.width/scl,this.y+this.height - this.width/scl);

			ctxUI.moveTo(this.x+this.width/scl+3,this.y+this.width/scl-3);
			ctxUI.lineTo(this.x+this.width/scl-3,this.y+this.width/scl+3);

			ctxUI.moveTo(this.x+this.width - this.width/scl+3,this.y+this.height - this.width/scl-3);
			ctxUI.lineTo(this.x+this.width - this.width/scl-3,this.y+this.height - this.width/scl+3);

			ctxUI.lineWidth = this.border;
		} else if(this.text == "rect") {
			ctxUI.beginPath();
			ctxUI.lineWidth = this.border;
			ctxUI.rect(this.x+this.width/4, this.y+this.height/4, this.width/2, this.height/2);
			ctxUI.strokeStyle = '#008000';
		} else if(this.text == "arc") {
			var r = this.width/3.5;
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';
			ctxUI.arc(x + this.width/2,y + this.height*3/5,r,0,Math.PI, true);
			ctxUI.lineWidth = this.border;
		} else if(this.text == "select") {
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';

			ctxUI.moveTo(this.x+this.width/5,this.y+this.width/5);
			ctxUI.lineTo(this.x+this.width - this.width/5,this.y+this.height - this.width/5);

			ctxUI.moveTo(this.x+this.width/5,this.y+this.width/5);
			ctxUI.lineTo(this.x+this.width/5,this.y+this.height - this.width/3);
			
			ctxUI.moveTo(this.x+this.width/5,this.y+this.width/5);
			ctxUI.lineTo(this.x+this.width - this.width/3,this.y+this.width/5);
			
			ctxUI.lineWidth = this.border;
		} else if(this.text == "polygon") {
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';

			var unit = this.width/5;
			var px = this.x;
			var py = this.y;

			ctxUI.moveTo(px+unit,py+unit);
			ctxUI.lineTo(px+unit,py+unit*4);

			ctxUI.moveTo(px+unit,  py+unit*4);
			ctxUI.lineTo(px+unit*3,py+unit*4);

			ctxUI.moveTo(px+unit*3,py+unit*4);
			ctxUI.lineTo(px+unit*4,py+unit*3);
			
			ctxUI.moveTo(px+unit*4,py+unit*3);
			ctxUI.lineTo(px+unit*3,py+unit*2);

			ctxUI.moveTo(px+unit*3,py+unit*2);
			ctxUI.lineTo(px+unit*2,py+unit*3);

			ctxUI.moveTo(px+unit*2,py+unit*3);
			ctxUI.lineTo(px+unit,py+unit);

		 	ctxUI.lineWidth = this.border;
		} else if(this.text == "ngon") {
			ctxUI.beginPath();
			ctxUI.strokeStyle = '#008000';

			var unit = this.width/5;
			var px = this.x;
			var py = this.y;

			ctxUI.moveTo(px+unit,py+unit);
			ctxUI.lineTo(px+unit,py+unit*4);

			ctxUI.moveTo(px+unit,  py+unit*4);
			ctxUI.lineTo(px+unit*3,py+unit*4);

			ctxUI.moveTo(px+unit*3,py+unit*4);
			ctxUI.lineTo(px+unit*4,py+unit*3);
			
			ctxUI.moveTo(px+unit*4,py+unit*3);
			ctxUI.lineTo(px+unit*3,py+unit*2);

			ctxUI.moveTo(px+unit*3,py+unit*2);
			ctxUI.lineTo(px+unit*2,py+unit*3);

			ctxUI.moveTo(px+unit*2,py+unit*3);
			ctxUI.lineTo(px+unit,py+unit);

			ctxUI.lineWidth = this.border;
		}
		ctxUI.stroke();
	}
}
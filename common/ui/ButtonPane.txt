function ButtonPane(width){
	this.width = width;

	this.buttons = new Array();
	this.selectedTool = "NONE";

	this.show = function(){
		ctxUI.beginPath();
		ctxUI.fillStyle = '#17bec1';
		ctxUI.fillRect(0, 0, this.width, ctxUI.canvas.height);
		ctxUI.stroke();

		//show all buttons
		for(i=0;i<this.buttons.length;i++){
			this.buttons[i].show();
		}
	}

	this.getWidth = function(){
		return this.width;
	}

	this.setWidth = function(width){
		this.width = width;
	}

	this.addButton = function(buttonText){
		var b = new PaneButton(0, (this.buttons.length)*this.width, this.width, this.width, buttonText);
		this.buttons.push(b);
	}

	this.unhoverAll = function(){
		for(i=0;i<this.buttons.length;i++){
			this.buttons[i].unhover();
		}
	}

	this.hasHoveringButton = function(){
		var hoveringButton = false;
		for(i=0;i<this.buttons.length;i++){
			if (this.buttons[i].hovering) {hoveringButton = true;}
		}
		return hoveringButton;
	}

	this.selectButton = function(y){
		var select = Math.floor(y/this.width);
		var foundButton = false;
		for(i=0;i<this.buttons.length;i++){
			if(i == select){
				this.buttons[i].select();
				this.selectedTool = this.buttons[i].text;
				this.foundButton = true;
			} else {
				this.buttons[i].deselect();
			}
		}
		if (!this.foundButton) {
			this.selectedTool = "NONE";
		}
		
	}

	this.hover = function(y){
		var hover = Math.floor(y/this.width);
		for(i=0;i<this.buttons.length;i++){
			i == hover ? this.buttons[i].hover() : this.buttons[i].unhover();
		}
	}

	this.getSelectedTool = function(){
		return this.selectedTool;
	}
}
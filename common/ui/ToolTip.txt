function ToolTip(tip){
	//TODO: actually use this class
	this.width = width;
	this.lifespan = 10000;
	this.tip = tip;

	this.show = function(){
		this.lifespan--;

		ctx.beginPath();
		ctx.fillStyle = '#17bec1';
		ctx.fillRect(0, 0, this.width, ctx.canvas.height);
		ctx.stroke();
	}

	this.getWidth = function(){
		return this.width;
	}

	this.setWidth = function(width){
		this.width = width;
	}
}
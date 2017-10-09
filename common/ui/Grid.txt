function Grid(g){
	this.gridDensity = g || 10; //default 10
	this.show = function(){
		ctxBG.globalAlpha = 0.1;
		ctxBG.beginPath();

		for (i = 0; i < c.height; i += this.gridDensity){
			ctxBG.moveTo(0,i);
			ctxBG.lineTo(c.width,i);
		}
		ctxBG.lineWidth = 1;
		ctxBG.strokeStyle = '#000000';

		for (i = 0; i < c.width; i += this.gridDensity){
			ctxBG.moveTo(i,0);
			ctxBG.lineTo(i,c.height);
		}
		ctxBG.lineWidth = 1;
		ctxBG.strokeStyle = '#000000';
		ctxBG.stroke();
		ctxBG.globalAlpha = 1;
	}

	this.setDensity = function(a){
		this.gridDensity = a;
		console.log(a + "   " + this.gridDensity);
	}

	this.getDensity = function(){
		return this.gridDensity;
	}
}
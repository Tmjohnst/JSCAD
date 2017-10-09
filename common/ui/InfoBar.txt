function InfoBar(){
	this.height = 40;
	this.info = "Start drawing!";
	this.record = "Empty";
	this.line = 2;
	this.blinkFreq = 10;
	this.blink = false;

	this.show = function(){
		ctxIB.beginPath();
		ctxIB.fillStyle = '#FFFFFF';
		ctxIB.fillRect(0, 0, cib.width, this.height);
		ctxIB.fillStyle = '#000000';
		ctxIB.fillRect(0, 0, cib.width, this.line);
		ctxIB.moveTo(0, this.height/2);
		ctxIB.strokeStyle = '#B0B1B2';
		ctxIB.lineTo(cib.width, this.height/2-this.line/2)
		ctxIB.stroke();

		ctxIB.font = this.height/2.5 + 'px serif';
		ctxIB.fillText(this.info, 15, this.height*3/8 + this.line/3);

		ctxIB.font = this.height/2.8 + 'px serif';
		ctxIB.fillText(this.record, 15, this.height*6/8 + this.line*2);

		ctxIB.moveTo(5, this.height - this.line*2);
		ctxIB.lineTo(8, this.height*3/4 - this.line/2);

		ctxIB.moveTo(8, this.height*3/4 - this.line/2);
		ctxIB.lineTo(5, this.height/2 + this.line);

		if(this.blink) {
			ctxIB.moveTo(6, this.height/8 - this.line/2);
			ctxIB.lineTo(6, this.height/2 - this.line);
			ctxIB.stroke();
		}

		if(this.blinkFreq >= 40) { this.blink = false; }
		else if(this.blinkFreq <= 0) { this.blink = true; }

		if (this.blink) { this.blinkFreq++; } else { this.blinkFreq--; }
		ctxIB.stroke();
	}

	this.inform = function(info){
		this.info = info;
	}
	
	this.addInform = function(infoAdd){
		this.info += infoAdd;
	}

	this.clearInfo = function(){
		this.info = " ";
		
		this.record = "Command not recognized (no commands implimented yet anyway)"
	}

	this.removeLastInfo = function(){
		this.info = info.splice(info.length-1, 1);
	}
		
	this.getHeight = function(){
		return this.height;
	}
}
function Vector(x, y, angle, mag){
	this.x = x || 0;
	this.y = y || 0;
	this.angle = (angle % 360) || 0;
	this.mag = mag || 1;

	//debug function - not to be used in regular operation
	this.show = function(){
		ctx.beginPath();
		ctx.moveTo(this.x, this.y);
		ctx.lineTo(Math.cos(this.angle*2*Math.PI/180) * this.mag, Math.sin(this.angle*2*Math.PI/180) * this.mag);
		ctx.lineWidth = 3;
		ctx.strokeStyle = '#2BD1FF';
		ctx.globalAlpha = 0.25;
		ctx.stroke();
		ctx.globalAlpha = 1;
	}

	this.setX = function(x){
		this.x = x;
	}

	this.setY = function(y){
		this.y = y;
	}
	
	this.setAngle = function(a){
		this.angle = (a % 360);
	}
	
	this.addAngle = function(a){
		this.angle = (this.angle + a) % 360
	}

	this.mult = function(s){
		this.mag = this.mag * s;
	}

	this.subMag = function(v){
		this.mag = this.mag - v.getMag();
	}

	this.norm = function(){
		this.mag = 1;
	}

	this.add = function(v){
		var xComponent = Math.cos(this.angle*2*Math.PI/180) * this.mag;
		var yComponent = Math.sin(this.angle*2*Math.PI/180) * this.mag;

		var vxComponent = Math.cos(v.angle*2*Math.PI/180) * v.getMag();
		var vyComponent = Math.sin(v.angle*2*Math.PI/180) * v.getMag();

		var newX = xComponent + vxComponent;
		var newY = yComponent + vyComponent;

		this.angle = Math.atan((newY/newX) * 180 / (2*Math.PI));
		this.mag = Math.sqrt(xComponent*xComponent + yComponent*yComponent);

	}
	
	this.getMagSqr = function(){
		var xComponent = Math.cos(this.angle*2*Math.PI/180) * this.mag;
		var yComponent = Math.sin(this.angle*2*Math.PI/180) * this.mag;

		return xComponent*xComponent + yComponent*yComponent;
	}

	this.dot = function(v){
		return (v.x*this.x + v.y*this.y);
	}
}
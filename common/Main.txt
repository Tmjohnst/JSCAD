//Main js file for this project

var currentTool = new Array(); //array that holds a single tool in use
var elements = new Array(); //all elements in the frame
var vertexes = new Array(); //all vertexes to be drawn (since elements can share vertecies, they should be drawn separate)
var keysDown = new Array(); //all keys pressed

var c = document.getElementById("canvas");
var ctx = c.getContext("2d");

var cui = document.getElementById("canvasUI");
var ctxUI = cui.getContext("2d");

var cbg = document.getElementById("canvasBack");
var ctxBG = cbg.getContext("2d");

var cib = document.getElementById("canvasInfo");
var ctxIB = cib.getContext("2d");

var cursor;
var grid;
var gridSize = 10; //this is only set by the grid - default is 10
var leftPanelWidth = c.width/10;
var buttonPane;
var infoBar;

var selectRangeVert = 10;
var selectRangeElem = 1;

//program option booleans
var snap = true;
var snapOverride = false; //the cursor shouldn't snap whilst selecting
var showGrid = true;

var debug = false; //global flag for debug information
var shift = false; //global flag to check if shift is pressed
var ctrl = false; //global flag to check if ctrl is pressed
var enter = false; //global flag to check if enter is pressed
var deleteKey = false; //global flag to check if delete is pressed
var dragging = false; //global flag to check if we're dragging
var dblclick = false; //global flag to watch for double clicks
var scrollup = false;
var scrolldown = false;

var framerate = 120; //fps
var ONE_FRAME_TIME = 1000 / framerate;
var dt;
var lastUpdate = Date.now();

//var VERTEXES = 0; //total vertexes created
var SELECTED_VERTEXES = 0; //to indicate whether or not we're selecting
window.addEventListener('resize', function(evt){resize(evt);}, false);
window.addEventListener('dblclick', function(evt){ doubleClick(); }, false);
window.addEventListener('mousemove', function(evt){ mousePos = handleMousePos(evt); }, false);
window.addEventListener('mousedown', function(evt){
	mouseClick = handleMousePos(evt);
	mouseClick.held = true; //never gets set externally
}, false);
window.addEventListener('mouseup', function(evt){ 
	mouseClick.down = false;
	mouseClick.rightDown = false;
	mouseClick.held = false;
	dragging = false;
}, false);

window.addEventListener('wheel', function(evt){
	dy = parseFloat((evt.deltaY/100).toFixed(2));
	if (dy > 0) { scrollup = true; }
	if (dy < 0) { scrolldown = true; }
}, false);

window.addEventListener('keydown', function(evt){ 
	shift = evt.shiftKey; 
	ctrl = evt.ctrlKey; 
	enter = (evt.keyCode == 13);
	deleteKey = (evt.keyCode == 46);
	if (evt.key.match(/^[a-zA-Z() ]$/)) { infoBar.addInform(evt.key); }
	if (evt.keyCode == 8) { infoBar.removeLastInfo(); }
	if (evt.keyCode == 13) { infoBar.clearInfo(); }
}, false);
window.addEventListener('keyup'  , function(evt){ 
	shift = evt.shiftKey; 
	ctrl = evt.ctrlKey; 
	enter = (evt.keyCode == 13); 
}, false);

var mousePos = {
	rightDown: false,
	down: false,
	x: 0,
	y: 0
};

var mouseClick = {
	rightDown: false,
	down: false,
	x: 0,
	y: 0
};

function resize(evt){
	c.width = window.innerWidth;
	c.style.leftMargin = leftPanelWidth;
	c.height = window.innerHeight;

	cbg.width = window.innerWidth;
	cbg.height = window.innerHeight;
	drawBG();
	
	cib.width = window.innerWidth;
}

function doubleClick(){
	dblclick = true;
}

function handleMousePos(evt){
	return {
		rightDown: evt.button == 2,
		down: evt.button == 0,
		x: evt.clientX,
		y: evt.clientY
	};
}

function getMousePos(){
	return mousePos;
}

function nearestVertex(x,y,buffer){
	var ret = {};
	var bestDist = 1000010;
	this.x = x;
	this.y = y;
	this.buff = 1;

	if(buffer) { this.buff = buffer; }

	if (snap) { this.x = Math.round(x/gridSize)*gridSize; } else { this.x = x; }
	if (snap) { this.y = Math.round(y/gridSize)*gridSize; } else { this.y = y; }

	for (var i=0; i<vertexes.length; i++){
		if (vertexes[i].isNear(this.x,this.y,gridSize*buff)) {
			var aDist = Math.hypot(vertexes[i].x - this.x, vertexes[i].y - this.y);
			if (aDist < bestDist) {
				ret = vertexes[i];
				bestDist = aDist;
			}
		}
	}
	if (bestDist == 1000010) { ret = false; }
	return ret;
}

function nearestElement(x,y){
	var ret = false;

	for (var i=0; i<elements.length; i++){
		if (elements[i].isNear(x,y,gridSize*1.5)) { ret = elements[i]; }
	}
	return ret;
}

function pushVertex(v){
	vertexes.push(v);
	infoBar.inform("New vertex: [" + v.x + ", " + v.y + "]");
}

function deleteVertex(v){
	for (var i=0; i<vertexes.length; i++){
		if (vertexes[i] === v) {
			vertexes.splice(i, 1);
			infoBar.inform("Deleted vertex: [" + v.x + ", " + v.y + "]");
		}
	}

	deselectAll();
}

function pushElement(e){
	elements.push(e);
	infoBar.inform("New element: [" + e.constructor.name + "]");
}

function deleteElement(e){
	for (var i=0; i<elements.length; i++){
		if (elements[i] === e) {
			elements.splice(i, 1);
			infoBar.inform("Deleted element: [" + e.constructor.name + "]");
		}
	}

	deselectAll();
}

function getAllSelectedVertexes(){
	var vertexesSelected = new Array();

	for (var i=0; i<vertexes.length; i++){
		if(vertexes[i].isSelected) { vertexesSelected.push(vertexes[i]); }
	}

	return vertexesSelected;
}

function removeElement(id){
	elements.splice(id, 1);
}

function init(){
	cursor = new Cursor();
	
	buttonPane = new ButtonPane(leftPanelWidth);
	buttonPane.addButton('select');
	buttonPane.addButton('circle');
	buttonPane.addButton('rect');
	buttonPane.addButton('line');
	buttonPane.addButton('polygon');
	buttonPane.addButton('ngon');
	buttonPane.addButton('arc');
	buttonPane.addButton('dimension');

	infoBar = new InfoBar();
	grid = new Grid(10);
	dt = 0;

	if (gridSize != grid.getDensity()) { grid.setDensity(gridSize); }
	c.width = window.innerWidth;
	c.style.leftMargin = leftPanelWidth;
	c.height = window.innerHeight;
	
	cib.width = window.innerWidth;
	cib.height = infoBar.getHeight();
	cib.style.top = c.height-cib.height;
	drawBG();
	drawUI();
	drawIB();
}

function deselectAll(){
	for (var i=0; i<vertexes.length; i++){ vertexes[i].deselect(); }
}

function drawUI(){
	cui.width = leftPanelWidth;
	cui.height = window.innerHeight;
	buttonPane.show();
}

function drawIB(){
	cib.width = window.innerWidth;
	infoBar.show();
}

function drawBG(){
	cbg.width = window.innerWidth;
	cbg.height = window.innerHeight;
	if (showGrid) { grid.show(); }
}

function draw(){
	var now = Date.now();
	var lastX;
	var lastY;
	ctx.clearRect(0, 0, c.width, c.height);

	//check if we are dragging by checking how far we are from the click and if the mouse is held
	if (Math.sqrt(Math.pow(mouseClick.x-mousePos.x,2) + Math.pow(mouseClick.y-mousePos.y,2)) > 10) {
		if (mouseClick.held) {dragging = true;}
	}

	var elementIsDrawing = false;
	var elementDrawing = 0;
	var toolSelected = false;
	var toolIsDrawing = false;

	//check if we are currently drawing something
	for (i=0; i<elements.length; i++){
		if(!elements[i].finished){
			elementIsDrawing = true;
			elementDrawing = i;
		}
	}
	
	if(!currentTool.length <= 0 && !currentTool[0].finished){
		toolSelected = true;
		toolIsDrawing = true;
	}

	//we want to run this check once more after the mouse leaves the pane...
	if (mousePos.x < leftPanelWidth) { buttonPane.hover(mousePos.y); }
	else if (mousePos.x >= leftPanelWidth && buttonPane.hasHoveringButton()) { buttonPane.unhoverAll(); }

	//handle clicks
	if (mouseClick.down){
		mouseClick.x = mousePos.x;
		mouseClick.y = mousePos.y;

		//We do these two checks to cancel the current operation if you click a new tool before it's done
		if(mouseClick.x < leftPanelWidth && elementIsDrawing){
			removeElement(elementDrawing);
			buttonPane.selectButton(mouseClick.y);
			elementIsDrawing = false;
			if (buttonPane.getSelectedTool() != "select") {
				this.deselectAll();
			}
			drawUI();
		}

		if(mouseClick.x < leftPanelWidth && !elementIsDrawing) {
			buttonPane.selectButton(mouseClick.y);
			//handle the selecter tools
			if (buttonPane.getSelectedTool() == "select"){
				mouseClick.down = false; //so the selector doesn't grab this click
				var select = new Selector();
				select.start(mouseClick.x, mouseClick.y);
				currentTool[0] = select;
				toolSelected = true;
				toolIsDrawing = true;
			} else { //didn't select a tool
				currentTool.splice(0,1);
				toolSelected = false;
				toolIsDrawing = false;
				this.deselectAll();
			}
			for (var i=0; i<elements.length; i++){
				if(!elements[i].finished){
					elementIsDrawing = true;
					elementDrawing = i;
				}
			}
			if(toolSelected && !currentTool[0].finished){
				toolIsDrawing = true;
			} else {
				toolIsDrawing = false;
			}
			drawUI();
		} 
		
		//if we are drawing and it's within the canvas
		if (mouseClick.x > leftPanelWidth){
			//handle the line tool
			if (buttonPane.getSelectedTool() == "line" && !elementIsDrawing){ //removed !drawing
				var line = new Line();
				line.start(mouseClick.x, mouseClick.y);
				this.pushElement(line);
			}

			//handle the dimension tool
			if (buttonPane.getSelectedTool() == "dimension" && !elementIsDrawing){ //removed !drawing
				var dimension = new Dimension();
				dimension.start(mouseClick.x, mouseClick.y);
				this.pushElement(dimension);
			}
		
			//handle the circle tool
			if (buttonPane.getSelectedTool() == "circle" && !elementIsDrawing){
				var circle = new Circle();
				circle.start(mouseClick.x, mouseClick.y);
				this.pushElement(circle);
			}

			//handle the rectangle tool
			if (buttonPane.getSelectedTool() == "rect" && !elementIsDrawing){
				var rect = new Rect();
				rect.start(mouseClick.x, mouseClick.y);
				this.pushElement(rect);
			}

			//handle the rectangle tool
			if (buttonPane.getSelectedTool() == "arc" && !elementIsDrawing){
				var arc = new Arc();
				arc.start(mouseClick.x, mouseClick.y);
				this.pushElement(arc);
			}

			//handle the polygon tool
			if (buttonPane.getSelectedTool() == "polygon" && !elementIsDrawing){
				var poly = new Polygon();
				poly.start(mouseClick.x, mouseClick.y);
				this.pushElement(poly);
			}

			//handle the ngon tool
			if (buttonPane.getSelectedTool() == "ngon" && !elementIsDrawing){ //removed !drawing
				var ngon = new Ngon();
				ngon.start(mouseClick.x, mouseClick.y);
				this.pushElement(ngon);
			}

			//advance any element if it's currently drawing
			if (elementIsDrawing) {
				elements[elementDrawing].step(mouseClick.x, mouseClick.y);
			}

			//advance any tool if it's currently drawing
			if (toolIsDrawing) {
				currentTool[0].step(mouseClick.x, mouseClick.y);
			}
		}
		
		this.lastX = mouseClick.x;
		this.lastY = mouseClick.y;
	} else {
		this.lastX = mousePos.x;
		this.lastY = mousePos.y;
	}
	
	//free the cursor from snaps when the select tool or dimension tool is being used
	if (buttonPane.getSelectedTool() == "select" || buttonPane.getSelectedTool() == "dimension"){
		snapOverride = false;
	} else {
		snapOverride = true;
	}

	for (var i=0; i<elements.length; i++){ elements[i].show(); } //show all elements
	for (var i=0; i<vertexes.length; i++){ vertexes[i].show(); } //show all vertexes
	if (!currentTool.length <= 0) { currentTool[0].show(); } //show current tool
	if (mousePos.x >= leftPanelWidth) { cursor.show(); }

	dt = now - lastUpdate;
	lastUpdate = now;
	dblclick = false; //if nothing handled the dblclick, we didn't need it
	mouseClick.down = false; // if nothing handled the mouseclick, we didn't need it
	mouseClick.rightDown = false;
	enter = false;
	deleteKey = false;
	scrollup = false;
	scrolldown = false;
	
	drawIB();
	requestAnimationFrame(draw);
}

//Gives us info about what's drawn every second
function info(){
	console.log("Total vertexes: " + vertexes.length + "  dt:" + dt);
	console.log("mouse pos: (" + mousePos.x + ", " + mousePos.y + ")");
	var a = "Elements: [";
	for (var i=0; i<elements.length; i++){ a = a + elements[i].constructor.name + " "; }
	console.log(a + "]");
	console.log("number of selected verts: " + SELECTED_VERTEXES);
}

init();
requestAnimationFrame(draw);
if (debug) {setInterval(info, 1000);}
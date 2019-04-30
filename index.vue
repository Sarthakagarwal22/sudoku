checkPresenceIntwoDimensionalArray = (twoDimensionalArray, value) => {
	var noOfRows = twoDimensionalArray.length;
	var rowIterator = 0;
	while(rowIterator < noOfRows) {
		var columnLength = twoDimensionalArray[rowIterator].length;
		var columnIterator = 0;
		while(columnIterator < columnLength){
			if(twoDimensionalArray[rowIterator][columnIterator].number === value)
				return true
			else
				columnIterator ++;
		}
		rowIterator++;
	}
	return false;
}

checkRow = (value, twoDimensionalArray, rowIndex)=> {
	var numberPresent = false;
	twoDimensionalArray[rowIndex].map(numberObject => {
		if(numberObject.number == value)
			numberPresent = true;
	})
	return numberPresent
}

checkColumn = (value, twoDimensionalArray, columnIndex) => {
	var numberPresent = false;
	twoDimensionalArray.map((row)=>{
		if(row[columnIndex].number === value)
			numberPresent=true;
	})
	return numberPresent;
}

check3x3Square = (value, twoDimensionalArray, rowIndex, columnIndex) => {
	rowStart = parseInt((rowIndex)/3)*3 ;
	rowEnd = ((parseInt((rowIndex)/3) + 1)*3) ;
	columnStart = parseInt((columnIndex)/3)*3 ;
	columnEnd = ((parseInt((columnIndex)/3) + 1)*3) ;

	var modifiedtwoDimensionalArray = twoDimensionalArray.slice(rowStart,rowEnd).map((row) => row.slice(columnStart, columnEnd));

	return checkPresenceIntwoDimensionalArray(modifiedtwoDimensionalArray, value)
}

Vue.component('timer-component', {
  	data: function () {
	    return {
	    	seconds: 0,
			minutes: 0,
			hours: 0,
			timer: null,
			timerStartedAndWindowBlurred:false
	    }
  	},
  	created() {
		window.addEventListener("focus", function(event){ 
		     if(this.timerStartedAndWindowBlurred)
		     	this._startTimer()
		}, false);
		window.addEventListener("blur", function(event){
			clearInterval(this.timer);
		},false);
	},
  	methods:{
	  	_startTimer: function(){
	  		this.timerStartedAndWindowBlurred = true;
	  		this.timer = setInterval(()=>{
	  			this.seconds += 1;
	  			if(this.minutes===60){
					this.seconds = 0,
					this.minutes = 0,
					this.hours += 1						
				}

				else if (this.seconds === 59){
					this.seconds = 0,
					this.minutes += 1					
				}
				},1000)
	  	},

	  	_pauseTimer: function(){
	  		clearInterval(this.timer);
	  		this.timerStartedAndWindowBlurred = false;
	  	},

	  	_resetTimer : function(){
	  		clearInterval(this.timer);
	  		this.seconds=this.minutes=this.hours=0;
	  		this.timerStartedAndWindowBlurred = false;
	  	},

	  	_stopTimerAndReturnTime : function(){
	  		var timeElapsed = {
	  			hours:this.hours,
	  			minutes: this.minutes,
	  			seconds: this.seconds
	  		};
	  		this._resetTimer();
	  		return timeElapsed;
	  	},
	},
 	template: '<div><button @click="_startTimer">Start Timer</button>&nbsp;<button @click="_pauseTimer"> Pause Timer</button>&nbsp;<button @click="_resetTimer"> Reset Timer</button><br><br>{{("0" + hours).slice(-2)}}:{{("0" + minutes).slice(-2)}}:{{("0" + seconds).slice(-2)}}<br><br></div>'
})

new Vue({
  el: '#sudokuGame',
  data: {
   	numbers : [
    	[{number:1,readOnly:true},{number:null},{number:3,readOnly:true},{number:null},{number:9,readOnly:true},{number:null},{number:8,readOnly:true},{number:null},{number:6,readOnly:true}],
    	[{number:null},{number:null},{number:null},{number:null},{number:4,readOnly:true},{number:3,readOnly:true},{number:null},{number:1,readOnly:true},{number:null}],
    	[{number:null},{number:6, readOnly:true},{number:null},{number:null},{number:null},{number:1, readOnly:true},{number:7, readOnly:true},{number:null},{number:null}],
    	[{number:null},{number:7, readOnly:true},{number:null},{number:null},{number:1, readOnly:true},{number:null},{number:6, readOnly:true},{number:3, readOnly:true},{number:null}],
    	[{number:3, readOnly:true},{number:null},{number:5, readOnly:true},{number:null},{number:2, readOnly:true},{number:null},{number:4, readOnly:true},{number:null},{number:7, readOnly:true}],
    	[{number:null},{number:9,readOnly:true},{number:6,readOnly:true},{number:null},{number:5,readOnly:true},{number:null},{number:null},{number:2,readOnly:true},{number:null}],
    	[{number:null},{number:null},{number:4,readOnly:true},{number:1,readOnly:true},{number:null},{number:null},{number:null},{number:7,readOnly:true},{number:null}],
    	[{number:null},{number:2,readOnly:true},{number:null},{number:4,readOnly:true},{number:3,readOnly:true},{number:null},{number:null},{number:null}, {number:null}],
    	[{number:5,readOnly:true},{number:null},{number:1,readOnly:true},{number:null},{number:7,readOnly:true},{number:null},{number:9,readOnly:true},{number:null},{number:4,readOnly:true}]
   	],
	maxErrors:3,
	errorsCommited: 0,
	lockSudoku: false,
	sudokuComplete: false,
	timeTaken : null,
  },
 	methods:{
	  	
	  	_checkValidInput : function (e) {
	  		
	  		//adding + to make the string, a number
	  		var rowIndex = +e.target.attributes[1].value;
	  		var columnIndex = +e.target.attributes[2].value;
	  		var inputValue = +e.target.value;

	  		if(checkRow(inputValue, this.numbers, rowIndex)){
	  			this._errorHandling("row", e);
	  			this.numbers[rowIndex][columnIndex] = {number:null};
	  		}

	  		else if(checkColumn(inputValue, this.numbers, columnIndex)){
	  			this._errorHandling("column", e);
	  			this.numbers[rowIndex][columnIndex] = {number:null}
	  		}

	  		else if(check3x3Square(inputValue, this.numbers, rowIndex, columnIndex)){
	  			this._errorHandling("3x3 Grid");
	  			this.numbers[rowIndex][columnIndex] = {number:null}
	  		}

	  		else {
	  			e.target.className += " correct-input"
	  			this.numbers[rowIndex][columnIndex] = {number:inputValue,readOnly:false}
	  			
	  			if(!checkPresenceIntwoDimensionalArray(this.numbers,null)){
	  				this.sudokuComplete = true;
	  				this.timeTaken = this.$refs.timerComponent._stopTimerAndReturnTime()
	  			}
	  		}

	  	},
	  	
	  	_errorHandling : function(string, event) {
	  		this.errorsCommited++;
	  			if(this.maxErrors - this.errorsCommited > 0){
	  				alert(`You have entered a number, already present in ${string}, you now have only ${this.maxErrors - this.errorsCommited} errors left` )
	  			}
	  			else
	  				this._lockDownSudoku()

	  		if(event.target.className.indexOf("correct-input") !== -1)
	  			event.target.className = event.target.className.slice(0,event.target.className.indexOf("correct-input")) 
	  	},

	  	_checkIfMaxErrorsDone : function(e) {

	  		this.maxErrors = parseInt(e.target.value);

	  		if ((e.target.value - this.errorsCommited) < 1 )
	  			this._lockDownSudoku()
	  	
	  		else
	  			this.lockSudoku = false;
	  	},

	  	_lockDownSudoku : function() {
	  		alert("You have done maximum errors, locking down sudoku");
	  		this.lockSudoku = true;
	  	} 
	},
	computed: {
		_errorsLeft: function(){
			return this.maxErrors - this.errorsCommited ;
		}
	}
});

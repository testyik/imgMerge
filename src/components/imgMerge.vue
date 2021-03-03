<template>
	<div>
		<input id="uploadInput" type="file" name="myFiles" multiple v-on:input="merge"  accept="image/*">
		selected files: <span id="fileNum">{{fileNum}}</span>
	</div>
	<div>
		marginH: <input name="marginH" type="number" v-model="marginH" />
	</div>
	<div>
		marginV: <input name="marginV" type="number" v-model="marginV" />
	</div>
	<div>
		canvas height: {{canHeight}}
	</div>
	<div>
		canvas width: {{canWidth}}
	</div>
	<div>
		<button v-on:click="refresh">refresh</button>
	</div>
	<br/>
	<div>
		<canvas id="final-img" ref="finalImg" height="1044" width="1896" v-on:click="save"></canvas>
	</div>
   
</template>

<script>
//import ImgLoad from './ImgLoad.vue'

export default {
  name: 'imgMerge',
  props: {
    msg: String
  },
  data() {
    return {
      // Define a reversed data property
		fileNum: 0,
		imgSrc: "",
		drawImageNum: 0,
		marginV: 0,
		marginH: 200,
		imgList: [],
		canHeight: 0,
		canWidth: 0,
		maxImgHeight: 0,
		totalImgWidth: 0
    };
  },  
  methods:{
	merge: function(event){
		
		let	oFiles = event.target.files;
        this.fileNum = oFiles.length;	
		
		let promiseArr = [];
		for (let j = 0; j < oFiles.length; j++) {
			promiseArr.push(this.loadImg(oFiles[j]));
		}
		Promise.all(promiseArr).then((values) => {
			this.imgList = values;
			this.getMaxImgHeight();
			this.alignImgHeight();
			this.setFinalImgSize();
			this.mergeImg();
		});
	},
	refresh: function(){
		location.reload();
	},
	getMaxImgHeight: function(){
		let minImgHeight = 0;
		for (let i = 0; i < this.imgList.length; i++){
			if (minImgHeight > this.imgList[i].height || minImgHeight == 0){
				minImgHeight = this.imgList[i].height;
			}
		}
		this.maxImgHeight = minImgHeight;
		return this.maxImgHeight;
	},
	getTotalImgWidth: function(){
		let totalWidth = 0;
		for (let i = 0; i < this.imgList.length; i++){
			totalWidth += this.imgList[i].width;
		}
		this.totalImgWidth = totalWidth;
		return this.totalImgWidth;
	},
	setFinalImgSize: function(){
		
		let canvasWidth = (Number(this.marginH) * 2) + this.getTotalImgWidth();
		let canvasHeight = (this.marginV * 2) + this.maxImgHeight;
		
		let canvas = this.$refs.finalImg;
		const ctx = canvas.getContext('2d');
		this.canWidth = ctx.canvas.width = canvasWidth;
		this.canHeight = ctx.canvas.height = canvasHeight; 
		ctx.fillStyle = 'rgba(0, 0, 0, 0)';
		ctx.fillRect(0, 0, this.canWidth, this.canHeight);
		
	},
	loadImg: function(imgFile){
		
		return new Promise((resolve, reject) => {
			var reader = new FileReader();
			reader.onload = function () {
				var img = new Image();
				img.src = reader.result;
				img.onload = function() {
					resolve(img);
				}
			};
			reader.onerror = function() {
				reject(new Error('load fail'));
			}
			
			reader.readAsDataURL(imgFile);
		});		
	},
	mergeImg: function (){
		let canvas = this.$refs.finalImg;
		const ctx = canvas.getContext('2d');
		
		let drewX = Number(this.marginH);
		while(this.drawImageNum < this.fileNum){
			this.drawImg(ctx,this.imgList[this.drawImageNum],drewX,this.marginV);
			drewX += this.imgList[this.drawImageNum].width;
			console.log(drewX);
			this.drawImageNum++;
		}
		
	},
	drawImg: function(ctx,img,x,y){
		ctx.drawImage(img, x, y, img.width, img.height );
	},
	alignImgHeight: function(){
		if(this.maxImgHeight <= 0 )
			return;
		
		for (let k = 0; k < this.fileNum; k++){
			if(this.imgList[k].height > this.maxImgHeight){
				let aspectRatio = this.imgList[k].height / this.imgList[k].width;
				this.imgList[k].width = Math.round(this.maxImgHeight / aspectRatio);
				this.imgList[k].height = this.maxImgHeight;
			}
		}
		console.log(this.imgList);
	},
	save: function(){
		const canvas = this.$refs.finalImg;
		var link = document.createElement('a');
		link.download = 'download.png';
		link.href = canvas.toDataURL()
		link.click();
		link.delete;
		
	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	#final-img { cursor:pointer;}
</style>

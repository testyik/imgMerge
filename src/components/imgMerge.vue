<template>
  <form name="uploadForm">
    <div>
      <input id="uploadInput" type="file" name="myFiles" multiple v-on:input="updateSize"  accept="image/*">
      selected files: <span id="fileNum">{{fileNum}}</span>;
      total size: <span id="fileSize">{{fileSize}}</span>
    </div>
    
  </form>
  <canvas id="final-img" ref="finalImg" height="1044" width="1896" ></canvas>
</template>

<script>


export default {
  name: 'imgMerge',
  props: {
    msg: String
  },
  data() {
    return {
      // Define a reversed data property
		fileNum: 0,
		fileSize: "0 bytes",
		imgSrc: ""
    };
  },  methods:{
	updateSize: function(event){
		
		let nBytes = 0,
        oFiles = event.target.files,
        nFiles = oFiles.length;	
		
		if(nFiles < 2){
			alert("please select 2 image");
			return;
		}
		
		for (let nFileId = 0; nFileId < nFiles; nFileId++) {
			nBytes += oFiles[nFileId].size;
		}
		let sOutput = nBytes + " bytes";
		// optional code for multiples approximation
		const aMultiples = ["KiB", "MiB", "GiB", "TiB", "PiB", "EiB", "ZiB", "YiB"];
		for (let nMultiple = 0, nApprox = nBytes / 1024; nApprox > 1; nApprox /= 1024, nMultiple++) {
			sOutput = nApprox.toFixed(3) + " " + aMultiples[nMultiple] + " (" + nBytes + " bytes)";
		}
		
		// end of optional code
		this.fileNum = nFiles;
		this.fileSize = sOutput;
		
			
		this.mergeImg(event.target.files);
	},
	mergeImg: function (files){
		const canvas = this.$refs.finalImg;
		const ctx = canvas.getContext('2d');
		//setup background
		ctx.fillStyle = 'rgba(255, 0, 0, 0)';
		ctx.fillRect(0, 0, 1896, 1044);
		var reader = new FileReader();
		reader.readAsDataURL(files[0]);
		
		reader.onload = function () {
			var img = new Image();
			img.src = reader.result;
			img.onload = function() {
				ctx.drawImage(img, 200, 0);
			}
		};
		
		var reader2 = new FileReader();
		reader2.readAsDataURL(files[1]);
		
		reader2.onload = function () {
			var img = new Image();
			img.src = reader2.result;
			img.onload = function() {
				ctx.drawImage(img, 949, 0);
			}
		};
	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

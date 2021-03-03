<script>
export default {
	name: 'ImgLoad',
	props: {
		img: Object
	},
	data(){
		this.load(this.img);
		return {
			
			loadedImage: object
		};
	},
	
	methods: {
		load: function(img){
			this.loadedImage = this.image;
			return this.loadedImage;
		}

	}
}

</script>
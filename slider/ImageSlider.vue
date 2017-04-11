<template>
	<div class="slider-container" :style="{ height: sliderHeight + 'px'}">
	    <div class="slider-box">
	      <image-slide 
	           v-for="(slide, index) in slides"
	           v-show="currentSlideIndex === index"
	           :progress="progress"
	           :slide="slide"
	           @mouseenter.native="isPaused = true"
	           @mouseleave.native="isPaused = false"
	           :key="index"></image-slide>
          <slide-controls 
          		@prev="prev" 
          		@next="next" 
          		:isPrevDisabled="isFirstSlide" 
          		:isNextDisabled="isLastSlide"></slide-controls>
        </div>
    	<side-widget 
    		:slides="slides" 
    		:currentSlide="currentSlideIndex"
    		@setSlideIndex="setSlide"></side-widget>
  </div>
</template>

<script>
import Slide from './Slide.vue';
import SlideControls from './SlideControls.vue';
import SideWidget from './SideWidget.vue';

export default {
	mounted() {
		this.resizeImages();
		this.startTimer();
	},

	destroyed() {
		clearInterval(this.timer);
	},

	components: {
		imageSlide: Slide,
		slideControls: SlideControls,
		sideWidget: SideWidget
	},

	props: {
		slides: {
			type: Array,
			required: true
		},
		autoSlide: {
			type: Boolean,
			default: () => true
		},
		sliderHeight: {
			type: Number,
			default: () => 400
		},
		duration: {
			type: Number,
			default: () => 10000
		}
	},

	data() {
		return {
			currentSlideIndex: 0,
			timer: null,
			isPaused: false,
			progress: 0,

		}
	},

	methods: {
		setScrollBar() {
			const el = document.querySelector('.is-active');
			const sideWidgetHeight = el.parentNode.clientHeight;

			if (el.parentNode.scrollHeight > sideWidgetHeight) {
				if (this.isFirstSlide) {
					el.parentNode.scrollTop = 0;
				} else if (this.isLastSlide) {
					el.parentNode.scrollTop = el.parentNode.scrollHeight;
				} else {
					el.parentNode.scrollTop = el.offsetTop;
				}
				
			}
		},

	    resizeImages() {
	      
	      this.slides.forEach(slide => {
	        const el = document.querySelector(`[data-slide-id='${slide.id}']`);

	        const parentWidth = el.parentNode.clientWidth;
	        const parentHeight = el.parentNode.clientHeight;

	        //target the img and set the width/height. 
	        el.children[0].style.width = parentWidth + 'px';
	        el.children[0].style.height = parentHeight + 'px';
	        
	      });
	      
	    },

	    startTimer() {
	    	if (this.autoSlide) {
	    		if (!this.timer) {
	    			const interval = (this.duration / 100);

	    			this.timer = setInterval(() => {
	    				if (this.progress === 100) {
	    					if (this.isLastSlide) {
	    						this.currentSlideIndex = 0;
	    						this.reset();
	    					} else {
	    						this.next();
	    					}
	    				} else {
	    					if (!this.isPaused) {
	    						this.progress++;
	    					}
	    				}
	    			}, interval);
	    		}
	    	}
	    },

	    reset() {
	    	clearInterval(this.timer);
	    	this.timer = null;
	    	this.progress = 0;
	    	this.setScrollBar();
	    	this.startTimer();
	    },

	    prev() {
	      if (!this.isFirstSlide) {
	      	this.currentSlideIndex--;
	      	if (this.timer) {
	      		this.reset();
	      	}
	        
	      }
	    },

	    next() {
	      if (!this.isLastSlide) {
	      	this.currentSlideIndex++;
	      	if (this.timer) {
	      		this.reset();
	      	}
	        
	      }
	    },

	    setSlide(i) {
	    	this.currentSlideIndex = i;
	    	if (this.timer) {
	    		this.reset();
	    	}
	    	
	    }
      
  	},
  	computed: {
  		isFirstSlide() {
  			return this.currentSlideIndex === 0;
  		},
  		isLastSlide() {
  			const lastSlide = this.slides.length - 1;
  			return this.currentSlideIndex === lastSlide;
  		}
  	}
}	
</script>

<style src="./styles.css"></style>
<style lang="scss" scoped>@import url('https://fonts.googleapis.com/css?family=Lato:700');</style>
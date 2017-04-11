<template>
	<div class="slider-image" :data-slide-id="slide.id">
		<img :src="slide.img" :alt="slide.body">
        <div class="slider-info">
          <h4>{{slide.title}}</h4>
          {{body}}
        </div>
        <div class="slider-bar-primary">
      		<div class="slider-bar-secondary" :style="{ width: progress + '%'}"></div>
      	</div>
    </div>
</template>

<script>
import {truncateString} from './util';

export default {
	props: {
		slide: {
			type: Object,
			required: true
		},
		progress: {
			type: Number,
			required: true
		}
	},
	computed: {
		body() {
			return truncateString(this.slide.body, 100, true);
		}
	}
}
</script>

<style>
.slider-bar-primary {
	width: 100%;
	background-color: rgba(255,255,255,0.5);
	height: 5px;
	position: absolute;
	bottom: 0;
	left: 0;
	z-index: 10000;
}

.slider-bar-secondary {
	width: 0%;
	height: inherit;
	background-color: red;
}	
</style>
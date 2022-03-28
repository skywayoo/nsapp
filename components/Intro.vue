<template>
  <Page class="page">
     <GridLayout class="carousel-layout-fix"  rows="*, auto" >
        <Carousel id="carousel" @pageChanged="myChangeEvent" row="0" ios:indicatorOffset="0,-30" ios:finite="true" ios:bounce="false" showIndicator="true" indicatorAnimation="SWAP"
            indicatorColor="#66ccff" indicatorColorUnselected="#cceeff" height="100%" width="100%" debug="true">
            
            <CarouselItem id="slide1" verticalAlignment="middle">
            </CarouselItem>

            <CarouselItem id="slide2" verticalAlignment="middle">
            </CarouselItem>

            <CarouselItem id="slide3" verticalAlignment="middle">
            </CarouselItem>
        </Carousel>
        <button text="開始使用" row="1" @tap = "gomain" style="background-color: #e01f3d; color : white; font-size:25px ;"/>
    </GridLayout>

  </Page>
</template>

<script>
  const carousel = require("nativescript-carousel");
  import HelloWorld from './HelloWorld';

  export default {
    data() {
      return {
        message: "Fear not, young Skywalker"
      }
    },
    created() {
    },
    methods: {
      onTextRecognitionResult() {
        console.log("onTextRecognitionResult");
      },
	  myChangeEvent: function(args) {
		var changeEventText = 'Page changed to index: ' + args.index;
		console.log(changeEventText);
      },
      gomain() {
        this.$navigateTo(HelloWorld);
      },
    }
  }
</script>

<style scoped>
  #slide1 {
    background-image: url("/storage/emulated/0/Android/data/org.nativescript.playvue/slide1.jpg");
    background-repeat:no-repeat;
    background-size: contain;
    background-position: center; 
    vertical-align: center;
  }
  
  #slide2 {
    background-image: url("/storage/emulated/0/Android/data/org.nativescript.playvue/slide2.jpg");
    background-repeat:no-repeat;
    background-size: contain;
    background-position: center; 
    vertical-align: center;
  }
  
  #slide3 {
    background-image: url("/storage/emulated/0/Android/data/org.nativescript.playvue/slide3.jpg");
    background-repeat:no-repeat;
    background-size: contain;
    background-position: center; 
    vertical-align: center;
  }
  
  .carousel-layout-fix{
    padding: 0.1 0;
    margin: -0.1 0;
  }
</style>
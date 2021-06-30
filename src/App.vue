<template>
<v-container>
<v-row><h1>Camera</h1></v-row>
 <v-row>
    <v-col
      cols="12"
      sm="12"
      md="12"
      lg="12"
      xl="12"
      class="mt-0 pt-0 pb-0 mb-0"
    >
      <v-card
        id="Camera"
        width="100%"
        min-height="380"
        class="Camera"
        elevation="0"
      >
      </v-card>
    </v-col>
 </v-row>
 <v-btn @click="recordVideo">Capture</v-btn>
 <v-btn @click="startAgain">Retry</v-btn>
</v-container>

</template>

<script>
import Webcam from "webcamjs";

export default {
  name: 'App',
  data: () => ({
    imagesArray: [],
    faceSnap: '',
    interval: null,
    newInterval: null,
  }),
  methods: {
    accessCamera() {
      Webcam.attach("#Camera");
      Webcam.set('constraints',{
        image_format: "jpeg",
        jpeg_quality: 90,
        width: "100%",
        height: "380",
        facingMode: "environment"
      });
    },

    recordVideo() {

      navigator.mediaDevices
        .getUserMedia({
          audio: false,
          video: true,
          facingMode: "environment"
        })
        .then((stream) => {
          // console.log(stream, "stream:")
          const video = document.querySelector("video");
          video.srcObject = stream;
          video.onloadedmetadata = (e) => video.play();

          var canvas = document.createElement("canvas");
          canvas.width = 600;
          canvas.height = 380;
          var ctx = canvas.getContext("2d");

          this.interval = setInterval(() => {
            ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
            let imageData = canvas.toDataURL();
            this.imagesArray.push(imageData);
            console.log("imagesArray",this.imagesArray)
          }, 700);

          // this.newInterval = setInterval(() => {
          //   this.captureImageTimmer -= 1;
          // }, 1000);

          setTimeout(() => {
            // var track = stream.getTracks()[0]; // if only one media track
            // // ...
            // track.stop();
            clearInterval(this.interval);
            // clearInterval(this.newInterval);
            // this.newInterval = null;
            this.interval = null;
            Webcam.snap(async (data_uri) => {
              this.faceSnap = data_uri;
              console.log(this.faceSnap, "faceSnap::")
              // this.$store.commit("user/setFaceImage", this.faceSnap);
              document.getElementById("Camera").innerHTML =
                '<img style="display:flex;align-items: center; justify-content: center; width:95%; margin-left:12px; border-radius: 0px !important; " src="' +
                data_uri +
                '"/>';
            });
          }, 5000);
        });
    },
    startAgain(){
      this.accessCamera();

    }
  },
  mounted(){
    this.accessCamera();
  },
};
</script>

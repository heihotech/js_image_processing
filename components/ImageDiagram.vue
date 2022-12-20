<script>
import { toPng, toJpeg, toBlob, toPixelData, toSvg } from 'html-to-image'
export default {
  data() {
    return {
      markers: [],
      size: 10,
      currentImgWidth: 0,
      currentImgHeight: 0,
      img: null,
      newImgUrl: null,
    }
  },
  methods: {
    findPosition(oElement) {
      if (typeof oElement.offsetParent != 'undefined') {
        for (
          var posX = 0, posY = 0;
          oElement;
          oElement = oElement.offsetParent
        ) {
          posX += oElement.offsetLeft
          posY += oElement.offsetTop
        }
        return [posX, posY]
      } else {
        return [oElement.x, oElement.y]
      }
    },
    getCoordinates(e) {
      var posX = 0
      var posY = 0

      var imgPos = this.findPosition(this.img)
      if (!e) var e = window.event
      if (e.pageX || e.pageY) {
        posX = e.pageX
        posY = e.pageY
      } else if (e.clientX || e.clientY) {
        posX =
          e.clientX +
          document.body.scrollLeft +
          document.documentElement.scrollLeft
        posY =
          e.clientY +
          document.body.scrollTop +
          document.documentElement.scrollTop
      }
      posX = posX - imgPos[0]
      posY = posY - imgPos[1]

      this.markers.push({
        x: posX,
        y: posY,
        imgWidth: this.img.clientWidth,
        imgHeight: this.img.clientHeight,
      })
    },
    async submitPicture() {
      this.newImgUrl = await toJpeg(document.getElementById('img-area'))
    },
  },
  mounted() {
    this.img = document.getElementById('male-body-img')
    this.currentImgWidth = this.img.clientWidth
    this.currentImgHeight = this.img.clientHeight
  },
}
</script>

<template>
  <div>
    <b-card no-body class="overflow-hidden">
      <b-row no-gutters>
        <b-col v-if="newImgUrl !== null" md="6">
          <b-card-img
            id="new-male-body-img"
            :src="newImgUrl"
            alt="Image"
            class="rounded-0"
          >
          </b-card-img>
        </b-col>
        <b-col md="6" id="img-area">
          <div id="img-area">
            <img
              id="male-body-img"
              src="~/static/img/male-body.jpg"
              alt="Image"
              class="rounded-0"
              @click="(e) => getCoordinates(e)"
            />

            <div
              v-for="(marker, index) in markers"
              :key="`${marker.x}-${marker.y}-${index}`"
            >
              <span
                :style="`width: ${size}px; height: ${size}px; background: red; position: absolute; top: ${marker.y}px; left: ${marker.x}px; border-radius: ${size}px`"
              ></span>
              <span
                :style="`margin-top: 2px; position: absolute; top: ${
                  marker.y + size
                }px; left: ${marker.x}px;`"
                >{{ index + 1 }}</span
              >
            </div>
          </div>
        </b-col>
        <b-col md="6">
          <b-card-body title="Horizontal Card">
            <button class="btn btn-primary" @click="submitPicture">
              Submit
            </button>
            <b-card-text>
              This is a wider card with supporting text as a natural lead-in to
              additional content. This content is a little bit longer.
            </b-card-text>
          </b-card-body>
        </b-col>
      </b-row>
    </b-card>
  </div>
</template>

<template>
  <div id="ring-container" class="radial-progress-container">
    <div class="radial-progress-inner" :style="innerCircleStyle">
      <slot></slot>
    </div>
    <svg
      id="radial-progress-bar"
      class="radial-progress-bar"
      :width="diameter"
      :height="diameter"
      version="1.1"
      xmlns="http://www.w3.org/2000/svg"
    >
      <circle
        :r="innerCircleRadius"
        :cx="radius"
        :cy="radius"
        fill="transparent"
        :stroke="innerStrokeColor"
        :stroke-dasharray="4"
        stroke-dashoffset="0"
        stroke-linecap="round"
        :style="strokeStyle"
      ></circle>

      <path :d="arcPath" fill="none" :stroke="outerStrokeColor" stroke-width="5px"></path>
      <image :xlink:href="imgButton" :width="imgSize" :height="imgSize" :style="btnMover"></image>
    </svg>
    <div
      class="absolute inset-0 flex justify-center items-center inside-circle"
      style="flex-direction: column;"
      :style="innerCircle"
    >
      <i class="fa fa-bolt"></i>
      <p class="text-center font-normal" :style="titleFontSize">
        <slot name="title"></slot>
      </p>
      <p class="text-center font-normal p-4">
        <a :href="link" class="flex items-center hover:text-black">
          <p :style="subtitleFontSize">
            <slot name="sub-title"></slot>
          </p>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
            class="w-4 h-4 fill-current text-black ml-2"
          >
            <path fill="none" d="M0 0h24v24H0z"></path>
            <path
              d="M12 0C5.4 0 0 5.4 0 12s5.4 12 12 12 12-5.4 12-12S18.6 0 12 0zm-1.1 16.7c-.3.3-.8.3-1.1.1-.3-.3-.3-.8-.1-1.1 0 0 0-.1.1-.1l3.7-3.6-3.7-3.6c-.3-.3-.3-.8 0-1.1.3-.3.8-.3 1.1 0l4.8 4.7-4.8 4.7z"
            ></path>
          </svg>
        </a>
      </p>
      <div class="relative">
        <a :href="link">
          <p class="btn" :class="btnClass" :style="btnTitle">
            <slot name="btn-text"></slot>
          </p>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    totalSteps: {
      type: Number,
      required: true,
      default: 10
    },
    completedSteps: {
      type: Number,
      required: true,
      default: 0
    },
    strokeWidth: {
      type: Number,
      required: false,
      default: 5
    },
    direction: {
      type: Boolean,
      required: false,
      default: true
    },
    animateSpeed: {
      type: Number,
      required: false,
      default: 1000
    },
    fps: {
      type: Number,
      required: false,
      default: 60
    },
    timingFunc: {
      type: String,
      required: false,
      default: "linear"
    },
    link: {
      type: String,
      required: false,
      default: "#"
    }
  },
  data() {
    return {
      gradient: {
        fx: 0.99,
        fy: 0.5,
        cx: 0.5,
        cy: 0.5,
        r: 0.65
      },
      gradientAnimation: null,
      currentAngle: 0,
      strokeDashoffset: 0
    };
  },
  data() {
    return {
      diameter: 300,
      gradient: {
        fx: 0.99,
        fy: 0.5,
        cx: 0.5,
        cy: 0.5,
        r: 0.65
      },
      gradientAnimation: null,
      currentAngle: 0,
      strokeDashoffset: 0
    };
  },

  computed: {
    radius() {
      return this.diameter / 2;
    },
    circumference() {
      return Math.PI * this.innerCircleDiameter;
    },
    stepSize() {
      if (this.totalSteps === 0) {
        return 0;
      }
      return 100 / this.totalSteps;
    },
    finishedPercentage() {
      return this.stepSize * this.completedSteps;
    },
    circleSlice() {
      return (2 * Math.PI) / this.totalSteps;
    },
    animateSlice() {
      return this.circleSlice / this.totalPoints;
    },
    innerCircleDiameter() {
      return this.diameter - this.strokeWidth * 2 - this.imgSize / 2;
    },
    innerCircleRadius() {
      return this.innerCircleDiameter / 2;
    },
    totalPoints() {
      return this.animateSpeed / this.animationIncrements;
    },
    animationIncrements() {
      return 1000 / this.fps;
    },
    containerStyle() {
      return {
        height: `${this.diameter}px`,
        width: `${this.diameter}px`
      };
    },
    innerProgressStyle() {
      return {
        strokeWidth: 1
      };
    },
    progressStyle() {
      return {
        height: `${this.diameter}px`,
        width: `${this.diameter}px`,
        strokeWidth: `${this.strokeWidth}px`,
        strokeDashoffset: this.strokeDashoffset
      };
    },
    strokeStyle() {
      return {
        height: `${this.diameter}px`,
        width: `${this.diameter}px`,
        strokeWidth: `2px`
      };
    },
    innerCircleStyle() {
      return {
        width: `${this.innerCircleDiameter}px`
      };
    },
    innerStrokeColor() {
      return "#eee";
    },
    outerStrokeColor() {
      if (this.direction === true) {
        return this.completedSteps <= 4
          ? "#FF3434"
          : this.completedSteps < 7
          ? "#FF8A34"
          : "#0D8";
      } else {
        return this.completedSteps <= 6
          ? "#0D8"
          : this.completedSteps < 10.5
          ? "#FF8A34"
          : "#FF3434";
      }
    },
    imgButton() {
      const imgGreen = require("../../svg/icons/add_green.png");
      const imgOrange = require("../../svg/icons/add_orange.png");
      const imgRed = require("../../svg/icons/add_red.png");

      if (this.direction === true) {
        if (this.completedSteps <= 4) return imgRed;
        else if (this.completedSteps > 4 && this.completedSteps < 7)
          return imgOrange;
        else {
          console.log(this.completedSteps);
          return imgGreen;
        }
      } else {
        if (this.completedSteps <= 6) return imgGreen;
        else if (this.completedSteps > 6 && this.completedSteps < 10.5)
          return imgOrange;
        else return imgRed;
      }
    },
    imgSize() {
      return this.diameter / 16.0;
    },
    btnMover() {
      const completedSteps =
        this.completedSteps > this.totalSteps
          ? this.totalSteps
          : this.completedSteps;
      const x =
        this.radius +
        this.innerCircleRadius *
          Math.sin((completedSteps * 2 * Math.PI) / this.totalSteps) -
        this.imgSize / 2;
      const y =
        this.radius -
        this.innerCircleRadius *
          Math.cos((completedSteps * 2 * Math.PI) / this.totalSteps) -
        this.imgSize / 2;
      return { x, y };
    },
    btnClass() {
      if (this.direction === true) {
        if (this.completedSteps <= 4) return "btn-red";
        else if (this.completedSteps > 4 && this.completedSteps < 7)
          return "btn-orange";
        else return "btn-green";
      } else {
        if (this.completedSteps <= 6) return "btn-green";
        else if (this.completedSteps > 6 && this.completedSteps < 10.5)
          return "btn-orange";
        else return "btn-red";
      }
    },
    titleFontSize() {
      const fontSize = this.diameter / 10.0;
      return { "font-size": `${fontSize}px` };
    },
    subtitleFontSize() {
      const padding = (this.diameter / 300.0) * 8;
      const fontSize = this.diameter / 20;
      return {
        padding: `${padding}px`,
        "font-size": `${fontSize}px`
      };
    },
    btnTitle() {
      const fontSize = (this.diameter / 300.0) * 16;
      const fontWeight = 800;
      return {
        "font-size": `${fontSize}px`,
        "font-weight": fontWeight
      };
    },
    innerCircle() {
      const width = this.diameter;
      const height = this.diameter;
      return { width: `${width}px`, height: `${height}px` };
    },
    arcPath() {
      const completedSteps =
        this.completedSteps > this.totalSteps
          ? this.totalSteps
          : this.completedSteps;
      const angle = (completedSteps * 360) / this.totalSteps;
      const start = this.polarToCartesian(
        this.radius,
        this.radius,
        this.innerCircleRadius,
        angle
      );
      const end = this.polarToCartesian(
        this.radius,
        this.radius,
        this.innerCircleRadius,
        0
      );
      const largeArcFlag = angle <= 180 ? 0 : 1;
      if (angle === 360) {
        const halfPt = this.polarToCartesian(
          this.radius,
          this.radius,
          this.innerCircleRadius,
          180
        );
        return `M ${start.x} ${start.y} A ${this.innerCircleRadius} ${
          this.innerCircleRadius
        } 0 1 0 ${halfPt.x} ${halfPt.y} M ${halfPt.x} ${halfPt.y} A ${
          this.innerCircleRadius
        } ${this.innerCircleRadius} 0 1 0 ${start.x} ${start.y}`;
      } else
        return `M ${start.x} ${start.y} A ${this.innerCircleRadius} ${
          this.innerCircleRadius
        } 0 ${largeArcFlag} 0 ${end.x} ${end.y}`;
    }
  },
  methods: {
    getStopPointsOfCircle(steps) {
      const points = [];
      for (let i = 0; i < steps; i++) {
        const angle = this.circleSlice * i;
        points.push(this.getPointOfCircle(angle));
      }
      return points;
    },
    getPointOfCircle(angle) {
      const x = this.radius + this.radius * Math.cos(angle);
      const y = this.radius + this.radius * Math.sin(angle);
      return { x, y };
    },
    gotoPoint() {
      const point = this.getPointOfCircle(this.currentAngle);
      this.gradient.fx = point.x;
      this.gradient.fy = point.y;
    },
    changeProgress({ isAnimate = true }) {
      this.strokeDashoffset =
        ((100 - this.finishedPercentage) / 100) * this.circumference;
      if (this.gradientAnimation) {
        clearInterval(this.gradientAnimation);
      }
      if (!isAnimate) {
        this.gotoNextStep();
        return;
      }
      const angleOffset = (this.completedSteps - 1) * this.circleSlice;
      let i = (this.currentAngle - angleOffset) / this.animateSlice;
      const incrementer = Math.abs(i - this.totalPoints) / this.totalPoints;
      const isMoveForward = i < this.totalPoints;
      this.gradientAnimation = setInterval(() => {
        if (
          (isMoveForward && i >= this.totalPoints) ||
          (!isMoveForward && i < this.totalPoints)
        ) {
          clearInterval(this.gradientAnimation);
          return;
        }
        this.currentAngle = angleOffset + this.animateSlice * i;
        this.gotoPoint();
        i += isMoveForward ? incrementer : -incrementer;
      }, this.animationIncrements);
    },
    gotoNextStep() {
      this.currentAngle = this.completedSteps * this.circleSlice;
      this.gotoPoint();
    },
    getComponentSize() {
      const el = document.getElementById("ring-container");
      const width = el.clientWidth;
      // const height = el.clientHeight;
      return;
    },
    polarToCartesian(centerX, centerY, radius, angleInDegrees) {
      const angleInRadians = ((angleInDegrees - 90) * Math.PI) / 180.0;
      return {
        x: centerX + radius * Math.cos(angleInRadians),
        y: centerY + radius * Math.sin(angleInRadians)
      };
    }
  },
  watch: {
    totalSteps() {
      this.changeProgress({ isAnimate: true });
    },
    completedSteps() {
      this.changeProgress({ isAnimate: true });
    },
    diameter() {
      this.changeProgress({ isAnimate: true });
    },
    strokeWidth() {
      this.changeProgress({ isAnimate: true });
    }
  },
  created() {
    this.changeProgress({ isAnimate: true });
  },
  mounted() {
    const self = this;
    self.diameter = document.getElementById("ring-container").clientWidth;
    window.addEventListener("resize", () => {
      self.diameter = document.getElementById("ring-container").clientWidth;
    });
  }
};
</script>

<style>
.radial-progress-container {
  position: relative;
}
.radial-progress-inner {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  position: absolute;
  border-radius: 50%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.inside-circle {
  border-radius: 50%;
  transform: scale(0.8);
  box-shadow: 0px 8px 20px rgba(0, 0, 0, 0.1);
}

.btn-red {
  background-color: #ff3434;
  color: white;
}

.btn-orange {
  background-color: #ff8a34;
  color: white;
}

.btn-green {
  background-color: #00dd88;
  color: white;
}
</style>

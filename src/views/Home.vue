<script>
export default {
  name: 'Home',
  data() {
    return {
      query: '',
      appearance: {
        backgroundColor: '#eee',
      },
      options: {
        normalScrollElements: '.test-container, .modal-active, .modal-inactive, .more-work-grid, .more-work-container, .modal-images',
        controlArrows: true,
        loopHorizontal: false,
        scrollingSpeed: 600,
        slidesNavigation: true,
        //navigation: true,
        fadingEffect: true,
        navigationPosition: 'left',
        navigationTooltips: ['Home','Section 2', 'Section 3', 'Section 4','Section 5','Section 6'],
        showActiveTooltip: true,
        easingcss3: 'cubic-bezier(0.22, 1, 0.36, 1)', //swoopy
        anchors: ['home','section2', 'section3', 'section4','section5','section6'],
        keyboardScrolling: false,
        onLeave: (origin, destination, direction) => {
          this.handleLeave(origin, destination, direction);
        },
        onSlideLeave: (origin, destination, direction) => {
          this.handleSlideLeave(origin, destination, direction);
        }
      },
      triggerUp: false,
      triggerDown: false,
      activeSection: 0,
      activeSlide: 1,
      hover: false,
      scrollOverflow: false,
      context: 0,
    }
  },
  props: {
    dataRef: Object
  },
  components: {

  },
  created() {

  },
  mounted() {
      this.$refs.fullpage.api.setMouseWheelScrolling(false);
      this.$refs.fullpage.api.setAllowScrolling(false);

      //Navigator
      this._keyListener = function(e) {
          //console.log(e.keyCode);

          //Avian view
          if (e.ctrlKey && e.keyCode === 32) {
              this.toggleAvian();
              e.preventDefault();
          }

          //desktopUp
          if (e.ctrlKey && e.keyCode === 38) {
              this.desktopUp();
              e.preventDefault();
          }

          //desktopDown
          if (e.ctrlKey && e.keyCode === 40) {
              this.desktopDown();
              e.preventDefault();
          }
      };

      document.addEventListener('keyup', this._keyListener.bind(this));
  },
  beforeUnmount() {
      document.removeEventListener('keyup', this._keyListener);
  },
  methods: {
    toggleAvian() {
      console.log('Avian view');
    },
    desktopUp() {
      console.log('desktopUp');
      this.$refs.fullpage.api.moveSectionUp();
    },
    desktopDown() {
      console.log('desktopDown');
      this.$refs.fullpage.api.moveSectionDown();
    },
    handleLeave(origin, destination, direction) {
      //console.log('origin: ', origin);
      //console.log('destination: ', destination);
      //console.log('direction: ', direction);
  
      this.activeSection = destination.index;

      //console.log('activeSection: ', this.activeSection);

      if(direction == 'up') {
        this.triggerUp = true;
        this.triggerDown = false;
        //console.log('going up');
      }
      else {
        this.triggerDown = true;
        this.triggerUp = false;
        //console.log('going down');
      }

      if(destination.index == 0) {
        //console.log('on first slide');
      }
    },
    handleSlideLeave(origin, destination, direction) {
      console.clear();
      //console.log('origin: ', origin);
      //console.log('destination: ', destination);
      //console.log('direction: ', direction);
  
      this.activeSlide = destination.index;

      //console.log('activeSlide: ', this.activeSlide);

      if(direction == 'up') {
        this.triggerUp = true;
        this.triggerDown = false;
      }
      else {
        this.triggerDown = true;
        this.triggerUp = false;
      }
    },
    handleMouseEnter(i, index) {
      //console.clear();
      console.log('enter' , index);
      this.hover = true;
      this.currentPreview = i.thumb;
      this.currentProjectText = i.description;
      this.currentProjectTitle = i.title;
      this.context = index;

      console.log(this.currentPreview);
    },
    handleMouseLeave(i, index) {
      //console.clear();
      console.log('leave', index);

      this.hover = false;

      /*if(!this.modalActive) {}*/
    },
    handleScroll() {
      //console.log(scrollY);
      //this.bannerOffset = scrollY;
    },
    goDown() {
      this.$refs.fullpage.api.moveSectionDown()
    }
  }
}
</script>

<template>
  <div class="home">

    <!-- optional top layer for modals & such -->
    <!--div class="top-layer">
      <div :class="( smallEnter ? 'enter' : 'stage-left' )" class="modal-small">This is some text</div>
      <div :class="( bigEnter ? 'enter' : 'stage-left' )" class="modal-big"></div>
    </div-->

    <!-- navigation -->
    <div id="nav" class="hoverable">
      <div class="link-list hoverable">

      </div>
    </div>

    <!-- main (fullpage.js; TODO: Configuure locomotive scroll) -->
    <full-page ref="fullpage" :options="options" id="fullpage">
      
      <!-- Section -->
      <section class="section">
        <div class="page-container" :style="'background-color: ' + appearance.backgroundColor" :class="( activeSection == 0 ? '' : 'stage-section' )">
            <div class="landing-search-container">
              <p class="search-label">Open an app or type a URL</p>
              <input type="text" id="main-search" v-model="query">
            </div>
        </div>
      </section>

      <!-- Section -->
      <section class="section">
        <div class="page-container" :style="'background-color: ' + appearance.backgroundColor" :class="( activeSection == 1 ? '' : 'stage-section' )">
          <webview class="webview" src="https://www.github.com/"></webview>
        </div>
      </section>

      <!-- Section -->
      <section class="section">
        <div class="page-container" :style="'background-color: ' + appearance.backgroundColor" :class="( activeSection == 2 ? '' : 'stage-section' )">
          desktop3
        </div>
      </section>

    </full-page>
  </div>
</template>

<style lang="scss" scoped>
@import '../assets/styles/global';

.webview {
  width: 100vw;
  height: 100vh;
  border: none;
  border-radius: 12px 12px 0px 0px;
}

.search-label {
  text-align: center;
  font-size: 13px;
}

#main-search {
  border-radius: 36px;
  border: none;
  padding: 6px 6px 6px 12px;
  width: 360px;
  text-align: center;
}

.section {
  border-radius: 16px;
  //background: gray;
}

.home {
  //border-radius: 12px 12px 0px 0px;
  margin-top: $topbarHeight;
}

#fullpage {
  //pointer-events: none;
  overflow: hidden;
  margin-top: 24px;
  background: black;
  //border-radius: 12px 12px 0px 0px;
}

.page-container {
  height: 100vh;
  width: 100%;
  margin: 0px;
  padding: 0px;
  display: flex;
  justify-content: center;
  align-items: center;
  background: blue;
  transition: 600ms;
  border-radius: 12px 12px 0px 0px;
}

.arrows {
  position: relative;
  width: 30px;
  height: 30px;
  transform: scale(0.5);
  margin-top: 190px;
}

.arrows:before {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  border-left: 26.66667px solid rgba(0, 0, 0, 0.7);
  border-bottom: 26.66667px solid rgba(0, 0, 0, 0.7);
  transform: translate(26.66667px, 106.66667px) rotate(-45deg);
  animation: arrows 3s linear infinite;
}

.arrows:after {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  border-left: 26.66667px solid rgba(0, 0, 0, 0.7);
  border-bottom: 26.66667px solid rgba(0, 0, 0, 0.7);
  transform: translate(53.33333px, 0px) rotate(-45deg);
  animation: arrows 3s linear infinite -1.5s;
}

@keyframes arrows {
  0% {
    border-left: 10px solid transparent;
    border-bottom: 10px solid transparent;
    transform: translate(-13.33333px, -53.33333px) rotate(-45deg);
  }
  10%, 90% {
    border-left: 10px solid transparent;
    border-bottom: 10px solid transparent;
  }
  50% {
    border-left: 10px solid rgba(255, 255, 255, 0.7);
    border-bottom: 10px solid rgba(255, 255, 255, 0.7);
    transform: translate(-13.33333px, 0px) rotate(-45deg);
  }
  100% {
    border-left: 26.66667px solid transparent;
    border-bottom: 26.66667px solid transparent;
    transform: translate(-13.33333px, 53.33333px) rotate(-45deg);
  }
}

.top-layer {
  position: fixed;
  z-index: 99999;
  height: 100vh;
  width: 100%;
  display: flex;
  pointer-events: none;
  justify-content: center;
  align-items: center;
}

.soc-container {
  position: fixed;
  top: $pad;
  right: $pad;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
  //background: red;
}

.landing-container {
  background-image: url('../assets/sample-logo-white.png');
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
  width: 500px;
  height: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: auto;
  padding: 0px;
  position: absolute;
  left: 0px;
  right: 0px;
  bottom: 0px;
  top: 0px;

  h1 {
    font-size: 48px;
  }
}

.landing {
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.wave-panel {
  background: #181818;
  position: fixed;
  height: 100vh;
  width: 100%;
  margin: 0px;
  z-index: 9;
  transition: 2s;
  pointer-events: none;

  .wave {
    height: 100vh;
    transform: translateX(0px) scale(4);
    width: 175px;
    position: absolute;
    background-image: url('../assets/wave.svg');
    background-size: contain;
    background-position-y: 50%;
    background-repeat: repeat-y;
    transition: 2s;
  }
}

.wave-left {
  .wave {
    background-position-y: 7% !important;
    //background: rgba(yellow, 0.2);
  }
}

.wave-right {
  .wave {
    background-position-y: 50% !important;
    //background: rgba(aqua, 0.2);
  }
}

.main-title {
  //background:red;
  width: 70px;
  background-image: url("../assets/sample-emblem-white.png");
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
  height: 70px;
  margin: 42px;
}

.hoverable {
  cursor: pointer;
}

.project-text {
  font-size: 14px !important;
}

.mwm-expanded {
  background: white;
  width: auto;
  height: 70%;
  border-radius: 18px;
  min-width: 25%;
}

.mwm-inactive {
  //background: blue;
  width: 100vw;
  height: 100vh;
  position: absolute;
  padding: 0px;
  margin: 0px;
  z-index: 999;
  transition: 1s;
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 0;
  pointer-events: none;
  
  img {
    transition: 600ms;
    transform: scale(0.8);
    opacity: 0;
  }
}

.mvm-active {
  background: rgba(black, 0.8);
  width: 100vw !important;
  height: 100vh !important;
  position: absolute;
  padding: 0px;
  margin: 0px;
  z-index: 999;
  transition: 1s;
  display: flex;
  justify-content: center;
  align-items: center;
  opacity: 1;
  transition-delay: 150ms;

  img {
    transition: 600ms;
    transform: scale(1);
  }
}

.modal-title {
  font-size: 36px;
}

.image-list {
  list-style: none;
  padding: 0px;
  margin: 0px;
}

.modal-text {
  //@extendanimation: flyleft 1s ease forwards 3s;
  width:300px;
  text-align: left;
}

.modal {
  display: flex;
  justify-content: center;
  align-items: center;
  //background: purple;
  transition: 1s;
}

#nav {
  position: fixed;
  z-index: 9999;

  h1 {
    padding-left: 18px;
  }

  ul {
    list-style: none;
    text-align: left;
    font-family: 'Inconsolata', monospace;

    li {
      margin-top: 6px;
      font-size: 18px;

      &:hover {
        span {
          opacity: 1;
          padding-right: 8px;
        }
      }

      span {
        opacity: 0;
        margin-right: 12px;
        transition: 300ms;
        font-weight: bold;
      }
    }
  }

  a {
    color: white;
    text-decoration: none;
    opacity: 0.7;
    //cursor: none;

    &.router-link-exact-active {
      color: white;
      opacity: 1;
      font-weight: bold;
    }
  }
}

//grid flyin animation
.grid-item {
  background: #555;
  height: 250px;
  transition: 1.2s;

  &:hover {
    //transform: scale(0.95);
    //filter:hue-rotate(145deg);
  }

  &:nth-child(1) {
    transition-delay: 1.4s;  
  }

  &:nth-child(2) {
    transition-delay: 1.2s;  
  }

  &:nth-child(3) {
    transition-delay: 1s;  
  }

  &:nth-child(4) {
    transition-delay: 1.6s;  
  }

  &:nth-child(5) {
    transition-delay: 1.4s;  
  }

  &:nth-child(6) {
    transition-delay: 1.2s;  
  }

  &:nth-child(7) {
    transition-delay: 1.8s;  
  }

  &:nth-child(8) {
    transition-delay: 1.6s;  
  }

  &:nth-child(9) {
    transition-delay: 1.4s;  
  }
}

.modal-active {
  background: black;
  width: 100vw;
  height: 100vh;
  position: absolute;
  z-index: 999;
  transition: 600ms;
  transition-delay: 600ms;
  margin: auto;
  left: 0px;
  top: 0px;
  overflow: auto;
}

.modal-inactive {
  background: black;
  opacity: 0;
  width: 100vw;
  height: 100vh;
  position: absolute;
  z-index: 999;
  transition: 600ms;
  margin: auto;
  left: 0px;
  top: 0px;
  overflow: auto;
  pointer-events: none;
}



.blur {
  filter: blur(24px);
}

.clear {
  filter: blur(0px);
}

@media only screen and (max-width: 900px) {

}
</style>
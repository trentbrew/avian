<script>
import Calendar from 'v-calendar/lib/components/calendar.umd'

export default {
  name: 'App',
  components: {
    Calendar
  },
  data() {
    return {
      unveil: null,
      dataRef: {},
      time: {
        weekdays: ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'],
        months: ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'],
        currentDate: null,
        currentTime: null,
        currentTime24: null,
      },
      weather: {
        currentWeather: null,
        location: 'Chicago, IL',
        degreeType: 'F'
      },
      cursor: {
        xChild: 0,
        yChild: 0,
        xParent: 0,
        yParent: 0,
        hideCursor: false,
        hover: false,
      },
      dialogs: {
        calendarActive: false,
        weatherActive: false,
        menuActive: false,
        calendar: {
          attributes: [
            {
              dot: true,
              popover: {

              },

            }
          ]
        }
      },
    }
  },
  computed: {
    cursorCircle() {
      return `transform: translateX(${this.xParent}px) translateY(${this.yParent}px) translateZ(0) translate3d(0, 0, 0);`
    },
    cursorPoint() {
      return `transform: translateX(${this.xChild - 3}px) translateY(${this.yChild - 3}px) translateZ(0) translate3d(0, 0, 0);`
    }
  },
  mounted() {
    this.getWeather();
    this.startTime();
    setTimeout(() => {
      this.unveil = true;
      //this.waveOffset = 700;
      console.log('unveiled');
    }, 3000);
    document.addEventListener("mousemove", this.moveCursor);
    document.addEventListener('mouseleave', (e) => {
      this.cursor.hideCursor = true;
      console.log('left: ', e.target);
    });
    document.addEventListener('mouseenter', (e) => {
      this.cursor.hideCursor = false;
      console.log('entered: ', e.target);
    });
  },
  methods: {
    getWeather() {
      const weather = require('weather-js');

      weather.find(
        { 
          search: this.weather.location, 
          degreeType: this.weather.degreeType 
        },
        (error, result) => {
          if (error) console.error(error);

          console.log(JSON.stringify(result, null, 2));
          console.log('chicago temp: ', result[0].current.temperature);
          this.weather.currentWeather = result[0].current.temperature + '°' + this.weather.degreeType;
        },
      )
    },
    handleNav(index) {
      console.clear();
      console.log(index);
      console.log('ROUTE HOME');
      this.$refs.fullpage.api.moveTo(1);
    },
    startTime() {
      //console.log('tock');
      var today = new Date();
      var h = today.getHours();
      var m = today.getMinutes();
      //var s = today.getSeconds();
      m = this.checkTime(m);
      //s = this.checkTime(s);
      this.time.currentTime = (h + ":" + m);
      this.time.currentTime24 = ((h < 13 ? h : h - 12 ) + ":" + m + ( h < 13 ? ' AM' : ' PM' ));
      this.time.currentDate = this.time.weekdays[parseInt(today.getDay() - 1)] + ' ' + this.time.months[today.getMonth()] + ' ' + today.getUTCDate();
      setTimeout(this.startTime, 500);
    },
    checkTime(i) {
      if (i < 10) {i = "0" + i}  // add zero in front of numbers < 10
      return i;
    },
    moveCursor(e) {
      var self = this;

      //console.log(e.target.classList);

      if(e.target.classList.contains('hoverable') || e.target.classList.contains('fp-tooltip') || e.target.tagName == "SPAN") {
        //console.log(e.target.tagName);
        //console.log('HOVERABLE:)');
        self.cursor.hover = true;
      } else {
        //console.log('NOT HOVERABLE!');
        self.cursor.hover = false;
      }

      this.cursor.xChild = e.clientX;
      this.cursor.yChild = e.clientY;
      setTimeout(() => {
        this.cursor.xParent = e.clientX - 20;
        this.cursor.yParent = e.clientY - 20;
      }, 100);
    },
    handleDialogClick(context) {
      console.log('clicked ', context);
      if(context == 'calendar') {
        this.dialogs.calendarActive = !this.dialogs.calendarActive;
        this.dialogs.weatherActive = false;
        this.dialogs.menuActive = false;
      } else if(context == 'weather') {
        this.dialogs.calendarActive = false;
        this.dialogs.weatherActive = !this.dialogs.weatherActive;
        this.dialogs.menuActive = false;
      } else if(context == 'menu') {
        this.dialogs.calendarActive = false;
        this.dialogs.weatherActive = false;
        this.dialogs.menuActive = !this.dialogs.menuActive;
      } else {
        console.log('invalid operation');
      }
    }
  }
}
</script>

<template>
  <div id="app">
    <div class="topbar">
      <div class="left">
        Activities
      </div>
      <div class="middle">
        <div class="time-container" @click="handleDialogClick('calendar')">
          {{ time.currentTime24 + ', ' + time.currentDate }}
        </div>
        <div @click="handleDialogClick('weather')" class="weather-container">
          <div class="weather-icon icon"></div> 
          <div class="weather-text">{{ weather.currentWeather }}</div>
        </div>
      </div>
      <div @click="handleDialogClick('menu')" class="right menu icon"></div>
    </div>
    
    <div class="dialog-container">
      <div class="calendar" :class="( dialogs.calendarActive == true ? 'dialog' : 'stage-dialog' )">
        <div class="tri"></div>
        <Calendar trim-weeks is-dark :attributes="dialogs.calendar.attributes" />
      </div>
      
      <div class="weather" :class="( dialogs.weatherActive == true ? 'dialog' : 'stage-dialog' )">
      
      </div>
      
      <div class="menu" :class="( dialogs.menuActive == true ? 'dialog' : 'stage-dialog' )">
      
      </div>
    </div>

    <div :class="(unveil ? 'swoop' : '' )" class="logo-loader"></div>
    <div :class="(unveil ? 'loading-veil unveil' : 'loading-veil')">
    </div>
    <!--div class="cc" :class="[ 'g-cursor', { 'g-cursor_hover': hover }, {'g-cursor_hide': hideCursor} ]">
      <div :style="cursorCircle" class="g-cursor__circle"></div>
      <div class="g-cursor__point" ref="point" :style="cursorPoint"></div>
    </div-->
    <router-view :dataRef='dataRef' />
  </div>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Work+Sans&display=swap');

$colorMain: #212225;
$colorDark: #131417;
$colorLight: #2A2B2E;
$colorSecondary: #DFABBF;

$trans: 200ms;

$translucent: 0.6;

html {
  transition: filter 1s;
  font-family: 'Work Sans', sans-serif !important;
  font-weight:100;
}

body {
  background: $colorMain;
  margin: 0px;
  padding: 0px;
}

.dialog-container {
  position: absolute;
  top: 12px;
  width: 100%;

  .tri {
    border-color: transparent transparent black transparent;
    border-style: solid;
    border-width: 16px 16px 16px 16px;
    margin-left: auto;
    margin-right: auto;
    margin-top:-12px;
    width: 0px;
    height: 0px;
  }

  .vc-container {
    border: none !important;
    background: black !important;
  }

  .vc-day-content, .vc-title, .vc-weekday {
    font-weight: 100 !important;
  }

  .vc-weekday {
    color: $colorSecondary !important;
  }

  .calendar {
    top: 24px;
    left: -78px;
    right: 0px;
    margin: auto !important;
    background: transparent !important;
    width: max-content !important;
    height: auto;
  }

  .weather {
    top: 24px;
    left: 164px;
    right: 0px;
    margin: auto !important;
  }

  .menu {
    top: 24px;
    right: 24px;
    margin: auto !important;
  }
  
  .stage-dialog {
    opacity: 0;
    transform: translateY(-18px);
    pointer-events: none;
    transition: $trans;
    width: 200px;
    height: 300px;
    border-radius: 12px;
    margin: auto;
    z-index: 99999;
    position: absolute;
  }

  .dialog {
    background: $colorDark;
    border-radius: 12px;
    opacity: 1;
    transform: transitionY(0px);
    transition: $trans;
    width: 200px;
    height: 300px;
    margin: auto;
    z-index: 99999;
    position: absolute;
  }
}

.topbar {
  background: black;
  color: white;
  position: fixed;
  top: 0px;
  width: 100vw;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  z-index: 999999;
  font-size: 12px;
  line-height: 24px;

  .left {
    margin-left: 36px;
    cursor: pointer;

    &:hover {
      opacity: $translucent;
    }
  }

  .middle {
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    left: 0px;
    right: 0px;
    margin: auto;
    width: max-content;

    .time-container {
      &:hover {
        opacity: $translucent;
      }
    }

    .weather-container {
      display: flex;
      justify-content: center;
      align-items: center;

      &:hover {
        opacity: $translucent;
      }
    }

    .weather-icon {
      margin-left: 18px;
      margin-right: 8px;
      width: 16px;
      height: 16px;
      background-image: url('assets/icons/weather.svg');
      background-position: center;
      background-size: contain;
      background-repeat: no-repeat;
    }
  }

  .right {
    margin-right: 18px;
    //background: red;
    background-image: url('assets/icons/menu.svg');
    background-position: center;
    background-size: contain;
    background-repeat: no-repeat;
    height: 22px;
    width: 22px;
    cursor: pointer;

    &:hover {
      opacity: $translucent !important;
    }
  }
}

.swoop {
  transform: translate(-78px, -88px) scale(0.08) !important;
  //background: green !important;
  transition: 1s cubic-bezier(0.65, 0, 0.35, 1);
}

.logo-loader {
  position: absolute;
  top: 0px;
  left: 0px;
  transform: translate(calc(50vw - 100px), calc(50vh - 100px));
  margin: auto;
  z-index: 999999;
  width: 200px;
  height: 200px;
  //background: pink;  
  transition: 2s cubic-bezier(0.65, 0, 0.35, 1);
  background-image: url('assets/sample-emblem-white.png');
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
}

.fp-slideNav {
  margin-bottom: 400px !important;

  ul li a span {
    //background: red !important;
  }
}

#fp-nav {

  margin-left: 48px;

  li {
    margin-bottom: 36px !important;
  }

  .fp-tooltip {
    font-size: 18px !important;
    padding-left: 32px;
    line-height: 38px;
  }

  .active span {
    opacity: 1 !important;
    background: white !important;
    border: 2px solid white;
    //transform: translate(4px,4px);

    &:hover {
      //transform: translate(-1px,-1px) !important;
      //transform: scale(1.5);
    }
  }

  span {
    background: white !important;
    //background: rgba(0,0,0,0) !important;
    //height: 24px !important;
    //width: 24px !important;
    //transform: translate(-1px,-1px);
    transform: none !important;

    &:hover {
      //transform: translate(-1px,-1px);
      //transform: scale(1.05);
    }
  }
}

.unveil {
  opacity: 0 !important;
  transition: 4s;
  pointer-events: none;
}

.loading-veil {
  //display: none;
  background: $colorDark;
  opacity: 1;
  width: 100%;
  height: 100vh;
  position: fixed;
  margin: 0px;
  padding: 0px;
  pointer-events: none;
  z-index: 99999;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: 2s;
}

// ---------- transition shit -----------

//transition animation fade

.fade-enter, .fade-leave-to {
  opacity: 0;
  transform: scale(0.95);
}

.fade-enter-active, .fade-leave-active {
  transition: all $trans ease;
}

// --------- cursor stuff ----------

.g-cursor {
    position: absolute;

    &_hide {
      opacity: 0;
      width: 60px;
      height: 60px;
      transition: 
        width .6s ease,
        height .6s ease,
        opacity .6s ease;
    }

    &__circle {
      pointer-events: none;
      user-select: none;
      top: 2px;
      left: 2px;
      position: fixed;
      width: 36px;
      height: 36px;
      border: 2px solid rgba(white, 1);
      mix-blend-mode: difference;
      //background: rgba(white,0.3);
      border-radius: 100%;
      z-index: 5555;
      backface-visibility: hidden;
      transition: 
        margin 0.6s ease,
        opacity 0.6s ease,
        width 0.6s ease,
        height 0.6s ease,
    }

    &__point {
      top: 0;
      left: 0;
      opacity: 0;
      position: fixed;
      width: 10px;
      height: 10px;
      pointer-events: none;
      user-select: none;
      border-radius: 100%;
      background: #fff;
      z-index: 55555555;
      backface-visibility: hidden;
      will-change: transform;
      transition:
        margin .4s ease,
        opacity .4s ease,
        width .4s ease,
        height .4s ease;
    }

    &_hover {
      .g-cursor__point {
          opacity: 0;
          width: 64px;
          height: 64px;
          margin-left: -24px;
          margin-top: -24px;
          border: 2px solid rgba(white, 1);
          //background: white;
          //mix-blend-mode: difference;
          //background: rgba(white, 1);
          //border-color: white;
          transition: 
            margin .4s ease,
            width .4s ease,
            height .4s ease,
            opacity .4s ease,
            transform 0s;
        }

        .g-cursor__circle {
          opacity: 1;
          width: 64px;
          height: 64px;
          margin-left: -12px;
          margin-top: -12px;
          //background: rgba(white, 1);
          //border-color: transparent;
          transition: 
            margin .4s ease,
            width .4s ease,
            height .4s ease,
            opacity .4s ease;
        }
    }
}

@media only screen and (max-width: 900px) {
  .g-cursor {
    display: none !important;
  }
}
</style>

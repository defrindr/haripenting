<template>
  <div class="home">
    <div id="countdown">
      <p class="times">
        {{ countdownToNextEvent }}
      </p>
      <ul v-for="ev in nextEventList" :key="ev">
        <li>{{ ev }}</li>
      </ul>
    </div>
    <div id="info">
      <span class="info__text">
        <p>Waktu Sekarang : {{ now }}</p>
      </span>
      <div class="info__button__area">
        <button id="btnPrev" @click.prevent="bulanLalu">Mundur 1 Bulan</button>
        <button id="btnNext" @click.prevent="bulanDepan">Maju 1 Bulan</button>
      </div>
    </div>
    <div class="event_permonth_count">
      <p>
        Total : <b>{{ countEv }}</b> Event Di Bulan {{ currentMonth }}
      </p>
    </div>
    <div id="content">
      <div class="col-md-4 col-sm-12 col-xs-12" v-for="ev in events" :key="ev.date">
        <article>
          <div class="article__head">
            <p class="title">{{ ev.date }}</p>
          </div>
          <div class="article__content" v-for="row in ev.events" :key="row">
            <ul>
              <li>{{ row }}</li>
            </ul>
          </div>
        </article>
      </div>
    </div>
  </div>
</template>

<script>
import source from '@/data/source.json'
import month from '@/data/month.json'

export default {
  name: 'HomeView',
  data() {
    return {
      now: '',
      month: month,
      events: [],
      currentMonth: '',
      currentMonthInNumber: 0,
      countEv: 0,
      s: source.data,
      countdownToNextEvent: '',
      nextEventDate: '',
      nextEventList: [],
    }
  },
  methods: {
    findData(month) {
      let data = this.s.filter((data) => {
        if (data.date.toLowerCase().includes(month)) {
          return data
        }
      })

      this.countEv = data.length

      return data
    },
    checkBulan() {
      if (this.currentMonthInNumber - 1 <= -2 || this.currentMonthInNumber + 1 >= 13) {
        console.log(this.currentMonthInNumber)
        return false
      }

      let btnPrev = document.getElementById('btnPrev')
      btnPrev.disabled = ''
      let btnNext = document.getElementById('btnNext')
      btnNext.disabled = ''

      return true
    },
    bulanLalu() {
      this.currentMonthInNumber -= 1
      if (this.checkBulan()) {
        this.currentMonth = this.month[this.currentMonthInNumber]
        this.events = this.findData(this.currentMonth.toLowerCase())
      } else {
        let btn = document.getElementById('btnPrev')
        btn.disabled = 'disabled'
      }
    },
    bulanDepan() {
      this.currentMonthInNumber += 1
      if (this.checkBulan()) {
        this.currentMonth = this.month[this.currentMonthInNumber]
        this.events = this.findData(this.currentMonth.toLowerCase())
      } else {
        let btn = document.getElementById('btnNext')
        btn.disabled = 'disabled'
      }
    },
    getNextEventInfo() {
      let evtempToNextEv = this.s.filter((data) => {
        let oldDate = (data.date + ' ' + new Date().getFullYear()).split(' ')

        let strtotime1 = new Date(Date.UTC(oldDate[2], this.month.indexOf(oldDate[1]), oldDate[0]))

        // jika waktu event dikurangi waktu sekarang lebih besar dari 0 maka return data
        if (strtotime1 - Date.now() >= 0) {
          return data
        }
      })[0] // mengambil data dengan index 0

      this.nextEventList = evtempToNextEv.events
      this.nextEventDate = evtempToNextEv.date + ' ' + new Date().getFullYear()
    },
    funcGetCountDown() {
      // parse date of next event
      let dateString = this.nextEventDate.split(' ')

      let date1 = Date.UTC(
        dateString[2], // year
        this.month.indexOf(dateString[1]), // month
        dateString[0], // date
        -7, //-7 set to WIB
        0, // minutes
        0, // second
        0, // mili
      )

      let date2 = Date.now()

      let strtotime = date1 - date2

      let hour = Math.floor((strtotime / (1000 * 60 * 60)) % 24)
      let minutes = Math.floor((strtotime / (1000 * 60)) % 60)
      let seconds = Math.floor((strtotime / 1000) % 60)

      this.countdownToNextEvent = hour + ' : ' + minutes + ' : ' + seconds

      setTimeout(() => {
        this.funcGetCountDown()
      }, 1000)
    },
  },
  mounted() {
    let date = new Date()
    this.currentMonthInNumber = date.getMonth()

    this.now =
      date.getDate() + ' ' + this.month[this.currentMonthInNumber] + ' ' + date.getFullYear()

    this.currentMonth = this.month[this.currentMonthInNumber]

    this.events = this.findData(this.currentMonth.toLowerCase())
    // this.nextEventList =

    this.getNextEventInfo()

    this.funcGetCountDown()
  },
  computed: {},
}
</script>

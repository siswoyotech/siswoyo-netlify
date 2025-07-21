<template>
  <div class="main-container">
    <!-- Left / Sidebar -->
    <div class="left-panel">
      <div class="logo">
        <img class="mosque-logo" src="@/assets/mosque-logo.png" alt="Mosque Logo" />
      </div>
      <div class="title">PRAYER TIMES</div>

      <div
        v-for="(prayer, index) in prayers"
        :key="index"
        class="prayer-row"
      >
        <img :src="prayer.icon" class="icon" />
        <div class="prayer-text">
          <div class="name">{{ prayer.name }}</div>
          <div class="time">{{ prayer.time }}</div>
        </div>
      </div>
    </div>

    <!-- Right / Main visual -->
    <div class="right-panel">
      <!-- Top-right header -->
      <div class="header-info">
        <div class="city">{{ city }}</div>
        <div class="date-row">
          <div class="date">{{ currentDate }}</div>
          <div class="hijri">{{  hijriDate }}</div>
        </div>
      </div>

      <!-- Bottom-right mihrabs -->
      <div class="mihrab-container">
        <div class="mihrab mihrab-left"></div>
        <div class="mihrab mihrab-center"></div>
        <div class="mihrab mihrab-right"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "PrayerTimes",
  data() {
    return {
      city: "Loading...",
      currentDate:"",
      hijriDate:"",
      prayers: [
        //{ name: "IMSAK",   time: "04:10 AM", icon: "https://img.icons8.com/ios/50/crescent-moon.png" },
        //{ name: "FAJR",    time: "04:20 AM", icon: "https://img.icons8.com/ios/50/sunrise.png" },
        //{ name: "DHUHR",   time: "12:05 PM", icon: "https://img.icons8.com/ios/50/sun--v1.png" },
        //{ name: "ASR",     time: "03:15 PM", icon: "https://img.icons8.com/ios/50/sunrise--v2.png" },
        //{ name: "MAGHRIB", time: "06:05 PM", icon: "https://img.icons8.com/ios/50/sunset--v1.png" },
        //{ name: "ISHA",    time: "07:15 PM", icon: "https://img.icons8.com/ios/50/bright-moon.png" },
      ],
    };
  },
  mounted() {
    const arabicDays = [
      "Ahad",       // Sunday
      "Ithnayn",    // Monday
      "Thulatha",   // Tuesday
      "Arbi'aa",    // Wednesday
      "Khamees",    // Thursday
      "Jumu'ah",    // Friday
      "Sabt",       // Saturday
    ];

    // Format and set the current date
    const now = new Date();
    const dayIndex = now.getDay(); // 0 = Sunday
    const arabicDay = arabicDays[dayIndex];
    this.currentDate = now.toLocaleDateString("en-US", {
      weekday: "long",
      year: "numeric",
      month: "long",
      day: "numeric",
    });

    // Fetch user's city using IP geolocation
    // https://ipapi.co/json/
    // https://api.bigdatacloud.net/data/reverse-geocode-client
    fetch("https://api.bigdatacloud.net/data/reverse-geocode-client/")
      .then((res) => res.json())
      .then((data) => {
        if (data && data.city) {
          this.city = data.city.toUpperCase();
        }
      
    
        const lat = data.latitude;
        const lon = data.longitude;

        // 2. Fetch prayer times from Aladhan API
        fetch(`https://api.aladhan.com/v1/timings?latitude=${lat}&longitude=${lon}&method=20`)
          .then((res) => res.json())
          .then((prayerData) => {
            const t = prayerData.data.timings;
            const hijri= prayerData.data.date.hijri;
            this.hijriDate = `${arabicDay}, ${hijri.day} ${hijri.month.en} ${hijri.year} H`;
            this.prayers = [
              { name: "IMSAK", time: t.Imsak, icon: "https://img.icons8.com/ios/50/crescent-moon.png" },
              { name: "FAJR", time: t.Fajr, icon: "https://img.icons8.com/ios/50/sunrise.png" },
              { name: "DHUHR", time: t.Dhuhr, icon: "https://img.icons8.com/ios/50/sun--v1.png" },
              { name: "ASR", time: t.Asr, icon: "https://img.icons8.com/ios/50/sunrise--v2.png" },
              { name: "MAGHRIB", time: t.Maghrib, icon: "https://img.icons8.com/ios/50/sunset--v1.png" },
              { name: "ISHA", time: t.Isha, icon: "https://img.icons8.com/ios/50/bright-moon.png" },
            ];
        })
        .catch(() => {
            console.error("Failed to fetch prayer times");
          });
    })
    .catch(() => {
      this.city = "Unknown City";
    });
  },
};
</script>

<style scoped>
.main-container {
  display: flex;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  font-family: 'Segoe UI', sans-serif;
}

/* ---------- Left Panel ---------- */
.left-panel {
  width: 30%;
  min-width: 300px;
  background: linear-gradient(to bottom, #e6ffe6, #c3f5c3);
  padding: 30px 20px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

.logo {
  margin-bottom: 20px;
}

.mosque-logo {
  width: 60px;
  height: auto;
  filter: drop-shadow(1px 1px 2px #0a5f2e);
}

.title {
  font-size: 28px;
  font-weight: bold;
  color: #0a5f2e;
  margin-bottom: 30px;
}

.prayer-row {
  display: flex;
  align-items: center;
  background: linear-gradient(to right, #a0d468, #7cc242);
  padding: 12px 16px;
  border-radius: 10px;
  width: 100%;
  box-sizing: border-box;
  margin-bottom: 15px;
}

.icon {
  width: 24px;
  height: 24px;
  margin-right: 12px;
}

.prayer-text {
  color: #fff;
}

.prayer-text .name {
  font-weight: bold;
  font-size: 16px;
  line-height: 1.1;
}

.prayer-text .time {
  font-size: 14px;
  line-height: 1.1;
}

/* ---------- Right Panel ---------- */
.right-panel {
  flex: 1;
  background: #f9f9f9;
  position: relative;
  /* padding kept small; header/mihrabs are absolutely positioned */
  padding: 0;
}

/* Top-right city/date */
.header-info {
  position: absolute;
  top: 30px;
  right: 40px;
  text-align: right;
  z-index: 2;
}

.date-row {
  display: flex;
  flex-direction: column;
  font-size: 14px;
  color: #555;
  margin-top: 4px;
}

.date, .hijri {
  white-space: nowrap;
}

.city {
  font-size: 24px;
  font-weight: bold;
  color: #0a5f2e;
}

.date {
  font-size: 16px;
  color: #555;
  margin-top: 4px;
}

/* Bottom-right domes */
.mihrab-container {
  position: absolute;
  bottom: 30px;
  right: 40px;
  display: flex;
  gap: 30px;
  align-items: flex-end;
  z-index: 1;
}

.mihrab {
  border-radius: 50% 50% 0 0;
  background: linear-gradient(to top, #008000, #c0ffc0);
  border: 5px solid #fff;
}

.mihrab-center {
  height: 100px;
  width: 44px;
}

.mihrab-left {
  height: 80px;
  width: 36px;
}

.mihrab-right {
  height: 68px;
  width: 30px;
}
</style>

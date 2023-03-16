<!-- Верстка приложения -->
<template>
  <!-- Корневой узел Vue -->
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' && weather.main.temp >= 0
        ? 'warm'
        : 'cold'
    "
  >
    <!-- Узел для общей сетки приложения -->
    <main class="wrapper">
      <!-- Узел для секции поиска -->
      <section class="search-box">
        <!-- Блок инпута -->
        <input
          type="text"
          class="search-bar"
          placeholder="Enter city name..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </section>
      <!-- Узел для секции прогноза -->
      <section class="current" v-if="typeof weather.main != 'undefined'">
        <!-- Текстовый блок отбражения города -->
        <div class="city">
          <h3>{{ weather.name }}, {{ weather.sys.country }}</h3>
        </div>
        <!-- Текстовый блок отображения градусов -->
        <div class="temp">
          <h1>{{ Math.round(weather.main.temp) }}&deg;</h1>
        </div>
        <!-- Узел секции состояния осадков -->
        <div class="precipitation">
          <!-- Иконка состояния осадков -->
          <div class="precipitation-icon">
            <img v-bind:src="getImagePath()" alt="" />
            <!-- ./assets/icons/cloudy.png -->
          </div>
          <!-- Текстовый блок состояния осадков -->
          <div class="precipitation-state">
            <h4>{{ weather.weather[0].main }}</h4>
          </div>
        </div>
      </section>
      <!-- Узел секции приветствия -->
      <section class="greetings">
        <!-- Текстовый блок приветсвия -->
        <h2 class="welcome">Hello🖐</h2>
        <!-- Текстовый блок отображения дня недели -->
        <h2 class="day-of-the-week">
          Today is <span>{{ currentDate }}</span>
        </h2>
      </section>
    </main>
  </div>
</template>

<!-- Логика приложения -->
<script>
export default {
  // Инициализация приложения
  name: "app",
  // Данные
  data() {
    return {
      // API ключ
      api_key: "2ceb51754bbffba0622d8de7741f9a52",
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
    };
  },
  // Функции
  methods: {
    // Функция принимает значения из API и передает их в верстку через конкатенацию
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`
        )
          .then((res) => {
            return res.json();
          })
          .then(this.setResults);
      }
    },
    setResults(results) {
      this.weather = results;
    },
    // Функция принимает возвращаемое функцией fetchWeather API-значение и подставляется в директиву v-bind:src
    getImagePath() {
      const value = this.weather.weather[0].main;
      if (value === "Clouds") {
        return require("./assets/icons/cloudy.png");
      } else if (value === "Clear") {
        return require("./assets/icons/sunny.png");
      } else if (value === "Rain") {
        return require("./assets/icons/rainy.png");
      } else {
        return require("./assets/icons/snow.png");
      }
    },
  },
  // Вычисляемые значения
  computed: {
    // Функция перебирает массив и показывает актуальную дату
    currentDate() {
      const days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      const date = new Date();
      return days[date.getDay()] + ", " + date.toLocaleDateString();
    },
  },
};
</script>

<!-- Стилизация приложения -->
<style lang="scss">
// Сброс браузерных стилей
@import "@/assets/styles/reset/reset.css";

// Фон приложения
// Холодный
#app.cold {
  background: linear-gradient(0deg, #3561e8, #4a7ef2, #558df6);
  animation: ani 2.5s forwards;
}

// Теплый
#app.warm {
  background: linear-gradient(90deg, #f16205, #d98d00);
  animation: ani 2.5s forwards;
}
// Анимация плавной загрузки
@keyframes ani {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

// Импорт кастомных стилей
@import "@/assets/styles/main/main.css";
</style>

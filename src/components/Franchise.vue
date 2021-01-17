<template>
  <h3>Проверка доступности региона/города:</h3>

  <p>Страна</p>
  <select v-model="form.country" required>
    <option v-for="data in arrData">{{ data.name }}</option>
  </select>
  <br>
  <p>Регион</p>
  <select v-model="form.region">
    <option v-for="data in arrRegion">{{ data.name }}</option>
  </select>
  <br>
  <p>Город</p>
  <select v-model="form.city" required>
    <option v-for="data in arrCities">{{ data.name }}</option>
  </select>
  <br>
  <br>
  <span v-if="msg.spinner">{{ msg.spinner }}</span>
  <br>
  <button v-if:availability="!availability">Уведомить, если освободится</button>
  <button v-if:availability="availability">Отправить заявку</button>
</template>

<script>
import axios from "axios";

export default {
  name: 'Franchise',
  data() {
    return {
      form: {
        country: '',
        region: '',
        city: '',
      },
      msg: [],
      arrData: [],
      arrRegion: [],
      arrCities: [],
      availability: false
    }
  },
  beforeCreate() {
    const headers = {"Content-Type": "application/json"};
    axios.get(" https://api.hh.ru/areas", {headers})
        .then(response => {
          this.arrData = response.data;
        })

  },
  watch: {
    'form.country': function (value) {
      this.validRegion(value)
    },
    'form.region': function (value) {
      this.validCities(value)
    },
    'form.city': function (value) {
      this.checkCity(value)
    },
  },
  methods: {
    validRegion(value) {
      if (value) {
        this.arrData.forEach(item => {
          if (item.name === value) {
            this.arrRegion = item.areas
            if (item.areas[0].areas.length === 0) {
              this.arrCities = item.areas
              this.arrRegion = [];
            }
          }
        })
      }
    },
    validCities(value) {
      if (value) {
        this.arrRegion.forEach(item => {
          if (item.name === value) {
            this.arrCities = item.areas
          }
        })
      }
    },
    async checkCity(value) {
      this.msg['spinner'] = 'Загрузка...'
      await setTimeout(() => {
        if (!value) {
          this.msg['spinner'] = ''
          return this.availability = false
        }
        this.msg['spinner'] = ''
        return this.availability = (this.availability === false)
      }, 2000)
    }
  }
}

</script>


<style scoped>
button {
  margin-top: 15px;
}

span {
  margin-top: 10px;
}
</style>

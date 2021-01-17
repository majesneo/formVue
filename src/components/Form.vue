<template>
  <div id="form">
    <form>
      <label for="name">ФИО</label>
      <input id="name" type="text" v-model="form.name" required> <br>
      <span v-if="msg.name">{{ msg.name }}</span>
      <br>
      <label for="email">Email</label>
      <input id="email" type="text" v-model="form.email" required> <br>
      <span v-if="msg.email">{{ msg.email }}</span>
      <br>
      <label for="tel">Телефон</label>
      <input id="tel" type="text" v-model="form.tel" required> <br>
      <span v-if="msg.tel">{{ msg.tel }}</span>

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
      <button v-bind:disabled="isDisabled" type="submit">Отправить заявку</button>
    </form>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Form',
  data() {
    return {
      form: {
        name: '',
        email: '',
        country: '',
        region: '',
        city: '',
        tel: '',
      },
      msg: [],
      arrData: null,
      arrRegion: [],
      arrCities: [],
      isDisabled: true
    }
  },
  watch: {
    'form.email': function (value) {
      this.validateEmail(value);
    },
    'form.name': function (value) {
      this.validateName(value);
    },
    'form.country': function (value) {
      this.validRegion(value)
    },
    'form.region': function (value) {
      this.validCities(value)
    },
    'form.city': function (value) {
      this.validCities(value)
    },
    'form.tel': function (value) {
      this.validateTel(value)
    },
    form: {
      deep: true,
      handler() {
        this.validButton()
        console.log('The list of colours has changed!');
      },

    }
  },
  methods: {
    validateEmail(value) {
      if (!value) {
        this.msg['email'] = 'Введите Email'
      }
      if (/^([a-z0-9_\.-])+@[a-z0-9-]+\.([a-z]{2,4}\.)?[a-z]{2,4}$/i.test(value)) {
        this.msg['email'] = '';
      } else {
        this.msg['email'] = 'Неверный Email';
      }
    },
    validateName(value) {
      if (!value) {
        this.msg['name'] = 'Введите ФИО'
      } else if (/^([a-zA-ZА-Яа-яё ]{3,4})+$/gi.test(value)) {
        this.msg['name'] = '';
      } else {
        this.msg['name'] = 'Вводите ФИО через пробел,только буквы';
      }
    },
    validateTel(value) {
      if (!value) {
        this.msg['tel'] = 'Введите номер телефона'
      } else {
        if (!this.form.tel.match(/^\+[0-9]/g)) {
          this.msg['tel'] = 'Введите номер телефона формат +'
        } else {
          this.msg['tel'] = '';
        }
      }
    },
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
    validButton() {
      this.isDisabled = false;
      for (const formkey in this.form) {
        if (!this.form[formkey] && formkey !== 'region') {
          this.isDisabled = true;
        }
      }
    }
  },

  created() {
    const headers = {"Content-Type": "application/json"};
    axios.get(" https://api.hh.ru/areas", {headers})
        .then(response => {
          this.arrData = response.data;
        })
  }
}
</script>

<style scoped>
#form {
  margin: 20px auto;
  max-width: 700px;
  margin-bottom: 28px;
}

label {
  display: block;
  margin: 20px 0 10px;
}

span {
  padding-top: 0;
  margin-top: 0;
  font-size: 12px;
  color: red;
}

input {
  font-size: 30px;
  border: 1px double rgb(102, 97, 96);
  border-radius: 4px;
}

button {
  margin-top: 15px;
  height: 30px;
}

</style>
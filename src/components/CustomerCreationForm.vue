<template>
  <div class="container bg-light rounded pb-1" style="max-width: 600px">
    <h1 class="text-secondary">
      Добавить нового пациента
    </h1>

    <form 
    id="app"
    @submit.prevent="checkForm"
    >
      <div class="form-row">
        <div class="form-group col">
          <label for="surname">Фамилия*</label>
          <input type="text" v-model="surname" id="surname" name="surname" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.surname.$error }" placeholder="Иванов"/>
          <div v-if="isSubmitted && $v.surname.$error" class="invalid-feedback">
            <span v-if="!$v.surname.required">Поле обязательно для заполнения</span>
            <span v-if="!$v.surname.alpha">Поле должно содержать только русские буквы, пробелы и дефис</span>
          </div>
        </div>
        <div class="form-group col">
          <label for="name">Имя*</label>
          <input type="text" v-model="name" id="name" name="name" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.name.$error }" placeholder="Иван"/>
          <div v-if="isSubmitted && $v.name.$error" class="invalid-feedback">
            <span v-if="!$v.name.required">Поле обязательно для заполнения</span>
            <span v-if="!$v.name.alpha">Поле должно содержать только русские буквы, пробелы и дефис</span>
          </div>
        </div>   
        <div class="form-group col">
          <label for="patronymic">Отчество</label>
          <input type="text" v-model="patronymic" id="patronymic" name="patronymic" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.patronymic.$error }" placeholder="Иванович"/>
          <div v-if="isSubmitted && $v.patronymic.$error" class="invalid-feedback">
            <span v-if="!$v.patronymic.alpha">Поле должно содержать только русские буквы, пробелы и дефис</span>
          </div>
        </div>    
      </div>
      <div class="form-row">
        <div class="form-group col">
          <label for="date-of-birth">Дата рождения*</label>
          <input type="text" v-model="dateOfBirthRaw" @input="$v.dateOfBirth.$touch" id="date-of-birth" name="date-of-birth" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && ($v.dateOfBirth.$error || $v.dateOfBirthRaw.$error) }" placeholder="01.01.1980"/>
          <div v-if="isSubmitted && ($v.dateOfBirth.$error || $v.dateOfBirthRaw.$error)" class="invalid-feedback">
            <span v-if="!$v.dateOfBirthRaw.required">Поле обязательно для заполнения</span>
            <span v-else-if="!$v.dateOfBirthRaw.alpha">Введите дату в формате "dd.mm.yyyy"</span>
            <span v-else-if="!$v.dateOfBirth.validDate">Введите корректную дату</span>
            <span v-else-if="!$v.dateOfBirth.maxValue">Введите дату из прошлого</span>
          </div>
        </div>
        <div class="form-group col">
          <label for="mobile">Номер телефона*</label>
          <input type="text" v-model="mobile" id="mobile" name="mobile" @focus.once="addMobilePrefix" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.mobile.$error }" placeholder="+79999999999"/>
          <div v-if="isSubmitted && $v.mobile.$error" class="invalid-feedback">
            <span v-if="!$v.mobile.required">Поле обязательно для заполнения</span>
            <span v-else-if="!$v.mobile.alpha">Номер должен содержать 11 цифр и начинаться с "+7"</span>
          </div>
        </div>
        <div class="form-group col">
          <label for="gender">Пол</label>
          <div class="form-group">
            <div class="form-check form-check-inline">
              <input class="form-check-input" type="radio" name="gender" v-model="gender" id="gender1" value="male">
              <label class="form-check-label" for="gender1">Муж</label>
            </div>
            <div class="form-check form-check-inline">
              <input class="form-check-input" type="radio" name="gender" v-model="gender" id="gender2" value="female">
              <label class="form-check-label" for="gender2">Жен</label>
            </div>                   
          </div>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group col-6">
          <label for="customer-group">Группа клиентов*</label>
          <select
            multiple
            id="customer-group"
            v-model="customerGroups"
            name="customer-group"
            class="form-control form-control-sm"
            :class="{ 'is-invalid': isSubmitted && $v.customerGroups.$error }"
          >
            <option>VIP</option>
            <option>Проблемные</option>
            <option>ОМС</option>
          </select>
          <div v-if="isSubmitted && $v.customerGroups.$error" class="invalid-feedback">
            <span v-if="!$v.customerGroups.required">Поле обязательно для заполнения</span>
          </div>
        </div>
        <div class="form-col col-6">
          <div class="form-group col">
            <label for="doctor">Лечащий врач</label>
            <select
              id="doctor"
              v-model="doctor"
              name="doctor"
              class="form-control form-control-sm"
            >
              <option>Иванов</option>
              <option>Захаров</option>
              <option>Чернышева</option>
            </select>
          </div>

          <div class="form-group form-check col offset-1">
            <input type="checkbox" v-model="noMessage" id="no-message" class="form-check-input">
            <label class="form-check-label" for="no-message">Не отправлять СМС</label>
          </div>
        </div>
      </div>
      <p>
        Адрес:
      </p>
      <div class="form-row">
        <div class="form-group col">
          <label for="index">Индекс</label>
          <input type="text" v-model="index" id="index" name="index" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.index.$error }" placeholder="000000"/>
          <div v-if="isSubmitted && $v.index.$error" class="invalid-feedback">
            <span v-if="!$v.index.alpha">Индекс должен состоять из 6 цифр</span>
          </div>
        </div>
        <div class="form-group col">
          <label for="country">Страна</label>
          <input type="text" v-model="country" id="country" name="country" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.country.$error }" placeholder="Россия"/>
          <div v-if="isSubmitted && $v.country.$error" class="invalid-feedback">
            <span v-if="!$v.country.alpha">Поле должно содержать только русские буквы, пробелы и дефис</span>
          </div>
        </div>
        <div class="form-group col-5">
          <label for="region">Область</label>
          <input type="text" v-model="region" id="region" name="region" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.region.$error }" placeholder="Московская область"/>
          <div v-if="isSubmitted && $v.region.$error" class="invalid-feedback">
            <span v-if="!$v.region.alpha">Поле должно содержать только русские буквы, пробелы и дефис</span>
          </div>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group col-4">
          <label for="city">Город*</label>
          <input type="text" v-model="city" id="city" name="city" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.city.$error }" placeholder="Москва"/>
          <div v-if="isSubmitted && $v.city.$error" class="invalid-feedback">
            <span v-if="!$v.city.required">Поле обязательно для заполнения</span>
            <span v-if="!$v.city.alpha">Поле должно содержать только русские буквы, пробелы и дефис</span>
          </div>
        </div>
        <div class="form-group col-6">
          <label for="street">Улица</label>
          <input type="text" v-model="street" id="street" name="street" class="form-control form-control-sm" placeholder="Гагарина"/>
        </div>
        <div class="form-group col">
          <label for="house">Дом</label>
          <input type="text" v-model="house" id="house" name="house" class="form-control form-control-sm" placeholder="1"/>
        </div>
      </div>
      <p>
        Паспорт:
      </p>
      <div class="form-row">
        <div class="form-group col-6">
          <label for="document">Тип документа*</label>
          <select
            id="document"
            v-model="document"
            name="document"
            class="form-control form-control-sm"
            :class="{ 'is-invalid': isSubmitted && $v.document.$error }"
          >
            <option>Паспорт</option>
            <option>Свидетельство о рождении</option>
            <option>Вод. удостоверение</option>
          </select>
          <div v-if="isSubmitted && $v.document.$error" class="invalid-feedback">
            <span v-if="!$v.document.required">Поле обязательно для заполнения</span>
          </div>
        </div>
        <div class="form-group col">
          <label for="series">Серия</label>
          <input type="text" v-model="series" id="series" name="series" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.series.$error }"  placeholder="0000"/>
          <div v-if="isSubmitted && $v.series.$error" class="invalid-feedback">
            <span v-if="!$v.series.alpha">Серия должна состоять из 4 цифр</span>
          </div>
        </div>
        <div class="form-group col">
          <label for="number">Номер</label>
          <input type="text" v-model="number" id="number" name="number" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && $v.number.$error }"  placeholder="000000"/>
          <div v-if="isSubmitted && $v.number.$error" class="invalid-feedback">
            <span v-if="!$v.number.alpha">Номер должен состоять из 6 цифр</span>
          </div>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group col-9">
          <label for="issued-by">Кем выдан</label>
          <input type="text" v-model="issuedBy" id="issued-by" name="issued-by" class="form-control form-control-sm" placeholder="УФМС гор. Москва"/>
        </div>
        <div class="form-group col">
          <label for="date-of-issue">Дата выдачи*</label>
          <input type="text" v-model="dateOfIssueRaw" id="date-of-issue" name="date-of-issue" class="form-control form-control-sm" :class="{ 'is-invalid': isSubmitted && ($v.dateOfIssueRaw.$error || $v.dateOfIssue.$error)}" placeholder="01.01.1980"/>
          <div v-if="isSubmitted && ($v.dateOfIssueRaw.$error || $v.dateOfIssue.$error)" class="invalid-feedback">
            <span v-if="!$v.dateOfIssueRaw.required">Поле обязательно для заполнения</span>
            <span v-else-if="!$v.dateOfIssueRaw.alpha">Введите дату в формате "dd.mm.yyyy"</span>
            <span v-else-if="!$v.dateOfIssue.validDate">Введите корректную дату</span>
            <span v-else-if="!$v.dateOfIssue.maxValue">Введите дату из прошлого</span>
          </div>
        </div>
      </div>
      <p>
        * - Поле обязательно для заполнения
      </p>

      <div class="form-group">
        <button class="btn btn-secondary btn-block">Добавить пациента</button>
      </div>
    </form>
  </div>
</template>

<script>
  import {
    required, 
    maxValue
  } from "vuelidate/lib/validators";
  var userForm = {
      surname: '',
      name: '',
      patronymic: '',
      dateOfBirthRaw: '',
      mobile: '',
      gender: '',
      customerGroups: [],
      doctor: '',
      noMessage: false,
      index: '',
      country: '',
      region: '',
      city: '',
      street: '',
      house: '',
      document: '',
      series: '',
      number: '',
      issuedBy: '',
      dateOfIssueRaw: '',
      isSubmitted: false
    }
export default {
  name: 'HelloWorld',
  data() {
      return userForm
  },
  validations: {
    surname: {
      required,
      alpha: val => /^[а-яё\s-]*$/i.test(val),
    },
    name: {
      required,
      alpha: val => /^[а-яё\s-]*$/i.test(val),
    },
    patronymic: {
      alpha: val => /^[а-яё\s-]*$/i.test(val),
    },
    dateOfBirthRaw: {
      required,
      alpha: val => /^\d\d\.\d\d\.\d\d\d\d$/.test(val),
    },
    dateOfBirth: {
      validDate: val => val,
      maxValue: maxValue(new Date()),
    },
    mobile: {
      required,
      alpha: val => /^\+7\d{10}$/.test(val),
    },
    customerGroups: {
      required,
    },
    index: {
      alpha: val => /^\d{6}$|^$/.test(val),
    },
    country: {
      alpha: val => /^[а-яё\s-]*$/i.test(val),
    },
    region: {
      alpha: val => /^[а-яё\s-]*$/i.test(val),
    },
    city: {
      required,
      alpha: val => /^[а-яё\s-]*$/i.test(val),
    },
    document: {
      required,
    },
    series: {
      alpha: function (value) {
        if (this.document!=='Свидетельство о рождении') {
          return /^\d{4}$|^$/.test(value)
        } else {
          return true
        }
      },
    },
    number: {
      alpha: val => /^\d{6}$|^$/.test(val),
    },
    dateOfIssueRaw: {
      required,
      alpha: val => /^\d\d\.\d\d\.\d\d\d\d$/.test(val),
    },
    dateOfIssue: {
      validDate: val => val,
      maxValue: maxValue(new Date()),
    },
  },
  computed: {
    dateOfBirth () {
      return this.stringToDateObject(this.dateOfBirthRaw)
    },
    dateOfIssue () {
      return this.stringToDateObject(this.dateOfIssueRaw)
    },
  },
  methods: {
    addMobilePrefix: function () {
      return this.mobile = '+7'
    },
    stringToDateObject: function (str) {
      if (str) {
        var arrD = str.split(".");
        arrD[1] -= 1;
        var d = new Date(arrD[2], arrD[1], arrD[0]);
        if ((d.getFullYear() == arrD[2]) && (d.getMonth() == arrD[1]) && (d.getDate() == arrD[0])) {
          return d;
        } else return false       
      } else return ''
    },
    checkForm: function () {
      this.isSubmitted = true;
      this.$v.$touch();
      if (this.$v.$invalid) {
        return
      } else {alert('Данные отправлены: '+JSON.stringify(userForm))}            
    },
    
  }
}
</script>
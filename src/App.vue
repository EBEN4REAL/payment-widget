<template>
  <div class="payment-app mb-5">
    <h5 class="text-center py-4">Paymentwall Widget</h5>
    <form id="form">
      <div class="header-info">
        <div class="step-label">
          <span>1</span>
        </div>
        <div>
          <h6 class="ml-3 mt-2">Enter amount</h6>
        </div>
      </div>
      <div class="field-wrapper position-relative mt-3">
        <input type="text"  class="default-input " placeholder="Enter amount" value="0" @input="validateAmount" />
        <span class="span-right-text">
          <span>
            {{selectedCountry ? selectedCountry.currencies[0].code : countryInfo.code}}
          </span>
        </span>
      </div>
      <div class="header-info mt-3">
        <div class="step-label">
          <span>2</span>
        </div>
        <div>
          <h6 class="ml-3 mt-2">Select country</h6>
        </div>
      </div>
      <div class="field-wrapper position-relative mt-4">
        <img :src="selectedCountry.flag" width="25" v-if="selectedCountry" class="country-flag" />
        <select class="default-input" v-model="countryInfo.code" @change="selectCountry">
          <option :value="country.alpha2Code" v-for="country in countries" :key="country.id">{{country.name}}</option>
        </select>
      </div>
      <div class="header-info mt-3">
        <div class="step-label">
          <span>3</span>
        </div>
        <div>
          <h6 class="ml-3 mt-2">Choose Payment Method</h6>
        </div>
      </div>
      <h6 class="mt-4">Instant payment methods</h6>
      <div class="payment-methods-wrapper mt-4">
        <div class="" v-for="paymentMethod in paymentMethods" :key="paymentMethod.id">
          <div class="payment-method-block ">
            <img :src="paymentMethod.img_url" />
          </div>
          <div class="text-center mt-2 p-method-name">{{paymentMethod.name}}</div>
        </div>
      </div>
      <h6 class="mt-4">Non-Instant payment methods</h6>
      <div class="payment-methods-wrapper mt-4">
        <div class="">
          <div class="payment-method-block p-4">
            <img src="@/assets/img/BankTransfer.png" />
          </div>
          <div class="text-center mt-2 ">Transfer from bank <br>account</div>
        </div>
      </div>
      <div class="header-info mt-3">
        <div class="step-label">
          <span>4</span>
        </div>
        <div>
          <h6 class="ml-3 mt-2">Card Details</h6>
        </div>
      </div>
      <div class="field-wrapper mt-4 position-relative">
        <input type="text" placeholder="Full name" class="default-input" @keypress=" validateCharacters"/>
        <span class="error-message"></span>
        <span class="error-icon">
          <img src="@/assets/img/error.png" width="20" />
        </span>
      </div>
      <div class="field-wrapper mt-4 position-relative">
        <input type="text" placeholder="Card number" id="card-number" class="default-input" @input="validateCardNumber" />
        <span class="error-message" id="card-error-message"></span>
        <span class="error-icon" id="card-error-icon">
          <img src="@/assets/img/error.png" width="20" />
        </span>
        <span class="span-right">
          <img src="@/assets/img/card.png" class="card-img" width="30" />
        </span>
      </div>
      <div class="field-wrapper mt-4 position-relative">
        <div class="exp-wrapper default-input" id="expiry-field">
          <input autocomplete="off" class="exp" id="month" maxlength="2" pattern="[0-9]*" inputmode="numerical" placeholder="MM" type="text" data-pattern-validate @input="validateExpiryDate"  /> 
          <input autocomplete="off" class="exp" id="year" maxlength="2" pattern="[0-9]*" inputmode="numerical" placeholder="YY" type="text" data-pattern-validate @input="validateExpiryDate" />
        </div>
        <span class="error-message" id="expiry"></span>
        <span class="error-icon">
          <img src="@/assets/img/error.png" width="20" />
        </span>
        <span class="error-icon">
          <img src="@/assets/img/error.png" width="20" />
        </span>
      </div>
      <div class="field-wrapper mt-4 position-relative">
        <input type="text" placeholder="CVV" class="default-input"  @input="validateCvv"/>
        <span class="error-message"></span>
        <span class="error-icon">
          <img src="@/assets/img/error.png" width="20" />
        </span>
      </div>
      <div class="mt-4">
        <button class="default-button w-100" type="submit" 
          @click="pay" 
          :disabled="!formIsValid"
          :class="[!formIsValid ? 'disabled' : null]"
          :style="[
            !formIsValid
              ? { cursor: 'not-allowed' }
              : { cursor: 'pointer' }
          ]">Pay {{convertThousand(parseFloat(amount))}}  {{selectedCountry ? selectedCountry.currencies[0].code : countryInfo.code}} </button>
      </div>
    </form>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {

  },
 
  data() {
    return {
      clientIP: null,
      countryInfo: {},
      paymentMethods: [],
      countries: [],
      selectedCountry: null,
      countryCode: null,
      name: '',
      amount: '',
      formIsValid: false,
      validators: {
        amount: false,
        name: false,
        cardNumber: false,
        expiryDate: false,
        cvv: false
      }
    }
  },
  watch: {
    validators: {
      handler: function(validators) {
        for(let val in validators) {
          if(!validators[val]) {
            this.formIsValid = false
          }else {
            this.formIsValid = true
          }
        }
        console.log(this.formIsValid)
      },
      deep: true
    },
    clientIP() {
      this.getCountry()
    },
    countryCode() {
      this.requestPaymentMethod()
    },
    countryInfo() {
      this.getAllCountries()
    },
    countries(countries) {
      const allCountries = [...countries]
      const filteredCountry = allCountries.filter(country => country.alpha2Code == this.countryInfo.code)[0]
      this.selectedCountry = filteredCountry
    },
    
  },
  mounted() {
    this.getClientIP()
    const monthInput = document.querySelector('#month');
    const yearInput = document.querySelector('#year');

    const focusSibling = (target, direction, callback) => {
      const nextTarget = target[direction];
      nextTarget && nextTarget.focus();
      callback && callback(nextTarget);
    }

    monthInput.addEventListener('input', (event) => {

      const value = event.target.value.toString();
      if (value.length === 1 && value > 1) {
          event.target.value = "0" + value;
      }
      if (value === "00") {
          event.target.value = "01";
      } else if (value > 12) {
          event.target.value = "12";
      }
      2 <= event.target.value.length && focusSibling(event.target, "nextElementSibling");
      event.stopImmediatePropagation();
    });

    yearInput.addEventListener('keydown', (event) => {
      if (event.key === "Backspace" && event.target.selectionStart === 0) {
        focusSibling(event.target, "previousElementSibling");
        event.stopImmediatePropagation();
      }
    });

    const inputMatchesPattern = function(e) {
      const { 
        value, 
        selectionStart, 
        selectionEnd, 
        pattern 
      } = e.target;
      
      const character = String.fromCharCode(e.which);
      const proposedEntry = value.slice(0, selectionStart) + character + value.slice(selectionEnd);
      const match = proposedEntry.match(pattern);
      
      return e.metaKey || 
        e.which <= 0 ||
        e.which == 8 || 
        match && match["0"] === match.input; 
    };

    document.querySelectorAll('input[data-pattern-validate]').forEach(el => el.addEventListener('keypress', e => {
      if (!inputMatchesPattern(e)) {
        return e.preventDefault();
      }
    }));

    document.getElementById('card-number').addEventListener('input' , (e) => {
      let invalidChars = /[^0-9]/gi
      e.target.value.replace(/\W/gi, '').replace(/(.{4})/g, '$1 ')

      if(invalidChars.test(e.target.value)) {
        e.target.value = e.target.value.replace(invalidChars,"");
        return
      }
      this.validateCardNumber(e.target.value)
    })
    
  },
  methods: {
    generateuniqueId() {
      return Math.floor(Math.random() * Math.floor(Math.random() * Date.now()))
    },

    getCountry() {
      const uid = this.generateuniqueId()
      fetch(`https://api.paymentwall.com/api/rest/country?key=e8310e204978b629afa4dd0483740bf0&uid=${uid}&user_ip=${this.clientIP}`)
        .then(response => response.json())
        .then(data => {
          this.countryCode = data.code
          this.countryInfo = data
        });
    },

    getClientIP() {
      fetch(`https://api.bigdatacloud.net/data/ip-geolocation-full?key=d9e53816d07345139c58d0ea733e3870`)
        .then(response => response.json())
        .then(data => {
          this.clientIP = data.ip
        });
    },

    getAllCountries() {
      fetch(`https://restcountries.eu/rest/v2/all`)
        .then(response => response.json())
        .then(data => {
          this.countries = data
        });
    },

    requestPaymentMethod() {
      fetch(`https://api.paymentwall.com/api/payment-systems/?key=e8310e204978b629afa4dd0483740bf0&country_code=${this.countryCode}`)
        .then(response => response.json())
        .then(data => {
          this.paymentMethods = data
        });
    },
    selectCountry(e) {
      const countries = [...this.countries]
      const filteredCountry = countries.filter(country => country.alpha2Code == e.target.value)[0]
      this.countryCode = filteredCountry.currencies[0].code.length > 2 
        ? filteredCountry.alpha2Code 
        : filteredCountry.currencies[0].code
      this.selectedCountry = filteredCountry
    },
    validateCharacters(e) {
      const errorMessage = e.target.nextSibling
      let char = String.fromCharCode(e.keyCode)
      if(/^[A-Za-z]+$/.test(char)) {
        errorMessage.style.display = 'none'
        errorMessage.nextSibling.style.display = 'none'
        e.target.classList.remove('error')
        this.validators.name = true
      } else {
        e.preventDefault()
        errorMessage.style.display = 'inline'
        errorMessage.innerHTML = 'Please input letters'
        errorMessage.style.color = '#FF0000'
        errorMessage.nextSibling.style.display = 'inline'
        e.target.classList.add('error')
        this.validators.name = false
      }
    },
    validateExpiryDate() {
      const errorMessage = document.getElementById('expiry')
      const expField = document.getElementById('expiry-field')
      let today, someday;
      const monthInput = document.querySelector('#month').value;
      let yearInput = document.querySelector('#year').value;
      yearInput = 20 + yearInput
      today = new Date();
      someday = new Date();
      someday.setFullYear(yearInput, monthInput - 1);

      if (someday < today) {
        errorMessage.style.display = 'inline'
        errorMessage.innerHTML = 'Invalid expiration date'
        errorMessage.style.color = '#FF0000'
        errorMessage.nextSibling.style.display = 'inline'
        expField.classList.add('error')
        this.validators.expiryDate = false
      }else {
        errorMessage.style.display = 'none'
        errorMessage.nextSibling.style.display = 'none'
        expField.classList.remove('error')
        this.validators.expiryDate = true
      }
    },

    validateAmount(e){
      this.amount = e.target.value
      var invalidChars = /[^0-9]/gi
      if(invalidChars.test(e.target.value)) {
        e.target.value = e.target.value.replace(invalidChars,"");
        this.validators.amount = false
      }else {
        this.validators.amount = true
      }
      
    },

    validateCvv(e) {
      let invalidChars = /[^0-9]/gi
      if(invalidChars.test(e.target.value)) {
        e.target.value = e.target.value.replace(invalidChars,"");
        this.validators.cvv = false
      }else {
        this.validators.cvv = true
      }
      
    },

    validateCardNumber(cardNo) {
      const errorMessage = document.getElementById('card-error-message')
      const cardField = document.getElementById('card-number')

      var s = 0;
      var doubleDigit = false;

      for (let i = cardNo.length - 1; i >= 0; i--) {
          let digit = +cardNo[i];

          if (doubleDigit) {
              digit *= 2;
              if (digit > 9)
                  digit -= 9;
          }

          s += digit;
          doubleDigit = !doubleDigit;
      }

      if(s % 10 == 0) {
        errorMessage.style.display = 'none'
        errorMessage.nextSibling.style.display = 'none'
        cardField.classList.remove('error')
        this.validators.cardNumber = true
      }else {
        errorMessage.style.display = 'inline'
        errorMessage.innerHTML = 'Invalid card number'
        errorMessage.style.color = '#FF0000'
        errorMessage.nextSibling.style.display = 'inline'
        cardField.classList.add('error')
        this.validators.cardNumber = false
      }
    },
    pay(e){
      e.preventDefault()
    },
    convertThousand(request) {
      if (!isFinite(request)) {
        return "0.00";
      }
      return request
        .toFixed(2)
        .toString()
        .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  }
}
</script>

<style>
  @import "./assets/sass/styles.scss";
</style>

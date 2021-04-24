<template>
  <div class="payment-app mb-5">
    <h5 class="text-center py-4">Enter Payment Information</h5>
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
        <input type="text"  class="default-input " placeholder="Enter amount"/>
        <!-- <span class="floating-label">
          Enter amount
        </span> -->
        <span class="span-right-text">
          <span>EUR</span>
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
        <select class="default-input">
          <option>Country</option>
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
        <div class="">
          <div class="payment-method-block p-4">
            <img src="@/assets/img/image21.png" />
          </div>
          <div class="text-center mt-2">Ideal</div>
        </div>
        <div class="">
          <div class="payment-method-block p-4">
            <img src="@/assets/img/pay2.png" />
          </div>
          <div class="text-center mt-2">Giropay</div>
        </div>
        <div class="">
          <div class="payment-method-block p-4">
            <img src="@/assets/img/pay4.png" />
          </div>
          <div class="text-center mt-2">WebMoney</div>
        </div>
        <div class="">
          <div class="payment-method-block p-4">
            <img src="@/assets/img/pay3.png" />
          </div>
          <div class="text-center mt-2">Przelewy24</div>
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
        <input type="text" placeholder="Card number" class="default-input"/>
        <span class="span-right">
          <img src="@/assets/img/CC.png" />
        </span>
      </div>
      <div class="mt-4">
        <input type="text" placeholder="Expiration date" class="default-input"/>
      </div>
      <div class="mt-4">
        <input type="number" placeholder="CVV" class="default-input"/>
      </div>
      <div class="mt-4">
        <button class="default-button w-100" type="submit">Submit </button>
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
      clientIP: null
    }
  },
  watch: {
    clientIP() {
      this.getCountry()
    },
    countryInfo() {
      this.requestPaymentMethod()
    }
  },
  mounted() {
    this.getClientIP()
  },
  methods: {
    generateuniqueId() {
      return Math.floor(Math.random() * Math.floor(Math.random() * Date.now()))
    },
    async getCountry() {
      const uid = this.generateuniqueId()
      await fetch(`https://api.paymentwall.com/api/rest/country?key=e8310e204978b629afa4dd0483740bf0&uid=${uid}&user_ip=${this.clientIP}`)
        .then(response => response.json())
          .then(data => {
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
    requestPaymentMethod() {
      fetch(`https://api.bigdatacloud.net/data/ip-geolocation-full?key=d9e53816d07345139c58d0ea733e3870`)
        .then(response => response.json())
          .then(data => {
            this.clientIP = data.ip
          });
    }
  }
}
</script>

<style>
  @import "./assets/sass/styles.scss";
</style>

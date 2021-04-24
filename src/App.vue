<template>
  <div class="payment-app">
    <h5 class="text-center py-4">Enter Payment Information</h5>
    <form id="form">
      <div class="field-wrapper position-relative">
        <input type="text" placeholder="Email or phone number" class="default-input"/>
        <img src="@/assets/img/email.png" width="25" />
      </div>
      <div class="mt-4">
        <input type="text" placeholder="Card number" class="default-input"/>
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

  }
}
</script>

<style>
  @import "./assets/sass/styles.scss";
</style>

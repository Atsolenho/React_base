<template>
  <div>
    <SfHeading
      title="Shipping"
      :level="3"
      class="sf-heading--left sf-heading--no-underline title"
    />
    <SpDropdown
      :consignments="old_consignments"
      @click:setAddress="handleSetAddress"
    ></SpDropdown>
    <div class="form">
      <SfInput
        v-model="$v.shippingInfo.first_name.$model"
        :value="shippingInfo.first_name"
        label="First name"
        name="first_name"
        class="form__element form__element--half"
        :valid="checkFormValidation($v.shippingInfo.first_name)"
        :error-message="'First name is required'"
      />
      <SfInput
        v-model="$v.shippingInfo.last_name.$model"
        :value="shippingInfo.last_name"
        label="Last name"
        name="last_name"
        class="form__element form__element--half form__element--half-even"
        :valid="checkFormValidation($v.shippingInfo.last_name)"
        :error-message="'Last name is required'"
      />
      <SfInput
        v-model="$v.shippingInfo.email.$model"
        :value="shippingInfo.email"
        label="Email Address"
        name="email"
        class="form__element form__element--half"
        :valid="checkFormValidation($v.shippingInfo.email)"
        :error-message="'Email is invalid'"
      />
      <SfInput
        v-model="$v.shippingInfo.address1.$model"
        :value="shippingInfo.address1"
        label="Address 1"
        name="address1"
        class="form__element form__element--half form__element--half-even"
        :valid="checkFormValidation($v.shippingInfo.address1)"
        :error-message="'Address 1 is required'"
      />
      <SfInput
        v-model="$v.shippingInfo.address2.$model"
        :value="shippingInfo.address2"
        label="Address 2"
        name="address2"
        class="form__element form__element--half"
        :valid="checkFormValidation($v.shippingInfo.address2)"
        :error-message="'Address 2 is required'"
      />
      <SfInput
        v-model="$v.shippingInfo.city.$model"
        :value="shippingInfo.city"
        label="City"
        name="city"
        class="form__element form__element--half form__element--half-even"
        :valid="checkFormValidation($v.shippingInfo.city)"
        :error-message="'City is required'"
      />
      <SfInput
        v-model="$v.shippingInfo.postal_code.$model"
        :value="shippingInfo.postal_code"
        label="Postal-code"
        name="postal_code"
        class="form__element form__element--half"
        :valid="checkFormValidation($v.shippingInfo.postal_code)"
        :error-message="'Postal code is required'"
      />
      <SfInput
        v-model="$v.shippingInfo.country_code.$model"
        :value="shippingInfo.country_code"
        label="Country code"
        name="country_code"
        class="form__element form__element--half form__element--half-even"
        :valid="checkFormValidation($v.shippingInfo.country_code)"
        :error-message="'Country code is required'"
      />
      <country-select
        v-model="$v.shippingInfo.country.$model"
        :country-name="true"
        :country="shippingInfo.country"
        top-country="US"
        class="country_select"
      />
      <span
        v-if="!checkFormValidation($v.shippingInfo.country)"
        style="margin-top: -20px; color: red; font-size: 14px"
        >Country is required</span
      >
      <region-select
        v-model="$v.shippingInfo.state_or_province.$model"
        :country="shippingInfo.country"
        :region="shippingInfo.state_or_province"
        :country-name="true"
        :region-name="true"
        class="region_select"
      />
      <span
        v-if="!checkFormValidation($v.shippingInfo.state_or_province)"
        style="margin-top: -20px; color: red; font-size: 14px"
        >State is required</span
      >
      <SfInput
        v-model="$v.shippingInfo.phone.$model"
        :value="shippingInfo.phone"
        label="Phone number"
        name="phone"
        class="form__element"
        :valid="checkFormValidation($v.shippingInfo.phone)"
        :error-message="'Phone is required'"
      />
    </div>
  </div>
</template>
<script>
import { SfHeading, SfInput } from '@storefront-ui/vue';
import { mapGetters } from 'vuex';
// import SpDropdown from '~/components/checkout/basic/SpDropdown.vue';
import { required } from 'vuelidate/lib/validators';
import { checkFormValidation } from '~/utils/validation';
export default {
  name: 'Shipping',
  components: {
    SfHeading,
    SfInput,
    SpDropdown
  },
  data() {
    return {
      shippingInfo: {
        first_name: '',
        last_name: '',
        email: '',
        address1: '',
        address2: '',
        city: '',
        state_or_province: '',
        country: '',
        country_code: '',
        postal_code: '',
        phone: ''
      },
      test: {
        first_name: 'First',
        last_name: 'Last',
        email: 'test@test.com',
        address1: 'Test Address 1',
        address2: 'Test Address 2',
        city: 'Burnaby',
        state_or_province: 'Ontario',
        country: 'Canada',
        country_code: 'CA',
        postal_code: '123123',
        phone: '123123123123'
      },
      consignmentId: null,
      consignment: null
    };
  },
  validations: {
    shippingInfo: {
      first_name: {
        required
      },
      last_name: {
        required
      },
      email: {
        required
      },
      address1: {
        required
      },
      address2: {
        required
      },
      city: {
        required
      },
      state_or_province: {
        required
      },
      country: {
        required
      },
      country_code: {
        required
      },
      postal_code: {
        required
      },
      phone: {
        required
      }
    }
  },
  computed: {
    ...mapGetters('checkout', [
      'old_consignments',
      'line_items',
      'shippingAddress'
    ])
  },
  mounted() {
    if (this.shippingAddress) {
      this.shippingInfo = { ...this.shippingAddress };
    }
    if (this.old_consignments.length) {
      const consignment = this.old_consignments[0];
      this.consignment = consignment;
      this.shippingInfo = { ...consignment.shipping_address };
      this.consignmentId = consignment.id;
    }
  },
  destroyed() {
    this.$store.commit('checkout/SET_SHIPPING_ADDRESS', this.shippingInfo);
  },
  methods: {
    checkFormValidation,
    handleSetAddress(action) {
      if (action) {
        Object.keys(this.shippingInfo).map(
          (key) => (this.shippingInfo[key] = action?.shipping_address[key])
        );
        this.consignmentId = this.consignment?.id;
      } else {
        Object.keys(this.shippingInfo).map(
          (key) => (this.shippingInfo[key] = this.test[key])
        );
        this.consignmentId = null;
      }
    },
    runAction() {
      if (!this.$v.$invalid) {
        if (!this.consignmentId)
          this.$store.dispatch('checkout/setConsignmentToCheckout', {
            shipping_address: this.shippingInfo
          });
        else
          this.$store.dispatch('checkout/updateConsignmentToCheckout', {
            shipping_address: this.shippingInfo,
            consignmentId: this.consignmentId
          });
      }
      return !this.$v.$invalid;
    }
  }
};
</script>
<style
  src="~/assets/sass/components/checkout/shipping.scss"
  lang="scss"
  scoped
></style>

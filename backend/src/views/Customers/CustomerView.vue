<template>
  <!-- Customer form -->
  <div v-if="customer.id" class="animate-fade-in-down">
    <!-- Form submission -->
    <form @submit.prevent="onSubmit">
      <!-- Form fields -->
      <div class="bg-white px-4 pt-5 pb-4">
        <!-- Title -->
        <h1 class="text-2xl font-semibold pb-2">{{ title }}</h1>
        <!-- First Name Input -->
        <CustomInput class="mb-2" v-model="customer.first_name" label="First Name" :errors="errors.first_name"/>
        <!-- Last Name Input -->
        <CustomInput class="mb-2" v-model="customer.last_name" label="Last Name" :errors="errors.last_name"/>
        <!-- Email Input -->
        <CustomInput class="mb-2" v-model="customer.email" label="Email" :errors="errors.email"/>
        <!-- Phone Input -->
        <CustomInput class="mb-2" v-model="customer.phone" label="Phone" :errors="errors.phone"/>
        <!-- Status Checkbox -->
        <CustomInput type="checkbox" class="mb-2" v-model="customer.status" label="Active" :errors="errors.status"/>

        <!-- Billing Address -->
        <div>
          <!-- Billing Address Title -->
          <h2 class="text-xl font-semibold mt-6 pb-2 border-b border-gray-300">請求先住所</h2>

          <!-- Billing Address Fields -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-2">
            <!-- Address 1 Input -->
            <CustomInput v-model="customer.billingAddress.address1" label="Address 1" :errors="errors['billingAddress.address1']"/>
            <!-- Address 2 Input -->
            <CustomInput v-model="customer.billingAddress.address2" label="Address 2" :errors="errors['billingAddress.address2']"/>
            <!-- City Input -->
            <CustomInput v-model="customer.billingAddress.city" label="City" :errors="errors['billingAddress.city']"/>
            <!-- Zip Code Input -->
            <CustomInput v-model="customer.billingAddress.zipcode" label="Zip Code" :errors="errors['billingAddress.zipcode']"/>
            <!-- Country Select -->
            <CustomInput type="select" :select-options="countries" v-model="customer.billingAddress.country_code" label="Country" :errors="errors['billingAddress.country_code']"/>
            <!-- State Select -->
            <CustomInput v-if="billingCountry && !billingCountry.states" v-model="customer.billingAddress.state" label="State" :errors="errors['billingAddress.state']"/>
            <CustomInput v-else type="select" :select-options="billingStateOptions" v-model="customer.billingAddress.state" label="State" :errors="errors['billingAddress.state']"/>
          </div>
        </div>

        <!-- Shipping Address -->
        <div>
          <!-- Shipping Address Title -->
          <h2 class="text-xl font-semibold mt-6 pb-2 border-b border-gray-300">配送先住所</h2>

          <!-- Shipping Address Fields -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-2">
            <!-- Address 1 Input -->
            <CustomInput v-model="customer.shippingAddress.address1" label="Address 1" :errors="errors['shippingAddress.address1']"/>
            <!-- Address 2 Input -->
            <CustomInput v-model="customer.shippingAddress.address2" label="Address 2" :errors="errors['shippingAddress.address2']"/>
            <!-- City Input -->
            <CustomInput v-model="customer.shippingAddress.city" label="City" :errors="errors['shippingAddress.city']"/>
            <!-- Zip Code Input -->
            <CustomInput v-model="customer.shippingAddress.zipcode" label="Zip Code" :errors="errors['shippingAddress.zipcode']"/>
            <!-- Country Select -->
            <CustomInput type="select" :select-options="countries" v-model="customer.shippingAddress.country_code" label="Country" :errors="errors['shippingAddress.country_code']"/>
            <!-- State Select -->
            <CustomInput v-if="shippingCountry && !shippingCountry.states" v-model="customer.shippingAddress.state" label="State" :errors="errors['shippingAddress.state']"/>
            <CustomInput v-else type="select" :select-options="shippingStateOptions" v-model="customer.shippingAddress.state" label="State" :errors="errors['shippingAddress.state']"/>
          </div>
        </div>

      </div>

      <!-- Form submission buttons -->
      <footer class="bg-gray-50 px-4 py-3 sm:px-6 sm:flex sm:flex-row-reverse">
        <!-- Submit button -->
        <button type="submit"
                class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 focus:outline-none focus:ring-2 focus:ring-offset-2 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm
                          text-white bg-indigo-600 hover:bg-indigo-700 focus:ring-indigo-500">
          Submit
        </button>
        <!-- Cancel button -->
        <router-link :to="{name: 'app.customers'}" type="button"
                     class="mt-3 w-full inline-flex justify-center rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-base font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 sm:mt-0 sm:ml-3 sm:w-auto sm:text-sm"
                     ref="cancelButtonRef">
          Cancel
        </router-link>
      </footer>
    </form>
  </div>
</template>

<script setup>
import { computed, onMounted, ref } from "vue";
import store from "../../store";
import { useRoute, useRouter } from "vue-router";
import CustomInput from "../../components/core/CustomInput.vue";

const router = useRouter();
const route = useRoute();

// Title of the form
const title = ref('');
// Error messages for form fields
const errors = ref({
  first_name: [],
  last_name: [],
  email: [],
  phone: [],
  status: [],
  'billingAddress.address1': [],
  'billingAddress.address2': [],
  'billingAddress.city': [],
  'billingAddress.zipcode': [],
  'billingAddress.country_code': [],
  'billingAddress.state': [],
  'shippingAddress.address1': [],
  'shippingAddress.address2': [],
  'shippingAddress.city': [],
  'shippingAddress.zipcode': [],
  'shippingAddress.country_code': [],
  'shippingAddress.state': [],
});
// Customer data
const customer = ref({
  billingAddress: {},
  shippingAddress: {}
});
// Loading state for form submission
const loading = ref(false);

// Computed properties for country and state options
const countries = computed(() => store.state.countries.map(c => ({ key: c.code, text: c.name })));
const billingCountry = computed(() => store.state.countries.find(c => c.code === customer.value.billingAddress.country_code));
const billingStateOptions = computed(() => {
  if (!billingCountry.value || !billingCountry.value.states) return [];
  return Object.entries(billingCountry.value.states).map(c => ({ key: c[0], text: c[1] }));
});
const shippingCountry = computed(() => store.state.countries.find(c => c.code === customer.value.shippingAddress.country_code));
const shippingStateOptions = computed(() => {
  if (!shippingCountry.value || !shippingCountry.value.states) return [];
  return Object.entries(shippingCountry.value.states).map(c => ({ key: c[0], text: c[1] }));
});

// Form submission function
function onSubmit() {
  loading.value = true;
  if (customer.value.id) {
    customer.value.status = !!customer.value.status;
    store.dispatch('updateCustomer', customer.value)
      .then(response => {
        loading.value = false;
        if (response.status === 200) {
          store.commit('showToast', 'Customer has been successfully updated');
          store.dispatch('getCustomers');
          router.push({ name: 'app.customers' });
        }
      })
      .catch(err => {
        errors.value = err.response.data.errors;
      });
  } else {
    store.dispatch('createCustomer', customer.value)
      .then(response => {
        loading.value = false;
        if (response.status === 201) {
          store.dispatch('getCustomers');
          router.push({ name: 'app.customers' });
        }
      })
      .catch(err => {
        loading.value = false;
      });
  }
}

// Fetch customer data on component mount
onMounted(() => {
  store.dispatch('getCustomer', route.params.id)
    .then(({ data }) => {
      title.value = `Update customer: "${data.first_name} ${data.last_name}"`;
      customer.value = data;
    });
});
</script>

<style scoped>

</style>

<template>
  <div class="employee" v-if="this.employee">
    <form class="employee__form form" @submit.prevent="submitForm">
      <div class="form__field" :class="{ 'form__field--invalid': !name.isValid }">
        <label class="form__label">
          <span class="form__span">Name</span>
          <input class="form__input" name="name" type="text" v-model.trim="name.value" @blur="validateName()" />
          <p class="form__error" v-if="!name.isValid">Name must not be empty</p>
        </label>
      </div>
      <div class="form__field" :class="{ 'form__field--invalid': !address.isValid }">
        <label class="form__label">
          <span class="form__span">Address</span>
          <input class="form__input" name="address" type="text" v-model.trim="address.value" @blur="validateAddress()" />
          <p class="form__error" v-if="!address.isValid">Address must not be empty</p>
        </label>
      </div>
      <div class="form__field" :class="{ 'form__field--invalid': !phone.isValid }">
        <label class="form__label">
          <span class="form__span">Phone</span>
          <input class="form__input" name="phone" type="tel" v-model.trim="phone.value" @blur="validatePhone()" />
          <p class="form__error" v-if="!phone.isValid">Phone is not a number or empty</p>
        </label>
      </div>
      <div class="form__field" :class="{ 'form__field--invalid': !salary.isValid }">
        <label class="form__label">
          <span class="form__span">Monthly salary ($)</span>
          <input class="form__input" name="salary" type="text" v-model.trim="salary.value" @blur="validateSalary()" />
          <p class="form__error" v-if="!salary.isValid">Salary is not a number or empty</p>
        </label>
      </div>
      <div class="form__field" :class="{ 'form__field--invalid': !date.isValid }">
        <label class="form__label">
          <span class="form__span">Date of birth</span>
          <input class="form__input" name="date" type="date" v-model.trim="date.value" @blur="validateDate()" />
          <p class="form__error" v-if="!date.isValid">Date later than today or empty</p>
        </label>
      </div>
      <button class="form__button button">Save</button>
    </form>
  </div>
  <div class="form__not-found" v-else>Employee not fount</div>
</template>

<script>
import { notify } from "@kyvg/vue3-notification";

export default {
  props: ['id'],
  created() {
    this.employee = this.$store.getters.getEmployeeById(this.id);
    if (this.employee) {
      this.name.value = this.employee.name;
      this.address.value = this.employee.address;
      this.phone.value = this.employee.phone;
      this.salary.value = this.employee.salary;
      this.date.value = this.employee.date;
      this.status.value = this.employee.status;
    }
  },
  data() {
    return {
      name: {
        value: '',
        isValid: true,
      },
      address: {
        value: '',
        isValid: true,
      },
      phone: {
        value: '',
        isValid: true,
      },
      salary: {
        value: '',
        isValid: true,
      },
      date: {
        value: '',
        isValid: true,
      },
      status: {
        value: true,
        isValid: true,
      },
      formIsValid: true,
    };
  },
  methods: {
    validateName() {
      if (this.name.value == '') {
        this.name.isValid = false;
        return false;
      } else {
        this.name.isValid = true;
        return true;
      }
    },
    validateAddress() {
      if (this.address.value == '') {
        this.address.isValid = false;
        return false;
      } else {
        this.address.isValid = true;
        return true;
      }
    },
    validatePhone() {
      if (!/^[+ 0-9]+$/.test(this.phone.value) || this.phone.value == '') {
        this.phone.isValid = false;
        return false;
      } else {
        this.phone.isValid = true;
        return true;
      }
    },
    validateSalary() {
      if (!/^[ 0-9]+$/.test(this.salary.value) || this.salary.value == '') {
        this.salary.isValid = false;
        return false;
      } else {
        this.salary.isValid = true;
        return true;
      }
    },
     validateDate() {
      if (Date.parse(this.date.value) > Date.now() && this.date.value) {
        this.date.isValid = false;
        return false;
      } 
      if (!this.date.value) {
        this.date.isValid = false;
        return false;
      } else {
        this.date.isValid = true;
        return true;
      }
    },
    validateStatus() {
      if (this.status.value == undefined) {
        this.status.isValid = false;
        return false;
      } else {
        this.status.isValid = true;
        return true;
      }
    },
    validateForm() {
      this.formIsValid = true;
      if (!this.validateName()) {
        this.formIsValid = false;
      }
      if (!this.validateAddress()) {
        this.formIsValid = false;
      }
      if (!this.validatePhone()) {
        this.formIsValid = false;
      }
      if (!this.validateSalary()) {
        this.formIsValid = false;
      }
      if (!this.validateDate()) {
        this.formIsValid = false;
      }
      if (!this.validateStatus()) {
        this.formIsValid = false;
      }
    },
    submitForm() {
      this.validateForm();

      if (!this.formIsValid) {
        return;
      }

      const formData = {
        id: this.id,
        name: this.name.value,
        address: this.address.value,
        phone: this.phone.value,
        salary: this.salary.value,
        date: this.date.value,
        status: this.status.value,
      };

      this.$store.dispatch('editEmployee', formData);
      notify({type: 'success', title: "The employee was edited!" });
      this.$router.replace('/employees/list');
    },
  },
};
</script>

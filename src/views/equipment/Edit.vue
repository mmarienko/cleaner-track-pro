<template>
  <div class="equipment" v-if="this.equipment">
    <form class="equipment__form form" @submit.prevent="submitForm">
      <div class="form__field" :class="{ 'form__field--invalid': !name.isValid }">
        <label class="form__label">
          <span class="form__span">Name</span>
          <input class="form__input" name="name" type="text" v-model.trim="name.value" @blur="validateName()" />
          <p class="form__error" v-if="!name.isValid">Name must not be empty</p>
        </label>
      </div>
      <div class="form__field" :class="{ 'form__field--invalid': !storage.isValid }">
        <label class="form__label">
          <span class="form__span">Storage location</span>
          <input class="form__input" name="storage" type="text" v-model.trim="storage.value" @blur="validateStorage()" />
          <p class="form__error" v-if="!storage.isValid">Location must not be empty</p>
        </label>
      </div>
      <div class="form__field" :class="{ 'form__field--invalid': !usage.isValid }">
        <label class="form__label">
          <span class="form__span">Monthly fee ($)</span>
          <input class="form__input" name="usage" type="text" v-model.trim="usage.value" @blur="validateUsage()" />
          <p class="form__error" v-if="!usage.isValid">Usage fee is not a number or empty</p>
        </label>
      </div>
      <button class="form__button button">Save</button>
    </form>
  </div>
  <div class="form__not-found" v-else>Equipment not fount</div>
</template>

<script>
import { notify } from "@kyvg/vue3-notification";

export default {
  props: ['id'],
  created() {
    this.equipment = this.$store.getters.getEquipmentById(this.id);
    if (this.equipment) {
      this.name.value = this.equipment.name;
      this.storage.value = this.equipment.storage;
      this.usage.value = this.equipment.usage;
      this.status.value = this.equipment.status;
    }
  },
  data() {
    return {
      name: {
        value: '',
        isValid: true,
      },
      storage: {
        value: '',
        isValid: true,
      },
      usage: {
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
    validateStorage() {
      if (this.storage.value == '') {
        this.storage.isValid = false;
        return false;
      } else {
        this.storage.isValid = true;
        return true;
      }
    },
    validateUsage() {
      if (!/^[ 0-9]+$/.test(this.usage.value) || this.usage.value == '') {
        this.usage.isValid = false;
        return false;
      } else {
        this.usage.isValid = true;
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
      if (!this.validateStorage()) {
        this.formIsValid = false;
      }
      if (!this.validateUsage()) {
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
        storage: this.storage.value,
        usage: this.usage.value,
        status: this.status.value,
      };

      this.$store.dispatch('editEquipment', formData);
      notify({type: 'success', title: "The equipment was edited!" });
      this.$router.replace('/equipment/list');
    },
  },
};
</script>

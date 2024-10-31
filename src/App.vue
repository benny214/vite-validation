

<template>
<div class="container">
  <div class="field">
    <label class="label">Name</label>

    <div class="control">
      <input 
        v-model="form.name"
        @focus="v.$reset()"
        :class="{'is-danger': !!validate({prop: 'name'})}"
        class="input"
        autocomplete="off"
        type="text">

        <p 
          v-if="!!validate({prop: 'name'})"
          class="help is-danger">
          {{ validate({prop: 'name'}) }}
        </p>
    </div>
  </div>

  <div class="field">
    <label class="label">email</label>

    <div class="control">
      <input 
        v-model="form.email"
        @focus="v.$reset()"
        :class="{'is-danger': !!validate({prop: 'email'})}"
        class="input"
        autocomplete="off"
        type="text">

        <p 
          v-if="!!validate({prop: 'email'})"
          class="help is-danger">
          {{ validate({prop: 'email'}) }}
        </p>
    </div>
  </div>

  <div class="field">
    <label class="label">phone</label>

    <div class="control">
      <input 
        @focus="v.$reset()"
        v-model="form.phone"
        v-maska
        :data-maska="'+7 (###) ###-##-##'"
        :class="{'is-danger': !!validate({prop: 'phone'})}"
        class="input"
        autocomplete="off"
        type="text">

        <p 
          v-if="!!validate({prop: 'phone'})"
          class="help is-danger">
          {{ validate({prop: 'phone'}) }}
        </p>
    </div>
  </div>

  <div class="field">
    <label class="label">Message</label>

    <div class="control">
      <textarea 
        v-model="form.message"
         @focus="v.$reset()"
        :class="{'is-danger': !!validate({prop: 'message'})}"
        class="textarea"
        autocomplete="off"
        />

        <p 
          v-if="!!validate({prop: 'message'})"
          class="help is-danger">
          {{ validate({prop: 'message'}) }}
        </p>
    </div>
  </div>

  <div class="field">
    <label class="label">password</label>

    <div class="control">
      <input 
        @focus="v.$reset()"
        autocomplete="off"
        :class="{'is-danger': !!validate({prop: 'password'})}"
        v-model="form.password"
        class="input"
        type="password">

        <p 
          v-if="!!validate({prop: 'password'})"
          class="help is-danger">
          {{ validate({prop: 'password'}) }}
        </p>
    </div>
  </div>

  <div class="field">
    <label class="label">current password</label>

    <div class="control">
      <input 
        @focus="v.$reset()"
        autocomplete="off"
        :class="{'is-danger': !!validate({prop: 'currentPassword'})}"
        v-model="form.currentPassword"
        class="input"
        type="password">

        <p 
          v-if="!!validate({prop: 'currentPassword'})"
          class="help is-danger">
          {{ validate({prop: 'currentPassword'}) }}
        </p>
    </div>
  </div>

  <div class="field">

    <div class="control">
      <label 
        :class="{'has-text-danger': !!validate({prop: 'checkbox'})}"
        class="checkbox">
      <input 
        v-model="form.checkbox"
        @focus="v.$reset()"
        autocomplete="off"
        type="checkbox">
        checkbox <a href="#">with link</a>
    </label>
    </div>

    <div class="control mx-auto mt-2">
      <button 
            @click="onSubmit"
            :disabled="form.pending"
            :class="{'is-loading': form.pending}"
            class="button is-link">Submit</button>
    </div>
  </div>

</div>
</template>


<script>
import { reactive, computed } from 'vue';
import { required, email, requiredPhone, maxLength, sameAs, minLength } from "./utils/i18n-validators.js";
import { vMaska } from "maska/vue";
import useValidate from "@vuelidate/core";

export default {
  setup() {
    // data
    const form = reactive({
      name: null,
      email: null,
      phone: null,
      textarea: null,
      password: null,
      currentPassword: null,
      checkbox: null,
      pending: null,
    })

    // computed
    const rules = computed(() => ({
      name: {
        required,
      },
      email: {
        required,
        email,
      },
      phone: {
        required,
        requiredPhone: requiredPhone(18),
      },
      message: {
        required,
        minLength: minLength(5),
      },
      password: {
        required,
        minLength: minLength(5),
        maxLength: maxLength(15),
      },
      currentPassword: {
        required,
        minLength: minLength(5),
        maxLength: maxLength(15),
        sameAs: sameAs(form.password),
      },
      checkbox: {
        required,
        sameAs: sameAs(true),
      }
    }));

    const v = useValidate(rules, form);

    // methods
    const onSubmit = async () => {
      v.value.$touch();

      if (form.pending) return

      if (await v.value.$validate()) {
        form.pending = true;

        try {
          const payload = {
            name: form.name,
            email: form.email,
            phone: form.phone,
            message: form.message,
            password: form.password,
            currentPassword: form.currentPassword,
            checkbox: form.checkbox,
          }

          setTimeout(() => {
            console.log(payload);
            console.log('Запрос отправлен')

            resetForm();
          }, 2500);

        } catch (e) {
          console.log(e);
        }
      }
    };

    const validate = ({ prop }) => {
      const error = v.value.$errors.find((el) => el.$property === prop);

      return error && error.$message;
    };

    const resetForm = () => {
      Object.keys(form).forEach((key) => {
        form[key] = null;
      })

      v.value.$reset();
    };

    return {
      v,
      form,
      onSubmit,
      validate,
    }
  },

  directives: {
    maska: vMaska,
  }
}
</script>
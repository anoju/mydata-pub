<template>
  <div>
    <div class="form-group" :class="{ 'form-group--error': $v.username.$error, 'form-group--loading': $v.username.$pending }">
      <label class="form__label">Username</label>
      <input class="form__input" v-model.trim="$v.username.$model"/>
    </div>
    <div class="error" v-if="!$v.username.required">Username is required.</div>
    <div class="error" v-if="!$v.username.isUnique">This username is already registered.</div>
    <tree-view :data="$v.username" :options="{rootObjectKey: '$v.username', maxDepth: 2}"></tree-view>
  </div>
</template>

<script>
import { required } from 'vuelidate/lib/validators';

export default {
  name: 'asynchronousValidation',
  data() {
    return {
      username: '',
    };
  },
  validations: {
    username: {
      required,
      isUnique(value) {
        // standalone validator ideally should not assume a field is required
        if (value === '') return true;

        // simulate async call, fail for all logins with even length
        return new Promise((resolve, reject) => {
          try {
            setTimeout(() => {
              resolve(typeof value === 'string' && value.length % 2 !== 0);
            }, 350 + Math.random() * 300);
          } catch (error) {
            reject(error);
          }
        });
      },
      // async isUnique (value) {
      //   if (value === '') return true
      //   const response = await fetch(`/api/unique/${value}`)
      //   return Boolean(await response.json())
      // }
    },
  },
};
</script>

<style>

</style>

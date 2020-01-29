<template>
  <v-app>
    <v-content>
      <stepper-form/>
    </v-content>
  </v-app>
</template>

<script>
import StepperForm from './components/StepperForm';

export default {
  name: 'App',

  components: {
    StepperForm,
  },

  data: () => ({
    //
  }),
};
</script>

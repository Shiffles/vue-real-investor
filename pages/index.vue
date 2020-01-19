<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      xs12
      sm8
      md6
    >
      <div class="text-center">
        <br>
        <br>
        <br>
      </div>
      <v-card>
        <v-card-title class="headline">
          The Real Estate Investalizer Awaits
        </v-card-title>
        <v-card-text>
          <p>Proceed to have an interpretive look at mortgage terms</p>
          <div class="text-xs-right">
            <em><small>&mdash; Nicholas Ullman</small></em>
          </div>
          <hr class="my-3">

        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            nuxt
            to="/mortgage"
          >
            Continue
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  components: {
    Logo,
    VuetifyLogo
  }
}
</script>

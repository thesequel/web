<template>
  <v-list-item
    active-class="planetactive"
    nuxt
    :to="`/p/${planet.name}`"
    @click="$emit('selected')"
  >
    <v-list-item-avatar
      size="32"
      :color="
        planet.avatarImageUrl
          ? planet.themeColor
            ? planet.themeColor
            : 'primary'
          : ''
      "
    >
      <v-img v-if="planet.avatarImageUrl" :src="planet.avatarImageUrl" />
      <v-icon v-else class="text--secondary">{{
        $vuetify.icons.values.mdiEarth
      }}</v-icon>
    </v-list-item-avatar>

    <v-list-item-content>
      <v-list-item-title>
        {{ planet.name }}
      </v-list-item-title>
      <v-list-item-subtitle v-if="!hidePostCount" style="font-weight: 500">
        {{ planet.postCount }} post{{ planet.postCount === 1 ? '' : 's' }} today
      </v-list-item-subtitle>
    </v-list-item-content>
  </v-list-item>
</template>

<script>
export default {
  name: 'PlanetListItem',
  props: {
    planet: {
      type: Object,
      required: true
    },
    hidePostCount: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<style scoped></style>

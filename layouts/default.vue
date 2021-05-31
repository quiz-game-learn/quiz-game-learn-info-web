<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="baseLink + item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title"/>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"/>
      <img src="@/assets/logo.png" class="image"></img>
      <v-toolbar-title v-text="title"/>
      <v-spacer/>

      <a
        v-for="locale in availableLocales"
        :key="locale.code"
        :href="(locale.code === 'es')?'/' :'/'+ locale.code">
        <v-toolbar-title class="subtitle-2" v-text="(locale.code === 'es')? 'vers. español' : 'English version'"/>
      </a>

    </v-app-bar>
    <v-main>
      <v-container>
        <nuxt/>
      </v-container>
    </v-main>

    <v-footer
      :absolute="!fixed"
      app
    >
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: this.$t('Home'),
          to: "/"
        },
        {
          icon: 'mdi-book-open-page-variant-outline',
          title: this.$t('About'),
          to: '/posts/' + this.$t('about_post')
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Quiz Game Learn'
    }
  },
  methods: {
  },
  computed: {
    availableLocales() {
      return this.$i18n.locales.filter(i => i.code !== this.$i18n.locale)
    },
    baseLink() {
      let base = ""
      if (this.$i18n.locale === "es") {
        base = ""
      } else {
        base = "/en"
      }
      console.log(this.$i18n.locale,base)
      return base
    }
  }
}
</script>

<i18n>
{
  "en": {
    "Home": "Home",
    "About": "About",
    "about_post": "about.en"
  },
  "es": {
    "Home": "Inicio",
    "About": "Acerca de",
    "about_post": "about.es"
  }
}
</i18n>

<style>
.image{
  border-width: 10px;
  border-color: white;
  max-width: 60px;
  margin-right: 20px;
}
</style>

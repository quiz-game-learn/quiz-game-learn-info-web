<template>
  <div class="content">
    <h1
      class="my-8 max-w-full m-auto text-3xl text-center font-medium"
    >
      {{ post.title }}
    </h1>
    <h4 v-if="hasTranslation()" class="body-2 text-center">
      <a :href="translationPage()">{{ $t('Translated version') }}</a>
    </h4>
    <h3 class="py-4 text-center uppercase">{{ post.description }}</h3>
    <nuxt-content :document="post" class="leading-loose"/>

    <br></br>

    <p>{{ $t('Article last updated:') }} {{ formatDate(post.date) }}</p>
    <p> <span v-if="post.tags" v-for="tag in post.tags" :key="tag">
      <nuxt-link :to="baseLink+'/tag/'+tag"><v-chip>{{ tag }}</v-chip></nuxt-link>
    </span></p>

    <br></br>
    <v-card
      v-if="post.toc && post.toc.length >0"
      elevation="0"
    >
      <v-card-title>{{ $t('Table of contents') }}</v-card-title>
      <v-card-text>
        <ul>
          <li v-for="link of post.toc" :key="link.id"
              :class="{ 'py-2': link.depth === 2, 'ml-2 pb-2': link.depth === 3 }">
            <NuxtLink :to="`#${link.id}`">{{ link.text }}</NuxtLink>
          </li>
        </ul>
      </v-card-text>
    </v-card>

  </div>
</template>

<script>
export default {
  async asyncData({$content, params, error, app}) {
    const locale = app.i18n.locale

    const post = await $content(`posts/${params.slug}`)
      .fetch()
      .catch(err => {
        error({statusCode: 404, message: "Página no encontrada"});
      });

    return {
      post,
      locale,
      slug: params.slug
    };
  },
  methods: {
    formatDate(date) {
      const options = {year: 'numeric', month: 'long', day: 'numeric'}
      return new Date(date).toLocaleDateString('en', options)
    },
    hasTranslation() {
      console.log(this.slug)
      return this.slug.includes(".es") || this.slug.includes(".en")
    },
    translationPage() {
      console.log(this.locale)
      if (this.locale === "es") {
        return "/en/posts/" + this.slug.replace(".es", ".en")
      } else {
        return "/posts/" + this.slug.replace(".en", ".es")
      }
    }
  },
  computed: {
    baseLink() {
      let base = ""
      if (this.$i18n.locale === "es") {
        base = ""
      } else {
        base = "/en"
      }
      return base
    }

  }
}
</script>

<style>
.icon.icon-link {
}
.content {
  margin: 0 auto;
  max-width: 800px;
}
svg {
  fill: red;
}
</style>

<i18n>
{
  "en": {
    "Translated version": "versión en español",
    "Article last updated:": "Article last updated:",
    "Table of contents": "Table of contents"
  },
  "es": {
    "Translated version": "english version",
    "Article last updated:": "Articulo actualizado ",
    "Table of contents": "Contenidos"
  }
}
</i18n>

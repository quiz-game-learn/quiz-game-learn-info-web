<template>
  <div>
    <h1 v-if="!tag || tag===''" class="text-3xl py-6">{{ index.title }}</h1>

    <h1 v-if="tag && tag!==''" class="text-3xl py-6">{{$t('Posts in tag')}} <v-chip>{{tag}}</v-chip></h1>

    <p v-if="!tag || tag===''"  class="text-xl py-3">{{ index.description }}</p>
    <nuxt-content :document="index" class="leading-loose center"/>
    <div class="container">
      <div v-for="(post, index) in posts" :key="index">
        <nuxt-link :to="getLink(post.path)" class="underline">
          <v-card class="card">
            <div class="d-flex flex-no-wrap justify-space-between">
              <div>
                <v-card-title>
                  {{ post.title }}
                </v-card-title>
                <v-card-text>
                  {{ post.description }}
                </v-card-text>
                <v-card-subtitle>
                  {{ formatDate(post.date) }}
                </v-card-subtitle>
              </div>
              <v-avatar
                v-if="post.image"
                class="ma-3"
                size="125"
                tile
              >
                <v-img v-if="post.image"
                       :src="post.image"></v-img>
              </v-avatar>
            </div>
          </v-card>
        </nuxt-link>
        <br></br>
      </div>
    </div>

    <div class="text-center">
      <v-pagination
        v-model="page"
        :length="4"
        @input="nextPage"
      ></v-pagination>
    </div>
  </div>


</template>


<script>

const fetchPosts = async ($content, error, locale, tag, page, itemsPerPage) => {
  const skip = (page-1)*itemsPerPage

  const where = {language:locale}
  if (tag && tag !== "") where.tags = { $contains: tag }

  console.log(page, skip)
  return await $content("posts")
    .only(["title", "path", "date", "description", "image"])
    .limit(itemsPerPage)
    .skip(skip)
    .sortBy('date', "desc")
    .where(where)
    .fetch()
    .catch(err => {
      error({statusCode: 404, message: "Página no encontrada"});
    });
}

export default {
  async asyncData({$content, route, params, error, app}) {
    const tag = params.tag || ""

    const page = 1
    const itemsPerPage = 10

    const locale = app.i18n.locale

    const contentFile = (locale === "es") ? "index.es" : "index.en"


    const index = await $content(contentFile)
      .fetch()
      .catch(err => {
        error({statusCode: 404, message: "index not found"});
      });

    const posts = await fetchPosts($content, error, locale, tag, page, itemsPerPage)

    return {
      index,
      posts,
      page,
      locale,
      itemsPerPage,
      tag
    };
  },
  methods: {
    async nextPage(){
      this.posts = await fetchPosts(this.$content,this.error, this.locale, this.page, this.itemsPerPage )
      console.log(this.posts)
    },
    formatDate(date) {
      const options = { year: 'numeric', month: 'long', day: 'numeric' }
      return new Date(date).toLocaleDateString('en', options)
    },
    getLink(path){
      if (this.locale === "es"){
        return path
      } else {
        return `/${this.locale}${path}`
      }
    }
  }

};
</script>

<style>
a:link {
  text-decoration: none;
}
.card{
  margin: 0 auto;
  max-width: 600px !important;
}
.center{
  text-align: center;
}
</style>


<i18n>
{
  "en": {
    "Posts in tag": "Posts in tag "
  },
  "es": {
    "Posts in tag": "Posts en "
  }
}
</i18n>

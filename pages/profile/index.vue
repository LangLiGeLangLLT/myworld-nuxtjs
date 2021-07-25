<template>
    <div class="profile-page">

        <div class="user-info">
            <div class="container">
                <div class="row">

                    <div class="col-xs-12 col-md-10 offset-md-1">
                        <img :src="user.image" class="user-img" />
                        <h4>{{ user.username }}</h4>
                        <p>
                            Cofounder @GoThinkster, lived in Aol's HQ for a few months, kinda looks like Peeta from the Hunger Games
                        </p>
                        <button class="btn btn-sm btn-outline-secondary action-btn">
                            <i class="ion-plus-round"></i>
                            &nbsp;
                            Follow {{ user.username }}
                        </button>
                    </div>

                </div>
            </div>
        </div>

        <div class="container">
            <div class="row">

                <div class="col-xs-12 col-md-10 offset-md-1">
                    <div class="articles-toggle">
                        <ul class="nav nav-pills outline-active">
                            <li class="nav-item">
                                <nuxt-link
                                    class="nav-link"
                                    :class="{
                                        active: tab === 'my_articles'
                                    }"
                                    :to="{
                                        name: 'profile',
                                        query: {
                                            tab: 'my_articles'
                                        },
                                        params: {
                                            username: user.username
                                        }
                                    }"
                                    exact>My Articles</nuxt-link>
                            </li>
                            <li class="nav-item">
                                <nuxt-link
                                    class="nav-link"
                                    :class="{
                                        active: tab === 'favorited_articles'
                                    }"
                                    :to="{
                                        name: 'profile',
                                        query: {
                                            tab: 'favorited_articles'
                                        },
                                        params: {
                                            username: user.username
                                        }
                                    }"
                                    exact>Favorited Articles</nuxt-link>
                            </li>
                        </ul>
                    </div>

                    <div
                        class="article-preview"
                        v-for="article in articles">
                        <div class="article-meta">
                            <nuxt-link :to="{
                                name: 'article',
                                params: {
                                    slug: article.slug
                                }
                            }">
                                <img :src="article.author.image" />
                            </nuxt-link>
                            <div class="info">
                                <nuxt-link
                                    :to="{
                                        name: 'profile',
                                        params: {
                                            username: article.author.username
                                        }
                                    }"
                                    class="author">
                                    {{ article.author.username }}
                                </nuxt-link>
                                <span class="date">
                                    {{ article.updatedAt | date('MMMM D') }}
                                </span>
                            </div>
                            <button
                                class="btn btn-outline-primary btn-sm pull-xs-right"
                                :class="{ active: article.favorited }"
                                @click="onFavorite(article)"
                                :disabled="article.favoriteDisabled">
                                <i class="ion-heart"></i> {{ article.favoritesCount }}
                            </button>
                        </div>
                        <nuxt-link
                            :to="{
                                name: 'article',
                                params: {
                                    slug: article.slug
                                }
                            }"
                            class="preview-link">
                            <h1>{{ article.title }}</h1>
                            <p>{{ article.description }}</p>
                            <span>Read more...</span>
                            <ul class="tag-list">
                                <li class="tag-default tag-pill tag-outline"
                                    v-for="tag in article.tagList">
                                    {{ tag }}
                                </li>
                            </ul>
                        </nuxt-link>
                    </div>

                </div>

            </div>
        </div>

    </div>
</template>

<script>
  import { mapState } from 'vuex'
  import { getArticles, addFavorite, deleteFavorite } from '@/api/article'

  export default {
    middleware: 'authenticated',
    watchQuery: ['tab'],
    name: 'UserProfile',
    computed: {
      ...mapState(['user'])
    },
    async asyncData({ query, store }) {
      const tab = query.tab || 'my_articles'

      const loadArticles = store.state.user && tab === 'my_articles' ?
        getArticles.bind(null, { author: store.state.user.username }) :
        getArticles.bind(null, { favorited: store.state.user.username })

      const { data } = await loadArticles()
      const { articles } = data

      articles.forEach(article => article.favoriteDisabled = false)

      return {
        articles,
        tab
      }
    },
    methods: {
      async onFavorite(article) {
        article.favoriteDisabled = true
        if (article.favorited) {
          // 取消点赞
          await deleteFavorite(article.slug)
          article.favorited = false
          article.favoritesCount += -1
        } else {
          // 添加点赞
          await addFavorite(article.slug)
          article.favorited = true
          article.favoritesCount += 1
        }
        article.favoriteDisabled = false
      }
    }
  };
</script>

<style scoped>

</style>

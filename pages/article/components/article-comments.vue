<template>
    <div>
        <form class="card comment-form">
            <div class="card-block">
                <textarea
                    class="form-control"
                    placeholder="Write a comment..."
                    rows="3"
                    v-model="comment.body"></textarea>
            </div>
            <div class="card-footer">
                <img :src="user.image" class="comment-author-img" />
                <button
                    class="btn btn-sm btn-primary"
                    @click="onAddComment">
                    Post Comment
                </button>
            </div>
        </form>

        <div
            class="card"
            v-for="comment in comments"
            :key="comment.id">
            <div class="card-block">
                <p class="card-text">{{ comment.body }}</p>
            </div>
            <div class="card-footer">
                <nuxt-link
                    :to="{
                        name: 'profile',
                        params: {
                            username: comment.author.username
                        }
                    }"
                    class="comment-author">
                    <img :src="comment.author.image" class="comment-author-img" />
                </nuxt-link>
                &nbsp;
                <nuxt-link
                    :to="{
                        name: 'profile',
                        params: {
                            username: comment.author.username
                        }
                    }"
                    class="comment-author">
                    {{ comment.author.username }}
                </nuxt-link>
                <span class="date-posted">{{ comment.createdAt | date('MMM DD, YYYY') }}</span>
            </div>
        </div>
    </div>
</template>

<script>
  import { mapState } from 'vuex'
  import { getComments, addComment } from '@/api/article'

  export default {
    name: 'ArticleComments',
    props: {
      article: {
        type: Object,
        required: true
      }
    },
    computed: {
      ...mapState(['user'])
    },
    data() {
      return {
        comments: [],
        comment: { body: '' }
      }
    },
    async mounted() {
      const { data } = await getComments(this.article.slug)
      this.comments = data.comments
    },
    methods: {
      async onAddComment() {
        await addComment(this.article.slug, { comment: this.comment })
      }
    }
  };
</script>

<style scoped>

</style>

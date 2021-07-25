<template>
    <div class="editor-page">
        <div class="container page">
            <div class="row">

                <div class="col-md-10 offset-md-1 col-xs-12">
                    <form>
                        <fieldset>
                            <fieldset class="form-group">
                                <input
                                    type="text"
                                    class="form-control form-control-lg"
                                    placeholder="Article Title"
                                    v-model="article.title">
                            </fieldset>
                            <fieldset class="form-group">
                                <input
                                    type="text"
                                    class="form-control"
                                    placeholder="What's this article about?"
                                    v-model="article.description">
                            </fieldset>
                            <fieldset class="form-group">
                                <textarea
                                    class="form-control"
                                    rows="8"
                                    placeholder="Write your article (in markdown)"
                                    v-model="article.body"></textarea>
                            </fieldset>
                            <fieldset class="form-group">
                                <input
                                    type="text"
                                    class="form-control"
                                    placeholder="Enter tags"
                                    @change="onEnterTags($event)">
                                <div class="tag-list"></div>
                            </fieldset>
                            <button
                                class="btn btn-lg pull-xs-right btn-primary"
                                type="button"
                                @click="onSubmit">
                                Publish Article
                            </button>
                        </fieldset>
                    </form>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
  import { addArticle } from '@/api/article'

  export default {
    // 在路由匹配组件渲染之前会先执行中间件处理
    middleware: 'authenticated', // 可以是数组
    name: 'EditorIndex',
    data() {
      return {
        article: {
          title: '',
          description: '',
          body: '',
          tagList: []
        }
      }
    },
    methods: {
      async onSubmit() {
        await addArticle({ article: this.article })
        this.$router.push('/')
      },
      onEnterTags(e) {
        this.article.tagList = (e.target.value || '').split(/;/)
      }
    }
  };
</script>

<style scoped>

</style>

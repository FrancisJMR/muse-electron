<template>
  <v-layout column justify-center>
    <v-flex>
      <p>Enter a url to explore its RDF content.</p>
      <v-form v-model="valid">
        <v-text-field
          v-model="url"
          label="Lookup URL"
          :rules="urlRules"
          required
          placeholder="http://blog.woorank.com/2013/04/dublin-core-metadata-for-seo-and-usability/"
          @keyup.enter="loadResource"
        ></v-text-field>
      </v-form>
      <div v-if="metadata">
        <tree-view :data="metadata" :options="{modifiable: true}"></tree-view>
      </div>
    </v-flex>
  </v-layout>
</template>

<script>
  import htmlmeta from 'html-metadata'
  // import TreeView from 'vue-json-tree-view'
  export default {
    data () {
      return {
        metadata: 'Hi there!',
        valid: false,
        url: null,
        urlRules: [
          v => !!v || 'Full URL is required',
          v => /^http(s?):\/\/\w+([.-]?\w+)*(\.\w+)*([.-]?\w+\/)*([.-]?\w+)*$/.test(v) || 'URL must be valid'
        ]
      }
    },
    components: {
      // TreeView
    },
    mounted () {
    },
    methods: {
      loadResource () {
        console.log('hello')
        // var url = 'http://blog.woorank.com/2013/04/dublin-core-metadata-for-seo-and-usability/'
        htmlmeta(this.url)
          .then(function (metadata) {
            console.log(this.metadata)
            this.metadata = metadata
          }.bind(this))
          .catch(function (error) {
            console.log(error)
          })
      }
    }
  }
</script>

<style scoped>
  img {
    margin-left: auto;
    margin-right: auto;
    display: block;
  }
</style>
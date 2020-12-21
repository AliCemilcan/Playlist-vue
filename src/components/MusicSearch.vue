<template>
    <p class="control has-icons-left">
        <input type="text" placeholder="Search for songs" v-model="searchInput" class="input"/>
        <span class="icon is-small is-left">
            <font-awesome-icon icon="search" />
        </span>
    </p>
</template>
<script>
export default {
  props: {
    songs: {
      type: Array,
      required: true
    }
  },
  data () {
    return {
      searchInput: ''
    }
  },
  watch: {
    searchInput (searchTerm) {
      this.search(searchTerm)
    }
  },
  methods: {
    search (searchTerm) {
      if (searchTerm === '') {
        this.$emit('filteredSongs', this.songs)
        return
      }
      const piWorker = new Worker('/../music.worker.js', { type: 'module' })
      console.log(searchTerm)
      piWorker.postMessage({ search: searchTerm, songs: this.songs })
      piWorker.onmessage = (m) => {
        this.$emit('filteredSongs', m.data.filteredSongs)
      }
    }
  }
}
</script>

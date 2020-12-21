<template>
    <thead>
      <th style="width: 30%"><a @click="change_sort('artist')">Artist
        <font-awesome-icon icon="arrow-up" v-if="sortBy== 'artist' && sortDirection == 'asc'" />
         <font-awesome-icon icon="arrow-down" v-if="sortBy== 'artist' && sortDirection == 'desc'" />
         </a></th>
      <th style="width: 30%"><a @click="change_sort('title')"> Title
        <font-awesome-icon icon="arrow-up" v-if="sortBy== 'title' && sortDirection == 'asc'" />
         <font-awesome-icon icon="arrow-down" v-if="sortBy== 'title' && sortDirection == 'desc'" />
          </a></th>
      <th style="width: 20%"><a @click="change_sort('album')">Album
        <font-awesome-icon icon="arrow-up" v-if="sortBy== 'album' && sortDirection == 'asc'" />
         <font-awesome-icon icon="arrow-down" v-if="sortBy== 'album' && sortDirection == 'desc'" />
         </a></th>
      <th style="width: 20%"><a @click="change_sort('genre')">Genre
        <font-awesome-icon icon="arrow-up" v-if="sortBy== 'genre' && sortDirection == 'asc'" />
         <font-awesome-icon icon="arrow-down" v-if="sortBy== 'genre' && sortDirection == 'desc'" />
         </a></th>
    </thead>
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
      sortBy: '',
      sortDirection: 'asc'
    }
  },
  watch: {
    songs () {
      console.log('Hop sarkilari sortladin')
      this.sorted_songs()
    }
  },

  methods: {
    change_sort (column) {
      if (this.sortBy === column && this.sortDirection === 'asc') {
        this.sortDirection = 'desc'
      } else {
        this.sortDirection = 'asc'
      }
      this.sortBy = column
      this.sorted_songs()
    },
    sorted_songs () {
      console.log(this.sortBy)
      if (this.sortBy === '') {
        this.$emit('sortedSongs', this.songs)
        return
      }

      let sortModifier = (this.sortDirection === 'asc') ? 1 : -1

      let sortedSongs = this.songs.slice().sort((a, b) => {
        let colA = a[this.sortBy].toUpperCase()
        let colB = b[this.sortBy].toUpperCase()

        if (colA < colB) {
          return -1 * sortModifier
        }
        if (colA > colB) {
          return 1 * sortModifier
        }
        return 0
      })
      this.$emit('sortedSongs', sortedSongs)
    }
  }
}
</script>

<style>
</style>

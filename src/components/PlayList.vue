<template>
<div class="columns">
  <div class="column is-3">
    <playlists :selectedPlaylistSlug="selectedPlaylistSlug" @setPlaylist="set_playlist" />
  </div>

  <div class="column is-9">
    <table class="table is-full-width is-striped is-hoverable is-narrow">
      <sorted :songs="songs" @sortedSongs="sorted_songs" />
      <paginated :items="sortedSongs">
          <template slot="custom_column" slot-scope="songList"><a @click="delete_song(songList.song)" class="button is-small is-danger"><font-awesome-icon icon="trash"></font-awesome-icon></a></template>
      </paginated>
    </table>
  </div>
</div>
</template>

<script>
import MusicSort from '@/components/MusicSort.vue'
import PlayLists from '@/components/PlayLists.vue'
import PaginatedTableBody from '@/components/PaginatedTableBody'
export default {
  components: {
    'playlists': PlayLists,
    'sorted': MusicSort,
    'paginated': PaginatedTableBody
  },
  data () {
    return {
      selectedPlaylist: {},
      selectedPlaylistSlug: this.$route.params.slug,
      songs: [],
      sortedSongs: []
    }
  },
  methods: {
    // here we are communication back and forward with child component
    // $emit event
    sorted_songs (data) {
      console.log('sorted osngs for playlist', data)
      this.sortedSongs = data
    },
    set_playlist (playlist) {
      console.log(playlist, 'selected')
      this.selectedPlaylist = playlist
      this.songs = playlist.songs
    },
    delete_song (song) {
      this.selectedPlaylist.songs.splice(this.selectedPlaylist.songs.indexOf(song), 1)
    }
  },
  created () {
    console.log(this)
  },
  beforeRouteUpdate (to, from, next) {
    this.selectedPlaylistSlug = to.params.slug
    next()
  }
}
</script>

<style>
table td{
  word-break: break-word;
}
@keyframes flash{
  from {background-color: green}
  to {background-color: inherit}
}
.flash{
  animation: flash 1s 1;
}
</style>

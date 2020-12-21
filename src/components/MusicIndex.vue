<template>
<div class="columns">
  <div class="column is-3">
    <playlists addingEnabled @setActivePlaylists="set_active_playlist" />
  </div>

  <div class="column is-9">
    <search :songs="songs" @filteredSongs="filter_songs" />
      <h2 class="subtitle" v-if="activePlaylists.length > 0">Adding songs to
        <template v-for="(pl, index) in activePlaylists">
          <span class="has-text-weight-bold" v-bind:key="index">{{ pl.name }}<template v-if="index + 1 < activePlaylists.length">, </template></span></template>. Click on a song title to add it to your playlists.
      </h2>

    <table class="table is-full-width is-striped is-hoverable is-narrow">
      <sorted :songs="filteredSongs" @sortedSongs="sorted_songs" />
      <paginated :items="sortedSongs">

      <template slot="add_title" slot-scope="songList" v-if="activePlaylists.length > 0"><a @click="add_song(songList.song, $event.target)"><font-awesome-icon icon="plus"/></a></template>
      </paginated>
    </table>
  </div>
</div>
</template>

<script>
import MusicSearch from '@/components/MusicSearch'
import MusicData from '@/assets/list.json'
import MusicSort from '@/components/MusicSort.vue'
import PlayLists from '@/components/PlayLists.vue'
import PaginatedTableBody from '@/components/PaginatedTableBody'
export default {
  components: {
    'search': MusicSearch,
    'playlists': PlayLists,
    'sorted': MusicSort,
    'paginated': PaginatedTableBody
  },
  data () {
    return {
      songs: MusicData,
      sortedSongs: MusicData,
      filteredSongs: MusicData,
      activePlaylists: []
    }
  },
  methods: {
    // here we are communication back and forward with child component
    // $emit event
    sorted_songs (data) {
      this.sortedSongs = data
    },
    set_active_playlist (playlists) {
      console.log('here', playlists)
      this.activePlaylists = playlists
    },
    add_song (song, e) {
      console.log('insnide add_song', song)
      // add flas here
      e.closest('tr').classList.add('flash')
      setTimeout(() => e.closest('tr').classList.remove('flash'), 3000)
      this.activePlaylists.forEach((element, index) => {
        console.log(element)
        this.activePlaylists[index].songs.push(song)
      })
    },
    filter_songs (data) {
      this.filter_songs = data
    }
  },
  created () {
    console.log(this.activePlaylists)
  }
}
</script>

<style>

</style>

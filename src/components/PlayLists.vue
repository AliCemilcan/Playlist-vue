<template>
  <nav class="panel">
  <p class="panel-block" v-bind:class="{'is-active': selectedPlaylistSlug === undefined}">
      <router-link to="/">All songs</router-link>
    </p>
    <p class="panel-heading">Playlists</p>
    <template v-for="(playlist,index) in playlists">
      <div class="panel-block playlist-item" v-bind:key="playlist.slug" v-bind:class="{'is-active': selectedPlaylistSlug === playlist.slug}">
       <router-link v-if="!playlist.editing" :to="'/playlist/' + playlist.slug" class="panel-block"> <span class="panel-icon"><font-awesome-icon icon="book" /></span>
        {{ playlist.name }}
        </router-link>
        <form class="panel-block" v-if="playlist.editing" @submit.prevent="edit_playlist(index)">
          <div class="field has-addons">
            <input type="text" v-model="playlist.name" class="input">
            <p class="control"><button type="submit" class="button is-success"><font-awesome-icon icon="check" /></button></p>
          </div>
        </form>

        <div>
            <div class="dropdown is-hoverable">
              <div class="dropdown-trigger"><a aria-haspopup="true"
              aria-controls="dropdown-menu">
              <font-awesome-icon icon="chevron-down" /></a>
              </div>
              <div class="dropdown-menu">
                <div class="dropdown-content">
                  <a class="dropdown-item" title="Rename playlist"
                  @click="edit_playlist(index)"><font-awesome-icon icon="edit" /></a>
                   <a class="dropdown-item" title="Delete playlist"
                  @click="delete_playlist(index)"><font-awesome-icon icon="trash-alt" /></a>
                </div>
              </div>
            </div>
        <template v-if="addingEnabled">
          <a v-if="!playlist.adding" @click="add_songs(index)" title="Add songs"><font-awesome-icon icon="plus" /></a>
          <a v-if="!playlist.adding" @click="add_songs(index)" title="Disable Adding songs"><font-awesome-icon icon="check-square" /></a>
        </template>
        </div>
      </div>
    </template>

    <div class="panel-block">
      <form @submit.prevent="add_playlist">
        <div class="field has-addons">
          <input
            type="text"
            class="input"
            placeholder="New Playlist"
            v-model="newPlaylistName"
          />
          <p class="control">
            <button type="submit" class="button is-success">
              <font-awesome-icon icon="plus" />Add
            </button>
          </p>
        </div>
      </form>
    </div>
  </nav>
</template>

<script>
import localforage from 'localforage'
export default {
  props: {
    addingEnabled: {
      type: Boolean,
      default: false,
      required: false
    },
    selectedPlaylistSlug: {
      type: String,
      required: false
    }
  },
  data () {
    return {
      newPlaylistName: '',
      playlists: []
    }
  },
  watch: {
    playlists: {
      handler (playlists) {
        console.log('hellos')
        localforage.setItem('playlists', playlists)
      },
      deep: true
    },
    selectedPlaylistSlug (slug) {
      console.log('inside setPlaylist')
      this.$emit('setPlaylist', this.playlists.find(
        pl => pl.slug === slug
      ))
    }
  },
  methods: {
    // set playlist with songs object
    add_playlist () {
      this.playlists.push({
        name: this.newPlaylistName,
        slug: this.slugify(this.newPlaylistName),
        adding: false,
        editing: false,
        songs: []
      })
      console.log('slugifyname', this.slugify(this.newPlaylistName))
      this.newPlaylistName = ''
    },
    delete_playlist (index) {
      this.playlists.splice(index, 1)
    },
    edit_playlist (index) {
      this.playlists[index].editing = !this.playlists[index].editing
    },
    add_songs (index) {
      this.playlists[index].adding = !this.playlists[index].adding
      this.$emit('setActivePlaylists', this.playlists.filter(pl => pl.adding === true))
    },
    slugify (name) {
      return name
        .toString()
        .toLowerCase()
        .trim()
        .replace(/\s+/g, '-') // Replace spaces with -
        .replace(/&/g, '-and-') // Replace & with 'and'
        .replace(/[^\w-]+/g, '') // Remove all non-word chars
        .replace(/--+/g, '-') // Replace multiple - with single -
    }
  },
  created () {
    localforage.getItem('playlists')
      .then(data => {
        if (data !== null) {
          console.log(data)
          this.playlists = data
          // when we create the page, we will set all songs to false
          this.playlists.forEach((pl, index) => {
            this.playlists[index].adding = false
            this.playlists[index].editing = false
          })
        }
      })
      .then(() => {
        if (this.selectedPlaylistSlug !== undefined) {
          this.$emit('setPlaylist', this.playlists.find(pl => pl.slug === this.selectedPlaylistSlug))
        }
      })
  }

}
</script>
<style>
@media (min-width: 769px) {
  nav {
    position: fixed;
  }
}
@media (max-width: 1215px) {
  nav {
    width: inherit;
  }
}
.playlist-item {
  justify-content: space-between
}

</style>

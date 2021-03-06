<template>
  <content-with-hero>
    <template slot="heading-left">
      <h1 class="title is-5">{{ album.name }}</h1>
      <h2 class="subtitle is-6 has-text-link has-text-weight-normal"><a class="has-text-link" @click="open_artist">{{ album.artist }}</a></h2>

      <div class="buttons fd-is-centered-mobile fd-has-margin-top">
        <a class="button is-small is-dark is-rounded" @click="play">
          <span class="icon"><i class="mdi mdi-shuffle"></i></span> <span>Shuffle</span>
        </a>
        <a class="button is-small is-light is-rounded" @click="show_album_details_modal = true">
          <span class="icon"><i class="mdi mdi-dots-horizontal mdi-18px"></i></span>
        </a>
      </div>
    </template>
    <template slot="heading-right">
      <p class="image is-square fd-has-shadow fd-has-action">
        <cover-artwork
          :artwork_url="album.artwork_url"
          :artist="album.artist"
          :album="album.name"
          @click="show_album_details_modal = true" />
      </p>
    </template>
    <template slot="content">
      <p class="heading is-7 has-text-centered-mobile fd-has-margin-top">{{ album.track_count }} tracks</p>
      <list-tracks :tracks="tracks" :uris="album.uri"></list-tracks>
      <modal-dialog-album :show="show_album_details_modal" :album="album" @close="show_album_details_modal = false" />
    </template>
  </content-with-hero>
</template>

<script>
import { LoadDataBeforeEnterMixin } from './mixin'
import ContentWithHero from '@/templates/ContentWithHero'
import ListTracks from '@/components/ListTracks'
import ModalDialogAlbum from '@/components/ModalDialogAlbum'
import CoverArtwork from '@/components/CoverArtwork'
import webapi from '@/webapi'

const albumData = {
  load: function (to) {
    return Promise.all([
      webapi.library_album(to.params.album_id),
      webapi.library_album_tracks(to.params.album_id)
    ])
  },

  set: function (vm, response) {
    vm.album = response[0].data
    vm.tracks = response[1].data.items
  }
}

export default {
  name: 'PageAlbum',
  mixins: [LoadDataBeforeEnterMixin(albumData)],
  components: { ContentWithHero, ListTracks, ModalDialogAlbum, CoverArtwork },

  data () {
    return {
      album: {},
      tracks: [],

      show_album_details_modal: false
    }
  },

  methods: {
    open_artist: function () {
      this.show_details_modal = false
      this.$router.push({ path: '/music/artists/' + this.album.artist_id })
    },

    play: function () {
      webapi.player_play_uri(this.album.uri, true)
    }
  }
}
</script>

<style>
</style>

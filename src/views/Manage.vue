<template>
  <!-- Main Content -->
  <section class='container mx-auto mt-6'>
    <div class='md:grid md:grid-cols-3 md:gap-4'>
      <div class='col-span-1'>
        <app-upload ref='upload' :addSong='addSong' />
      </div>
      <div class='col-span-2'>
        <div class='bg-white rounded border border-gray-200 relative flex flex-col'>
          <div class='px-6 pt-6 pb-5 font-bold border-b border-gray-200'>
            <span class='card-title'>My Songs</span>
            <i class='fa fa-compact-disc float-right text-green-400 text-2xl'></i>
          </div>
          <div class='p-6'>
            <composition-item
              v-for='(song, i) in songs'
              :key='song.docID'
              :song='song'
              :updateSong='updateSong'
              :index='i'
              :removeSong='removeSong'
              :updateUnsavedChanges='updateUnsavedChanges'
            />
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
<script>
import AppUpload from '@/components/Upload.vue'
import CompositionItem from '@/components/CompositionItem.vue'
import { songsCollection, auth } from '@/includes/firebase'

export default {
  name: 'Manage',
  components: {
    AppUpload,
    CompositionItem
  },
  data() {
    return {
      songs: [],
      unsavedChanges: false
    }
  },
  async created() {
    const snapshot = await songsCollection.where('uid', '==', auth.currentUser.uid).get()
    snapshot.forEach(this.addSong)
  },
  methods: {
    updateSong(i, values) {
      this.songs[i].modifiedName = values.modifiedName
      this.songs[i].genre = values.genre
    },
    removeSong(i) {
      this.songs.splice(i, 1)
    },
    addSong(doc) {
      const song = {
        ...doc.data(),
        docID: doc.id
      }
      this.songs.push(song)
    },
    updateUnsavedChanges(value) {
      this.unsavedChanges = value
    }
  },
  beforeRouteLeave(to, from, next) {
    if (!this.unsavedChanges) {
      next()
    } else {
      const answer = window.confirm('You have unsaved changes. Are you sure you want to leave?')
      if (answer) {
        next()
      } else {
        next(false)
      }
    }
    next()
  }
}
</script>

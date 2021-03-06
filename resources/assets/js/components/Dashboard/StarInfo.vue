<template>
  <div class="star-info bg-grey-lighter relative flex flex-col">
    <div
      v-if="!noRepoSelected"
      class="star-info-bar flex bg-white border-b border-grey-light h-16 px-4 items-center">
      <button
        class="bg-grey-light hover:bg-grey transition-bg rounded text-grey-darkest text-xs px-2 py-2 font-bold tracking-wide uppercase focus-none"
        @click="notesShowing = !notesShowing">
        <Icon
          :type="toggleNotesIcon"
          height="16"
          width="16"
          class="mr-1 pointer-events-none stroke-current fill-none inline-block align-bottom"/>
        <span>{{ notesShowing ? 'Hide Notes' : 'Show Notes' }}</span>
      </button>
      <div class="ml-auto">
        <label
          for="starCloneUrl"
          class="mr-2 font-bold cursor-pointer">Clone:</label>
        <input
          id="starCloneUrl"
          :value="currentStarCloneUrl"
          type="text"
          readonly="readonly"
          class="github-clone-url rounded border-2 border-grey-light h-10 px-2 focus-none transition-border-color"
          @focus="highlightText"
        >
      </div>
    </div>
    <div
      v-if="noRepoSelected"
      class="flex flex-col flex-1 items-center justify-center w-full bg-grey-lighter no-repo-selected">
      <img
        src="/images/not-selected.svg"
        class="w-64 mb-8"
      />
      <span class="font-bold bg-grey px-4 py-2 text-white rounded">No Repo Selected</span>
    </div>
    <readme
      v-if="readme"
      :readme="readme"/>
    <div
      v-if="repoHasNoReadme"
      class="flex flex-col flex-1 items-center justify-center w-full bg-grey-lighter">
      <img
        src="/images/no-readme.svg"
        class="w-64 mb-8"
      />
      <span class="font-bold bg-grey px-4 py-2 text-white rounded">No README Found</span>
    </div>
    <notes-editor
      v-if="notesEditorShowing"
      :notes="currentStarNotes"
      @save="doEditStarNotes"/>
  </div>
</template>
<script>
import { mapGetters, mapActions } from 'vuex'
import NotesEditor from '@/components/Dashboard/NotesEditor'
import Readme from '@/components/Dashboard/Readme'
import Icon from '@/components/Icon'
export default {
  name: 'StarInfo',
  components: {
    Icon,
    NotesEditor,
    Readme
  },
  data () {
    return {
      notesShowing: false
    }
  },
  computed: {
    ...mapGetters(['readme', 'currentStar']),
    noRepoSelected () {
      return !Object.keys(this.currentStar).length
    },
    repoHasNoReadme () {
      return Object.keys(this.currentStar).length && !this.readme
    },
    currentStarCloneUrl () {
      return `git@github.com:${this.currentStar.node.nameWithOwner}.git`
    },
    currentStarNotes () {
      if (!this.noRepoSelected && this.currentStar.hasOwnProperty('notes')) {
        return this.currentStar.notes
      } else {
        return ''
      }
    },
    notesEditorShowing () {
      return this.notesShowing && !this.noRepoSelected
    },
    toggleNotesIcon () {
      return this.currentStarNotes ? 'FileTextIcon' : 'FileIcon'
    }
  },
  methods: {
    ...mapActions(['editStarNotes']),
    doEditStarNotes (notes) {
      this.editStarNotes({
        id: this.currentStar.node.databaseId,
        notes
      })
    },
    highlightText (e) {
      e.currentTarget.select()
    }
  }
}
</script>
<style lang="scss" scoped>
.github-clone-url {
  width: 300px;
  &:focus {
    border-color: config('colors.grey');
  }
}
</style>

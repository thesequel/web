<template>
  <div v-if="post.textContent" class="mt-2">
    <div
      :class="
        expand || isPostView || textContentHeight <= 90 ? '' : 'textcontent'
      "
      :style="
        expand || isPostView || textContentHeight <= 90 ? '' : 'cursor: pointer'
      "
      v-on="
        !isPostView && textContentHeight > 90 ? { click: toggleTextExpand } : {}
      "
    >
      <TextContent
        :text-content="post.textContent"
        :dark="$vuetify.theme.dark"
      />
    </div>
  </div>
  <div v-else-if="expand" class="mt-4">
    <v-row
      v-if="post.type === 'IMAGE' && isEmbeddableImage"
      no-gutters
      justify="start"
    >
      <a
        :href="post.link"
        rel="noopener nofollow noreferrer"
        target="_blank"
        @click.stop.prevent="openImageLink"
      >
        <img
          alt="Image preview"
          :src="post.link"
          style="max-height: 500px; max-width: 100%"
        />
      </a>
    </v-row>

    <v-row
      v-else-if="
        isYoutubeLink || isTweetLink || isSpotifyLink || isInstagramLink
      "
      no-gutters
      justify="start"
    >
      <client-only>
        <div
          v-if="isYoutubeLink"
          :class="$device.isDesktop ? '' : 'youtubecontainer'"
        >
          <iframe
            :src="`https://www.youtube.com/embed/${youtubeId}`"
            frameborder="0"
            allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture"
            allowfullscreen
            scrolling="no"
            :class="$device.isDesktop ? '' : 'youtube'"
            :style="$device.isDesktop ? 'width: 560px; height: 315px' : ''"
          />
        </div>

        <Tweet
          v-else-if="isTweetLink"
          :id="tweetId"
          :style="$device.isDesktop ? 'width: 560px' : 'width: 100%'"
        />

        <iframe
          v-else-if="isSpotifyLink"
          :src="spotifyUrl"
          width="300"
          height="380"
          frameborder="0"
          allowtransparency="true"
          allow="encrypted-media"
          :style="
            $device.isDesktop
              ? 'width: 300px; height: 380px'
              : 'width: 100%; height: 380px'
          "
        />

        <instagram-embed
          v-else-if="isInstagramLink"
          :url="post.link"
          :max-width="500"
        />
      </client-only>
    </v-row>
  </div>
</template>

<script>
import { getIdFromUrl } from 'vue-youtube'
import InstagramEmbed from 'vue-instagram-embed'
import { Tweet } from 'vue-tweet-embed'
import TextContent from '@/components/TextContent'

export default {
  name: 'PostPreview',
  components: {
    TextContent,
    InstagramEmbed,
    Tweet
  },
  props: {
    post: {
      type: Object,
      required: true
    },
    textContentHeight: {
      type: Number,
      required: true
    },
    expand: {
      type: Boolean,
      required: true
    },
    isPostView: {
      type: Boolean,
      required: true
    }
  },
  computed: {
    isEmbeddableImage() {
      return this.post.type === 'IMAGE' && this.post.link.startsWith('https://')
    },
    isYoutubeLink() {
      return (
        this.post.type === 'LINK' &&
        (this.post.link.includes('youtube.com/') ||
          this.post.link.includes('youtu.be/'))
      )
    },
    isTweetLink() {
      return (
        this.post.type === 'LINK' &&
        this.post.link.includes('twitter.com/') &&
        this.post.link.includes('/status/')
      )
    },
    isSpotifyLink() {
      return (
        this.post.type === 'LINK' &&
        this.post.link.startsWith('https://open.spotify.com/')
      )
    },
    isInstagramLink() {
      return (
        this.post.type === 'LINK' &&
        this.post.link.startsWith('https://www.instagram.com/p/')
      )
    },
    spotifyUrl() {
      if (!this.isSpotifyLink) return ''
      const split = this.post.link.split('.com/')
      return (split[0] + '.com/embed/' + split[1]).split('?si=')[0]
    },
    youtubeId() {
      if (!this.isYoutubeLink) return ''
      else return getIdFromUrl(this.post.link)
    },
    tweetId() {
      if (!this.isTweetLink) return ''
      else return this.post.link.split('status/')[1].split('?')[0]
    }
  },
  methods: {
    openImageLink() {
      window.open(this.post.link, '_blank')
    },
    toggleTextExpand(e) {
      this.$emit('togglemore')
      e.stopPropagation()
      e.preventDefault()
    }
  }
}
</script>

<style scoped>
.textcontent {
  max-height: 90px;
  overflow: hidden;
  -webkit-mask-image: linear-gradient(180deg, #000 60%, transparent);
  mask-image: linear-gradient(180deg, #000 60%, transparent);
}

.spotifyframe >>> iframe {
  width: 300px;
  height: 380px;
}

.youtubecontainer {
  position: relative;
  width: 100%;
  padding-bottom: 56.25%;
}

.youtube {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border: 0;
}
</style>

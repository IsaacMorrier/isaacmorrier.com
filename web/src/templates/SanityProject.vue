<template>
  <div class="project" :key="this.$store.state.page">

    <project-card
        :key="$page.project.id"
        :project="$page.project"
        :metadata="$page.metadata"
        :linked="false"
      />
    
    <!-- <div class="project__excerpt">
      <div class="project__excerpt-container">
        <block-content :blocks="$page.project._rawExcerpt" />
      </div>
    </div> -->

    <block-content
      class="project__content body-content"
      :blocks="$page.project._rawBody"
      v-if="$page.project._rawBody"
    />

    <div class="project__next">
      <div class="project__next-text-container">
          <p>next:</p>
      </div>
    </div>

    <project-card
        :key="next.id"
        :project="next"
        :metadata="next.metadata"
      />

    <div class="project__footer">
      <div class="project__footer-nav-container">
        <g-link to="/" class="nav-link">home</g-link>
        <g-link to="/info" class="nav-link">info</g-link>
        <a href="https://instagram.com/isaacmorrier" target="_blank" class="nav-link">instagram</a>
        <a href="https://linkedin.com/in/isaacmorrier" target="_blank" class="nav-link">linkedin</a>
      </div>
      <div class="project__footer-copyright-container">
          <p><span class="nav-link">© Isaac Morrier 2013&ndash;{{ new Date().getFullYear() }}</span></p>
      </div>
    </div>
  </div>

</template>

<script>
import urlForImage from '~/utils/urlForImage.js'
import ProjectCard from '~/components/ProjectCard'
import BlockContent from '~/components/BlockContent'

export default {
  name: "Project",
  components: {
    ProjectCard,
    BlockContent
  },
  metaInfo() {
    return {
      title: this.$page.project.title,
      meta: [
        {
          name: 'description',
          content: this.$page.project.description
        }
      ]
    }
  },
  methods: {
    commitSubtitle: function(subtitle) {
      this.$store.commit('setSubtitle', subtitle)
    }
  },
  computed: {
    next() {
      const projects = this.$page.projects.edges
      const currentId = this.$page.project.id
       
      // create array of IDs
      const ids = []
      for (var  i=0; i<projects.length; i++) {
        ids.push(projects[i].node.id)
      }

      // set next to node with current index + 1, or to 0 for the last project
      var nextIndex = (ids.indexOf(currentId) < projects.length - 1) ? (ids.indexOf(currentId) + 1) : 0;
      const next = projects[nextIndex].node

      // get main image
      const image = next.mainImage
      const options = this.$page.metadata.sanityOptions
      const url = urlForImage(image, options).url()
      const params = urlForImage(image, options).auto('format').url().replace(url, '')
      const src = url + '/' + image.filename.current + params
      next.src = src

      return next
    }
  },
  mounted(){
    this.commitSubtitle(this.$page.project.title)
  },
  updated(){
    this.commitSubtitle(this.$page.project.title)
  }
}
</script>

<page-query>
  query Project ($id: ID!) {
    metadata {
      sanityOptions {
        projectId
        dataset
      }
    }
    project: sanityProject (id: $id) {
      id
      title
      subtitle
      years
      slug {
        current
      }
      mainImage {
          alt
          asset {
            _id
            url
          }
          filename {
            current
          }
          caption
          layout
        }
      _rawExcerpt
      _rawBody
    }
    projects: allSanityProject(sortBy: "publishedAt") {
      edges {
        node {
          id
          title
          subtitle
          years
          slug {
            current
          }
          categories {
            id
            title
          }
          mainImage {
            alt
            asset {
              _id
              url
            }
            filename {
              current
            }
            caption
            layout
          }
        }
      }
    }
  }
</page-query>

<style lang="scss">
@import '~/assets/style/_utils.scss';

.project {
  @extend .grid-container;
  
  &__excerpt {
    @extend .row, .grid-padding;
  }
  &__excerpt-container {
    @extend .col-7, .col-offset-2, .col-md-3, .col-xl-offset-1;
  }
  &__content,
  &__footer,
  &__next {
    @extend .row, .grid-padding;
  }
  &__footer-nav-container {
    @extend .col-xxl-10
  }
  &__next-text-container {
    margin-top: 8rem;
  }
  &__title {
    font-size: 100%;
    margin-bottom: 1em;
  }
}
</style>

<template>
  <portable-text
    :blocks="blocks"
    :serializers="serializers"
    :projectId="$static.metadata.sanityOptions.projectId"
    :dataset="$static.metadata.sanityOptions.dataset"
  />
</template>

<script>
import PortableText from 'sanity-blocks-vue-component'

export default {
  props: {
    blocks: Array
  },
  components: {
    PortableText
  },
  data() {
    return {
      serializers: {
        types: {
          mainImage: ({ node }) => (
            <figure class={node.layout}>
              <img
                src={
                  this.$urlForImage(node, this.$static.metadata.sanityOptions)
                  .url()
                  +
                  '/'
                  +
                  node.filename.current
                  +
                  this.$urlForImage(node, this.$static.metadata.sanityOptions)
                  .auto('format')
                  .url()
                  .replace(
                    this.$urlForImage(node, this.$static.metadata.sanityOptions).url(),
                    ''
                  )
                }
                alt={node.alt}
              />
              <figcaption>{node.caption}</figcaption>
            </figure>
          )
        }
      }
    }
  }
}
</script>

<static-query>
  {
    metadata{
    sanityOptions{
      projectId
      dataset
    }
  }
}
</static-query>

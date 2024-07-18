<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col>
            <v-btn variant="text" @click="openModalHanler('Image')">Image</v-btn>
            <v-btn variant="text" @click="openModalHanler('Video')">Video</v-btn>
            <v-dialog v-model="showModal" max-width="500px">
              <media-modal @insert="insertMedia" :dataType="dataType"></media-modal>
            </v-dialog>
            
            <v-row class="mt-3 border-t">
              <v-col class="v-col-6 bg-grey-lighten-2">
                <v-textarea
                label="Markdown Editor"
                v-model="markdownContent"
                rows="20"
                outlined
              ></v-textarea>
              </v-col>
              <v-col class="v-col-6 bg-grey-lighten-2 border-s" >
                <div v-html="renderedMarkdown"></div>
              </v-col>
            </v-row>
            
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { ref, computed } from 'vue';
import MediaModal from './components/MediaModal.vue';


    const showModal = ref(false);
    const dataType = ref('');
    const markdownContent = ref(`
# Markdown Editor

This is a **markdown editor** with support for inserting media.

## Example Usage

1. Click on the **Insert Media** button.
2. Select an image, video, or file to insert.
3. Upload new media if needed.
4. The media will be inserted as markdown.

![Image](https://via.placeholder.com/150)
![Video](https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4)
`);
    const openModalHanler = (type)=>{
      showModal.value = true;
      dataType.value = type
    }
    const insertMedia = (media) => {
      console.log(media);
      media.forEach(item => {
        markdownContent.value += `![${dataType.value}](${item.url})\n`;
        //console.log(item.name);
      });

      // markdownContent.value += `![${media.type}](${media.url})\n`;
       showModal.value = false;
    };

    const parseMarkdown = (content) => {
      let html = content
        .replace(/!\[Image]\((.*?)\)/g, '<img class="customImages" src="$1" alt="Image">')
        .replace(/!\[Video]\((.*?)\)/g, '<video controls><source src="$1" type="video/mp4"></video>')
        .replace(/(^|\s)## (.*?)($|\s\s)/g, '$1<h2>$2</h2>$3')
        .replace(/(^|\s)# (.*?)($|\s\s)/g, '$1<h1>$2</h1>$3')
        .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
        .replace(/(\r\n|\r|\n)/g, '<br>')
        .replace(/\*(.*?)\*/g, '<em>$1</em>');
      return html;
    };

    const renderedMarkdown = computed(() => parseMarkdown(markdownContent.value));


</script>

<style>

video {
  max-width: 100%;
}
.customImages{
  max-width: 100%;
}
</style>

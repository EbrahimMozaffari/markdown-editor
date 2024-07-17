<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col>
            <v-btn variant="text" @click="openModalHanler('image')">Image</v-btn>
            <v-btn variant="text" @click="openModalHanler('video')">Video</v-btn>
            <v-dialog v-model="showModal" max-width="500px">
              <media-modal @insert="insertMedia" :dataType="dataType"></media-modal>
            </v-dialog>
            <hr class="my-3 grey" >
            <v-textarea
              label="Markdown Editor"
              v-model="markdownContent"
              rows="10"
              outlined
            ></v-textarea>
            <v-divider></v-divider>
            <div v-html="renderedMarkdown"></div>
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
![File](https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf)
`);
    const openModalHanler = (type)=>{
      showModal.value = true;
      dataType.value = type
    }
    const insertMedia = (media) => {
      markdownContent.value += `![${media.type}](${media.url})\n`;
      showModal.value = false;
    };

    const parseMarkdown = (content) => {
      let html = content
        .replace(/!\[Image]\((.*?)\)/g, '<img src="$1" alt="Image">')
        .replace(/!\[Video]\((.*?)\)/g, '<video controls><source src="$1" type="video/mp4"></video>')
        .replace(/!\[File]\((.*?)\)/g, '<a href="$1" download>Download File</a>')
        .replace(/(^|\s)## (.*?)($|\s)/g, '$1<h2>$2</h2>$3')
        .replace(/(^|\s)# (.*?)($|\s)/g, '$1<h1>$2</h1>$3')
        .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>')
        .replace(/\*(.*?)\*/g, '<em>$1</em>');
      return html;
    };

    const renderedMarkdown = computed(() => parseMarkdown(markdownContent.value));


</script>

<style>

video {
  max-width: 100%;
}
</style>

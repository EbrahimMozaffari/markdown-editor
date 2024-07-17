<template>
    <v-card>
      <v-card-title>Select Media</v-card-title>
      <v-card-text>
        <v-form @submit.prevent="handleSubmit">
          <v-select
            v-model="selectedType"
            :items="mediaTypes"
            label="Media Type"
            outlined
          ></v-select>
          <v-file-input
            v-model="selectedFile"
            label="Upload File"
            accept="image/*,video/*,.pdf,.doc,.docx"
            outlined
          ></v-file-input>
          <v-btn type="submit" color="primary">Insert</v-btn>
        </v-form>
        <v-divider></v-divider>
        <v-list>
          <v-list-item v-for="item in mediaList" :key="item.url" @click="selectExistingMedia(item)">
            <v-list-item-content>{{ item.name }}</v-list-item-content>
          </v-list-item>
        </v-list>
      </v-card-text>
    </v-card>
  </template>
  
  <script>
  import { ref } from 'vue';
  
  export default {
    emits: ['insert'],
    setup(props, { emit }) {
      const selectedType = ref('');
      const selectedFile = ref(null);
  
      const mediaTypes = ['Image', 'Video', 'File'];
  
      const mediaList = ref([
        { name: 'Sample Image', url: 'https://via.placeholder.com/150', type: 'Image' },
        { name: 'Sample Video', url: 'https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4', type: 'Video' },
        { name: 'Sample File', url: 'https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf', type: 'File' }
      ]);
  
      const handleSubmit = async () => {
        if (selectedFile.value) {
          const media = await uploadFile(selectedFile.value);
          emit('insert', media);
        }
      };
  
      const uploadFile = (file) => {
        return new Promise((resolve) => {
          setTimeout(() => {
            resolve({ name: file.name, url: URL.createObjectURL(file), type: selectedType.value });
          }, 1000);
        });
      };
  
      const selectExistingMedia = (item) => {
        emit('insert', item);
      };
  
      return {
        selectedType,
        selectedFile,
        mediaTypes,
        mediaList,
        handleSubmit,
        selectExistingMedia
      };
    }
  };
  </script>
  
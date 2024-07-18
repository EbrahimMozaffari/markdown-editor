<template>
    <v-card min-height="450px">
      <v-tabs v-model="tab" align-tabs="center" color="deep-purple-accent-4">
        <v-tab :value="1">Select Media</v-tab>
        <v-tab :value="2">Upload</v-tab>
      </v-tabs>
      <v-tabs-window v-model="tab">
        <v-tabs-window-item :value="1">
          <v-card-text>
          <v-card-title>Select {{dataType}}</v-card-title>
          <v-select
                class="my-3"
                v-model="selectedData"
                :items="mediaListType[dataType]"
                :label='`Select ${dataType}`'
                item-title="name"
                multiple
                persistent-hint
                return-object
              
              >
            </v-select>
            <v-btn color="primary"  class="w-100" @click="insertData">Insert</v-btn>
          </v-card-text>
        </v-tabs-window-item>
        <v-tabs-window-item :value="2">
          <v-card-text>
            <v-card-title>Upload {{dataType}}</v-card-title>
            <v-form @submit.prevent="handleSubmit">
              <v-file-input
                v-model="selectedFile"
                :label="`Upload ${dataType}`"
                :accept="acceptData"
                multiple="true"
                outlined
              ></v-file-input>
              <v-btn type="submit" color="primary" class="w-100">Upload</v-btn>
            </v-form>
          </v-card-text>
        </v-tabs-window-item>
      </v-tabs-window>
      <v-card-text>
      </v-card-text>
    </v-card>
  </template>
  
  <script setup>
  import { computed, reactive, ref } from 'vue';
  
      const emits = defineEmits(['insert'])
      const props = defineProps({dataType:String})
      const selectedType = ref('');
      const selectedFile = ref(null);
      const tab = ref(1);
      const selectedData = ref([]);
      const mediaTypes = ['Image', 'Video'];
  
      const mediaListType = reactive(
        { 
          Image: [
            { name: 'Sample Image1', url: 'https://via.placeholder.com/150'},
            { name: 'Sample Image2', url: 'https://via.placeholder.com/150'},
            { name: 'Sample Image3', url: 'https://via.placeholder.com/150'},
            { name: 'Sample Image4', url: 'https://via.placeholder.com/150'}
          ],
          Video: [
            { name: 'Sample Video1', url: 'https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4' },
            { name: 'Sample Video2', url: 'https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4' },
            { name: 'Sample Video3', url: 'https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4' },
            { name: 'Sample Video4', url: 'https://sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4' }
          ]
        }
      );
  
      const handleSubmit = async () => {
        if (selectedFile.value) {
          const media = await uploadFile(selectedFile.value);
          emits('insert', media);
        }
      };
  
      const uploadFile = (file) => {
        let newfiles = [];
        file.forEach(item => {
          newfiles.push({ name: item.name, url: URL.createObjectURL(item) })
        });
        console.log(newfiles);
        return new Promise((resolve) => {
          setTimeout(() => {
            resolve(newfiles);
          }, 1000);
        });
      };
      const insertData = () => {
        console.log(selectedData.value);
        emits('insert', selectedData.value);
      };
      const acceptData = () => {
        return dataType.value == 'Image' ? "image/*" : "video/*";
      };
  </script>
  
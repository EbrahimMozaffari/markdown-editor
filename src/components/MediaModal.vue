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
            accept="image/*,video/*"
            outlined
          ></v-file-input>
          <v-btn type="submit" color="primary">Insert</v-btn>
        </v-form>
        <v-divider></v-divider>
        <v-select
        class="my-3"
        v-model="favorites"
        :items="mediaListType[dataType]"
        :label='`Select ${dataType}`'
        multiple
        persistent-hint
        
      >
      <template v-slot:prepend-item>
        <v-list-item
          title="Select All"
          @click="toggle"
        >
          <template v-slot:prepend>
            <v-checkbox-btn
              :color="likesSomeFruit ? 'indigo-darken-4' : undefined"
              :indeterminate="likesSomeFruit && !likesAllFruit"
              :model-value="likesAllFruit"
            ></v-checkbox-btn>
          </template>
        </v-list-item>
  
        <v-divider class="mt-2"></v-divider>
      </template>
  
      <template v-slot:append-item>
        <v-divider class="mb-2"></v-divider>
  
        <v-list-item
          :subtitle="subtitle"
          :title="title"
          disabled
        >
          <template v-slot:prepend>
            <v-avatar color="primary" icon="mdi-food-apple"></v-avatar>
          </template>
        </v-list-item>
      </template>
    </v-select>
        <v-divider></v-divider>
        <v-list>
          <v-list-item v-for="item in mediaListType[dataType]" :key="item.url" @click="selectExistingMedia(item)">
            <v-list-item-content>{{ item.name }}</v-list-item-content>
          </v-list-item>
        </v-list>
      </v-card-text>
    </v-card>
  </template>
  
  <script setup>
  import { computed, reactive, ref } from 'vue';
  
  defineEmits(['insert'])
  const props = defineProps({dataType:String})
    // emits: ['insert'],
    // props:{
    //   dataType:String
    // },

      const selectedType = ref('');
      const selectedFile = ref(null);
      const favorites = ref([]);
      const mediaTypes = ['Image', 'Video'];
  
      const mediaListType = reactive(
        { 
          image: [
            { name: 'Sample Image1', url: 'https://via.placeholder.com/150'},
            { name: 'Sample Image2', url: 'https://via.placeholder.com/150'},
            { name: 'Sample Image3', url: 'https://via.placeholder.com/150'},
            { name: 'Sample Image4', url: 'https://via.placeholder.com/150'}
          ],
          video: [
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
      
      const likesAllFruit  = computed(()=>{
        return favorites.value.length === mediaListType.image.length;
      })
      const likesSomeFruit   = computed(()=>{
        return favorites.value.length > 0 && !likesAllFruit.value;
      })
      const title    = computed(()=>{
        return likesAllFruit.value ? 'All' : likesSomeFruit.value ? 'Some' : 'None';
      })
      const subtitle    = computed(()=>{
        return `${title.value} of ${mediaListType.image.length} images liked`
      })
      const toggle = ()=>{
        if(favorites.value.length === mediaListType[dataType].length){
          favorites.value = [];
          }else{
          favorites.value = mediaListType[dataType]
          }
      }
  </script>
  
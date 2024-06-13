<template>
    <modal id="project-import" :state="state" :hideable="!awaitingResponse" backdrop
      @hide="$emit('hide')">
      <template #title>title</template>
      <template #body>
        <div class="modal-introduction">
          <p>Blabla</p>
        </div>
        <form @submit.prevent="submit">
          <form-group ref="file" v-model="file"
            :placeholder="blabla" type="file" required autocomplete="off"/>
          <div class="modal-actions">
            <button type="submit" class="btn btn-primary"
              :aria-disabled="awaitingResponse">
            Import <spinner :state="awaitingResponse"/>
            </button>
            <button type="button" class="btn btn-link"
              :aria-disabled="awaitingResponse" @click="$emit('hide')">
              Cancel
            </button>
          </div>
        </form>
      </template>
    </modal>
  </template>
  
  <script>
  import FormGroup from '../form-group.vue';
  import Modal from '../modal.vue';
  import Spinner from '../spinner.vue';
  import useRequest from '../../composables/request';
  import { noop } from '../../util/util';
  
  export default {
    name: 'ProjectImport',
    components: { FormGroup, Modal, Spinner },
    props: {
      state: {
        type: Boolean,
        default: false
      }
    },
    emits: ['hide', 'success'],
    setup() {
      const { request, awaitingResponse } = useRequest();
      return { request, awaitingResponse };
    },
    data() {
      return {
        file: null
      };
    },
    watch: {
      state(state) {
        if (!state) this.file = null;
      }
    },
    methods: {
      submit() {
        const formData = new FormData();
        formData.append('file', this.file);
        this.request({
          method: 'POST',
          url: '/v1/projects/import',
          data: formData,
          headers: { 'Content-Type': 'multipart/form-data' }
        })
          .then(({ data }) => {
            this.$emit('success', data);
          })
          .catch(noop);
      }
    }
  };
  </script>
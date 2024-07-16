<template>
<MazDrawer :variant="'right'" :modelValue="isOpenedRight" @update:modelValue="updateIsOpenedRight">
    <template #title>
      List of Users
    </template>
    <template #default="{ close }">
      <div style="padding: 16px;">
        <div class="flex flex-wrap gap-05">
          <MazBtn color="danger" @click="close">Annuler</MazBtn>
          <MazBtn color="success" @click="confirmSelection">Confirmer</MazBtn>
        </div>

        <table id="field-key-list-table" class="table">
            <thead>
                <tr>
                <th></th> <!--Checkbox column header-->
                <th>{{ $t('header.displayName') }}</th>
                </tr>
            </thead>
            <tbody>
            <row-mobile-user v-for="mobileUser of mobileUsers" :key="mobileUser.id"
            :mobile-user="mobileUser" :highlighted="highlighted" :selected-users="selectedUsers"/>
            </tbody>
        </table>
        
      </div>
    </template>
</MazDrawer>
</template>

<script>
import MazDrawer from 'maz-ui/components/MazDrawer';
import MazBtn from 'maz-ui/components/MazBtn';
import RowMobileUser from './row-mobile-user.vue';

import { useRequestData } from '../../../request-data';
import { apiPaths } from '../../../util/request';
import { noop } from '../../../util/util';

export default {
  name: 'MobileUsersSideBar',
  components:{MazDrawer, MazBtn, RowMobileUser},
  props: {
    isOpenedRight: {
      type: Boolean,
      required: true,
    },
    selectedUsers: {
      type: Array,
      required: true,
    }
  },
  setup(){
    const { mobileUsers } = useRequestData();
    const fetchAllMobileUsers = () => {
      mobileUsers.request({
      url: apiPaths.allMobileUsers(),
      extended: true,
      }).catch(noop);
    };

    fetchAllMobileUsers();
    return {mobileUsers};
  },
  data(){
    return {highlighted: null}
  },
  methods: {
    updateIsOpenedRight(value) {
      this.$emit('update:isOpenedRight', value);
    },
    closeDrawer() {
      this.$emit('update:isOpenedRight', false);
    },
    confirmSelection() {
      this.$emit('confirm');
      this.closeDrawer();
    },
  },
}
</script>
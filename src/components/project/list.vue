<!--
Copyright 2017 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<!-- Hafsa edited this file-->
<template>
  <div id="project-list">
    <page-section>
      <template #heading>
        <span>{{ $t('resource.projects') }}</span>
        <button v-if="currentUser.can('project.create')"
          id="project-list-new-button" type="button" class="btn btn-primary"
          @click="createModal.show()">
          <span class="icon-plus-circle"></span>{{ $t('action.create') }}&hellip;
        </button>

        <button v-if="currentUser.can('project.create')"
          id="project-list-import-button" type="button" class="btn btn-secondary"
          @click="openModal">
          <span class="icon-upload"></span> Import &hellip;
        </button>

        <project-sort v-model="sortMode"/>
      </template>
      <template #body>
        
        <FileUploadModal v-if="modalOpen" @file-selected="handleFileSelected" @close="closeModal" />
        
        <div v-if="sheetData">
          <div id="sheet-header">
            <h2>Excel Sheet Data</h2>
            <div class="action-buttons">
              <button v-if="!showCheckboxes" type="button" id="btn-affect" class="btn btn-secondary" @click="toggleSelection">
                Select
              </button>
              <div v-if="showCheckboxes">
                <button type="button" class="btn btn-secondary" @click="affectSelection">
                  Affect
                </button>
                <button type="button" class="btn btn-secondary" @click="cancelSelection">
                  Cancel
                </button>
              </div>
            </div>
          </div>
          <div class="table-container">
          <table border="1">
            <thead>
              <tr>
                <th v-if="showCheckboxes"></th> <!--Checkbox column header-->
                <th v-for="(header, index) in sheetData[0]" :key="index">{{ header }}</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(row, rowIndex) in sheetData.slice(1)" :key="rowIndex">
                <!-- Select Dropdown-->
                <td v-if="showCheckboxes">
                  <input type="checkbox" v-model="row.selected">
                </td>
                <td v-for="(cell, cellIndex) in row" :key="cellIndex">
                  {{ formatCell(cell, sheetData[0][cellIndex]) }}
                </td>

              </tr>
            </tbody>
          </table>
        </div>
        </div>
        <MobileUsersSideBar :isOpenedRight="isOpenedRight" @update:isOpenedRight="isOpenedRight" 
        @confirm="postAffectation" :selectedUsers="selectedUsers"/>
        <div v-if="projects.dataExists">
          <project-home-block v-for="project of chunkyProjects" :key="project.id"
            :project="project" :sort-func="sortFunction"
            :max-forms="maxForms" :max-datasets="maxDatasets"/>
        </div>
        <loading :state="projects.initiallyLoading"/>
        <p v-if="projects.dataExists && activeProjects.length === 0"
          class="empty-table-message">
          <template v-if="currentUser.can('project.create')">
            {{ $t('emptyTable.canCreate') }}<sentence-separator/>
            <i18n-t keypath="moreInfo.clickHere.full">
              <template #clickHere>
                <doc-link to="central-projects/">{{ $t('moreInfo.clickHere.clickHere') }}</doc-link>
              </template>
            </i18n-t>
          </template>
          <template v-else>{{ $t('emptyTable.cannotCreate') }}</template>
        </p>
      </template>
    </page-section>
    <page-section v-if="archivedProjects.length > 0">
      <template #heading>
        <span>{{ $t('archived') }}</span>
      </template>
      <template #body>
        <div id="project-list-archived">
          <div v-for="project of archivedProjects" :key="project.id">
            <div class="project-title">
              <router-link :to="projectPath(project.id)">{{ project.name }}</router-link>
            </div>
          </div>
        </div>
      </template>
    </page-section>
    <project-new v-bind="createModal" @hide="createModal.hide()"
      @success="afterCreate"/>
  </div>
</template>

<script>
import { computed, ref, watchEffect } from 'vue';
import { sum } from 'ramda';

import DocLink from '../doc-link.vue';
import Loading from '../loading.vue';
import PageSection from '../page/section.vue';
import ProjectNew from './new.vue';
import ProjectHomeBlock from './home-block.vue';
import ProjectSort from './sort.vue';
import SentenceSeparator from '../sentence-separator.vue';
import MobileUsersSideBar from './sidebar/mobile-users.vue';

import FileUploadModal from './import.vue'; 
import { format } from 'date-fns';
import useRequest from '../../composables/request';
import sortFunctions from '../../util/sort';
import useChunkyArray from '../../composables/chunky-array';
import useRoutes from '../../composables/routes';
import { modalData } from '../../util/reactivity';
import { sumUnderThreshold } from '../../util/util';
import { useRequestData } from '../../request-data';
import { apiPaths } from '../../util/request';

export default {
  name: 'ProjectList',
  components: {
    DocLink,
    Loading,
    PageSection,
    ProjectNew,
    ProjectHomeBlock,
    ProjectSort,
    SentenceSeparator,
    FileUploadModal,
    MobileUsersSideBar
  },
  inject: ['alert'],
  setup() {
    const { currentUser, projects } = useRequestData();
    const { request, awaitingResponse } = useRequest();
    const sortMode = ref('latest');
    const sortFunction = computed(() => sortFunctions[sortMode.value]);

    const activeProjects = ref(null);
    watchEffect(() => {
      activeProjects.value = projects.dataExists
        ? projects.filter((p) => !(p.archived))
        : [];
    });
    watchEffect(() => { activeProjects.value.sort(sortFunction.value); });
    const chunkyProjects = useChunkyArray(activeProjects);

    const { projectPath } = useRoutes();
    return {
      currentUser, projects,
      sortMode, sortFunction,
      activeProjects, chunkyProjects,
      createModal: modalData(),
      importModal: modalData(),
      projectPath,
      request, awaitingResponse
    };
  },
  computed: {
    archivedProjects() {
      if (!this.projects.dataExists) return [];
      const filteredProjects = this.projects.filter((p) => (p.archived));
      return filteredProjects.sort(this.sortFunction);
    },
    // Requirement:
    // - Show atleast 3 items (forms/datasets) per project (if there are)
    // - if showing only 3 items per project doesn't show 15 items in total across all projects
    //   then show more items until total items shown is close to 15
    // - Don't show closed forms
    maxForms() {
      let limit = 2;
      let formsShown;

      const formCounts = this.projects.map((project) =>
        project.formList.filter((f) => f.state !== 'closed').length);
      const totalForms = sum(formCounts);

      do {
        limit += 1;
        formsShown = sumUnderThreshold(formCounts, limit); // formCounts.reduce((acc, i) => acc + Math.min(i, limit), 0);
      }
      while (formsShown < 15 && formsShown < totalForms);

      return formsShown > 15 && limit > 3 ? limit - 1 : limit;
    },
    maxDatasets() {
      let limit = 2;
      let dsShown;

      const datasetCounts = this.projects.map(p => p.datasetList.length);
      const totalDatasets = sum(datasetCounts);

      do {
        limit += 1;
        dsShown = sumUnderThreshold(datasetCounts, limit); // datasetCounts.reduce((acc, i) => acc + Math.min(i, limit), 0);
      }
      while (dsShown < 15 && dsShown < totalDatasets);

      return dsShown > 15 && limit > 3 ? limit - 1 : limit;
    }
  },
  data(){
    return{
      modalOpen: false,
      sheetData: null,
      showCheckboxes: false,
      isOpenedRight: false,
      selectedUsers: []
    };
  },
  created() {
    this.loadStoredData();
  },
  methods: {
    toggleSelection() {
      this.showCheckboxes = !this.showCheckboxes;
      //this.isOpenedRight = this.showCheckboxes; 
      if (this.showCheckboxes && this.sheetData) {
        //initialize selected property for each row if checkboxes are shown
        this.sheetData.slice(1).forEach(row => {
          row.showSelect = false;
        });
      }
    },
    cancelSelection() {
      this.showCheckboxes = false; // Hide checkboxes on cancel
      this.isOpenedRight = false;
      if (this.sheetData) {
        // Reset selected state for each row
        this.sheetData.slice(1).forEach(row => {
          row.selected = false;
        });
      }
    },
    affectSelection() {
      this.isOpenedRight = true; // Open sidebar on affect
    },
    afterCreate(project) {
      const message = this.$t('alert.create');
      this.$router.push(this.projectPath(project.id))
        .then(() => { this.alert.success(message); });
    },
    afterImport(project) {
      const message = this.$t('alert.import');
      this.$router.push(this.projectPath(project.id))
        .then(() => { this.alert.success(message); });
    },
    openModal() {
      this.modalOpen = true;
    },
    closeModal(){
      this.modalOpen = false;
    },
    handleFileSelected(sheetData){
      this.sheetData = sheetData;
      this.storeData(sheetData);
      this.modalOpen = false; //close the modal after file selection
    },
    formatCell(cell, header) {
      // Check if the header is 'date de declaration' and format the cell if it is
      if (header === 'date de declaration' && typeof cell === 'number') {
        const date = new Date((cell - 25569) * 86400 * 1000);
        return format(date, 'MM/dd/yyyy');
      }
      return cell;
    },
    storeData(data) {
      localStorage.setItem('sheetData', JSON.stringify(data));
    },
    loadStoredData() {
      const storedData = localStorage.getItem('sheetData');
      if (storedData) {
        this.sheetData = JSON.parse(storedData);
      }
    },
    closeSidebar() {
      this.sidebarOpen = false;
    },
    handleConfirmSelection(selectedUsers) {
      console.log('Selected Users:', selectedUsers);
      console.log('Selected Projects:', this.sheetData.filter(row => row.selected));
    },
    
    postAffectation() {
      // Collect selected projects
      const selectedProjects = this.sheetData.slice(1).filter(row => row.selected);

      console.log('selectedprojects:', selectedProjects);
      // Collect selected user IDs
      const selectedUserIds = this.selectedUsers.map(user => user.id);
      console.log('selected users id:', selectedUserIds);

      // Prepare the data to be sent to the server
      const data = {
        projects: selectedProjects,
        users: selectedUserIds
      };

      // Send a POST request to save the selected projects and users
      this.request({
        method: 'POST',
        url: apiPaths.affectations(),
        data: data
      })
      .then(({ data }) => {
        if (data) {
          console.log('successful');
          this.alert.success('Affectation successful');
          // Reset the selection
          this.cancelSelection();
          this.closeSidebar();
        } else {
          console.log('error in request');
          this.alert.error('Affectation failed');
        }
      })
      .catch((error) => {
        console.log('error in request:', error);
        this.alert.error('Affectation failed');
      });
    }
  }
};
</script>
<style scoped lang="scss">

#project-list-archived {
  .project-title {
    font-size: 24px;
    font-weight: 500;
  }
}

#sheet-header {
  
  align-items: flex-start;
  .action-buttons{
    
    align-items: flex-end;
  }
  h2 {
  width:60%;
  font-size: 30px;
  color: #bd006b;
  font-weight: 600;
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  }
  #btn-affect {
    background-color: #009ecc;
    color: #fff;
    border-radius: 2px;
    font-size: 12px;
    padding: 6px 10px 5px;
    display: inline-block;
    margin-bottom: 0;
    font-weight: normal;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    touch-action: manipulation;
    cursor: pointer;
  }
  #btn-affect:hover {
    color: #fff;
    background-color: #0086ad;
    border-color: #204d74;
  }
}
.table-container {
  overflow-x: auto;
  white-space: nowrap;
  max-width: 100%;
  border: 1px solid #ccc;
  margin-top: 20px;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 8px 12px;
  text-align: left;
}
</style>

<i18n lang="json5">
{
  "en": {
    // This header is shown above a section of a page, specificially a list of names of archived projects.
    "archived": "Archived Projects",
    "action": {
      // This is the text of a button that is used to create a new Project.
      "create": "New",
      "import": "Import" 
    },
    "emptyTable": {
      "canCreate": "To get started, create a Project. Projects help you organize your data by grouping related Forms and Users.",
      "cannotCreate": "There are no Projects to show. If you expect to see Projects here, talk to the person who gave you this account. They may need to assign a Project Role for Projects you’re supposed to see."
    },
    "alert": {
      "create": "Your new Project has been successfully created.",
      "import": "blablabla"
    }
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "archived": "Archivované projekty",
    "action": {
      "create": "Nový"
    },
    "emptyTable": {
      "canCreate": "Chcete-li začít, vytvořte projekt. Projekty vám pomohou uspořádat data seskupením souvisejících formulářů a uživatelů.",
      "cannotCreate": "Neexistují žádné projekty, které by bylo možné zobrazit. Pokud očekáváte, že se zde Projekty zobrazí, obraťte se na osobu, která vám tento účet poskytla. Možná bude potřebovat přiřadit Projektovou roli pro Projekty, které byste měli vidět."
    },
    "alert": {
      "create": "Váš nový projekt byl úspěšně vytvořen."
    }
  },
  "de": {
    "archived": "Archivierte Projekte",
    "action": {
      "create": "Neu"
    },
    "emptyTable": {
      "canCreate": "Erstellen Sie zunächst ein Projekt. Projekte helfen Ihnen, Ihre Daten zu organisieren, indem Sie verwandte Formulare und Benutzer gruppieren.",
      "cannotCreate": "Es gibt keine anzuzeigenden Projekte. Wenn Sie erwarten, dass Projekte hier angezeigt werden, sprechen Sie mit der Person, die Ihnen dieses Konto gegeben hat. Sie müssen möglicherweise eine Projektrolle für Projekte zuweisen, die Sie sehen sollen."
    },
    "alert": {
      "create": "Ihr neues Projekt wurde erfolgreich erstellt."
    }
  },
  "es": {
    "archived": "Proyectos archivados",
    "action": {
      "create": "Nuevo"
    },
    "emptyTable": {
      "canCreate": "Para comenzar, cree un Proyecto. Los proyectos lo ayudan a organizar sus datos agrupando formularios y usuarios relacionados.",
      "cannotCreate": "No hay Proyectos para mostrar. Si espera ver Proyectos aquí, hable con la persona que le dio esta cuenta. Es posible que deban asignar un rol de proyecto para los proyectos que se supone que debe ver."
    },
    "alert": {
      "create": "Su proyecto ha sido creado exitosamente"
    }
  },
  "fr": {
    "archived": "Projets archivés",
    "action": {
      "create": "Nouveau"
    },
    "emptyTable": {
      "canCreate": "Pour démarrer, créez un Projet. Les projets vous aident à organiser vos données en regroupant des formulaires et des utilisateurs qui sont liés",
      "cannotCreate": "Il n'y a pas de Projet à afficher. Si vous vous attendiez à en voir ici, contactez la personne qui vous a créé ce compte. Il pourrait devoir vous attribuer un rôle de projet pour les projets que vous êtes censé voir."
    },
    "alert": {
      "create": "Votre nouveau projet a été créé avec succès."
    }
  },
  "id": {
    "archived": "Proyek Terarsip",
    "action": {
      "create": "Baru"
    },
    "alert": {
      "create": "Proyek baru Anda telah sukses dibuat."
    }
  },
  "it": {
    "archived": "Progetti archiviati",
    "action": {
      "create": "Nuovo"
    },
    "emptyTable": {
      "canCreate": "Per iniziare, crea un progetto. I progetti ti aiutano a organizzare i tuoi dati raggruppando i formulari e gli utenti correlati.",
      "cannotCreate": "Non ci sono progetti da mostrare. Se prevedi di vedere Progetti qui, parla con la persona che ti ha fornito questo account. Potrebbe essere necessario assegnare un ruolo di progetto per i progetti che dovresti vedere."
    },
    "alert": {
      "create": "Il tuo nuovo progetto è stato creato con successo."
    }
  },
  "ja": {
    "action": {
      "create": "新規作成"
    },
    "alert": {
      "create": "新規プロジェクトは正しく作成されました。"
    }
  },
  "sw": {
    "archived": "Miradi Iliyohifadhiwa",
    "action": {
      "create": "Mpya"
    },
    "emptyTable": {
      "canCreate": "Ili kuanza, tengeneza Mradi. Miradi hukusaidia kupanga data yako kwa kupanga Fomu na Watumiaji husika.",
      "cannotCreate": "Hakuna Miradi ya kuonyesha. Ikiwa unatarajia kuona Miradi hapa, zungumza na mtu aliyekupa akaunti hii. Huenda wakahitaji kupeana Jukumu la Mradi kwa Miradi unayopaswa kuona."
    },
    "alert": {
      "create": "Mradi wako mpya umeundwa."
    }
  }
}
</i18n>
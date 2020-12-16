<!--
Copyright 2020 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <label id="submission-filters-submitter" class="form-group">
    <select class="form-control" :value="value"
      @change="$emit('input', $event.target.value)">
      <option value="">{{ $t('common.anybody') }}</option>
      <template v-if="submitters != null">
        <option v-for="submitter of submitters" :key="submitter.id"
          :value="submitter.id.toString()">
          {{ submitter.displayName }}
        </option>
      </template>
    </select>
    <span class="form-label">{{ $t('field.submitter') }}</span>
  </label>
</template>

<script>
import { requestData } from '../../../store/modules/request';

export default {
  name: 'SubmissionFiltersSubmitter',
  props: {
    value: {
      type: String,
      required: true
    }
  },
  // The component does not assume that this data will exist when the component
  // is created.
  computed: requestData(['submitters'])
};
</script>

<style lang="scss">
#submission-filters-submitter {
  select { width: 201px; }
}
</style>

<i18n lang="json5">
{
  "en": {
    "field": {
      // This is the text of a form field that shows the names of users who have
      // submitted data.
      "submitter": "Submitted by"
    }
  }
}
</i18n>
<!--
Copyright 2021 ODK Central Developers
See the NOTICE file at the top-level directory of this distribution and at
https://github.com/getodk/central-frontend/blob/master/NOTICE.

This file is part of ODK Central. It is subject to the license terms in
the LICENSE file found in the top-level directory of this distribution and at
https://www.apache.org/licenses/LICENSE-2.0. No part of ODK Central,
including this file, may be copied, modified, propagated, or distributed
except according to the terms contained in the LICENSE file.
-->
<template>
  <table class="table analytics-metrics-table">
    <thead>
      <tr>
        <th>{{ title }}</th>
        <th class="metric-value recent-col">{{ $t('recent') }}</th>
        <th class="metric-value total-col">{{ $t('common.total') }}</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(value, name) in metrics" :key="name">
        <td>{{ $t(`fields.${name}`) }}</td>
        <template v-if="value.recent != null">
          <td class="metric-value">{{ $n(value.recent) }}</td>
          <td class="metric-value">{{ $n(value.total) }}</td>
        </template>
        <template v-else>
          <td colspan="2" class="metric-value">{{ $n(value) }}</td>
        </template>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  name: 'AnalyticsMetricsTable',
  props: {
    title: {
      type: String,
      required: true
    },
    metrics: {
      type: Object,
      required: true
    }
  }
};
</script>

<style lang="scss">
@import '../../assets/scss/mixins';

.analytics-metrics-table {
  .metric-value {
    text-align: right;
  }

  .total-col {
    width: 50px;
  }
}
</style>

<i18n lang="json5">
{
  "en": {
    "recent": "in the past 45 days",
    "fields": {
      "num_admins": "Number of Administrators",
      "num_projects_encryption": "Number of Projects with encryption enabled",
      "num_questions_biggest_form": "Number of questions in biggest Form",
      "num_audit_log_entries": "Number of Audit Log entries",
      "backups_configured": "Backups configured",
      "database_size": "System database size",
      "num_managers": "Number of Project Managers",
      "num_viewers": "Number of Project Viewers",
      "num_data_collectors": "Number of Data Collectors",
      "num_app_users": "Number of App Users",
      "num_device_ids": "Number of device IDs",
      "num_public_access_links": "Number of Public Access Links",
      "num_forms": "Number of Forms",
      "num_forms_with_repeats": "Number of Forms with repeats",
      "num_forms_with_geospatial": "Number of Forms with geodata",
      "num_forms_with_encryption": "Number of Forms with encryption",
      "num_forms_with_audits": "Number of Forms with audits",
      "num_submissions_received": "Number of Submissions - Received",
      "num_submissions_approved": "Number of Submissions - Approved",
      "num_submissions_has_issues": "Number of Submissions - Has Issues",
      "num_submissions_rejected": "Number of Submissions - Rejected",
      "num_submissions_edited": "Number of Submissions - Edited",
      "num_submissions_with_edits": "Number of Submissions with edits",
      "num_submissions_with_comments": "Number of Submissions with comments",
      "num_submissions_from_app_users": "Number of Submissions from App Users",
      "num_submissions_from_public_links": "Number of Submissions from Public Links",
      "num_submissions_from_web_users": "Number of Submissions from Web Users"
    }
  }
}
</i18n>

<!-- Autogenerated by destructure.js -->
<i18n>
{
  "cs": {
    "recent": "v posledních 45 dnech",
    "fields": {
      "num_admins": "Počet správců",
      "num_projects_encryption": "Počet projektů s povoleným šifrováním",
      "num_questions_biggest_form": "Počet otázek v největším formuláři",
      "num_audit_log_entries": "Počet záznamů v protokolu auditů",
      "backups_configured": "Konfigurace záloh",
      "database_size": "Velikost systémové databáze",
      "num_managers": "Počet projektových manažerů",
      "num_viewers": "Počet diváků projektu",
      "num_data_collectors": "Počet sběračů dat",
      "num_app_users": "Počet uživatelů aplikace",
      "num_device_ids": "Počet ID zařízení",
      "num_public_access_links": "Počet veřejně přístupných odkazů",
      "num_forms": "Počet formulářů",
      "num_forms_with_repeats": "Počet formulářů s opakováním",
      "num_forms_with_geospatial": "Počet formulářů s geodaty",
      "num_forms_with_encryption": "Počet formulářů se šifrováním",
      "num_forms_with_audits": "Počet formulářů s audity",
      "num_submissions_received": "Počet přijatých podání",
      "num_submissions_approved": "Počet podání - schváleno",
      "num_submissions_has_issues": "Počet podání - Má problémy",
      "num_submissions_rejected": "Počet podání - Zamítnuto",
      "num_submissions_edited": "Počet příspěvků - Upraveno",
      "num_submissions_with_edits": "Počet podání s úpravami",
      "num_submissions_with_comments": "Počet podání s komentáři",
      "num_submissions_from_app_users": "Počet podání od uživatelů aplikace",
      "num_submissions_from_public_links": "Počet podání z veřejných odkazů",
      "num_submissions_from_web_users": "Počet podání od uživatelů webu"
    }
  },
  "es": {
    "recent": "en los últimos 45 días",
    "fields": {
      "num_admins": "Número de Administradores",
      "num_projects_encryption": "Número de proyectos con cifrado habilitado",
      "num_questions_biggest_form": "Número de preguntas en el Formulario más grande",
      "num_audit_log_entries": "Número de entradas del registro de auditoría",
      "backups_configured": "Copias de seguridad configuradas",
      "database_size": "Tamaño de la base de datos del sistema",
      "num_managers": "Número de jefes de proyecto",
      "num_viewers": "Número de observadores del proyecto",
      "num_data_collectors": "Número de recopiladores de datos",
      "num_app_users": "Número de usuarios móviles",
      "num_device_ids": "Número de ID de dispositivo",
      "num_public_access_links": "Número de enlaces de acceso público",
      "num_forms": "Número de formularios",
      "num_forms_with_repeats": "Número de formularios con repeticiones",
      "num_forms_with_geospatial": "Número de formularios con geodatos",
      "num_forms_with_encryption": "Número de formularios con cifrado",
      "num_forms_with_audits": "Número de formularios con auditorías",
      "num_submissions_received": "Número de envíos - Recibidos",
      "num_submissions_approved": "Número de envíos - Aprobados",
      "num_submissions_has_issues": "Número de envíos - Con problemas",
      "num_submissions_rejected": "Número de envíos - Rechazados",
      "num_submissions_edited": "Número de envíos - Con ediciones",
      "num_submissions_with_edits": "Número de envíos con ediciones",
      "num_submissions_with_comments": "Número de envíos con comentarios",
      "num_submissions_from_app_users": "Número de envíos de Usuarios móviles",
      "num_submissions_from_public_links": "Número de envíos de enlaces públicos",
      "num_submissions_from_web_users": "Número de envíos de usuarios web"
    }
  },
  "fr": {
    "recent": "au cours des 45 derniers jours",
    "fields": {
      "num_admins": "Nombre d'administrateurs",
      "num_projects_encryption": "Nombre de projets avec le chiffrement activé",
      "num_questions_biggest_form": "Nombre de question dans le plus gros formulaire",
      "num_audit_log_entries": "Nombre d'entrées dans le log d'audit",
      "backups_configured": "Sauvegardes configurées",
      "database_size": "Taille de la base de données du système",
      "num_managers": "Nombre de gestionnaires de projet",
      "num_viewers": "Nombre de lecteurs de projet",
      "num_data_collectors": "Nombre de collecteurs de données",
      "num_app_users": "Nombre d'utilisateurs mobiles",
      "num_device_ids": "Nombre d'identifiants d'appareils",
      "num_public_access_links": "Nombre de liens d'accès public",
      "num_forms": "Nombre de formulaires",
      "num_forms_with_repeats": "Nombre de formulaires avec répétitions",
      "num_forms_with_geospatial": "Nombre de formulaires avec données géographiques",
      "num_forms_with_encryption": "Nombre de formulaires avec chiffrement",
      "num_forms_with_audits": "Nombre de formulaires avec audits",
      "num_submissions_received": "Nombre de soumissions - Reçues",
      "num_submissions_approved": "Nombre de soumissions - Approuvées",
      "num_submissions_has_issues": "Nombre de soumissions - Avec problèmes",
      "num_submissions_rejected": "Nombre de soumissions - Rejetées",
      "num_submissions_edited": "Nombre de soumissions - Editées",
      "num_submissions_with_edits": "Nombre de soumissions avec éditions",
      "num_submissions_with_comments": "Nombre de soumissions avec commentaires",
      "num_submissions_from_app_users": "Nombre de soumissions depuis les utilisateurs d'application",
      "num_submissions_from_public_links": "Nombre de soumissions depuis les liens d'accès public",
      "num_submissions_from_web_users": "Nombre de soumissions par des utilisateurs web"
    }
  },
  "id": {
    "fields": {
      "database_size": "Ukuran database sistem",
      "num_app_users": "Jumlah Pengguna Aplikasi",
      "num_forms": "Jumlah Formulir",
      "num_forms_with_repeats": "Jumlah Formulir dengan ulangan",
      "num_forms_with_geospatial": "Jumlah Formulir dengan geodata",
      "num_forms_with_encryption": "Jumlah Formulir dengan enkripsi",
      "num_forms_with_audits": "Jumlah Formulir dengan audit",
      "num_submissions_edited": "Jumlah Kiriman - Diubah",
      "num_submissions_with_edits": "Jumlah Kiriman yang diubah",
      "num_submissions_from_app_users": "Jumlah Kiriman dari Pengguna Aplikasi",
      "num_submissions_from_web_users": "Jumlah Kiriman dari Pengguna Web"
    }
  },
  "it": {
    "recent": "Negli ultimi 45 giorni",
    "fields": {
      "num_admins": "Numero di Amministratori",
      "num_projects_encryption": "Numero di progetti con crittografia abilitata",
      "num_questions_biggest_form": "Numero di domande nel questionario più grande",
      "num_audit_log_entries": "Numero di voci del registro di controllo",
      "backups_configured": "Backups configurati.",
      "database_size": "System database size",
      "num_managers": "Numero di responsabili del progetto",
      "num_viewers": "Numero di visualizzatori del progetto",
      "num_data_collectors": "Numero di raccoglitori di dati",
      "num_app_users": "Numero di utenti dell'applicazione",
      "num_device_ids": "Numero di ID dispositivi",
      "num_public_access_links": "Numero di link con accesso pubblico",
      "num_forms": "Numero di formulari",
      "num_forms_with_repeats": "Numero di formulari con repeats all'interno",
      "num_forms_with_geospatial": "Numero di formulari con geodati",
      "num_forms_with_encryption": "Numero di formulari con crittografia abilitata",
      "num_forms_with_audits": "Numero di formulari con audit",
      "num_submissions_received": "Numero di invii - Ricevuti",
      "num_submissions_approved": "Numero di invii - Approvati",
      "num_submissions_has_issues": "Numero di invii - Con problemi",
      "num_submissions_rejected": "Numero di invii - Scartati",
      "num_submissions_edited": "Numero di invii - Modificati",
      "num_submissions_with_edits": "Numero di invii con modifiche",
      "num_submissions_with_comments": "Numero di invii con commenti",
      "num_submissions_from_app_users": "Numero di invii ricevuti da utenti applicazione",
      "num_submissions_from_public_links": "Numero di invii ricevuti da link pubblici",
      "num_submissions_from_web_users": "Numero di invii ricevuti da utenti web"
    }
  },
  "ja": {
    "recent": "過去45日間",
    "fields": {
      "num_admins": "管理者数",
      "num_projects_encryption": "暗号化が有効化されたプロジェクト数",
      "num_questions_biggest_form": "最も大きなフォームの質問数",
      "num_audit_log_entries": "監査ログ数",
      "backups_configured": "設定されたバックアップ",
      "database_size": "システムのデータベースサイズ",
      "num_managers": "プロジェクト・マネージャー数",
      "num_viewers": "プロジェクト・閲覧者数",
      "num_data_collectors": "データ収集者数",
      "num_app_users": "アプリユーザー数",
      "num_device_ids": "デバイスID数",
      "num_public_access_links": "一般公開リンク数",
      "num_forms": "フォーム数",
      "num_forms_with_repeats": "リピートを有するフォーム数",
      "num_forms_with_geospatial": "ジオデータを有するフォーム数",
      "num_forms_with_encryption": "暗号化されたフォーム数",
      "num_forms_with_audits": "監査を受けたフォーム数",
      "num_submissions_received": "提出済みフォーム数 ー 受信済み",
      "num_submissions_approved": "提出済みフォーム数 ー 承認済み",
      "num_submissions_has_issues": "提出済みフォーム数 ー 問題有り",
      "num_submissions_rejected": "提出済みフォーム数 ー リジェクト済み",
      "num_submissions_edited": "提出済みフォーム数 ー 編集済み",
      "num_submissions_with_edits": "編集された提出済みフォーム数",
      "num_submissions_with_comments": "コメントのある提出済みフォーム数",
      "num_submissions_from_app_users": "アプリユーザーから提出されたフォーム数",
      "num_submissions_from_public_links": "一般公開リンクから提出されたフォーム数",
      "num_submissions_from_web_users": "Webユーザーから提出されたフォーム数"
    }
  }
}
</i18n>
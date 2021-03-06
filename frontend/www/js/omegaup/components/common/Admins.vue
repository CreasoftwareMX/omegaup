<template>
  <div class="panel panel-primary">
    <div class="panel-body">
      <form class="form" v-on:submit.prevent="onSubmit">
        <div class="form-group">
          <label
            >{{ T.wordsAdmin }}
            <span
              aria-hidden="true"
              class="glyphicon glyphicon-info-sign"
              data-placement="top"
              data-toggle="tooltip"
              v-bind:title="T.courseEditAddAdminsTooltip"
            ></span>
            <omegaup-autocomplete
              class="form-control"
              v-bind:init="el => typeahead.userTypeahead(el)"
              v-model="username"
            ></omegaup-autocomplete>
          </label>
        </div>
        <div class="row">
          <div class="action-container col-md-6">
            <button class="btn btn-primary" type="submit">
              {{ T.wordsAddAdmin }}
            </button>
          </div>
          <div class="toggle-container col-md-6">
            <label>
              <input
                type="checkbox"
                name="toggle-site-admins"
                v-model="showSiteAdmins"
              />
              {{ T.wordsShowSiteAdmins }}
            </label>
          </div>
        </div>
      </form>
    </div>
    <div v-if="admins.length === 0">
      <div class="empty-category">
        {{ T.courseEditAdminsEmpty }}
      </div>
    </div>
    <table class="table table-striped" v-else="">
      <thead>
        <tr>
          <th>{{ T.contestEditRegisteredAdminUsername }}</th>
          <th>{{ T.contestEditRegisteredAdminRole }}</th>
          <th>{{ T.contestEditRegisteredAdminDelete }}</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="admin in admins"
          v-if="admin.role !== 'site-admin' || showSiteAdmins"
        >
          <td>
            <omegaup-user-username
              v-bind:linkify="true"
              v-bind:username="admin.username"
            ></omegaup-user-username>
          </td>
          <td>{{ admin.role }}</td>
          <td>
            <button
              type="button"
              class="close"
              v-if="admin.role === 'admin'"
              v-on:click="onRemove(admin)"
            >
              &times;
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop, Watch } from 'vue-property-decorator';
import { omegaup } from '../../omegaup';
import T from '../../lang';
import * as typeahead from '../../typeahead';
import Autocomplete from '../Autocomplete.vue';
import user_Username from '../user/Username.vue';

@Component({
  components: {
    'omegaup-autocomplete': Autocomplete,
    'omegaup-user-username': user_Username,
  },
})
export default class Admins extends Vue {
  @Prop() initialAdmins!: omegaup.UserRole[];
  @Prop({ default: false }) hasParentComponent!: boolean;

  T = T;
  typeahead = typeahead;
  username = '';
  showSiteAdmins = false;
  selected = {};
  admins = this.initialAdmins;

  @Watch('initialAdmins')
  onAdminsChanged(newValue: omegaup.UserRole[]): void {
    this.admins = newValue;
  }

  onSubmit(): void {
    if (this.hasParentComponent) {
      this.$emit('emit-add-admin', this);
      return;
    }
    this.$emit('add-admin', this.username);
  }

  onRemove(admin: omegaup.UserRole): void {
    if (this.hasParentComponent) {
      this.selected = admin;
      this.$emit('emit-remove-admin', this);
      return;
    }
    this.$emit('remove-admin', admin.username);
  }
}
</script>

<template>
  <select
    :value="value"
    class="selectpicker"
    :title="title"
    data-live-search="true"
    data-width="100%"
    @change="select($event)"
  >
    <option v-for="item in characters" :key="item.name" :data-tokens="getTokens(item)">
      {{ item.name }}
    </option>
  </select>
</template>

<script lang="ts">
import 'bootstrap-select';
import $ from 'jquery';
import Vue from 'vue';
import { cn } from '../assets/js/cn.characters';
import { en } from '../assets/js/en.characters';
import { fr } from '../assets/js/fr.characters';
import { nicknames } from '../assets/js/nicknames';
import { tw } from '../assets/js/tw.characters';

export default Vue.extend({
  props: ['title', 'value'],
  data() {
    return {
      characters: en,
      nicknames,
    };
  },
  created() {
    this.characters = this.getItems();
  },
  watch: {
    title(): void {
      this.characters = this.getItems();
    },
  },
  updated() {
    $(this.$el).selectpicker({ title: this.title }).selectpicker('render');
  },
  methods: {
    select($event: Event): void {
      this.$emit('input', ($event.target as HTMLInputElement).value);
    },
    /* eslint-disable  @typescript-eslint/no-explicit-any */
    getItems(): Array<any> {
      let characters = null;
      switch (this.$i18n.locale) {
        case 'en':
          characters = en;
          break;
        case 'fr':
          characters = this.uniqueMerge([en, fr]);
          break;
        case 'cn':
          characters = this.uniqueMerge([en, cn]);
          break;
        case 'tw':
          characters = this.uniqueMerge([en, tw]);
          break;
        default:
          characters = en;
          break;
      }
      return characters;
    },
    uniqueMerge(arrays: Array<any>) {
      const results = {};
      arrays.forEach((arr) => {
        arr.forEach((item: { _id: string; name: string }) => {
          results[item._id] = item;
        });
      });
      return Object.values(results);
    },
    getTokens(item: { _id: string }): string {
      if (item._id && nicknames[item._id]) {
        return nicknames[item._id];
      }
      return '';
    },
  },
});
</script>

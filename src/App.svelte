<script lang="ts">
  import type {Question} from './types';
  import Downloader from './Downloader.svelte';
  import Uploader from './Uploader.svelte';
  import Viewer from './Viewer.svelte';
  import localForage from 'localforage';

  localForage.config({
    driver: localForage.INDEXEDDB,
    name: 'view-ysb'
  })

  let questions: Question[] = [];

  window['printQuestions'] = () => console.log(questions);
  window['printQuestion'] = (index) => console.log(questions[index-1]);
</script>

<main class="container">
  <h3>YSB Applications Viewing Tool</h3>
  <Uploader bind:questions />
  <Viewer bind:questions />
  <Downloader bind:questions />
</main>

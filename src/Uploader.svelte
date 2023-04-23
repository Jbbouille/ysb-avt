<script lang="ts">
  import {onMount} from 'svelte';
  import {read, utils} from 'xlsx';
  import type {Question} from './types';
  import localForage from 'localforage';

  export let questions: Question[] = [];
  let openUpload = true;

  onMount(async () => {
    const input = document.getElementById('questioner');
    let localQuestions = await localForage.getItem('questions');
    if (localQuestions) {
      questions = JSON.parse(localQuestions);
      openUpload = false;
    }
    input.onchange = e => {
      const file = (e.target as HTMLInputElement).files[0];
      const reader = new FileReader();
      reader.readAsArrayBuffer(file);
      reader.onload = async readerEvent => {
        questions = [];
        const content = readerEvent.target.result;
        const wb = read(content, {dateNF: 'dd/mm/yyyy hh:mm:ss', cellDates: true});
        questions = utils.sheet_to_json(wb.Sheets[wb.SheetNames[0]], {dateNF: 'dd/mm/yyyy hh:mm:ss', defval: ''});
        await localForage.setItem('questions', JSON.stringify(questions));
        await localForage.setItem('index', '0');
      };
    };
  });
</script>

<details open={openUpload || null}>
  <summary>Fichier</summary>
  <input type="file" id="questioner" name="questions" accept=".xlsx,.xls">
</details>

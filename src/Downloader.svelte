<script lang="ts">
  import {utils, writeFile} from 'xlsx';
  import type {Question} from './types';

  export let questions: Question[] = [];

  function downloadFile() {
    const data = questions.map(q => {
      q['totalNotationIndividual'] = Object.keys(q).reduce((acc, prev) => {
        if (prev.endsWith('-ysb-note')) {
          return acc + q[prev];
        }
        return acc;
      }, 0);
      q['totalNotationOrganisation'] = Object.keys(q).reduce((acc, prev) => {
        if (prev.endsWith('-ysb-org-note')) {
          return acc + q[prev];
        }
        return acc;
      }, 0);
      return q;
    });
    const workSheet = utils.json_to_sheet<Question>(data, {cellDates: true, dateNF: 'dd/mm/yyyy hh:mm:ss'});
    writeFile({Sheets: {'Content': workSheet}, bookType: 'xlsx', SheetNames: ['Content']}, 'out.xlsx');
  }
</script>

<style>
    [data-theme="green"] {
        --primary: #43a047;
        --primary-hover: #388e3c;
        --primary-focus: rgba(67, 160, 71, 0.125);
        --primary-inverse: #FFF;
    }
</style>

<div>
  <h4>End</h4>
  <button on:click={downloadFile} disabled={!questions || !questions.length ? 'disabled' : null} data-theme="green">Download</button>
</div>

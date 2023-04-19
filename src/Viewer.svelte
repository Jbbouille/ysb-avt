<script lang="ts">
  import type {Question} from './types';
  import localForage from 'localforage';
  import {onMount} from 'svelte';
  import {isStopWord} from './service';

  const individual = [
    {name: 'E-1 What is your experience in youth participation and empowerment at local, national, international level?', maxValue: 12},
    {
      name: 'E-2 What is your experience in reaching out to large groups of youth, including to youth that are in disadvantaged or vulnerable situations?',
      maxValue: 8
    },
    {name: 'M-1 Why do you want to join the Youth Sounding Board?', maxValue: 4},
    {name: 'M-2 How will you contribute to the work of the Youth Sounding Board?', maxValue: 4},
    {
      name: 'M-3 Imagine that you are selected for the Youth Sounding Board and after two years you look back on your work. What concrete result would you like to have achieved?',
      maxValue: 3
    },
    {name: 'M-4 How should the Youth Sounding Board contribute to the implementation of the Youth Action Plan according to you?', maxValue: 1},
    {
      name: 'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Language',
      maxValue: 2
    },
    {
      name: 'C-2 Please describe how you have successfully used your communication skills (e.g. conducting campaigns, verbal and written skills, etc.) in youth participation and engagement processes, including the use of traditional and social media to advocate and negotiate for youth.',
      maxValue: 6
    },
  ];

  const organization = [
    {name: 'O-5 What is your role in your organisation?', maxValue: 2.5},
    {name: 'O-6 Why are you a good representative of your organisation?', maxValue: 2.5},
    {
      name: 'O-7 Please explain briefly how your organisation has contributed to policy processes (e.g. consultation, advocacy etc.), in particular on youth.',
      maxValue: 10
    },
    {
      name: 'O-8 Please give an example of how your organisation has successfully contributed to strengthening youth participation (e.g. campaigns, outreach to vulnerable youth, etc.).',
      maxValue: 10
    },
    {name: 'O-9 Please briefly explain which regions, countries, continents your organisation is covering through its activities.', maxValue: 5},
    {name: 'M-1 How many individual members does your organisation have?', maxValue: 3},
    {name: 'M-2 Please describe the profile of your individual members? (e.g. age range, general background, interest and expertise)', maxValue: 2},
    {name: 'M-3 As a network (or similar), how many organisations are member of your network?', maxValue: 3},
    {
      name: 'M-4 As a network (or similar), please briefly describe the profile of your member organisations / groups (e.g. age range, general background, interest and expertise)',
      maxValue: 2
    },
  ];

  export let questions: Question[] = [];
  $: index = 0;
  $: current = questions[index];
  $: isIndividual = current?.['Which option are you applying for?'] === 'Option A : I am applying as an individual.';

  const listToCheckSize = [
    'O-7 Please explain briefly how your organisation has contributed to policy processes (e.g. consultation, advocacy etc.), in particular on youth.',
    'O-8 Please give an example of how your organisation has successfully contributed to strengthening youth participation (e.g. campaigns, outreach to vulnerable youth, etc.).',
    'C-2 Please describe how you have successfully used your communication skills (e.g. conducting campaigns, verbal and written skills, etc.) in youth participation and engagement processes, including the use of traditional and social media to advocate and negotiate for youth.',
    'M-1 Why do you want to join the Youth Sounding Board?',
    'M-2 How will you contribute to the work of the Youth Sounding Board?',
    'M-3 Imagine that you are selected for the Youth Sounding Board and after two years you look back on your work. What concrete result would you like to have achieved?',
  ];

  onMount(async () => {
    const savedIndex = await localForage.getItem('index');
    if (savedIndex) {
      index = Number(savedIndex);
    }
  })

  function calculateAge(birthday: Date | string): number {
    const ageDifMs = Date.now() - new Date(birthday);
    const ageDate = new Date(ageDifMs);
    return Math.abs(ageDate.getUTCFullYear() - 1970);
  }

  async function saveQuestions() {
    await localForage.setItem('questions', JSON.stringify(questions));
  }

  async function moveForward() {
    if (index === questions.length) {
      return;
    }
    index++;
    await localForage.setItem('index', `${index}`);
  }

  async function moveBackwards() {
    if (index === 0) {
      return;
    }
    index--;
    await localForage.setItem('index', `${index}`);
  }

  function getNumberOfWords(key: string, _: Question): number | null {
    if (!listToCheckSize.includes(key) || !current?.[key]) {
      return null;
    }
    const size = current[key].split(/\W+/).filter(w => !isStopWord(w)).length;
    if (size > 15) {
      return null;
    }
    return size;
  }

  function getTotalNotation(_: Question, isIndividual: boolean): Number {
    if (!current) {
      return 0;
    }
    const notationKeyEnd = isIndividual ? '-ysb-note' : '-ysb-org-note';
    return Object.keys(current).reduce((acc, prev) => {
      if (prev.endsWith(notationKeyEnd)) {
        return acc + current[prev];
      }
      return acc;
    }, 0);
  }

</script>

<style>
    .grow {
        flex: 1;
    }

    .small {
        max-width: 7rem;
    }

    .extra-small {
        max-width: 4rem;
    }

    .line {
        display: flex;
        justify-content: space-evenly;
    }

    .green {
        color: #2e7d32 !important;
    }

    .red {
        color: #b71c1c !important;
    }

    .warning {
        color: coral !important;
    }

    .description {
        color: #1e88e5;
    }
</style>

<div class="grow">
  {#if questions && questions.length}
    <span>{index + 1} / {questions.length}</span>
    <progress value={index+1} max={questions.length}></progress>
    <div class="line">
      <button class="extra-small" on:click={moveBackwards} disabled={index === 0 ? 'disabled' : null}>
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M19.5 12h-15m0 0l6.75 6.75M4.5 12l6.75-6.75"/>
        </svg>
      </button>
      <button class="extra-small" on:click={moveForward} disabled={index === questions.length ? 'disabled' : null}>
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
          <path stroke-linecap="round" stroke-linejoin="round" d="M4.5 12h15m0 0l-6.75-6.75M19.5 12l-6.75 6.75"/>
        </svg>
      </button>
    </div>
    <article>
      <div>
        <span class="description">Applying: </span>
        {#if isIndividual}
          <span>Individual</span>
        {:else}
          <span class="red"><b>Youth Organisation</b></span>
        {/if}
      </div>
      <div>
        <div>
          <span class="description">Education: </span>
          <span>{current?.['What is the highest education level you have completed?']}</span>
        </div>
        <div>
          <span class="description">Organisation: </span>
          <span>{current?.['What is the name of your organisation?']}</span>
        </div>
        <div>
          <span class="description">Citizenship: </span>
          <span>{current?.['What is your citizenship?']}</span>
        </div>
        <div>
          <span class="description">Occupation: </span>
          <span>{current?.['What is your current occupation?']}</span>
        </div>
        <div>
          <span class="description">Place of birth: </span>
          <span>{current?.['What is your place of birth?']}</span>
        </div>
        <div>
          <span class="description">City born: </span>
          <span>{current?.['Please mention the city where you were born.']}</span>
        </div>
        <div>
          <span class="description">Living in: </span>
          <span>{current?.['Which country are you currently living in?']}</span>
        </div>
        <div>
          <span class="description">Diversity: </span>
          <span>{current?.['I identify as a person with:']}</span>
        </div>
        <div>
          <span class="description">Other orgs: </span>
          <span>{current?.['Please list all the Civil Society Organisations and other relevant organisations/programmes (e.g. UNICEF, etc.) that you have been involved in.']}</span>
        </div>
        <div>
          <span class="description">Expertises: </span>
          <span>{current?.['What are your areas of expertise in the frame of working with young people?']}</span>
        </div>
        <div>
          <span class="description">Details: </span>
          <span>{current?.['Please describe briefly your interest and/or expertise:']}</span>
        </div>
        <div>
          <span class="description">Interests: </span>
          <span>{current?.['What are your areas of interest when it comes to working with young people?']}</span>
        </div>
        <div>
          <span class="description">Areas interests: </span>
          <span>{current?.['Please describe briefly your areas of interest:']}</span>
        </div>
        <div>
          <span class="description">Age: </span>
          {#if current?.['Please indicate your birthday*:']}
            <span>{calculateAge(current['Please indicate your birthday*:'])}</span>
          {/if}
        </div>
      </div>
      <hr>
      {#if isIndividual}
        <div>Total notation individual: {getTotalNotation(current, true)}</div>
      {:else}
        <div>Total notation individual: {getTotalNotation(current, true)}</div>
        <div>Total notation organisation: {getTotalNotation(current, false)}</div>
      {/if}
    </article>
    <article>
      {#each individual as questionToRank}
        {#if current?.[questionToRank.name]}
          {#if questionToRank.name === 'C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Language'}
            <details open>
              <summary
                  class={current[`${questionToRank.name}-ysb-note`] ? current[`${questionToRank.name}-ysb-note`] > questionToRank.maxValue ? 'red' : 'green' : ''}>
                {questionToRank.name} {current[`${questionToRank.name}-ysb-note`] ? current[`${questionToRank.name}-ysb-note`] : ''}
              </summary>
              <div class="grid">
                {#if current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Language']}
                  <div>
            <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Language']}
              -</span>
                    <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 1:Level (A1, A2, B1, B2, C1, C2)']}</span>
                  </div>
                {/if}
                {#if current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Language']}
                  <div>
            <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Language']}
              -</span>
                    <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 2:Level (A1, A2, B1, B2, C1, C2)']}</span>
                  </div>
                {/if}
                {#if current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Language']}
                  <div>
            <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Language']}
              -</span>
                    <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 3:Level (A1, A2, B1, B2, C1, C2)']}</span>
                  </div>
                {/if}
                {#if current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Language']}
                  <div>
            <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Language']}
              -</span>
                    <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 4:Level (A1, A2, B1, B2, C1, C2)']}</span>
                  </div>
                {/if}
                {#if current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Language']}
                  <div>
            <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Language']}
              -</span>
                    <span>{current['C-1 Please rate your language skills in ANY OTHER LANGUAGE than English: (according to the language self-assessment grid) Language 5:Level (A1, A2, B1, B2, C1, C2)']}</span>
                  </div>
                {/if}
              </div>
              <input class="small" type="number" max={questionToRank.maxValue} min="0" bind:value={current[`${questionToRank.name}-ysb-note`]}
                     on:change={saveQuestions}
                     aria-invalid={current[`${questionToRank.name}-ysb-note`] != null ? current[`${questionToRank.name}-ysb-note`] > questionToRank.maxValue : null}>
            </details>
          {:else}
            <details open>
              <summary
                  class={current[`${questionToRank.name}-ysb-note`] ? current[`${questionToRank.name}-ysb-note`] > questionToRank.maxValue ? 'red' : 'green' : ''}>
                {questionToRank.name} {current[`${questionToRank.name}-ysb-note`] ? current[`${questionToRank.name}-ysb-note`] : ''}
              </summary>
              <p class={getNumberOfWords(questionToRank.name, current) ? 'warning': ''}
                 data-tooltip={getNumberOfWords(questionToRank.name, current) ? `Text contains few words.`: null}>{current[questionToRank.name]}</p>
              <input class="small" type="number" max={questionToRank.maxValue} min="0" bind:value={current[`${questionToRank.name}-ysb-note`]}
                     on:change={saveQuestions}
                     aria-invalid={current[`${questionToRank.name}-ysb-note`] != null ? current[`${questionToRank.name}-ysb-note`] > questionToRank.maxValue : null}>
            </details>
          {/if}
        {/if}
      {/each}
    </article>
    {#if !isIndividual}
      <article>
        <div>
          <div>
            <span class="description">Organisation name: </span>
            <span>{current?.['O-1 Information about the nominating organisation Name of the organisation::Please indicate below the requested information']}</span>
          </div>
          <div>
            <span class="description">Contact details: </span>
            <span>{current?.['O-1 Information about the nominating organisation Contact details: (Email):Please indicate below the requested information']}</span>
          </div>
          <div>
            <span class="description">Website: </span>
            <span>{current?.['O-1 Information about the nominating organisation Website: (if available):Please indicate below the requested information']}</span>
          </div>
          <div>
            <span class="description">Social media: </span>
            <span>{current?.['O-1 Information about the nominating organisation Social Media: (if available):Please indicate below the requested information']}</span>
          </div>
          <div>
            <span class="description">Category: </span>
            <span>{current?.['O-2 Which category fits best to your nominating organisation?	You selected Other, please specify:']}</span>
          </div>
          <div>
            <span class="description">Thematic: </span>
            <span>{current?.['O-3 Please indicate briefly the thematic areas your organisation is focusing on:']}</span>
          </div>
          <div>
            <span class="description">Expertises: </span>
            <span>{current?.['O-4 Please describe briefly the area(s) of expertise of your organisation/network.']}</span>
          </div>
        </div>
        <hr>
        <div>Total notation organisation: {getTotalNotation(current, false)}</div>
      </article>
      <article>
        {#each organization as questionToRank}
          {#if current?.[questionToRank.name]}
            <details open>
              <summary
                  class={current[`${questionToRank.name}-ysb-org-note`] ? current[`${questionToRank.name}-ysb-org-note`] > questionToRank.maxValue ? 'red' : 'green' : ''}>
                {questionToRank.name} {current[`${questionToRank.name}-ysb-org-note`] ? current[`${questionToRank.name}-ysb-org-note`] : ''}
              </summary>
              <p class={getNumberOfWords(questionToRank.name, current) ? 'warning': ''}
                 data-tooltip={getNumberOfWords(questionToRank.name, current) ? `Text contains few words.`: null}>{current[questionToRank.name]}</p>
              <input class="small" type="number" max={questionToRank.maxValue} min="0" bind:value={current[`${questionToRank.name}-ysb-org-note`]}
                     on:change={saveQuestions}
                     aria-invalid={current[`${questionToRank.name}-ysb-org-note`] != null ? current[`${questionToRank.name}-ysb-org-note`] > questionToRank.maxValue : null}>
            </details>
          {/if}
        {/each}
      </article>
    {/if}
  {/if}
</div>

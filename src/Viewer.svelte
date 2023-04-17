<script lang="ts">
  import type {Question} from './types';
  import localForage from 'localforage';
  import {onMount} from 'svelte';

  export let questions: Question[] = [];
  let index = 0;
  $: current = questions[index];
  const listQuestionToRank = [
    'C-2 Please describe how you have successfully used your communication skills (e.g. conducting campaigns, verbal and written skills, etc.) in youth participation and engagement processes, including the use of traditional and social media to advocate and negotiate for youth.',
    'E-1 What is your experience in youth participation and empowerment at local, national, international level?',
    'E-2 What is your experience in reaching out to large groups of youth, including to youth that are in disadvantaged or vulnerable situations?',
    'I identify as a person with:',
    'If YES, please specify:',
    'If you selected other, please specify:',
    'If you selected other, please specify:_1',
    'If you selected other, please specify:_2',
    'In order to ensure diversity and inclusion within the Youth Sounding Board, we would like to ask you whether you identify as a person with disability, special needs, a member of a minority group, with special needs and/or in a situation of vulnerability. Providing this information is not mandatory. Please note that not providing this information will not prevent a candidate from applying.',
    'M-1 How many individual members does your organisation have?',
    'M-1 Why do you want to join the Youth Sounding Board?',
    'M-2 How will you contribute to the work of the Youth Sounding Board?',
    'M-2 Please describe the profile of your individual members? (e.g. age range, general background, interest and expertise)',
    'M-3 As a network (or similar), how many organisations are member of your network?',
    'M-3 Imagine that you are selected for the Youth Sounding Board and after two years you look back on your work. What concrete result would you like to have achieved?',
    'M-4 As a network (or similar), please briefly describe the profile of your member organisations / groups (e.g. age range, general background, interest and expertise)',
    'M-4 How should the Youth Sounding Board contribute to the implementation of the Youth Action Plan according to you?',
    'M-5 Is your organisation member of a larger network?',
    'O-1 Information about the nominating organisation Contact details: (Email):Please indicate below the requested information',
    'O-1 Information about the nominating organisation Name of the organisation::Please indicate below the requested information',
    'O-1 Information about the nominating organisation Social Media: (if available):Please indicate below the requested information',
    'O-1 Information about the nominating organisation Website: (if available):Please indicate below the requested information',
    'O-2 Which category fits best to your nominating organisation?',
    'O-3 Please indicate briefly the thematic areas your organisation is focusing on:',
    'O-4 Please describe briefly the area(s) of expertise of your organisation/network.',
    'O-5 What is your role in your organisation?',
    'O-6 Why are you a good representative of your organisation?',
    'O-7 Please explain briefly how your organisation has contributed to policy processes (e.g. consultation, advocacy etc.), in particular on youth.',
    'O-8 Please give an example of how your organisation has successfully contributed to strengthening youth participation (e.g. campaigns, outreach to vulnerable youth, etc.).',
    'O-9 Please briefly explain which regions, countries, continents your organisation is covering through its activities.',
    'OPTIONAL - Is there anything you would like to add to your application?',
    'OPTIONAL - You have chosen "other". Please specify:',
    'Please describe briefly your areas of interest:',
    'Please describe briefly your interest and/or expertise:',
    'Please list all the Civil Society Organisations and other relevant organisations/programmes (e.g. UNICEF, etc.) that you have been involved in.',
    'Please upload your signed Nomination Letter (pdf-format) here:',
    'What are your areas of expertise in the frame of working with young people?',
    'What are your areas of interest when it comes to working with young people?',
    'You have chosen "other". Please specify:',
    'You have chosen "other". Please specify:_1',
    'You have chosen Other. Please specify the type of organisation:',
    'You selected Other, please specify:',
  ];

  onMount(async () => {
    const savedIndex = await localForage.getItem('index');
    if (savedIndex) {
      index = Number(savedIndex);
    }
  })

  function calculateAge(birthday: Date): number {
    const ageDifMs = Date.now() - birthday;
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
        <span>Applying: {current['Which option are you applying for?'] === 'Option A : I am applying as an individual.' ? 'individual' : 'youth organisation' }</span>
      </div>
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
      <div>
        <div>
          <span>Education: </span>
          <span>{current['What is the highest education level you have completed?']}</span>
        </div>
        <div>
          <span>Organisation: </span>
          <span>{current['What is the name of your organisation?']}</span>
        </div>
        <div>
          <span>Citizenship: </span>
          <span>{current['What is your citizenship?']}</span>
        </div>
        <div>
          <span>Occupation: </span>
          <span>{current['What is your current occupation?']}</span>
        </div>
        <div>
          <span>Place of birth: </span>
          <span>{current['What is your place of birth?']}</span>
        </div>
        <div>
          <span>City born: </span>
          <span>{current['Please mention the city where you were born.']}</span>
        </div>
        <div>
          <span>Living in: </span>
          <span>{current['Which country are you currently living in?']}</span>
        </div>
        <div>
          <span>Age: </span>
          <span>{calculateAge(current['Please indicate your birthday*:'])}</span>
        </div>
      </div>
    </article>
    <article>
      {#each listQuestionToRank as currentKey}
        {#if current[currentKey]}
          <details open>
            <summary class={current[`note-${currentKey}`] ? current[`note-${currentKey}`] > 10 ? 'red' : 'green' : ''}>
              {currentKey} {current[`note-${currentKey}`] ? current[`note-${currentKey}`] : ''}
            </summary>
            <p>{current[currentKey]}</p>
            <input class="small" type="number" max="10" min="0" bind:value={current[`note-${currentKey}`]} on:change={saveQuestions}
                   aria-invalid={current[`note-${currentKey}`] ? current[`note-${currentKey}`] > 10 : null}>
          </details>
        {/if}
      {/each}
    </article>
  {/if}
</div>

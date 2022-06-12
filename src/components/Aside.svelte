<script>
  export let currentTab;

  const options = ["calls", "contacts/sms"];
</script>

<aside class="bg-slate-100 w-52 pt-5">
  {#each options as option}
    <div
      class="capitalize py-2 px-4 hover:bg-blue-200 cursor-pointer font-medium transition"
      class:bg-blue-300={currentTab == option}
      on:click={() => (currentTab = option)}
    >
      {option}
    </div>
  {/each}
</aside>

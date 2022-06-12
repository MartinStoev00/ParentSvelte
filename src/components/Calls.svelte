<script>
  export let calls;
  export let block;
  export let blockNumber;
  export let unblockNumber;
</script>

<div class="flex flex-col p-5">
  {#if calls}
    <div class="flex justify-evenly content-center px-20">
      <div class="flex-grow flex justify-center items-center">
        <div>graph</div>
      </div>
      <div class="overflow-y-scroll scroller">
        {#each calls as { contact, date, duration, number, type }}
          <div
            class="border-solid border rounded bg-white p-2 my-2 flex justify-between"
          >
            <div class="w-72">
              <div class="flex justify-between">
                <div>
                  <b>{contact}</b> <span class="font-light">{number}</span>
                </div>
                <div>{date}</div>
              </div>
              <div class="flex justify-between">
                <div>
                  <b>Duration:</b> <span class="font-light">{duration}</span>
                </div>
                <div><b>Type:</b> <span class="font-light">{type}</span></div>
              </div>
            </div>
            <div
              class="font-bold flex justify-center items-center px-2 transition w-12 cursor-pointer hover:scale-150"
              on:click={() =>
                block.includes(number)
                  ? unblockNumber(number)
                  : blockNumber(number)}
            >
              {block.includes(number) ? "🔒" : "⛔"}
            </div>
          </div>
        {/each}
      </div>
    </div>
  {:else}
    <div>Loading...</div>
  {/if}
</div>

<style>
  .scroller {
    height: calc(100vh - 110px) !important;
  }
</style>

<script>
  export let contacts;
  export let sms;
  export let block;
  export let blockNumber;
  export let unblockNumber;

  let searchSms = "";
  let searchContact = "";
</script>

<div class="flex justify-evenly items-center h-full">
  {#if contacts && sms && block}
    <div class="mx-2" style="width: 60%;">
      <div class="font-bold text-2xl my-2">SMS</div>
      <div
        class="input-group relative flex flex-wrap items-stretch w-full mb-4"
      >
        <input
          bind:value={searchSms}
          type="search"
          class="form-control relative flex-auto min-w-0 block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none"
          placeholder="Search SMS/Contact"
          aria-label="Search"
          aria-describedby="button-addon2"
        />
      </div>
      <div class="overflow-y-scroll scroller">
        {#each sms.filter(({ body, sender }) => (body + sender)
            .toLowerCase()
            .includes(searchSms.toLocaleLowerCase())) as { body, date, sender, status }}
          <div class="border-solid border rounded bg-white p-2 mb-2">
            <div class="flex flex-col">
              <div class="flex justify-between">
                <div>
                  <b>{sender}</b>
                </div>
                <div>
                  {date}
                </div>
              </div>
              <div>{body}</div>
              <div class="self-end">{status}</div>
            </div>
          </div>
        {:else}
          <div>No Results</div>
        {/each}
      </div>
    </div>
    <div class="">
      <div class="font-bold text-2xl my-2">Contacts</div>
      <div class="flex justify-center">
        <div
          class="input-group relative flex flex-wrap items-stretch w-full mb-4"
        >
          <input
            bind:value={searchContact}
            type="search"
            class="form-control relative flex-auto min-w-0 block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none"
            placeholder="Search Name/Number"
            aria-label="Search"
            aria-describedby="button-addon2"
          />
        </div>
      </div>
      <div class="overflow-y-scroll scroller">
        {#each contacts.filter(({ name, number }) => (name + number)
            .toLowerCase()
            .includes(searchContact.toLocaleLowerCase())) as { name, number }}
          <div
            class="border-solid border rounded bg-white p-2 mb-2 flex justify-between"
          >
            <div class="w-fit">
              <div>
                <b>{name}</b>
              </div>
              <div>
                {number}
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
    Loading...
  {/if}
</div>

<style>
  .scroller {
    height: calc(90vh - 140px) !important;
  }
</style>

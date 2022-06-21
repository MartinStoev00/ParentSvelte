<script>
  import { PieChart } from "@carbon/charts-svelte";

  import "@carbon/styles/css/styles.css";
  import "@carbon/charts/styles.css";

  export let calls;
  export let block;
  export let blockNumber;
  export let unblockNumber;

  let callSearch = "";

  const parseData = (c) => {
    let b = {};
    let d = new Date();
    let curr =
      "-" +
      (d.getMonth() + 1 + "").padStart(2, "0") +
      "-" +
      (d.getFullYear() + "").substring(2);
    c?.forEach(({ date, duration, contact, number }) => {
      if (date.includes(curr)) {
        let values = duration.split(":");
        let seconds =
          parseInt(values[0]) * 3600 +
          parseInt(values[1]) * 60 +
          parseInt(values[2]);
        let owner = contact;
        if (!owner) {
          owner = number;
        }
        if (owner in b) {
          b[owner] += seconds;
        } else {
          b[owner] = seconds;
        }
      }
    });

    return Object.entries(b).map(([group, count]) => ({ group, count }));
  };

  const formatDuration = (secondsTotal) => {
    let hours = (Math.floor(secondsTotal / 3600) + "").padStart(2, "0");
    let minutes = (Math.floor((secondsTotal % 3600) / 60) + "").padStart(
      2,
      "0"
    );
    let seconds = ((secondsTotal % 60) + "").padStart(2, "0");
    return hours + ":" + minutes + ":" + seconds;
  };
</script>

<div class="flex flex-col p-5">
  {#if calls}
    <div class="flex justify-evenly content-center px-20">
      <div class="flex-grow flex justify-center items-center">
        <PieChart
          data={parseData(calls)}
          options={{
            title: "Time Spent Talking to People for This Month",
            resizable: true,
            pie: {
              valueMapsTo: "count",
              alignment: "center",
              labels: {
                formatter: ({ data: { group, count } }) => {
                  return group + " " + formatDuration(count);
                },
              },
            },
            tooltip: {
              valueFormatter: formatDuration,
            },
            height: "400px",
          }}
        />
      </div>
      <div>
        <div class="font-bold text-2xl my-2">Calls</div>
        <div
          class="input-group relative flex flex-wrap items-stretch w-full mb-4"
        >
          <input
            bind:value={callSearch}
            type="search"
            class="form-control relative flex-auto min-w-0 block w-full px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none"
            placeholder="Search Calls"
            aria-label="Search"
            aria-describedby="button-addon2"
          />
        </div>
        <div class="overflow-y-scroll scroller">
          {#each calls.filter(({ contact, number }) => (contact + number)
              .toLowerCase()
              .includes(callSearch.toLocaleLowerCase())) as { contact, date, duration, number, type }}
            <div
              class="border-solid border border-slate-200 transition hover:shadow rounded bg-white p-2 mb-2 flex justify-between"
            >
              <div class="w-72">
                <div class="flex justify-between">
                  <div>
                    <b>{contact || "Anonymous"}</b>
                    <span class="font-light">{number}</span>
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
    </div>
  {:else}
    <div>Loading...</div>
  {/if}
</div>

<style>
  .scroller {
    height: calc(90vh - 140px) !important;
  }
</style>

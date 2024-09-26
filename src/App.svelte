<script>
  import { onMount } from "svelte";
  let data = [];
  let ld = [];
  let osztList = new Set();
  let olt = [];
  let t = {};
  let selectedOsztály;
  let [mino, maxo] = [20, 0];
  const napok = ["H", "K", "Sz", "Cs", "P"];

  onMount(async () => {
    data = (await fetch("https://tomuwhu.github.io/orarend_generator_svelte/orarend.json").then(v => v.json())).orarend;
    ld = data;
    ld.forEach((v) => {
      osztList.add(v.osztaly);
    });
    olt = Array.from(osztList);
  });

  function updateSchedule() {
    ld = data.filter(v => v.osztaly.includes(selectedOsztály) && v.het == "A");
    t = {};
    [mino, maxo] = [20, 0]; // Reset mino and maxo
    ld.forEach(v => {
      if (!t[v.ora]) {
        t[v.ora] = {};
      }
      if (v.ora > maxo) maxo = v.ora;
      if (v.ora < mino) mino = v.ora;
      t[v.ora][v.nap] = {
        tanar: v.tanar,
        targy: v.targy,
        terem: v.terem
      };
    });
  }
</script>

<main>
  <h1>Órarend</h1>
  <select bind:value={selectedOsztály} on:change={updateSchedule}>
    {#each olt as oszt}
      <option value={oszt}>{oszt}</option>
    {/each}
  </select>
  {#if maxo > mino}
    <table>
      <tr>
        {#each napok as nap}
          <th>{nap}</th>
        {/each}
      </tr>
      {#each Array(maxo - mino + 1).fill(0).map((_, i) => i + mino) as o}
        <tr>
          {#each napok as nap}
            {#if t[o] && t[o][nap]}
              <td>
                <div class="tan">{t[o][nap].tanar}</div>
                <div class="tar">{t[o][nap].targy}</div>
                <div class="ter">{t[o][nap].terem}</div>
              </td>
            {:else}
              <td class="x"></td>
            {/if}
          {/each}
        </tr>
      {/each}
    </table>
  {/if}
</main>

<style>
main {
  width: 100%;
  padding: 2rem;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h1 {
  font-size: 2.5rem;
  margin-bottom: 1rem;
  color: #333;
}

select {
  padding: 0.5rem;
  border-radius: 4px;
  border: 1px solid #ccc;
  margin-bottom: 1rem;
}

table {
  width: 80%;
  margin: 0 auto;
  border-collapse: separate;
  border-spacing: 0.5rem;
  margin-top: 1rem;
}

th, td {
  padding: 1rem;
  text-align: center;
  border: 1px solid #ddd;
  font-size: 1.2rem; /* Increased font size */
}

th {
  background-color: #f4f4f4;
}

td {
  background-color: rgb(49, 188, 36);
  border-radius: 8px;
  box-shadow: 1px 3px 3px black;
  margin: 7px;
  padding: 5px;
}

.x {
  background-color: #f9f9f9;
}

.tan, .tar, .ter {
  margin: 0;
  padding: 0;
}

:global(body) {
  font-size: 11px;
}
</style>
<script>
  // @ts-nocheck

  import { onMount, afterUpdate } from "svelte";
  import { apiData, matchData, rankedData } from "../store.js";
  import Champimg from "../lib/Champimg.svelte";

  let summonerName = "drunken wanderer";
  let notFound = "false";

  onMount(async () => {
    if (!$apiData.id) {
      fetch(
        `https://statcheck-server-production.up.railway.app/api/summoner/${summonerName}`
      )
        .then((response) => response.json())
        .then((data) => {
          apiData.set(data);
        })
        .catch((error) => {
          console.log(error);
          return [];
        });
    } else {
      summonerName = $apiData.name;
    }
  });

  async function searchName() {
    fetch(
      `https://statcheck-server-production.up.railway.app/api/summoner/${summonerName}`
    )
      .then((response) => response.json())
      .then((data) => {
        apiData.set(data);
        notFound = "false";
      })
      .then(() => {
        fetch(
          `https://statcheck-server-production.up.railway.app/api/rank?summonerId=${$apiData.id}`
        )
          .then((response) => response.json())
          .then((data) => {
            rankedData.set(data);
            console.log(data);
          });
      })
      .catch((error) => {
        console.log(error);
        notFound = "true";
        return [];
      });
  }

  function onKeyDown(e) {
    switch (e.keyCode) {
      case 13:
        searchName();
        break;
    }
  }

  $: icon = `http://ddragon.leagueoflegends.com/cdn/12.22.1/img/profileicon/${$apiData.profileIconId}.png`;
</script>

<svelte:head>
  <title>Summoner Search</title>
  <meta name="description" content="Summoner Search" />
  <link rel="icon" type="image/png" href="/favicon.png" />
</svelte:head>
<main>
  <div class="wrapper">
    <Champimg />
    <h2>Summoner Search</h2>
    <div class="search">
      <input
        type="text"
        bind:value={summonerName}
        on:keydown={onKeyDown}
        placeholder="Enter Summoner Name"
      />
      <button class="search-btn" on:click={searchName} on:keydown={onkeydown}
        >Search</button
      >
    </div>

    {#if notFound == "true"}
      <h2 class="search-name">Summoner Not Found</h2>
    {:else}
      <div class="summonerInfo">
        <a href="/details/{$apiData.name}"
          ><h2 class="search-name">{$apiData.name}</h2></a
        >
        <h3>Summoner Level: {$apiData.summonerLevel}</h3>

        <img src={icon} alt="Summoner Icon" />
      </div>
    {/if}
  </div>
</main>

<style>
  img {
    max-width: 100%;
  }

  .wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
    width: 100%;
    height: 100%;
  }

  main {
    justify-content: flex-start;
  }

  .search {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }
  .search input {
    width: 320px;
    padding: 12px 20px;
    margin: 8px 0;
    box-sizing: border-box;
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    height: 46px;
    border: none;
  }

  .search input:focus {
    outline: none;
  }

  .search button {
    width: 120px;
    background-color: #4caf50;
    color: white;
    padding: 14px 20px;
    margin: 8px 0;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
  }

  .search button:focus {
    border: none;
  }

  .search button:hover {
    background-color: #45a049;
  }

  .search-name {
    margin-bottom: 0;
  }

  .summonerInfo {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
  }

  @media (max-width: 500px) {
    .search {
      width: 85%;
      margin: auto;
    }
  }
</style>

<script>
  // @ts-nocheck

  import {
    apiData,
    matchIds,
    matchData,
    rankedData,
    runeData,
    mostPlayedData,
    itemData,
    summonerData,
  } from "../../../store.js";
  import WinsLoss from "../../../lib/WinsLoss.svelte";
  import CsPerMin from "../../../lib/CsPerMin.svelte";
  import { onMount, afterUpdate, onDestroy, tick } from "svelte";
  import GoldPerMin from "../../../lib/GoldPerMin.svelte";
  import DamagePerMin from "../../../lib/DamagePerMin.svelte";
  import Rank from "../../../lib/Rank.svelte";
  import Streak from "../../../lib/Streak.svelte";
  import ChampsPlayed from "../../../lib/ChampsPlayed.svelte";
  import MatchHistory from "../../../lib/MatchHistory.svelte";
  import MostPlayedImg from "../../../lib/MostPlayedImg.svelte";
  import VisionScore from "../../../lib/VisionScore.svelte";
  import RolesPlayed from "../../../lib/RolesPlayed.svelte";
  import { Jumper } from "svelte-loading-spinners";
  import runes from "../../../runesReforged.json";
  import item from "../../../item.json";
  import summoner from "../../../summoner.json";
  import { page } from "$app/stores";

  //fetch json runesreforged file
  runeData.set(runes);
  itemData.set(item);
  summonerData.set(summoner);

  let loaded = false;
  if ($matchData.length > 1) {
    loaded = true;
  }

  onMount(async () => {
    checkSlug();
  });

  //update page when params change on tick

  async function checkSlug() {
    if ($page.params.slug !== $apiData.name) {
      console.log("slug not equal to apiData.name");
      fetch(
        `https://statcheck-server-production.up.railway.app/api/summoner/${$page.params.slug}`
      )
        .then((response) => response.json())
        .then((data) => {
          apiData.set(data);
          console.log(`apiData: ${$apiData}`);
          fetchDetailsData();
        })
        .catch((error) => {
          console.log(error);
          return [];
        });
    } else {
      fetchDetailsData();
    }
  }

  async function fetchDetailsData() {
    fetch(
      `https://statcheck-server-production.up.railway.app/api/matches?puuid=${$apiData.puuid}`
    )
      .then((response) => response.json())
      .then((data) => {
        matchIds.set(data);
        console.log($matchIds);
        fetchMatchDetails();
        fetchRank();
      })
      .catch((error) => {
        console.log(error);
        return [];
      });
    window.scrollTo(0, 0);

    //set loaded to true when call is finished
    setTimeout(() => {
      loaded = true;
    }, 2000);
  }

  //fetch match details and sort by
  async function fetchMatchDetails() {
    if (!loaded) {
      for (let i = 0; i < $matchIds.length; i++) {
        fetch(
          `https://statcheck-server-production.up.railway.app/api/matchdetails?matchId=${$matchIds[i]}`
        )
          .then((response) => response.json())
          .then((data) => {
            $matchData = [...$matchData, data];
            console.log($matchData);
            sortByDate();
          })
          .catch((error) => {
            console.log(error);
            return [];
          });
      }
    }
  }

  //fetch rank and tier
  async function fetchRank() {
    fetch(
      `https://statcheck-server-production.up.railway.app/api/rank?summonerId=${$apiData.id}`
    )
      .then((response) => response.json())
      .then((data) => {
        rankedData.set(data);
        console.log(data);
      })
      .catch((error) => {
        console.log(error);
        return [];
      });
  }

  onDestroy(() => {
    matchData.set([]);
  });

  //sort matches by date
  function sortByDate() {
    $matchData.sort((a, b) => {
      return b.info.gameCreation - a.info.gameCreation;
    });
  }

  $: champ = $mostPlayedData;
</script>

<svelte:head>
  <title>{$apiData.name}</title>
  <meta name="description" content="Details" />
</svelte:head>

<main class="matches">
  <div class="wrapper">
    {#await $matchData}
      <Jumper />
    {:then data}
      {#if $matchIds == 0}
        <Jumper />
      {:else if loaded}
        <MostPlayedImg {loaded} {champ} />
        <div class="name-and-stats">
          <div class="left-column active">
            <ChampsPlayed {loaded} {data} />
          </div>
          <div class="stats">
            <h2>{$apiData.name}</h2>
            <!-- <h3>Level: {$apiData.summonerLevel}</h3> -->
            <div class="inner">
              <div class="name-and-rank">
                <!-- <img
                  class="summoner-icon"
                  src={`http://ddragon.leagueoflegends.com/cdn/11.6.1/img/profileicon/${$apiData.profileIconId}.png`}
                  alt="summoner icon"
                /> -->

                <Rank />
                <WinsLoss />
              </div>
              <div class="game-stats">
                <RolesPlayed />
                <h4>Average Game Stats</h4>
                <DamagePerMin />
                <GoldPerMin />
                <CsPerMin />
                <VisionScore />
                <Streak {loaded} />
              </div>
            </div>
            {#if loaded}
              <div>
                <MatchHistory {loaded} {checkSlug} />
              </div>
            {/if}
          </div>
        </div>
      {:else}
        <Jumper />
      {/if}
    {/await}
  </div>
</main>

<style>
  .wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    margin: 0 auto;
    width: 100%;
    height: 100%;
  }

  .name-and-stats {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 100%;
    margin: 0 auto;
  }

  .stats {
    display: flex;
    flex-direction: column;
    width: 100%;
    margin: 0 auto;
  }

  .stats .inner {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    width: 100%;
    margin: 0 auto;
    align-items: center;
  }

  .game-stats {
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    text-align: left;
  }

  .left-column {
    width: 275px;
  }

  .name-and-rank {
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  @media (max-width: 991px) {
    .name-and-stats {
      flex-direction: column;
    }

    .stats {
      width: 100%;
    }

    .left-column {
      width: 100%;
    }

    .inner {
      flex-wrap: wrap;
    }
  }

  @media (max-width: 500px) {
    .game-stats {
      margin-top: 48px;
      text-align: center;
    }
    .matches {
      width: 95%;
    }
  }
</style>

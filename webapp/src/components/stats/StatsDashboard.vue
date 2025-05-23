<template>
  <div v-if="stats" id="stats-container">
    <div id="main-stats-row">
      <div class="card bg-dark">
        <DonutChart
          identifier="kdChart"
          :data="[stats.kills, stats.deaths]"
          :labels="['Kills', 'Deaths']"
          :inner-title="`${roundNumber(stats.kdr)}`"
          inner-subtitle="KDR"
          :side-length="210"
          legend-positon="bottom"
        />
      </div>
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.hsRate * 100) }}%</h2>
          <span>Headshot%</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.avgKillsPerMatch) }}</h2>
          <span>Avg. kills per match</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.maxKillsPerMatch) }}</h2>
          <span>Max kills in a single match</span>
        </div>
      </div>
      <div class="card bg-dark">
        <DonutChart
          identifier="resultsChart"
          :data="[
            stats.matchesByResult.victory,
            stats.matchesByResult.tie,
            stats.matchesByResult.defeat,
          ]"
          :labels="['Victories', 'Ties', 'Defeats']"
          :inner-title="`${roundNumber(stats.winRate)}`"
          inner-subtitle="Win rate"
          :palette="palettes.results"
          :side-length="210"
          legend-positon="bottom"
        />
      </div>
      <div class="card bg-dark">
        <DonutChart
          identifier="mapsChart"
          :data="sortedMapValues"
          :labels="sortedMapNames"
          title="Maps"
          :inner-title="mostPlayedMap"
          inner-subtitle="Most played map"
          inner-title-size="1.8em"
          inner-subtitle-size="0.9em"
          :side-length="210"
        />
      </div>
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h2>{{ stats.totalMatches }}</h2>
          <span>Total matches</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.totalDuration / 3600) }} h</h2>
          <span>Time played</span>
        </div>
      </div>
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.avgMatchDuration / 60) }} min</h2>
          <span>Avg. match duration</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.avgMvps) }}</h2>
          <span>Avg. MVPs per match</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.avgScore) }}</h2>
          <span>Avg. score</span>
        </div>
      </div>
    </div>
    <div id="main-stats-row">
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h4>{{ stats.totalRounds }}</h4>
          <span>Rounds played</span>
        </div>
        <div class="info-box card bg-dark">
          <h4>{{ roundNumber(stats.avgRoundDuration) }} s</h4>
          <span>Avg. round duration</span>
        </div>
        <div class="info-box card bg-dark">
          <h4>{{ roundNumber(stats.avgRoundsPerMatch) }}</h4>
          <span>Avg. rounds per match</span>
        </div>
      </div>
      <div class="card bg-dark">
        <DonutChart
          identifier="roundChart"
          :data="[stats.totalRoundsWon, stats.totalRoundsLost]"
          :labels="['Won', 'Lost']"
          :inner-title="`${roundNumber(stats.roundWinRate)}`"
          inner-title-size="2.5em"
          inner-subtitle="Round win rate"
          inner-subtitle-size="0.8em"
          :palette="palettes.resultsWithoutTie"
          :side-length="150"
          legend-positon="bottom"
        />
      </div>
      <div class="card bg-dark">
        <DonutChart
          identifier="pistolRoundChart"
          :data="[stats.pistolRoundsWon, stats.pistolRoundsLost]"
          :labels="['Won', 'Lost']"
          :inner-title="`${roundNumber(stats.pistolRoundWinRate)}`"
          inner-title-size="2.5em"
          inner-subtitle="Pistol round win rate"
          inner-subtitle-size="0.69em"
          :palette="palettes.resultsWithoutTie"
          :side-length="150"
          legend-positon="bottom"
        />
      </div>
      <div class="card bg-dark">
        <BarChart
          title="Rounds by kills"
          :small-title="true"
          identifier="roundsByKillsChart"
          :data="roundsByKillsData"
          :height="140"
          :bar-width="36"
        />
      </div>
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.avgKillsPerRound) }}</h2>
          <span>Avg. kills per round</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.roundDeathPercent) }}</h2>
          <span>Death to round ratio</span>
        </div>
      </div>
    </div>
    <div id="main-stats-row">
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h2>{{ stats.mvpRounds }}</h2>
          <span>Total MVPs won</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>{{ roundNumber(stats.mvpRate) }}</h2>
          <span>MVP rate</span>
        </div>
      </div>
      <div class="card bg-dark">
        <DonutChart
          identifier="teamsChart"
          :data="Object.values(stats.roundsByTeam)"
          :labels="Object.keys(stats.roundsByTeam)"
          title="Rounds by team"
          :small-title="true"
          :inner-title="mostPlayedTeam"
          inner-subtitle="Most played team"
          inner-title-size="2.5em"
          inner-subtitle-size="0.75em"
          :side-length="150"
          :palette="palettes.teams"
        />
      </div>
      <div class="card bg-dark">
        <BarChart
          title="Win rate by team"
          :small-title="true"
          identifier="roundWinRateByTeamChart"
          :data="roundWinRateByTeamData"
          :palette="palettes.teams"
          :height="140"
          :bar-width="40"
        />
      </div>
      <div class="stats-column">
        <div class="info-box card bg-dark">
          <h2>${{ roundNumber(stats.avgInitMoney) }}</h2>
          <span>Avg. initial money</span>
        </div>
        <div class="info-box card bg-dark">
          <h2>${{ roundNumber(stats.avgEquipValue) }}</h2>
          <span>Avg. equipment value</span>
        </div>
      </div>
      <div class="card bg-dark">
        <DonutChart
          identifier="winTypeChart"
          :data="sortedWinTypeValues"
          :labels="sortedWinTypeNames"
          title="Rounds by win type"
          :small-title="true"
          :inner-title="mostUsualWinType"
          inner-subtitle="Most usual win type"
          inner-title-size="0.9em"
          inner-subtitle-size="0.66em"
          :side-length="150"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
  import { computed, watchEffect, ref, watch } from 'vue';
  import DonutChart from './DonutChart.vue';
  import { getAllStats } from '../../api/api';
  import palettes from '../../util/palette';
  import BarChart from './BarChart.vue';
  import { selectedGameMode } from '../../store/filters';

  const stats = ref(null);

  function updateStats() {
    const options = selectedGameMode.value ? { mode: selectedGameMode.value } : {};
    getAllStats(options).then((newStats) => {
      stats.value = newStats;
    });
  }

  function roundNumber(number) {
    return Math.round((number + Number.EPSILON) * 100) / 100;
  }

  const mostPlayedTeam = computed(() => {
    const maxTeam = Math.max(...Object.values(stats.value.roundsByTeam));
    return Object.keys(stats.value.roundsByTeam).filter(
      (team) => stats.value.roundsByTeam[team] === maxTeam
    )[0];
  });
  const sortedMapValues = computed(() =>
    Object.values(stats.value.matchesByMap).sort(
      (first, second) => second - first
    )
  );
  const sortedMapNames = computed(() =>
    Object.keys(stats.value.matchesByMap).sort(
      (firstMap, secondMap) =>
        stats.value.matchesByMap[secondMap] - stats.value.matchesByMap[firstMap]
    )
  );
  const sortedWinTypeValues = computed(() =>
    Object.values(stats.value.roundsByWinType).sort(
      (first, second) => second - first
    )
  );
  const sortedWinTypeNames = computed(() =>
    Object.keys(stats.value.roundsByWinType).sort(
      (first, second) =>
        stats.value.roundsByWinType[second] - stats.value.roundsByWinType[first]
    )
  );
  const mostPlayedMap = computed(() => sortedMapNames.value[0]);
  const mostUsualWinType = computed(() => sortedWinTypeNames.value[0]);
  const roundsByKillsData = computed(() =>
    stats.value.roundsByKills.map((element, index) => ({
      label: index,
      value: element,
    }))
  );
  const roundWinRateByTeamData = computed(() =>
    Object.keys(stats.value.roundWinRateByTeam).map((key) => ({
      label: key,
      value: roundNumber(stats.value.roundWinRateByTeam[key]),
    }))
  );

  watchEffect(() => {
    updateStats();
  });

  watch(selectedGameMode, () => {
    updateStats();
  });
</script>

<style scoped>
  #stats-container {
    height: 100%;
    display: grid;
  }

  #main-stats-row {
    margin-top: 1vh;
    margin-bottom: 1vh;
    display: flex;
    flex-direction: row;
  }

  .stats-column {
    flex: 1 1 auto;
    display: flex;
    flex-flow: column;
  }

  .info-box {
    flex: 1 1 auto;
    margin-bottom: 0.7em;
    justify-content: center;
    align-items: center;
    padding: 0 1em;
  }

  .info-box h4 {
    margin-bottom: 0.1em;
  }

  .info-box span {
    font-size: 0.75em;
    text-align: center;
  }

  .info-box:last-child {
    margin-bottom: 0;
  }

  .card {
    margin-right: 0.35em;
    margin-left: 0.35em;
    align-items: center;
    justify-content: center;
  }
</style>

<template>
  <div class="card m-2 mb-8 border-x-1 border-400">
    <DataTable :value="teamInfo" tableStyle="min-width: 50rem">
        <template #header>
        <div
        style="background-color: rgb(36, 1, 59);"
          class="p-3 border-round-md flex flex-wrap align-items-center justify-content-between gap-2" 
        >
        <div class="flex">
          <img :src="logo" class="bg-white border-round-md" width="50"/>
          <div class="flex flex-column ml-2">
            <span class="text-xl text-900 font-bold text-white">Premier League</span>
            <span class="text-sm text-gray-500 mt-2">2023-24 Season</span>
          </div>
        </div>
        </div>
      </template>
      <Column field="rank" header=""></Column>
      <Column field="Team" header="Team">
        <template #body="slotProps">
          <div class="flex align-items-center">
            <img :src="slotProps.data.logo" width="30" />
            <span class="ml-2">{{ slotProps.data.Team }}</span>
          </div>
        </template>
      </Column>
      <Column field="PTS" header="PTS"></Column>
      <Column field="MP" header="MP"></Column>
      <Column field="W" header="W"></Column>
      <Column field="L" header="L"></Column>
      <Column field="D" header="D"></Column>
      <Column field="GF" header="GF"></Column>
      <Column field="GA" header="GA"></Column>
      <Column field="GD" header="GD"></Column>
    </DataTable>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import logo from "~/assets/th.png"
import DataTable from "primevue/datatable";
import Column from "primevue/column";
import { TeamModel } from "../models/teams/TeamModel";

class teamInfoClass {
  rank: Number = 0;
  logo: String = "";
  Team: String = "";
  PTS: Number = 0;
  MP: Number = 0;
  W: Number = 0;
  L: Number = 0;
  D: Number = 0;
  GF: Number = 0;
  GA: Number = 0;
  GD: Number = 0;
}

const teamInfo = ref<teamInfoClass[]>([new teamInfoClass()]);

// Assuming the useFetch function returns AsyncData with the correct types
interface AsyncData<Data, Error> {
  data: Data;
  error: Error;
}

// Assuming FetchError is defined properly
interface FetchError<T> {
  // Define the properties of FetchError
  // Adjust the properties according to the actual structure of FetchError if necessary
  message: string;
  status: number;
  data: T;
}

// Assuming useFetch has the correct signature and returns AsyncData
declare function useFetch(
  url: string,
  options: any
): AsyncData<TeamModel[], FetchError<any> | null>;

const headers: { [key: string]: string } = {
  "X-RapidAPI-Key": "73b5e81bf2msha5bb61cd6acb311p124a6ajsn4b06e546c7c8",
  "X-RapidAPI-Host": "premier-league-standings1.p.rapidapi.com",
};

const fetchData = async () => {
  try {
    const { data: teams }: { data: TeamModel[] } = await useFetch(
      "https://premier-league-standings1.p.rapidapi.com/",
      {
        headers,
        query: {
          season: 2023,
        },
      }
    );

    teamInfo.value = teams._value.map((team: TeamModel) => ({
      rank: team.stats.rank,  
      logo: team.team.logo,
      Team: team.team.name,
      PTS: team.stats.points,
      MP: team.stats.gamesPlayed,
      W: team.stats.wins,
      L: team.stats.losses,
      D: team.stats.ties,
      GF: team.stats.goalsFor,
      GA: team.stats.goalsAgainst,
      GD: team.stats.goalDifference,
    }));
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

fetchData();
</script>

<style scoped>
</style>
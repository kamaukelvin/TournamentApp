<template>
  <div v-if="currentGroupTeams">
    <h1>{{ currentGroupTeams.title }}</h1>

    <div v-if="currentGroupTeams.teams">
      <v-list>
        <v-list-item
          v-for="(item, index) in currentGroupTeams.teams"
          :key="index"
        >
          <v-list-item-icon>
            <v-icon v-if="item.icon" color="pink">
              mdi-star
            </v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title v-text="item.name"></v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <br />
        <p>Nothing</p>
      </v-list>
    </div>
    <add-team :groupInfo="currentGroupTeams" @refreshList="refreshList" />
  </div>
  <div v-else>
    <br />
    <p>Please click on a Tutorial...</p>
  </div>
</template>

<script>
import AddTeam from "./AddTeam.vue";
export default {
  components: { AddTeam },
  name: "group_teams",
  props: ["group_teams"],
  data() {
    return {
      currentGroupTeams: null,
      message: ""
    };
  },
  watch: {
    group_teams: function(groupTeam) {
      console.log("any", groupTeam);
      this.currentGroupTeams = { ...groupTeam };
      this.message = "";
    }
  },
  methods: {
    refreshList() {
      this.$emit("refreshList");
    }
  },

  mounted() {
    this.message = "";
    this.currentGroupTeams = { ...this.group_teams };
  }
};
</script>

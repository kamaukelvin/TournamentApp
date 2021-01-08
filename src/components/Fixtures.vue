<template>
  <v-container>
    <v-simple-table dark height="300px">
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">
              Home
            </th>
            <th class="text-left">
              Away
            </th>
            <th class="text-left">
              Time
            </th>
            <th class="text-left">
              Date
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in currentFixtures.fixtures" :key="index">
            <td>{{ item.homeTeam.name }}</td>
            <td>{{ item.awayTeam.name }}</td>
            <td>{{ item.time }}</td>
            <td>{{ item.date }}</td>
            <td><v-btn @click="showupdateMatchDialog(index)" color="info">Add Team</v-btn></td>
          </tr>
        </tbody>
        
      </template>
    </v-simple-table>
      <v-dialog v-model="dialog" :key="componentKey">
      <UpdateMatchModal
        :matchKey="matchKey"
        :teamGroupKey="this.currentFixtures.key"
      />
    </v-dialog>
  </v-container>
</template>

<script>
import UpdateMatchModal from "./modal/UpdateMatchModal.vue";

export default {
  name: "Fixtures",
  props: ["groupInfo"],
  components: {UpdateMatchModal},
  data() {
    return {
      currentFixtures: null,
      message: null,
      matchKey:"",
      teamGroupKey:null,
      dialog:false,
      componentKey:0
    };
  },
  watch: {
    groupInfo: function(groupInfo) {
      console.log("any yo", groupInfo);
      this.currentFixtures = { ...groupInfo };
      this.message = "";
    }
  },
  methods:{
        forceRerender() {
      this.componentKey += 1;
    },
      showupdateMatchDialog(key) {
        console.log("are we here ")
            this.forceRerender();

      this.matchKey = key;
      this.dialog = true;
    },
  },

  mounted() {
    this.message = "";
    this.currentFixtures = { ...this.groupInfo };
  }
};
</script>

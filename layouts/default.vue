<template>
  <v-app app dark>
    <v-navigation-drawer
      app
      v-model="drawer"
      class="grey darken-2"
      mobile-break-point="650"
    >
      <v-list subheader>
        <v-subheader class="grey darken-3"><h1>Users</h1></v-subheader>
        <v-list-tile v-for="u in users" :key="u.id" @click.prevent>
          <v-list-tile-content>
            <v-list-tile-title>{{ u.name }}</v-list-tile-title>
          </v-list-tile-content>

          <v-list-tile-action>
            <v-icon :color="u.id === user.id ? 'blue' : 'grey'"
              >chat_bubble</v-icon
            >
          </v-list-tile-action>
        </v-list-tile>
      </v-list>
      <Chart :key="users.toString()" />
    </v-navigation-drawer>

    <v-toolbar app>
      <v-tooltip bottom>
        <template v-slot:activator="{ on, attrs }">
          <v-toolbar-side-icon
            color="grey darken-3"
            dark
            v-bind="attrs"
            v-on="on"
            @click="drawer = !drawer"
          >
          </v-toolbar-side-icon>
        </template>
        <span>Hide aside</span>
      </v-tooltip>
      <v-btn icon @click="exit">
        <v-icon color="success">arrow_back</v-icon>
        <small>leave</small>
      </v-btn>
      <v-toolbar-title class="red--text"
        >Room number
        <strong class="blue--text">{{ user.room }}</strong></v-toolbar-title
      >
    </v-toolbar>
    <v-content>
      <div style="height: 100%">
        <nuxt />
      </div>
    </v-content>
  </v-app>
</template>

<script>
import Chart from "@/components/Chart.vue";
import { mapState, mapMutations } from "vuex";
export default {
  data: () => ({
    drawer: true,
  }),
  components: { Chart },

  computed: {
    ...mapState(["user", "users"]),
  },
  methods: {
    ...mapMutations(["clearData"]),
    exit() {
      this.$socket.emit("userLeft", this.user.id, () => {
        this.$router.push("/?message=leftChat");
        this.clearData();
      });
    },
  },
};
</script>
  

<template>
  <v-app dark>
    <v-app-bar fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <h3 style="width: 100%; text-align: center" v-if="$route.path == '/'">
        Chat Vocal GTP-3
      </h3>
      <h3 style="width: 100%; text-align: center" v-else>DALL-E</h3>
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>

    <v-navigation-drawer v-model="drawer" absolute bottom temporary>
      <v-list nav>
        <v-list-item-group v-model="group">
          <v-list-item nuxt to="/">
            <v-list-item-title class="text-h6 white--text">
              GPT-3
            </v-list-item-title>
          </v-list-item>

          <v-list-item nuxt to="/dalle">
            <v-list-item-title class="text-h6 white--text">
              DALL-E
            </v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
    <!-- <v-footer app>
      <span>Powered by Dev. Rabibocheur</span>
    </v-footer> -->
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      drawer: false,
    };
  },
};
</script>

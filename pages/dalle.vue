<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card
        max-height="calc(100vh - 230px)"
        class="overflow-auto"
        id="container"
        v-chat-scroll
      >
        <v-card-text v-for="(msg, i) in messages" :key="i" class="pa-0">
          <div class="pa-4">
            <p class="font-weight-bold">
              Prompt :
              <span class="d-inline-block font-weight-light ml-2">{{
                msg.msg
              }}</span>
            </p>

            <v-row v-if="msg.data != null">
              <v-col v-for="(data, i) in msg.data" :key="i" cols="6">
                <v-img :src="data.url"></v-img>
              </v-col>
            </v-row>
          </div>

          <v-divider class="mx-4"></v-divider>
        </v-card-text>
      </v-card>
      <v-dialog
        transition="dialog-bottom-transition"
        max-width="600"
        v-model="dialog"
      >
        <v-card>
          <v-card-text class="text-h5 pa-5 d-flex justify-center align-center">
            <v-textarea
              v-model="resultsRecognizer"
              outlined
              name="input-7-4"
              placeholder=" Parlez ou recommencez"
            ></v-textarea>
          </v-card-text>

          <v-card-actions class="justify-end pt-1">
            <v-btn depressed text @click="speakRecognition()">
              Recommencer
            </v-btn>
            <v-btn
              v-show="resultsRecognizer != ''"
              color="primary"
              @click="fetchDataBot()"
            >
              Envoyer
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <v-btn
        color="start-btn primary rounded-circle"
        width="130px"
        height="130px"
        @click="loading ? '' : (dialog = true)"
      >
        <v-progress-circular
          v-if="loading"
          :size="60"
          width="8"
          color="white"
          indeterminate
        ></v-progress-circular>
        <v-icon v-else style="font-size: 70px"> mdi-microphone </v-icon>
      </v-btn>
    </v-col>
  </v-row>
</template>

<script>
import { SpeechRecognition } from "@capacitor-community/speech-recognition";
import { Microphone } from "@mozartec/capacitor-microphone";

export default {
  name: "IndexPage",
  async mounted() {
    await Microphone.requestPermissions();
  },
  methods: {
    async speakRecognition() {
      const { available } = await SpeechRecognition.available();

      if (available) {
        this.resultsRecognizer = "";
        this.dialog = true;

        SpeechRecognition.start({
          language: "fr-FR",
          maxResults: 2,
          partialResults: true,
          popup: false,
        });

        SpeechRecognition.addListener("partialResults", async (data) => {
          if (data.matches && data.matches.length > 0) {
            this.resultsRecognizer = data.matches[0];
          }

          if (data.value && data.value.length > 0) {
            this.resultsRecognizer = data.value[0];
          }
        });
      }
    },
    async fetchDataBot() {
      this.dialog = false;
      this.loading = true;

      const response = await fetch("", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          prompt: this.resultsRecognizer,
          number: 2,
        }),
      });

      let data = null;

      if (response.ok) {
        this.loading = false;
        data = await response.json();
        //this.speakVoice();
      } else {
        this.loading = false;
        alert(await response.text());
      }

      console.log(data.images.data);

      this.messages.push({
        msg: this.resultsRecognizer,
        data: data.images.data,
      });
    },
  },
  data() {
    return {
      dialog: false,
      messages: [],
      resultsRecognizer: "",
      loading: false,
    };
  },
};
</script>

<style lang="css">
.start-btn {
  position: fixed;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
}
</style>

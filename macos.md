```
<template>
  <div class="splashlayout">
    <v-row style="padding: 50px">
      <v-col cols="12" class="text-center">
        <h1 class="intro-header">Have a nice day!</h1>
      </v-col>
    </v-row>

    <v-container fluid class="app-container justify-center align-center">
      <v-row>
        <v-col
          cols="12"
          sm="2"
          class="text-center d-flex flex-column justify-center align-center"
        >
          <div>
            <div
              class="
                app-icon-container
                block
                text-center
                d-flex
                flex-column
                justify-center
                align-center
              "
              style="
                background-image: linear-gradient(to bottom, #00695c, #4db6ac);
              "
            >
              <div
                class="app-icon app-icon-dvr"
                @mouseover="scaleup($event)"
                @mouseout="scaledown($event)"
                @click="goToDvrPage"
              ></div>
            </div>
            <b class="block app-name">DVR 1.0</b>
          </div>
        </v-col>

        <v-col
          cols="12"
          sm="2"
          class="text-center d-flex flex-column justify-center align-center"
        >
          <div>
            <div
              class="
                app-icon-container
                block
                text-center
                d-flex
                flex-column
                justify-center
                align-center
              "
              style="
                background-image: linear-gradient(to bottom, #ff6f00, #ffc107);
              "
            >
                          <div
                class="app-icon app-icon-dvr2"
                @mouseover="scaleup($event)"
                @mouseout="scaledown($event)"
                @click="goToDvr2Page"
              ></div>
            </div>
            <b class="block app-name">DVR 2.0</b>
          </div>
        </v-col>

        <v-col
          cols="12"
          sm="2"
          class="text-center d-flex flex-column justify-center align-center"
        >
          <div>
            <div
              class="
                app-icon-container
                block
                text-center
                d-flex
                flex-column
                justify-center
                align-center
              "
              style="
                background-image: linear-gradient(to bottom, #039be5, #4fc3f7);
              "
            >
              <div
                class="app-icon app-icon-dbuilder"
                @mouseover="scaleup($event)"
                @mouseout="scaledown($event)"
              ></div>
            </div>
            <b class="block app-name">dBuilder</b>
          </div>
        </v-col>
        <v-col
          cols="12"
          sm="2"
          class="text-center d-flex flex-column justify-center align-center"
        >
          <div>
            <div
              class="
                app-icon-container
                block
                text-center
                d-flex
                flex-column
                justify-center
                align-center
              "
              style="
                background-image: linear-gradient(to bottom, #A1887F, #BCAAA4);
              "
            >
             <div
                class="app-icon app-icon-atgitlab"
                @mouseover="scaleup($event)"
                @mouseout="scaledown($event)"
              ></div>
            </div>
            <b class="block app-name">AT Gitlab</b>
          </div>
        </v-col>
      </v-row>
      <div class="icon-footer">
        <div>
          <div
            class="
              app-icon-container-small
              block
              text-center
              d-flex
              flex-column
              justify-center
              align-center
            "
            style="
              background-image: linear-gradient(to bottom, #2962ff, #2962ff);
            "
          >
          <div
              class="app-icon-small app-icon-user"
              @mouseover="scaleup($event)"
              @mouseout="scaledown($event)"
            ></div>
          </div>
        </div>
        <div>
          <div
            class="
              app-icon-container-small
              block
              text-center
              d-flex
              flex-column
              justify-center
              align-center
            "
            style="
              background-image: linear-gradient(to bottom, #651fff, #651fff);
            "
          >
          <div
              class="app-icon-small app-icon-setting"
              @mouseover="scaleup($event)"
              @mouseout="scaledown($event)"
            ></div>
          </div>
        </div>
        <div>
          <div
            class="
              app-icon-container-small
              block
              text-center
              d-flex
              flex-column
              justify-center
              align-center
            "
            style="
              background-image: linear-gradient(to bottom, #455a64, #455a64);
            "
          >
           <div
              class="app-icon-small app-icon-chat"
              @mouseover="scaleup($event)"
              @mouseout="scaledown($event)"
            ></div>
          </div>
        </div>
        <div>
          <div
            class="
              app-icon-container-small
              block
              text-center
              d-flex
              flex-column
              justify-center
              align-center
            "
            style="
              background-image: linear-gradient(to bottom, #00796b, #00796b);
            "
          >
          <div
              class="app-icon-small app-icon-help"
              @mouseover="scaleup($event)"
              @mouseout="scaledown($event)"
            ></div>
          </div>
        </div>

      </div>
    </v-container>
  </div>
</template>
<style lang="scss"></style>
<script>
import SideBar from "@/components/SidebarPlugin/SideBar.vue";
import SidebarLink from "@/components/SidebarPlugin/SidebarLink.vue";
import SidebarShare from "./SidebarSharePlugin.vue";
export default {
  components: {
    SideBar,
    SidebarLink,
    SidebarShare
  },
  data() {
    return {
      loading: true,
      backgroundColor: "at-primary"
    };
  },
  mounted() {
    console.log("splash is mounted!");
  },
  computed: {
    isReady() {
      console.log(
        "is ready: ",
        this.projects != null &&
          this.versions != null &&
          this.vfVersions != null &&
          this.users != null
      );
      
            return (
        this.projects != null &&
        this.versions != null &&
        this.vfVersions != null &&
        this.users != null
      );
    },
    projects() {
      console.log(this.$store.state.common.projects);
      return this.$store.state.common.projects;
    },
    versions() {
      console.log(this.$store.state.common.versions);
      return this.$store.state.common.versions;
    },
    vfVersions() {
      console.log(this.$store.state.common.vfVersions);
      return this.$store.state.common.vfVersions;
    },
    users() {
      console.log(this.$store.state.common.users);
      return this.$store.state.common.users;
    }
  },
        methods: {
    openDocs() {
      const { spawn } = require("child_process");
      const ls = spawn(
        "/user/athome/solutions/AntsDVA/script/csh/open_gdva_docs"
      );
      ls.stdout.on("data", data => {
        console.log("done");
      });
    },
    openTutorial() {
      const { spawn } = require("child_process");
      const ls = spawn(
        "/user/athome/solutions/AntsDVA/script/csh/open_gdva_tutorial"
      );
      ls.stdout.on("data", data => {
        console.log("done");
      });
    },
    bounce() {
      this.$refs.logo.$el.animate(
        [
          { transform: "translateY(0)" },
          { transform: "translateY(-15px)" }, // bounce amount
          { transform: "translateY(0)" }
        ],
        {
          duration: 500, // ms
          easing: "ease-out",
          fill: "forwards"
        }
      );
    },
      shake() {
      this.$refs.logo.$el.animate(
        [
          { transform: "translateX(0)" },
          { transform: "translateX(5px)" }, // shake amount
          { transform: "translateX(-5px)" },
          { transform: "translateX(5px)" },
          { transform: "translateX(-5px)" },
          { transform: "translateX(0)" }
        ],
        {
          duration: 2000, // ms
          easing: "ease-out",
          fill: "forwards"
        }
      );
    },
    scaleup(event) {
      event.target.parentNode.style.setProperty(
        "transform",
        "scale(1.1)",
        "important"
      );
      event.target.parentNode.style.setProperty(
        "transition",
        "transform 0.2s ease-in-out",
        "important"
      );
    },
    
    scaledown(event) {
      event.target.parentNode.style.setProperty(
        "transform",
        "scale(1)",
        "important"
      );
      event.target.parentNode.style.setProperty(
        "transition",
        "transform 0.2s ease-in-out",
        "important"
      );
    },
    goToDvrPage() {
      this.$router.push("/dvr");
    },
    goToDvr2Page() {
      this.$router.push("/dvr2");
    },
    toggleSidebar() {
      if (this.$sidebar.showSidebar) {
        this.$sidebar.displaySidebar(false);
      }
    }
  }
};
</script>

<style>
.content {
  height: 100%;
}
.splashlayout {
  width: 100%;
  height: 100%;
  background-image: url("../../assets/img/yosemite.jpg");
  position: relative;
}
.icon-footer {
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  right: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0 10px; /* add some padding to the left and right of the icons */
  padding-bottom: 10px;
  z-index: 1; /* make sure the icons appear on top of other elements */
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 18px;
  width: 400px;
  margin-bottom: 10px;
}
.intro-header {
  font-size: 5em;
  color: black;
}
.app-container {
  width: 50%;
}
.app-icon-container {
  background-size: cover;
  width: 100px !important;
  height: 100px !important;
  padding: 10px;
  border-radius: 24px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 1);
}
.app-icon-container-small {
  background-size: cover;
  width: 50px !important;
  height: 50px !important;
  padding: 10px;
  margin: 10px;
  border-radius: 18px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
}
.app-icon {
  width: 70px !important;
  height: 70px !important;
  background-size: 100%;
  background-position: center;
  border-radius: 18px;
  cursor: pointer;
}
.app-icon-small {
  width: 30px !important;
  height: 30px !important;
  background-size: 100%;
  background-position: center;
  border-radius: 18px;
  cursor: pointer;
}
.app-icon-dvr {
  background-image: url("../../assets/img/dvr.png");
}

.app-icon-dvr2 {
  background-image: url("../../assets/img/automation.png");
}
.app-icon-dbuilder {
  background-image: url("../../assets/img/tm.png");
}
.app-icon-atgitlab{
  background-image: url("../../assets/img/gitlab.png");
}
.app-icon-user {
  background-image: url("../../assets/img/user.png");
  filter: invert(100%);
}
.app-icon-setting {
  background-image: url("../../assets/img/setting.png");
  filter: invert(100%);
}
.app-icon-chat {
  background-image: url("../../assets/img/chat.png");
  filter: invert(100%);
}
.app-icon-help {
  background-image: url("../../assets/img/help.png");
  filter: invert(100%);
}
.app-name {
  padding-top: 10px;
}

.block {
  display: block;
}
</style>

          
```

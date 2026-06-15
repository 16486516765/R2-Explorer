<template>
  <div class="q-pa-md sidebar-wrapper" style="height: 100%">
    <div class="flex column no-wrap items-center" style="height: 100%">
      
      <q-btn v-if="mainStore.apiReadonly" flat class="q-mb-lg readonly-btn" stack label="Read only" icon="lock" />
      
      <q-btn v-else flat stack class="q-mb-lg btn-new-action" label="New">
        <div class="new-btn-content flex column items-center">
          <q-icon name="add" size="24px" class="add-icon-rotate" />
          <span class="q-mt-xs">New</span>
        </div>
        
        <q-menu transition-show="jump-down" transition-hide="jump-up" class="glass-menu">
          <q-list class="glass-list">
            <q-item clickable v-close-popup @click="$refs.createFile.open()">
              <q-item-section avatar min-width="24px">
                <q-icon name="note_add" class="text-primary" />
              </q-item-section>
              <q-item-section>New File</q-item-section>
            </q-item>
            
            <q-item clickable v-close-popup @click="$refs.createFolder.open()">
              <q-item-section avatar min-width="24px">
                <q-icon name="create_new_folder" class="text-teal" />
              </q-item-section>
              <q-item-section>New Folder</q-item-section>
            </q-item>

            <q-item clickable v-close-popup @click="$bus.emit('openFilesUploader')">
              <q-item-section avatar min-width="24px">
                <q-icon name="upload_file" class="text-orange" />
              </q-item-section>
              <q-item-section>Upload Files</q-item-section>
            </q-item>

            <q-item clickable v-close-popup @click="$bus.emit('openFoldersUploader')">
              <q-item-section avatar min-width="24px">
                <q-icon name="folder" class="text-amber" />
              </q-item-section>
              <q-item-section>Upload Folders</q-item-section>
            </q-item>
          </q-list>
        </q-menu>
      </q-btn>

      <q-btn 
        flat 
        class="q-mb-sm nav-btn" 
        :class="{ 'nav-active': selectedApp === 'files' }"
        @click="gotoFiles" 
        icon="folder_copy" 
        label="Files" 
        stack 
      />
      
      <q-btn 
        v-if="mainStore.config && mainStore.config.emailRouting !== false" 
        flat
        class="q-mb-sm nav-btn" 
        :class="{ 'nav-active': selectedApp === 'email' }"
        @click="gotoEmail" 
        icon="email" 
        label="Email" 
        stack 
      />

      <q-btn 
        flat
        class="q-mb-sm q-mt-auto q-mb-0 nav-btn info-btn" 
        @click="infoPopup=true" 
        icon="info" 
        label="Info"
        stack 
      />
    </div>
  </div>

  <q-dialog v-model="infoPopup" persistent no-route-dismiss transition-show="scale" transition-hide="scale">
    <q-card class="glass-dialog-card">
      <q-card-section class="dialog-header text-center">
        <div class="text-h6 text-bold brand-text-gradient">🎉 R2-Explorer 🚀</div>
      </q-card-section>

      <q-card-section class="q-pt-none dialog-body text-grey-9">
        <div class="info-row"><span>Version:</span> <b>{{ mainStore.version }}</b></div>
        <div v-if="updateAvailable" class="update-banner q-my-sm">
          🔥 Latest version: <b>{{latestVersion}}</b><br>
          <a href="https://r2explorer.com/getting-started/updating-your-project/" target="_blank" class="update-link">Learn how to update →</a>
        </div>
        <hr class="divider">
        <div v-if="mainStore.auth" class="auth-info-block">
          <div class="info-title">🔒 Authentication</div>
          <div class="info-row"><span>Method:</span> <code>{{ mainStore.auth.type }}</code></div>
          <div class="info-row"><span>User:</span> <code>{{ mainStore.auth.username }}</code></div>
        </div>
        <div v-else class="text-negative text-weight-medium">⚠️ Not authenticated</div>
        
        <hr class="divider">
        <div class="info-title">⚙️ Server Config</div>
        <pre class="config-pre"><code>{{ mainStore.config }}</code></pre>
      </q-card-section>

      <q-card-actions align="right" class="q-pb-md q-pr-md">
        <q-btn rounded unelevated label="Dismiss" color="primary" class="close-dialog-btn" v-close-popup />
      </q-card-actions>
    </q-card>
  </q-dialog>

  <create-folder ref="createFolder" />
  <create-file ref="createFile" />
</template>

<script>
import CreateFile from "components/files/CreateFile.vue";
import CreateFolder from "components/files/CreateFolder.vue";
import { useMainStore } from "stores/main-store";
import { defineComponent } from "vue";

export default defineComponent({
	name: "LeftSidebar",
	data: () => ({
		infoPopup: false,
		updateAvailable: false,
		latestVersion: "",
	}),
	components: { CreateFolder, CreateFile },
	methods: {
		gotoEmail: function () {
			if (this.selectedApp !== "email") this.changeApp("email");
		},
		gotoFiles: function () {
			if (this.selectedApp !== "files") this.changeApp("files");
		},
		changeApp: function (app) {
			this.$router.push({
				name: `${app}-home`,
				params: { bucket: this.selectedBucket },
			});
		},
		isUpdateAvailable: (currentVersion, latestVersion) => {
			const current = currentVersion.split(".").map(Number);
			const latest = latestVersion.split(".").map(Number);
			if (latest[0] > current[0]) return true;
			if (latest[0] < current[0]) return false;
			if (latest[1] > current[1]) return true;
			if (latest[1] < current[1]) return false;
			if (latest[2] > current[2]) return true;
			return false;
		},
	},
	computed: {
		selectedBucket: function () {
			return this.$route.params.bucket;
		},
		selectedApp: function () {
			return this.$route.name?.split("-")[0] || "files";
		},
	},
	async mounted() {
		const resp = await fetch(
			"https://api.github.com/repos/G4brym/R2-Explorer/releases/latest",
		);
		if (!resp.ok) {
			console.log("Unable to retrieve latest r2-explorer updates :(");
		} else {
			const parsed = await resp.json();
			const latestVersion = parsed.tag_name.replace("v", "");
			if (this.isUpdateAvailable(this.mainStore.version, latestVersion)) {
				this.latestVersion = latestVersion;
				this.updateAvailable = true;
			}
		}
	},
	setup() {
		const mainStore = useMainStore();
		return { mainStore };
	},
});
</script>

<style scoped>
/* ==========================================
   1. 整体容器优化
   ========================================== */
.sidebar-wrapper {
  user-select: none;
}

.q-btn {
  width: 76px;
  height: 76px;
  border-radius: 16px !important;
  text-transform: none;
  font-size: 0.75rem;
  font-weight: 600;
  letter-spacing: 0.02em;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

/* ==========================================
   2. "New" 炫彩悬浮按钮 (仿谷歌极客风)
   ========================================== */
.btn-new-action {
  background: linear-gradient(135deg, #4f46e5 0%, #06b6d4 100%);
  color: white !important;
  box-shadow: 0 8px 20px rgba(6, 182, 212, 0.25);
}

.btn-new-action:hover {
  transform: translateY(-3px) scale(1.03);
  box-shadow: 0 12px 24px rgba(6, 182, 212, 0.35);
}

.btn-new-action:active {
  transform: translateY(1px) scale(0.97);
}

.add-icon-rotate {
  transition: transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

.btn-new-action:hover .add-icon-rotate {
  transform
                  <q-icon name="folder" size="sm" />
                  Upload Folders
                </q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
        </q-menu>
      </q-btn>

      <q-btn class="q-mb-sm" @click="gotoFiles" color="blue" icon="folder_copy" label="Files" stack />
      <q-btn v-if="mainStore.config && mainStore.config.emailRouting !== false" class="q-mb-sm" @click="gotoEmail" color="blue" icon="email" label="Email" stack />

      <q-btn class="q-mb-sm q-mt-auto q-mb-0" @click="infoPopup=true" color="secondary" icon="question_mark"
             label="Info"
             stack />
    </div>
  </div>

  <q-dialog v-model="infoPopup" persistent no-route-dismiss>
    <q-card>
      <q-card-section>
        <div class="text-h6">🎉 Thank you for using R2-Explorer! 🚀</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
        You are running version <b>{{ mainStore.version }}</b><br>
        <template v-if="updateAvailable">
          Latest version is <b>{{latestVersion}}</b>, learn how to <a href="https://r2explorer.com/getting-started/updating-your-project/" target="_blank">update your instance here</a>.<br>
        </template>
        <br>
        <template v-if="mainStore.auth">
          <b>Authentication</b><br>
          Method: {{ mainStore.auth.type }}<br>
          Username: {{ mainStore.auth.username }}
        </template>
        <template v-else>
          Not authenticated
        </template>
        <br><br>
        <b>Server Configuration</b><br>
        {{ JSON.stringify(mainStore.config, null, 2) }}
      </q-card-section>

      <q-card-actions align="right">
        <q-btn flat label="OK" color="primary" v-close-popup />
      </q-card-actions>
    </q-card>
  </q-dialog>

  <create-folder ref="createFolder" />
  <create-file ref="createFile" />
</template>

<script>
import CreateFile from "components/files/CreateFile.vue";
import CreateFolder from "components/files/CreateFolder.vue";
import { useMainStore } from "stores/main-store";
import { defineComponent } from "vue";

export default defineComponent({
	name: "LeftSidebar",
	data: () => ({
		infoPopup: false,
		updateAvailable: false,
		latestVersion: "",
	}),
	components: { CreateFolder, CreateFile },
	methods: {
		gotoEmail: function () {
			if (this.selectedApp !== "email") this.changeApp("email");
		},
		gotoFiles: function () {
			if (this.selectedApp !== "files") this.changeApp("files");
		},
		changeApp: function (app) {
			this.$router.push({
				name: `${app}-home`,
				params: { bucket: this.selectedBucket },
			});
		},
		isUpdateAvailable: (currentVersion, latestVersion) => {
			// Split versions into parts and convert to numbers
			const current = currentVersion.split(".").map(Number);
			const latest = latestVersion.split(".").map(Number);

			// Compare major version
			if (latest[0] > current[0]) return true;
			if (latest[0] < current[0]) return false;

			// Compare minor version
			if (latest[1] > current[1]) return true;
			if (latest[1] < current[1]) return false;

			// Compare patch version
			if (latest[2] > current[2]) return true;

			return false;
		},
	},
	computed: {
		selectedBucket: function () {
			return this.$route.params.bucket;
		},
		selectedApp: function () {
			return this.$route.name?.split("-")[0] || "files";
		},
	},
	async mounted() {
		const resp = await fetch(
			"https://api.github.com/repos/G4brym/R2-Explorer/releases/latest",
		);
		if (!resp.ok) {
			console.log("Unable to retrieve latest r2-explorer updates :(");
			console.log(
				"Manually check them here: https://github.com/G4brym/R2-Explorer/releases",
			);
		} else {
			const parsed = await resp.json();
			const latestVersion = parsed.tag_name.replace("v", "");
			if (this.isUpdateAvailable(this.mainStore.version, latestVersion)) {
				this.latestVersion = latestVersion;
				this.updateAvailable = true;
			}
		}
	},
	setup() {
		const mainStore = useMainStore();

		return {
			mainStore,
		};
	},
});
</script>

<style scoped>
.q-btn {
  max-width: 100%;
  padding: 4px;
}
</style>

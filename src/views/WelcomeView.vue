<template>
  <div class="d-flex justify-content-center align-items-center vh-100 bg-light text-center">
    <div class="p-4 rounded shadow-sm bg-white" style="width: 400px;">
      <!-- Rasm -->
      <img
        src="https://em-content.zobj.net/source/telegram/386/waving-hand_1f44b.webp"
        loading="lazy"
        alt="Welcome"
        class="img-fluid rounded-circle mb-3"
        style="width: 150px; height: 150px;"
      />

      <!-- Xabar -->
      <h1 class="h5 mb-3 text-dark">
        {{ t("welcomeMessage") }}, {{ firstName }}!
      </h1>
      <p class="text-muted mb-4">{{ t("welcomeDescription") }}</p>

      <!-- Tekshirish holati -->
      <div v-if="loading" class="text-muted mb-3">
        ⏳ {{ t("loading") }}
      </div>
      <div v-if="errorMessage" class="text-danger mb-3">
        {{ errorMessage }}
      </div>

      <!-- Biometric xatolik -->
      <div v-if="biometricError" class="alert alert-warning mb-3">
        {{ t("biometricError") || "Biometric autentifikatsiya ishlamadi" }}
      </div>

      <!-- Biometric qayta urinish tugmasi -->
      <button
        v-if="biometricError && !loading"
        @click="retryBiometry"
        class="btn btn-warning btn px-4 me-2"
      >
        {{ t("retryBiometric") || "Qayta urinish" }}
      </button>

      <!-- Boshlash tugmasi -->
      <router-link
        v-if="showStartButton"
        to="/register"
        class="btn btn-primary btn px-4"
      >
        {{ t("start") }}
      </router-link>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import { useI18n } from "vue-i18n";
import apiLink from "@/config/api";
import { biometry } from '@tma.js/sdk-vue'

export default {
  name: "WelcomeView",
  setup() {
    const router = useRouter();
    const { t } = useI18n();
    const chatId = ref(null);
    const firstName = ref("Foydalanuvchi");
    const loading = ref(true);
    const errorMessage = ref("");
    const showStartButton = ref(false);
    const biometricError = ref(false);

    const attemptBiometry = async () => {
      try {
        const { status, token } = await biometry.authenticate({
          reason: 'Shaxsingizni tasdiqlang',
        });
        if (status === 'authorized') {
          biometricError.value = false;
          router.push("/documents");
        } else {
          biometricError.value = true;
        }
      } catch (error) {
        console.error("Biometry xatolik:", error);
        biometricError.value = true;
      }
    };

    const retryBiometry = async () => {
      loading.value = true;
      await attemptBiometry();
      loading.value = false;
    };

    const checkUser = async () => {
      try {
        const response = await axios.get(`${apiLink}/user/getChecked`, {
          params: { chatId: chatId.value },
          headers: getHeaders(),
        });

        if (response.status === 200) {
          if (response.data.data.checked === true || response.data.data.checked === "true") {
            await attemptBiometry();
          } else if (response.data.data.checked === false || response.data.data.checked === "false") {
            router.push("/sent");
          } else {
            showStartButton.value = true;
          }
        }
      } catch (error) {
        console.error("Xatolik:", error);
        errorMessage.value = t("serverError");
        if (error.status === 400) {
          errorMessage.value = "";
          showStartButton.value = true;
        }
      } finally {
        loading.value = false;
      }
    };

    onMounted(() => {
      if (window.Telegram?.WebApp) {
        const tg = window.Telegram.WebApp;
        tg.ready();
        tg.expand();

        if (tg.initDataUnsafe?.user) {
          chatId.value = tg.initDataUnsafe.user.id;
          firstName.value = tg.initDataUnsafe.user.first_name || t("defaultUser");
          checkUser();
        } else {
          errorMessage.value = t("userNotFound");
          loading.value = false;
          showStartButton.value = true;
        }
      } else {
        errorMessage.value = t("notTelegramEnvironment");
        loading.value = false;
        showStartButton.value = true;
      }
    });

    const getHeaders = () => {
      return {
        Authorization: "Basic " + btoa("admin:57325732"),
        "Content-Type": "application/json",
        "ngrok-skip-browser-warning": "true",
      };
    };

    return { firstName, loading, errorMessage, showStartButton, biometricError, retryBiometry, t };
  },
};
</script>

<style scoped>
.img-fluid {
  max-width: 100%;
  height: auto;
}

h1 {
  font-size: 1.25rem;
  font-weight: 600;
}

p {
  font-size: 0.9rem;
}

.btn-dark {
  background-color: #333;
  border-color: #333;
}

.btn-dark:hover {
  background-color: #000;
  border-color: #000;
}

.bg-light {
  background-color: #f8f9fa !important;
}

.shadow-sm {
  box-shadow: 0 0.125rem 0.25rem rgba(0, 0, 0, 0.075) !important;
}
</style>
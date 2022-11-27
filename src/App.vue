<script setup>
import MessageContainer from "@/components/Message/MessageContainer.vue";
import { useI18n } from "vue-i18n";
import Title from "@/components/utils/Title.vue";
import Header from "@/components/Header.vue";
import Footer from "@/components/Footer.vue";
import LoadingIcon from "./components/LoadingIcon.vue";
import { onMounted, ref } from "vue";
import { ENV_BASE_URL } from "./utils/env";

const { t } = useI18n();

// loading | error | success
const status = ref("loading");

const platformMap = {
  "afdian": "afdian",
  "patreon": "patreon"
};

const platformUnit = {
  "afdian": "￥",
  "patreon": "$"
};

const sponsorData = ref({});

onMounted(async () => {
  try {
    const data = await fetch(ENV_BASE_URL).then((res) => res.json());
    sponsorData.value = data
    status.value = "success";
  } catch (error) {
    console.error(error)
    status.value = "error";
  }
})
</script>

<template>
  <div class="main-wrapper">
    <Title title="title.main" />
    <Header></Header>
    <main class="main">
      <div class="summary">
        <div v-html="t('page.summary')"></div>
        <br>
        <div class="links-wrapper">
          {{ t('page.links.protips') }}
          &nbsp;
          <div class="links">
            <a href="https://www.patreon.com/daidr/membership" target="_blank" rel="noopener noreferrer">{{
                t('page.platform.patreon')
            }}</a> |
            <a href="https://afdian.net/a/daidr" target="_blank" rel="noopener noreferrer">{{
                t('page.platform.afdian')
            }}</a>
          </div>
        </div>
      </div>
      <template v-if="status === 'loading'">
        <section class="loading-wrapper">
          <LoadingIcon />
        </section>
      </template>
      <template v-else-if="status === 'error'">
        <section class="error-wrapper">
          {{ t('page.error') }}
        </section>
      </template>
      <template v-else-if="status === 'success'">
        <template v-for="platform of Object.keys(sponsorData)" :key="platform">
          <div class="sponsor-item" v-for="sponsor of sponsorData[platform]" :key="sponsor.name + sponsor.amount">
            <div class="left">
              <div class="avatar" :style="{
                backgroundImage: `url(${sponsor.avatar})`
              }">
              </div>
            </div>
            <div class="middle">
              <div class="name">{{ sponsor.name }}</div>
              <div class="platform">{{ t(`page.platform.${platformMap[platform]}`) }}</div>
            </div>
            <div class="right">
              <div class="amount">
                {{ platformUnit[platform] }}{{ sponsor.amount }}
              </div>
            </div>
          </div>
        </template>
      </template>
    </main>
    <Footer></Footer>
  </div>
  <MessageContainer />
</template>

<style lang="scss">
.summary {
  @apply col-span-12;

  li {
    @apply list-disc ml-7;
  }

  ul {
    @apply mb-3;
  }

  a {
    text-decoration: underline solid #55190222 2px;
    cursor: pointer;
    transition: all 0.15s ease-in-out;
    position: relative;

    &:hover {
      text-decoration: underline solid #ffffff00 2px;
      background: #55190222;
      border-radius: 10px;
      padding: 1px 10px;
    }
  }

  b {
    @apply font-bold;
  }
}
</style>

<style scoped lang="scss">
.toolbar {
  @apply select-none;
  @apply fixed top-0 right-0 mt-2 mr-2 flex gap-x-2;

  .lang-switch {
    select {
      @apply w-full h-full;
    }
  }
}

.main-wrapper {
  @apply max-w-1300px w-full min-h-100vh;
  @apply px-3 md: px-5;
  @apply flex flex-col;

  .main {
    @apply transition my-5 flex-grow;
    @apply gap-5;
    @apply grid grid-cols-12 items-start content-start;

    &>div {
      @apply bg-primary-light;
      @apply rounded-lg md: rounded-xl;
      @apply shadow-lg shadow-primary/10;
      @apply p-3 md: p-5;
    }

    .links-wrapper {
      @apply flex;
    }

    .sponsor-item {
      @apply "col-span-12 sm:col-span-6 lg:col-span-4";
      @apply flex select-none;

      .left {
        @apply mr-2;

        .avatar {
          @apply bg-cover;
          @apply rounded-lg;
          @apply w-12 h-12;
          @apply opacity-70;
        }
      }

      .middle {
        @apply flex flex-col justify-center;
        @apply flex-grow;
        @apply flex-col flex;
        @apply overflow-hidden;

        .name {
          @apply overflow-hidden;
          @apply font-bold text-lg text-ellipsis whitespace-nowrap;
        }

        .platform {
          @apply text-sm opacity-50;
        }
      }

      .right {
        @apply flex items-center text-lg;
      }
    }

    .loading-wrapper {
      @apply mt-10;
      @apply text-5xl col-span-12;
      @apply flex justify-center items-center;
    }

    .error-wrapper {
      @apply mt-10;
      @apply text-lg col-span-12;
      @apply flex justify-center items-center;
    }
  }
}
</style>

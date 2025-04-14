<template>
  <div class="knowledgegraph-container">
    <!-- 知识网络 -->
    <KnowledgeNetwork>
      <!-- Slot content for full-screen overlay -->
      <div class="fullscreen-overlay-content">
        <NodeCreationForm v-if="this.$store.state.displayNodeCreationForm"></NodeCreationForm>
        <LinkCreationForm v-else-if="this.$store.state.displayLinkCreationForm"></LinkCreationForm>
        <NodeInfo v-else></NodeInfo>
      </div>
    </KnowledgeNetwork>
    
    <div class="non-fullscreen-content">
      <NodeCreationForm v-if="this.$store.state.displayNodeCreationForm"></NodeCreationForm>
      <LinkCreationForm v-else-if="this.$store.state.displayLinkCreationForm"></LinkCreationForm>
      <NodeInfo v-else></NodeInfo>
    </div>
  </div>

  <!-- 动态推送 - 使用v-show控制显示与否 -->
  <div v-show="showFeedSection" class="feed-modal">
    <div class="feed-close-btn">
      <v-btn icon @click="showFeedSection = false">
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </div>
    
    <div class="feed-container">
      <v-container id="feed-section">
        <v-row>
          <v-col class="trend-cols">
            <v-btn class="split-color-container" variant="plain">
              <h1 class="split-color-text">{{ $t('header.trend') }}</h1>
            </v-btn>
          </v-col>
          <v-col class="feed-cols dot-lattice-container">
            <v-btn variant="plain">
              <h1 class="feed-text">{{ $t('header.production') }}</h1>
            </v-btn>
            <v-divider color="secondary" :thickness="2" opacity="1"></v-divider>
            <v-btn variant="plain">
              <h1 class="feed-text">{{ $t('header.Learning') }}</h1>
            </v-btn>
            <v-divider color="secondary" :thickness="2" opacity="1"></v-divider>
            <v-btn variant="plain">
              <h1 class="feed-text">{{ $t('header.research') }}</h1>
            </v-btn>
          </v-col>
          <v-col class="refresh-col">
            <v-btn variant="plain">
              <h2><v-icon class="refresh-text">mdi-refresh</v-icon></h2>
            </v-btn>
          </v-col>
        </v-row>
      </v-container>

      <v-divider color="darkred" :thickness="2" opacity="1" style="margin-top: 40px; margin-left: 120px"></v-divider>

      <v-spacer style="height: 60px"></v-spacer>

      <v-container>
        <v-row>
          <v-col v-for="media in mediaList" :key="media.id" cols="3">
            <mediaFeed :cover="media.cover" :title="media.title" :author="media.author" :date="media.date"></mediaFeed>
          </v-col>
        </v-row>
      </v-container>
      <!-- Final Footer -->
      <DefaultFooterBar />
    </div>
  </div>
</template>

<script>
import KnowledgeNetwork from '@/components/KnowledgeNetwork.vue'
import NodeInfo from '@/components/NodeInfo.vue'
import MediaFeed from '@/components/MediaFeed.vue'
import NodeCreationForm from '@/components/NodeCreationForm.vue'
import LinkCreationForm from '@/components/LinkCreationForm.vue'
import DefaultFooterBar from '@/components/DefaultFooterBar.vue'
import { eventBus } from '@/eventBus' // 导入事件总线

export default {
  name: 'HomePage',
  components: {
    KnowledgeNetwork,
    NodeInfo,
    MediaFeed,
    NodeCreationForm,
    LinkCreationForm,
    DefaultFooterBar,
  },
  data() {
    return {
      displayNodeCreationForm: false,
      isFullScreen: false,
      showFeedSection: false, // 控制动态推送区域显示/隐藏
      mediaList: [
        {
          id: 1,
          cover: require('@/assets/images/banner.png'),
          title: '动态推送功能开发中，敬请期待！',
          author: 'Sciencetopia团队',
          date: '2024-01-14',
        },
        {
          id: 2,
          cover: '',
          title: '',
          author: '',
          date: '',
        },
        {
          id: 3,
          cover: '',
          title: '',
          author: '',
          date: '',
        },
        {
          id: 4,
          cover: '',
          title: '',
          author: '',
          date: '',
        },
        // ... more media items ...
      ],
    }
  },
  created() {
    // 添加事件监听器，用于接收来自HeaderBar的指令
    eventBus.on('show-feed-section', this.showFeed)
  },
  beforeUnmount() {
    // 移除事件监听器
    eventBus.off('show-feed-section', this.showFeed)
  },
  methods: {
    showFeed() {
      this.showFeedSection = true
    }
  }
}
</script>

<style scoped>
@import '../assets/css/special-text.css';
@import '../assets/css/knowledge-graph.css';

/* 设置主容器高度为视口高度，禁止滚动 */
.knowledgegraph-container {
  height: 100vh;
  overflow: hidden;
  position: relative;
}

.fullscreen-overlay-content {
  position: fixed;
  top: 6vh;
  /* Offset from the top */
  right: 2vw;
  /* Offset from the right */
  width: 20vw;
  /* Fixed width */
  height: auto;
  /* Allows content to adjust based on its own size */
  max-height: 40vh;
  /* Restrict height if needed */
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  /* Align items to the right */
  justify-content: flex-start;
  /* Align items to the top */
  z-index: 100000;
  /* Ensures it's always on top */
  pointer-events: auto;
  /* Allows interactions */
}

.non-fullscreen-content {
  position: absolute;
  top: 24vh;
  /* Offset from the top */
  right: 20px;
  /* Offset from the right */
  width: 20vw;
  /* Fixed width */
  height: auto;
  /* Allows content to adjust based on its own size */
  max-height: 40vh;
  /* Restrict height if needed */
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  /* Align items to the right */
  justify-content: flex-start;
  /* Align items to the top */
  z-index: 10;
  /* Ensures it's always on top */
  pointer-events: auto;
  /* Allows interactions */
}

/* 动态推送模态框样式 */
.feed-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: white;
  z-index: 2000;
  /* 确保在最上层 */
  overflow-y: auto;
}

.feed-close-btn {
  position: absolute;
  top: 16px;
  right: 16px;
  z-index: 2001;
}

.feed-container {
  width: 100%;
  padding-top: 60px;
}

.trend-cols {
  position: absolute;
  left: 5vw;
}

.feed-cols {
  position: absolute;
  left: 1vw;
}

.refresh-col {
  position: absolute;
  left: 20vw;
  top: 5vh;
  width: 60px;
}

.refresh-text {
  color: #c8001d;
}
</style>

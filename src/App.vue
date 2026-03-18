<template>
  <div class="page">
    <div class="topbar">
      <div class="title">Umo Wrapper（导出到父页面）</div>

      <button class="btn" @click="exportToParent">
        导出到父页面（postMessage）
      </button>
    </div>

    <div class="editor-wrap">
      <!-- Umo Editor：高度必须给 -->
      <UmoEditor ref="umoRef" v-bind="options" />
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { UmoEditor } from "@umoteam/editor";

const umoRef = ref(null);

/**
 * 关键：导出 HTML
 * Umo Editor Next 文档里有 exportHTML 方法（download=false 时返回 HTML 字符串）。:contentReference[oaicite:1]{index=1}
 * 这里用 try/catch：如果你当前版本方法名不同，我会再根据报错帮你改成正确调用。
 */
const exportToParent = async () => {
  try {
    // 一般情况下：umoRef.value.exportHTML({ download:false, withWrapper:false, showBreakMarks:false })
    const html = await umoRef.value?.exportHTML?.({
      download: false,
      withWrapper: false,
      showBreakMarks: false,
    });

    if (!html) {
      alert("没有拿到 HTML（可能需要配置 export.styleURL 或方法名不同）。");
      return;
    }

    window.parent.postMessage(
      { type: "UMO_EXPORT", payload: { html, title: "Umo 导出" } },
      "*"
    );

    alert("已发送到父页面：UMO_EXPORT");
  } catch (e) {
    console.error(e);
    alert("导出失败：请看控制台报错，我帮你对齐正确导出方法。");
  }
};

const options = ref({
  locale: "zh-CN",
  // 建议：先用 web 页面模式（更适合你的网站文章）
  // 不同版本的配置字段可能略有差异：后面我们可按实际效果微调
});
</script>

<style>
.page {
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: #f3f4f6;
}

.topbar {
  height: 52px;
  background: #fff;
  border-bottom: 1px solid #e5e7eb;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 14px;
}

.title {
  font-size: 13px;
  font-weight: 600;
  letter-spacing: .08em;
  text-transform: uppercase;
}

.btn {
  border: 1px solid #111;
  background: #111;
  color: #fff;
  border-radius: 999px;
  padding: 8px 12px;
  cursor: pointer;
  font-size: 12px;
}

.editor-wrap {
  flex: 1;
  padding: 12px;
}

/* 给编辑器一个容器高度 */
.editor-wrap :deep(.umo-editor) {
  height: calc(100vh - 76px);
  background: #fff;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  overflow: hidden;
}
</style>

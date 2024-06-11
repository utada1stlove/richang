# 自定义样式

```css
html {
    background-image: url('https://bing.immmmm.com/img/bing?region=zh-CN&type=image');
    width: 100%;
    height: 100vh;
    background-position: center;
    background-size: cover;
    background-attachment: fixed;
}

.w-full.bg-zinc-100,
.bg-white,
.hover\:bg-white:hover,
.dark .dark\:bg-zinc-700,
.dark .dark\:hover\:bg-zinc-700:hover,
.memo-wrapper,
.bg-gray-200,
.dark .memo-wrapper,
.memo-editor-container {
    --tw-bg-opacity: 0.66 !important;
}

.dark header.dark\:bg-zinc-800,
aside.dark\:bg-zinc-800,
.bg-gray-100,
.dark html,
.dark body {
    --tw-bg-opacity: 0 !important;
}

.memo-editor-container>.memo-editor {
    background-color: transparent !important;
}

.status-text {
    font-size: 10px !important;
    border: none;
    color: rgb(156, 163, 175) !important;
}

.tag-span,
.dark .tag-span {
    border: 1px solid;
    border-radius: 6px;
    padding: 0px 6px;
    color: rgb(22, 163, 74) !important;
    font-size: 12px !important;
    -webkit-transform: scale(calc(10 / 12));
    transform-origin: left center;
}

.memo-content-text .link {
    color: rgb(22, 163, 74) !important;
    margin-right: -6px;
}

header .bg-blue-600 {
    display: none !important;
}

.text-lg {
    font-size: 1rem !important;
}

.header-wrapper,
.sidebar-wrapper {
    width: 11rem !important;
}

.filter-query-container {
    padding-bottom: 0.5rem;
}

body {
    font-family: "LXGW WenKai Screen", sans-serif !important;
}

/* 修改特殊字体颜色 */
.text-blue-600 {
    --tw-text-opacity: 1;
    color: rgb(11 96 176 / var(--tw-text-opacity));
}

/* 修改标签样式 */
.dark .inline-block {
    border: 1px solid rgb(110,110,110);
    border-radius: 8px;
    background-color: rgb(90, 90, 90);
    padding: 0px 4px;
}

.inline-block {
    border: 1px solid rgb(230,230,230);
    border-radius: 8px;
    background-color: rgb(245, 245, 245);
    padding: 0px 4px;
}

/* 修改Memo字号 */
.memo-wrapper .text-base {
    font-size: 0.95rem;
}

/* 修改代码块字号 */
.text-sm {
    font-size: 0.85rem;
}

/* 隐藏 通知 选项卡 */
#header-inbox {
  display: none;
}

/* 隐藏 个人资料 选项卡 */
#header-profile {
  display: none;
}

/* 隐藏 探索 选项卡 */
#header-explore {
  display: none;
}

/* 修改编辑器字体为等宽 */
textarea {
    font-family: 'Courier New', Courier, monospace;
}
```



# 自定义脚本

```java
function changeFont() { 
    const link = document.createElement("link");
    link.rel = "stylesheet";
    link.type = "text/css";
    link.href = "https://cdn.staticfile.org/lxgw-wenkai-screen-webfont/1.6.0/lxgwwenkaiscreen.css";
    document.head.append(link);
  };
  changeFont()

document.addEventListener('keydown', (e) => {
        if (e.key.toLowerCase() === 'q' && e.ctrlKey) {
            const sbutton = document.querySelector('.justify-end > button');
            if (sbutton) { sbutton.click(); }
            setTimeout(function() {
                const input = document.querySelector('.MuiInput-input');
                if (input) {  input.focus(); input.select(); }
            }, 100); 
        }
    });
```


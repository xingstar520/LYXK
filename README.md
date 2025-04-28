# 表白小游戏 ❤️

一个可爱的表白小游戏，用表情来表达你的心意。包含 HTML 和 Vue 两个版本，让你可以轻松地向心仪的人表白！

## 功能特点 ✨

- 可爱的表情动画，根据鼠标移动实时变化
- 浪漫的粉色主题，营造温馨氛围
- 有趣的交互效果，拒绝按钮会逃跑
- 响应式设计，适配各种设备
- 支持中英文双语
- 提供 HTML 和 Vue 两个版本

## 技术栈 🛠️

### HTML 版本

- 原生 HTML/CSS/JavaScript
- 使用 CSS 变量实现表情动画
- 使用 requestAnimationFrame 实现流畅动画

### Vue 版本

- Vue 3 + TypeScript
- Vite 构建工具
- 组件化开发

## 使用方法 📝

### HTML 版本

1. 直接打开 `html/index.html` 文件即可使用
2. 可以修改 `html/script.js` 中的文字内容来自定义表白语
3. 可以修改 `html/style.css` 中的颜色来自定义主题

### Vue 版本

1. 安装依赖：

```bash
npm install
# 或
pnpm install
```

2. 启动开发服务器：

```bash
npm run dev
# 或
pnpm dev
```

3. 构建生产版本：

```bash
npm run build
# 或
pnpm build
```

## 自定义配置 🎨

### 修改文字

在 `config.states` 中修改各个状态下的文字内容：

```javascript
states: {
  normal: {
    ui: {
      titleText: '我喜欢你',
      subtitleText: '可以和我在一起吗？'
    }
  },
  happy: {
    ui: {
      titleText: '太好了！',
      subtitleText: '我们在一起吧！'
    }
  },
  unhappy: {
    ui: {
      titleText: '不要拒绝我嘛',
      subtitleText: '再给我一次机会好不好'
    }
  }
}
```

### 修改颜色

在 CSS 文件中修改颜色变量：

```css
.face-slider {
  background-image: radial-gradient(#ffccd5, #ff4d6d);
  border: solid 2px #ff758f;
}
```

## 贡献指南 🤝

欢迎提交 Issue 和 Pull Request 来改进这个项目！

## 许可证 📄

MIT License

## 作者 👨‍💻

xingstar

## 致谢 😊😊

感谢所有使用和贡献这个项目的人！

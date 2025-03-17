1. 方案概述
目标：当用户选择不同的照片尺寸（如 100×150mm）时，URL 更新为 example.com/photo-size/100x150，但页面无需刷新，并且搜索引擎可以正确索引这些 URL。

2. 修改你的 size.html 代码
在 selectPreset() 函数中，每当用户选择尺寸时，更新 URL 并修改页面内容。

(1) 更新 URL（无刷新）
在 selectPreset() 函数中加入：

js
Kopieren
Bearbeiten
function selectPreset() {
    const preset = document.getElementById("presetSizes").value;
    const widthInput = document.getElementById("widthMM");
    const heightInput = document.getElementById("heightMM");

    if (preset === "custom") {
        widthInput.disabled = false;
        heightInput.disabled = false;
    } else {
        const [width, height] = preset.split(",").map(Number);
        widthInput.value = width;
        heightInput.value = height;
        widthInput.disabled = true;
        heightInput.disabled = true;
        drawRectangle();
    }

    // 更新 URL，但不刷新页面
    const newUrl = `/photo-size/${widthInput.value}x${heightInput.value}`;
    history.pushState({ width: widthInput.value, height: heightInput.value }, "", newUrl);
}
📌 原理：

history.pushState() 修改 URL，但不会刷新页面
widthInput.value 和 heightInput.value 作为路径参数，形成 SEO 友好的 URL
Google 会将 /photo-size/100x150 视为独立页面，提升索引效果
(2) 页面加载时，解析 URL 并自动选定尺寸
如果用户直接访问 example.com/photo-size/100x150，页面应当自动解析 URL 并显示正确尺寸：

js
Kopieren
Bearbeiten
window.onload = function() {
    // 解析 URL，检查是否包含尺寸参数
    const path = window.location.pathname;
    const match = path.match(/\/photo-size\/(\d+)x(\d+)/);
    
    if (match) {
        const width = match[1];
        const height = match[2];
        
        // 设置输入框值
        document.getElementById("widthMM").value = width;
        document.getElementById("heightMM").value = height;
        
        // 重新绘制矩形
        drawRectangle();
    }

    // 继续加载默认行为
    updateLanguage();
    updateSizes();
};
📌 这样，当用户访问 /photo-size/100x150，页面会自动解析 URL，并展示正确的尺寸。

3. SEO 相关优化
(1) 生成 <meta> 标签，增强搜索引擎抓取
每次 URL 变更时，更新 <title> 和 <meta>：

js
Kopieren
Bearbeiten
document.title = `${widthInput.value}x${heightInput.value} mm 照片真实大小`;
document.querySelector("meta[name='description']").setAttribute("content", `${widthInput.value}×${heightInput.value} mm 照片真实大小对比，直接在屏幕上查看！`);
(2) 在 <head> 添加 canonical，避免重复页面
html
Kopieren
Bearbeiten
<link rel="canonical" href="https://example.com/photo-size/100x150" />
📌 这样可防止 Google 认为 /photo-size/100x150 是 example.com 的重复页面，确保 SEO 友好。
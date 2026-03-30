#### Canvas（位图，像素级操作）:有空做个实践

```
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');  // 获取 2D 渲染上下文

// 基本图形绑制
ctx.fillStyle = '#ff0000';
ctx.fillRect(10, 10, 100, 50);        // 填充矩形
ctx.strokeStyle = '#0000ff';
ctx.strokeRect(10, 10, 100, 50);       // 描边矩形

// 路径绘制
ctx.beginPath();
ctx.moveTo(50, 50);                    // 移动到起点
ctx.lineTo(200, 50);                   // 画线到终点
ctx.lineTo(125, 150);                  // 继续画线
ctx.closePath();                       // 闭合路径
ctx.fill();                            // 填充
// ctx.stroke();                       // 或描边

// 圆弧
ctx.beginPath();
ctx.arc(200, 200, 50, 0, Math.PI * 2); // 圆心(200,200) 半径50 完整圆
ctx.fill();

// 文本
ctx.font = '24px Arial';
ctx.fillText('Hello Canvas', 100, 100);

// 图片
const img = new Image();
img.onload = () => ctx.drawImage(img, 0, 0, 200, 150);
img.src = 'photo.jpg';
```

#### SVG（矢量图，XML 描述）

```
<svg width="400" height="300" xmlns="http://www.w3.org/2000/svg">
  <!-- 矩形 -->
  <rect x="10" y="10" width="100" height="50" fill="red" stroke="blue" />
  
  <!-- 圆 -->
  <circle cx="200" cy="100" r="50" fill="green" />
  
  <!-- 线 -->
  <line x1="10" y1="200" x2="300" y2="200" stroke="black" stroke-width="2" />
  
  <!-- 路径（最灵活） -->
  <path d="M 10 80 Q 95 10 180 80" stroke="blue" fill="none" />
  
  <!-- 文本 -->
  <text x="100" y="250" font-size="20" fill="purple">Hello SVG</text>
  
  <!-- SVG 支持事件绑定！ -->
  <circle cx="300" cy="200" r="30" fill="orange" 
          onclick="alert('clicked!')" style="cursor: pointer" />
</svg>
```

```
Canvas vs SVG 对比：
┌───────────┬────────────────────┬────────────────────┐
│           │ Canvas             │ SVG                │
├───────────┼────────────────────┼────────────────────┤
│ 类型       │ 位图（像素）        │ 矢量（数学描述）    │
│ 缩放       │ 会模糊             │ 不失真             │
│ 元素数量   │ 大量元素性能好       │ 元素多了会卡       │
│ 事件       │ 不能绑定到图形上     │ 每个元素都能绑事件  │
│ 操作方式   │ JS 命令式绘制       │ DOM + CSS 声明式   │
│ SEO       │ 不友好             │ 可被搜索引擎读取    │
│ 适用场景   │ 游戏/粒子/大数据    │ 图标/图表/动画     │
│ 动画       │ requestAnimationFrame │ CSS/SMIL 动画    │
└───────────┴────────────────────┴────────────────────┘
```


# framerX

> 从底层圈上来交互圈，有时还真需要些辅助。

## 0-demos

### Frame

- `initial` 属性可以设置对象初始值，如果不要初始动画可以设 `false`
- `variants` 动画状态组，把几个动画对象放到一起
  - 先传 `variants` 里，再 `字符串` 方式用
- `custom` 可以向 `variants` 的函数对象传递变量

```js
const group = {
  begin: {
    x: -100,
    y: -100,
    opacity: 0.5,
  },
  end: {
    x: 200,
    y: 300,
    opacity: 0.5,
  },
  middle: i => ({
    x: 100 * i,
    y: 150 * i,
  }),
};

export function Card(props) {
  return (
    <Frame
      backgroundColor="#3d06ff"
      style={style}
      radius={20}
      custom={2.5}
      shadow={'0px 0px 10px #3d06ff'}
      variants={group}
      initial={'begin'}
      animate={'end'}
      animate={'middle'}
    >
      FRAMER
    </Frame>
  );
}
```

- 自动动画：页面加载完后自动播放的动画
- 受控动画：等待用户交互后才会播放的动画
  - 第一种实现方式：`useState`
  - 第二种实现方式：`variants`
  - 第三种实现方式：`useAnimation`
    - `.start`
    - `.stop`
    - `.set` 直接设置元素为指定状态，没有过度效果
- `useAnimation` 所返回的对象可以 `obj.start` 可以返回三种对象
  - `animationTarget`
  - `variants` 的 `label` 属性名
    - 如果要用这个 `label` 那就必须先把动画状态对象传递给 `variants`，然后就可以在 `.start` 中字符串方式用
  - 函数
    - 一定要返回动画对象
- 可以在两个不同元素上使用 useAnimation 捕获，然后 `.start` 时两个同时播放动画，且可以用 `custom` + 函数 设定范围

### 延迟动画

- `custom` + 函数对象返回 实现分布动画
  - 以后在 `map` 中设置 `custom` 属性来控制延迟

```js
const ani = i => ({
  rotate: 30,
  transition: {
    delay: i * 0.5,
  },
});

export function Card(props) {
  const controller = useAnimation();

  return (
    <div>
      <Frame
        backgroundColor="3d06ff"
        style={style}
        radius={20}
        shadow={'0px 0px 10px #3d06ff'}
        onTap={() => controller.start(ani)}
        animate={controller}
        custom={0}
      >
        Framer111
      </Frame>
      <Frame
        backgroundColor="3d06ff"
        style={style}
        radius={20}
        shadow={'0px 0px 10px #3d06ff'}
        onTap={() => controller.start(ani)}
        animate={controller}
        y={300}
        custom={1}
      >
        Framer222
      </Frame>
    </div>
  );
}
```

```js
// ----useState-
const [num, set] = React.useState(1)

onTap={() => {
  set(_num => _num + 1)
}

animate={{
    x: 100,
    y: num * 50,
    rotate: 30 * num,
}}

// ----useAnimation
const controller = useAnimation()
onTap={() => {
  controller.start({
      x: 111,
      y: 400,
  })
}}
animate={controller}
```

### 分布动画

- `async/await`

```js
async function stepAni() {
  await controller.start({ rotate: 30 });
  await controller.start({ rotate: 60 });
  await controller.start({ rotate: 90 });
  await controller.start({ rotate: 270 });
}

return (
  <div>
    <Frame
      backgroundColor="3d06ff"
      style={style}
      radius={20}
      shadow={'0px 0px 10px #3d06ff'}
      onTap={() => {
        stepAni();
      }}
      animate={controller}
      custom={0}
    >
      Framer111
    </Frame>
    <Frame
      backgroundColor="3d06ff"
      style={style}
      radius={20}
      shadow={'0px 0px 10px #3d06ff'}
      animate={controller}
      y={300}
      custom={1}
    >
      Framer222
    </Frame>
  </div>
);
```

- FramerX的样式布局
  - 在 FramerX 里给 Frame 设置宽高 100% 后也可以在界面大小自定义
  - 这是因为我们所有的自定义代码组件，在界面新建后，FramerX 都会为我们默认在最外层加一个框

### Graphic

![g1](http://cdn.jerryshi.com/20200329164933.png)

- 删除某个图片paths上的一条边
  - 双击图形打开锚点编辑界面
  - 轻点某条边出现浅蓝色即可点击删除
- 图形支持翻转：左右和上下
- 点解界面 `Fill` 可以选择是否导出图形背景色，默认是 `exclude`

---

### Page

![p1](http://cdn.jerryshi.com/666.gif)

- 页面轮播效果
  - `Overflow: show`
  - `Gap: 30`

### Util

- `FramerX` 中不支持类似 `cmd+G` 针对两个 `Frame` 快速打组，要么手动创建外出元素拖入，要么框选两个要打组的元素然后 `cmd+Enter`
  - `Frame` 会自动识别拖入容器元素的子元素，并自动打组，如果不想这样，可以按住 `cmd` 拖入就不会自动分组
  - 同理，可以按住 `cmd` 键点选容器内的子元素，则可以有穿透的效果，直接点选到子元素

## 1-FramerX

### Part I

- cmd + d = 复制该元素
- 选择某个 layer 后上安装 [alt] 即可显示到达边缘距离

![home-icon](http://cdn.jerryshi.com/20200328171743.png)

- 创建一个 `[Home]` 图标
  - `[g]` 进入图形编辑模式，拖一个多边形，改成五边形
  - `[enter]` 进入 path 编辑，把底边两点和腰上两点垂直
  - 画个小长方形 `[门]` 放好后，选择两个图形，模式为 `[subtarct]`
  - 在该图形下的 `[Substract]` 上选择 `[flatten]`，使两个图形扁平化(俩图形变成路径)
  - 选择该路径 `[Path]` 后按 `[enter]` 进入路径点编辑模式
  - 按住 `[shift]` 选择门的下边缘两个点，设置 radius 即可完成
- 点击 设备标题 [Apple iPhone XS] 后即可拖动屏幕宽度

### Part II

- 在颜色选择器 `[linear]` 模式下，按 `[ctl + c]` 可以快速启动吸色管
- 在 `Frame` 中，如果某个元素超过超出了圆角部分，可以设置 `[hidden]` 属性
- 在 `Frame` 上，设置 `[Create Component]` 即可使该 frame 成为一个可复用的组件
  - 成为 “组件”后，可以勾选子组件合为一体
- Text 元素如果在某容器内，最好设置 `[fixed]` 属性，否则容易发生位移
- `[icon-generator]` 包的图标需要到对应网站上去找：`Feather`、`Material`、`Font` `Awesome`
- 如果一个组件在多个页面中有交互功能，复制前先将它设置为 `Component`
- 同样的 `Home` 页 `Card` 组件在复用前，先设置 `[Create Component]`

## 2-FramerX Playground

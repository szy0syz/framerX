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

## 1-FramerX

### Part I

- cmd + d = 复制该元素
- 选择某个 layer 后上安装 [alt] 即可显示到达边缘距离

![home-icon](http://cdn.jerryshi.com/20200328171743.png)

- 创建一个 [Home] 图标
  - [g] 进入图形编辑模式，拖一个多边形，改成五边形
  - [enter] 进入 path 编辑，把底边两点和腰上两点垂直
  - 画个小长方形 [门] 放好后，选择两个图形，模式为 [subtarct]
  - 在该图形下的 [Substract] 上选择 [flatten]，使两个图形扁平化(俩图形变成路径)
  - 选择该路径 [Path]后按 [enter]进入路径点编辑模式
  - 按住 [shift] 选择门的下边缘两个点，设置 radius 即可完成
- 点击 设备标题 [Apple iPhone XS] 后即可拖动屏幕宽度

## 2-FramerX Playground

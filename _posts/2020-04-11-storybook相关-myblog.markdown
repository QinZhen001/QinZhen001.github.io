---
layout:     post
title:      "storybook相关"
date:       2020-04-11 16:20:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Other
---

> “Yeah It's on. ”
>



>storybook目前没有比较好的中文文档，只能啃英文文档，omg




# 正文

[手把手教你快速搭建专属的storybook]( https://juejin.im/post/5c307916e51d4551e418baa0#heading-7 )

 https://storybook.js.org/ 

 Storybook is an open source tool for developing UI components in isolation for React, Vue, and Angular. It makes building stunning UIs organized and efficient. 

Storybook是一个开源工具，用于为React，Vue和Angular隔离开发UI组件。 它使构建令人惊叹的UI井井有条且高效。



###  preview.js 

> preview.js 在.storybook文件夹

You might be using global components or vue plugins such as vuex, in that case you’ll need to register them in this preview.js file.

```js
import { configure } from '@storybook/vue';

import Vue from 'vue';

// Import Vue plugins
import Vuex from 'vuex';

// Import your global components.
import Mybutton from '../src/stories/Button.vue';

// Install Vue plugins.
Vue.use(Vuex);

// Register global components.
Vue.component('my-button', Mybutton);

configure(require.context('../src', true, /\.stories\.js$/), module);
```

This example registered your custom `Button.vue` component, installed the Vuex plugin, and loaded your Storybook stories defined in `../src/index.stories.js`.

All custom components and Vue plugins should be registered before calling `configure()`.



### Storybook for Vue

Storybook for Vue is a UI development environment for your Vue components. With it, you can visualize different states of your UI components and develop them interactively.

Vue的Storybook是Vue组件的UI开发环境。有了它，您可以可视化UI组件的不同状态，并交互式地开发它们。


 Storybook runs outside of your app. So you can develop UI components in isolation without worrying about app specific dependencies and requirements.

Storybook在你的应用程序之外运行，所以你可以独立开发UI组件，而不用担心应用程序特定的依赖和需求。





### **Webpack 5 support**

[https://storybook.js.org/blog/storybook-for-webpack-5/](https://storybook.js.org/blog/storybook-for-webpack-5/)

The Webpack 5 upgrade is Storybook's most upvoted  Github issue. Since 6.2 is a minor release, Webpack 4 is still the default builder. But you can start using Webpack 5 by opting in.

For a fresh Storybook install:

```bash
npx sb init --builder webpack5
```

For an existing Storybook, upgrade to the latest version, add `@storybook/builder-webpack5`, and set the `core.builder` configuration in `.storybook/main.js` ([full instructions](https://gist.github.com/shilman/8856ea1786dcd247139b47b270912324#upgrade)).









## 问题



### addon-info



####  vue中使用addon-info 



[ https://github.com/storybookjs/storybook/issues/4770 ]( https://github.com/storybookjs/storybook/issues/4770 )

Installed `@storybook/addon-info` to work with a Vue app, but I've been unable to get it working as it seems to break storybook as it renders the error `Cannot add property components, object is not extensible`.

**原来是 addon-info 只支持react框架，不支持其他。**

 The official `addon-info` is platform specific, and currently has only React implementation

 官方的addon-info是特定于平台的，目前只有React实现

[ https://github.com/storybooks/storybook/blob/next/ADDONS_SUPPORT.md ]( https://github.com/storybooks/storybook/blob/next/ADDONS_SUPPORT.md )



#### Warning: Failed prop type

出现Warning

```js
checkPropTypes.js:20 Warning: Failed prop type: Invalid prop `type` of type `symbol` supplied to `TableComponent`, expected `function`.
    in TableComponent (created by Story)
    in Story (created by storyFn)
    in storyFn
    in ErrorBoundary
```

定位错误:

```tsx
storiesOf("Input component", module)
  .add("大小不同的 Input", sizeInput)

const sizeInput = () => (
  <>
    <Input
      style={{width:'300px'}}
      defaultValue="large size"
      size='lg'
    ></Input>
    <Input
      style={{width:'300px'}}
      placeholder="small size"
      size='sm'  
    ></Input>
  </>
);
```

这会导致addon-info出现"Unknown" Component

**No propTypes defined!**  (sizeInput 并不是返回一个组件)





解决:

```tsx
const sizeInput = () => (
    <Input
      style={{width:'300px'}}
      placeholder="small size"
      size='sm'  
    ></Input>
);
```

这样就可以推到出类型。





### load twice

[ https://github.com/storybookjs/storybook/issues/7636 ]( https://github.com/storybookjs/storybook/issues/7636 )



出现load twice的问题



```js
Unexpected loaded state. Did you call `load` twice?



Story with id pc端组件--to-storybook already exists in the store!



Perhaps you added the same story twice, or you have a name collision?
Story ids need to be unique -- ensure you aren't using the same names modulo url-sanitization.
```





Calling `configure` from `@storybook/react` more than once in your `.storybook/config.js` file works just fine in dev mode. All stories for each call to `configure` are present in the storybook.



翻译成白话文就是configure被调用了两次，检查一下





### addon-actions



#### action无法触发

错误的写法：

action无法触发

```tsx
import { action } from "@storybook/addon-actions";
 
<Menu onSelect={(index) => action(`asdsd ${index}`)}>
    <MenuItem>cool link</MenuItem>
    <MenuItem disabled>disabled</MenuItem>
    <MenuItem>cool link 2</MenuItem>
    <MenuItem>cool link 3</MenuItem>
    <MenuItem>cool link 4</MenuItem>
  </Menu>
```

解决方案:

```tsx
<Menu onSelect={action('selected')}>
    <MenuItem>cool link</MenuItem>
    <MenuItem disabled>disabled</MenuItem>
    <MenuItem>cool link 2</MenuItem>
    <MenuItem>cool link 3</MenuItem>
    <MenuItem>cool link 4</MenuItem>
  </Menu>
```





###  Unexpected character '@'   （scss）

遇到一个问题:

```
ERROR in ./src/styles/index.scss 2:0
Module parse failed: Unexpected character '@' (2:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| // config
> @import 'variables';
| 
| //layout
 @ ./.storybook/config.tsx 7:0-34
 @ multi ./node_modules/@storybook/core/dist/server/common/polyfills.js ./node_modules/@storybook/core/dist/server/preview/globals.js ./.storybook/config.tsx (webpack)-hot-middleware/client.js?reload=true&quiet=true
```

用了非常多的时间，定位到scss文件中@import问题

那怎么解决呢？



**在.storybook文件夹下的webpack.config.js增加**

```js
module.exports = ({ config }) => {

  // 增加scss后缀
  config.resolve.extensions.push('.ts', '.tsx', '.scss', '.css')

  // 处理scss文件
  config.module.rules.push({
    test: /\.(sa|sc)ss$/,
    use: ['style-loader', 'css-loader', 'sass-loader'],
  })

  return config
}
```

最后完美解决问题，其实也就是配置了处理scss文件




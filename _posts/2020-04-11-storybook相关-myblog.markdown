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





## 爬坑





###  vue中使用addon-info 



[ https://github.com/storybookjs/storybook/issues/4770 ]( https://github.com/storybookjs/storybook/issues/4770 )





Installed `@storybook/addon-info` to work with a Vue app, but I've been unable to get it working as it seems to break storybook as it renders the error `Cannot add property components, object is not extensible`.



**原来是 addon-info 只支持react框架，不支持其他。**



 The official `addon-info` is platform specific, and currently has only React implementation

 官方的addon-info是特定于平台的，目前只有React实现





[ https://github.com/storybooks/storybook/blob/next/ADDONS_SUPPORT.md ]( https://github.com/storybooks/storybook/blob/next/ADDONS_SUPPORT.md )







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




 
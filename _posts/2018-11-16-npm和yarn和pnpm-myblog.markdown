---
layout:     post
title:      "npm和yarn和pnpm"
date:       2018-11-16 20:19:00
author:     "Qz"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Package
---

> “Yeah It's on. ”


# npm

[https://www.cnblogs.com/penghuwan/p/6973702.html#_label4](https://www.cnblogs.com/penghuwan/p/6973702.html#_label4)

[https://www.ruanyifeng.com/blog/2016/10/npm_scripts.html](https://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)

**npm 脚本的原理非常简单。每当执行`npm run`，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面。**



比较特别的是，`npm run`新建的这个 Shell，会将当前目录的`node_modules/.bin`子目录加入`PATH`变量，执行结束后，再将`PATH`变量恢复原样。



npm 脚本的退出码，也遵守 Shell 脚本规则。如果退出码不是`0`，npm 就认为这个脚本执行失败。



## 参数

向 npm 脚本传入参数，要使用`--`标明。

```
"lint": "jshint **.js"
```

向上面的`npm run lint`命令传入参数，必须写成下面这样。

```
$ npm run lint --  --reporter checkstyle > checkstyle.xml
```





### --if-present

[https://stackoverflow.com/questions/50683885/how-to-check-if-npm-script-exists](https://stackoverflow.com/questions/50683885/how-to-check-if-npm-script-exists)

```bash
npm run test --if-present
```

 if you only want to run the command if it exists, npm has an option for that

如果你只想在命令存在的情况下运行它，npm有一个选项

`--if-present` 选项允许在 脚本命令存在时执行，而在不存在时不报错，直接退出命令。这在某些情况下可以帮助绕过因缺少必须的脚本命令而导致的错误。





## npm unpublish

[https://docs.npmjs.com/unpublishing-packages-from-the-registry](https://docs.npmjs.com/unpublishing-packages-from-the-registry)

撤销已经发布的包

```bash
npm unpublish 包名 --force
```



Unpublishing a single version

```bash
npm unpublish <package-name>@<version>
```





### npm ci

[https://stackoverflow.com/questions/63187000/what-is-the-npm-equivalent-of-yarn-install-frozen-lockfile](https://stackoverflow.com/questions/63187000/what-is-the-npm-equivalent-of-yarn-install-frozen-lockfile)

npm ci bypasses a package’s package.json to install modules from a package’s lockfile. This ensures reproducible builds—you are getting exactly what you expect on every install.

npm ci 绕过包的 package.json 从包的锁定文件安装模块。这确保了可重复的构建——您在每次安装时都能得到您所期望的结果。





## npm link

[https://docs.npmjs.com/cli/link](https://docs.npmjs.com/cli/link)

First, npm link in a package folder will create a symlink in the global folder `{prefix}/lib/node_modules/<package>`  that links to the package where the npm link command was executed. 



这个命令的作用就是在全局环境下，生成一个符号链接文件，该文件的名字就是package.json文件中指定的模块名。同时我们对此模块的修改会实时反馈在全局目录下。



## npm init 

[https://www.npmjs.cn/cli/init/](https://www.npmjs.cn/cli/init/)

[https://docs.npmjs.com/cli/v6/commands/npm-init](https://docs.npmjs.com/cli/v6/commands/npm-init)

`npm init <initializer>` can be used to set up a new or existing npm package.



`initializer` in this case is an npm package named `create-<initializer>`, which will be installed by [``](https://npm.im/npx)`npx`, and then have its main bin executed -- presumably creating or updating `package.json` and running any other initialization-related operations.



The init command is transformed to a corresponding `npx` operation as follows:

 init命令转换为对应的npx操作，如下所示

- `npm init foo` -> `npx create-foo`
- `npm init @usr/foo` -> `npx @usr/create-foo`
- `npm init @usr` -> `npx @usr/create`



## npm version

[https://blog.csdn.net/weixin_40817115/article/details/90384398](https://blog.csdn.net/weixin_40817115/article/details/90384398)

[规范升级 NPM 包](https://juejin.cn/post/7143025612267978760)

`npm version`获取当前的详细版本信息。

```tsx
{ 'npm-version-test': '1.0.0',
  npm: '6.4.1',
  ares: '1.14.0',
  cldr: '33.1',
  http_parser: '2.8.0',
	// ....
  zlib: '1.2.11' }
```

```
npm version prerelease // 1.0.1-0 变为 1.0.1-1
npm version prepatch   // 1.0.1-1 变为 1.0.2-0
npm version preminor   // 1.0.2-0 变为 1.1.0-0
// ...  
```





## npm publish

如果直接使用 `npm publish` 来发布的话，发布出来的包的 tag 是 latest

只有使用 `npm publish --tag XXX` 才是给 npm 包上打了 tag 标签。

```tsx
npm run build // 打包
npm publish --tag beta // 发布 beta 包
```

如果不小心直接使用 `npm publish` 发错了也没有关系，可以使用以下命令来添加 tag：

```
npm dist-tag add <pkg>@2.3.2-beta.1 <tag>
```

当需要删除多余的 tag 时：

```
npm dist-tag rm <pkg> <tag>
```

一条指令完成 beta 版本的发布：

```tsx
"scripts": {
    "publish:beta": npm version prerelease --preid=beta && npm run build && npm publish --tag=beta"
  },
```



## npm ci

> **尤其在 CI/CD 流程中请使用npm ci**

[https://juejin.cn/post/7089058201814958116](https://juejin.cn/post/7089058201814958116)

- 首先，它会删除 `node_modules` 文件夹；
- 然后，依照 `package-lock.json`（或 `npm-shrinkwrap.json`）文件 创建新得`node_modules` 文件夹并**精准安装对应的依赖版本**；

`npm i` 不能精准安装依赖主要是因为`package.json`文件里面的依赖版本往往是一个范围，并不是一个固定的版本，它允许依赖的升级；而`package-lock.json` 文件，是对`package.json`文件，做了依赖的版本检查以及梳理后生成的，所以`npm ci`能够快速精确的安装依赖；





## npm create

[https://dev.to/mikhaelesa/create-your-own-npm-create-cli-like-create-vite-3ig7](https://dev.to/mikhaelesa/create-your-own-npm-create-cli-like-create-vite-3ig7)

[npm create命令解析](https://juejin.cn/post/7241514309717573692)

**也就是说npm create其实就是npm init的别名**

**`npm` 将在你提供的初始项前拼接 `create-` 然后使用`npx`工具下载并执行该方法**，也就是说

```bash
npm create vite
// 等同于
npm init vite
// 等同于
npx create-vite
// 等同于
npm install create-vite -g
create-vite
```



### @latest

[https://stackoverflow.com/questions/73594809/what-is-the-difference-between-npm-init-vitelatest-and-npm-init-vite](https://stackoverflow.com/questions/73594809/what-is-the-difference-between-npm-init-vitelatest-and-npm-init-vite)

我们用 create-vite 举例子

IF you had already the `create-vite` package **globally** installed, and for example, a **older version**, it will be what `npm init` uses.

当你全局已经有安装create-vite，npm create vite/npm init 会去找全局的这个create-vite而不会用最新的版本

fetches and runs the latest

所以要获取和运行最新的话使用

```bash
npm create vite@latest
```







## user-agent

[https://docs.npmjs.com/cli/v7/using-npm/config#user-agent](https://docs.npmjs.com/cli/v7/using-npm/config#user-agent)

```
Default: "npm/{npm-version} node/{node-version} {platform} {arch} workspaces/{workspaces} {ci}"
```

通过这个可以判断当前是否是使用 npm

```
process.env.npm_config_user_agent
```







## workspaces

npm workspaces是npm 7.0.0版本中新引入的功能，它允许您在具有相互依赖关系的多个包之间进行协同工作。

使用npm workspaces，您可以在一个项目中管理多个包，这些包可以同时具有公共依赖关系。一种常见的用例是在一个大型代码库中，将多个相关的模块或功能作为独立的包进行开发和管理。

在使用npm workspaces时，您只需在项目的根目录下创建一个`package.json`文件，并在其中声明`"workspaces"`属性，然后在该属性的值中列出您要包含的包的路径。例如：

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "private": true,
  "workspaces": [
    "packages/*"
  ]
}
```

上面的例子中，`packages/*`表示项目中所有位于`packages`目录下的包都将被包含在工作区中。

当您使用`npm install`命令时，npm会自动将所有被声明的工作区中的包安装到项目的`node_modules`目录下，而不是每个包都有自己的



## 设置自动执行的默认值

我们通常在一个项目中使用npm init来初始化package.json文件。如果你想把经常用到的一些值搞成默认配置，可以使用config set命令。


```javascript
npm config set init.author.name $name
npm config set init.author.email $email
```

## **legacy-peer-deps**

[https://juejin.cn/post/6971268824288985118](https://juejin.cn/post/6971268824288985118)

[https://www.coder.work/article/7517082](https://www.coder.work/article/7517082)

> 在NPM v7中，现在默认安装**peerDependencies**。

*--legacy-peer-deps*标志是在*v7*中引入的，目的是绕过**peerDependency**自动安装；它告诉 NPM 忽略项目中引入的各个**modules**之间的相同**modules**但不同版本的问题并继续安装，保证各个引入的依赖之间对自身所使用的不同版本**modules**共存。



`--legacy-peer-deps` :安装时忽略所有 peerDependencies，采用 npm 版本 4 到版本 6 的样式。

`--strict-peer-deps` :在遇到任何冲突的 peerDependencies 时失败并中止安装过程。默认情况下，npm 只会因根项目直接依赖导致的 peerDependencies 冲突而崩溃。





## npm-access


[https://www.npmjs.cn/cli/access/](https://www.npmjs.cn/cli/access/s)

Set access level on published packages

设置已发布包的访问级别


For all of the subcommands, npm access will perform actions on the packages in the current working directory if no package name is passed to the subcommand.


对于所有子命令，如果没有将包名传递给子命令，npm访问将对当前工作目录中的包执行操作。



public / restricted: Set a package to be either publicly accessible or restricted.


默认为:restricted

还可以在package.json里面配置


```

...
"publishConfig": {
		"access": "public"
	}
```

## 串行或并行运行多个命令

[https://www.html.cn/archives/8029](https://www.html.cn/archives/8029)

package.json 代码:

并行：

```JavaScript
"lint": "eslint & csslint & htmllint"
```

串行:

```JavaScript
"build": "babel; jest"
```







## npm-run-all

```
"lint": "eslint & csslint & htmllint"
```

它会工作很好，但是这种方法有一个相当大的问题。 `＆`语法会创建一个子进程，这会导致无法判断原始的 `npm` 进程是否已经完成。这可能是有问题的，特别是长时间运行 scripts 时。



为了使事情更加一致，我们可以使用一个名为 `npm-run-all` 的包。它提供了额外的命令，更具体地说就是，用 `run-s` 来运行串联任务， 用 `run-p` 来运行并行任务，它将正确处理所有的子进程。



并行：

```
"lint": "run-p eslint csslint htmllint"
```

串行:

```
"build": "run-s babel jest"
```



## 当命令失败时执行

`;` 语法会让下一个命令等待，直到上一个命令完成后。也就是说上一个命令完成后，再运行下一个命令，不管上一个命令的退出代码是什么，下一个命令始终会执行。



而我们想要的是，如果任何一个串联命令失败，则停止后续的所有命令执行。

要改成这样，我们只需使用 `&&` 代替 `;` 即可：

```
"build": "babel && jest"
```

现在，如果 `babel` 以除了 `0` 以外的代码退出，这意味着该进程会被中止，`jest` 将永远不会运行。



## 运行命令组

**npm 中的命名约定使用冒号对整组特定任务进行分组**

```
"lint": "eslint & csslint & htmllint"
```

我们可以做的是将他们分隔成单独的任务（例如，如果我们需要添加一些 flags 标志来配置它们）,并将它们组合在一起。

我们所能做的是将每一个单独的(例如，我们需要添加一些标记来配置它们)并将它们组合在一起。

```
"lint": "npm run lint:js & npm run lint:css & npm run lint:html",
"lint:js": "eslint --some-flag",
"lint:css": "csslint --that-will-change",
"lint:html": "htmllint --how-things-work"
```

在这个更改之后，它将以同样的方式工作，但是现在我们可以同时运行它们，或者在需要的时候也可以分别单独运行。

为了使其更加清洁，我们可以再次使用 `npm-run-all` ，并将我们的主要 `lint` 命令更改为 `npm-run-all lint:*` ，然后它将匹配 `lint:` 分组中的所有 scripts 。



## 为你的 NPM 编写自定义检查

举个例子：确保用户指定了所有必需的 `ENV` 环境变量，或者在尝试运行命令名时不包含任何拼写错误。

```
"build": "node ./scripts/env-check.js && rimraf dist && webpack --bail --progress --profile --display-error-details",
"build:development": "NODE_ENV=development npm run build",
"build:staging": "NODE_ENV=staging npm run build",
"build:production": "NODE_ENV=production npm run build",
```



```js
// env-check.js

const task = process.env.npm_lifecycle_event
const packageJSON = require('../package.json')
const availableEnvironments = Object.keys(packageJSON.scripts)
  .filter(key => key.startsWith(task))
  .map(key => key.split(':')[1])
  .filter(key => key)
 
if (!process.env.NODE_ENV) {
  console.error(`[ Error ] NODE_ENV is required. Use ${task}:${availableEnvironments.join('/')} scripts instead.`)
  process.exit(1)
}
 
if (!availableEnvironments.includes(env)) {
  console.error(`[ Error ] ${env} is not valid NODE_ENV. Use ${task}:${availableEnvironments.join('/')} scripts instead.`)
  process.exit(1)
}
 
process.exit(0)
```

现在，每当开发人员输入 `npm run build` 时，该提示将显示：

```
[ Error ] NODE_ENV is required. Use build:development/staging/production scripts instead.
```

上面的代码有个缺点是，没有使用 `pre` script

```
"prebuild": "node ./scripts/env-check.js && rimraf dist",
"build": "webpack --bail --progress --profile --display-error-details",
```

但是，这样子task变量就不对了，因为 `process.env.npm_lifecycle_event` 不会返回 `build` 名称，而是返回 `prebuild`。



我们必须要有所改造

```js
const task = process.env.npm_lifecycle_event.startsWith('pre') ? process.env.npm_lifecycle_event.slice(3) : process.env.npm_lifecycle_event
```

现在，我们可以在任何 `pre` script 中放置 `node ./scripts/env-check.js`，它将为我们执行这些所有的初步检查。
















# yarn 



## yarn install

[https://classic.yarnpkg.com/lang/en/docs/cli/install/](https://classic.yarnpkg.com/lang/en/docs/cli/install/)



### --frozen-lockfile

[https://classic.yarnpkg.com/lang/en/docs/cli/install/#toc-yarn-install-frozen-lockfile](https://classic.yarnpkg.com/lang/en/docs/cli/install/#toc-yarn-install-frozen-lockfile)

If you need reproducible dependencies, which is usually the case with the continuous integration systems, you should pass --frozen-lockfile flag.

如果您需要可重现的依赖关系（持续集成系统通常就是这种情况），您应该传递 --frozen-lockfile 标志。

`yarn install --frozen-lockfile` 的作用是在安装依赖包时，如果 package-lock.json 文件被修改过并且与 yarn.lock 文件不匹配，Yarn 会提示错误并停止安装。这个命令可以用来确保安装过程中不会意外改变 package-lock.json 文件，保持它的一致性。





## yarn的优势

* **并行执行**

npm会等一个包完全安装完才跳到下一个包，但yarn会并行执行包，因此速度会快很多

* **离线模式**

离线的原理比较简单，安装过的包会被保存进缓存目录，以后安装就直接从缓存中复制过来，这样做的本质还是会提高安装下载的速度，避免不必要的网络请求。

* **版本控制**

npm用下来比较强的一个痛点就是：当包的依赖层次比较深时，版本控制不够精确。会出现相同package.json，但不同人的电脑上安装出不同版本的依赖包，出现类似 “我电脑上是好的，没问题呀”的bug很难查找。你可以使用[npm-shrinkwrap](https://link.jianshu.com/?t=https://docs.npmjs.com/cli/shrinkwrap)来实现版本固化，版本信息会写入npm-shrinkwrap.json文件中，但它毕竟不是npm的标准配置。

而yarn天生就能实现版本固化。会生成一个类似npm-shrinkwrap.json的yarn.lock文件，文件内会描述包自身的版本号，还会锁定所有它依赖的包的版本号：yarn.lock存储这你的每个包的确切依赖版本，能确保从本地开发到生产环境，所有机器上都有精确相同的依赖版本。







## workspace

* 能帮助你更好地管理多个子project的repo，这样你可以在每个子project里使用独立的package.json管理你的依赖，又不用分别进到每一个子project里去yarn
* install/upfrade安装/升级依赖，而是使用一条yarn命令去处理所有依赖就像只有一个package.json一样
* yarn会根据就依赖关系帮助你分析所有子project的共用依赖，保证所有的project公用的依赖只会被下载和安装一次。



使用：

yarn workspace并不需要安装什么其他的包，只需要简单的更改package.json便可以工作。 首先我们需要确定workspace root，一般来说workspace root都会是repo的根目录



yarn workspace目录结构树:

![](https://upload-images.jianshu.io/upload_images/12564775-6eb7783e32e44b13.png?imageMogr2/auto-orient/strip|imageView2/2/w/921/format/webp)

package.json:

```js
{
    //当private为true时workspace才会被启用
    "private": true，
    "workspace": ["workspace-a","workspace-b"]
}

// workspaces属性的值为一个字符串数组，每一项指代一个workspace路径，支持全局匹配，这里的路径指向指的是package.json所在文件夹文件夹名。
```

你会发现整个repo只生成了一份yarn.lock，绝大多数的依赖包都被提升到了根目录下的node_modules之内。各个子project的node_modules里面不会重复存在依赖，只会有针对根目录下依赖的引用



### workspace的不足

* yarn workspace并没有像lerna那样封装大量的高层API，整个workspace整体上还是依赖于整个yarn命令体系。
* workspace不能嵌套（只能有一个根workspace）
* workspace采用的是向上遍历，所以workspace并不能识别根workspace之外的依赖。





## resolutions

[https://classic.yarnpkg.com/lang/en/docs/selective-version-resolutions/](https://classic.yarnpkg.com/lang/en/docs/selective-version-resolutions/)

`resolutions` 字段是 `yarn` 包管理器的一个特性，允许你在 `package.json` 文件中指定项目中依赖的特定包的版本，解决依赖包版本冲突的问题。通过使用 `resolutions` 字段，你可以更好地控制项目中使用的包版本，确保项目的依赖关系符合你的预期。

`resolutions` 字段允许你指定项目中依赖的包的特定版本，覆盖依赖包的版本。这是 `yarn` 的特性，`npm` 并不支持。

示例

假设你的项目依赖的某个包 `packageA`，而 `packageA` 又依赖 `packageB` 的某个版本。你希望在你的项目中强制使用 `packageB` 的特定版本。这时你可以在 `package.json` 文件中使用 `resolutions` 字段。

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "dependencies": {
    "packageA": "^1.0.0"
  },
  "resolutions": {
    "packageB": "2.0.0"
  }
}
```

在这个例子中，`resolutions` 字段强制项目中所有依赖 `packageB` 的包使用 `packageB` 的 `2.0.0` 版本。

解决依赖冲突

假设你的项目依赖 `packageA` 和 `packageC`，而 `packageA` 依赖 `packageB` 的 `1.0.0` 版本，`packageC` 依赖 `packageB` 的 `2.0.0` 版本。你希望在项目中强制使用 `packageB` 的 `2.0.0` 版本。

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "dependencies": {
    "packageA": "^1.0.0",
    "packageC": "^1.0.0"
  },
  "resolutions": {
    "packageB": "2.0.0"
  }
}
```

具体的 `resolutions` 规则

你可以使用更具体的规则来指定特定的依赖关系。例如，强制 `packageA` 依赖的 `packageB` 版本。

```json
{
  "resolutions": {
    "packageA/packageB": "2.0.0"
  }
}
```





# pnpm

[https://www.pnpm.cn/](https://www.pnpm.cn/)

[https://www.kochan.io/nodejs/why-should-we-use-pnpm.html](https://www.kochan.io/nodejs/why-should-we-use-pnpm.html)

节约磁盘空间并提升安装速度

[平铺的结构不是 node_modules 的唯一实现方式](https://pnpm.io/zh/blog/2020/05/27/flat-node-modules-is-not-the-only-way)

**对于大多数操作系统来说，硬链接通常只能用于文件，而不能用于文件夹 =>   所以pnpm既需要软链接也需要硬链接。**





## filter

[https://pnpm.io/zh/filtering](https://pnpm.io/zh/filtering)

```bash
pnpm --filter <package_selector> <command>
```

过滤允许您将命令限制于包的特定子集。



例子：

```ts
"demo:dev": "pnpm --filter=demo run dev"
```

```bash
"build": "pnpm --filter='./packages/*' run build",
```





### parallel

[https://pnpm.io/zh/cli/exec#--parallel](https://pnpm.io/zh/cli/exec#--parallel)

并发执行多个命令

```bash
"dev": "pnpm --parallel --filter='./packages/*' run dev",
```



## [store directory](https://stackoverflow.com/questions/55403775/how-to-get-pnpm-store-directory)

```bash
pnpm store path
```



### prune

裁剪。 从store中删除当前未被引用的包，来释放store空间

```
pnpm store prune
```





## shamefully-hoist

[https://pnpm.io/zh/blog/2020/10/17/node-modules-configuration-options-with-pnpm](https://pnpm.io/zh/blog/2020/10/17/node-modules-configuration-options-with-pnpm)

它的作用是将依赖项的公共模块移到项目的根目录下，以减少依赖项的嵌套层级。

当一个项目中有多个依赖项时，每个依赖项通常会有自己的模块依赖关系。这些依赖项的模块可能会存在相同的依赖模块，导致模块的重复安装和冗余的磁盘空间使用。

```bash
pnpm install --shamefully-hoist
```

要将所有内容提升到node_modules的根目录：

```ini
shamefully-hoist=true
```

按匹配规则提升:

```ini
public-hoist-pattern[]=babel-*
```





## patch

[https://pnpm.io/zh/cli/patch](https://pnpm.io/zh/cli/patch)

给软件包添加补丁

该命令会将指定的软件包提取到一个可以随意编辑的临时目录中。





## overrides

[https://pnpm.io/zh/next/package_json#pnpmoverrides](https://pnpm.io/zh/next/package_json#pnpmoverrides)

**请注意，overrides 字段只能在项目的根目录下设置。**

**请注意，overrides 字段只能在项目的根目录下设置。**

**请注意，overrides 字段只能在项目的根目录下设置。**



在 `pnpm` 中，`package.json` 中的 `resolutions` 字段确实不起作用，因为 `resolutions` 是 `yarn` 包管理器的特性。`pnpm` 有自己的一套机制来处理依赖版本的管理和冲突。尽管 `pnpm` 不支持 `resolutions` 字段，它提供了一些替代方法来处理依赖版本冲突和特定版本的依赖管理。

`pnpm` 中的替代方法

1. 使用 `pnpm.overrides` 字段

`pnpm` 允许在 `package.json` 中使用 `pnpm.overrides` 字段来覆盖特定依赖的版本。`pnpm.overrides` 字段的作用类似于 `yarn` 中的 `resolutions`。

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "dependencies": {
    "packageA": "^1.0.0",
    "packageC": "^1.0.0"
  },
  "pnpm": {
    "overrides": {
      "packageB": "2.0.0"
    }
  }
}
```

在这个例子中，`pnpm.overrides` 字段强制项目中所有依赖 `packageB` 的包使用 `packageB` 的 `2.0.0` 版本。

2. 使用 `pnpm` 的 `dependenciesMeta` 字段

`dependenciesMeta` 字段允许你为特定的依赖包定义元数据。尽管它的主要用途不是覆盖版本，它可以在某些特定情况下提供帮助。

```json
{
  "name": "your-project",
  "version": "1.0.0",
  "dependencies": {
    "packageA": "^1.0.0",
    "packageC": "^1.0.0"
  },
  "pnpm": {
    "dependenciesMeta": {
      "packageB": {
        "version": "2.0.0"
      }
    }
  }
}
```

3. 通过 `pnpm` 的 `shrinkwrap.yaml` 文件

`pnpm` 使用 `shrinkwrap.yaml` 文件来锁定依赖的版本。你可以手动编辑这个文件来强制使用特定版本的依赖包，但这不是推荐的做法，因为它可能会导致依赖管理的复杂性和不一致性。



## circular dependency

当两个或多个模块相互依赖时，如果出现循环依赖，即 A 依赖于 B，而 B 又依赖于 A，就会导致循环依赖错误。这可能会引起程序运行时的问题，例如堆栈溢出错误或模块加载失败等。

pnpm 使用了一个叫做 "Hoist Algorithm" 的方式来处理循环依赖。它会将循环依赖的模块全部安装在同一个目录下，并将它们的依赖关系解析为符号链接（symbolic links）。这样可以避免循环依赖问题，并且确保依赖模块的单一实例被共享。

在使用 pnpm 进行包安装时，如果发现存在循环依赖，pnpm 会给出警告或错误提示，然后尝试通过 Hoist Algorithm 处理该问题。在处理完成后，pnpm 会生成一个类似于 `node_modules/.pnpm` 的目录，在其中包含了解析后的模块及其符号链接。

需要注意的是，虽然 pnpm 可以处理循环依赖问题，但在编写应用程序时，尽量避免循环依赖可以提高代码的可维护性和可理解性。循环依赖通常会导致代码耦合度增加，难以进行有效的单元测试和重构等。因此，建议在设计模块间的依赖关系时，尽量避免出现循环依赖的情况。





## 原理

[https://juejin.cn/post/7158631927992287263#heading-4](https://juejin.cn/post/7158631927992287263#heading-4)

采用symbolic ink (软链接) 与 hard link (硬链接) 结合的方式 

node_modules/.pnpm  硬链接到 pnpm store

node_modules/其他 软链接到 node_modules/.pnpm



# npx

[http://www.ruanyifeng.com/blog/2019/02/npx.html](http://www.ruanyifeng.com/blog/2019/02/npx.html)

npx 的原理很简单，就是运行的时候，会到`node_modules/.bin`路径和环境变量`$PATH`里面，检查命令是否存在。



一般来说，调用 Mocha ，只能在项目脚本和 package.json 的[`scripts`](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)字段里面， 如果想在命令行下调用，必须像下面这样。

```
# 项目的根目录下执行
$ node-modules/.bin/mocha --version
```

npx 就是想解决这个问题，让项目内部安装的模块用起来更方便，只要像下面这样调用就行了。

```
$ npx mocha --version
```

**也就是说 npx 会自动查找当前依赖包中的可执行文件，如果找不到，就会去 PATH 里找。如果依然找不到，就会帮你安装！**



**除了调用项目内部模块，npx 还能避免全局安装的模块。**比如，`create-react-app`这个模块是全局安装，npx 可以运行它，而且不进行全局安装。

```tsx
$ npx create-react-app my-react-app
```







# 命令修饰符



## --if-present

如果脚本名不存在时不想报错，可以使用`--if-present`，比如：`npm run \<script> --if-present`



# 补充



## NPM发包


**NPM默认不会把node_modules发上去**

默认忽略的有


```
.git
CVS
.svn
.hg
.lock-wscript
.wafpickle-N
.*.swp
.DS_Store
._*
npm-debug.log
.npmrc
node_modules
config.gypi
*.orig
package-lock.json (use shrinkwrap instead)

```

想设置发布文件的黑名单 用.npmignore来设置忽略的文件或文件夹。



如果你的项目中没有使用.npmignore文件，那么它默认匹配的是.gitignore中的规则以及一些额外的默认配置。


**可是如果你在项目中添加了.npmignore文件，.gitignore中的规则就会被忽略，而且这时候你还需要维护两份儿规则文件。**




### 以@开头的包

[https://www.npmjs.cn/misc/scope/](https://www.npmjs.cn/misc/scope/)



Publishing public scoped packages to the primary npm registry


To publish a public scoped package, you must specify --access public with the initial publication. This will publish the package and set access to public as if you had run npm access public after publishing.


要发布公共作用域的包，必须指定——使用初始发布访问public。这将发布包并设置对public的访问，就像您在发布后运行npm访问public一样。



### require子路径

 [ https://runkit.com/qinzhen001/5eb232f5cf366c00149f7b33 ]( https://runkit.com/qinzhen001/5eb232f5cf366c00149f7b33 )



```js
var test = require("qz-asdasd/ccc")
test()
```



有的时候，我们想访问npm包子路径下的文件，那该如何设计这种包的结构尼？



例子：

![](https://s1.ax1x.com/2020/05/06/YAGGX8.png)



> 子文件夹 ccc中的index.js默认导出一个文件













## npm彩蛋

圣诞节彩蛋

```javascript
npm xmas
```





### 版本后缀

* -alpha 说明这个文件目前的这个版本是 内测版本
* -beta 说明这个文件目前的这个版本是 公测版本
* -rc 这个代表的是发布正式版本前的预览版本，和正式的版本很接近了



## ~和^和*

- ~ 会匹配最近的小版本依赖包，比如~1.2.3会匹配所有1.2.x版本，但是不包括1.3.0
- ^ 会匹配最新的大版本依赖包，比如^1.2.3会匹配所有1.x.x的包，包括1.3.0，但是不包括2.0.0
- `* `这意味着安装最新版本的依赖包





### 查看npm包的具体发布时间

```bash
npm view express time --json
```



结果：

```
{
  "modified": "2021-09-28T09:07:14.800Z",
  "created": "2010-12-29T19:38:25.450Z",
  "0.14.0": "2010-12-29T19:38:25.450Z",
  "0.14.1": "2010-12-29T19:38:25.450Z",
  "1.0.0beta": "2010-12-29T19:38:25.450Z",
  "1.0.0beta2": "2010-12-29T19:38:25.450Z",
  "1.0.0rc": "2010-12-29T19:38:25.450Z",
  "1.0.0rc2": "2010-12-29T19:38:25.450Z",
  "1.0.0rc3": "2010-12-29T19:38:25.450Z",
  "1.0.0rc4": "2010-12-29T19:38:25.450Z",
  
  // ....
}
```





## HOOK SCRIPTS

npm 中的每条 script 在引擎内部都会运行三个单独的 script 。一个 `pre` scripts，一个 scripts 本身和一个 `post` scripts。

这两个额外运行的 scripts ，正如他们的名字所描述的那样，就是在该 scripts 运行前和该 scripts 运行后运行的脚本。



例如，在部署期间，它们可用来做一些设置和清理。

这两个 scripts 使用与之前 `scripts` 名称相同的 `pre[scriptname]` 和 `post[scriptname]` 来表示。



举个例子：

```
"prebuild": "mkdir dist tmp; rm -rf dist/*",
"build": "browserify main.js -o tmp/bundle.js && uglifyjs -o dist/bundle.min.js -- tmp/bundle.js",
"postbuild": "rm -rf tmp"
```

> 请注意，你应该使用的 `rimraf` 包进行跨平台兼容性，因为上面的代码无法在 Windows 上运行。





## 判断包管理器

[https://4ark.me/post/weekly-npm-packages-01.html/](https://4ark.me/post/weekly-npm-packages-01.html/)

有的时候我们需要判断当前环境用的包管理器 是npm or yarn or pnpm 等

使用：

```ts
const pkgInfo = pkgFromUserAgent(process.env.npm_config_user_agent);

function pkgFromUserAgent(userAgent: string | undefined) {
  if (!userAgent) return undefined;
  const pkgSpec = userAgent.split(" ")[0];
  const pkgSpecArr = pkgSpec.split("/");
  return {
    name: pkgSpecArr[0],
    version: pkgSpecArr[1],
  };
}

// pkgManager 为 pkgInfo.name
```







## 各种依赖

[https://zhuanlan.zhihu.com/p/29855253](https://zhuanlan.zhihu.com/p/29855253)

在一个Node.js项目中，package.json几乎是一个必须的文件，它的主要作用就是管理项目中所使用到的外部依赖包，同时它也是npm命令的入口文件。




npm 目前支持以下几类依赖包管理：


* dependencies
* devDependencies
* peerDependencies
* optionalDependencies
* bundledDependencies / bundleDependencies



例子：

```javascript
"devDependencies": {
    "fw2": "^0.3.2",
    "grunt": "^1.0.1",
    "webpack": "^3.6.0"
  },
  "dependencies": {
    "gulp": "^3.9.1",
    "hello-else": "^1.0.0"
  },
  "peerDependencies": { },
  "optionalDependencies": { },
  "bundledDependencies": []  
```




### dependencies


应用依赖，或者叫做业务依赖，这是我们最常用的依赖包管理对象！它用于指定应用依赖的外部包，这些依赖是应用发布后正常执行时所需要的，但不包含测试时或者本地打包时所使用的包。可使用下面的命令来安装


```javascript
npm install packageName --save
```



dependencies是一个简单的JSON对象，包含包名与包版本，其中包版本可以是版本号或者URL地址。比如：


```
{ 
  "dependencies" :{ 
    "foo" : "1.0.0 - 2.9999.9999", // 指定版本范围
    "bar" : ">=1.0.2 <2.1.2", 
    "baz" : ">1.0.2 <=2.3.4", 
    "boo" : "2.0.1", // 指定版本
    "qux" : "<1.0.0 || >=2.3.1 <2.4.5 || >=2.5.2 <3.0.0", 
    "asd" : "http://asdf.com/asdf.tar.gz", // 指定包地址
    "til" : "~1.2",  // 最近可用版本
    "elf" : "~1.2.3", 
    "elf" : "^1.2.3", // 兼容版本
    "two" : "2.x", // 2.1、2.2、...、2.9皆可用
    "thr" : "*",  // 任意版本
    "thr2": "", // 任意版本
    "lat" : "latest", // 当前最新
    "dyl" : "file:../dyl", // 本地地址
    "xyz" : "git+ssh://git@github.com:npm/npm.git#v1.0.27", // git 地址
    "fir" : "git+ssh://git@github.com:npm/npm#semver:^5.0",
    "wdy" : "git+https://isaacs@github.com/npm/npm.git",
    "xxy" : "git://github.com/npm/npm.git#v1.0.27",
  }
}
```





### devDependencies


开发环境依赖，仅次于dependencies的使用频率！它的对象定义和dependencies一样，只不过它里面的包只用于开发环境，不用于生产环境，这些包通常是单元测试或者打包工具等，例如gulp, grunt, webpack, moca, coffee等，可使用以下命令来安装：

```javascript
npm install packageName --save-dev
```




举个栗子：

```javascript
{ "name": "ethopia-waza",
  "description": "a delightfully fruity coffee varietal",
  "version": "1.2.3",
  "devDependencies": {
    "coffee-script": "~1.6.3"
  },
  "scripts": {
    "prepare": "coffee -o lib/ -c src/waza.coffee"
  },
  "main": "lib/waza.js"
}
```

prepare脚本会在发布前运行，因此使用者在编译项目时不用依赖它。在开发模式下，运行npm install, 同时也会执行prepare脚本，开发时可以很容易的测试。



### **peerDependencies**

同等依赖，或者叫同伴依赖，用于指定当前包（也就是你写的包）兼容的宿主版本。如何理解呢？ 试想一下，我们编写一个gulp的插件，而gulp却有多个主版本，我们只想兼容最新的版本，此时就可以用同等依赖（peerDependencies）来指定：

```javascript
{
  "name": "gulp-my-plugin",
  "version": "0.0.1",
  "peerDependencies": {
    "gulp": "3.x"
  }
}
```

当别人使用我们的插件时，peerDependencies就会告诉明确告诉使用方，你需要安装该插件哪个宿主版本。

通常情况下，我们会在一个项目里使用一个宿主（比如gulp）的很多插件，如果相互之间存在宿主不兼容，在执行npm install时，cli会抛出错误信息来告诉我们，比如


```javascript
npm ERR! peerinvalid The package gulp does not satisfy its siblings' peerDependencies requirements!
npm ERR! peerinvalid Peer gulp-cli-config@0.1.3 wants gulp@~3.1.9
npm ERR! peerinvalid Peer gulp-cli-users@0.1.4 wants gulp@~2.3.0
```


运行命令npm install gulp-my-plugin --save-dev来安装我们插件，我们来看下依赖图谱：

```
├── gulp-my-plugin@0.0.1
└── gulp@3.9.1
```

**重点注意gulp-my-plugin和gulp是在同一层级别的，peerDependencies也常用第三方库的开发**




>npm 1 与 npm 2 会自动安装同等依赖，npm 3 不再自动安装，会产生警告！手动在package.json文件中添加依赖项可以解决。





#### peerDependenciesMeta

peerDependenciesMeta 用户安装您的软件包时，如果peerDependencies尚未安装中指定的软件包，npm将发出警告。该 peerDependenciesMeta字段用于向npm提供有关如何使用对等依赖项的更多信息。

```tsx
"peerDependenciesMeta": {
    "soy-milk": {
    "optional": true
    }
}
```






### optionalDependencies

可选依赖，如果有一些依赖包即使安装失败，项目仍然能够运行或者希望npm继续运行，就可以使用optionalDependencies。另外optionalDependencies会覆盖dependencies中的同名依赖包，所以不要在两个地方都写。

举个栗子，可选依赖包就像程序的插件一样，如果存在就执行存在的逻辑，不存在就执行另一个逻辑。



```javascript
try {
  var foo = require('foo')
  var fooVersion = require('foo/package.json').version
} catch (er) {
  foo = null
}
if ( notGoodFooVersion(fooVersion) ) {
  foo = null
}

// .. then later in your program ..

if (foo) {
  foo.doFooThings()
}
```



### bundledDependencies 

打包依赖，bundledDependencies是一个包含依赖包名的数组对象，在发布时会将这个对象中的包打包到最终的发布包里。如：


```javascript
{
  "name": "fe-weekly",
  "description": "ELSE 周刊",
  "version": "1.0.0",
  "main": "index.js",
  "devDependencies": {
    "fw2": "^0.3.2",
    "grunt": "^1.0.1",
    "webpack": "^3.6.0"
  },
  "dependencies": {
    "gulp": "^3.9.1",
    "hello-else": "^1.0.0"
  },
  "bundledDependencies": [
    "fw2",
    "hello-else"
  ]
}
```


执行打包命令npm pack, 在生成的fe-weekly-1.0.0.tgz包中，将包含fw2和hello-else。 但是值得注意的是，这两个包必须先在devDependencies或dependencies声明过，否则打包会报错。







## 开发组件库需要的依赖

https://jingsam.github.io/2016/11/01/peerDependencies-in-Vue-components.html

假如我们需要开发一个vue组价库

- `dependencies`：放到 `dependencies` 的好处是安装组件库的时候 Vue 会自动安装。问题是已有的工程项目中往往已经安装了 Vue，会导致在 npm 1 或 2 中重复安装 Vue。虽然此问题在 npm 3 中可以规避，但是如果已安装的 Vue 版本与组件库所依赖的版本不兼容时，Vue 仍然会重复安装。
- `devDependencies`：由于组件库单元测试时会用到 Vue，所以严格来说 Vue 是属于开发工具包，放到 `devDependencies` 合情合理。但是，在 `devDependencies` 中的 Vue 依赖不会自动安装，所以需要用户去查文档来确定到底应该安装哪个版本的 Vue，对用户似乎不太友好。
- `peerDevDependencies`：Vue 组件库本质上是 Vue 的插件，依赖于外部提供的 Vue。把 Vue 放到这里，npm 能够提示用户需要安装哪个版本的 Vue，这点比 `devDependencies` 友好。问题在于，从 npm 3 开始，`peerDevDependencies` 中的依赖不会自动安装，会导致自动集成测试失败。
- 不添加依赖：这基本上是最差的选择了，用户甚至不能知道组件库到底依赖哪个版本的 Vue，只能靠猜。
- `devDependencies`、`peerDevDependencies` 同时添加：这是我和 Element UI 的开发者讨论后最优的做法。`peerDevDependencies` 中的 Vue 是组件库运行所依赖的 Vue 的最低版本，而 `devDependencies` 运行测试时需要的 Vue 版本，一般情况下 `devDependencies >= peerDevDependencies`。这种做法保证了 npm 1、2、3 都不会出问题，并且当用户安装组件库时给予友好的提示信息。

结论：

**在开发 Vue 组件库是，应当同时在 `devDependencies` 和 `peerDevDependencies` 添加 Vue 依赖。**



# 问题



## 解决Error: Cannot find module 'node-sass'问题

 https://blog.csdn.net/sliver1836/article/details/95950284 

1.在项目目录cmd下运行 npm install -g cnpm --registry=https://registry.npm.taobao.org

2.下载成功后再运行 cnpm install node-sass





## 扁平化结构

扁平化结构意味着npm将不同版本的模块安装在项目的顶级node_modules目录下，而不是按照它们在依赖树中的位置进行安装。这可能会导致以下问题：

1. 版本冲突：当一个模块的不同版本同时存在于项目中时，可能会导致依赖模块出现冲突，因为它们可能对不同版本的模块有不同的依赖要求。
2. 模块重复：同一个模块的多个版本的副本会被安装到不同的位置，导致项目中存在多个相同的模块。（分身）

**原本存在于孙子节点的包，顶部项目能直接访问到 （幽灵依赖）**

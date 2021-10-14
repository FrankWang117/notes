# 使用 npm scripts 你可能不知道的几件事

![KJU3P1YkdS1lSP4uhTILvd7leAnikjCppZBRpi8ekKIdi.width-1616](https://raw.githubusercontent.com/FrankWang117/images/master/2021-01-01/KJU3P1YkdS1lSP4uhTILvd7leAnikjCppZBRpi8ekKIdi.width-1616.png?token=AFFPCSCMSSS5CPNSC5EVN42754VPS) 

在开始前确保已经安装了 npm。 [原文链接](https://www.twilio.com/blog/npm-scripts)

## 什么是 npm scripts

当我们说 ‘npm scripts’ 的时候，我们说的是 `package.json` 中的  'scripts' 属性值。你可以在此制定要公开的各种命令和脚本，然后可以使用 `npm run <script-name>` 去执行它们。

我们使用 `npm init` 初始化一个项目，生成的 `package.json` 像下面这样：

![carbon](https://raw.githubusercontent.com/FrankWang117/images/master/2021-01-01/carbon.png?token=AFFPCSHC2EV6D43YOVEP25C754WWO)

使用：

```shell
npm run test
```

将会打印错误并退出。  

为了演示，我们安装 typescript 模块，我们可以使用 `npm i --save-dev typescript` 来将 typescript 安装在当前目录，并修改 `scripts` 部分为：

``` json
  "scripts": {
    "build": "tsc"
  }
```

## Pre-/post-scripts

预执行脚本与后执行脚本。

假设你有以下脚本：

```json
"scripts": {
    "prebuild": "rimraf dist",
    "build": "tsc",
    "postbuild": "npm run test",
    "test": "jest"
  }
```

当你执行 `npm run build` 的时候：

1. Prebuild 会首先执行，删除 `dist` 文件夹
2. build 会执行 typescript 的编译
3. postbuild 会执行测试步骤（test 命令）
4. test 执行 jest 测试（步骤三实际执行的）

之所以这一套流程会执行下来，是因为 npm 会自动检测一个脚本命令是否有其他命令与其有相同的命名方式，但是以`pre` 或者 `post` 开头的，找到后会按照顺序执行。你可以在 [npm 文档](https://docs.npmjs.com/cli/v6/using-npm/scripts) 中查看详细说明以及各个命令的执行顺序。  

## 环境变量

当运行命令或脚本时：`npm run <script-name>` ，你的环境变量将自动增加一组来自 npm 的变量。  

所有变量都以 `npm_` 为前缀，并分为两种类型：

- 以 `npm_config_` 开头的任何内容都来自于全局的 npm config 或项目内特定的 `.npmrc`文件配置。
- 以 `npm_package_` 开头的任何东西都来自于你的项目

如果你对环境变量列表感兴趣，可以将下列命令添加入 “scripts” 中：

```json
{
  "scripts": {
    "check-env": "node -e 'console.log(process.env)' | grep npm"
  }
}
```

使用 `npm run check-env` ，就可以看到：

![截屏2021-01-01 下午10.36.53](https://raw.githubusercontent.com/FrankWang117/images/master/2021-01-01/%E6%88%AA%E5%B1%8F2021-01-01%20%E4%B8%8B%E5%8D%8810.36.53.png?token=AFFPCSEARN22AIXSF3TDTRS754Z4Y)

等。

## 参数传递

到目前为止，我们已经介绍了如何创建脚本，设置了哪些环境变量以及如何调用脚本。但是，有时你希望能够将参数传递给脚本，以使它们更具动态性。  

你可以通过两种方法将参数传递给 npm scripts:

第一种方法只是将参数直接传递给您的实际命令。例如：![carbon (1)](https://raw.githubusercontent.com/FrankWang117/images/master/2021-01-01/carbon%20(1).png?token=AFFPCSBRLDQBADRMMJ67TES7542JS)

将执行 `tsc --watch`；

第二种选择是使用 npm 的内置参数解析器。这可能是npm脚本鲜为人知的功能之一。本质上，npm会解析您传递给脚本的任何参数，**除非** 在传递参数后`--`接一个空格。npm解析它们之后，它们将`npm_config_`在环境变量下可用。

要对此进行测试，请创建一个新`scripts`条目：

```json
{
  "scripts": {
    "demo": "echo \"Hello $npm_config_first $npm_config_last\""
  }
}
```

![carbon (2)](https://raw.githubusercontent.com/FrankWang117/images/master/2021-01-01/carbon%20(2).png?token=AFFPCSGAAGQUGGFDDZ5LFLS7542Q6)

它应该输出`Hello Dominik Kundel`。重要的是要注意，由于我们没有配置此参数解析器，因此在参数语法方面不是很灵活。例如，如果我们删除`=`标志并再次运行相同的命令：![carbon (3)](https://raw.githubusercontent.com/FrankWang117/images/master/2021-01-01/carbon%20(3).png?token=AFFPCSELJVMQT6HPHXWA5IK7542TY)

我们会得到`Hello true true Kundel Dominik`，因为它会将`--last`和`--first`解释为布尔，并将其设置为 `true`，并将其余参数作为未解析的参数传递给脚本.

## 结论

npm 在我们开发过程中时刻都在，合理的规划使用 npm scripts 可以改善我们的开发体验。


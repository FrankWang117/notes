如何在 Angular 项目中自定义引用路径

## 前言

在项目的开发过程中我们总会出现这样的问题：

```typescript
import { AModule } from '../../module';
import { BEnum } from '../../../enumFile';
import { CConst } from '../../constsFile';
```

也就是如果我们的组件嵌套多，子组件想要使用顶层 `app` 定义的一些全局枚举或者常量以及一些其他数据的时候，使用**相对路径**引用的时候往往会比较繁杂，并且使引入路径代码看起来很繁琐。  

那现在就以 Angular 项目中的解决此问题的方式来解答这种问题。

## 解决

首先想到的就是使用**绝对路径**引入不就解决相对路径引入的 `/../` 的问题了么：

```typescript
import { AModule } from 'src/app/module';
import { BEnum } from 'src/app/emum/enumFile';
import { CConst } from 'src/app/constsFile';
```

当然这也是一种解决问题的方式，但是我们想要的肯定不是这样。

尤其是在 `app` 路径下，细分了不同的文件后，我们又需要多加一层，比如 `src/app/someFolder` 

所以我们会给路径添加一个别名 [alias](https://webpack.js.org/configuration/resolve/) ：

### webpack 中

在 **webpack.config.js** 文件中

```js
const path = require('path');

module.exports = {
  //...
  resolve: {
    alias: {
      Utilities: path.resolve(__dirname, 'src/utilities/'),
      Templates: path.resolve(__dirname, 'src/templates/'),
    },
  },
};
```

现在导入就可以从：

```javascript
import Utility from '../../utilities/utility';
```

变更为：

```javascript
import Utility from 'Utilities/utility';
```

当然还有其他用法，更详细的查看 [webpack resolve 的用法](https://webpack.js.org/configuration/resolve/)。

### Angular 项目中

虽然我们知道 Angular cli 内部是基于 webpack 实现的，但是默认是没有 webpack 的配置文件 `webpack.config.js` 的，所以我们需要：

使用 **tsconfig.json** 来配置。在 `angular-cli` 生成的项目修改如下：

在根目录下的 **`tsconfig.json`** :

```json
{
  // ...
  "compilerOptions": {
    "baseUrl": "./",
    // ...
    "paths": {
      "@shared/*": ["src/app/shared/*"],
      "@app/*": ["src/app/*"]
    }
  }
}
```

然后在使用到的地方就可以从别名引入了：

```typescript
import { OffsetUnit } from '@shared/constants';
```

如果 VScode 提示 `找不到模块“@shared/constants”或其相应的类型声明` 可以通过重启 VScode 来解决此问题。

## 总结

学好 webpack，走天下。
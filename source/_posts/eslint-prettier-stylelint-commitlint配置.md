---
title: 'eslint,prettier,stylelint,commitlint配置'
date: 2022-02-10 17:03:22
tags:
  - Vue
---



# 代码、项目规范，配置 eslint、prettier、stylelint、commitlint



> 为了规范项目代码，统一代码风格，同时提升代码的提交规范，使用 **eslint** 进行代码规范化的检测和自动修改；使用  **prettier** 统一代码风格和自动格式化；使用  **stylelint** 统一 css 风格和自动格式化；使用 **commitlint**、**lint-staged**、**husky** 进行提交检查和提交自动格式化。 同时为了消除不同使用者 vscode 的差异，提供了 .vscode -- setting.json 的配置.

<!-- more -->

## Eslint\prettier\vscode 配置

1. .vscode 配置:

```json
{
  "files.associations": {
    "*.vue": "vue"
  },
  "editor.formatOnSave": false, // prettier保存格式化
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[vue]": {
    "editor.defaultFormatter": "octref.vetur"
  },
  // vetur 配置
  "vetur.validation.template": false,
  "vetur.format.defaultFormatter.html": "prettyhtml",
  "vetur.format.defaultFormatter.css": "prettier",
  "vetur.format.defaultFormatter.postcss": "prettier",
  "vetur.format.defaultFormatter.scss": "prettier",
  "vetur.format.defaultFormatter.less": "prettier",
  "vetur.format.defaultFormatter.stylus": "stylus-supremacy",
  "vetur.format.defaultFormatter.sass": "sass-formatter",
  "vetur.format.defaultFormatter.js": "prettier-eslint",
  "vetur.format.defaultFormatter.ts": "vscode-typescript",
  "vetur.format.options.tabSize": 2,
  "vetur.format.defaultFormatterOptions": {
   // "js-beautify-html": {
   //   "wrap_attributes": "force-expand-multiline"
   // },
    "prettyhtml": {
      "printWidth": 150,
      "singleQuote": false,
      "wrapAttributes": true,
      "sortAttributes": false
    },
    "prettier": {
      "semi": true,
      "singleQuote": true
    }
  },
  "prettier.vueIndentScriptAndStyle": true,
  // eslint扩展设置
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.options": {
    "extensions": [".js", ".vue"]
  },
  "eslint.validate": ["javascript", "javascriptreact", "html", "typescript", "typescriptreact", "vue"]
}


```

2. 安装以下npm包

```shell
npm install --save-dev eslint@7.28.x prettier eslint-config-prettier eslint-plugin-prettier eslint-formatter-pretty
```

> Tips: 
>
> eslant 要求版本7.28以上，eslint-config-prettier eslint-plugin-prettier用于解决prettier和eslint规则的冲突.
>
> eslint-formatter-pretty （用于命令行美化）



3. .eslintrc 配置如下

```json
{
  root: true,
  parserOptions: {
    parser: 'babel-eslint',
    sourceType: 'module',
    ecmaVersion: 2020,
  },
  env: {
    browser: true,
    node: true,
    es6: true,
  },
  extends: [
    'plugin:vue/recommended',
    'eslint:recommended',
    'plugin:prettier/recommended', // 解决prettier和eslint的冲突
  ]
} 
```

> prettier和eslint存在部分冲突的规则， 通过 eslint-config-prettier eslint-plugin-prettier 已经 能解决大部分冲突，剩下的比如  "singleQuote"、"semi" 手动配置 eslint 就能解决
>
> 配置的原则就是 eslint 负责代码检查和自动修复，prettier 负责自动格式化 



## stylelint

1. Npm 安装如下包

```json
{
		"stylelint": "^14.3.0",
    "stylelint-config-prettier": "^9.0.3",
    "stylelint-config-recess-order": "^3.0.0",
    "stylelint-config-recommended-vue": "^1.1.0",
    "stylelint-config-standard": "^24.0.0",
    "stylelint-config-standard-scss": "^3.0.0",
    "stylelint-scss": "^4.1.0",
    "stylelint-webpack-plugin": "^2.3.2",
}
```

2. 新建stylelint.config.js 文件并配置如下:

```js
module.exports = {
  defaultSeverity: 'warning',
  extends: ['stylelint-config-standard-scss', 'stylelint-config-recommended-vue/scss', 'stylelint-config-prettier', 'stylelint-config-recess-order'],
  rules: {
    'selector-class-pattern': [
      // 命名规范 - kebab-case
      '^([a-z][a-z0-9]*)(-+[a-z0-9]+)*$',
      {
        message: 'Expected class selector to be kebab-case',
      },
    ],
    'rule-empty-line-before': null,
    'declaration-block-no-duplicate-properties': true, // 禁止声明重复属性
    'no-descending-specificity': true, // 禁止在具有较高优先级的选择器后出现被其覆盖的较低优先级的选择器。
    // 最多允许嵌套20层，去掉默认的最多2层
    'max-nesting-depth': 20,
    // 颜色值要小写
    'color-hex-case': 'lower',
    // 颜色值能短则短
    'color-hex-length': 'short',
  },
  ignoreFiles: ['**/*.js', '**/*.jsx', '**/*.tsx', '**/*.ts'],
};

```

3. webpack StyleLintPlugin 配置如下：

​		

```js
new StyleLintPlugin({
        files: ['src/*.(s(c|a)ss|css|less|vue)'],
        extensions: ['ccs', 'scss', 'sass', 'less', 'vue', 'html'],
        fix: true,
        cache: false,
        emitErrors: true,
        failOnError: false,
      }),
```





4. vscode 配置如下

```json
{
"stylelint.validate": ["vue", "less", "css", "html", "scss", "sass"],
  "editor.codeActionsOnSave": {
    "source.fixAll.stylelint": true
  },
}

```

## commitlint husky lint-staged

1. > npm install --save-dev @commitlint/config-conventional @commitlint/cli  husky lint-staged

   

2. commitlint.config.js 配置如下:

```js
/**
 * init: 项目初始化
 * feat：新功能
 * fix：修补某功能的bug
 * refactor：重构某个功能
 * perf: 优化构建工具或运行时性能
 * style：仅样式改动
 * docs：仅文档新增/改动
 * chore：构建过程或辅助工具的变动
 * ci: ci配置相关，docker等配置文件修改
 */
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', ['init', 'feat', 'fix', 'refactor', 'perf', 'style', 'docs', 'chore', 'ci']],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72],
  },
};

```

3. husky lint-staged

> npm install husky lint-staged prettier --save-dev
>
> npx husky install  (启用)
>
> "prepare": "husky install" 【install后自动启用，这个配置在package.json中，否则下次新拉代码还需要手动启用】
>
> npx husky add .husky/pre-commit "npm run lint-staged" 【创建一个hook，执行完根目录回生成一个.husky目录】

​	

4. husky 和 lint-staged 配置如下

```json
"husky": {
    "hooks": {
      "pre-commit": "npm run lint-staged"
    }
  },
  "lint-staged": {
    "**/*.vue": [
      "npm run lint:fix",
      "npm run lint:style:fix"
    ],
    "**/*.{js,jsx,ts,tsx}": [
      "npm run lint:fix"
    ],
    "**/*.{htm,html,css,sss,less,scss,sass}": [
      "npm run lint:style:fix",
      "git add"
    ]
  }
```

## npm 脚本部分配置如下:

```json
{
   "lint": "eslint --ext .js,.vue src --ignore-path .gitignore .",
    "lint:fix": "eslint --fix --ext .js,.vue --format=pretty ./src",
    "lint:prettier": "prettier --check \"**/*\" --end-of-line auto",
    "lint:style": "stylelint src/**/*.{vue,css,sass,scss,less} --allow-empty-input",
    "lint:style:fix": "stylelint src/**/*.{vue,css,sass,scss,less} --fix",
    "lint-staged": "lint-staged",
    "prepare": "husky install",
}
```

## 用到的部分依赖及版本如下： 

```
{
	"devDependencies": {
		"@commitlint/cli": "^16.1.0",
    "@commitlint/config-conventional": "^16.0.0",
     "eslint": "^7.29.0",
    "eslint-config-prettier": "^8.3.0",
    "eslint-formatter-pretty": "^4.1.0",
    "eslint-plugin-prettier": "^4.0.0",
    "eslint-plugin-vue": "6.2.2",
    "husky": "^7.0.4",
    "lint-staged": "^12.3.2",
    "postcss": "^8.4.5",
    "postcss-html": "^1.3.0",
    "postcss-scss": "^4.0.3",
    "prettier": "2.5.1",
    "stylelint": "^14.3.0",
    "stylelint-config-prettier": "^9.0.3",
    "stylelint-config-recess-order": "^3.0.0",
    "stylelint-config-recommended-vue": "^1.1.0",
    "stylelint-config-standard": "^24.0.0",
    "stylelint-config-standard-scss": "^3.0.0",
    "stylelint-scss": "^4.1.0",
    "stylelint-webpack-plugin": "^2.3.2",
	}
}
```


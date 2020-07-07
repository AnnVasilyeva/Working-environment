# Working-environment for frontend-developer (instruction)

* [Установка ESLint](#eslint-installation)
* [Установка BABEL](#babel-installation)
* [Установка WEBPACK](#webpack-installation)
* [Установка JEST](#jest-installation)


1. Создать:
    - файл [.gitignore](https://github.com/AnnVasilyeva/Working-environment/blob/master/.gitignore) c содержимым
    - папку `src` - папки js и css, файлы index.html и index.js 
    - в папке `js` - файл app.js (и другие если надо) и папку ```__tests__``` (в ней файлы с названием типа app.test.js для тех файлов, где нужно писать тесты);  

2. Инициализируем пакет
        ```
        npm init
        ```      
3. Генерация файла `package.json`
         
          package name -- 
          version (1.0.0)
          description --
          entry point (index.js)
          test command (jest --coverage)
          git repository --
          keywords --
          author --
          license (ISC)
              
## ESLint installation

(инструмент, отслеживающий стиль кодирования и типичные ошибки в мире JS)
  
        npm install --save-dev eslint 
        npx eslint --init

 Конфигуратор: 
 
 - How would you like to use ESLint? 
 >To check syntax, find problems, and enforce code style 
 - What type of modules does your project use? 
 >JavaScript modules (import/export)
 - Which framework does your project use? 
 >None of this
 - Does your project use TypeScript? 
 >No
 - Where does your code
 >Browser
 - How would you like to define a style for your project? 
 >Use a popular style guide 
- Which style guide do you want to follow? 
>Airbnb
 - What format do you want your config file to be in? 
 >JSON
 - Would you like to install them now with npm? 
 >Yes

В файле  `.eslintrc.json` 
   ```
{
    "env": {
        "jest": true,
        "browser": true,
        "es6": true 
    },
    "extends": "airbnb-base", 
    "globals": {
        "Atomics": "readonly",
        "SharedArrayBuffer": "readonly" 
    },
    "parserOptions": { 
        "ecmaVersion": 2018, 
        "sourceType": "module"
    }, 
    "rules": {
        "no-restricted-syntax": [
            "error", 
            "LabeledStatement", 
            "WithStatement"
        ]
     }
}
  ```
В файле `package.json`

```
"scripts": {
    "lint": "eslint ."
},
```
Создать файл `.eslintignore`
```
dist
coverage
docs
```
Запуск линтера ```npm run lint```

Исправление проблем ```npm run lint -- --fix```

## BABEL installation

(инструмент, преобразующий код из одной версии стандарта в другую, более старую. Можно не устанавливать отдельно, а установить вместе с [JEST](#jest-installation))

```
npm install --save-dev @babel/core @babel/cli @babel/preset-env 
npm install core-js@3
```
Создаем конфигурационный файл `.babelrc`
```
{
  "presets": [
    [
       "@babel/preset-env", {
            "useBuiltIns": "usage",
            "corejs": 3 
       }
    ] 
  ]
}
```
В файле `package.json`
```
"scripts": {
    "build": "babel src -d dist"
},
```
Запускаем трансплайлинг ```npm run build```

Создать файл `.browserslistrc` 
(позволяет установить, поддержку каких браузеров (окружений) необходимо обеспечивать, исходя из статистики [caniuse.com](https://caniuse.com/))
```
last 1 version
> 1%
maintained node versions
not dead
```

## WEBPACK installation

(самый популярный инструмент сборки в мире JS)

```
npm install --save-dev webpack webpack-cli babel-loader
```
В файле `package.json` заменить
```
"scripts": {
    "build": "webpack --mode production"
},
```
Сборка ```npm run build```

Создаем базовый файл `.webpack.config.js`
```
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "app.bundle.js",
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./src/index.html",
    }),
    new MiniCssExtractPlugin({
      filename: "[name].css",
    }),
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, "css-loader"],
      },
    ],
  },
};
```
Установка плагина HTML

(позволит по шаблону генерировать выходной файл и в него встраивать ссылки на .js (и другие файлы))
```
npm install --save-dev html-webpack-plugin
```
Установка плагина CSS
```
npm install --save-dev mini-css-extract-plugin css-loader
```
Установка WEBPACK DEV SERVER

(если нужен)
```
npm install --save-dev webpack-dev-server
```

В файле `package.json` заменить
```
"scripts": {
    "start": "webpack-dev-server --mode development",
},
```
Запуск сервера ```npm start```

## JEST installation

(фреймворк для тестирования, устанавливается вместе с BABEL, так что предварительно BABEL можно не устанавливать)

```
npm install --save-dev jest babel-jest @babel/core @babel/cli @babel/preset-env 
npm install core-js@3
```
В файле `package.json` проверить
```
"scripts": {
    "test": "jest", 
    "lint": "eslint .",
},
```
В файле `.babelrc` 
```
{
  "presets": [
    [
       "@babel/preset-env", {
           "useBuiltIns": "usage",
            "corejs": 3
        }
    ]
  ]
}
```
Запуск тестов ```npm test```

Общий вид теста
```
test('<описание того, что проверяем>', () => { 
    // Функция проверки
    
    // 1. Выполняем нужные нам действия 
        TODO:
    
    // 2. Проверяем результат с помощью:
        expect(>что получили<).toBe(>что должно быть<); 
})
```
Например: 
 * В файле `app.js`
 ```
export default function exampleFunction(user) { какая-то функция... } 
```
 * В файле `app.test.js`
 ```
import exampleFunction from '../app';

Дальше идут тесты типа: 

test('проверка индикации жизни при здоровье 50', () => {
      const gamer = { name: 'Маг', health: 50 };
      const received = exampleFunction(gamer);
      const expected = 'healthy';
      expect(received).toBe(expected);
});
```

### Code Coverage

(Покрытие кода - метрика, показывающая, насколько наш код покрыт авто- тестами)

```npm test -- --coverage```



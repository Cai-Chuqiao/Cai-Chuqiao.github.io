## Шаг1. Сборка приложения.
1) В терминале в папке приложения `onnx-app` выполнить `npm init –y`.
2) Заменить содержимое package.json на следующее:
```
{
  "name": "yolov8seg",
  "homepage": "https://your_github_id.github.io/",
  "version": "0.1.0",
  "dependencies": {
    "@techstark/opencv-js": "4.5.5-release.2",
    "onnxruntime-web": "^1.14.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1"
  },
  "scripts": {
    "start": "craco start",
    "build": "craco build",
    "predeploy": "yarn build",
    "deploy": "gh-pages -d build"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "devDependencies": {
    "@craco/craco": "^7.1.0",
    "copy-webpack-plugin": "^11.0.0",
    "gh-pages": "^6.1.1"
  }
}
```
3) В корне проекта создать файл `craco.config.js` со следующим содержанием:
```
const CopyPlugin = require("copy-webpack-plugin");

module.exports = {
  webpack: {
    plugins: {
      add: [
        new CopyPlugin({
          // Use copy plugin to copy *.wasm to output folder.
          patterns: [
            { from: "node_modules/onnxruntime-web/dist/*.wasm", to: "static/js/[name][ext]" },
            { from: './public/model/model.onnx', to: '[name][ext]'},
            { from: './public/model/nms-yolov8.onnx', to: '[name][ext]'},
            { from: './public/model/mask-yolov8-seg.onnx', to: '[name][ext]'}
          ],
        }),
      ],
    },
    configure: (config) => {
      // set resolve.fallback for opencv.js
      config.resolve.fallback = {
        fs: false,
        path: false,
        crypto: false,
      };
      return config;
    },
  },
};
```
4) Проверить, что приложение собирается. Создадать папку `src` в корне проекта. В папке src создаем папку components. В ней создаем файл `App.js`:
```
import React from "react";

export const App = () => {
    return (
        <div>
            Пример onnx
        </div>
    )
}
```
В папке src создать файл `index.js`:
```
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import "./style/index.css";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```
Программ на основе [Hyuto/yolov8-seg-onnxruntime-web](https://github.com/Hyuto/yolov8-seg-onnxruntime-web/tree/master)<br>
`git clone https://github.com/Cai-Chuqiao/Cai-Chuqiao.github.io`
## Шаг2. Развертывайте проекты React локально
### Шаг2.1
Поместите файл модели ONNX, созданный вами в [yolov8+onnx.ipynb](https://github.com/Cai-Chuqiao/Cai-Chuqiao.github.io/blob/main/colab/yolov8%2Bonnx.ipynb), 
в `./public/model/`.
### Шаг2.2
Измените `yolov8-seg.onnx` на собственное имя файла ONNX в шаблонах файла `craco.config.js`.<br>
```
          patterns: [
            { from: "node_modules/onnxruntime-web/dist/*.wasm", to: "static/js/[name][ext]" },
            { from: './public/model/model.onnx', to: '[name][ext]'},
            { from: './public/model/nms-yolov8.onnx', to: '[name][ext]'},
            { from: './public/model/mask-yolov8-seg.onnx', to: '[name][ext]'}
          ],
```
Измените метку в `./src/utils/label.json` на класс, распознаваемый вашей собственной моделью.<br>
```json
[
  "airplane",
  "weather_balloon",
  "UFO"
]
```
### Шаг2.3
Удалите папку `node_modules` в корневом каталоге.
### Шаг2.3
Если вы хотите развернуть проект React только локально, вы можете выполнить действия, описанные в [Hyuto/yolov8-seg-onnxruntime-web](https://github.com/Hyuto/yolov8-seg-onnxruntime-web/tree/master), 
но если вы хотите развернуть проект React на страницах GitHub, вам не нужно выполнять `yarn install`, `yarn start` и `yarn build`, 
иначе при загрузке проекта в собственный репозиторий github загрузка завершится неудачей из-за слишком больших файлов в node_modules.<br>
## Шаг3. Развертывание проекта реагирования на страницах github
### Шаг3.1
Создайте свой репозиторий на github. Имя репозитория — `your_github_id.github.io`, и репозиторий должен быть общедоступным.
![image](https://github.com/Cai-Chuqiao/Cai-Chuqiao.github.io/assets/150286732/7ddf6e33-b88a-45e6-87c5-ee425e079a72)
### Шаг3.2
Измените `"homepage": "https://your_github_id.github.io/"` в `package.json` в папке, где проект реагирования хранится локально, 
на URL-адрес ваших собственных страниц github.
![image](https://github.com/Cai-Chuqiao/Cai-Chuqiao.github.io/assets/150286732/b4ef31b9-d7d8-4d11-b93e-f1efa07e1305)
### Шаг3.3
Используйте `git pull`, чтобы загрузить проект в свой репозиторий.<br>
### Шаг3.4
Откройте терминал в корневом каталоге вашего локального проекта React и последовательно запустите:<br>
```
yarn add gh-pages --save-dev
git add .
git commit -m "deploy commit"
git push
yarn deploy
```
### Шаг3.5
Выберите `pages` в `Settings` вашего репозитория, измените `Branch` на `gh-pages` и нажмите `Save`, 
![image](https://github.com/Cai-Chuqiao/Cai-Chuqiao.github.io/assets/150286732/d1456289-e90f-4af4-b16b-38bbdae2c04c)
после чего вы сможете запустить свой проект React, открыв URL-адрес ваших страниц GitHub.
![image](https://github.com/Cai-Chuqiao/Cai-Chuqiao.github.io/assets/150286732/3bf1d3f5-3499-4796-b0c9-4b95df9f6d9b)


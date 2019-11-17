# Способ 1 (простой): симлинк
Способ предполагает создание симлинка в папке node_modules. Не поддерживает специальные символы (напрмиер, @), нельзя сделать один алиас на несколько папок, не работает на Windows. Но зато легко и непринужденно настраивается.
### [Ссылка](https://github.com/alfa-laboratory/arui-scripts/blob/b2a8e16557a914e629b463d5f56440e33d8d974c/commands/postinstall/index.js)
Это пример из библиотеки arui-scripts, используемой в Альфа-Банке. Библиотека подддерживается [@Heymdall](https://github.com/Heymdall)

# Способ 2 (сложный): алиасы
1. **TypeScript** - tsconfig.json:
    * Указать `baseUrl`
    * Прописать алиасы в параметр `path` в формате `"alias/*": ["blob/*"]`. Подробнее - в [документации](https://www.typescriptlang.org/docs/handbook/module-resolution.html#path-mapping)<br/>
        Пример:
        ```
        "baseUrl": ".",
        "paths": {
            "@/*": ["src/*"],
            "@client/*": ["src/client/*"],
            "@server/*": ["src/server/*"]
          }
        ```
2.
    1. **Babel/Webpack** - Установить [babel-plugin-module-resolver](https://github.com/tleunen/babel-plugin-module-resolver). (Не забудьте прописать `ts`- файлы в расширениях.)<br/>
    Тайпскрипт не преобразовывает импорты из алиасов при компиляции, поэтому нужны дополнительные инструменты, чтобы вебпак (или любой другой сборщик) корректно их обрабатывал
    Пример:
      ```
      ['module-resolver', {
          root: ['./src'],
          alias: {
              '@': './src',
              '@client': './src/client',
              '@server': './src/server'
          },
          extensions: ['.js', '.jsx', '.ts', '.tsx']
      }]
      ```
    2. **Webpack** - если не хотите вообще использовать Babel, есть альтернативный вариант - прописать алиасы в самом конфиге вебпака. Как это сделать - описано в [документации](https://webpack.js.org/configuration/resolve/). Есть нюанс. Вебпак поддерживает алиасы только на конкретные папки (например, `paths: { '@/*': ['src/*'] }`, но не `paths: { '*': ['src/*'] }`), поэтому рекомендация воздержаться от написания "общих" алиасов
3. **eslint**
    * убедитесь, что у вас в качестве парсера стоит @typescript-eslint/eslint-plugin. С другими парсерами дальнейшие шаги работать не будут
    * Установите [eslint-plugin-import](https://github.com/benmosher/eslint-plugin-import) и [eslint-import-resolver-typescript](https://github.com/alexgorbatchev/eslint-import-resolver-typescript)
    * Добавьте в `.eslintrc` настройку
      ```
      settings: {
          "import/resolver": {
              typescript: {}
          }
      }
      ```
      Подробнее читайте в [документации](https://github.com/alexgorbatchev/eslint-import-resolver-typescript)<br/>
      (Есть еще альтернативный вариант с [eslint-import-resolver-babel-module](https://github.com/tleunen/eslint-import-resolver-babel-module), но у меня не получилось его заставить работать с TypeScript)

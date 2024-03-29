[Toc]

# 项目目录

```
|-- root
    |-- .gitignore
    |-- jsconfig.json ------------------------------------------------ 用于写@快捷目录
    |-- package-lock.json
    |-- package.json
    |-- postcss.config.js -------------------------------------------- postcss配置
    |-- prettier.config.js ------------------------------------------- prettier配置
    |-- config
    |   |-- webpack.dev.js ------------------------------------------- 开发配置
    |   |-- webpack.prod.js ------------------------------------------ 生产配置
    |-- src
        |-- App.vue -------------------------------------------------- html页面主挂载页面
        |-- index.js ------------------------------------------------- 入口文件
        |-- assets --------------------------------------------------- 储存静态文件目录
        |   |-- audio 
        |   |-- components ------------------------------------------- 组件目录, 由它们组成单个页面
        |   |   |-- Footer
        |   |   |   |-- Footer.vue
        |   |   |-- Header
        |   |       |-- Header.vue
        |   |-- css
        |   |   |-- reset.css
        |   |-- img
        |   |-- pages ------------------------------------------------ 单页面目录
        |   |   |-- Home.vue 
        |   |-- utils ------------------------------------------------ 工具目录
        |   |   |-- $.js
        |   |   |-- getView.js
        |   |-- video
        |-- public --------------------------------------------------- 公共目录, 主要储存html页面与页面引入静态资源
            |-- index.html
```



# package.json

```json
{
    "name": "webpack-test2",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "dev": "webpack server --config ./config/webpack.dev.js",
        "build": "webpack --config ./config/webpack.prod.js"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "devDependencies": {
        "@babel/core": "^7.17.2",
        "@babel/polyfill": "^7.12.1",
        "@babel/preset-env": "^7.16.11",
        "autoprefixer": "^10.4.2",
        "babel-loader": "^8.2.3",
        "core-js": "^3.21.0",
        "css-loader": "^6.6.0",
        "css-minimizer-webpack-plugin": "^3.4.1",
        "esbuild-loader": "^2.18.0",
        "eslint": "^8.8.0",
        "eslint-webpack-plugin": "^3.1.1",
        "html-loader": "^3.1.0",
        "html-webpack-plugin": "^5.5.0",
        "less": "^4.1.2",
        "less-loader": "^10.2.0",
        "mini-css-extract-plugin": "^2.6.0",
        "postcss-flexbugs-fixes": "^5.0.2",
        "postcss-loader": "^6.2.1",
        "postcss-nested": "^5.0.6",
        "postcss-normalize": "^10.0.1",
        "postcss-preset-env": "^7.3.1",
        "postcss-short": "^5.0.0",
        "style-loader": "^3.3.1",
        "vue-loader": "^15.9.8",
        "vue-template-compiler": "^2.5.16",
        "webpack": "^5.68.0",
        "webpack-cli": "^4.9.2",
        "webpack-dev-server": "^4.7.4"
    },
    "browserslist": [
        "defaults",
        "not IE 11"
    ],
    "dependencies": {
        "vue": "^2.5.16"
    }
}
```



# jsconfig.json

```json
{
    "compilerOptions": {
        "target": "ES6",
        "module": "commonjs",
        "allowSyntheticDefaultImports": true,
        "baseUrl": "./",
        "paths": {
            "@/*": ["src/*"],
            "@components/*": ["src/assets/components/*"],
            "@pages/*": ["/src/assets/pages/*"]
        }
    },
    "exclude": ["node_modules"]
}

```



# postcss.config.js

```js
module.exports = {
    ident: 'postcss',
    plugins: [
        require('postcss-flexbugs-fixes'),
        require('postcss-preset-env')({
            autoprefixer: {
                flexbox: 'no-2009',
            },
            stage: 3,
        }),
        require('postcss-normalize')(),
        require('postcss-nested'),
    ],
    sourceMap: true,
}

```



# perttier.config.js

```js
module.exports = {
	tabWidth: 4, // 指定每个缩进级别的空格数。 // 默认2
	useTabs: false, // 用制表符而不是空格缩进行。// 默认false // 制表符将用于缩进，但Prettier使用空格来对齐事物，例如在三元音中
	semi: false, //在语句末尾打印分号 // 仅在可能引入 ASI 故障的行首添加分号
	singleQuote: true, // 使用单引号而不是双引号。
	trailingComma: 'es5', // 尾随逗号在ES5中有效
	bracketSpacing: true, //在对象文本中的括号之间打印空格。
	bracketSameLine: true, // 将多行 HTML（HTML、JSX、Vue、Angular）元素放在最后一行的末尾，而不是单独放在下一行（不适用于自闭合元素）。
	arrowParens: 'always', //箭头函数始终包含括号
	requirePragma: false,
	vueIndentScriptAndStyle: true //缩进 Vue 文件中的脚本和样式标签。
}

```



# webpack.dev.js

```js
const { resolve } = require('path')
// 用于整合css为同一个文件
// const MiniCssExtractPlugin = require('mini-css-extract-plugin')
// 用于压缩css文件
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')
// 用于js语法检查
const ESLintPlugin = require('eslint-webpack-plugin')
// 用于打包html文件
const HtmlWebpackPlugin = require('html-webpack-plugin')
// webpack.config.js
const { VueLoaderPlugin } = require('vue-loader')
// 压缩js代码 , webpack5自带不需要安装 ,开发环境注释
// const TerserPlugin = require('terser-webpack-plugin')

/* 将css的编译配置提取出来 */
const commonCssCompilerLoader = [
    // css文件进行兼容性处理, 将所有css文件打包为一个文件
    // {
    //     loader: MiniCssExtractPlugin.loader,
    // },
    process.env.NODE_ENV !== 'production'
        ? 'vue-style-loader'
        : MiniCssExtractPlugin.loader,
    {
        loader: 'css-loader',
        options: {
            // importLoader配置借鉴于官网
            importLoaders: 1,
        },
    },
    'postcss-loader', // css兼容性处理
]

module.exports = {
    // 入口文件
    entry: resolve(__dirname, '../src/index.js'),
    /*  entry: { // 入口文件
         main: ['../src/js/main.js', '../index.html']
     }, */
    output: {
        // 出口
        filename: 'js/bundle.js', // 输出文件名
        path: resolve(__dirname, '../dist'), // 输出文件路径配置
        clean: true, // 每次打包前都清空dist文件夹
        publicPath: '/',
    },
    mode: 'development',
    module: {
        rules: [
            /* 打包.vue文件, 这个规则要写在rules的最前面,否则会报 Make sure the rule matching .vue files include vue-loader in its use. 错误 */
            {
                test: /\.vue$/,
                loader: 'vue-loader',
                options: {
                    transformAssetUrls: {
                        video: ['src', 'poster'],
                        source: 'src',
                        img: 'src',
                        image: ['xlink:href', 'href'],
                        use: ['xlink:href', 'href'],
                    },
                    productionMode: true,
                },
            },
            {
                test: /\.css$/,
                use: [...commonCssCompilerLoader],
            },
            /* less文件转css文件, 进行兼容性处理, 与css文件打包为同一个文件 */
            {
                test: /\.less$/,
                use: [
                    ...commonCssCompilerLoader,
                    'less-loader', // less文件转为css文件
                ],
            },
            /* js兼容性处理 */
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [
                            [
                                '@babel/preset-env',
                                {
                                    useBuiltIns: 'usage', // 按需引入需要使用polyfill
                                    corejs: {
                                        version: 3,
                                    }, // 解决warn
                                    targets: {
                                        // 指定兼容性处理哪些浏览器
                                        chrome: '58',
                                        ie: '10',
                                    },
                                },
                            ],
                        ],
                        cacheDirectory: true, // 开启babel缓存
                    },
                },
            },
            /* 打包样式文件中的图片资源 */
            {
                test: /\.(jpe?g|png|gif)$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/img/[name][ext]', // 放入dist/assets/img 目录中
                },
                parser: {
                    dataUrlCondition: {
                        maxSize: 0 * 1024, // 超过0kb不转 base64
                    },
                },
            },
            /* 打包音频资源 */
            {
                test: /\.mp3$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/audio/[name][ext]', // 放入dist/assets/audio 目录中
                },
            },

            /* 打包视频资源 */
            {
                test: /\.mp4$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/video/[name][ext]', // 放入dist/assets/video 目录中
                },
            },

            /* 打包字体文件 */
            {
                test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/font/[name][ext]', // 放入dist/assets/font 目录中
                },
            },

            /* 打包html中引用的资源 */
            {
                test: /\.html$/,
                use: {
                    loader: 'html-loader',
                },
            },
        ],
    },
    optimization: {
        /* 取消以下配置的注释, 可以在开发环境下压缩css文件 */
        // minimize: true,

        /* 以下配置保证在生产环境下能够压缩css文件 */
        minimizer: [
            /* 开发环境下没必要压缩js和css代码 */
            // For webpack@5 you can use the `...` syntax to extend existing minimizers (i.e. `terser-webpack-plugin`), uncomment the next line
            // `...`,
            // css压缩
            // new CssMinimizerPlugin(),
            /* 似乎使用了CSS压缩就会造成在生产模式下webpack无法压缩js代码, 于是使用这一内置插件用以压缩js代码 */
            /* 开发环境下请注释掉下面的代码 */
            // new TerserPlugin({
            //     parallel: true, // 可省略，默认开启并行
            //     terserOptions: {
            //         toplevel: true, // 最高级别，删除无用代码
            //         ie8: true,
            //         safari10: true,
            //     }
            // })
        ],
    },
    cache: true, // 可省略，默认最优配置：生产环境，不缓存 false。开发环境，缓存到内存，memory
    // devtool: process.env.NODE_ENV === 'production' ? 'eval-cheap-module-source-map' : 'inline-source-map', // 生产环境和开发环境的配置
    devtool:
        process.env.NODE_ENV === 'production'
            ? 'eval-cheap-module-source-map'
            : 'inline-source-map', // 生产环境和开发环境的配置
    plugins: [
        // new MiniCssExtractPlugin({
        //     // 输出到dist/assets/css目录,且文件名为10位hash值
        //     filename: 'assets/css/[name].css',
        // }),
        new ESLintPlugin({
            context: resolve(__dirname), //指定文件根目录，类型为字符串
            extensions: 'js', // 指定需要检查的扩展名 ,默认js
            exclude: '/node_modules/', // 排除node_modules文件夹, 配置里可以只保留这一项
            fix: false, // 启用 ESLint 自动修复特性。 小心: 该选项会修改源文件。
            quiet: false, // 设置为 true 后，仅处理和报告错误，忽略警告
        }),
        new HtmlWebpackPlugin({
            template: resolve(__dirname, '../src/public/index.html'), // 以index.html为模板
        }),
        new VueLoaderPlugin(),
    ],

    /* dev-serve 自动化编译 */
    devServer: {
        hot: true, // 热更新
        static: resolve(__dirname, '../src/assets'),
        compress: true, //是否启动压缩 gzip
        port: 8080, // 端口号
        open: true, // 是否自动打开浏览器
        client: {
            //在浏览器端打印编译进度
            progress: false,
        },
    },
    resolve: {
        extensions: ['.js', '.vue', '.json'], // 可以省略的后缀名
        alias: {
            // 路径别名
            vue$: 'vue/dist/vue.esm.js', // 表示精准匹配 带vue编辑器
            '@': resolve(__dirname, '../src'), // 路径别名
            '@components': resolve(__dirname, '../src/assets/components'),
            '@pages': resolve(__dirname, '../src/assets/pages'),
        },
    },
}

```



# webpack.prod.js

```js
const { resolve } = require('path')
// 用于整合css为同一个文件
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
// 用于压缩css文件
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin')
// 用于js语法检查
const ESLintPlugin = require('eslint-webpack-plugin')
// 用于打包html文件
const HtmlWebpackPlugin = require('html-webpack-plugin')
// 压缩js代码 , webpack5自带不需要安装
const TerserPlugin = require('terser-webpack-plugin')
// webpack.config.js
const { VueLoaderPlugin } = require('vue-loader')

/* 将css的编译配置提取出来 */
const commonCssCompilerLoader = [
    process.env.NODE_ENV !== 'production'
        ? 'vue-style-loader'
        : MiniCssExtractPlugin.loader,
    {
        loader: 'css-loader',
        options: {
            // importLoader配置借鉴于官网
            importLoaders: 1,
            // modules: true,
            // localIdentName: '[local]_[hash:base64:5]',
        },
    },
    'postcss-loader', // css兼容性处理
]

module.exports = {
    // 入口文件
    entry: resolve(__dirname, '../src/index.js'),
    output: {
        // 出口
        filename: 'js/bundle_[hash:10].js', // 输出文件名
        path: resolve(__dirname, '../dist'), // 输出文件路径配置
        clean: true, // 每次打包前都清空dist文件夹
        publicPath: '/',
    },
    mode: 'production',
    module: {
        rules: [
            /* 打包.vue文件, 这个规则要写在rules的最前面,否则会报 Make sure the rule matching .vue files include vue-loader in its use. 错误 */
            {
                test: /\.vue$/,
                loader: 'vue-loader',
                options: {
                    transformAssetUrls: {
                        video: ['src', 'poster'],
                        source: 'src',
                        img: 'src',
                        image: ['xlink:href', 'href'],
                        use: ['xlink:href', 'href'],
                    },
                    productionMode: true,
                },
            },
            {
                test: /\.css$/,
                use: [...commonCssCompilerLoader],
            },
            /* less文件转css文件, 进行兼容性处理, 与css文件打包为同一个文件 */
            {
                test: /\.less$/,
                use: [
                    ...commonCssCompilerLoader,
                    'less-loader', // less文件转为css文件
                ],
            },
            /* js兼容性处理 */
            {
                test: /\.js$/,
                exclude: /(node_modules|bower_components)/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: [
                            [
                                '@babel/preset-env',
                                {
                                    useBuiltIns: 'usage', // 按需引入需要使用polyfill
                                    corejs: {
                                        version: 3,
                                    }, // 解决warn
                                    targets: {
                                        // 指定兼容性处理哪些浏览器
                                        chrome: '58',
                                        ie: '10',
                                    },
                                },
                            ],
                        ],
                        cacheDirectory: false, // 开启babel缓存
                    },
                },
            },
            /* 打包样式文件中的图片资源 */
            {
                test: /\.(jpe?g|png|gif)$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/img/[hash:8][ext]', // 放入dist/assets/img 目录中
                },
                parser: {
                    dataUrlCondition: {
                        maxSize: 0 * 1024, // 超过0kb不转 base64
                    },
                },
            },
            /* 打包音频资源 */
            {
                test: /\.mp3$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/audio/[hash:8][ext]', // 放入dist/assets/audio 目录中
                },
            },

            /* 打包视频资源 */
            {
                test: /\.mp4$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/video/[hash:8][ext]', // 放入dist/assets/video 目录中
                },
            },

            /* 打包字体文件 */
            {
                test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/i,
                type: 'asset',
                generator: {
                    filename: 'assets/font/[hash:8][ext]', // 放入dist/assets/font 目录中
                },
            },

            /* 打包html中引用的资源 */
            {
                test: /\.html$/,
                use: {
                    loader: 'html-loader',
                },
            },
        ],
    },
    optimization: {
        /* 取消以下配置的注释, 可以在开发环境下压缩css文件 */
        // minimize: true,

        /* 以下配置保证在生产环境下能够压缩css文件 */
        minimizer: [
            // For webpack@5 you can use the `...` syntax to extend existing minimizers (i.e. `terser-webpack-plugin`), uncomment the next line
            // `...`,
            // css压缩
            new CssMinimizerPlugin(),
            /* 似乎使用了CSS压缩就会造成在生产模式下webpack无法压缩js代码, 于是使用这一内置插件用以压缩js代码 */
            /* 开发环境下请注释掉下面的代码 */
            new TerserPlugin({
                parallel: true, // 可省略，默认开启并行
                terserOptions: {
                    toplevel: true, // 最高级别，删除无用代码
                    ie8: true,
                    safari10: true,
                },
            }),
        ],
    },
    cache: false, // 可省略，默认最优配置：生产环境，不缓存 false。开发环境，缓存到内存，memory
    // devtool: process.env.NODE_ENV === 'production' ? 'eval-cheap-module-source-map' : 'inline-source-map', // 生产环境和开发环境的配置
    devtool:
        process.env.NODE_ENV === 'production'
            ? 'eval-cheap-module-source-map'
            : 'inline-source-map', // 生产环境和开发环境的配置
    plugins: [
        new MiniCssExtractPlugin({
            // 输出到dist/assets/css目录,且文件名为10位hash值
            filename: 'assets/css/[name]_[hash:10].css',
        }),
        new ESLintPlugin({
            context: resolve(__dirname), //指定文件根目录，类型为字符串
            extensions: 'js', // 指定需要检查的扩展名 ,默认js
            exclude: '/node_modules/', // 排除node_modules文件夹, 配置里可以只保留这一项
            fix: false, // 启用 ESLint 自动修复特性。 小心: 该选项会修改源文件。
            quiet: false, // 设置为 true 后，仅处理和报告错误，忽略警告
        }),
        new HtmlWebpackPlugin({
            template: resolve(__dirname, '../src/public/index.html'), // 以index.html为模板
        }),
        new VueLoaderPlugin(),
    ],
    performance: { hints: false },
    resolve: {
        extensions: ['.js', '.vue', '.json'], // 可以省略的后缀名
        alias: {
            // 路径别名
            vue$: 'vue/dist/vue.esm.js', // 表示精准匹配 带vue编辑器
            '@': resolve(__dirname, '../src'), // 路径别名
            '@components': resolve(__dirname, '../src/assets/components'),
            '@pages': resolve(__dirname, '../src/assets/pages'),
        },
    },
}

```



# 备注

- 这份配置并不完美, css分离打包并没有得到实现 
- 实现了开发环境, 生产环境. 除了css文件被和js一起之外其它都可以正常使用
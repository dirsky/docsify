
> This is My Vue Process.



## 解决js_debugger调试问题


vue config/index.js devtool

devtool: config.build.productionSourceMap ? '#source-map' : false, // 开启source-map，生产环境下推荐使用cheap-source-map或source-map，后者得到的.map文件体积比较大，但是能够完全还原以前的js代码


必须设置成source-map才能调试
  

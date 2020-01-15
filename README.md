# tim-hieu-webpack-p1

* Về cốt lõi, webpack là một gói module tĩnh cho các ứng dụng JavaScript hiện đại. Khi webpack xử lý ứng dụng của bạn, nó sẽ xây dựng một biểu đồ phụ thuộc để ánh xạ mọi module mà dự án của bạn cần và tạo ra một hoặc nhiều gói.

* Webpack là một trình đóng gói (bundler) module tĩnh cho các ứng dụng JavaScript. Khi webpack xử lý ứng dụng của bạn, nó sẽ xây dựng một dependency graph để ánh xạ mọi mô-đun mà dự án của bạn cần và tạo ra một hoặc nhiều gói.

* Bài đầu tiên này chúng ta cùng tìm hiểu các core concept trong Webpack

# Core Concepts

## 1. Entry

* Cho biết webpack module nào sẽ sử dụng để bắt đầu xây dựng dependency graph nội bộ của nó. webpack sẽ chỉ ra các module và libraries khác mà entry point phụ thuộc vào (trực tiếp và gián tiếp).
    Theo mặc định, giá trị của nó là ./src/index.js, nhưng ta có thể chỉ định một (hoặc nhiều điểm Entry) khác nhau bằng cách đặt thuộc tính Entry trong webpack configuration. Ví dụ:

```
module.exports = {
  entry: './path/to/my/entry/file.js'
};
```

## 2. Output
* Chỉ cho webpack biết nơi emit các gói mà nó tạo và cách đặt tên cho các tệp này. Nó mặc định là ./dist/main.js cho main output file và vào file ./dist cho bất kỳ file nào được tạo khác.

* Ta có thể configure phần này của process bằng cách chỉ định output field trong configuration của mình:

webpack.config.js

```
const path = require('path');

module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js'
  }
};
```

* Trong ví dụ trên, chúng ta sử dụng các thuộc tính output.filename và output.path để báo cho webpack tên gói của chúng ta và nơi chúng ta muốn nó được emit. Trong trường hợp ta đang tự hỏi về path module được imported ở trên cùng, thì đó là module core của Node.js được sử dụng để thao tác các file paths.

## 3. Loaders
* Webpack chỉ hiểu các file JavaScript và JSON. Loaders cho phép webpack xử lý các loại file khác và convert chúng thành các module hợp lệ mà có thể được ứng dụng của bạn sử dụng và thêm vào dependency grap.

* At a high level, loaders có hai thuộc tính trong webpack configuration:

1. Thuộc tính **test**: xác định file hoặc files nào sẽ được transformed.
2. Thuộc tính **use**: cho biết loader nào sẽ được sử dụng để thực hiện transformed.

webpack.config.js

```
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js'
  },
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  }
};
```

* Configuration ở trên đã xác định một thuộc tính rules cho một module duy nhất với hai thuộc tính bắt buộc: test và use. Điều này nói với trình biên dịch của webpack như sau:

> "Này webpack compiler, khi bạn gặp một path mà resolves 1 file '.txt' bên trong câu lệnh require() / import, hãy sử dụng raw-loader để transform nó trước khi bạn thêm nó vào bundle."

* Điều quan trọng cần nhớ là khi defining các rules trong webpack config của bạn, bạn đang xác định chúng theo module.rules chứ không phải rules. Vì lợi ích của bạn, webpack sẽ cảnh báo bạn nếu điều này được thực hiện không chính xác.

* Hãy nhớ rằng khi sử dụng regex để match các file, ta có thể không quote nó. i.e /\.txt$/ không giống với '/\.txt$/' hoặc "/\.txt$/". Cái trước hướng dẫn webpack match với bất kỳ file nào có đuôi .txt và cái sau hướng dẫn webpack match với một file duy nhất với một đường dẫn tuyệt đối '.txt'; đây có thể không phải là ý định của bạn

## 4. Plugins

* Trong khi các loaders được sử dụng để transform một số loại module nhất định, các plugin có thể được tận dụng để thực hiện một phạm vi rộng hơn của các tác vụ như **bundle optimization**, **asset management** và **injection of environment variables**.

* Để sử dụng một plugin, bạn cần phải **require()** nó và thêm nó vào **plugins array**. Hầu hết các plugin đều có thể customizable thông qua các **options**. Vì bạn có thể sử dụng một plugin nhiều lần trong một config cho các mục đích khác nhau, bạn cần tạo một instance của nó bằng cách gọi nó với new operator.

webpack.config.js

```
const HtmlWebpackPlugin = require('html-webpack-plugin'); //installed via npm
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
  module: {
    rules: [
      { test: /\.txt$/, use: 'raw-loader' }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({template: './src/index.html'})
  ]
};
```

* Trong ví dụ trên, plugin-webpack-plugin tạo một file HTML cho ứng dụng của bạn bằng cách tự động injecting tất cả các bundles được tạo của bạn.

## 5. Mode

* Bằng cách setting các mode parameter thành **development**, **production** hoặc **none**, bạn có thể enable optimizations của webpack được built-in tương ứng với từng môi trường. Giá trị **default là production**.

```
module.exports = {
  mode: 'production'
};
```

## 6. Browser Compatibility
* Webpack hỗ trợ tất cả các browsers tuân thủ ES5 (IE8 trở xuống không được hỗ trợ). webpack cần Promise để import() và require.ensure (). Nếu bạn muốn hỗ trợ các browsers cũ hơn, bạn sẽ cần tải một polyfill trước khi sử dụng các expressions này.

## 7. Environment
Webpack runs trên Node.js version 8.x hoặc cao hơn.

*Như vậy chúng ta đã tìm hiểu tổng quan các concept của webpack, bài sau chúng ta sẽ đi sâu hơn vào từng thành phần này trong webpack*

# Tài liệu tham khảo 
- https://webpack.js.org/concepts













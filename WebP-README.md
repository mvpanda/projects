## 支持webp格式图片
通过调用libwebp so库文件使得Java Image I/O reader and writer(javax.imageio.ImageIO)可以读写WebP格式图片

ref: https://bitbucket.org/luciad/webp-imageio/overview

#### 用法
* 将luciad-webp-imageio-版本号.jar加入到classpath
* 将libwebp-imageio.so(linux上), libwebp-imageio.dylib or webp-imageio.dll(windows上)添加到Java native library path (java.library.path)
* 通过 Image I/O reader and writer读写WebP图片

#### 读
```java
// 方式1
BufferedImage image = ImageIO.read(new File("input.webp"));

// 方式2
// Obtain a WebP ImageReader instance
ImageReader reader = ImageIO.getImageReadersByMIMEType("image/webp").next();

// Configure decoding parameters
WebPReadParam readParam = new WebPReadParam();
readParam.setBypassFiltering(true);

// Configure the input on the ImageReader
reader.setInput(new FileImageInputStream(new File("input.webp")));

// Decode the image
BufferedImage image = reader.read(0, readParam);
```

#### 写
```java
// 方式1
// Obtain an image to encode from somewhere
BufferedImage image = ImageIO.read(new File("input.png"));

// Encode it as webp using default settings
ImageIO.write(image, "webp", new File("output.webp"));

// 方式2
// Obtain an image to encode from somewhere
BufferedImage image = ImageIO.read(new File("input.png"));

// Obtain a WebP ImageWriter instance
ImageWriter writer = ImageIO.getImageWritersByMIMEType("image/webp").next();

// Configure encoding parameters
WebPWriteParam writeParam = new WebPWriteParam(writer.getLocale());
writeParam.setCompressionMode(WebPWriteParam.LOSSLESS_COMPRESSION);

// Configure the output on the ImageWriter
writer.setOutput(new FileImageOutputStream(new File("output.webp")));

// Encode
writer.write(null, new IIOImage(image, null, null), writeParam);
```

#### so库文件和jar包编译（Linux）

_外网下载可借助代理：https://mm.nn.rrdl.pw/_

__cmake__:
1. 下载cmake-3.9.4.tar.gz
2. ```cd cmake-3.9.4```
3. ```./configure```
4. ```make```
5. ```make install```
    
__luciad-webp-imageio__:
1. 下载luciad-webp-imageio-57d9633ec7d5.zip(https://bitbucket.org/luciad/webp-imageio/downloads/)
2. 解压
    
__libwebp__:
1. 下载libwebp-0.6.0.tar.gz
2. 解压到luciad-webp-imageio根目录下（luciad-webp-imageio-57d9633ec7d5）
    
__编译libwebp-imageio.so__:
1. ```cd luciad-webp-imageio-57d9633ec7d5```
2. ```mkdir build```
3. ```cd build```
4. ```cmake ..```
5. ```cmake -build .```
6. 库文件生成在build/src/main/c目录下

__编译luciad-webp-imageio-版本号.jar__:
1. 为了避免执行grale命令时联网下载gradle压缩包，需要先自行下载gradle-3.2.1-bin.zip
2. 修改gradle/wrapper/gradle-wrapper.propertis文件的distributionUrl=https\://services.gradle.org/distributions/gradle-3.2.1-bin.zip改为distributionUrl=自己的gradle zip包
3. ```cd luciad-webp-imageio-57d9633ec7d5```
4. ```./gradlew build -x test```
5. luciad-webp-imageio-版本号.jar生成在build目录下
 
#### 项目添加jar依赖以及加载so库文件
1. 修改assemble.xml在打包时将luciad-webp-imageio-版本号.jar和libwebp-imageio.so打到lib目录下
2. 修改启动脚本server_ctl.sh把lib目录和$LD_LIBRARY_PATH拼接成JAVA_LIBRARY_PATH，再在启动时加入-Djava.library.path=${JAVA_LIBRARY_PATH}选项


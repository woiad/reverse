<a name="oBFR8"></a>
# 一、逆向基础
1、能熟练使用 hook 技术调试代码，包括cookie、xhr 等<br />2、能绕过网页调试限制如无限debugger<br />3、熟悉网页调试，可以使用xhr断点、断点、关键字搜索、hook等网页调试技术快速定位到逆向位置
<a name="deSWg"></a>
# 二、逆向经历
<a name="d4Olc"></a>
### 1、MD5算法逆向

- 主页：[https://www.mytokencap.com/](https://www.mytokencap.com/)
- 逆向参数：code: '9c503a1f852edcc3526ea56976c38edf'
- 逆向过程：经过抓包和xhr断点分析发现参数加密的位置在拦截器里面，并且加密的算法是原生MD5，没有进行魔改，可以直接用 python 调用 NodeJs 生成的加密参数来获取数据。
<a name="tdXHF"></a>
### 2、SHA256算法逆向

- 主页：[http://www.hh1024.com/](http://www.hh1024.com/)
- 逆向参数：sign: "0d2864b1420c42f12de6efeff30bcb4b458157d8177675b8910fa632524604cb"
- 逆向过程：抓包分析发现 sign 参数每次请求都会改变，直接在浏览器控制台通过全局搜索定位 sign 参数的加密位置，发现使用的是 SHA256 算法把请求参数拼接成字符串进行加密，且没有魔改算法，可以直接调用 python 的 SHA256 算法对参数加密。使用加密后的参数可以成功获取数据
<a name="Q5XBq"></a>
### 3、DES算法逆向

- 主页：[https://www.endata.com.cn/BoxOffice/BO/Month/oneMonth.html](https://www.endata.com.cn/BoxOffice/BO/Month/oneMonth.html)
- 数据：[https://www.endata.com.cn/API/GetData.ashx](https://www.endata.com.cn/API/GetData.ashx)
- 逆向过程：接口请求返回的数据是加密的，因此需要对密文进行逆向，找出解密的位置和算法。后端返回的加密数据解密之后会是字符串，前端需要使用数据的话，肯定会使用 JSON.parse 函数把字符串转为对象。利用这一特性，可以对 JSON.parse hook，然后通过函数调用栈就可以找到数据解密的位置。利用 hook 可以快速定位到数据解密的位置。定位到参数加密位置后，需要把页面代码扣下来，把主要的解密代码扣下来后，在编辑器里面运行代码，发现少了什么就去页面上，把少的东西拿下来，一步一步的把代码补齐。直到最后可以成功解密数据。
<a name="Z90g3"></a>
### 4、AES算法逆向

- 主页：[https://jzsc.mohurd.gov.cn/data/company](https://jzsc.mohurd.gov.cn/data/company)
- 数据：[https://jzsc.mohurd.gov.cn/api/webApi/dataservice/query/comp/list](https://jzsc.mohurd.gov.cn/api/webApi/dataservice/query/comp/list)
- 逆向过程：通过浏览器控制面板发现需要解密的是响应参数，因此可以使用 hook JSON.parse 的方法快速定位到解密位置。也可以使用 xhr 断点或关键字搜索方法(decrypt/encrypt)等方法定位加密位置，但是 hook 的方法快速一点。定位到加密位置后，便发现使用的是 AES 加密方法，且没有魔改算法，可以直接使用 AES 算法库对响应参数进行解密（IV和KEY网友是直接写死的，可以直接使用）。通过 python 的 ExecJs 可以直接调用 nodejs，所以通过 python 拿到响应参数之后，就可以通过 ExecJs 拿到解密后的数据。
<a name="tYBTm"></a>
### 5、魔改算法

- 主页：[https://www.aigei.com/sound/class/role?page=7#resContainer](https://www.aigei.com/sound/class/role?page=7#resContainer)
- 数据：[https://www.aigei.com/gei-common/jsonComp/f/awd/log.json?t=5657288%2C30409027%2C29958178%2C5633685%2C5657573%2C5635533%2C30392468%2C5657696%2C5656237%2C5656067%2C30377190%2C5634248%2C41239479%2C2286941%2C29951406%2C69050439%2C5636242%2C2286905%2C5632707%2C68820738&w=rowPc&f=false](https://www.aigei.com/gei-common/jsonComp/f/awd/log.json?t=5657288%2C30409027%2C29958178%2C5633685%2C5657573%2C5635533%2C30392468%2C5657696%2C5656237%2C5656067%2C30377190%2C5634248%2C41239479%2C2286941%2C29951406%2C69050439%2C5636242%2C2286905%2C5632707%2C68820738&w=rowPc&f=false)
- 逆向过程：通过浏览器的网络抓包发现响应参数b是加密的，需要对参数b逆向拿到解密之后的数据。同理通过 hook 技术快速定位到解密位置。定位到解密位置后发现使用的是魔改之后的算法，这种情况下，只能扣页面的代码来解密。把主要的解密代码扣下来之后，在编辑器一步一步的调试，缺什么就补什么，不断地完善扣下来的代码，直到最后代码可以成功运行并且可以解密数据。
<a name="PUSD2"></a>
### 6、非对称算法

- 主页：[http://birdreport.cn/home/activity/page.html](http://birdreport.cn/home/activity/page.html)
- 数据：[https://api.birdreport.cn/front/activity/search](https://api.birdreport.cn/front/activity/search)
- 逆向过程：抓包分析发现，在请求数据的时候，请求头的参数 Sign 加密。因此可以直接通过搜索关键字的方法 Sing 去定位加密的位置，同时还对载荷数据也做了加密的处理。分析后发现，Sign 参数使用的是 MD5 加密，通过时间戳 + uuid + 请求载荷字符拼接成字符串，再对字符串进行MD5加密。data 使用的是 RSA 加密。公钥页面上有，可以直接获取。返回的数据则是通过 AES 解密的，IV和key页面上也有。通过 python 和 ExceJs 配合使用就可以请求数据并获取数据。

<a name="pcCGD"></a>
### 7、webpack 逆向

- 主页：[https://36kr.com/](https://36kr.com/)
- 逆向参数：password: 8cbf7f88e70300def68533a74c77b785e11d743c77627b624
- 逆向过程：通过关键字搜索 password 定位到参数加密位置，经过分析之后发现页面是通过webpack打包的，因此需要把加载器和页面用到的模块扣下来。把主要的代码扣下来之后，就可以调试代码，把缺少的模块去网页上扣下来。weback 打包的代码与其他的不同，需要找到加载器和对应的模块，加载器一般是一个自行方法，而模块则是传给自执行方法的参数，这个参数可能是数组，或对象。通过调试，就可以一步一步的还原代码，把网页的加密方法实现。通过 python 调用 ExecJs 就可以拿到加密后的参数，python 就可以请求数据。

<a name="ysOeA"></a>
### 8、ob混淆逆向

- 主页：[https://bz.zzzmh.cn/index](https://bz.zzzmh.cn/index)
- 接口：[https://api.zzzmh.cn/bz/v3/getData](https://api.zzzmh.cn/bz/v3/getData)
- 逆向过程：经过分析发现，对响应进行了加密。这时候可以通过 hook JSON.parse 找到加密位置。调试的时候发现代码都是 _0x 开头，这些代码都是经过混淆的，用的是 ob 混淆技术。混淆之后的代码，主要注意三部分的代码，一个是大数组，里面存放的是数据，一个是自执行方法，里面会对大数组进行重新排序，还有一个就是解密函数了，对混淆之后的数据做解密处理。需要把这些都扣下来，才能成功的运行从网友上扣下来的代码。
<a name="A0Cj2"></a>
### 9、RPC技术逆向

- 主页：[http://q.10jqka.com.cn/](http://q.10jqka.com.cn/)
- [http://q.10jqka.com.cn/index/index/board/all/field/zdf/order/desc/page/2/ajax/1/](http://q.10jqka.com.cn/index/index/board/all/field/zdf/order/desc/page/2/ajax/1/)
- 逆向参数：cookie 的 v参数
- 逆向过程：经过分析发现 cookie 的 v 参数是加密的。因此，可以使用 hook 快速定位到加密的位置。定位到加密的位置之后，需要先把加密的js文件替换为本地文件，这样就可以对文件进行编辑了。在定位到的加密位置，把rpc的代码注入到加密的位置，然后在 python 调用就可以了。使用 Sekiro 库来实现 RPC逆向。

<a name="tMfVr"></a>
### 10、cookie-瑞数系列 逆向

- 主页：[http://www.fangdi.com.cn/index.html](http://www.fangdi.com.cn/index.html)
- 逆向参数：cookie 的 FSSBBIl1UgzbN7Nenable 参数
- 逆向过程：抓包分析之后发现请求页面的时候，会有两次请求，第一次请求返回 412，第二次请求返回200，第二次请求返回的数据才是正确的。第二次请求会携带第一次请求服务器返回的 cookie 和第一次请求返回的代码生成的cookie。因此，逆向主要是针对第一次请求返回的代码。第一次请求返回的是 html 页面，页面有一段自执行代码和一个外链的 ts文件代码。需要把这两个代码拿下来。同时还需要 meta 标签里面的 content。把这些都扣下来之后，就需要补环境。NodeJs 运行代码的环境和网页运行代码的环境不一致，因此需要手动补齐代码需要的环境才能正确的生成cookie 参数。

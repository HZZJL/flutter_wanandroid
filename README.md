# Flutter版 WanAndroid App.


## 项目已全部开源。欢迎Star&Fork。
App拥有漂亮的UI界面，统一的交互，侧滑退出，列表和Web界面均提供快速滚动至顶部功能(界面参考[gitme](https://flutterchina.club/app/gm.html))。  
本项目包含启动页，引导页，主题色，国际化，Bloc，RxDart。拥有较好的项目结构，比较规范的代码。  
作者初衷是为大家提供一个比较规范的Flutter项目示例。  
有关项目最新动态，可以关注App内第一条Hot Item信息。

[更新说明](./CHANGELOG.md)

## App目录结构
>- |--lib
>    - |-- blocs (bloc相关)
>    - |-- common (常用类，例如常量Constant)
>    - |-- data (网络数据层)
>    - |-- db (数据库)
>    - |-- event (事件类)
>    - |-- models (实体类)
>    - |-- resources (资源文件，string，colors，dimens，styles)
>    - |-- ui (界面相关page，dialog，widgets)
>    - |-- utils (工具类)
## data网络数据层
>- |--data
>    - |-- api (url字段)
>    - |-- net (单例DioUtil)
>    - |-- protocol (请求与返回实体类)
>    - |-- repository (接口请求&解析)
### api
```
class WanAndroidApi {
  /// 首页banner http://www.wanandroid.com/banner/json
  static const String BANNER = "banner";
  static const String USER_REGISTER = "user/register"; //注册
  static const String USER_LOGIN = "user/login"; //登录
  static const String USER_LOGOUT = "user/logout"; //退出

  // 拼接url
  static String getPath({String path: '', int page, String resType: 'json'}) {
    StringBuffer sb = new StringBuffer(path);
    if (page != null) {
      sb.write('/$page');
    }
    if (resType != null && resType.isNotEmpty) {
      sb.write('/$resType');
    }
    return sb.toString();
  }
}
```
### 请求与返回实体类 protocol
```
class LoginReq {
  String username;
  String password;

  LoginReq(this.username, this.password);

  LoginReq.fromJson(Map<String, dynamic> json)
      : username = json['username'],
        password = json['password'];

  Map<String, dynamic> toJson() => {
    'username': username,
    'password': password,
  };

  @override
  String toString() {
    StringBuffer sb = new StringBuffer('{');
    sb.write("\"username\":\"$username\"");
    sb.write(",\"password\":$password");
    sb.write('}');
    return sb.toString();
  }
}
```
### 接口请求&解析 repository
```
 class WanRepository {
  Future<List<BannerModel>> getBanner() async {
    BaseResp<List> baseResp = await DioUtil().request<List>(
        Method.get, WanAndroidApi.getPath(path: WanAndroidApi.BANNER));
    List<BannerModel> bannerList;
    if (baseResp.code != Constant.STATUS_SUCCESS) {
      return new Future.error(baseResp.msg);
    }
    if (baseResp.data != null) {
      bannerList = baseResp.data.map((value) {
        return BannerModel.fromJson(value);
      }).toList();
    }
    return bannerList;
  }
}
```

## 资源文件 resources
>- |--resources
>    - |-- colors.dart
>    - |-- dimens.dart
>    - |-- strings.dart
>    - |-- styles.dart

### colors.dart
```
class ColorT {
  static const Color app_main = Color(0xFF666666);  
  
  static const Color text_dark = Color(0xFF333333);
  static const Color text_normal = Color(0xFF666666);
  static const Color text_gray = Color(0xFF999999);  
}
```
### dimens.dart
```
class Dimens {
  static const double font_sp12 = 12;
  static const double font_sp14 = 14;
  static const double font_sp16 = 16;
  
  static const double gap_dp5 = 5;
  static const double gap_dp10 = 10;
}
```
### strings.dart
```
class Ids {
  static const String titleHome = 'title_home';
}  
Map<String, Map<String, Map<String, String>>> localizedValues = {
  'en': {
    'US': {
      Ids.titleHome: 'Home',
    }
  },
  'zh': {
    'CN': {
      Ids.titleHome: '主页',
    },
    'HK': {
      Ids.titleHome: '主頁',
    },
    'TW': {
      Ids.titleHome: '主頁',
    }
  }
};
```
### styles.dart
```
class TextStyles {
  static TextStyle listTitle = TextStyle(
    fontSize: Dimens.font_sp16,
    color: ColorT.text_dark,
    fontWeight: FontWeight.bold,
  );
  static TextStyle listContent = TextStyle(
    fontSize: Dimens.font_sp14,
    color: ColorT.text_normal,
  );
  static TextStyle listExtra = TextStyle(
    fontSize: Dimens.font_sp12,
    color: ColorT.text_gray,
  );
}
```
### Flutter 国际化相关
[fluintl](https://github.com/Sky24n/fluintl) 是一个为应用提供国际化的库，可快速集成实现应用多语言。该库封装了一个国际化支持类，通过提供统一方法getString(id)获取字符串。
```
// 在MyApp initState配置多语言资源
setLocalizedValues(localizedValues); //配置多语言资源
// 在MaterialApp指定localizationsDelegates和supportedLocales
MaterialApp(  
   home: MyHomePage(),  
   localizationsDelegates: [  
   GlobalMaterialLocalizations.delegate,  
   GlobalWidgetsLocalizations.delegate,  
   CustomLocalizations.delegate //设置本地化代理     
   ],  
   supportedLocales: CustomLocalizations.supportedLocales,//设置支持本地化语言集合     
); 
// 字符串获取
IntlUtil.getString(context, Ids.titleHome);
CustomLocalizations.of(context).getString(StringIds.titleHome);
```

### Flutter 屏幕适配 [ScreenUtil](https://github.com/Sky24n/flustars)
 方案一、不依赖context
```
步骤 1
//如果设计稿尺寸默认配置一致，无需该设置。  配置设计稿尺寸 默认 360.0 / 640.0 / 3.0
setDesignWHD(_designW,_designH,_designD);  
  
步骤 2
// 在MainPageState build 调用MediaQuery.of(context)
class MainPageState extends State<MainPage> {
  @override
  Widget build(BuildContext context) {
    // 在 MainPageState build 调用 MediaQuery.of(context)
    MediaQuery.of(context);
    return new Scaffold(
      appBar: new AppBar(),
    );
  }
}  
  
步骤 3
ScreenUtil.getInstance().screenWidth
ScreenUtil.getInstance().screenHeight
//屏幕适配相关  
ScreenUtil.getInstance().getWidth(size); //返回根据屏幕宽适配后尺寸（单位 dp or pt）
ScreenUtil.getInstance().getHeight(size); //返回根据屏幕高适配后尺寸 （单位 dp or pt）
ScreenUtil.getInstance().getWidthPx(sizePx); //sizePx 单位px
ScreenUtil.getInstance().getHeightPx(sizePx); //sizePx 单位px
ScreenUtil.getInstance().getSp(fontSize); //返回根据屏幕宽适配后字体尺寸

```
方案二、依赖context
```
//如果设计稿尺寸默认配置一致，无需该设置。  配置设计稿尺寸 默认 360.0 / 640.0 / 3.0
setDesignWHD(_designW,_designH,_designD);  

ScreenUtil.getScreenW(context); //屏幕 宽
ScreenUtil.getScreenH(context); //屏幕 高
//屏幕适配相关  
ScreenUtil.getScaleW(context, size); //返回根据屏幕宽适配后尺寸（单位 dp or pt）
ScreenUtil.getScaleH(context, size); //返回根据屏幕高适配后尺寸 （单位 dp or pt）
ScreenUtil.getScaleSp(context, size) ;//返回根据屏幕宽适配后字体尺寸
```
### Flutter 数据存储  [SpUtil](https://github.com/Sky24n/flustars)
SpUtil : 单例"同步" SharedPreferences 工具类。
项目中为大家提供SpHelper，方便存取实体对象类。
```
// 存储SplashModel实体对象
SplashModel model = new SplashModel();
SpHelper.putObject(Constant.KEY_SPLASH_MODEL, model);
// 获取SplashModel实体对象
SplashModel model = SpHelper.getSplashModel(); 
class SpHelper {
 // 存储Obj，T 用于区分存储类型
  static void putObject<T>(String key, Object value) {
    switch (T) {
      case int:
        SpUtil.putInt(key, value);
        break;
      case double:
        SpUtil.putDouble(key, value);
        break;
      case bool:
        SpUtil.putBool(key, value);
        break;
      case String:
        SpUtil.putString(key, value);
        break;
      case List:
        SpUtil.putStringList(key, value);
        break;
      default:
        SpUtil.putString(key, value == null ? "" : json.encode(value));
        break;
    }
  }

  static SplashModel getSplashModel() {
    String _splashModel = SpUtil.getString(Constant.KEY_SPLASH_MODEL);
    if (ObjectUtil.isNotEmpty(_splashModel)) {
      Map userMap = json.decode(_splashModel);
      return SplashModel.fromJson(userMap);
    }
    return null;
  }
}
```

### APK:[点击下载 v0.1.3](https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppStore/flutter_wanandroid.apk)

### APK QR:
  ![flutter_wanandroid](https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/qrcode.png)

## Screenshot
### 主界面
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/home.gif" width="240">  

### 启动页
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/splash.gif" width="240"> 

### 侧滑Back
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/slide_back.gif" width="240"> 

### 快速滚动到顶部
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/scroll_top.gif" width="240"> 

### 分类页面
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/tree.gif" width="240"> 

### 国际化 
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/intl.gif" width="240">  

### 主题色 
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/theme_color.gif" width="240">  

### 闪屏广告页 
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/2018-11-23_13_05_08.gif" width="240">  
 
### 引导页 
<img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/2018-11-19_12_35_32.gif" width="240">  

### Big Thanks
① 感谢鸿洋大佬提供的[开源api](http://www.wanandroid.com/blog/show/2)  
② 界面参考[gitme](https://flutterchina.club/app/gm.html)  
③ 优秀[开源库](https://www.jianshu.com/p/6d3c648340b6)

## 作者简书，欢迎关注～
 [![jianshu][jianshuSvg]][jianshu]

## Donations
 如果您觉得该项目不错的话，并且希望作者持续升级维护，欢迎随意打赏，请作者喝杯咖啡～   
 <img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/alipay.png" width="240">  <img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/wechat.png" width="240">    
 <img src="https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/alipay_reward.png" width="240">


[flutter_wanandroid_github]: https://github.com/Sky24n/flutter_wanandroid
[flutter_wanandroid_apk]: https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppStore/flutter_wanandroid.apk
[flutter_wanandroid_qr]: https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_wanandroid/qrcode.png

[flutter_demos_github]: https://github.com/Sky24n/flutter_demos
[flutter_demos_apk]: https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppStore/flutter_demos.apk
[flutter_demos_qr]: https://raw.githubusercontent.com/Sky24n/LDocuments/master/AppImgs/flutter_demos/qrcode.png

[common_utils_github]: https://github.com/Sky24n/common_utils

[flustars_github]: https://github.com/Sky24n/flustars

[jianshuSvg]: https://img.shields.io/badge/简书-@Sky24n-34a48e.svg
[jianshu]: https://www.jianshu.com/u/cbf2ad25d33a




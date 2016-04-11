#JSPatch的基本用法
===
### 在我们的项目开发中，经常会碰到已经上线的项目发现bug这样的问题，针对这样的问题一般的解决方案都是提交新版本，然后等待AppStore审核，但是这种解决方案一般都耗时比较长，一般都需要花费一周左右的时间，为了避免等待漫长的AppStore审核，我们可以在以后的项目中导入[JSPatch](https://github.com/bang590/JSPatch)这个框架，实现热更新的功能。
===
##JSPatch的基本原理
###JSPatch用iOS内置的JavaScriptCore.framework`作为JS引擎，通过Objective-C Runtime，从JS传递要调用的类名函数名到Objective-C，再使用NSInvocation动态调用对应的Objective-C 方法。
===
##热更新解决方案
### 1.在AppDelegate的 (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions方法中启动JSPatch引擎(下载脚本有同步和异步两种方法)。

* ###同步下载
```Objective-C
[JPEngine startEngine];
    NSURL *url = [NSURL URLWithString:@"https://raw.githubusercontent.com/Mag1cPanda/JSPatchTest/master/callx.js"];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    NSError *error = nil;
    NSData   *data = [NSURLConnection sendSynchronousRequest:request
                                           returningResponse:nil
                                                       error:&error];
    
    NSString *script = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
    [JPEngine evaluateScript:script];
```

* ###异步下载
```Objective-C
[JPEngine startEngine];
    NSURL *url = [NSURL URLWithString:@"https://raw.githubusercontent.com/Mag1cPanda/JSPatchTest/master/callx.js"];
    [NSURLConnection sendAsynchronousRequest:[NSURLRequest requestWithURL:url] queue:[NSOperationQueue mainQueue] completionHandler:^(NSURLResponse *response, NSData *data, NSError *connectionError) {
        
        NSString *script = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
        [JPEngine evaluateScript:script];
        
        
        UIStoryboard *sb = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
        ViewController *vc = [sb instantiateInitialViewController];
        self.window.rootViewController = vc;
    }];
```
### 2.在发现bug的时候，在GitHub上配置好用来修复bug的脚本，(放到gitHub上是为了测试方便，后期可以部署到我们自己的服务器上)，配置好后提交到gitHub仓库中，程序再次运行时，有bug的方法就被替换了，这样就在无需等待AppStore审核的情况下实现了热更新。













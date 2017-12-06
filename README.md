# CAAnimationUtil

QuartzCore框架下CAAnimation的动画集合：移动、旋转、缩放、弹簧、组合动画以及各种翻页效果和落叶动画。

![CAAnimationUtil](Screenshot.gif)

### 移动

```ruby
- (void)move
{
    // 位置移动
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"position"];
    // 1秒后执行
    animation.beginTime = CACurrentMediaTime() + 1;
    // 持续时间
    animation.duration = 2.5;
    // 重复次数
    animation.repeatCount = 2;
    // 起始位置
    animation.fromValue = [NSValue valueWithCGPoint:_moveView.layer.position];
    // 终止位置
    animation.toValue = [NSValue valueWithCGPoint:CGPointMake(_moveView.layer.position.x + 100, _moveView.layer.position.y)];
    // 添加动画
    [_moveView.layer addAnimation:animation forKey:@"move"];
}
```

### 旋转

```ruby
- (void)rotate
{
    // 对Y轴进行旋转
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.rotation.y"];
    // 1秒后执行
    animation.beginTime = CACurrentMediaTime() + 1;
    // 持续时间
    animation.duration = 2.5;
    // 重复次数
    animation.repeatCount = 2;
    // 起始角度
    animation.fromValue = @(0.0);
    // 终止角度
    animation.toValue = @(2 * M_PI);
    // 添加动画
    [_rotateView.layer addAnimation:animation forKey:@"rotate"];
}
```

### 缩放

```ruby
- (void)zoom
{
    // 比例缩放
    CABasicAnimation *animation = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
    // 1秒后执行
    animation.beginTime = CACurrentMediaTime() + 1;
    // 持续时间
    animation.duration = 2.5;
    // 重复次数
    animation.repeatCount = 2;
    // 起始scale
    animation.fromValue = @(1.0);
    // 终止scale
    animation.toValue = @(1.5);
    // 添加动画
    [_zoomView.layer addAnimation:animation forKey:@"zoom"];
}
```

### 弹簧

```ruby
- (void)spring
{
    // 位置移动
    CASpringAnimation *animation = [CASpringAnimation animationWithKeyPath:@"position"];
    // 1秒后执行
    animation.beginTime = CACurrentMediaTime() + 1;
    // 阻尼系数（此值越大弹框效果越不明显）
    animation.damping = 2;
    // 刚度系数（此值越大弹框效果越明显）
    animation.stiffness = 50;
    // 质量大小（越大惯性越大）
    animation.mass = 1;
    // 初始速度
    animation.initialVelocity = 10;
    // 开始位置
    [animation setFromValue:[NSValue valueWithCGPoint:_springView.layer.position]];
    // 终止位置
    [animation setToValue:[NSValue valueWithCGPoint:CGPointMake(_springView.layer.position.x, _springView.layer.position.y + 50)]];
    // 持续时间
    animation.duration = animation.settlingDuration;
    // 添加动画
    [_springView.layer addAnimation:animation forKey:@"spring"];
}
```

### 组合动画

```ruby
- (void)group
{
    // x方向平移
    CABasicAnimation *animation1 = [CABasicAnimation animationWithKeyPath:@"transform.translation.x"];
    // 平移100
    animation1.toValue = @(100);
    
    // 绕Z轴中心旋转
    CABasicAnimation *animation2 = [CABasicAnimation animationWithKeyPath:@"transform.rotation.z"];
    // 起始角度
    animation2.fromValue = [NSNumber numberWithFloat:0.0];
    // 终止角度
    animation2.toValue = [NSNumber numberWithFloat:2 * M_PI];
    
    // 比例缩放
    CABasicAnimation *animation3 = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
    // 终止scale
    animation3.toValue = @(0.5);
    
    // 动画组
    CAAnimationGroup *group = [CAAnimationGroup animation];
    // 1秒后执行
    group.beginTime = CACurrentMediaTime() + 1;
    // 持续时间
    group.duration = 2.5;
    // 重复次数
    group.repeatCount = 2;
    // 动画结束是否恢复原状
    group.removedOnCompletion = YES;
    // 动画组
    group.animations = [NSArray arrayWithObjects:animation1, animation2, animation3, nil];
    // 添加动画
    [_groupView.layer addAnimation:group forKey:@"group"];
}
```

### 翻页动画

**动画效果的枚举**

```ruby
typedef NS_ENUM(NSInteger,AnimationType) {
    kAnimationTypeFade,                         //淡入淡出
    kAnimationTypeMoveIn,                       //覆盖
    kAnimationTypePush,                         //推挤
    kAnimationTypeReveal,                       //揭开
    kAnimationTypeCube,                         //3D立方体
    kAnimationTypeSuckEffect,                   //吮吸
    kAnimationTypeOglFlip,                      //翻转
    kAnimationTypeRippleEffect,                 //波纹
    kAnimationTypePageCurl,                     //翻页
    kAnimationTypePageUnCurl,                   //反翻页
    kAnimationTypeCameraIrisHollowOpen,         //开镜头
    kAnimationTypeCameraIrisHollowClose,        //关镜头
    kAnimationTypeCurlDown,                     //下翻页
    kAnimationTypeCurlUp,                       //上翻页
    kAnimationTypeFlipFromLeft,                 //左翻转
    kAnimationTypeFlipFromRight                 //右翻转
} ;
```
其中包含8个私有API，使用过程需要留意，私有API是不被AppStore接受的。

```ruby
// 全局常量
NSString * const kCATransitionCube = @"cube";
NSString * const kCATransitionSuckEffect = @"suckEffect";
NSString * const kCATransitionOglFlip = @"oglFlip";
NSString * const kCATransitionRippleEffect = @"rippleEffect";
NSString * const kCATransitionPageCurl = @"pageCurl";
NSString * const kCATransitionPageUnCurl = @"pageUnCurl";
NSString * const kCATransitionCameraIrisHollowOpen = @"cameraIrisHollowOpen";
NSString * const kCATransitionCameraIrisHollowClose = @"cameraIrisHollowClose";
```

**使用方式**

1.CAAnimation动画使用方式如下：

```ruby
- (void)transitionWithType:(NSString *)type subtype:(NSString *)subtype
{
    CATransition *animation = [CATransition animation];
    // 动画时间
    animation.duration = DURATION;
    // 动画类型
    animation.type = type;
    // 动画子类型
    if (subtype) {
        animation.subtype = subtype;
    }
    // 动画缓冲|速度
    animation.timingFunction = UIViewAnimationOptionCurveEaseInOut;
    [self.view.layer addAnimation:animation forKey:@"animation"];
}
```

2.UIView动画使用方式如下：

```objc
- (void)animationWithTransition:(UIViewAnimationTransition)transition
{
    [UIView animateWithDuration:DURATION animations:^{
        // 详见UIViewAnimationCurve
        [UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
        [UIView setAnimationTransition:transition forView:self.view cache:YES];
    }];
}                            
```

PS：动画是直接加在self.view上的，可根据需要自行修改，具体效果见Demo吧。

### 落叶动画

使用UIView动画实现落叶效果，具体实现如下：

```objc
// 首先添加定时器
- (void)viewDidLoad
{
    [super viewDidLoad];
    // 动画定时器
    flowTimer = [NSTimer scheduledTimerWithTimeInterval:0.8 target:self selector:@selector(downLeaf) userInfo:nil repeats:YES];
}

// 定时器方法【落叶实现】
- (void)downLeaf
{
    // 使用落叶图片的角标
    NSInteger leafNumber = random() % 5;
    // 起始位置随机
    NSInteger startX = self.view.bounds.size.width;
    startX = random() % startX;
    // 结束位置随机
    NSInteger endX = self.view.bounds.size.height;
    endX = random() % endX;
    // 叶子大小随机
    CGFloat scale = 1 / (random() % 10 + 1) + 1.0;
    // 叶子下落速度随机
    CGFloat speed = 1 / (random() % 10 + 1) + 1.0;
    // 创建叶子imageView
    UIImageView *leafImageView = [[UIImageView alloc] initWithFrame:CGRectMake(startX, -30.0* scale, 30.0 * scale, 30.0 * scale)];
    leafImageView.image = [UIImage imageNamed:[NSString stringWithFormat:@"leaf_%ld",(long)leafNumber]];
    leafImageView.alpha = 0.8;
    [self.view addSubview:leafImageView];
    // 开始动画
    [UIView beginAnimations:@"leaf" context:(__bridge void * _Nullable)(leafImageView)];
    [UIView setAnimationDuration:10 * speed];
    leafImageView.frame = CGRectMake(endX, self.view.bounds.size.height, 35.0 * scale, 35.0 * scale);
    leafImageView.alpha = 1.0;
    [UIView setAnimationDidStopSelector:@selector(animationCompletion:finished:context:)];
    [UIView setAnimationDelegate:self];
    [UIView commitAnimations];
}

//下落到底部，移除
- (void)animationCompletion:(NSString *)animationID finished:(NSNumber *)finished context:(void *)context
{
    UIImageView *leafImageView = (__bridge UIImageView *)(context);
    [leafImageView removeFromSuperview];
}
```


### 参考链接

https://github.com/lizelu/CATransitionDemo

http://blog.csdn.net/iosevanhuang/article/details/14488239



@interface smashEffect : UIView;
/**
 *  还原方法
 */
- (void)restore;
/**
 *  打碎方法
 */
- (void)smash;
/**
 *
 *  @param frame frame
 *  @param image 需要打碎的图片
 */
-(instancetype)initWithFrame:(CGRect)frame Image:(UIImage *)image;
@end



#import "smashEffect.h"
@interface smashEffect ()

@end
@implementation smashEffect
-(instancetype)initWithFrame:(CGRect)frame Image:(UIImage *)image
{
    if (self = [super init]) {
        self.frame = frame;
        CGFloat ratio = 0.1;
        for (int i = 0; i < 100; i++) {
            UIImageView *imageView = [[UIImageView alloc] init];
            imageView.backgroundColor = [UIColor redColor];
            imageView.image = image;
            NSInteger column = i % 10;    // 列
            NSInteger row = i / 10;      // 行
            //层内容的可视区域
            imageView.layer.contentsRect = CGRectMake(ratio*column, row*ratio, ratio, ratio);
            imageView.frame = CGRectMake(self.frame.size.width*ratio*column, row*(self.frame.size.height*ratio), self.frame.size.width*ratio, self.frame.size.height*ratio);
            [self addSubview:imageView];
        }
    }
    return self;
}
// 打碎
- (void)smash
{
    [UIView animateWithDuration:1 animations:^{
        [self.subviews enumerateObjectsUsingBlock:^(__kindof UIView * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            UIImageView *imageView = obj;
            imageView.layer.transform = [self configTransform3DWithRotateAngle:[self getRandomNumber:0 to:360] andPositionX:[self getRandomNumber:0 to:100] andPositionY:[self getRandomNumber:0 to:100]];
        }];
    }];
    
}
//还原
- (void)restore
{
    [UIView animateWithDuration:1 animations:^{
        [self.subviews enumerateObjectsUsingBlock:^(__kindof UIView * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            UIImageView *imageView = obj;
            imageView.layer.transform = CATransform3DIdentity;
        }];
    }];
}
/**
 *  传进一个角度，x坐标，y坐标
 *
 *  @param angle 角度
 *  @param x     x坐标
 *  @param y     y坐标
 *
 *  @return transform属性
 */
- (CATransform3D)configTransform3DWithRotateAngle:(double)angle andPositionX:(double)x andPositionY:(double)y
{
    CATransform3D transform = CATransform3DIdentity;
    //  iOS的三维透视投影 实现view（layer）的透视效果（就是近大远小），是通过设置m34的参考：http://blog.csdn.net/yongyinmg/article/details/38780953
    //    transform.m34 = 1/0;
    /*
     旋转 CATransform3DRotate (CATransform3D t, CGFloat angle,CGFloat x, CGFloat y, CGFloat z) angle旋转弧度：角度 * M_PI / 180，
     x值范围-1 --- 1之间 正数表示左侧看向外旋转，负数表示向里CATransform3DRotate(transform, M_PI*angle/180, 1, 0, 0）
     y值范围-1 --- 1之间 正数左侧看表示向外旋转，负数表示向里CATransform3DRotate(transform, M_PI*angle/180, 0, 1, 0）
     同时设置x，y表示沿着对角线翻转
     CATransform3DRotate(transform, M_PI*angle/180, 1, 1, 0）
     z值范围-1 --- 1之间 正数逆时针旋转，负数表示顺CATransform3DRotate(transform, M_PI*angle/180, 0, 0, -1）
     同时设置x,y,z按照设定的数值进行旋转
     CATransform3D rotateTransform = CATransform3DRotate(transform, M_PI*angle/180, 1, 1, 1);
     */
    CATransform3D rotateTransform = CATransform3DRotate(transform, M_PI*angle/180, 1, 1, 1);
    // 移动
    CATransform3D moveanim = CATransform3DMakeTranslation(x, y, 0);
    // 合并
    CATransform3D concatTransform = CATransform3DConcat(rotateTransform, moveanim);
    return concatTransform;
}
/**
 *  获取范围随机数
 *
 *  @param from 最小
 *  @param to   最大
 *
 *  @return 返回一个from到to之间的一个随机值
 */
-(CGFloat)getRandomNumber:(int)from to:(int)to
{
    return (from+ 1 + (arc4random() % (to - from + 1)));
}
@end

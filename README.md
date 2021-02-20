### 一、开发经验总结

1. 当编译发现大量错误的时候，从第一个看起，一个一个的解决，不要急着去看下一个错误，往往后面的错误都是由于前面的错误引起的，第一个解决后很可能都解决了。

2. 定时器是个好东西，学会好使用它，有时候用QTimer::singleShot可以解决意想不到的问题。

3. 打开creator，在构建套件的环境中增加MAKEFLAGS=-j8，可以不用每次设置多线程编译。珍爱时间和生命。新版的QtCreator已经默认就是j8。

4. 如果你想顺利用QtCreator部署安卓程序，首先你要在AndroidStudio 里面配置成功，把坑全部趟平。

5. 很多时候找到Qt对应封装的方法后，记得多看看该函数的重载，多个参数的，你会发现不一样的世界，有时候会恍然大悟，原来Qt已经帮我们封装好了。

6. 可以在pro文件中写上标记版本号+ico图标（Qt5才支持）
``` c++
VERSION  = 2021.02.25
RC_ICONS = main0.ico
```

7. 管理员运行程序，限定在MSVC编译器。
``` c++
QMAKE_LFLAGS += /MANIFESTUAC:"level='requireAdministrator' uiAccess='false'" #以管理员运行
QMAKE_LFLAGS += /SUBSYSTEM:WINDOWS,"5.01" #VS2013 在XP运行
```

8. 运行文件附带调试输出窗口
CONFIG += console pro

9. 绘制平铺背景QPainter::drawTiledPixmap,绘制圆角矩形QPainter::drawRoundedRect(),而不是QPainter::drawRoundRect();

10. 移除旧的样式
``` c++
//移除原有样式
style()->unpolish(ui->btn);
//重新设置新的该控件的样式。
style()->polish(ui->btn);
```

11. 获取类的属性
``` c++
const QMetaObject *metaobject = object->metaObject();
int count = metaobject->propertyCount();
for (int i = 0; i < count; ++i) {
    QMetaProperty metaproperty = metaobject->property(i);
    const char *name = metaproperty.name();
    QVariant value = object->property(name);
    qDebug() << name << value;
}
```

12. Qt内置图标封装在QStyle中，大概七十多个图标，可以直接拿来用。
``` c++
SP_TitleBarMenuButton,
SP_TitleBarMinButton,
SP_TitleBarMaxButton,
SP_TitleBarCloseButton,
SP_MessageBoxInformation,
SP_MessageBoxWarning,
SP_MessageBoxCritical,
SP_MessageBoxQuestion,
...
```

13. 根据操作系统位数判断加载
``` c++
win32 {
    contains(DEFINES, WIN64) { DESTDIR = $${PWD}/../../bin64
    } else { DESTDIR = $${PWD}/../../bin32 }
}
```

14. Qt5增强了很多安全性验证，如果出现setGeometry: Unable to set geometry，请将该控件的可见移到加入布局之后。

15. 可以将控件A添加到布局，然后控件B设置该布局，这种灵活性大大提高了控件的组合度，比如可以在文本框左侧右侧增加一个搜索按钮，按钮设置图标即可。
``` c++
QPushButton *btn = new QPushButton;
btn->resize(30, ui->lineEdit->height());
QHBoxLayout *layout = new QHBoxLayout(ui->lineEdit);
layout->setMargin(0);
layout->addStretch();
layout->addWidget(btn);
```

16. 对QLCDNumber控件设置样式，需要将QLCDNumber的segmentstyle设置为flat。

17. 巧妙的使用findChildren可以查找该控件下的所有子控件。findChild为查找单个。
``` c++
//查找指定类名objectName的控件
QList<QWidget *> widgets = parentWidget.findChildren<QWidget *>("widgetname");
//查找所有QPushButton
QList<QPushButton *> allPButtons = parentWidget.findChildren<QPushButton *>();
//查找一级子控件,不然会一直遍历所有子控件
QList<QPushButton *> childButtons = parentWidget.findChildren<QPushButton *>(QString(), Qt::FindDirectChildrenOnly);
```

18. 巧妙的使用inherits判断是否属于某种类。
``` c++
QTimer *timer = new QTimer;         // QTimer inherits QObject
timer->inherits("QTimer");          // returns true
timer->inherits("QObject");         // returns true
timer->inherits("QAbstractButton"); // returns false
```

19. 使用弱属性机制，可以存储临时的值用于传递判断。可以通过widget->dynamicPropertyNames()列出所有弱属性名称，然后通过widget->property("name")取出对应的弱属性的值。

20. 在开发时, 无论是出于维护的便捷性, 还是节省内存资源的考虑, 都应该有一个 qss 文件来存放所有的样式表, 而不应该将 setStyleSheet 写的到处都是。如果是初学阶段或者测试阶段可以直接UI上右键设置样式表，正式项目还是建议统一到一个qss样式表文件比较好，统一管理。

21. 如果出现Z-order assignment: is not a valid widget.错误提示，用记事本打开对应的ui文件，找到<zorder></zorder>为空的地方，删除即可。

22. 善于利用QComboBox的addItem的第二个参数设置用户数据，可以实现很多效果，使用itemData取出来。

23. 如果用了webengine模块，发布程序的时候带上QtWebEngineProcess.exe+translations文件夹+resources文件夹。

24. 默认Qt是一个窗体一个句柄，如果要让每个控件都拥有独立的句柄，设置下 a.setAttribute(Qt::AA_NativeWindows);

25. Qt+Android防止程序被关闭。
``` c++
#if defined(Q_OS_ANDROID)
QAndroidService a(argc, argv);
return a.exec()
#else
QApplication a(argc, argv);
return a.exec();
#endif
```

26. 可以对整体的指示器设置样式，例如 *::down-arrow,*::menu-indicator{}  *::up-arrow:disabled,*::up-arrow:off{}。

27. 可以执行位置设置背景图片。
``` c++
QMainWindow > .QWidget {
    background-color: gainsboro;
    background-image: url(:/images/pagefold.png);
    background-position: top right;
    background-repeat: no-repeat
}
```

28. 嵌入式linux运行Qt程序  Qt4写法：./HelloQt -qws &   Qt5写法：./HelloQt --platform xcb

29. Qtcreator软件的配置文件存放在：C:\Users\Administrator\AppData\Roaming\QtProject，有时候如果发现出问题了，将这个文件夹删除后打开creator自动重新生成即可。

30. QMediaPlayer是个壳，依赖本地解码器，视频这块默认基本上就播放个MP4，如果要支持其他格式需要下载k-lite或者LAV Filters安装即可（WIN上，其他系统上自行搜索）。如果需要做功能强劲的播放器，初学者建议用vlc、mpv，终极大法用ffmpeg。

31. 判断编译器类型、编译器版本、操作系统。
``` c++
//GCC编译器
#ifdef __GNUC__
#if __GNUC__ >= 3 // GCC3.0以上

//MSVC编译器
#ifdef _MSC_VER
#if _MSC_VER >=1000 // VC++4.0以上
#if _MSC_VER >=1100 // VC++5.0以上
#if _MSC_VER >=1200 // VC++6.0以上
#if _MSC_VER >=1300 // VC2003以上
#if _MSC_VER >=1400 // VC2005以上
#if _MSC_VER >=1500 // VC2008以上
#if _MSC_VER >=1600 // VC2010以上
#if _MSC_VER >=1700 // VC2012以上
#if _MSC_VER >=1800 // VC2013以上
#if _MSC_VER >=1900 // VC2015以上

//Borland C++
#ifdef __BORLANDC__

//Cygwin
#ifdef __CYGWIN__
#ifdef __CYGWIN32__

//mingw
#ifdef __MINGW32__

//windows
#ifdef _WIN32    //32bit
#ifdef _WIN64    //64bit
#ifdef _WINDOWS     //图形界面程序
#ifdef _CONSOLE     //控制台程序
//Windows（95/98/Me/NT/2000/XP/Vista）和Windows CE都定义了
#if (WINVER >= 0x030a)     // Windows 3.1以上
#if (WINVER >= 0x0400)     // Windows 95/NT4.0以上
#if (WINVER >= 0x0410)     // Windows 98以上
#if (WINVER >= 0x0500)     // Windows Me/2000以上
#if (WINVER >= 0x0501)     // Windows XP以上
#if (WINVER >= 0x0600)     // Windows Vista以上
//_WIN32_WINNT 内核版本
#if (_WIN32_WINNT >= 0x0500) // Windows 2000以上
#if (_WIN32_WINNT >= 0x0501) // Windows XP以上
#if (_WIN32_WINNT >= 0x0600) // Windows Vista以上

```

32. 在pro中判断Qt版本及构建套件位数
``` c++
#打印版本信息
message(qt version: $$QT_VERSION)
#判断当前qt版本号
QT_VERSION = $$[QT_VERSION]
QT_VERSION = $$split(QT_VERSION, ".")
QT_VER_MAJ = $$member(QT_VERSION, 0)
QT_VER_MIN = $$member(QT_VERSION, 1)
#下面是表示 Qt5.5
greaterThan(QT_VER_MAJ, 4) {
greaterThan(QT_VER_MIN, 4) {
#自己根据需要做一些处理
}
}

#QT_ARCH是Qt5新增的,在Qt4上没效果
#打印当前Qt构建套件的信息
message($$QT_ARCH)
#表示arm平台构建套件
contains(QT_ARCH, arm) {}
#表示32位的构建套件
contains(QT_ARCH, i386) {}
#表示64位的构建套件
contains(QT_ARCH, x86_64) {}
```

33. Qt最小化后恢复界面假死冻结，加上代码
``` c++
void showEvent(QShowEvent *e)
{
    setAttribute(Qt::WA_Mapped);
    QWidget::showEvent(e);
}
```

34. 获取标题栏高度：style()->pixelMetric(QStyle::PM_TitleBarHeight); PM_TitleBarHeight点进去你会发现新大陆。

35. 设置高分屏属性以便支持2K4K等高分辨率，尤其是手机app。必须写在main函数的QApplication a(argc, argv);的前面。
``` c++
#if (QT_VERSION > QT_VERSION_CHECK(5,6,0))
    QGuiApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
#endif
```

36. 如果运行程序出现 Fault tolerant heap shim applied to current process. This is usually due to previous crashes. 错误。
办法：打开注册表，找到HKEY_LOCAL_MACHINE\Software\Microsoft\Windows  NT\CurrentVersion\AppCompatFlags\Layers\，选中Layers键值，从右侧列表中删除自己的那个程序路径即可。

37. Qt内置了QFormLayout表单布局用于自动生成标签+输入框的组合的表单界面。

38. qml播放视频在linux需要安装 sudo apt-get install libpulse-dev。

39. 可以直接继承QSqlQueryModel实现自定义的QueryModel，比如某一列字体颜色，占位符，其他样式等，重写QVariant CustomSqlModel::data(const QModelIndex &index, int role) const。

40. Qt5以后提供了类QScroller直接将控件滚动。
``` c++
//禁用横向滚动条
ui->listWidget->setHorizontalScrollBarPolicy(Qt::ScrollBarAlwaysOff);
//禁用纵向滚动条
ui->listWidget->setVerticalScrollBarPolicy(Qt::ScrollBarAlwaysOff);
//设置横向按照像素值为单位滚动
ui->listWidget->setHorizontalScrollMode(QListWidget::ScrollPerPixel);
//设置纵向按照像素值为单位滚动
ui->listWidget->setVerticalScrollMode(QListWidget::ScrollPerPixel);
//设置滚动对象以及滚动方式为鼠标左键拉动滚动
QScroller::grabGesture(ui->listWidget, QScroller::LeftMouseButtonGesture);
//还有个QScrollerProperties可以设置滚动的一些参数
```

41. 如果使用sqlite数据库不想产生数据库文件，可以创建内存数据库。
``` c++
QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
db.setDatabaseName(":memory:");
```

42. 清空数据表并重置自增ID，sql = truncate table table_name。

43. Qtchart模块从Qt5.7开始自带，最低编译要求Qt5.4。在安装的时候记得勾选，默认不勾选。使用该模块需要引入命名空间。
``` c++
#include <QChartView>
QT_CHARTS_USE_NAMESPACE
class CustomChart : public QChartView
```

44. QPushButton左对齐文字，需要设置样式表QPushButton{text-align:left;}

45. QLabel有三种设置文本的方法，掌握好Qt的属性系统，举一反三，可以做出很多效果。
``` c++
ui->label->setStyleSheet("qproperty-text:hello;");
ui->label->setProperty("text", "hello");
ui->label->setText("hello");
```

46. 巧妙的用QEventLoop开启事件循环，可以使得很多同步获取返回结果而不阻塞界面。QEventLoop内部新建了线程执行。
``` c++
QEventLoop loop;
connect(reply, SIGNAL(finished()), &loop, SLOT(quit()));
loop.exec();
```

47. 多种预定义变量 #if (defined webkit) || (defined webengine)，去掉生成空的debug和release目录 CONFIG -= debug_and_release。

48. 新版的Qtcreator增强了语法检查，会弹出很多警告提示等，可以在插件列表中关闭clang打头的几个即可，Help》About Plugins。也可以设置代码检查级别，Tools》Options 》C++ 》Code Model。

49. QSqlTableModel的rowCount方法，默认最大返回256，如果超过256，可以将表格拉到底部，会自动加载剩余的，每次最大加载256条数据，如果需要打印或者导出数据，记得最好采用sql语句去查询，而不是使用QSqlTableModel的rowCount方法。不然永远最大只会导出256条数据。
如果数据量很小，也可以采用如下方法：
``` c++
//主动加载所有数据,不然获取到的行数<=256
while(model->canFetchMore()) {
    model->fetchMore();
}
```

50. 如果需要指定无边框窗体，但是又需要保留操作系统的边框特性，可以自由拉伸边框，可以使用 setWindowFlags(Qt::CustomizeWindowHint);

51. 在某些http post数据的时候，如果采用的是&字符串连接的数据发送，中文解析乱码的话，需要将中文进行URL转码。
``` c++
QString content = "测试中文";
QString note = content.toUtf8().toPercentEncoding();
```

52. Qt默认不支持大资源文件，比如添加了字体文件，需要pro文件开启。
CONFIG += resources_big

53. Qt中继承QWidget之后，样式表不起作用，解决办法有三个。强烈推荐方法一。
- 方法一：设置属性 this->setAttribute(Qt::WA_StyledBackground, true);
- 方法二：改成继承QFrame，因为QFrame自带paintEvent函数已做了实现，在使用样式表时会进行解析和绘制。
- 方法三：重新实现QWidget的paintEvent函数时，使用QStylePainter绘制。
``` c++
void Widget::paintEvent(QPaintEvent *)
{
    QStyleOption option;
    option.initFrom(this);
    QPainter painter(this);
    style()->drawPrimitive(QStyle::PE_Widget, &option, &painter, this);
}
```

54. 有时候在界面上加了弹簧，需要动态改变弹簧对应的拉伸策略，对应方法为changeSize，很多人会选择使用set开头去找，找不到的。

55. 在使用QFile的过程中，不建议频繁的打开文件写入然后再关闭文件，比如间隔5ms输出日志，IO性能瓶颈很大，这种情况建议先打开文件不要关闭，等待合适的时机比如析构函数中或者日期变了需要重新变换日志文件的时候关闭文件。不然短时间内大量的打开关闭文件会很卡，文件越大越卡。

56. 在很多网络应用程序，需要自定义心跳包来保持连接，不然断电或者非法关闭程序，对方识别不到，需要进行超时检测，但是有些程序没有提供心跳协议，此时需要启用系统层的保活程序，此方法适用于TCP连接。
``` c++
int fd = tcpSocket->socketDescriptor();
int keepAlive = 1;      //开启keepalive属性,缺省值:0(关闭)
int keepIdle = 5;       //如果在5秒内没有任何数据交互,则进行探测,缺省值:7200(s)
int keepInterval = 2;   //探测时发探测包的时间间隔为2秒,缺省值:75(s)
int keepCount = 2;      //探测重试的次数,全部超时则认定连接失效,缺省值:9(次)
setsockopt(fd, SOL_SOCKET, SO_KEEPALIVE, (void *)&keepAlive, sizeof(keepAlive));
setsockopt(fd, SOL_TCP, TCP_KEEPIDLE, (void *)&keepIdle, sizeof(keepIdle));
setsockopt(fd, SOL_TCP, TCP_KEEPINTVL, (void *)&keepInterval, sizeof(keepInterval));
setsockopt(fd, SOL_TCP, TCP_KEEPCNT, (void *)&keepCount, sizeof(keepCount));
```

57. 如果程序打包好以后弹出提示 This application failed to start because it could not find or load the Qt platform plugin 一般都是因为platforms插件目录未打包或者打包错了的原因导致的。

58. 非常不建议tr中包含中文，尽管现在的新版Qt支持中文到其他语言的翻译，但是很不规范，也不知道TMD是谁教的，tr的本意是包含英文，然后翻译到其他语言比如中文，现在大量的初学者滥用tr，如果没有翻译的需求，禁用tr，tr需要开销的，Qt默认会认为他需要翻译，会额外进行特殊处理。

59. 很多人Qt和Qt Creator傻傻分不清楚，经常问Qt什么版本结果发一个Qt Creator的版本过来，Qt Creator是使用Qt编写的集成开发环境IDE，和宇宙第一的Visual Studio一样，他可以是msvc编译器的（WIN对应的Qt集成安装环境中自带的Qt Cerator是msvc的），也可以是mingw编译的，还可以是gcc的。如果是自定义控件插件，需要集成到Qt Creator中，必须保证该插件的动态库文件（dll或者so等文件）对应的编译器和Qt版本以及位数和Qt Creator的版本完全一致才行，否则基本不大可能集成进去。特别注意的是Qt集成环境安装包中的Qt版本和Qt Creator版本未必完全一致，必须擦亮眼睛看清楚，有些是完全一致的。

60. 超过两处相同处理的代码，建议单独写成函数。代码尽量规范精简，比如 if(a == 123) 要写成 if (123 == a)，值在前面，再比如 if (ok == true) 要写成 if (ok)，if (ok == false) 要写成 if (!ok)等。

61. 很多人问Qt嵌入式平台用哪个好，这里统一回答（当前时间节点2018年）：imx6+335x比较稳定，性能高就用RK3288 RK3399，便宜的话就用全志H3，玩一玩可以用树莓派香橙派。

62. 对于大段的注释代码，建议用 #if 0 #endif 将代码块包含起来，而不是将该段代码选中然后全部 // ，下次要打开这段代码的话，又需要重新选中一次取消，如果采用的是 #if 0则只要把0改成1即可，效率大大提升。

63. Qt打包发布，有很多办法，Qt5以后提供了打包工具windeployqt（linux上为linuxdeployqt，mac上为macdeployqt）可以很方便的将应用程序打包，使用下来发现也不是万能的，有时候会多打包一些没有依赖的文件，有时候又会忘记打包一些插件尤其是用了qml的情况下，而且不能识别第三方库，比如程序依赖ffmpeg，则对应的库需要自行拷贝，终极大法就是将你的可执行文件复制到Qt安装目录下的bin目录，然后整个一起打包，挨个删除不大可能依赖的组件，直到删到正常运行为止。

64. Qt中的动画，底层用的是QElapsedTimer定时器来完成处理，比如产生一些指定规则算法的数据，然后对属性进行处理。

65. 在绘制无背景颜色只有边框颜色的圆形时候，可以用绘制360度的圆弧替代，效果完全一致。
``` c++
QRect rect(-radius, -radius, radius * 2, radius * 2);
//以下两种方法二选一,其实绘制360度的圆弧=绘制无背景的圆形
painter->drawArc(rect, 0, 360 * 16);
painter->drawEllipse(rect);
```

66. 不要把d指针看的很玄乎，其实就是在类的实现文件定义了一个私有类，用来存放局部变量，个人建议在做一些小项目时，没有太大必要引入这种机制，会降低代码可读性，增加复杂性，新手接受项目后会看的很懵逼。

67. 很多人在绘制的时候，设置画笔以为就只可以设置个单调的颜色，其实QPen还可以设置brush，这样灵活性就提高不知道多少倍，比如设置QPen的brush以后，可以使用各种渐变，比如绘制渐变颜色的进度条和文字等，而不再是单调的一种颜色。

68. 很多控件都带有viewport，比如QTextEdit/QTableWidget/QScrollArea，有时候对这些控件直接处理的时候发现不起作用，需要对其viewport()设置才行，比如设置滚动条区域背景透明，需要使用scrollArea->viewport()->setStyleSheet("background-color:transparent;");而不是scrollArea->setStyleSheet("QScrollArea{background-color:transparent;}");

69. 有时候设置了鼠标跟踪setMouseTracking为真，如果该窗体上面还有其他控件，当鼠标移到其他控件上面的时候，父类的鼠标移动事件MouseMove识别不到了，此时需要用到HoverMove事件，需要先设置 setAttribute(Qt::WA_Hover, true);

70. Qt封装的QDateTime日期时间类非常强大，可以字符串和日期时间相互转换，也可以毫秒数和日期时间相互转换，还可以1970经过的秒数和日期时间相互转换等。
``` c++
QDateTime dateTime;
QString dateTime_str = dateTime.currentDateTime().toString("yyyy-MM-dd hh:mm:ss");
//从字符串转换为毫秒（需完整的年月日时分秒）
datetime.fromString("2011-09-10 12:07:50:541", "yyyy-MM-dd hh:mm:ss:zzz").toMSecsSinceEpoch();
//从字符串转换为秒（需完整的年月日时分秒）
datetime.fromString("2011-09-10 12:07:50:541", "yyyy-MM-dd hh:mm:ss:zzz").toTime_t();
//从毫秒转换到年月日时分秒
datetime.fromMSecsSinceEpoch(1315193829218).toString("yyyy-MM-dd hh:mm:ss:zzz");
//从秒转换到年月日时分秒（若有zzz，则为000）
datetime.fromTime_t(1315193829).toString("yyyy-MM-dd hh:mm:ss[:zzz]");
```

71. 在我们使用QList、QStringList、QByteArray等链表或者数组的过程中，如果只需要取值，而不是赋值，强烈建议使用 at() 取值而不是 [] 操作符，在官方书籍《C++ GUI Qt 4编程（第二版）》的书中有特别的强调说明，此教材的原作者据说是Qt开发的核心人员编写的，所以还是比较权威，至于使用 at() 与使用 [] 操作符速度效率的比较，网上也有网友做过此类对比。原文在书的212页，这样描述的：Qt对所有的容器和许多其他类都使用隐含共享，隐含共享是Qt对不希望修改的数据决不进行复制的保证，为了使隐含共享的作用发挥得最好，可以采用两个新的编程习惯。第一种习惯是对于一个（非常量的）向量或者列表进行只读存取时，使用 at() 函数而不用 [] 操作符，因为Qt的容器类不能辨别 [] 操作符是否将出现在一个赋值的左边还是右边，他假设最坏的情况出现并且强制执行深层赋值，而 at() 函数则不被允许出现在一个赋值的左边。

72. 如果是dialog窗体，需要在exec以后还能让其他代码继续执行，请在dialog窗体exec前增加一行代码，否则会阻塞窗体消息。
``` c++
QDialog dialog;
dialog.setWindowModality(Qt::WindowModal);
dialog.exec();
```

73. 安全的删除Qt的对象类，强烈建议使用deleteLater而不是delete，因为deleteLater会选择在合适的时机进行释放，而delete会立即释放，很可能会出错崩溃。如果要批量删除对象集合，可以用qDeleteAll，比如 qDeleteAll(btns);

74. 在QTableView控件中，如果需要自定义的列按钮、复选框、下拉框等其他模式显示，可以采用自定义委托QItemDelegate来实现，如果需要禁用某列，则在自定义委托的重载createEditor函数返回0即可。自定义委托对应的控件在进入编辑状态的时候出现，如果想一直出现，则需要重载paint函数用drawPrimitive或者drawControl来绘制。

75. 将 QApplication::style() 对应的drawPrimitive、drawControl、drawItemText、drawItemPixmap等几个方法用熟悉了，再结合QStyleOption属性，可以玩转各种自定义委托，还可以直接使用paint函数中的painter进行各种绘制，各种牛逼的表格、树状列表、下拉框等，绝对屌炸天。QApplication::style()->drawControl 的第4个参数如果不设置，则绘制出来的控件不会应用样式表。

76. 心中有坐标，万物皆painter，强烈建议在学习自定义控件绘制的时候，将qpainter.h头文件中的函数全部看一遍、试一遍、理解一遍，这里边包含了所有Qt内置的绘制的接口，对应的参数都试一遍，你会发现很多新大陆，会大大激发你的绘制的兴趣，犹如神笔马良一般，策马崩腾遨游代码绘制的世界。

77. 在使用setItemWidget或者setCellWidget的过程中，有时候会发现设置的控件没有居中显示而是默认的左对齐，而且不会自动拉伸填充，对于追求完美的程序员来说，这个可不大好看，有个终极通用办法就是，将这个控件放到一个widget的布局中，然后将widget添加到item中，这样就完美解决了，而且这样可以组合多个控件产生复杂的控件。
``` c++
//实例化进度条控件
QProgressBar *progress = new QProgressBar;
//增加widget+布局巧妙实现居中
QWidget *widget = new QWidget;
QHBoxLayout *layout = new QHBoxLayout;
layout->setSpacing(0);
layout->setMargin(0);
layout->addWidget(progress);
widget->setLayout(layout);
ui->tableWidget->setCellWidget(0, 0, widget);
```

78. 很多时候需要在已知背景色的情况下，能够清晰的绘制文字，这个时候需要计算对应的文字颜色。
``` c++
//根据背景色自动计算合适的前景色
double gray = (0.299 * color.red() + 0.587 * color.green() + 0.114 * color.blue()) / 255;
QColor textColor = gray > 0.5 ? Qt::black : Qt::white;
```

79. 对QTableView或者QTableWidget禁用列拖动。
``` c++
#if (QT_VERSION <= QT_VERSION_CHECK(5,0,0))
    ui->tableView->horizontalHeader()->setResizeMode(0, QHeaderView::Fixed);
#else
    ui->tableView->horizontalHeader()->setSectionResizeMode(0, QHeaderView::Fixed);
#endif
```

80. 从Qt4转到Qt5，有些类的方法已经废弃或者过时了，如果想要在Qt5中启用Qt4的方法，比如QHeadVew的setMovable，可以在你的pro或者pri文件中加上一行即可：DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0

81. Qt中的QColor对颜色封装的很完美，支持各种转换，比如rgb、hsb、cmy、hsl，对应的是toRgb、toHsv、toCmyk、toHsl，还支持透明度设置，颜色值还能转成16进制格式显示。
``` c++
QColor color(255, 0, 0, 100);
qDebug() << color.name() << color.name(QColor::HexArgb);
//输出 #ff0000 #64ff0000
```

82. QVariant类型异常的强大，可以说是万能的类型，在进行配置文件的存储的时候，经常会用到QVariant的转换，QVariant默认自带了toString、toFloat等各种转换，但是还是不够，比如有时候需要从QVariant转到QColor，而却没有提供toColor的函数，这个时候就要用到万能办法。
``` c++
if (variant.typeName() == "QColor") {
    QColor color = variant.value<QColor>();
    QFont font = variant.value<QFont>();
    QString nodeValue = color.name(QColor::HexArgb);
}
```

83. Qt中的QString和const char *之间转换，最好用toStdString().c_str()而不是toLocal8Bit().constData()，比如在setProperty中如果用后者，字符串中文就会不正确，英文正常。

84. Qt的信号槽机制非常牛逼，也是Qt的独特的核心功能之一，有时候我们在很多窗体中传递信号来实现更新或者处理，如果窗体层级比较多，比如窗体A的父类是窗体B，窗体B的父类是窗体C，窗体C有个子窗体D，如果窗体A一个信号要传递给窗体D，问题来了，必须先经过窗体B中转到窗体C再到窗体D才行，这样的话各种信号关联信号的connect会非常多而且管理起来比较乱，可以考虑增加一个全局的单例类AppEvent，公共的信号放这里，然后窗体A对应信号绑定到AppEvent，窗体D绑定AppEvent的信号到对应的槽函数即可，干净清爽整洁。

85. QTextEdit右键菜单默认英文的，如果想要中文显示，加载widgets.qm文件即可，一个Qt程序中可以安装多个翻译文件，不冲突。

86. Qt中有个全局的焦点切换信号focusChanged，可以用它做自定义的输入法。Qt4中默认会安装输入法上下文，比如在main函数打印a.inputContext会显示值，这个默认安装的输入法上下文，会拦截两个牛逼的信号QEvent::RequestSoftwareInputPanel和QEvent::CloseSoftwareInputPanel，以至于就算你安装了全局的事件过滤器依然识别不到这两个信号，你只需要在main函数执行a.setInputContext(0)即可，意思是安装输入法上下文为空。

87. 在Qt5.10以后，表格控件QTableWidget或者QTableView的默认最小列宽改成了15，以前的版本是0，所以在新版的qt中，如果设置表格的列宽过小，不会应用，取的是最小的列宽。所以如果要设置更小的列宽需要重新设置ui->tableView->horizontalHeader()->setMinimumSectionSize(0);

88. Qt源码中内置了一些未公开的不能直接使用的黑科技，都藏在对应模块的private中，比如gui-private widgets-private等，比如zip文件解压类QZipReader、压缩类QZipWriter就在gui-private模块中，需要在pro中引入QT += gui-private才能使用。
``` c++
#include "QtGui/private/qzipreader_p.h"
#include "QtGui/private/qzipwriter_p.h"

QZipReader reader(dirPath);
QString path("");
//解压文件夹到当前目录
reader.extractAll(path);
//文件夹名称
QZipReader::FileInfo fileInfo = reader.entryInfoAt(0);
//解压文件
QFile file(filePath);
file.open(QIODevice::WriteOnly);
file.write(reader.fileData(QString::fromLocal8Bit("%1").arg(filePath)));
file.close();
reader.close();

QZipWriter *writer = new QZipWriter(dirPath);
//添加文件夹
writer->addDirectory(unCompress);
//添加文件
QFile file(filePath);
file.open(QIODevice::ReadOnly);
writer->addFile(data, file.readAll());
file.close();
writer->close();
```

89. 理论上串口和网络收发数据都是默认异步的，操作系统自动调度，完全不会卡住界面，网上那些说收发数据卡住界面主线程的都是扯几把蛋，真正的耗时是在运算以及运算后的处理，而不是收发数据，在一些小数据量运算处理的项目中，一般不建议动用线程去处理，线程需要调度开销的，不要什么东西都往线程里边扔，线程不是万能的。只有当真正需要将一些很耗时的操作比如编码解码等，才需要移到线程处理。

90. 在构造函数中获取控件的宽高很可能是不正确的，需要在控件首次显示以后再获取才是正确的，控件是在首次显示以后才会设置好正确的宽高值，记住是在首次显示以后，而不是构造函数或者程序启动好以后，如果程序启动好以后有些容器控件比如QTabWidget中的没有显示的页面的控件，你去获取宽高很可能也是不正确的，万无一失的办法就是首次显示以后去获取。

91. 数据库处理一般建议在主线程，如果非要在其他线程，务必记得打开数据库也要在那个线程，即在那个线程使用数据库就在那个线程打开，不能打开数据库在主线程，执行sql在子线程，很可能出问题。

92. 新版的QTcpServer类在64位版本的Qt下很可能不会进入incomingConnection函数，那是因为Qt5对应的incomingConnection函数参数变了，由之前的int改成了qintptr，改成qintptr有个好处，在32位上自动是quint32而在64位上自动是quint64，如果在Qt5中继续写的参数是int则在32位上没有问题在64位上才有问题，所以为了兼容Qt4和Qt5，必须按照不一样的参数写。
```cpp
#if (QT_VERSION > QT_VERSION_CHECK(5,0,0))
    void incomingConnection(qintptr handle);
#else
    void incomingConnection(int handle);
#endif
```

93. Qt支持所有的界面控件比如QPushButton、QLineEdit自动关联 on_控件名_信号(参数) 信号槽，比如按钮的单击信号 on_pushButton_clicked()，然后直接实现槽函数即可。

94. QWebEngineView控件由于使用了opengl，在某些电脑上可能由于opengl的驱动过低会导致花屏或者各种奇奇怪怪的问题，比如showfullscreen的情况下鼠标右键失效，需要在main函数启用软件opengl渲染。
```cpp
#if (QT_VERSION > QT_VERSION_CHECK(5,4,0))
    //下面两种方法都可以,Qt默认采用的是AA_UseDesktopOpenGL
    QCoreApplication::setAttribute(Qt::AA_UseOpenGLES);
    //QCoreApplication::setAttribute(Qt::AA_UseSoftwareOpenGL);
#endif
    QApplication a(argc, argv);
```
另外一个方法解决 全屏+QWebEngineView控件一起会产生右键菜单无法弹出的bug,需要上移一个像素
```cpp
QRect rect = qApp->desktop()->geometry();
rect.setY(-1);
rect.setHeight(rect.height());
this->setGeometry(rect);
```

95. QStyle内置了很多方法用处很大，比如精确获取滑动条鼠标按下处的值。
```cpp
QStyle::sliderValueFromPosition(minimum(), maximum(), event->x(), width());
```

96. 用QFile读写文件的时候，推荐用QTextStream文件流的方式来读写文件，速度快很多，基本上会有30%的提升，文件越大性能区别越大。
```cpp
//从文件加载英文属性与中文属性对照表
QFile file(":/propertyname.txt");
if (file.open(QFile::ReadOnly)) {
    //QTextStream方法读取速度至少快30%
#if 0
    while(!file.atEnd()) {
        QString line = file.readLine();
        appendName(line);
    }
#else
    QTextStream in(&file);
    while (!in.atEnd()) {
        QString line = in.readLine();
        appendName(line);
    }
#endif
    file.close();
}
```

97. 用QFile.readAll()读取QSS文件默认是ANSI格式，不支持UTF8，如果在QtCreator中打开qss文件来编辑保存，这样很可能导致qss加载以后没有效果。
```cpp
void frmMain::initStyle()
{
    //加载样式表
    QString qss;
    //QFile file(":/qss/psblack.css");
    //QFile file(":/qss/flatwhite.css");
    QFile file(":/qss/lightblue.css");
    if (file.open(QFile::ReadOnly)) {
#if 1
        //用QTextStream读取样式文件不用区分文件编码 带bom也行
        QStringList list;
        QTextStream in(&file);
        //in.setCodec("utf-8");
        while (!in.atEnd()) {
            QString line;
            in >> line;
            list << line;
        }

        qss = list.join("\n");
#else
        //用readAll读取默认支持的是ANSI格式,如果不小心用creator打开编辑过了很可能打不开
        qss = QLatin1String(file.readAll());
#endif
        QString paletteColor = qss.mid(20, 7);
        qApp->setPalette(QPalette(QColor(paletteColor)));
        qApp->setStyleSheet(qss);
        file.close();
    }
}
```

98. QString内置了很多转换函数，比如可以调用toDouble转为double数据，但是当你转完并打印的时候你会发现精确少了，只剩下三位了，其实原始数据还是完整的精确度的，只是打印的时候优化成了三位，如果要保证完整的精确度，可以调用 qSetRealNumberPrecision 函数设置精确度位数即可。
```cpp
QString s1, s2;
s1 = "666.5567124";
s2.setNum(888.5632123, 'f', 7);
qDebug() << qSetRealNumberPrecision(10) << s1.toDouble() << s2.toDouble();
```

99. 用QScriptValueIterator解析数据的时候，会发现总是会多一个节点内容，并且内容为空，如果需要跳过则增加一行代码。
```cpp
while (it.hasNext()) {
    it.next();    
    if (it.flags() & QScriptValue::SkipInEnumeration)      
       continue;     
    qDebug() << it.name();
}
```

100. setPixmap是最糟糕的贴图方式，一般只用来简单的不是很频繁的贴图，频繁的建议painter绘制，默认双缓冲，在高级点用opengl绘制，利用GPU。

101. 如果需要在尺寸改变的时候不重绘窗体，则设置属性即可 this->setAttribute(Qt::WA_StaticContents, true); 这样可以避免可以避免对已经显示区域的重新绘制。

102. 默认程序中获取焦点以后会有虚边框，如果看着觉得碍眼不舒服可以去掉，设置样式即可：setStyleSheet("*{outline:0px;}");

103. Qt表格控件一些常用的设置封装，QTableWidget继承自QTableView，所以下面这个函数支持传入QTableWidget。
```cpp
void QUIHelper::initTableView(QTableView *tableView, int rowHeight, bool headVisible, bool edit)
{
    //奇数偶数行颜色交替
    tableView->setAlternatingRowColors(false);
    //垂直表头是否可见
    tableView->verticalHeader()->setVisible(headVisible);
    //选中一行表头是否加粗
    tableView->horizontalHeader()->setHighlightSections(false);
    //最后一行拉伸填充
    tableView->horizontalHeader()->setStretchLastSection(true);
    //行标题最小宽度尺寸
    tableView->horizontalHeader()->setMinimumSectionSize(0);
    //行标题最大高度
    tableView->horizontalHeader()->setMaximumHeight(rowHeight);
    //默认行高
    tableView->verticalHeader()->setDefaultSectionSize(rowHeight);
    //选中时一行整体选中
    tableView->setSelectionBehavior(QAbstractItemView::SelectRows);
    //只允许选择单个
    tableView->setSelectionMode(QAbstractItemView::SingleSelection);

    //表头不可单击
#if (QT_VERSION > QT_VERSION_CHECK(5,0,0))
    tableView->horizontalHeader()->setSectionsClickable(false);
#else
    tableView->horizontalHeader()->setClickable(false);
#endif

    //鼠标按下即进入编辑模式
    if (edit) {
        tableView->setEditTriggers(QAbstractItemView::CurrentChanged | QAbstractItemView::DoubleClicked);
    } else {
        tableView->setEditTriggers(QAbstractItemView::NoEditTriggers);
    }
}
```

104. 在一些大的项目中，可能嵌套了很多子项目，有时候会遇到子项目依赖其他子项目的时候，比如一部分子项目用来生成动态库，一部分子项目依赖这个动态库进行编译，此时就需要子项目按照顺序编译。
```cpp
TEMPLATE = subdirs
#设置ordered参数以后会依次编译 demo designer examples
CONFIG  += ordered
SUBDIRS += demo
SUBDIRS += designer
SUBDIRS += examples
```

105. MSVC编译器的选择说明
- 如果是32位的Qt则编译器选择x86开头的
- 如果是64位的Qt则编译器选择amd64开头的
- 具体是看安装的Qt构建套件版本以及目标运行平台的系统位数和架构
- 一般现在的电脑默认以64位的居多，选择amd64即可
- 如果用户需要兼容32位的系统则建议选择32位的Qt，这样即可在32位也可以在64位系统运行
- 诸葛大佬补充：x86/x64都是编译环境和运行环境相同，没有或。带下划线的就是交叉编译，前面是编译环境，后面是运行环境。

| 名称 | 说明 |
| ------ | ------ |
|x86|32/64位系统上编译在32/64位系统上运行|
|x86_amd64|32/64位系统上编译在64位系统上运行|
|x86_arm|32/64位系统上编译在arm系统上运行|
|amd64|64位系统上编译在64位系统上运行|
|amd64_x86|64位系统上编译在32/64位系统上运行|
|amd64_arm|64位系统上编译在arm系统上运行|

106. 很多时候用QDialog的时候会发现阻塞了消息，而有的时候我们希望是后台的一些消息继续运行不要终止，此时需要做个设置。
```cpp
QDialog dialog;
dialog.setWindowModality(Qt::WindowModal);
```

107. 很多初学者甚至几年工作经验的人，对多线程有很深的误解和滥用，尤其是在串口和网络通信这块，什么都往多线程里面丢，一旦遇到界面卡，就把数据收发啥的都搞到多线程里面去，殊不知绝大部分时候那根本没啥用，因为没找到出问题的根源。
- 如果你没有使用wait***函数的话，大部分的界面卡都出在数据处理和展示中，比如传过来的是一张图片的数据，你需要将这些数据转成图片，这个肯定是耗时的；
- 还有就是就收到的数据曲线绘制出来，如果过于频繁或者间隔过短，肯定会给UI造成很大的压力的，最好的办法是解决如何不要频繁绘制UI比如合并数据一起绘制等；
- 如果是因为绘制UI造成的卡，那多线程也是没啥用的，因为UI只能在主线程；
- 串口和网络的数据收发默认都是异步的，由操作系统调度的，如果数据处理复杂而且数据量大，你要做的是将数据处理放到多线程中；
- 如果没有严格的数据同步需求，根本不需要调用wait***之类的函数来立即发送和接收数据，实际需求中大部分的应用场景其实异步收发数据就足够了；
- 有严格数据同步需求的场景还是放到多线程会好一些，不然你wait***就卡在那边了；
- 多线程是需要占用系统资源的，理论上来说，如果线程数量超过了CPU的核心数量，其实多线程调度可能花费的时间更多，各位在使用过程中要权衡利弊；

108. 在嵌入式linux上，如果设置了无边框窗体，而该窗体中又有文本框之类的，发现没法产生焦点进行输入，此时需要主动激活窗体才行。
```cpp
//这种方式设置的无边框窗体在嵌入式设备上无法产生焦点
setWindowFlags(Qt::WindowStaysOnTopHint | Qt::FramelessWindowHint | Qt::X11BypassWindowManagerHint);

//需要在show以后主动激活窗体
w->show();
w->activateWindow();
```

109. QString的replace函数会改变原字符串，切记，他在返回替换后的新字符串的同时也会改变原字符串，我的乖乖！

110. QGraphicsEffect类的相关效果很炫，可以实现很多效果比如透明、渐变、阴影等，但是该类很耗CPU，如果不是特别需要一般不建议用，就算用也是要用在该部件后期不会发生频繁绘制的场景，不然会让你哭晕在厕所。

111. 在不同的平台上文件路径的斜杠也是不一样的，比如linux系统一般都是 / 斜杠，而在windows上都是 \\ 两个反斜杠，Qt本身程序内部无论在win还是linux都支持 / 斜杠的路径，但是一些第三方库的话可能需要转换成对应系统的路径，这就需要用到斜杠转换，Qt当然内置类方法。
```cpp
QString path = "C:/temp/test.txt";
path = QDir::toNativeSeparators(path);
//输出 C:\\temp\\test.txt

QString path = "C:\\temp\\test.txt";
path = QDir::toNativeSeparators(path);
//输出 C:/temp/test.txt
```

112. 巧用QMetaObject::invokeMethod方法可以实现很多效果，包括同步和异步执行，很大程度上解决了跨线程处理信号槽的问题。比如有个应用场景是在回调中，需要异步调用一个public函数，如果直接调用的话会发现不成功，此时需要使用 QMetaObject::invokeMethod(obj, "fun", Qt::QueuedConnection); 这种方式来就可以。invokeMethod函数有很多重载参数，可以传入返回值和执行方法的参数等。invokeMethod函数不仅支持槽函数还支持信号，而且这逼居然是线程安全的，可以在线程中放心使用，牛逼！
```cpp
//头文件声明信号和槽函数
signals:
    void sig_test(int type,double value);
private slots:
    void slot_test(int type, double value);

//构造函数关联信号槽
connect(this, SIGNAL(sig_test(int, double)), this, SLOT(slot_test(int, double)));
//单击按钮触发信号和槽,这里是同时举例信号槽都可以
void MainWindow::on_pushButton_clicked()
{
    QMetaObject::invokeMethod(this, "sig_test", Q_ARG(int, 66), Q_ARG(double, 66.66));
    QMetaObject::invokeMethod(this, "slot_test", Q_ARG(int, 88), Q_ARG(double, 88.88));
}
//会打印 66 66.66 和 88 88.88
void MainWindow::slot_test(int type, double value)
{
    qDebug() << type << value;
}
```

113. Qt5中的信号是public的，可以在需要的地方直接emit即可，而在Qt4中信号是protected的，不能直接使用，需要定义一个public函数来emit。

114. Qt5.15版本开始官方不再提供安装包，只提供源码，可以自行编译或者在线安装，估计每次编译各种版本太麻烦，更多的是为了统计收集用户使用信息比如通过在线安装，后期可能会逐步加大商业化力度。

115. 有时候我们需要判断当前Qt版本有没有某个模块可以使用qtHaveModule（Qt5新引入的判断）来判断，如果要判断自己的项目中有没有 QT += 的方式添加的模块，可以用 contains来判断。
```cpp
qtHaveModule(webenginewidgets) {
message("当前Qt库有找到 webenginewidgets 模块")
}

!qtHaveModule(webkit) {
message("当前Qt库没有找到 webkit 模块")
}

contains(QT, network) {
message("当前项目已经引入 network 模块")
}

!contains(QT, widgets) {
message("当前项目没有引入 widgets 模块")
}
```

116. c++11新引入了原始字符串格式，用户避免在字符串中加入转义字符\，可以用于表示json字符串等场景。
```cpp
QString s1 = R"(test\001.jpg)";
s1.replace("\\", "#");
qDebug()<< s1;
//结果 test#001.jpg
```

117. 安卓上打印信息建议使用 qInfo() 而不是 qDebug() ，qInfo()才有效果。

118. Qt的默认定时器精度不够高（比如应用场景是1分钟保存一条记录或者文件，当你用默认的定时器的时候你会发现有些时候是60秒而有些是59秒随机的，如果客户有要求这就需要设置精度了。当然我们所做的绝大部分项目也不需要精度非常高的定时器，毕竟精度越高，占用的系统资源可能越大），如果需要设置更高的精度可以设置 setTimerType(Qt::PreciseTimer)。Qt有两种定时器处理，一种是QTimer类，还有一种是QObject类就内置的timeevent事件，如果是QObject类的定时器要设置的话调用 startTimer(interval, Qt::PreciseTimer);
- Qt::PreciseTimer 精确的定时器，尽量保持毫秒精度。
- Qt::CoarseTimer 粗略的定时器，尽量保持精度在所需的时间间隔5%范围内。
- Qt::VeryCoarseTimer 很粗略的定时器，只保留完整的第二精度。
- 精度再高，也依赖对应的操作系统中断，假设中断需要 5ms，则定时器精度不可能高于5毫秒。

119. QGraphicsEffect相关类很耗CPU，甚至在绘制的时候和某些地方有冲突干扰，基本上不建议使用，情非得已只建议少量使用和非频繁触发绘制的地方使用。

120. 用QSettings设置注册表，如果不是管理员身份运行会打印 QSettings: failed to set subkey "xxx" (拒绝访问。)，你需要手动鼠标右键管理员身份运行就可以。

121. QLineEdit除了单纯的文本框以外，还可以做很多特殊的处理用途。
- 限制输入只能输入IP地址。
- 限制输入范围，强烈推荐使用 QRegExpValidator 正则表达式来处理。
```cpp
//正在表达式限制输入
QString str = "\\b(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\\b";
ui->lineEdit->setValidator(new QRegExpValidator(QRegExp(str)));
//用于占位
ui->lineEdit->setInputMask("000.000.000.000");

#if 0
//下面代码设置浮点数范围限制失败
ui->lineEdit->setValidator(new QDoubleValidator(20, 50, 1));
#else
//下面代码设置浮点数范围限制成功
QDoubleValidator *validator = new QDoubleValidator(20, 50, 1);
validator->setNotation(QDoubleValidator::StandardNotation);
ui->lineEdit->setValidator(validator);
#endif
//下面代码设置整数范围限制成功
ui->lineEdit->setValidator(new QIntValidator(10, 120));

//其实上面的代码缺陷很多，只能限制只输入小数，无法设定数值范围，很操蛋
//需要来个万能的牛逼的 QRegExpValidator

//限制浮点数输入范围为[-180,180]
QRegExp regexp("^-?(180|1?[0-7]?\\d(\\.\\d+)?)$");
//限制浮点数输入范围为[-90,90]并限定为小数位后4位
QRegExp regexp("^-?(90|[1-8]?\\d(\\.\\d{1,4})?)$");
QRegExpValidator *validator = new QRegExpValidator(regexp, this);
ui->lineEdit->setValidator(validator);
```

122. 在继承自QAbstractItemView的控件中，比如QTableView、QTableWidget，如果文本超过对应item的宽度，则会自动省略号显示，想要快速显示完整的文本，可以在该列和下一列分割线中间双击即可，会自动自适应显示最大宽度，如果是Qt5.14或者更高版本，你会发现显示省略号的计算规则变了，如果是rtsp、http之类的开头的英文字符串，同样的列宽下，会提前就显示省略号，比如字符串 rtmp://58.200.131.2:1935/livetv/cctv1，会显示成 rtmp://...  ，而在旧版本的Qt中会显示成 rtmp://58.200.131... ，很多时候我们并不想看到烦人的省略号，可以设置取消。
```cpp
//取消自动换行
tableView->setWordWrap(false);
//超出文本不显示省略号
tableView->setTextElideMode(Qt::ElideNone);
```

123. QVideoWidget播放视频，可能会遇到画面闪烁的情况，播放视频的窗体需要设置个属性。
```cpp
QVideoWidget *videoWidget = new QVideoWidget;
videoWidget->setAttribute(Qt::WA_OpaquePaintEvent);
```

123. Qt bug成千上万，这个不用大惊小怪，也基本上遇不到，大部分都是特殊极端情况特定应用场景出现，甚至你会遇到有些是debug可以release报错，有些release可以debug却报错的情况，最神奇的还有先是debug报错，然后release正常，再返回去用debug又正常，需要用release激活一下！学习编程的路本来就是一条坑坑洼洼的路，不断填坑，尽量规避坑！很多时候很多看起来的坑其实是自己没有注意细节导致的。

124. Qt试图中默认排序是按照字符串的ASCII排序的，如果是IP地址的话会出现192.168.1.117排在192.168.1.2前面的情况，如果要规避这种情况，一种做法是取末尾的地址转成整型再比较大小，缺点是跨网段就歇菜了，又会出现192.168.2.65出现在192.168.1.70前面，终极大法是将IP地址转成整型再比较大小。
```cpp
QString QUIHelper::ipv4IntToString(quint32 ip)
{
    QString result = QString("%1.%2.%3.%4").arg((ip >> 24) & 0xFF).arg((ip >> 16) & 0xFF).arg((ip >> 8) & 0xFF).arg(ip & 0xFF);
    return result;
}

quint32 QUIHelper::ipv4StringToInt(const QString &ip)
{
    int result = 0;
    if (isIP(ip)) {
        QStringList list = ip.split(".");
        int ip0 = list.at(0).toInt();
        int ip1 = list.at(1).toInt();
        int ip2 = list.at(2).toInt();
        int ip3 = list.at(3).toInt();
        result = ip3 | ip2 << 8 | ip1 << 16 | ip0 << 24;
    }
    return result;
}
```

125. 在主QWidget窗体如果直接qss设置背景图片的话，预览是可见的，运行并没有效果，你需要在这个主widget上再放个widget，在新的widget上设置qss图片就行，而如果是Dialog或者QMainWindow窗体是支持直接设置qss背景图的，预览和运行效果一致。

126. Qt提供了qDebug机制直接输出打印信息，这个大大弥补了QtCreator调试很鸡肋的缺点，而且无缝对接日志钩子，使得现场运行期间按照预定的打印信息输出到日志文件，有时候在开发阶段，又不想要看到一堆堆的打印信息，最笨的做法是一行行注释掉qdebug的地方，其实还可以直接pro中加上一行来禁用整个项目的qdebug输出。
```cpp
#禁用qdebug打印输出
DEFINES += QT_NO_DEBUG_OUTPUT
```

127. 在使用QT_NO_DEBUG_OUTPUT关键字禁用了所有打印信息以后，可以节约不少的开销，有时候又想在禁用打印信息后，极少地方还需要看到打印信息，怎么办呢？其实QT_NO_DEBUG_OUTPUT禁用的qdebug的输出，Qt还有其他几种打印信息比如 qInfo、qWarning、qCritical，这些是不受影响的，也就是说在极少部分需要打印的地方用qInfo来输出信息就好。特别注意：qFatal打印完信息程序会自动结束。
```cpp
qDebug() << "qDebug";
qInfo() << "qInfo";
qWarning() << "qWarning";
qCritical() << "qCritical";

qDebug("qDebug");
qWarning("qWarning");
qCritical("qCritical");
```

128. Qt的pro文件可以添加各种处理来使得配置更方便，比如指定输出文件路径等，这样就不会全部在一堆编译生成的临时文件中找来找去。
```cpp
#禁用qdebug打印输出
DEFINES     += QT_NO_DEBUG_OUTPUT

#自定义define变量 可以在整个项目中使用
#pro文件可以这样判断 contains(DEFINES, videovlc) {}
#代码文件可以这样判断 #ifdef videovlc
DEFINES     += videovlc1 videoffmpeg

#关闭编译警告提示 眼不见为净
CONFIG      += warn_off

#指定编译生成的文件到temp目录 分门别类存储
MOC_DIR     = temp/moc
RCC_DIR     = temp/rcc
UI_DIR      = temp/ui
OBJECTS_DIR = temp/obj

#指定编译生成的可执行文件到bin目录
DESTDIR     = bin
```

129. Qt对操作系统层的消息也做了很多的封装，可以直接拿到进行处理（如果需要拦截处理要用对应操作系统的API才行比如鼠标键盘钩子），比如系统休眠和唤醒做一些处理。
```cpp
//主窗体头文件
protected:
    bool nativeEvent(const QByteArray &eventType, void *message, long *result);
#ifdef Q_OS_WIN
    bool winEvent(MSG *message, long *result);
#endif

//主窗体实现函数
#ifdef Q_OS_WIN
#include "Windows.h"
#endif

bool frmMain::nativeEvent(const QByteArray &eventType, void *message, long *result)
{
    if (eventType == "windows_generic_MSG") {
#ifdef Q_OS_WIN
        MSG *msg = static_cast<MSG *>(message);
        //qDebug() << TIMEMS << msg->message;
        if (msg->wParam == PBT_APMSUSPEND && msg->message == WM_POWERBROADCAST) {
            //系统休眠的时候自动最小化可以规避程序可能出现的问题
            this->showMinimized();
        } else if (msg->wParam == PBT_APMRESUMEAUTOMATIC) {
            //休眠唤醒后自动打开
            this->showNormal();
        }
#endif
    } else if (eventType == "NSEvent") {
#ifdef Q_OS_MACOS
#endif
    }
    return false;
}

#ifdef Q_OS_WIN
bool frmMain::winEvent(MSG *message, long *result)
{
    return nativeEvent("windows_generic_MSG", message, result);
}
#endif
```

130. Qt的pro项目管理配置文件中也可添加各种编译前后的操作及配置，主要通过 QMAKE_POST_LINK和QMAKE_PRE_LINK，他们支持的函数以及写法，可以在QtCreator的帮助中搜索 qmake Function Reference 查看详情说明。
- QMAKE_PRE_LINK    表示编译前执行内容
- QMAKE_POST_LINK   表示编译后执行内容
```cpp
srcFile1 = $$PWD/1.txt
srcFile2 = $$PWD/2.txt
dstDir = $$PWD/../bin
#windows上需要转换路径斜杠 其他系统不需要
srcFile1 = $$replace(srcFile1, /, \\);
srcFile2 = $$replace(srcFile2, /, \\);
dstDir = $$replace(dstDir, /, \\);

#编译前执行拷贝 多个拷贝可以通过 && 符号隔开
QMAKE_PRE_LINK += copy /Y $$srcFile1 $$dstDir && copy /Y $$srcFile2 $$dstDir
#编译后执行拷贝 多个拷贝可以通过 && 符号隔开
QMAKE_POST_LINK += copy /Y $$srcFile1 $$dstDir && copy /Y $$srcFile2 $$dstDir
```

131. Qt新版本往往会带来一些头文件的更新，比如以前使用QPainter绘制，不需要额外包含QPainterPath头文件，而5.15版本开始就需要显示主动引入#include "qpainterpath.h"才行。

132. Qt6.0发布了，是个比较大的改动版本，很多基础的类或者组件都放到单独的源码包中，需要自行官网下载并编译，默认不提供集成在开发目录下，需要手动编译并集成，比如QRegExp，QTextCodec类，需要编译集成后pro文件 QT += core5compat 才能用， 具体说明在https://doc.qt.io/qt-6/qtcore5-index.html。

133. qDebug输出打印信息，默认会完整打印转义字符，例如：\\  \" \t \n" 等，所以当你发现你明明设置了转义字符以后打印确还是转义前的字符，这就懵逼了，其实这是qdebug为了方便调试将各种字符都打印输出。无可否认，很多时候，我们极其兴奋的享受着Qt带来的各种轮子各种便利，但是偶尔，稍不留意，这些便利可能也会坑你一把。要做的就是擦亮眼睛，时刻谨慎，一步一个脚印踏踏实实码代码。
```cpp
QString s1 = R"(\:device0)";
//TNND居然输出的是 \\:device0
qDebug() << s1;
//这次终于正确的输出 \:device0
qDebug().noquote() << s1;
```

134. 很多人有疑问为何qss对浏览器控件中的网页样式没法控制，其实用屁股想想也知道，那玩意是html css去控制的，和Qt一毛钱关系也没有，根本管不着，如果想要对滚动条样式设置，可以在网页代码中设置样式就行。
```cpp
<style type="text/css">
  ::-webkit-scrollbar{width:0.8em;}
  ::-webkit-scrollbar-track{background:rgb(241,241,241);}
  ::-webkit-scrollbar-thumb{background:rgb(188,188,188);}
</style>
```

135. Qt的ini配置文件默认不支持直接读写中文，需要手动设置下编码格式才行，强烈建议统一用utf-8编码，包括代码文件。
```cpp
//设置了编码以后配置文件内容为 Company=上海物联网技术研究中心
//没有设置编码则配置文件内容为 Company=\xe4\xb8\x8a\xe6\xb5\xb7\xe7\x89\xa9\xe8\x81\x94\xe7\xbd\x91\xe6\x8a\x80\xe6\x9c\xaf\xe7\xa0\x94\xe7\xa9\xb6\xe4\xb8\xad\xe5\xbf\x83
void App::readConfig()
{
    QSettings set(App::ConfigFile, QSettings::IniFormat);
    set.setIniCodec("utf-8");

    set.beginGroup("AppConfig1");
    App::Company = set.value("Company", App::Company).toString();
    set.endGroup();
}
void App::writeConfig()
{
    QSettings set(App::ConfigFile, QSettings::IniFormat);
    set.setIniCodec("utf-8");

    set.beginGroup("AppConfig1");
    set.setValue("Company", App::Company);
    set.endGroup();
}
```

136. 用Qt做安卓开发都会遇到权限的问题，早期的安卓版本可以直接通过 AndroidManifest.xml 配置文件来添加需要的权限，这样在安装app的时候就会提示该app需要哪些权限让用户同意，现在的安卓版本都改成了动态权限，需要在app运行的时候弹出提示让用户确认再有权限，Qt迎合了这种策略内置了动态申请权限的方法 QtAndroid::requestPermissionsSync。
```cpp
//动态设置权限
bool checkPermission(const QString &permission)
{
#ifdef Q_OS_ANDROID
#if (QT_VERSION >= QT_VERSION_CHECK(5, 10, 0))
    QtAndroid::PermissionResult result = QtAndroid::checkPermission(permission);
    if (result == QtAndroid::PermissionResult::Denied) {
        QtAndroid::requestPermissionsSync(QStringList() << permission);
        result = QtAndroid::checkPermission(permission);
        if (result == QtAndroid::PermissionResult::Denied) {
            return false;
        }
    }
#endif
#endif
    return true;
}

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
   
    //请求权限
    checkPermission("android.permission.READ_EXTERNAL_STORAGE");
    checkPermission("android.permission.WRITE_EXTERNAL_STORAGE");   

    return a.exec();
}
```

### 二、其他经验

1. Qt界的中文乱码问题，版本众多导致的如何选择安装包问题，如何打包发布程序的问题，堪称Qt界的三座大山！

2. 在Qt的学习过程中，学会查看对应类的头文件是一个好习惯，如果在该类的头文件没有找到对应的函数，可以去他的父类中找找，实在不行还有爷爷类，肯定能找到的。通过头文件你会发现很多函数接口其实Qt已经帮我们封装好了，有空还可以阅读下他的实现代码。

3. Qt安装目录下的Examples目录下的例子，看完学完，月薪20K起步；Qt常用类的头文件的函数看完学完使用一遍并加以融会贯通，月薪30K起步。

4. Qt在开发阶段不支持中文目录，切记，这是无数人可能犯的错误，在安装Qt集成开发环境以及编译器的时候，务必记得目录必须英文，否则很可能不正常，建议尽量用默认的安装位置。

5. 如果出现崩溃和段错误，80%都是因为要么越界，要么未初始化，死扣这两点，80%的问题解决了。

6. Qt一共有几百个版本，关于如何选择Qt版本的问题，我一般保留四个版本，为了兼容Qt4用4.8.7，最后的支持XP的版本5.7.0，最新的长期支持版本比如5.15，最高的新版本比如5.15.2。强烈不建议使用4.7以前和5.0到5.3之间的版本，太多bug和坑，稳定性和兼容性相比于之后的版本相当差，能换就换，不能换睡服领导也要换。

7. Qt和msvc编译器常见搭配是Qt5.7+VS2013、Qt5.9+VS2015、Qt5.12+VS2017，按照这些搭配来，基本上常用的模块都会有，比如webengine模块，如果选用的Qt5.12+msvc2015，则很可能官方没有编译这个模块，只是编译了Qt5.12+msvc2017的。

8. Qt默认有对应VS版本，在下载对应VS插件的时候心里要有个数，官方默认提供的是原配的插件，如果想要Qt4.8+VS2015的插件，需要自行编译。一般来说是Qt4.8原配VS2010，Qt5.6原配VS2013，Qt5.9原配VS2015，Qt5.12原配VS2017，切记：原配最好。

9. 新版本Qt安装包安装的时候需要填写注册信息，如果不想填写，先禁用网卡，在运行安装包，可以直接跳过这一步进行安装。

10. 终极秘籍：如果遇到问题搜索Qt方面找不到答案，试着将关键字用JAVA C# android打头，你会发现别有一番天地，其他人很可能做过！

11. 如果Qt能从下面几个方面努力，相信会更有发展前景。
- QWidget支持CSS3，具有诸多的牛逼的效果，目前支持的是CSS2。
- QWidget支持GPU绘制，可选切换CPU或者GPU，大大提升绘制效率，利用现在强大的硬件。
- Qml无缝支持js，可以利用现在各种js轮子，指数级提升qml的项目范围。
- 支持将程序转成web运行，比如转成cgi之类的程序，目前Qt for WebAssembly很鸡肋，功能极其有限，首次加载速度超慢，大部分Qt类还不支持。

12. 最后一条：珍爱生命，远离编程。祝大家头发浓密，睡眠良好，情绪稳定，财富自由！

### 三、推荐的Qt论坛+个人博客+网站+群
| 名称 | 网址 |
| ------ | ------ |
|QQ学习群|Qt交流大会群 853086607 Qt技术交流群 46679801 Qt进阶之路群 734623697|
|QtWidget开源demo集合|[https://gitee.com/feiyangqingyun/QWidgetDemo](https://gitee.com/feiyangqingyun/QWidgetDemo)|
|QtQuick/Qml开源demo集合|[https://gitee.com/jaredtao/TaoQuick](https://gitee.com/jaredtao/TaoQuick)|
|qtcn|[http://www.qtcn.org](http://www.qtcn.org)|
|豆子的空间|[https://www.devbean.net](https://www.devbean.net)|
|yafeilinux|[http://www.qter.org](http://www.qter.org)|
|一去二三里|[http://blog.csdn.net/liang19890820](http://blog.csdn.net/liang19890820)|
|乌托邦2号|[http://blog.csdn.net/taiyang1987912](http://blog.csdn.net/taiyang1987912)|
|foruok|[http://blog.csdn.net/foruok](http://blog.csdn.net/foruok)|
|jason|[http://blog.csdn.net/wsj18808050](http://blog.csdn.net/wsj18808050)|
|朝十晚八|[http://www.cnblogs.com/swarmbees](http://www.cnblogs.com/swarmbees)|
|BIG_C_GOD|[http://blog.csdn.net/big_c_god](http://blog.csdn.net/big_c_god)|
|公孙二狗|[https://qtdebug.com/qtbook](https://qtdebug.com/qtbook)|
|雨田哥|[https://blog.csdn.net/ly305750665](https://blog.csdn.net/ly305750665)|
|郑天佐|[https://blog.csdn.net/zhengtianzuo06](https://blog.csdn.net/zhengtianzuo06)|
|寒山-居士|[https://blog.csdn.net/esonpo](https://blog.csdn.net/esonpo)|
|feiyangqingyun|[https://blog.csdn.net/feiyangqingyun](https://blog.csdn.net/feiyangqingyun)|
|前行中小猪|[http://blog.csdn.net/goforwardtostep](http://blog.csdn.net/goforwardtostep)|  
|涛哥的知乎专栏|[https://zhuanlan.zhihu.com/TaoQt](https://zhuanlan.zhihu.com/TaoQt)|
|Qt君|[https://blog.csdn.net/nicai_xiaoqinxi](https://blog.csdn.net/nicai_xiaoqinxi)|  
|Qt老外视频教程|[http://space.bilibili.com/2592237/#!/index](http://space.bilibili.com/2592237/#!/index)|
|Qt维基补充文档|[https://wiki.qt.io/Main](https://wiki.qt.io/Main)|
|Qt源码查看网站|[https://code.woboq.org/qt5](https://code.woboq.org/qt5)|
|Qt官方下载地址|[https://download.qt.io](https://download.qt.io)|
|Qt官方下载新地址|[https://download.qt.io/new_archive/qt/](https://download.qt.io/new_archive/qt/)|
|Qt国内镜像下载地址|[https://mirrors.cloud.tencent.com/qt](https://mirrors.cloud.tencent.com/qt)|
|Qt安装包下载地址|[http://qthub.com/download/](http://qthub.com/download/) (超过1000多个，由Qt君整理)|
|精美图表控件QWT|[http://qwt.sourceforge.net/](http://qwt.sourceforge.net/)|
|精美图表控件QCustomPlot|[https://www.qcustomplot.com/](https://www.qcustomplot.com/)|
|免费图标下载|[http://www.easyicon.net/](http://www.easyicon.net/)|
|图形字体下载|[https://www.iconfont.cn/](https://www.iconfont.cn/)|
|漂亮界面网站|[https://www.ui.cn/](https://www.ui.cn/)|

### 三、其他
1. C++入门书籍推荐《C++ primer plus》，进阶书籍推荐《C++ primer》。
2. Qt入门书籍推荐霍亚飞的《Qt Creator快速入门》，Qt进阶书籍推荐官方的《C++ GUI Qt4编程》，qml书籍推荐《Qt5编程入门》。
3. 强烈推荐程序员自我修养和规划系列书《大话程序员》《程序员的成长课》《解忧程序员》，受益匪浅，受益终生！

# QT

一个完整的QT程序包含main.cpp，自定义类的.h、.cpp文件、.ui文件（可选）、.qrc文件（可选）

main()函数有固定的格式：

```C++
int main(int argc, char *argv[])
{
    QApplication a(agc, argv);
    // 填充代码
    return a.e
}
```



## 信号与槽

信号函数：只需要声明，不需要定义（实现）

槽函数：可以看作对信号做出的响应，需要定义（实现）

QT5版本中，connect()函数的常用语法格式：

```C++
QObject::connect(const QObject *sender, PointerToMemberFunction signal, const QObject *receiver, PointerToMemberFuntion method, QT::ConnectionType type = QT::AutoConnection)
```

eg. 关联 But 按钮的 clicked() 信号函数和 widget 窗口的 close() 槽函数

```C++
connect(&But, &QPushButon::clicked, &widget, &QWidget::close);
```

QT5版本之前的写法：

```C++
connect(&But, SIGNAL(clicked()), &widget, SLOT(close()));
```


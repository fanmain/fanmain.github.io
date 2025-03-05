# 

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/3737c733e3b5)

flutter SDK 提供的默认标签样式不太吸引人。 但这并不意味着您无法自定义标签的外观。 在 Flutter 中自定义`Tab`指示器的样式可以通过简单的代码行完成，而无需实现我们自己的窗口小部件。

在本文中，我将向您展示如何为下一个 Flutter 项目添加 5 种不同的标签样式。

首先，您需要使用`DefaultTabController`类创建一个基本选项卡。 将`DefaultTabController`分配给`MaterialApp`小部件的`home`属性。 作为`DefaultTabController`的子级，可以将`Scaffold与` `Appbar`和主体一起使用。 将`Appbar`小部件分配到`Scaffold`的`Appbar`属性，以使选项卡的标题部分。 对于脚手架的`body`属性，可以为`TabBarView`小部件分配 3 个子小部件，以在单击时显示`Tab`内容项。

![](http://upload-images.jianshu.io/upload_images/9926752-509178602a15cc90.JPG) image

检查完整的代码以获取默认`Tab`。

```
return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          elevation: 0,
          bottom: TabBar(
            indicatorSize: TabBarIndicatorSize.label,
            tabs: [
              Tab(
                child: Align(
                  alignment: Alignment.center,
                  child: Text("APPS"),
                ),
              ),
              Tab(
                child: Align(
                  alignment: Alignment.center,
                  child: Text("MOVIES"),
                ),
              ),
              Tab(
                child: Align(
                  alignment: Alignment.center,
                  child: Text("GAMES"),
                ),
              ),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Icon(Icons.apps),
            Icon(Icons.movie),
            Icon(Icons.games),
          ],
        ),
      ),
    );
```

### 1. 圆角 Tab 风格

作为第一种样式，我们将向选项卡指示器添加圆角样式。 首先，我将简要介绍我们将要修改的参数。

###### 1. unselectedLabelColor –不存在指示符的标签颜色。 基本上，尚未选择的指标。

###### 2. indicatorSize –选定的指标大小。 我们可以添加两个值以使指标为标签宽度或标签宽度。

###### 3. Indicator –这是我们要为指标分配自定义样式的地方

`Tab`–这将包含`Tab`标题的列表。 在这里，我们可以为每个`Tab`标题添加额外的样式。  
可以通过添加带有`borderRadius 50`的`BoxDecoration`来实现圆角样式。在这里，我们向每个`Tab```标题添加红色边框。 当有人选择`Tab`` 时，它将用红色填充。 如果您对边框不感兴趣，可以删除边框并保持简单。

![](http://upload-images.jianshu.io/upload_images/9926752-b6aaf224eee19174.png) image

```
return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          backgroundColor:Colors.white,
          elevation: 0,
          bottom: TabBar(
            unselectedLabelColor:Colors.redAccent,
            indicatorSize: TabBarIndicatorSize.label,
            indicator:BoxDecoration(
              color:Colors.redAccent,
              borderRadius:BorderRadius.circular(50),
            ),
            tabs: [
              Tab(
                child:Container(
                  decoration:BoxDecoration(
                    borderRadius:BorderRadius.circular(50),
                    border:Border.all(color:Colors.redAccent,
                      width:1,
                    ),
                  ),
                  child:Align(
                    alignment:Alignment.center,
                    child:Text("APPS"),
                  ),
                ),
              ),
              Tab(
                child:Container(
                  decoration:BoxDecoration(
                    borderRadius:BorderRadius.circular(50),
                    border:Border.all(color:Colors.redAccent,
                      width:1,
                    ),
                  ),
                  child:Align(
                    alignment:Alignment.center,
                    child:Text("MOVIES"),
                  ),
                ),
              ),
              Tab(
                child:Container(
                  decoration:BoxDecoration(
                    borderRadius:BorderRadius.circular(50),
                    border:Border.all(color:Colors.redAccent,
                      width:1,
                    ),
                  ),
                  child:Align(
                    alignment:Alignment.center,
                    child:Text("GASMES"),
                  ),
                ),
              ),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Icon(Icons.apps),
            Icon(Icons.movie),
            Icon(Icons.games),
          ],
        ),
      ),
    );
```

### 2. 圆角带有渐变色 Tab 风格

我们将删除以前方法中添加到每个`Tab`的样式。 删除后，向`BoxDecoration`添加渐变。 您可以使用带有两种颜色的`LinearGradient`小部件来获得渐变效果。 您可以根据自己的喜好更改渐变。

![](http://upload-images.jianshu.io/upload_images/9926752-e2422c899d8b13f1.png) image

```
return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          backgroundColor:Colors.white,
          elevation: 0,
          bottom: TabBar(
            unselectedLabelColor:Colors.redAccent,
            indicatorSize: TabBarIndicatorSize.tab,
            indicator:BoxDecoration(
              gradient:LinearGradient(
                colors:[
                  Colors.redAccent,
                  Colors.orangeAccent,
                ],
              ),
              color:Colors.redAccent,
              borderRadius:BorderRadius.circular(50),
            ),
            tabs: [
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("APPS"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("MOVIES"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("GASMES"),
                ),
              ),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Icon(Icons.apps),
            Icon(Icons.movie),
            Icon(Icons.games),
          ],
        ),
      ),
    );
```

### 3. 矩形 Tab 风格

矩形样式可以通过更改上一个中的小代码来完成。 可以通过为`leftTop`和`rightTop`都添加`10`来更改`boxDecoration`的`BorderRadius`。 然后，我将`appbar backgroundColor`更改为红色，以使其看起来更好。

![](http://upload-images.jianshu.io/upload_images/9926752-78db1333f0ca1fd1.png) image

```
return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          backgroundColor:Colors.redAccent,
          elevation: 0,
          bottom: TabBar(
            labelColor:Colors.redAccent,
            unselectedLabelColor:Colors.white,
            indicatorSize: TabBarIndicatorSize.label,
            indicator:BoxDecoration(
              color:Colors.white,
              borderRadius:BorderRadius.only(
                topLeft:Radius.circular(10),
                topRight:Radius.circular(10),
              ),
            ),
            tabs: [
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("APPS"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("MOVIES"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("GASMES"),
                ),
              ),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Icon(Icons.apps),
            Icon(Icons.movie),
            Icon(Icons.games),
          ],
        ),
      ),
    );
```

### 4. 菱形 Tab 样式

您可以通过为`ShapeDecoration`小部件的`shape`参数添加带有 [BeveledRectangleBorder](https://links.jianshu.com/go?to=https%3A%2F%2Fapi.flutter.dev%2Fflutter%2Fpainting%2FBeveledRectangleBorder-class.html) 的`ShapeDecoration`来获得`Diamond`选项卡样式。 `BeveledRectangleBorder`将允许您添加展平角而不是圆角。

在这里，我们使用 borderRadius 作为 10 使其看起来像这样。

![](http://upload-images.jianshu.io/upload_images/9926752-37d0b22a5e0eb792.png) image

```
return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          backgroundColor:Colors.white,
          elevation: 0,
          bottom: TabBar(
            unselectedLabelColor:Colors.redAccent,
            indicatorPadding:EdgeInsets.only(left:30,right:30),
            indicator:ShapeDecoration(
              color:Colors.redAccent,
              shape:BeveledRectangleBorder(
                side:BorderSide(
                  color:Colors.redAccent,
                ),
                borderRadius:BorderRadius.circular(10),
              ),
            ),
            tabs: [
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("APPS"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("MOVIES"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("GASMES"),
                ),
              ),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Icon(Icons.apps),
            Icon(Icons.movie),
            Icon(Icons.games),
          ],
        ),
      ),
    );
```

### 5. 菱形 Tab 样式（2）

同样，通过更改`BeveledRectangleBorder`的`borderRadius`，可以实现不同的形状。 您可以将`borderRadius`更改为 20，以获得其他形状。 您可以通过更改 borderRadius 值尝试不同的样式。

![](http://upload-images.jianshu.io/upload_images/9926752-d407b7368cb7c17e.png) image

```
return DefaultTabController(
      length: 3,
      child: Scaffold(
        appBar: AppBar(
          backgroundColor:Colors.white,
          elevation: 0,
          bottom: TabBar(
            unselectedLabelColor:Colors.redAccent,
            indicatorPadding:EdgeInsets.only(left:30,right:30),
            indicator:ShapeDecoration(
              color:Colors.redAccent,
              shape:BeveledRectangleBorder(
                side:BorderSide(
                  color:Colors.redAccent,
                ),
                borderRadius:BorderRadius.circular(20),
              ),
            ),
            tabs: [
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("APPS"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("MOVIES"),
                ),
              ),
              Tab(
                child:Align(
                  alignment:Alignment.center,
                  child:Text("GASMES"),
                ),
              ),
            ],
          ),
        ),
        body: TabBarView(
          children: [
            Icon(Icons.apps),
            Icon(Icons.movie),
            Icon(Icons.games),
          ],
        ),
      ),
    );
```

我希望您能通过几行代码更好地了解如何更改选项卡样式。 如果您想观看此视频，请观看以下视频。  
[https://www.youtube.com/watch?v=Vnd0yvCkdNA&feature=youtu.be](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3DVnd0yvCkdNA%26feature%3Dyoutu.be)

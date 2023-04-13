---
title: qt富文本解析
tags:
  - test1
  - test2
categories:
  - test3
  - test4
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 08:47:42/qt富文本解析.html
mathJax: false
date: 2023-04-13 16:47:42
thumbnail:
---
富文本结构详见Qt文档：Rich Text Document Structure

Qt里`QTextDocument`类提供了富文本支持，可以使用`setHtml()`设置富文本，使用`toHtml()`获取富文本。

<!-- more -->

### 1.自定义对象

`QTextObjectInterface`类允许在`QTextDocument`类中增加自定义对象

##### 实现

详见`QTextObjectInterface Class`

1. 继承 `QObject` 和 `<QTextObjectInterface>`

    ```cpp
    class SvgTextObject : public QObject, public QTextObjectInterface
    {
        Q_OBJECT
        Q_INTERFACES(QTextObjectInterface)
    ...
    ```

 2. 实现两个接口函数

    ```cpp
    // 对象呈现样式，用painter画出来
    virtual void drawObject(QPainter *painter, const QRectF &rect, QTextDocument *doc, int posInDocument, const QTextFormat &format) = 0;
    // 自定义对象大小
    virtual QSizeF intrinsicSize(QTextDocument *doc, int posInDocument, const QTextFormat &format) = 0
    ```

##### 注册

1. 注册自定义枚举

   ```cpp
   // 对象枚举
   enum MyTextObject
   {
       kMyObject = QTextFormat::UserObject + 1
   };
   // 属性枚举
   enum MyTextProperty
   {
       kMyProperty = QTextFormat::UserProperty + 1
   };
   ```

2. 注册自定义对象

    `void QAbstractTextDocumentLayout::registerHandler(int objectType, QObject *component)`

    ```cpp
    QTextEdit* editor = new QTextEdit(/*parent*/);
    SvgTextObject* handle = new SvgTextObject;
    handle->setParent(editor);
    editor->document()->documentLayout()->registerHandler(kMyObject, handle);
	```

##### 插入

1. 设置对象属性

   ```cpp
   T info; // 对象携带信息
   QTextCharFormat format;
   format.setObjectType(kMyObject);
   format.setProperty(kMyProperty, QVariant::fromValue(info));
   ```

2. 插入

   ```cpp
   QTextCursor tc = editor->textCursor();
   tc.insertText(QString(QChar::ObjectReplacementCharacter), format);
   editor.setTextCursor(tc);
   ```

</br>

### 2.图片

##### 插入

法1：使用html语言

```cpp
QString html_text;
html_text.append("<img src=\"%1\" width=\"%2\" height=\"%3\" />").arg(img_name).arg(img_width).arg(img_height);
QTextDocument doc;
doc.setHtml(html_text);
```

法2：使用`QTextCursor `插入 `QTextImageFormat`

```cpp
// QImage image 图片资源 
QTextImageFormat image_format;
image_format.setName(name);
image_format.setWidth(w);
image_format.setHeight(h);
// 如果需要锚点，可以设置
image_format.setAnchor(true);
image_format.setAnchorHref(name);
// 图片布局
image_format.setVerticalAlignment(QTextCharFormat::VerticalAlignment);
// 相同名字的图片解析时只会触发一次，暂时设置唯一标识避免
image_format.setAnchorName(Util::GenUuid());
// 加入资源
editor->document()->addResource(QTextDocument::ImageResource, name, QVariant(image));
// 插入图片
editor()->textCUrsor().insertImage(image_format);
// 解决后续输入文本也是超链接
QTextCharFormat format;
format.setAnchor(false);
format.setForeground(Qt::black);
editor->setCurrentCharFormat(format);
```

### 3.UI交互

可以根据鼠标位置获取锚点

```cpp
QString QTextEdit::anchorAt(const QPoint &pos) const
```

删除鼠标位置元素

```cpp
void MyTextEdit::RemoveChar(const QPoint& pos) // pos 鼠标位置 event->pos()
{
    QTextCurosr tc = cursorForPosition(pos);
    // 鼠标位置在元素的左半部分和右半部分点击获取的光标位置不同
    // 比较anchor, 不匹配，删除position后一个；匹配，删除position前一个
    do {
        if (tc.position() == 0) {
            tc.deleteChar();
            break;
        }
        QTextCharFormat format = tc.charFormat();
        QString source_name = anchorAt(pos);
        QString anchor_name;
        if (QTextFormat::ImageObject == format.objectType()) {
            anchor_name = format.toImageFormat().name();
        } // else if 自定义类型
        if (anchor_name == source) {
            tc.deletePreviousChar();
        } else {
            tc.deleteChar();
        }
        
    } while (0);
    setTextCursor(tc);
}
```


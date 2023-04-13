---
title: qt剪贴板
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 08:31:46/qt剪贴板.html
mathJax: false
date: 2023-04-13 16:31:46
thumbnail:
---
### 全局剪贴板

剪贴板数据更新时发送信号

```cpp
[signal] void QClipboard::dataChanged();
```

<!-- more -->

获取剪贴板数据

```cpp
const QMimeData *mimeData(QClipboard::Mode mode = Clipboard) const;
//
QApplication::clipboard()->mimeData();
```

设置剪贴板数据

```cpp
void setMimeData(QMimeData *src, QClipboard::Mode mode = Clipboard);
//
QApplication::clipboard()->setMimeData(mimedata);
```

</br>

### QMimeData

QMimeData 存储剪贴板数据

通常由对象 `QDrag` 和 `QClipboard` 使用

| Tester     | Getter      | Setter         | MIME Types          |
| ---------- | ----------- | -------------- | ------------------- |
| hasText()  | text()      | setText()      | text/plain          |
| hasHtml()  | html()      | setHtml()      | text/html           |
| hasUrls()  | urls()      | setUrls()      | text/uri-list       |
| hasImage() | imageData() | setImageData() | image/ *            |
| hasColor() | colorData() | setColorData() | application/x-color |

##### example

```cpp
void MyWidget::dragEnterEvent(QDragEnterEvent *event)
{
    if (event->mimeData()->hasUrls())
        event->acceptProposedAction();
}

void MyWidget::dropEvent(QDropEvent *event)
{
    if (event->mimeData()->hasUrls()) {
        foreach (QUrl url, event->mimeData()->urls()) {
            ...
        }
    }
}
```

##### 存储自定义数据

1. Custom data can be stored directly in a QMimeData object as a QByteArray using setData(). For example:

   ```cpp
   QByteArray csvData = ...;
   QMimeData *mimeData = new QMimeData;
   mimeData->setData("text/csv", csvData);
   ```

2. We can subclass QMimeData and reimplement hasFormat(), formats(), and retrieveData().

3. If the drag and drop operation occurs within a single application, we can subclass QMimeData and add extra data in it, and use a qobject_cast() in the receiver's drop event handler. 

   For example: 

   ```cpp
   void MyWidget::dropEvent(QDropEvent *event)
   {
       const MyMimeData *myData =
       qobject_cast<const MyMimeData *>(event->mimeData());
       if (myData) {
       // access myData's data directly (not through QMimeData's API)
       }
   }
   ```



</br>

### QTextEdit

##### 文本输入框的粘贴

1. 重写该函数，返回值决定是否允许粘贴

   ```cpp
   [virtual protected] bool QTextEdit::canInsertFromMimeData(const QMimeData *source) const
   ```

2. 重写该函数，自定义粘贴

   ```cpp
   [virtual protected] void QTextEdit::insertFromMimeData(const QMimeData *source)
   ```

   


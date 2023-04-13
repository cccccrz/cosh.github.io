---
title: qt增加右键菜单
tags:
  - qt
categories:
  - qt
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-04-13 06:40:35/qt增加右键菜单.html
mathJax: false
date: 2023-04-13 14:40:35
thumbnail:
---
示例
<!-- more -->

普通widget增加右键菜单，增加事件过滤器即可

```cpp
QWidget::eventFilter(QObject* obj, QEvent* e) 
{
	if (QEvent::MouseButtonPress == e->type()) {
		if (Qt::RightButton == event->button()) {
			CreateMenu(event->globalPos());
		}
	}
}

void CreateMenu(const QPoint& pos)
{
    QMenu* menu = new QMenu(/*parent*/);
    QAction* action1 = new QAction("a1", menu);
    QAction* action2 = new QAction("a2", menu/*, SLOT()*/);
    menu->addAction(aciton1);
    menu->addSeparator();
    menu->addAction(action2);
    connect(menu, SIGNAL(triggered(QAction*)), this, SLOT(OnMenuTrigged(QAction*)));
    
    // 同步
#if 1
    menu->exec(pos);
    delete menu;
#else
    // 异步
    menu->setAttribute(Qt::WA_DeleteOnClose); // 窗口关闭后自行析构
    menu->popup(pos);
#endif
}
```



默认的菜单事件

`[virtual protected] void QWidget::contextMenuEvent(QContextMenuEvent *event)`

部分控件可以重写该函数，实现自定义菜单

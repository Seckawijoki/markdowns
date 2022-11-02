## 目录
- [目录](#目录)
- [环境](#环境)
- [需求](#需求)
- [实现原理](#实现原理)
- [实现](#实现)
  - [源码改动](#源码改动)
    - [QtTreePropertyBrowser](#qttreepropertybrowser)
  - [关键代码](#关键代码)
  - [业务代码](#业务代码)

## 环境
Windows 10
Visual Studio 2017/2019 Professional/Community
Qt 5.15.1
Qt Creator 4.11.0

## 需求
现在需要拖入文件到一个String类型的属性，能自动转换成路径的快捷功能

## 实现原理
由于源码中缺乏通过QPoint获取QtProperty/QtBrowserItem/QModelIndex的功能，关键部分是要实现通过放下文件一瞬间的QPoint找到对应的QtProperty。
QtTreePropertyBrowser分为标题头和属性表两部分。属性表由源码自定义的QTreeWidget作为表现。
```c++
class QtPropertyEditorView : public QTreeWidget
{
    //...
}
class QtTreePropertyBrowserPrivate
{
    //...
private:
    QtPropertyEditorView *m_treeWidget;
    //...
};
```
特别地，该版本中，调用~~QTreeWidgetItem* QTreeWidget::itemAt(QPoint)~~这个函数，无法获取想要的结果。

## 实现

### 源码改动

#### QtTreePropertyBrowser

```c++

```

获取内置的QTreeWidget
```c++
QTreeWidget* QtTreePropertyBrowser::treeWidget() const
{
	return d_ptr->treeWidget();
}
```

从获取到的``QTreeWidgetItem``，转换成``QtBrowserItem``，进而取到``QtProperty``
```c++
QtBrowserItem * QtTreePropertyBrowser::browserItem(QTreeWidgetItem* twi) const
{
	return d_ptr->m_itemToIndex[twi];
}
```

### 关键代码

封装两个函数：

使用BFS遍历各个QTreeWidgetItem，使用``QRect QTreeWidget::visualItemRect(QTreeWidgetItem*)``获取改子项的UI范围，判断子项是否包含posLocal。**posLocal是相对于QTreeWidget的位置**，下方有进一步说明。
```c++
QTreeWidgetItem* treeWidgetItemAtPoint(const QTreeWidget* tw, const QPoint& posLocal)
{
	QQueue<QTreeWidgetItem*> bfsQueue;
	int count = tw->topLevelItemCount();
	for (int i = 0; i < count; i++)
	{
		QTreeWidgetItem* twi = tw->topLevelItem(i);
		bfsQueue.push_back(twi);
	}
	QTreeWidgetItem* target = nullptr;
	while (!bfsQueue.empty())
	{
		QTreeWidgetItem* twi = bfsQueue.takeFirst();
		count = twi->childCount();
		for (int i = 0; i < count; i++)
		{
			QTreeWidgetItem* twiChild = twi->child(i);
			bfsQueue.push_back(twiChild);
		}
		QRect rect = tw->visualItemRect(twi);
        //QString text = twi->text(0);
		if (!rect.contains(posLocal))
		{
			continue;
		}
		target = twi;
		break;
	}
	return target;
}
```

这里的pos是相对于QtTreePropertyBrowser的位置，接入您的项目时，根据实际情况调整pos的值。
可优先用tpb->geometry()判断是否在UI范围内。
**下一步重要的是，pos.y是否要减去HeaderView的高度。**
然后调用上述的treeWidgetItemAtPoint，获取QTreeWidgetItem。
最后通过在源码新增的函数获取QtBrowserItem，进而获取QtProperty。
```c++
QtProperty* propertyAtPoint(const QtTreePropertyBrowser* tpb, const QPoint& pos)
{
	QTreeWidget* tw = tpb->treeWidget();
	QRect rect = tpb->geometry();
	if (!rect.contains(pos))
	{
		return nullptr;
	}
	QPoint posBody = pos;
	if (tpb->isHeaderVisible())
	{
		//pos减去Header高度
		posBody.setY(pos.y() - hv->height());
	}
	QTreeWidgetItem* twi = treeWidgetItemAtPoint(tw, posBody);
	if (!twi)
	{
		return nullptr;
	}
	QtBrowserItem* bi = tpb->browserItem(twi);
	if (!bi)
	{
		return nullptr;
	}
	return bi->property();
}
```

### 业务代码

开始，设置QtTreePropertyBrowser接收放置文件事件（同样可在Qt Creator中设置）。
同时重写``dragEnterEvent``和``dropEvent``，来接收文件。
在dropEvent中，直接使用``QCursor::pos()``与QTreeWidget的``QWidget::mapFromGlobal(posCursor)``，传入我们上述封装函数的相对于QtTreePropertyBrowser的位置。
>> 最后的vpm->propertyType(prop) == QVariant::String，可根据自身项目，判断不同的类型（含自定义类型）来处理不同逻辑
```c++
XXX::XXX(/*...*/) :
    //...
{
    //...
	QtTreePropertyBrowser* tpb = /*...*/;
    tpb->setAcceptDrops(true);
    //...
}

void XXX::dragEnterEvent(QDragEnterEvent *event)
{
	if (!event->mimeData()->hasUrls())
	{
		return;
	}
	event->acceptProposedAction();
}

void XXX::dropEvent(QDropEvent *event)
{
	QList<QUrl> listUrl = event->mimeData()->urls();
	if (listUrl.empty())
	{
		return;
	}
	QUrl url = listUrl[0];
	QtTreePropertyBrowser* tpb = /*...*/;
	QTreeWidget* tw = tpb->treeWidget();
	QPoint posCursor = QCursor::pos();
	QPoint posTwLocal = tw->mapFromGlobal(posCursor);
	QtProperty* prop = QtUtils::propertyAtPoint(tpb, posTwLocal);
	if (!prop)
	{
		return;
	}
	QtVariantProperty* vp = static_cast<QtVariantProperty*>(prop);
	QtVariantPropertyManager* vpm = /*...*/;
    if (vpm->propertyType(prop) == QVariant::String)
	{
		if (vp)
		{
			vp->setValue(url.toLocalFile());
		}
	}
}
```
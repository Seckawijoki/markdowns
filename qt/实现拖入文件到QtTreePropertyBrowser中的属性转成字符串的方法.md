## Ŀ¼
- [Ŀ¼](#Ŀ¼)
- [����](#����)
- [����](#����)
- [ʵ��ԭ��](#ʵ��ԭ��)
- [ʵ��](#ʵ��)
  - [Դ��Ķ�](#Դ��Ķ�)
    - [QtTreePropertyBrowser](#qttreepropertybrowser)
  - [�ؼ�����](#�ؼ�����)
  - [ҵ�����](#ҵ�����)

## ����
Windows 10
Visual Studio 2017/2019 Professional/Community
Qt 5.15.1
Qt Creator 4.11.0

## ����
������Ҫ�����ļ���һ��String���͵����ԣ����Զ�ת����·���Ŀ�ݹ���

## ʵ��ԭ��
����Դ����ȱ��ͨ��QPoint��ȡQtProperty/QtBrowserItem/QModelIndex�Ĺ��ܣ��ؼ�������Ҫʵ��ͨ�������ļ�һ˲���QPoint�ҵ���Ӧ��QtProperty��
QtTreePropertyBrowser��Ϊ����ͷ�����Ա������֡����Ա���Դ���Զ����QTreeWidget��Ϊ���֡�
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
�ر�أ��ð汾�У�����~~QTreeWidgetItem* QTreeWidget::itemAt(QPoint)~~����������޷���ȡ��Ҫ�Ľ����

## ʵ��

### Դ��Ķ�

#### QtTreePropertyBrowser

```c++

```

��ȡ���õ�QTreeWidget
```c++
QTreeWidget* QtTreePropertyBrowser::treeWidget() const
{
	return d_ptr->treeWidget();
}
```

�ӻ�ȡ����``QTreeWidgetItem``��ת����``QtBrowserItem``������ȡ��``QtProperty``
```c++
QtBrowserItem * QtTreePropertyBrowser::browserItem(QTreeWidgetItem* twi) const
{
	return d_ptr->m_itemToIndex[twi];
}
```

### �ؼ�����

��װ����������

ʹ��BFS��������QTreeWidgetItem��ʹ��``QRect QTreeWidget::visualItemRect(QTreeWidgetItem*)``��ȡ�������UI��Χ���ж������Ƿ����posLocal��**posLocal�������QTreeWidget��λ��**���·��н�һ��˵����
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

�����pos�������QtTreePropertyBrowser��λ�ã�����������Ŀʱ������ʵ���������pos��ֵ��
��������tpb->geometry()�ж��Ƿ���UI��Χ�ڡ�
**��һ����Ҫ���ǣ�pos.y�Ƿ�Ҫ��ȥHeaderView�ĸ߶ȡ�**
Ȼ�����������treeWidgetItemAtPoint����ȡQTreeWidgetItem��
���ͨ����Դ�������ĺ�����ȡQtBrowserItem��������ȡQtProperty��
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
		//pos��ȥHeader�߶�
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

### ҵ�����

��ʼ������QtTreePropertyBrowser���շ����ļ��¼���ͬ������Qt Creator�����ã���
ͬʱ��д``dragEnterEvent``��``dropEvent``���������ļ���
��dropEvent�У�ֱ��ʹ��``QCursor::pos()``��QTreeWidget��``QWidget::mapFromGlobal(posCursor)``����������������װ�����������QtTreePropertyBrowser��λ�á�
>> ����vpm->propertyType(prop) == QVariant::String���ɸ���������Ŀ���жϲ�ͬ�����ͣ����Զ������ͣ�������ͬ�߼�
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
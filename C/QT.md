### 遍历一个文件夹下的文件
```c++
QString filename = QFileDialog::getExistingDirectory();
QDir *dir = new QDir(filename);
QStringList filter;
filter << "*.cpp";
QList<QFileInfo> *fileInfo = new QList<QFileInfo>(dir->entryInfoList(filter));
for (int i = 0; i<fileInfo->count(); i++)
{
  qDebug() << fileInfo->at(i).filePath();
  qDebug() << fileInfo->at(i).fileName();
}
```  

### connect函数
```c++
QObject::connect(upLoadDir, &QPushButton::clicked, [=]()
{
	func();
});  
```

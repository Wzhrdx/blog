---
title: 如何利用Qt创建一个IDE
categories:
  - Qt
tags:
  - qt
  - c++
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-06.jpg'
abbrlink: 5e5eb9b1
date: 2020-04-22 00:00:00
---
## 新建一个Qt项目
新建一个`Qt Widgets Applications`项目
在`Details`不勾选`Generate form`
![图1](https://s1.ax1x.com/2020/04/22/JtF61K.png)

一直点下一步
在`Kits`里勾选`MinGw 32-bit`和`MinGW 64-bit`
在`mainwindows.h`里修改
```css

#ifndef MAINWINDOW_H
#define MAINWINDOW_H


#include <QMainWindow>

#include <QTextEdit>    //文本

QT_BEGIN_NAMESPACE
class QTextEdit;
QT_END_NAMESPACE


class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    MainWindow(QWidget *parent = 0);

private:

    QTextEdit *editor; //中央XextEdit控件

private slots:
    void setupEditor();
};


#endif // MAINWINDOW_H


```
在`mainwindows.cpp`里修改
```css


#include <QtWidgets>

#include "mainwindow.h"


MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
{

    setupEditor();
    setCentralWidget(editor);
    setWindowTitle(tr("IDE"));

}

void MainWindow::setupEditor()
{
    QFont font;
    font.setFamily("Courier");
    font.setFixedPitch(true);
    font.setPointSize(10);

    editor = new QTextEdit;
    editor->setFont(font);

}

```
在`main.cpp`里修改
```css

#include "mainwindow.h"

#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication app(argc, argv);
    MainWindow window;
    window.resize(640, 512);
    window.show();
    return app.exec();
}


```
## 添加状态栏
在`mainwindows.h`里修改
```css

#ifndef MAINWINDOW_H
#define MAINWINDOW_H


#include <QMainWindow>

#include <QTextEdit>    //文本
#include <QMenu>        //加入菜单
#include <QMenuBar>     //加入菜单栏
#include <QAction>      //加入菜单栏
#include <QFileDialog>

QT_BEGIN_NAMESPACE
class QTextEdit;
QT_END_NAMESPACE


class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    MainWindow(QWidget *parent = 0);

private:

    bool is_changed;
    QTextEdit *editor; //中央XextEdit控件

    QString fileName;  //当前文件的名字


    //文件菜单
    QMenu *file_menu;   //文件菜单
    QAction *new_file;   //新建文件菜单项
    QAction *open_file;   //打开文件菜单项
    QAction *save_file;   //保存文件菜单项
    QAction *exit_file;   //退出文件菜单项

    //编辑菜单
    QMenu *edit_menu;   //编辑菜单
    QAction *copy_edit;   //编辑菜单的复制按钮
    QAction *paste_edit;
    QAction *cut_edit;
    QAction *allselect_edit;

    QMenu *help_menu;   //帮助菜单

    //编译菜单
    QMenu *comp_menu;   //编译菜单
    QAction *comp_comp;   //编译按钮
    QAction *run_comp;   //运行按钮


    void precomp();

private slots:
    void on_exit();//在QT编辑环境，安装ALT+ENTER，出现提示再按一次回车
    void on_open(const QString &path = QString());
    void on_save();
    void on_new();
    void on_copy();
    void on_paste();
    void on_cut();
    void on_allselect();
    void on_changed();
    void on_comp();
    void on_run();
    void setupEditor();
    void setupFileMenu();
    void setupHelpMenu();
    void about();
};


#endif // MAINWINDOW_H


```
在`mainwindows.cpp`里修改
```css


#include <QtWidgets>

#include "mainwindow.h"


MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
{
    setupFileMenu();
    setupHelpMenu();
    setupEditor();

    setCentralWidget(editor);
    setWindowTitle(tr("IDE"));


    //文本内容在保存前是否发生变动
    is_changed = false;

    //添加菜单项，并为其设定快捷键
    //【文件菜单栏】
    file_menu = this->menuBar()->addMenu("文件");
    new_file = new QAction("新建文件",this);  //第一个参数是菜单项的文字，第二个参数是指向主体的指针
    new_file ->setShortcut(tr("ctrl+n"));   //设定快捷键
    file_menu ->addAction(new_file);        //关联 菜单栏 与 菜单项
    file_menu ->addSeparator();             //在文件下拉菜单上面显示一个分隔符

    open_file = new QAction("打开文件",this);
    file_menu ->addAction(open_file);
    open_file ->setShortcut(tr("ctrl+o"));

    save_file = new QAction("保存文件",this);
    file_menu ->addAction(save_file);
    save_file ->setShortcut(tr("ctrl+s"));

    exit_file = new QAction("退出",this);
    file_menu ->addAction(exit_file);



    //【编译菜单栏】
    comp_menu = this->menuBar()->addMenu("编译");
    comp_comp = new QAction("编译",this); comp_menu->addAction(comp_comp);
    run_comp = new QAction("运行",this);  comp_menu->addAction(run_comp);

    //【帮助菜单栏】
    help_menu = this->menuBar()->addMenu("帮助");



    //【鼠标事件与函数关联】当鼠标点击exit_file 菜单的时候，执行on_exit()函数
    connect(exit_file,SIGNAL(triggered()),this,SLOT(on_exit()));
    connect(open_file,SIGNAL(triggered()),this,SLOT(on_open()));
    connect(save_file,SIGNAL(triggered()),this,SLOT(on_save()));
    connect(new_file,SIGNAL(triggered()),this,SLOT(on_new()));
    connect(copy_edit,SIGNAL(triggered()),this,SLOT(on_copy()));
    connect(paste_edit,SIGNAL(triggered()),this,SLOT(on_paste()));
    connect(cut_edit,SIGNAL(triggered()),this,SLOT(on_cut()));
    connect(allselect_edit,SIGNAL(triggered()),this,SLOT(on_allselect()));
    connect(editor,SIGNAL(textChanged()),this,SLOT(on_changed()));//当文本内容发生变化时，触发on_changed函数
    connect(comp_comp,SIGNAL(triggered()),this,SLOT(on_comp()));//当文本内容发生变化时，触发on_changed函数
    connect(run_comp,SIGNAL(triggered()),this,SLOT(on_run()));//当文本内容发生变化时，触发on_changed函数

}



void MainWindow::on_open(const QString &path)
{
    QString fileName = path;

    if (fileName.isNull())
        fileName = QFileDialog::getOpenFileName(this, tr("Open File"), "", "C++ Files (*.cpp *.h)");

    if (!fileName.isEmpty()) {
        QFile file(fileName);
        if (file.open(QFile::ReadOnly | QFile::Text))
            editor->setPlainText(file.readAll());
    }
}

void MainWindow::setupFileMenu()
{
    QMenu *fileMenu = new QMenu(tr("&File"),this);
    menuBar()->addMenu(fileMenu);
    //【编辑菜单栏】
    edit_menu = this->menuBar()->addMenu("编辑");
    copy_edit = new QAction("复制",this);
    copy_edit ->setShortcut(tr("ctrl+c"));
    edit_menu ->addAction(copy_edit);

    paste_edit = new QAction("粘贴",this);
    paste_edit ->setShortcut(tr("ctrl+v"));
     edit_menu ->addAction(paste_edit);

    cut_edit = new QAction("剪切",this);
    cut_edit ->setShortcut(tr("ctrl+x"));
    edit_menu ->addAction(cut_edit);

    allselect_edit = new QAction("全选",this);
    allselect_edit ->setShortcut(tr("ctrl+a"));
    edit_menu ->addAction(allselect_edit);

}

void MainWindow::setupEditor()
{
    QFont font;
    font.setFamily("Courier");
    font.setFixedPitch(true);
    font.setPointSize(10);

    editor = new QTextEdit;
    editor->setFont(font);



    QFile file("mainwindow.h");
    if (file.open(QFile::ReadOnly | QFile::Text))
        editor->setPlainText(file.readAll());
}
void MainWindow::about()
{
    QMessageBox::about(this, tr("About IDE"),
                tr("version0.0.1"));
}



void MainWindow::setupHelpMenu()
{
    QMenu *helpMenu = new QMenu(tr("&Help"), this);
    menuBar()->addMenu(helpMenu);

    helpMenu->addAction(tr("&About"), this, SLOT(about()));
}


void MainWindow::precomp()//预编译
{
    FILE *p = fopen(fileName.toStdString().data(),"r");
    if(p == NULL) return ;
    QString cmd = fileName +".c";
    FILE *p1 = fopen(cmd.toStdString().data(),"w");
    if(p1 == NULL) return ;
    QString str;
    while(!feof(p))
    {
        char buf[1024] = {0};
        fgets(buf,sizeof(buf),p);
        str += buf;
    }

    str.replace("包含","#include");
    str.replace("主函数","main");
    str.replace("整数","int");
    str.replace("开始","{");
    str.replace("收工","}");
    str.replace("。",";");
    str.replace("返回","return");
    str.replace("打印","printf");
    str.replace("输入输出","<stdio.h>");
    str.replace("无声的等待...","getchar()");

    fputs(str.toStdString().data(),p1);
    fclose(p);
    fclose(p1);
}

//程序退出
void MainWindow::on_exit()
{
    this ->close();
}

//保存文件
void MainWindow::on_save()
{
    if(fileName.isEmpty())
    {
        fileName = QFileDialog::getSaveFileName(this,"保存文件");
    }
    if(!fileName.isEmpty())
    {
        FILE *p = fopen(fileName.toStdString().data(),"w");
        if(p == NULL) return ;
        QString str = editor->toPlainText();
        fputs(str.toStdString().data(),p);
        fclose(p);
    }
}
//新建文件
void MainWindow::on_new()
{
    if(is_changed == true)
    {
        on_save();
        is_changed = false;
    }
    fileName = "";
    editor->setText("");
}
//IDE的复制功能
void MainWindow::on_copy()
{
    editor->copy();
}

void MainWindow::on_paste()
{
    editor->paste();
}

void MainWindow::on_cut()
{
    editor->cut();
}

void MainWindow::on_allselect()
{
    editor->selectAll();
}

void MainWindow::on_changed()
{
    is_changed = true;
}
//编译并运行按钮
void MainWindow::on_comp()
{
    if (is_changed == true)//在点击编译按钮，如果文本内容发生变化，就自动保存
    {
        on_save();
    }
    precomp();//自动以预编译
    QString cmd;
    const char *s = fileName.toStdString().data();
    cmd.sprintf("gcc -o %s.exe %s.c",s,s);
    system(cmd.toStdString().data());//先编译

    //如何删除那个临时文件呢
    cmd = fileName.replace("/","\\") + ".c";
    remove(cmd.toStdString().data());


    cmd = fileName + ".exe";
    system(cmd.toStdString().data());//再运行
}

void MainWindow::on_run()
{
    QString cmd;
    cmd = fileName + ".exe";
    system(cmd.toStdString().data());
}

```
### 添加浮动窗口
在`mainwindows.cpp`里修改
```css
MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
{
}
```
在这里面添加浮动窗口
```css
MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
{
	....#省略前面的代码
	connect(run_comp,SIGNAL(triggered()),this,SLOT(on_run()));//当文本内容发生变化时，触发on_changed函数
#在上一行代码后添加如下内容    
    //浮动窗口---------------------------------------------------------------------
    QDockWidget *dock = new QDockWidget(this);
    addDockWidget(Qt::RightDockWidgetArea, dock);

    //给浮动窗口添加控件
      QTreeView *treeView = new QTreeView(this);
      dock->setWidget(treeView);
}
```
### 给代码添加高亮
在文件上右键选择`add new...`
![图2](https://s1.ax1x.com/2020/04/22/JtQkkT.png)

![图3](https://s1.ax1x.com/2020/04/22/Jtl0M9.png)

将`class name`命名为`highlighter`
将`.pro`文件修改
```css
QT += widgets
requires(qtConfig(filedialog))

HEADERS         = highlighter.h \
                  mainwindow.h
SOURCES         = highlighter.cpp \
                  mainwindow.cpp \
                  main.cpp

# install
target.path = $$[QT_INSTALL_EXAMPLES]/widgets/richtext/syntaxhighlighter
INSTALLS += target


```
将`highlighter.h`的代码修改为
```css

#ifndef HIGHLIGHTER_H
#define HIGHLIGHTER_H

#include <QSyntaxHighlighter>
#include <QTextCharFormat>
#include <QRegularExpression>

QT_BEGIN_NAMESPACE
class QTextDocument;
QT_END_NAMESPACE


class Highlighter : public QSyntaxHighlighter
{
    Q_OBJECT

public:
    Highlighter(QTextDocument *parent = 0);

protected:
    void highlightBlock(const QString &text) override;

private:
    struct HighlightingRule
    {
        QRegularExpression pattern;
        QTextCharFormat format;
    };
    QVector<HighlightingRule> highlightingRules;

    QRegularExpression commentStartExpression;
    QRegularExpression commentEndExpression;

    QTextCharFormat keywordFormat;
    QTextCharFormat classFormat;
    QTextCharFormat singleLineCommentFormat;
    QTextCharFormat multiLineCommentFormat;
    QTextCharFormat quotationFormat;
    QTextCharFormat functionFormat;
};


#endif // HIGHLIGHTER_H

```
将`highlighter.cpp`里的代码修改为
```css

#include "highlighter.h"


Highlighter::Highlighter(QTextDocument *parent)
    : QSyntaxHighlighter(parent)
{
    HighlightingRule rule;

    keywordFormat.setForeground(Qt::darkBlue);
    keywordFormat.setFontWeight(QFont::Bold);
    QStringList keywordPatterns;
    keywordPatterns << "\\bchar\\b" << "\\bclass\\b" << "\\bconst\\b"
                    << "\\bdouble\\b" << "\\benum\\b" << "\\bexplicit\\b"
                    << "\\bfriend\\b" << "\\binline\\b" << "\\bint\\b"
                    << "\\blong\\b" << "\\bnamespace\\b" << "\\boperator\\b"
                    << "\\bprivate\\b" << "\\bprotected\\b" << "\\bpublic\\b"
                    << "\\bshort\\b" << "\\bsignals\\b" << "\\bsigned\\b"
                    << "\\bslots\\b" << "\\bstatic\\b" << "\\bstruct\\b"
                    << "\\btemplate\\b" << "\\btypedef\\b" << "\\btypename\\b"
                    << "\\bunion\\b" << "\\bunsigned\\b" << "\\bvirtual\\b"
                    << "\\bvoid\\b" << "\\bvolatile\\b" << "\\bbool\\b";
    foreach (const QString &pattern, keywordPatterns) {
        rule.pattern = QRegularExpression(pattern);
        rule.format = keywordFormat;
        highlightingRules.append(rule);

    }

    classFormat.setFontWeight(QFont::Bold);
    classFormat.setForeground(Qt::darkMagenta);
    rule.pattern = QRegularExpression("\\bQ[A-Za-z]+\\b");
    rule.format = classFormat;
    highlightingRules.append(rule);

    singleLineCommentFormat.setForeground(Qt::red);
    rule.pattern = QRegularExpression("//[^\n]*");
    rule.format = singleLineCommentFormat;
    highlightingRules.append(rule);

    multiLineCommentFormat.setForeground(Qt::red);

    quotationFormat.setForeground(Qt::darkGreen);
    rule.pattern = QRegularExpression("\".*\"");
    rule.format = quotationFormat;
    highlightingRules.append(rule);

    functionFormat.setFontItalic(true);
    functionFormat.setForeground(Qt::blue);
    rule.pattern = QRegularExpression("\\b[A-Za-z0-9_]+(?=\\()");
    rule.format = functionFormat;
    highlightingRules.append(rule);

    commentStartExpression = QRegularExpression("/\\*");
    commentEndExpression = QRegularExpression("\\*/");
}

void Highlighter::highlightBlock(const QString &text)
{
    foreach (const HighlightingRule &rule, highlightingRules) {
        QRegularExpressionMatchIterator matchIterator = rule.pattern.globalMatch(text);
        while (matchIterator.hasNext()) {
            QRegularExpressionMatch match = matchIterator.next();
            setFormat(match.capturedStart(), match.capturedLength(), rule.format);
        }
    }

    setCurrentBlockState(0);

    int startIndex = 0;
    if (previousBlockState() != 1)
        startIndex = text.indexOf(commentStartExpression);


    while (startIndex >= 0) {

        QRegularExpressionMatch match = commentEndExpression.match(text, startIndex);
        int endIndex = match.capturedStart();
        int commentLength = 0;
        if (endIndex == -1) {
            setCurrentBlockState(1);
            commentLength = text.length() - startIndex;
        } else {
            commentLength = endIndex - startIndex
                            + match.capturedLength();
        }
        setFormat(startIndex, commentLength, multiLineCommentFormat);
        startIndex = text.indexOf(commentStartExpression, startIndex + commentLength);
    }
}



```

在`mainwindow.h`里添加
```css
//添加include文件

#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include "highlighter.h"

#include <QMainWindow>

#include <QTextEdit>    //文本
#include <QMenu>        //加入菜单
#include <QMenuBar>     //加入菜单栏
#include <QAction>      //加入菜单栏
#include <QFileDialog>


```
找到`private:`添加
```css
    Highlighter *highlighter;
```
如图
![图4](https://s1.ax1x.com/2020/04/22/Jt3mcQ.png)

修改`mainwindow.cpp`
```css
 highlighter = new Highlighter(editor->document());

```
位置如图
![图5](https://s1.ax1x.com/2020/04/22/Jt8vsH.png)
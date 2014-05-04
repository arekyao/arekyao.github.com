---
layout: post
title: "Scraping JavaScript webpages with webkit"
date: 2014-02-05 07:51
comments: true
categories: Architecture
tags: [Spider, JavaScript, webkit, Qt, python]
---

1.Preface
-------

Nowerdays, there are more and more webpages rendered through javascript, tranditional spider such wget, curl is useless.

An alternative solution is webkit, the open source browser engine used most famously in Apple's Safari browser. Webkit has now been ported to the Qt framework and can be used through its Python bindings.

<!-- more -->

This solution has those advantages:

* browser, lots of pic,js etc, automaticlly done
* Python, my fav language


2.Environment 
-------

####a. webkit (QtWebkit)
It's ported to Qt, called [QtWebkit](http://qt-project.org/doc/qt-4.8/modules.html). Here is [Qt install](http://qt-project.org/doc/qt-4.8/install-x11.html)

* Download the achieve

* [Dependencies](http://qt-project.org/doc/qt-4.8/requirements-x11.html)


```bash
    sudo apt-get install bison flex libqt4-dev libqt4-opengl-dev libphonon-dev libicu-dev libsqlite3-dev libxext-dev libxrender-dev gperf libfontconfig1-dev libphonon-dev g++
```


```bash
    sudo apt-get build-dep qt4-qmake libqt4-webkit
```

* Compiling

```bash
./configure
make
make install
```

Pay attention to configure's log, webkit module should be "yes"

WebKit module .......... yes


####b. PyQt

[PyQt](http://www.riverbankcomputing.co.uk/software/pyqt/intro) is a set of Python v2 and v3 bindings for Digia's Qt application framework and runs on all platforms supported by Qt including Windows, MacOS/X and Linux. PyQt5 supports Qt v5. PyQt4 supports Qt v4 and will build against Qt v5. The bindings are implemented as a set of Python modules and contain over 620 classes.

ps: another option is [PySide](http://qt-project.org/wiki/PySide)

* Sip install

```
sudo apt-get install python-dev

sudo apt-get install libqt4-dev 

sudo apt-get install python-qt4

python configure.py

make 

make install

```

* PyQt4 install

```
python configure.py

make 

make install

```

ps1: you can see this in "/your/python/env/share/sip/PyQt4", if your sip works. 

```
phonon  QtCore  QtDeclarative  QtGui   QtMultimedia  QtOpenGL  QtScriptTools  QtSvg   QtWebKit  QtXmlPatterns
Qt      QtDBus  QtDesigner     QtHelp  QtNetwork     QtScript  QtSql          QtTest  QtXml
```

ps2: you can sess this in "env/lib/python2.7/site-packages/PyQt4",if your python package works.  

```

__init__.py   pyqtconfig.py  QtDeclarative.so  QtHelp.so        QtOpenGL.so       Qt.so     QtTest.so         QtXml.so
__init__.pyc  QtCore.so      QtDesigner.so     QtMultimedia.so  QtScript.so       QtSql.so  QtWebKit.so       uic
phonon.so     QtDBus.so      QtGui.so          QtNetwork.so     QtScriptTools.so  QtSvg.so  QtXmlPatterns.so

```

####c. Xvfb

If you access your sever by ssh, without Graphic Interface, xvfb is one of your choice.

The error log is:

"xxxxxx cannot connect to X server"

```
sudo apt-get xvfb

xvfb-run --server-args="-screen 0, 640x480x24" python xxxxx.py

```

####d. TTP and Chinese

Last, qtwebkit works, but maybe you have a new problem is that, chinese word in webkit is [][][][].

TrueType,ttp is the root of the problem, here is the way:

```
sudo apt-get update
sudo apt-get install ttf-arphic-ukai ttf-arphic-uming
sudo apt-get install ttf-wqy-zenhei 
sudo fc-cache -v
```


3.One Example
--------

Here is a simple class that renders a webpage (including executing any JavaScript) and then saves the final HTML to a file:

```python
import sys  
from PyQt4.QtGui import *  
from PyQt4.QtCore import *  
from PyQt4.QtWebKit import *  
  
class Render(QWebPage):  
  def __init__(self, url):  
    self.app = QApplication(sys.argv)  
    QWebPage.__init__(self)  
    self.loadFinished.connect(self._loadFinished)  
    self.mainFrame().load(QUrl(url))  
    self.app.exec_()  
  
  def _loadFinished(self, result):  
    self.frame = self.mainFrame()  
    self.app.quit()  
  
url = 'http://webscraping.com'  
r = Render(url)  
html = r.frame.toHtml()  
```


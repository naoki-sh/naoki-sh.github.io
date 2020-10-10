---
permalink: articles/python/threading
title: "python threading"
header:
  overlay_image: /assets/images/coding.jpg
  caption: "Photo credit: [**Pexels**](Pixabay.com)"
  overlay_filter: rgba(0, 0, 0, 0.4)
toc: true
toc_sticky: true
categories:
  - single
tags:
  - Memo
author_profile: false
---

<div class="row">

<div class="medium-12  columns" markdown="1">
  
# python threading
* pythonを使ったマルチスレッド処理の実装。
* 複数の処理を並列処理として記述することができる。

## シンプルな例
* ２つの関数を並列で実行
### ソースコード
{% highlight python linenos %}
#!/usr/bin/env python
import threading
import time

def thread1():
    print "thread1: start"
    time.sleep(2)
    print "thread1: end"

def thread2():
    print "thread2: start"
    time.sleep(1)
    print "thread2: end"

if __name__ == '__main__':
    t1 = threading.Thread(target=thread1)
    t2 = threading.Thread(target=thread2)

    t1.start()
    t2.start()
{% endhighlight %}

* thread1とthread2を順に起動。
* シーケンシャルでれば、thread1終了後thread2が実行されるが、マルチスレッドなので、同時に実行される。
* thread2はthread1よりも後に起動したが、sleep時間がthread1よりも短いため、早く終了する。

## ループ処理
* ２つの無限ループをマルチスレッドで実装する。
* ctrl+C が押された際に、すべての処理を終了する。
### ソースコード
{% highlight python linenos %}
#!/usr/bin/env python
import threading
import time
import signal

class ThreadingTest:
    def __init__(self):
        self.exit_signal = False
        signal.signal(signal.SIGINT, self.Handler)
        
        self.t1 = threading.Thread(target=self.thread1)
        self.t2 = threading.Thread(target=self.thread2)

        self.t1.setDaemon(True)
        self.t2.setDaemon(True)

    def Run(self):
        self.t1.start()
        self.t2.start()
        while not self.exit_signal:
            time.sleep(1)

    def Handler(self, signal, frame):
        self.exit_signal = True

    def thread1(self):
        while(True):
            print "thread1: working"
            time.sleep(1)

    def thread2(self):
        while(True):
            print "thread2: working"
            time.sleep(2)

if __name__ == '__main__':
    threading_test = ThreadingTest()
    threading_test.Run()
{% endhighlight %}
* メインスレッドでsignalのハンドラを設定しておく。
* ctrl+cが押されたら、メインスレッドの処理を終了する。
* thread1とthread2はデーモンに設定しておくことで。メインスレッドが終了すると自動的に終了する。
* signalのハンドラはメインスレッドでしか動作しないので注意（メインスレッドを終了するとctrl+cが効かなくなる）。

## Opencvでのマルチスレッド処理
* python-opencvでカメラ画像を取得して画像処理を施し、表示する処理をマルチスレッドによって実装
* サンプルとしてカメラ画像をグレースケール化して表示する処理を実装
* ctrl+cまたはqが押されたらすべての処理を終了

### ソースコード
{% highlight python linenos %}
#!/usr/bin/env python
import threading
import time
import cv2
import numpy as np
import signal

class OpencvThreading:
    def __init__(self):
        self.exit_signal = False
        signal.signal(signal.SIGINT, self.Handler)

        self.cap = cv2.VideoCapture(0)
        if not self.cap.isOpened():
            print "error: cannot open camera"
            sys.exit()
        
        self.process_rate = 10.0 # 10 Hz
        self.update_rate = 60.0 # 60 Hz

        self.update = threading.Thread(target=self.UpdateFrame)
        self.proc = threading.Thread(target=self.Processing)
        
        self.proc.setDaemon(True)

    def MouseCB(self, eventType, x, y, flags, userdata):
        if eventType == cv2.EVENT_LBUTTONDOWN:
            print "click"

    def UpdateFrame(self):
        self.frame = self.out = np.zeros((1,1,3), np.uint8)
        cv2.namedWindow("window", cv2.WINDOW_AUTOSIZE)
        cv2.imshow("window", self.out)
        cv2.setMouseCallback("window", self.MouseCB, None)
        while(True):
            ret, self.frame = self.cap.read()
            if not ret:
                print "error: cannot get frame"
                break
            cv2.imshow("window", self.out)
            key = cv2.waitKey(int(1000.0 * 1.0 / self.update_rate))
            if key & 0xFF == ord('q') or self.exit_signal:
                break

    def Processing(self):
        while(True):
            self.out = cv2.cvtColor(self.frame, cv2.COLOR_BGR2GRAY)
            time.sleep(1.0 / self.process_rate)

    def Run(self):
        self.update.start()
        self.proc.start()
        while(True):
            self.update.join(1)
            if not self.update.is_alive():
                break
    
    def Handler(self, signal, frame):
        self.exit_signal = True

if __name__ == '__main__':
    opencv_threading = OpencvThreading()
    opencv_threading.Run()
{% endhighlight %}

* スレッドを２つ（画像の読み込みと表示、グレースケール化）用意
* グレースケール化処理は、デーモンにすることで、他の処理が終了したら、同時に終了するように設定
* 画像の読み込みと表示は、デーモンにせず、waitKeyでqが押されるまたは、ctrl+cが押されたらbreakするように設定
* メインスレッド（スレッド処理の外）では、is_alive()を使って、画像の更新と読み込みのスレッドが終了しているか監視し、終了していたら、プログラムを終了する。

### reference
* [\[Python\] スレッドで実装する, Qiita](https://qiita.com/tchnkmr/items/b05f321fa315bbce4f77)
* [threading, Python The Standard Library](https://docs.python.org/ja/3/library/threading.html)
* [signal, Python The Standard Library](https://docs.python.org/ja/3/library/signal.html)
* [OpenCV 接続したカメラから動画を取得しよう (Python), WEB ARCH LABO](https://weblabo.oscasierra.net/python/opencv-videocapture-camera.html)

---
  
|[TOP](/) | <a href="javascript:history.back()">back</a> |

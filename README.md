# js多线程的简单操作
## * 多线程
所谓多线程，就是可以让javascript函数能够在多个进程中进行或者说是可以让部分代码在后台运行。从而让js的进程可以继续运行下去，大大减少了浏览器在运行复杂的JavaScript脚本时发生假死现象的概率。这便是多线程的作用，提高浏览器的使用效率。
## * Web workers
 Web workers 是一种可以让web应用程序具备后台处理能力的方法。利用web workers，
我们可以创建一个不影响前台处理的后台线程，并且可以在这个后台线程中创建多个子线程。而且web workers的使用步骤十分简单，可以快速上手。
## * Web workers的使用
###    *创建worker对象
    var worker=new Worker("worker.js");      /worker对象就是后台运行的脚本 <br>
###   *与 Web Workers 通信
要想与web workers通信，可以调用postmessage（）函数传入所需的数据，调用onmessage()函数接收数据，同时建立一个监听器，
用来监听由web workers发送到页面的消息。 <br>
    Documwent.getElementById(“helloButton”).onlick=function(){ <br>
    Worker.postMessage(“Hi,How are you”); <br>
    } <br>
当用户单击按钮后，相应信息会被发送给web workers，然后将事件监听器添加到页面中，用来监听从web workers发来的信息。 <br>
`事件监听器代码：` <br>
    worker.addEventListener(“message”,messageHandler,true);   /添加事件监听器<br>
    function messageHandler(e){ <br>
    //来自worker的过程信息 <br>
    } <br>
Workers 通过调用onmessage()函数接收页面数据后根据不同函数的调用对数据进行不同的处理，在通过调用PostMessage()函数将数据发送给主线程。
###   * 监听错误事件
如果在web workers脚本中发生未处理的错误，就会引发web workers对象的错误事件。特别是在调试用到web workers叫本事，对错误事件的监听就显得尤为重要 。
并且需要一个函数将错误记录在控制台上。
`错误处理函数代码：` <br>
    Function errorHandler(e){ <br>
    Console.log(e.message,e); <br>
    } <br>
`错误事件监听器：` <br>
    Worker.addEventListener(“error”,errorHandler,true); <br>
###   * 终止worker
Web workers不能自行终止，但能够被起用他们的页面所终止。而终止所要调用的函数是terminate(). <br>
`终止代码：` <br>
    worker.terminate(); <br>
## * 案例
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8" />
    <title></title>
    </head>
    <body>
        <input type="text" name="ipt" id="ipt" value="" />
        <button id="start">start</button>
        <button id="stop">stop</button>
        <button id="ale">alert</button>
        <script type="text/javascript">
            var ipt = document.getElementById("ipt");
            var stop = document.getElementById("stop");
            var start = document.getElementById("start");
            var ale = document.getElementById("ale");
            var worker = new Worker("js/test22.js");
            worker.onmessage = function(){
                ipt.value = event.data;
            };
            stop.addEventListener("click",function(){
                //用于关闭worker线程
                worker.terminate();
            });
            start.addEventListener("click",function(){
                //开起worker线程
                worker = new Worker("js/test22.js");
            });
            ale.addEventListener("click",function(){
                alert("i'm a dialog");
            });
        </script>
     </body>
    </html>
下面是test22.js里的代码，也就是存在于worker线程里的代码

     var i = 0;
    function mainFunc(){
        i++;
        //把i发送到浏览器的js引擎线程里
        postMessage(i);
    }
    var id = setInterval(mainFunc,1000);
运行起来我们会发现 <br>
 ![](https://raw.githubusercontent.com/suwhite/js-/8f747c7468a2d55a172ea2870ea221f500e70257/1.png) <br>
 <br>
点击确定后，它的数值并非2，而是一个比2更大的数 <br>
![](https://raw.githubusercontent.com/suwhite/js-/8f747c7468a2d55a172ea2870ea221f500e70257/2.png) <br>
<br>
虽然dialog的弹出会阻塞js引擎线程，但是并不影响worker线程的运行，所以，在我们点击确定后，只是在js引擎线程上更新了新的内容，而数值是一直在跑动的，这就说明worker线程和原本的js线程互不影响.

而主线程要想与web workers线程进行通信的话，那就要通过onPostMessage 和 onmessage这两个函数，其中onPostMessage（data）的参数是你要传递的数据，而onmessage是一个回调函数，只有在接受到数据时，onmessage会被回调，onmessage有一个隐藏的参数，那就是event，我们可以用event.data获取到传递过来的数据来更新主线程。

使用worker线程应注意的是，所有js里集成的对象都在js线程里，而并非worker线程。
例如我们在worker线程里写上：

var a = document.getElementById("a");
结果你会得到一条Error，告诉你找不到document,或者document is undefined。所以我们尽量把需要的东西都写到主线程里，而只把耗时的操作写到worker线程里。
## * 自我小结
java script是一个单线程的语言，它只能进行异步编程，而web workers的存在可以让js实现多线程的操作。将网址中数据计算的部分以及一些需要较长时间运行的脚本放置后台让web workers线程运行，再通过与web workers线程通信的方式来获取结果，这便是网页运行速度大大提高的一个重要原因，也是web workers的一大优点。

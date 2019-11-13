## Python音频相关工具包使用

这里面会包含一些关于音频合成，播放，可视化，频谱分析相关工具和代码的使用与学习说明。



### Pyaudio

pyaudio模块可以负责生成播放音频数据。

[这里]( http://people.csail.mit.edu/hubert/pyaudio/docs/#pasampleformat )是它的文档页面

按照文档说法，使用之前，我们首先需要生成一个PyAudio的实例，这个实例负责初始化音频系统，`pyaudio.PyAudio()`

无论是要记录还是播放音频，都需要获取一个音频stream，然后如果要播放音频可以向stream写入音频数据，也可以从stream中读取数据。

如何获取一个stream呢？使用`pyaudio.PyAudio.open()`方法

这个方法接收如下参数：

~~~python
__init__(PA_manager, rate, channels, format, input=False, output=False, input_device_index=None, output_device_index=None, frames_per_buffer=1024, start=True, input_host_api_specific_stream_info=None, output_host_api_specific_stream_info=None, stream_callback=None)
~~~

明显第一个参数相当于`self`是不用管的，其他参数：

~~~
rate: 采样率
channels:  channels数量
format: 采样格式，一会细说
input:  这是否是一个输入stream，默认False
output: 这是否是一个输出stream，默认False
~~~

一般而言指定一些这些参量就足够了。其余参量的意义可以去看文档

这里的format的要求是Portaudio Sample Formats常量，其中的常量的值包含了以下几种：

~~~python
paFloat32, paInt32, paInt24, paInt16, paInt8, paUInt8, paCustomFormat
~~~

自然这些常量就是一些int常数

所以format究竟如何指定呢？有这么一个函数`pyaudio.get_format_from_width(width, unsigned=True)`

这里面的width的期望值是`1,2,3,4`，分别代表了不同的字节数，如果使用`unsigned`的话，会得到如下结果：

~~~
1:   32  --->  paUInt8
2:   8   --->  paInt16
3:   4   --->  paInt24
4:   1   --->  paFloat32
~~~

如果使用`signed`的话，只有1不同，结果会变成`paInt8`

总之，这意味着什么呢？我认为这个参量对应的应该是音频数据数字化的级数，也就是数值精度，采样率决定了x轴的精度，format自然就决定了y轴精度

至于channels对应的是频道数量吗？左右声道之类的？这个不太懂



欧科，总之就是写入音频数据就欧科了，虽然音频数据的格式我还没懂





### Matplotlib

这里我期望使用Matplotlib以动画的形式可视化音频，我从一个叫做`Charles的皮卡丘`的公众号处获取到了一份具有这种功能的python代码，名字叫做`monitor.py`

下面
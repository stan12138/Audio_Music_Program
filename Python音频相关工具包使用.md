## Python音频相关工具包使用

这里面会包含一些关于音频合成，播放，可视化，频谱分析相关工具和代码的使用与学习说明。



### 如何生成指定频率的简谐信号

当我们想生成一个正弦信号的时候。毫无以为这是一个指定采样率的信号。

那么当我们想生成一个这样的信号的时候，我们都会给出什么参数呢？持续时间t，采样率fs，以及信号频率f，以及信号幅度A。

那么正弦信号的定义式是如下的：
$$
y = A\sin(2\pi ft)
$$
有了上述式子，我觉得你应该能写出对应代码了吧，很简单的。



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











### 快速傅里叶变换(FFT)

根据上述基本知识，应该可以知道传统乐器的乐音可以认为是谐波叠加产生的，那么如果我们想模拟钢琴的一个音，只需要知道它的频率成分，然后合成即可。

所以为了知道音的频率成分，我们需要傅里叶变换。

这里应用的算法是FFT。这里我想做的仅仅是知道就FFT而言，结果是什么，怎么使用。

毫无疑问，我们分析的信号肯定是离散数字信号。

来自Elegent Sicpy的[这一部分]( https://www.oreilly.com/library/view/elegant-scipy/9781491922927/ch04.html )是相当不错的参考资料

#### 基本知识

假设我们需要分析的信号采样频率是Fs，信号本身的频率是F，采样的点数一共有N个。

那么FFT的结果将是长度为N的复数数组，其中每个值代表一个频率的信号，每个值的模代表这个频率的分量的幅值。

具体的，其中每一个值代表的分量的频率为：$F_n = \frac{(n-1)F_s}{N}$，Fn=()





~~~python
y  #假设y已经是我们一个数字信号了

from scipy import fftpack

Y = fftpack.fft(y)    # 获取fft结果结果的长度和y一致

freqs = fftpack.fftfreq(len(y))*fs    #fs是我们的采样频率， 这个函数将会告诉我们fft变换结果里面的每一个位置对应什么样频率的信号

Volume = np.abs(Y)    #每个结果的幅值

~~~


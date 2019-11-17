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







### 音频信号

下述内容，大多数摘抄自[这个博客]( https://blog.csdn.net/weixin_30824277/article/details/95092351 )

音频信号的channel称之为声道。

常见的有

单声道(mono)， 

双声道(stereo)包含左右声道， 

2.1声道：双声道+低音声道

5.1声道：包含正面声道，左前，右前，左环绕，右环绕，低音声道。最早应用于早期电影院

7.1声道：5.1的基础上，将左右环绕拆分为左右环绕和左右后置声道，应用于BD和现代电影院。



采样率

8000Hz用于电话

11025  AM调幅广播

22050和24000  FM

32000Hz， 数码视频

44100    音频CD

47250    商用PCM录音机

48000    数字电视 DVD 电影 专业音频

50000    商用数字录音机

还有更高的，超过48000Hz对于人耳没有意义



采样位数

常用8bit 16bit 32bit  量化结果的编码可以用整型和浮点型两种存储，PCM常用整型，高精度常用浮点型







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







I am sorry。我的音乐合成似乎失败了

我在尝试生成指定频率，持续时长，采样率的正弦波，然后输出到pyaudio进行播放，但是听起来似乎失败了，因为我并没有听到频率逐渐升高的音频，如果和winsound的Beep对比，也可以明显地发现生成的频率对应不上，我现在还不知道到底是怎么了。

现在唯一成功的就是利用scipy将音频数据输出到wav文件中，这样得到的结果听起来似乎是可以的，代码如下：

~~~python
data = []
for i in range(40, 20000, 400) :
    sample_rate = 22050
    volume = 3
    t, wave = wave_generate(1, i, sample_rate, volume)
    data += list(wave)

data = np.array(data, dtype=np.float32)
wavfile.write("test.wav", 22050, data)
~~~

但是没能直接播放，让我感觉很不爽。



elegent scipy里面的例子我还没看懂，就直接生猛的找了一个叫做[freesound]( https://freesound.org/people/Jaz_the_MAN_2/sounds/316913/?page=2#comments )的网站下载了钢琴的单音，然后直接对这个音没有做任何多余的处理，读进来，平均左右声道，fft，画出频谱图，虽然看似的到了一些结果，但是频率的数值似乎不太对.......

我不想搞了，最近对什么都无法提起兴趣。丧到爆炸。
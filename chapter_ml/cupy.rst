Python での 高速計算。 NumPy 互換 GPU 計算ライブラリ cupy
====================================================

Cupy は、簡単に GPU計算を実装するためのライブラリです。
これまで紹介した数値計算ライブラリNumPy と同じAPIを提供しているため、
NumPyでプログラムを作っておいて、きちんと動くことを確認してから Cupy に変更する、
といった使い方が可能です。
なんと言ってもデバッグが簡単で、非常に気軽にGPU計算を試すことができます。
ここでは Cupy の使い方を簡単にご紹介します。


Cupy の特徴
----------------

Cupy は日本のベンチャー企業 Preferred Networks が主導して開発している
オープンソースライブラリです。
同企業が開発している深層学習ライブラリ Chainer で用いられています。

GPU計算には、例えば NVIDIA が提供するライブラリの CUDA [cuda]_ を呼び出して実行する必要があります。
しかしそのインターフェースは非常に低レベルで、なかなか素人が気軽に使えるものではありません。
さらに、もしそのインターフェースに習熟したとしても、
低レベルなインターフェースからバグのないアルゴリズムを組み立てることは非常に骨の折れるものとなります。

Cupy を用いることで、すごく気軽にGPU計算を行うことが可能になります。
PythonからGPU計算を行うライブラリは複数ありますが、Cupyの特徴はなんといっても、
NumPy と（ほとんど）同じAPIを提供する点です。
そのため、使い方の習得が容易で、デバッグも非常に簡単です。
NumPyで開発したプログラムをGPU用に移植することも簡単でしょう。


.. [cuda] https://developer.nvidia.com/cuda-downloads

Cupyのインストール
--------------------

Cupy をインストールするには、 NVIDIA 製GPUとそのドライバ、
さらにCUDAライブラリをインストールしておく必要があります。
これらのインストールについては
https://docs-cupy.chainer.org/en/stable/install.html
を参照してください。

以上が終了すると、

.. code-block:: bash

  pip install cupy

でインストールすることができます。


Cupyの使い方
-----------------

上で述べたように、CupyはNumPyと同じAPIで設計されているので、
使い方もほとんどNumPyのものと同じです。
以下では、ランダムに生成した行列の行列積を求めるプログラム例です。

.. code-block:: python

  import numpy as np
  A_cpu = np.random.randn(10000, 20000)
  B_cpu = np.random.randn(20000, 30000)
  # numpy を使ってCPUで行列積を計算する
  AB_cpu = np.dot(A_cpu, B_cpu)

この計算を、Cupyを用いてGPU上で行うには、以下のようにします。

.. code-block:: python

  import cupy as cp  # cupy は cp と略すのが一般的なようです

  # np.ndarray から GPU 上のメモリにデータを移動する
  A_gpu = cp.ndarray(A_cpu)
  B_gpu = cp.ndarray(B_cpu)

  # cupy を使って GPU で行列積を計算する
  AB_gpu = cp.dot(A_gpu, B_gpu)

  # メインメモリ上にデータを移動する
  AB_cpu2 = AB_gpu.get()  # AB_cpu2 は np.ndarray 型


A_gpu は ``cp.ndarray`` 型のオブジェクトであり、実体はGPUメモリ上に生成されています。
``cp.dot`` 関数で、GPUメモリ上に確保された配列の行列積をGPUを用いて計算します。
この関数の戻り値も ``cp.ndarray`` 型のオブジェクトであり、実体は同様にGPUメモリ上にあります。
``get`` メソッドを実行することで、この配列をCPUメモリ上に移すことができます。
この戻り値は ``np.ndarray`` 型となります。

その他にも、要素積やFFT、特異値分解など、NumPyの多くの操作が実装されています。
詳しくは、公式ページ [cupy]_ をご覧ください。

ここでは、非常にお手軽にGPU計算を行えるライブラリとしてCupyを紹介しました。
もちろん、GPUを使って高速化するにはGPU計算についての知識が必要です。
例えば、GPUは行列計算のような並列処理は得意でCPUより数10倍速く計算できることもしばしばですが、
条件分岐が重なるなど並列化が難しい処理の速度は非常に遅くなります。
また、メインメモリ・GPUメモリ間のデータの移動がボトルネックとなることが多いため、
そういった操作は最小限に留める必要があります。

とは言え、このようなGPU計算の長所・短所も、
色々なプログラムを実装しながら学んで行けるというのがこういったライブラリを使うメリットだと思います。

.. [cupy] https://cupy.chainer.org/

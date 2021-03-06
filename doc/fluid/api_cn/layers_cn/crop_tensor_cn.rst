.. _cn_api_fluid_layers_crop_tensor:

crop_tensor
-------------------------------

.. py:function:: paddle.fluid.layers.crop_tensor(x, shape=None, offsets=None, name=None)

:alias_main: paddle.crop_tensor
:alias: paddle.crop_tensor,paddle.tensor.crop_tensor,paddle.tensor.creation.crop_tensor
:old_api: paddle.fluid.layers.crop_tensor



根据偏移量（offsets）和形状（shape），裁剪输入（x）Tensor。

**示例**：

::

    * 示例1（输入为2-D Tensor）：

        输入：
            X.shape = [3, 5]
            X.data = [[0, 1, 2, 0, 0],
                      [0, 3, 4, 0, 0],
                      [0, 0, 0, 0, 0]]

        参数：
            shape = [2, 2]
            offsets = [0, 1]

        输出：
            Out.shape = [2, 2]
            Out.data = [[1, 2],
                        [3, 4]]

    * 示例2（输入为3-D Tensor）：

        输入：

            X.shape = [2, 3, 4]
            X.data =  [[[0, 1, 2, 3],
                        [0, 5, 6, 7],
                        [0, 0, 0, 0]],
                       [[0, 3, 4, 5],
                        [0, 6, 7, 8],
                        [0, 0, 0, 0]]]

        参数：
            shape = [2, 2, -1]
            offsets = [0, 0, 1]

        输出：
            Out.shape = [2, 2, 3]
            Out.data = [[[1, 2, 3],
                         [5, 6, 7]],
                        [[3, 4, 5],
                         [6, 7, 8]]]

参数:
  - **x** (Variable): 1-D到6-D Tensor，数据类型为float32、float64、int32或者int64。
  - **shape** (list|tuple|Variable) - 输出Tensor的形状，数据类型为int32。如果是列表或元组，则其长度必须与x的维度大小相同，如果是Variable，则其应该是1-D Tensor。当它是列表时，每一个元素可以是整数或者形状为[1]的Tensor。含有Variable的方式适用于每次迭代时需要改变输出形状的情况。
  - **offsets** (list|tuple|Variable，可选) - 每个维度上裁剪的偏移量，数据类型为int32。如果是列表或元组，则其长度必须与x的维度大小相同，如果是Variable，则其应是1-D Tensor。当它是列表时，每一个元素可以是整数或者形状为[1]的Variable。含有Variable的方式适用于每次迭代的偏移量（offset）都可能改变的情况。默认值：None，每个维度的偏移量为0。
  - **name** (str，可选) - 具体用法请参见 :ref:`api_guide_Name` ，一般无需设置，默认值为None。

返回: 裁剪后的Tensor，数据类型与输入（x）相同。

返回类型: Variable

抛出异常：
    - :code:`TypeError` - x 的数据类型应该是float32、float64、int32或者int64。
    - :code:`TypeError` - shape 应该是列表、元组或Variable。
    - :code:`TypeError` - shape 的数据类型应该是int32。
    - :code:`TypeError` - offsets 应该是列表、元组、Variable或None。
    - :code:`TypeError` - offsets 的数据类型应该是int32。
    - :code:`TypeError` - offsets 的元素应该大于等于0。

**代码示例**:

..  code-block:: python
    
    import paddle.fluid as fluid
    x = fluid.data(name="x", shape=[None, 3, 5], dtype="float32")
    # x.shape = [-1, 3, 5], where -1 indicates batch size, and it will get the exact value in runtime.

    # shape is a 1-D Tensor
    crop_shape = fluid.data(name="crop_shape", shape=[3], dtype="int32")
    crop0 = fluid.layers.crop_tensor(x, shape=crop_shape)
    # crop0.shape = [-1, -1, -1], it means crop0.shape[0] = x.shape[0] in runtime.

    # or shape is a list in which each element is a constant
    crop1 = fluid.layers.crop_tensor(x, shape=[-1, -1, 3], offsets=[0, 1, 0])
    # crop1.shape = [-1, 2, 3]

    # or shape is a list in which each element is a constant or Tensor
    y = fluid.data(name="y", shape=[3, 8, 8], dtype="float32")
    dim1 = fluid.layers.data(name="dim1", shape=[1], dtype="int32")
    crop2 = fluid.layers.crop_tensor(y, shape=[3, dim1, 4])
    # crop2.shape = [3, -1, 4]

    # offsets is a 1-D Tensor
    crop_offsets = fluid.data(name="crop_offsets", shape=[3], dtype="int32")
    crop3 = fluid.layers.crop_tensor(x, shape=[-1, 2, 3], offsets=crop_offsets)
    # crop3.shape = [-1, 2, 3]

    # offsets is a list in which each element is a constant or Tensor
    offsets_var =  fluid.data(name="offset", shape=[1], dtype="int32")
    crop4 = fluid.layers.crop_tensor(x, shape=[-1, 2, 3], offsets=[0, 1, offsets_var])
    # crop4.shape = [-1, 2, 3]


# TFLite Segmentation Python
This code snipset is heavily based on <b><a href="https://www.tensorflow.org/lite/examples/segmentation/overview">TensorFlow Lite Segmentation</a></b><br>
The segmentation model can be downloaded from above link.<br>
For the mask generation I looked into the <a href="https://github.com/tensorflow/examples/tree/master/lite/examples/image_segmentation/android">Android Segmentation Example</a><br>
Follow the <a href="https://github.com/joonb14/TFLite-Segmentation-Python/blob/main/DeepLabv3.ipynb">DeepLabv3.ipynb</a> to get information about how to use the TFLite model in your Python environment.<br>

### Details
The <b>lite-model_deeplabv3_1_metadata_2.tflite</b> file's input takes normalized 257x257x3 shape image. And the output is 257x257x21 where the 257x257 denotes the pixel location of image and the last 21 is equal to the labels that the TFLite model can classify.<br>
The order of the 21 classes are<br>
```python
labelsArrays = ["background", "aeroplane", "bicycle", "bird", "boat", "bottle", "bus",  "car", "cat", "chair", "cow", "dining table", "dog", "horse", "motorbike", "person", "potted plant", "sheep", "sofa", "train", "tv"]
```
For model inference, we need to load, resize, normalize the image.<br>
In my case for convenience used pillow library to load and just applied /255 for all values. <br>
Then if you follow the correct instruction provided by Google in <a href="https://www.tensorflow.org/lite/guide/inference#load_and_run_a_model_in_python">load_and_run_a_model_in_python</a>, you would get output in below shape<br>
<img src="https://user-images.githubusercontent.com/30307587/109275995-d0c5a100-7858-11eb-99a9-d370cb38f068.png" width=300px/><br>
Now we need to process this output to a mask like this to segment the class we want.<br>
<img src="https://user-images.githubusercontent.com/30307587/109276397-4e89ac80-7859-11eb-837e-c3258edf0a97.png"/><br>
For this process we need to compare the values in the output<br>
```python
mSegmentBits = np.zeros((257,257)).astype(int)
outputbitmap = np.zeros((257,257)).astype(int)
for y in range(257):
    for x in range(257):
        maxval = 0
        mSegmentBits[x][y]=0
        
        for c in range(21):
            value = output_data[0][y][x][c]
            if c == 0 or value > maxVal:
                maxVal = value
                mSegmentBits[y][x] = c
#         print(mSegmentBits[x][y])
        label = labelsArrays[mSegmentBits[x][y]]
#         print(label)
        if(mSegmentBits[y][x]==15):
            outputbitmap[y][x]=1
        else:
            outputbitmap[y][x]=0
```
In the above example, I wanted to segment person only and consider all others as background, I knew that the person's label is 15 in the TFLite model, that's why I used 15 in <br>
```python 
if(mSegmentBits[y][x]==15):
	outputbitmap[y][x]=1
```
I believe you can modify the rest of the code as you want by yourself.<br>
Thank you!<br>

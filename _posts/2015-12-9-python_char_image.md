---
layout: post
title: "Python字符画"
excerpt: "Final week's coming, wtf am I doing?!"
categories: articles
tags: [生活]
comments: true
share: true
---

Python字符画
----

闲来无事，用Python的PIL实现了一下字符画的功能。

思路就是，先把图像转为灰度，然后每个像素的值就是0-255，然后把0-255转到0-14的区间。
0是黑，255是白，所以对应之后，0是黑，14是白。
字符画的对应符号来自网络。

其中值得借鉴的是，比如说把100 * 75的图片的每个像素值，分成75个list，每个list中含100个数值，在100个数值后面加上'\n'就可以做到分行了。

{% highlight python %}
from PIL import Image
color = 'MNHQ$OC?7>!:-;.'

def scale_image(image, new_width=100):  

    (original_width, original_height) = image.size  
    aspect_ratio = original_height/float(original_width)  
    new_height = int(aspect_ratio * new_width)  
  
    new_image = image.resize((new_width, new_height))  
    return new_image  

def convert_to_gray(image):
    return image.convert('L')

def convert_to_chars(gray_image):

    pixel_image = list(gray_image.getdata())
    pixel_to_chars = [color[n / 18] for n in pixel_image]
    image_char = []
    width, height = gray_image.size
    
    for index in xrange(0, len(pixel_to_chars), width):
        image_char.append(pixel_to_chars[index : index + width])
    
    for n in image_char:
        n.append('\n')
    return image_char

def save_to_file(image_char, text_file_name):
    outfile = open('IMG.txt', 'w')
    for i in image_char:
        for n in i:
            outfile.write(n)
    outfile.close()

def run(image_file_name, text_file_name):
    image = Image.open(image_file_name)
    rescaled_image = scale_image(image)
    gray_image = convert_to_gray(rescaled_image)
    
    image_char = convert_to_chars(gray_image)
    save_to_file(image_char, text_file_name)

if __name__ == '__main__':
    run('IMG2.jpg', 'IMG.txt')

{% endhighlight %}

Good luck with studying! It's final week and I'm still thinking about Python? WTF am I doing?!


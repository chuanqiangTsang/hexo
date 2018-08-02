---
title: 如何获取上传图片的尺寸(宽高)
date: 2018-07-06 16:20:29
tags: [Javascript]
categories: Javascript
---
# 开始
我们知道，使用文件域上传文件时，我们可以获取到文件的大小(size)，但是无法获取到文件的宽高，假如上传时，有宽高限制，我们就需要用一些别的方法来获取到图片的宽高了。

当然， 有一些上传插件可以获取到，假如不使用上传插件，如何做到呢？

# 思路
事先准备好一个img标签用以暂时存放图片
当获取到文件域中的图片时，使用fileReader中的readAsDataURL方法，把文件按照dataurl方式来读取，并把生成的url赋值给事前准备好的img, 最后获取该图片的尺寸即可。

# 代码

文件域
 
    <input type="file" id="file" />

暂时存放图片的img标签

    <img id="img" src="" alt="">


上传事件

      $('#file').change(function(){
		var file = $('#file')[0].files;
		var fileReader = new FileReader();
		fileReader.readAsDataURL(file[0]);

		// 读取完成
		fileReader.onload = (e)=>{
			var url = e.currentTarget.result;
			$('#img').attr('src', url);
		}
	});  

<font style="color: red">此时就会将上传的图片在html中预览出来</font>

在获取预览图片的宽高即可

    var w = $('#img').width();
	var h = $('#img').height();
			
	alert('宽度是：' + w + '高度是：' + h)

# 结语
以上， 就可以获取到上传时图片的宽高了，如果上传的要求中有宽高尺寸的限制，就可以通过这个方法去进行判断了。

另外， 上传图片预览也可用此方法。
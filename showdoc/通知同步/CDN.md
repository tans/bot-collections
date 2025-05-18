**请求**

> 当file_type为1时， 可以上传图片(包括gif动图格式)

```json
 {
	"type":11115,
	"data":{
		"file_path":"c:\\Users\\evilbeast\\Pictures\\750x1250-1.jpg",
		"file_type":1
	}
}
```



**返回**

```json
{
	"data":{
		"error_code":0,  // 为0是上传成功 
		"file_aes_key":"39373437333363346461613761633761", // cdn文件密钥
		"file_id":"3052020102044b30490201000204283cabaa02030f424102049cd366b4020461b698dc040d3136333933353636333438383602010002030ec8700410059b34e084cf19a38a92a677177863d20201010201000400", // cdn文件id
		"file_key":"1639356634886", // file_key
		"file_md5":"059b34e084cf19a38a92a677177863d2", // 文件md5
		"file_path":"c:\\Users\\evilbeast\\Pictures\\750x1250-1.jpg",
		"file_size":968802,   // 图片大小
		"file_type":1,
		"image_height":1250,  // 图片高
		"image_width":750    // 图片宽
	},
	"type":11115
}
```




### AES加密算法（CBC模式）

AES是目前比较流行的对称加密算法，是一种分组密码算法，AES的分组长度为128比特（16字节），而密钥长度可以是128比特、192比特或256比特。
CBC模式（密码分组链接模式）是常用的一种分组密码的模式。

实现代码如下：
```go
import (
	"crypto/aes"
	"crypto/cipher"
	"fmt"
	"bytes"
)

//对明文进行填充
func Padding(plainText []byte,blockSize int) []byte{
	//计算要填充的长度
	n:= blockSize-len(plainText)%blockSize
	//对原来的明文填充n个n
	temp:=bytes.Repeat([]byte{byte(n)},n)
	plainText=append(plainText,temp...)
	return plainText
}
//对密文删除填充
func UnPadding(cipherText []byte) []byte{
	//取出密文最后一个字节end
	end:=cipherText[len(cipherText)-1]
	//删除填充
	cipherText=cipherText[:len(cipherText)-int(end)]
	return cipherText
}
//AEC加密（CBC模式）
func AES_CBC_Encrypt(plainText []byte,key []byte) []byte{
	//指定加密算法，返回一个AES算法的Block接口对象
	block,err:=aes.NewCipher(key)
	if err!=nil{
		panic(err)
	}
	//进行填充
	plainText=Padding(plainText,block.BlockSize())
	//指定初始向量vi,长度和block的块尺寸一致
	iv:=[]byte("12345678abcdefgh")
	//指定分组模式，返回一个BlockMode接口对象
	blockMode:=cipher.NewCBCEncrypter(block,iv)
	//加密连续数据库
	cipherText:=make([]byte,len(plainText))
	blockMode.CryptBlocks(cipherText,plainText)
	//返回密文
	return cipherText
}
//AEC解密（CBC模式）
func AES_CBC_Decrypt(cipherText []byte,key []byte) []byte{
	//指定解密算法，返回一个AES算法的Block接口对象
	block,err:=aes.NewCipher(key)
	if err!=nil{
		panic(err)
	}
	//指定初始化向量IV,和加密的一致
	iv:=[]byte("12345678abcdefgh")
	//指定分组模式，返回一个BlockMode接口对象
	blockMode:=cipher.NewCBCDecrypter(block,iv)
	//解密
	plainText:=make([]byte,len(cipherText))
	blockMode.CryptBlocks(plainText,cipherText)
	//删除填充
	plainText=UnPadding(plainText)
	return plainText
}
```

测试代码如下：

```go
func main(){
	message:=[]byte("Hello!My name is X.")
	//指定密钥h
	key:=[]byte("abcdefgh98765432")
	//加密
	cipherText:=AES_CBC_Encrypt(message,key)
	fmt.Println("加密后为：",string(cipherText))
	//解密
	plainText:=AES_CBC_Decrypt(cipherText,key)
	fmt.Println("解密后为：",string(plainText))
}
```
测试结果如下：


---
title: C#网络流读取(图片）
date: 2019-3-5 13:34:04 
tags: C#、流、图片
---
# C#网络流读取(图片）

```C#
string url = "....";
            Stream stream = WebRequest.Create(url).GetResponse().GetResponseStream();
            const int bufferLen = 512;
            byte[] buffer = new byte[bufferLen];//byte缓存区
            int count = 0; int offset = 0;
            while ((count = stream.Read(buffer, offset, buffer.Length - offset)) > 0) //读取固定长度的流，直到读取完毕
            {
                offset += count;
                if (offset == buffer.Length) //检查是否读取完了，长度想等说明还有内容
                {
                    int nextByte = stream.ReadByte(); //读取下一个字节
                    if (nextByte == -1)
                        break;
                    byte[] newbyte= new byte[buffer.Length * 2]; //还有内容扩大缓存区大小
                    Array.Copy(buffer, newbyte, buffer.Length);
                    newbyte[offset] = (byte)nextByte;
                    buffer = newbyte; //从新指向
                    offset++;
                }
            }
            byte[] bytes= new byte[offset]; //读取完了 就能获得具体长度，再获取长度内的流
            Array.Copy(buffer, bytes, offset);
            return bytes;
```

网络流无法确定具体长度，一般通过缓存区来一步一步读取
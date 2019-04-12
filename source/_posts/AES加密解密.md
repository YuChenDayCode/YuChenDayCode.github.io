---
title: C# AES加密解密
date: 2019-2-28 9:27
tags: C#、加密
---
# C# AES加密解密

```c#
		/// <summary>
        ///  AES 加密
        /// </summary>
        /// <param name="str">明文（待加密）</param>
        /// <param name="key">密文</param>
        /// <returns></returns>
        public static string AesEncrypt(string str, string key)
        {
            if (string.IsNullOrEmpty(str)) return null;
            if (key.Length != 16) return null;
            Byte[] toEncryptArray = Encoding.UTF8.GetBytes(str);
            RijndaelManaged rm = new RijndaelManaged
            {
                Key = Encoding.UTF8.GetBytes(key),
                Mode = CipherMode.ECB,
                Padding = PaddingMode.PKCS7
            };
            ICryptoTransform cTransform = rm.CreateEncryptor();
            Byte[] resultArray = cTransform.TransformFinalBlock(toEncryptArray, 				0, toEncryptArray.Length);
            return Convert.ToBase64String(resultArray, 0, resultArray.Length);
        }
        /// <summary>
        ///  AES 解密
        /// </summary>
        /// <param name="str">明文（待解密）</param>
        /// <param name="key">密文</param>
        /// <returns></returns>
        public static string AesDecrypt(string str, string key)
        {
            if (string.IsNullOrEmpty(str)) return null;
            // 判断Key是否为16位
            if (key.Length != 16) return null;
            Byte[] toEncryptArray = Convert.FromBase64String(str);
            RijndaelManaged rm = new RijndaelManaged
            {
                Key = Encoding.UTF8.GetBytes(key),
                Mode = CipherMode.ECB,
                Padding = PaddingMode.PKCS7
            };
            ICryptoTransform cTransform = rm.CreateDecryptor();
            Byte[] resultArray = cTransform.TransformFinalBlock(toEncryptArray, 				0, toEncryptArray.Length);
            return Encoding.UTF8.GetString(resultArray);
        }
```

- 问题：
  - 1.DES和AES加密：指定键的大小对于此算法无效 
  - 2.Base-64 字符数组或字符串的长度无效

​             输入的不是有效的 Base-64 字符串，因为它包含非 Base-64 字符、两个以上的填充字符，或者填充字符间包含非法字符

- 解决：
  - 1.key的值必须为16+位
  - 2.注意base64字符串中的特殊字符是否被转义 或者 有没有被替换为空格

​      
# openssl编程要点
## 易错点
新的`openssl`编程已经不允许直接使用`EVP_CIPHER_CTX`，而是需要先初始化。错误示例如下所示:
```
EVP_CIPHER_CTX ctx; 
EVP_CIPHER_CTX_init(&ctx); 
ret=EVP_EncryptInit_ex(&ctx,cipher,NULL,key,iv); 
EVP_EncryptUpdate(&ctx,out+len,&outl,in,inl); 
EVP_EncryptFinal_ex(&ctx,out+len,&outl); 
EVP_CIPHER_CTX_cleanup(&ctx); 
```
正确示例如下
```
EVP_CIPHER_CTX *ctx;
ctx = EVP_CIPHER_CTX_new();
EVP_CIPHER_CTX_init(ctx);
EVP_EncryptInit_ex(ctx, cipher, NULL,
                                 (const unsigned char *)MYAES_KEY, NULL);
EVP_EncryptUpdate(ctx, outstring + len, &outl, instring, lentest);
EVP_EncryptFinal_ex(ctx, (unsigned char *)outstring + len, &outl);
EVP_CIPHER_CTX_cleanup(ctx);
```
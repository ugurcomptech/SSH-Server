# SSH Server

## SSH Server Nedir?

SSH sunucusu, uzaktan erişim için kullanılan bir sunucu yazılımıdır. SSH (Secure Shell), ağ üzerinde güvenli iletişim sağlamak için kullanılan bir protokoldür. Bir SSH sunucusu, kullanıcıların SSH istemcileri aracılığıyla sunucuya güvenli bir şekilde bağlanmasını sağlar. Bu bağlantılar, kullanıcı kimlik doğrulaması ve şifreleme sağlayarak güvenli bir şekilde gerçekleştirilir. SSH sunucuları, uzaktan sunucuları yönetmek, dosya transferleri yapmak ve diğer ağ hizmetlerine erişmek gibi çeşitli görevler için kullanılır. 


## Key İle Güvenli Bağlantı

İlk önce herhangi bir sunucumu açıyoruz. Ben Web serverımda yapacağım bu işlemleri. Aşağıdaki kodu yazıyoruz.

```
ssh-keygen -t rsa -b 4096
```

Burada kullanıcımız için bir tane ssh key oluşturuyoruz. Önemli olan nokta ise bu keylerin kullanıcı bazı oluşmasıdır. Eğer bu Keyi root ile oluşturursanız sadece root ile bağlanabilirsiniz. Ben sudo yetkilerine sahip olmayan `masterweb` userımla oluşturuyorum.


![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/3609aea6-f618-4d7c-8693-419dec6be7f2)

Enterladıktan sonra bize keyin kaydedileceği dosya yolunu söylüyor eğer değiştirmek istemiyorsak `enter` deyip geçiyoruz. Bir sonraki aşamalarda daha güvenli SSH bağlantısı istiyorsak bu dosya yolunu değiştireceğiz. Geçtikten sonra bizden bir tane parola istiyor bunun sebebi şudur; anahtarlar iki tane oluşturulur bunlardan birisi `Private` diğeri ise `Public`'tir. Public olanı serverlara bağlanmak için kullanırız. Private olanı da Publici kullanabilmek için kullanırız. Bunların ikisi birbirine bağıntılıdır. Burada private anahtarının parolası olsun mu diye soruyor. Biz şuanlık parolasız oluşturacağız. 

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/2b13d3cc-c538-4a46-8583-a72de78bb72f)

Başarılı bir şekilde oluşturduk.

Şimdi oluşturduğumuz keylerin nerede olduğunu görelim. Kullanıcının home dizinindeki gizli `ssh` klasöründedir. 

```
ls -l ~/.ssh/
```

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/243e4a75-8249-4a6c-922b-cb2da776d4d6)


Tab tuşuna basınca iki dosyayı da görebiliyoruz. `.pub` ile biten public  diğeri de private olandır. Uzağa gidebilmek için `id_rsa.pub` dosyasını kullanacağız. `id_rsa.pub` kullanabilmek için `id_rsa`'yı kullanacağız.

`id_rsa.pub` dosyamızı açıyoruz.


![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/52557637-1d36-47bd-adf1-601c99f1b27b)


Gördüğünüz gibi baya bi uzun `end` tuşuna basarak sonuna gidebilirsiniz.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/6fd9bc06-6443-4292-9f07-1428509ef707)


Şimdi bunu SSH serverımıza tanıtacağız.

```
ssh-copy-id root@172.16.1.5
```

Bunu yazdıktan sonra enterlıyoruz.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/dcd4d0aa-14c1-43d4-857c-c8f8521bd4b1)

İlk defa bağlandığımız için fingerprint çıkıyor. Yes deyip ilerliyoruz. SSH serverımızın root şifresini soruyor. Girdikten sonra keyin eklendiğini görebiliyoruz.


Şimdi SSH serverımızda bunu kontrol edelim.

Root kullanıcımıza tanıttığımız için root kullanıcısın home klasöründe authorized_keys dosyasını kontrol edeceğiz.

```
nano ~/.ssh/authorized_keys
```

Dosyamızı açınca ssh-keyi görebiliyoruz.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/7941d146-7d04-477e-b175-af70882729b0)

Şimdi SSH serverımıza bağlanmayı deneyelim:

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/7d3c1c70-3313-4984-b9f6-334073a3b55f)

Gördüğünüz gibi şifre vs. istemedi.


Tabi bu güvenli olmayan bir yöntem saldırganlar için bazı açıklar vermiş oluyoruz. Bu açıkları kapatmak için daha güvenli hale getireceğiz.

## Güvenlik Güçlendirmesi

Salgırdanlar bu keyleri ararken default dosya yollarına bakarlar eğer bulamazlarsa `id_rsa` gibi aratmalar yaparak keyleri bulmaya çalışırlar. Biz bunun önüne geçmek için bazı güvenlik önlemleri alacağız.

İlk önce bir tane klasör oluşturacağız. `mkdir ~/.keys` yazarak gizli bir klasör oluşturuyoruz. Ben örnek olsun diye böyle basit bir isim verdim siz istediğinizi verebilirsiniz. Şimdi ssh-key oluşturalım. Önceden `ssh-keygen -t rsa -b 4096` yazıyorduk fakat bu sefer rsa şifreleme yöntemini kullanmayacağız. 

Tab tuşuna bastığımızda diğer şifreleme yöntemlerini görebiliyoruz. Biz burada en modern olan `ed25519` olanı kullanacağız. ed25519, RSA kadar uyumlu değildir. Gideceğimiz serverın bunu desteklemesi gerekmektedir. OpenSSH version 6 dan sonrasıdır. SSH serverımıza gidip `ssh -V` yazarak sürümünüzü görebilirsiniz.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/241513ba-d071-42ab-9f04-78d5d13e6a6f)


Keyimizi oluşturalım. Bizi varsayılan dizinin altına götürmeye çalışıyor. Biz buranın yerini değiştirip `/home/master/.keys/key` olarak belirtiyoruz.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/0420e8c1-f2d5-4be5-88de-21da51f3a3d5)


Dosya yolunu belirttikten sonra bizden bir parola istiyor. Private keyin hangi parolayla korunacağını soruyor. Burada unutulmaması gereken şey bu parolayı servera her bağlandığınızda girmeniz gerektiğidir. O yüzden fazla uzun şifre koymamanız önerilir.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/58e38d2c-1b70-4e75-a047-4c71290693b3)




![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/9827a649-ee55-400b-ab63-368c5768c332)

Başarılı şekilde oluşturduk.


Şimdi ssh-copy-id yapmaya çalışalım.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/802bc7e7-6aca-4575-a0f4-1755ad2afff8)

Hata aldık. Bunun sebebi varsayılan dizinin altında aramasıdır. `-i` parametresi ile bunu tanımlayacağız.


```
ssh-copy-id -i ~/.keys/key.pub root@172.16.1.5
```

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/dccbb90c-c489-4a5c-915f-aa90cc3384ad)


Başarılı bir şekilde keyi ekledik.

SSH serverımızda gelip kontrol ediyoruz.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/9b655ad5-fe57-4d1e-9830-6058284904ab)

**Not:** authorized_keys dosyasına elle de ilgili keyleri girebilirsiniz. Saldırganlar da gelip ssh-keygeni çalıştırıp keyi buraya yazabilir. Bu yüzden dosyanın yazılabilir olmaması gerekir. Root ele geçirilirse çalıştırabilir mi derseniz sormaya bile gerek yok root ele geçirildiyse o sistem artık sizin değildir.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/cb325552-8af1-43da-972c-90089c428831)


SSH serverımıza bağlanalım.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/cb2f37ac-3306-4284-b523-8ca63b4fc324)

Fark ettiyseniz hala şifre istiyor bunun sebebi varsayılan dizinin altında aramasıdır. Bağlantı yaparken private anahtarı da kullanmamız gerekmektedir.

![image](https://github.com/ugurcomptech/SSH-Server/assets/133202238/b60f8231-0231-4ebc-9059-41ef5ec88802)

Sarı ile çizdiğim yerde ssh-keyi oluştururken belirlediğimiz parolayı istiyor. Parolayı girdikten sonra başarılı bir şekilde bağlantı kurabilirdik.



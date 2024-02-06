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


Tab tuşuna basınca iki dosyası da görebiliyoruz. `.pub` ile biten public  diğeri de private olandır. Uzağa gidebilmek için `id_rsa.pub` dosyasını kullanacağız. `id_rsa.pub` kullanabilmek için `id_rsa`'yı kullanacağız.

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











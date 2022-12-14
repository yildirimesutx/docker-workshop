# Docker

- Docker CLI (Command Line Interface)

`docker version` komutu ile client ve server Docker engine kontrol ediliyor, bu komutu powershell(windowsda, mac de terminal) ile calistirdik, asagıda belirtilen ekran gelmeli

```
Client:
 Cloud integration: v1.0.28
 Version:           20.10.17
 API version:       1.41
 Go version:        go1.17.11
 Git commit:        100c701
 Built:             Mon Jun  6 23:09:02 2022
 OS/Arch:           windows/amd64
 Context:           default
 Experimental:      true

Server: Docker Desktop 4.11.1 (84025)
 Engine:
  Version:          20.10.17
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.11
  Git commit:       a89b842
  Built:            Mon Jun  6 23:01:23 2022
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.6
  GitCommit:        10c12954828e7c7c9b6e0ea9b0c02b01407d3ae1
 runc:
  Version:          1.1.2
  GitCommit:        v1.1.2-0-ga916309
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

client o isletim sistemini baz alarak kuruluyor, server ise linux isletim sistemli sanal makine kurularak deamen buraya kuruluyor.

client win da deamon ise linuxda calisiyor.

`docker info` komutu ile de sistemde docker ile ilgili temel bilgilere ulaşıyoruz. 


`docker` komutu ile de docker komutlarını gorebiliriz.

- bu komutlar 3 ana baslik altında toplanmistir. Options, Managment Commands, Commands 

- 2017 yilinda itibaren <docker "yönetilmek istenilen bolum" "komut"> seklinde komutlari yaziyoruz  

örnek => `docker container run hello-word` 

örnek => `docker image --help` yazarsak image ile ilgili yazabilcegimiz komutlar gelmektedir.

---


# 3.Bolum Container 101

**Container Temelleri-1**


`docker container create` ile container oluşturuluyor, `start` ile baslatılıyor,
bu islemleri tek seferde yapabilmek icin `docker container run` kullanılıyor

- komut ile cagrilan image localde yoksa docker hub tan cagrilir.

`docker container ls -a` komutu ile tüm calisan kapatilmis containerları gorüyoruz.


- Her container imajında, o imajdan bir container yarattıgımiz zaman varsayılan olarak calısması icin ayarlanmıs bir uygulama vardır.

- bu uygulama calıştigi sürece container ayakta kalır

- uygulama calismayi biraktiginda container da kapatilir.

**Container Temelleri-2**

 soru-1: Docker imajlarında sadece tek bir uygulama mı vardır?

 Hayır. Docker imajında birden fazla uygulama olabilir. Tek bir uygulama içermesi kural değildir.

 soru-2: Docker, imajlarından container yaratıldığında çalışması için birden fazla uygulama atanabilir mi?

 Hayır. Docker container başlatıldığı zaman otomatik çalışması için tek bir uygulamanın ayarlanmasına izin verir.

soru-3 : Docker container içerisinde sadece tek bir uygulama mı çalıştırılabilir?

Hayır. Docker container başlatıldığı zaman otomatik çalışması için tek bir uygulamanın ayarlanmasına izin verir fakat container içerisinde daha sonra bu uygulamanın yanında başka uygulamaklar da çalıştırılabilir.

soru- 4: Docker image inda varsayılan olarak çalıştırılması için ayarlanan uygulama terine başka uygulama ile container başlatabiliyor muyuz?

Evet. Container yaratılırken hangi uygulamayı çalıştırılmasını belirtebiliriz.



```
docker container run --name deneme -d -p 80:80 ozgurozturknet/adanzyedocker
docker container run -p 80:80 dizin/imagename  
# 80:80 potunu acıp ilgili image baslattı container oluştu
# containerlar için uniq id üretilir

docker container ls
#bize calisan containerları gösterir

`docker container run --name denemecon dizin/imagename java app1`
# bu komut ile containeri default isim yerine kendi yazdigimiz ismi verdik, ayrıca baslatilan uygulama yerine son kısımda yazdığımız uygulayı baslattik.

docker container run -d -p 80:80 dizin/imagename
# bu komut ile arka planda container calisiyor, bizim shell mizi baglamadi.

docker container ls -a => docker ps -a
#calisan calismayan tüm containerlari gösterir

docker container rm idnumarasi
# containeri siler, calisan containeri silemez, fakat rm -f idnumra ile zorla silme yapabiliyoruz.

docker container stop f43
# calisan containeri durdurur. sondaki uc karakter id numarası basi

```


**Container Temelleri-3**

```
docker container run --name websunucu -p 80:80 -d ozgurozturknet/adanzyedocker

 docker container exec -it websunucu sh
# containeri bağlanma komutu, websunucu bizim proje ismi 

 ls -l  içindeki uygulamaları gördük ve isimlerini yazarak uygulamayı calıştirdik

 container a baglantı kurulduktandan sonra ps ile container içinde yüklü  appleri görüntüledik


docker container run --name websunucu2 -p 80:80 -d ozgurozturknet/adanzyedocker 
# bir containerdan aynı image kullanarak milyonlarca container oluşturulabiliyor

```


docker container prune # calısmayan tüm containerları siliyor
docker image ls # image gösteriyor
docker image prune -a # image siliyor
docker image pull alpine #

**Docker Katmanlı Dosya Yapısı**

- Docker Union File System (birlesik dosya sistemi)

<img src="notes/katman.png">

- aynı image ile birçok container uretilebilir. Yapilan bu degisikler ayri bir katman olarak tutulur. Bu degisiklikten diger containerlar etkilenmez.

- ayri katmanlardan olusan container ls komutu ile tek bir dosya yapısında gibi görünür. Bu yapıya Union file system denir.

- Copy on Write

   - container içinde yapilan degisiklik image file icindeyse bu degisiklik yazılabilir katmana R/W Layer tasinir. 

<img src="notes/copy_on_write.png">


- Not : İmage katmanli yapisi mevcut, docker aynı katmanlı olanları tek bir sefer hafizaya aliyor.

**Docker Container Yasam Suresi**

- Container bir sorun yasandiginda silinip yeniden kurulabilme ozelligi mevcuttur.Fiziksel makineler gibi uzaktan ulasip sorunu cozmuyoruz. direk siliyoruz :)

<img src="notes/container_yasalari.png">
- Container i tek kullanımlık olarak dusunebiliriz.Eger proje icinde tutulan kayitlar/log ve db benzeri bir yapı varsa bu container icinde tutulmaz. Bunları ayri bir yerde yutuyoruz. Container disinda tutarak erisebilir ve paylasilabilir olarak tutarız.

- Bu ayrı olarak tutulan yapıya Volume diyoruz. 

<img src="notes/container_volume.png">


**Docker Volume, Container Dısı veri saklama**

- `docker volume create ilkvolume` ile ilkvolume olarak ilk volumumuzu olusturduk.

- `docker volume inspect ilkvolume` inspect komutu ile inceliyoruz.

- `docker container run -it -v ilkvolume:/deneme2 centos sh`

<img src="notes/inspect_volume.png">

- yukaridaki resimde bulunan containeri sildik `docker container rm 074`

- asagidaki gibi volume baska bir containera bağladık

<img src="notes/volume_deneme.png">


- Not : bir volume birden çok container tarafından kullanılabilir. Baska bir container içinde yapılan değişiklik diğer container içindeki volumu etkiler,

   - `docker container run -it -v ilkvolume:/deneme3:ro centos sh` bu komut ile read only olarak oluşturulabilir yani container içinden volume yazma yapılamaz


**Docker Volume, Bos-dolu Volume davranışı**

<img src="notes/volume_mount.png">


`docker container run --rm -it -v deneme1:/xyz alpine sh` oluşturulan containera volume ekliyoruz rm komu ile container kapatıldığında silinmesini saglar.


- volume yeni klasor oluşturularak mount edilir

- volume var olan bir klasore mount edilir;
   - eger volume ici bos ise olusturulan klsorun ici dolu ise bu dolu olan dosyalar volume icinde de olur
   - eger volume ici dolu ve olusturulan klasorun ici dolu ise mount isleminden sonra o klasorde volume icindekiler gozukur

**Bind Mounts**

<img src="notes/bind_mount.png">

 Volume'un bind mount'tan farkı volume için bu klasörün yolunu sizin manuel göstermenize gerek olmaması.

 Volume yaratığınızda otomatik olarak yaratılıyor ve siz path yerine volume adı kullanarak daha kolay işlerinizi hallediyorsunuz. 
 Bunun dışında diğer fark da şu. 
 
 Volume illa local olarak kullandığınız bir şey değil. Çeşitli volume driverları kullanarak local dışında da örneğin bir storage box üstünde volume yaratabiliyorsunuz.

 - bind mount sadece local development da kullanılıyor, production ortamında kullanılmıyor.
 
- docker engine yüklü olan bir sistemden bir dosyayı bir docker containerina mount edilmesine bind mount denir.

`docker container run -d -p 80:80 --name ilkweb nginx`
- bu komut ile `nginx` web sunucusu kurduk. 
- `docker exec -it 7de sh` 7de container id ilk 3 harfi, container içerisine girmek için kullandık 
- /usr/share/nginx/html ile default gelen http://127.0.0.1/ html sayfasına ulaştık
- `docker rm -f 7de` zorla silme komutu

- `docker container run -d -p 80:80 -v C:\Users\yildi\Desktop\Docker\AdanZyeDocker\kisim3\bolum28\websitesi:/usr/share/nginx/html nginx` bu komut ile localdeki proje dosyamızı nginx image web server ne mount ettik

- mount edildikten sonra artık localdeki proje dosyamızda yaptığımız değişiklişler web server üzerinden görüntülenmemektedir.

**3.Bolum Sonu Alistirma**

- kisim3\bolum29\alistirma.txt

- 1- image/volume/container sergileme ve silme islemi (https://docs.docker.com/)
   
   - `docker image ls`
   - `docker container ls`
   - `docker volume ls`

   - `docker image rm 'image_name'` ek olar force komutu eklenip zorla silinir `docker image rm -f 'image_name'`
   - `docker container rm 'container_name'`
   - `docker volume rm 'volume_name'`
       
- 2- image calistigimiz sisteme cekilmesi

  - `docker image pull 'image_name'`

- 3- container olusturulmasi

   - `docker container create 'image'`

- 4- container olusturulmasi detached

     - `docker container run -d 'image'`

- 5- containerin loglarına bakma
   - `docker container logs 'container'`       

- 6- container durdurma ve tekrar başlatma ve kaldırma 

    - `docker container stop 'container'`
    - `docker container start 'container'`
    - `docker container rm -f 'container'`

- 7- detached ve '-p 80:80' portuna  publish edilmiş container olusturalim. Browserda görelim

   - `docker container run -d -p 80:80 'image'`

- 8- websunucu adlı bu container’ın içerisine bağlanalım. /usr/local/apache2/htdocs klasörünün altına geçelim ve echo “denemedir” >> index.html komutuyla buradaki dosyaya denemedir yazısını ekleyelim. Web tarayıcıya geçerek dosyaya ekleme yapabildiğimizi görmek için refresh edelim. Sonrasında container içerisinden exit ile çıkalım.

   - `docker container run -d -p 80:80 --name ilkweb nginx`
   - `docker container exec -it 773 sh`
   - `cd /usr/share/nginx/html`
   - `echo "denemedir" >> index.html`
   - `cat index.html`



- 9- websunucu isimli container’ı çalışırken silelim.

    - `docker container rm -f 773`


- 10 alpine isimli imajdan bir container yaratalım. Ama varsayılan olarak çalışması gereken uygulama yerine “ls” uygulamasının çalışmasını sağlayalım.
    - `docker container run alpine ls`

- 11  “alistirma1” isimli bir volüme yaratalım.
    - `docker volume create alistirma1`

- 12  alpine isimli imajdan “birinci” isimli bir container yaratalım. Bu container’ı interactive modda yaratalım ve bağlanabilelim. Aynı zamanda “alistirma1” isimli volume’u bu containerın “/test” isimli folder’ına mount edelim. Bu folder içerisine geçelim ve “touch abc.txt” komutuyla bir dosya yaratalım daha sonra “echo deneme >> abc.txt” komutuyla bu dosyanın içerisine yazı ekleyelim. 
    - `docker container run -i --name birinci alpine sh` 
    - `docker container run -it -v alistirma1:/test alpine sh`
    - `touch abc.txt`

- 13  alpine isimli imajdan “ikinci” isimli bir container yaratalım. Bu container’ı interactive modda yaratalım ve bağlanabilelim. Aynı zamanda “alistirma1” isimli volume’u bu containerın “/test” isimli folder’ına mount edelim. Bu folder içerisinde “ls” komutyla dosyaları listeleyelim ve abc.txt dosyası olduğunu görelim. “cat abc.txt” ile dosyanın içeriğini kontrol edelim.
    - `docker container run -i --name ikinci alpine sh` 
    -  `docker container run -it -v alistirma1:/test alpine sh` 

- 14  alpine isimli imajdan “ucuncu” isimli bir container yaratalım. Bu container’ı interactive modda yaratalım ve bağlanabilelim. Aynı zamanda “alistirma1” isimli volume’u bu containerın “/test” isimli folder’ına mount edelim fakat Read Only olarak mount edelim. Bu folder içerisine geçelim ve “touch abc1.txt” komutuyla bir dosya yaratmaya çalışalım. Ve yaratamadığımızı görelim.

      - `docker container run -i --name ucuncu alpine`
      - `docker container run -it -v alistirma1:/test:ro alpine sh`  

- 16

 `docker container run -d --name websunucu1 -p 80:80 -v c:\deneme:/usr/local/apache2/htdocs ozgurozturknet/adanzyedocker`

- 17 `docker container ls -a`


# 4.Bolum Container 102


**Docker Plugin-Driver**

- Docker bize third party uygulama yüklememize izin vermektedir. Bu özelliği ile ile yetenekler kazandırılmaktadır.

**Docker Network Driver**

- Network Objeleri => docker containerların brbirleri ile iletişime geçmeleri ve dişarıdan ulaşılması için  tüm iletişim alt yapısını network objeleri ile sağlıyoruz
    - Bridge
    - Host
    - Macvlan
    - None
    - Overlay


- Brige

   - Varsayılan drivedir. Network objesi yaratılırken başka bir drive belirtilmezse bridge drive ile network yaratılır.
<img src="notes/bridge.png">


- Host

   -  üzerinde çalıştığı host un bir parçası olması gerektiği durumlarda, o containerin ağ kartına özelliklerinin kullanılması, üzerinde çalıştığı network ile bir izalosyon olmadığı durumdur. her sistemde host driver ile yaratılmış host adında bir network bulunur.
<img src="notes/host.png">


- MacVlan

   - bu driver ile olusturulan network objeleri sayesinde containerlar fiziksel ağlara kendi mac adreslserine sahip birer fiziksel ağ adaptörüne sahipçesine bağlanabilirler.

<img src="notes/macvlan.png">



- None

 - container hiçbir şekilde ağ bağlantısına sahip olmasın istenirse bu driver ile yaratılan networke bağlanır


- Overlay

  - Aynı hostlar üstündeki Birden fazla containerların aynı ağda çalışıyormuş gibi çalışması istenildiğinde Overlay network devreye girer.


**Docker Network Objeleri-1**

- `BRIDGE`

- `docker network ls`  ile yüklü network objelerini  gördük, 

- `docker network inspect bridge`  inspect ile tüm objenin özellikleri görebiliriz. image ve container da da kullanılır

- `ctrl p q` containerdan çıkış yaparken çalışır halde kalmasını sağlar


- `docker container run -it --name deneme23 --net none ozgurozturknet/adanzyedocker sh` none kurulması


- `docker exec -it container_name sh` komutu ile container içine girdik, `ifconfig` komutu ile de containerin ip adresini görüntüledik.


- container a dış dünyadan bağlanabileceğimiz gibi container içinden de ping atabiliyoruz  ``ping 8.8.8.8.`

- bir container diğer container a da ping atabilir.

- `HOST`

- hostlara kendi pc den bakamadığımız için docker with playden takip ediyoruz.

- `docker container run -it --name deneme1 --net host ozgurozturknet/adanzyedocker sh` host olarak oluşturulmak istenildiğinde 


- `None`

- `docker container run -it --name deneme1 --net none ozgurozturknet/adanzyedocker sh` none olarak olusturduk, herhangi bir network bağlantısı olmamasını istediğimiz durumlarda oluşturuyoruz.



**Docker Network Objeleri-2**



- `docker container run -d --publish 8080:80 ozgurozturknet/adanzyedocker` oluşturduğumuz containera ulaşmak için portunu açmamız gerekiyor, 

- `-p host_port:container_port` ilk önce hostun portu, sonra container portu yazılıyor

- default olarak tcp oluşturur,  `udp` istenilirse kodun sonuna 53:53/udp yapılır

- Aynı networke sahip containerlar port publish etmeden haberleşebilirler


**Docker Network Objeleri-3**

- varsayılan network objeleri yerine kendi objelerimizide oluşturabiliriz.

- Ayrıca bir network objesinden de bircok olusturabiliriz,

- aynı makine icinde birbiri ile iletisimi olan uygulamaları aynı 
  network objesine, iletisim kurulmasını istemedigimiz olanları 
  ayırmak maksadıyla farklı network objesine baglamak icin 
  birkac sayıda network objesi olusturabiliriz

<img src="notes/many_bridge.png"> 
<img src="notes/bridge_not.png">  

- Default bridge network de IP adres aralığını degistirme islemleri karisik ve zorlayıcı bu sebeple yeni network objesi olusturarak bu sorunu cözebiliriz.

- Varsayılan bridge network objesine bagli containerlar, birbirlerine isimleri ile ulasamazlar, dns hizmeti yotur.

- Kullanıcı tanımlı olusturulan bridge networklerine bagli containerlar isimleri ile birbirlerine ulasabilmektedir.

- Varsayılan bridge networkune bagli  containerların baglantılarını kesemiyoruz. Kullanıcı tanımlı olanların baglantıları kesilebilmekte ve tekrar baglanabilmektedir.


- uygulama;

- `docker container run -d --name websunucu1 ozgurozturknet/adanzyedocker`

- `docker container run -it --name database1 ozgurozturknet/adanzyedocker sh`  iki container oluşturduk, default olarak bridge networkune bağlandı ve isimleri ile ping attığımızda baglantı kurulamadı.

- kullanıcı bridge olusturulmasi :

- `docker network create kopru1`  komutu ile yeni network olusturduk, burada default olarak bridge network oluşturdu, 

- `docker network create --driver host` olarak da host oluşturabiliriz

- `docker network ls` mevcut networkleri görüntüledik

- `docker network inspect kopru1` detaylarna baktık

- `docker container run -dit --name websunucu --net kopru1 ozgurozturknet/adanzyedocker sh`
   
    
    - `-d` container oluşturduktan sonra calışmasını sağlar
    - `-it` oluşturulduktan sonra containera bağlanmak için 
    - `-dit` container oluşturduktan sonra calışmasını ve containere bağlanmasyı sağlar fakat, bağlanmadan komut satırına geçer, diğer containerları olusturabilmek icin, yani arka planda bağlantı sağlar
    - `docker attach websunucu` arka tarafta baglantı saglayan containera bu komut ile baglandık


- `docker network create --driver=bridge --subnet=10.10.0.0/16 --ip-range=10.10.10.0/24 --gateway=10.10.10.10 kopru2` subnet ve ip tanımlayarak network objesi olusturduk 


- `docker network connect kopru2 database`, kullanıcı tarafından olusturulmus networke baglı bir containeri baska bir network objesine de baglıyabiliyoruz. 

- bir container birden fazla networke baglanabilir

- `ctrl p q ` ile calısan containeri durdurmadan icinden cıkılabiliyor

- `docker network disconnect kopru2 database` ilgili network objesinden container baglantısını kestik.

- ``dcoker network rm kopru1` network objesini siliyoruz fakat bagli container varsa silmez, container baglantılarını kesmek yada silmek gerekir.


**Logging Uygulama Günlükleri**


- `docker logs name` log komutu ile log kayıtlarını görüntüledik,

- `stdin` => uygulamaya giriş için  `stdout` => uygulama çıktıları `stderr` => hata kayıtları


- `docker logs --details container_name` detayli log kayıtları için

- `docker logs -t container_name` log kaydının ne zaman olusturulduğunu iletir


- `docker logs --until zaman_damgası container_name` belirtilen zaman kadar olan kayıtları getirir

- `docker logs --since zaman_damgası container_name` belirtilen tarihten sonrasının getirir

- `docker logs --tail 3 container_name` son uc 3 satırı verir

-  `docker logs -f  container_name` sabit bir log ekranı oluşturuyor, logları takip ediyor

- `docker container run --log-driver splunk nginx` merkezi bir log dirve kurularak logları burada tutabiliriz. container kurulurken aktif edilir. 



**Docker Stats ve Top**


- `docker top container_name`  sistemde calışan prosesleri görüntülüyoruz.

- `docker stats` o sistemde calisan tüm containerların cpu, mem, limit, net ı/o bakabiliyoruz. ismini yazarsak sadece o containeri veriri.



**Container Cpu ve limitleri**

- sistem üzerinde calisan containerların ram lerinde kısılama getirebiliyoruz.

- `docker container run -d --memory=100m --name con1 ozgurozturknet/adanzyedocker` ram e kısıtlama getirdik

- `docker container run -d --memory=100m --memory-swap=200m  --name con2 ozgurozturknet/adanzyedocker` ek olarak swap alanı verebiliyoruz. M=> mb , G=> gb


- CPU kısıtlaması ve ayarlanması

    - calisilan ortamdaki core sayısına göre containerlara atama yapabiliyoruz.
    - task manager/logical processors pc de bu sekilde kontrol edilir, 0 dan 8 e kadar oldugunu varsayarsak, örnek olarak con1=> 1, 4 uncu core atayabiliyoruz.

    - iki yontemi var bu islemin;

    - `docker container run -d --cpus="1.5" ozgurozturknet/adanzyedocker` sistemde ne kadar cpu varsa 1.5 tanesi kullanacak anlamı var.
    - `docker container run -d --cpus="1.5" --cpuset-cpus="0,3"  ozgurozturknet/adanzyedocker` cpuset-cpus ile hangi core kullanacaksa o cekirdekler secilir


**Environment Variables**

- `Get-ChildItem Env:` sistem üzerinden tanımlanmış environment variables bu sekilde sorugladık 

- `$Env:TEMP` temp yerine farklı variable yazarak value ulasiriz.

- `$Env:test="denemedir"`ayrıca kendimizde environment variable tanımlayabiliyoruz test:variable, denemedir:value 


- Linux ortamında nasıl oluyor;

   - `printenv` ile linux ortamındaki bütün env görüntüleriz

   - `echo $TERM` burada TERM variablenin value görüntüledik

   - `export test="denemedir"` variable ve value olusturduk


**Docker Environment Variables**

- `docker conteiner run -it --env variable_name=value_name ubuntu bash` variabler key sensitive dir. nasıl yazıldıysa o sekilde cagrılmalıdır. 

- `docker container run -it --env TEMP ubuntu bash` seklinde yazarsak yani value tanımlamazsak o makinenin üzerindeki variable alır

- `docker container run -it --env-file .\env.list ubuntu bash` bircok env yüklencekse bir file icesinden de yukleme yapılabilir. env file path belirtilir

- Variabler neden kullanılır sorusunu şu şekilde özetleyebiliriz. uygulamamızı production ve test ortamında kullanacağımız DB için bir variable tanımlanır, hangi ortama gecildiyse o secilen variable atam ayapılarak devam edilir. 



**4.Bolum Sonu Alistirma**

- ./isim4/bolum43/alistirma.txt 

- 1 - `docker container prune` tüm containerları sildi

- 2 - `docker network create --driver=bridge --subnet=10.10.0.0/16 --ip-range=10.10.10.0/24 --gateway=10.10.10.10 alistirma-agi` and `docker network inspect 708` 

- 3 - `docker container run -d --name web1 --net alistirma-agi --publish 8080:80  nginx:1.16`

- 4 - `127.0.0.1:8080` and `docker logs web1`

- 5 - `docker logs -f web1`

- 6 - 




# 5.Bolum Image ve Registry

**Docker İmage isimlendirme Tag Yapısı**

- Docker Image = Bir linux isletim sisteminden çekirdek ve gereksiz tüm dosyaları cıkardıktan sonra, çalıştırmak istediğimiz uygulamayı ve bu uygulama icin gerekli olan kütüphanelerin yükledikten sonra containerda calimasi icin gerekli ayarlar yapılarak bir paket haline Image deniyor.


- Docker Image Registry = Docker image depolandığımız sistemlerdir. Bunların en buyugu docker hub, azure container registery gibi cloud yapılar olabilir. Ya da sadece bizim ulasabilecegimiz sistemlerde olabiliyor.  Docker trusted registry, private image registry gibi

- Aksini belirtmediğimiz sürece olusturulan docker image docker hub ile haberleşecek sekilde olusur.

- Docker image isimlendirilmesi, her image uniq id vardır, fakat id ile cagrılması vs zor oldugundan tag/isim yapısı vardır. Bu tag aynı zamanda bu image depolandıgı yer ile ilgili bilgi vermektedir.



<img src="notes/docker_image_name.png"> 


- image versiyonlarını tag ile ayırabiliriz.  örn  ozgurozturknet/adanzyedocker:v1, : den sonta tag belirtilir, aynı repository de farklı versionlar tutulur, default olarak `latest` olarak gelir


- `latest` turkce anlamı en güncel olmasına ragmen, image de bu sekilde olmayabilir, bir image kullanılırken guncel vs kontrol edilmelidir.



- `docker image pull ozgurozturknet/adanzyedocker` var olan bir image pull ettik, vesiyon belirtilmedigi icin latest olarak geldi.

- `docker.io/ozgurozturknet/adanzyedocker:latest` yukaridaki komut ile belirtildigi sekilde image geldi, 
    - ilk kısım: docker.io => image barindigi alani belirtir
    - orta kisim: ozgurozturknet/adanzyedocker => repostry 
    - :latest => versiyonunu belirtir.


- image resmi ve  resmi olmayan image olarak ikiye ayrılır, 
      - `ozgurozturknet/adanzyedocker` resmi olmayan image kirimli olarak gelmektedir.
      - `docker.io/library/ubuntu:latest` resmi olan

- `docker image pull gcr.io/google-containers/busybox`   docker hub da bulunan image barindigi alanı yazmıyorduk fakat docker hub disindakiler icin bunu yaziyoruz

- `docker pull ozgurozturknet/adanzyedocker:v1` direk docker pull ile kullanabiliriz, ayrıca tag belirterek da indirmeyi tamamladık.


**Docker Hub**

- Docker EE, Docker CE, Plugins, Containers

- offical hesap ve kullanıc/organize tarafından hesaplar mevcut,


- resmi hesaplarda güvenlik acikları docker hub tarafından kontrol ediliyor, 


- tag lerde 5, 5.2, 5.2.7 yan yana yazılanlar aynı id image sahip oluyorlar


-  bir cok kisi ile calisian yapılarda organization kullanaılabilir, 

- repository CLI ile de docker hub panelden de olusturulabilir, CLI login olabiliyoruz.

**Docker Image Olusturma Dockerfile-1**

- `Dockerfile` isimli bir file olusturuyoruz, ozel bir talimat dili ile yazilir. talimatların aciklamalari .\kisim5\bolum47\Dockerfile Talimatlari Aciklamasi.txt

```
FROM : olusturulacak imajin hangi imajdan olusturulacağını belirtilen talimat, tek mecburi talimat budur. mutlaka olmalidir. örn: ubuntu:18.04

RUN  : olusturulması istenilen image icinde komut calismasını saglar. shell de calistirilmasi gereken komutlar kullanilir.


WORKDIR  :istenilen klasöre gecmek icin oradan calismak icin verilen talimattir. WORKDIR /usr/src/app, cd ile yaptigimiz isi WORKDİR ile yaptık, ilgili klasör yok ise olusturdu.

COPY  : image icine dosya veya klasör kopyalamak icin kullanılır.  

EXPOSE : image den olusacak containerlarin hangi portlarinin üstünden erisebilecegini yani hangi portlarin yayınlanacagini bu taliamt ile belirleriz. EXPOSE 80/tcp

CMD : bu imageden  container yaratildigi zaman varsayılan olarak calistirilmasini istedigimiz komutu bu talimat ile belirleriz. CMD java merhaba



```


**Docker Image Olusturma**

- uygulamanın olduğu aynı dizinde `Dockerfile` isimli file olusturduk,

- Dockerfile=> icerisne talimatlarimizi yaziyoruz,

    ```
     FROM ubuntu:18.04
     RUN apt-get update -y
     RUN apt-get install default-jre -y
     WORKDIR /merhaba
     COPY /myapp .
     CMD ["java", "merhaba"]
     ```

- `docker image build -t yildirimesut/ilk_image .` komutu ile de image olusturuyoruz. eger Dockerfile ile file ismi olusturulmamıssa  `docker image build -t yildirimesut/ilk_image -f Dockerfile .` bu sekilde tekrar file ismini belirtmemiz lazım. eger aynı dizinde degilsek nokta yerine app oldugu path dizini yazıyoruz. 


- `docker container run yildirimesut/ilk_image` image den cntainer olusturduk ve calistirdik

- `docker image history yildirimesut/ilk_image` image hakkında gecmisi verir 


- `docker image history yildirimesut/ilk_image` ile image ait katmaları görebiliyoruz.

- `docker image push yildirimesut/ilk_image` docker hub olusturdugumuz image gönderdik


**Docker image olusturma-4**

- bolum50/py dizindeki app den image olusturduk. yukarıdaki java app den farklı olarak ubuntu, env gibi yapıları kurmaık, office yapıyı kurduk, bu bize kurulması gereken yapıları hazır olarak verdi, 

```
FROM python:alpine
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 5000    # portunu belirledik
CMD python ./index.py
```

- `docker image build -t yildirimesut/flask .` dockerfile ile aynı dizinde olacak şekilde komutu calistirarak image olusturduk.

- `docker container run --rm -p 80:5000 yildirimesut/flask` ile container olusturduk, --rm ile container kapatıldıktan sonra silinmesi icin gecerli

- kisim5\bolum50\nodejs icerisindeki app den yeni bir image olusturduk.


- Dockerfile olusturuken kısa ozet;
 ```
 1. ilgili app icin gerekli yapıyıyı kur, node, python vs icin officel yapısını kuralım
 2. bu app in barinacagi folder yapisini olustur.
 3. bu olusturulan dizin icerisine gerekli ihtiyac duyulan gerekli file copy islemini uygula
 4. ilgili app in calışması icin gerekli komutlaruı yazalim, "npm install", "pip install -r requirements.txt" gibi
 5. port belinlenmesi gerekiyorsa port ataması yap
 6. container olusturuulduğunda calistirimalsi icin gerekli komutu yaz CMD [ "node", "server.js" ] 


 ```


 - Dockerfile hazırlanırken şu konuya da dikkat edilmeli, app icinde sürekli güncellenecek olan yapılar daha alt tarafa yazilmalidir. degismeyecek olan yapilar üst kisima yazilmalidir. yapilan her degisiklikten sonra tekrardan olusturulan image cache kullanarak daha hızlı olusmasi icin gerekli bir düzenlemedir.

 ```
FROM node:10

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm ci --only=production

# Bundle app source
COPY server.js .
EXPOSE 8080
CMD [ "node", "server.js" ]
 ```

***





**Linux Shell bazı komutlar**

- `docker run -it ubuntu bash` ile linux bash acabiliriz.

- `echo` bir durumun ciktisini verir. istenirse bu durum bir file ,cerisinede yazdirilabilir.


- `echo "kale" > test.txt `  `>` isareti ile yazdirilmak istenilen ifade ilgili dosya belirtirerek icerisine yazdırır. ilgili file yoksa olusturur.


- `&` ampersand ilgili komutun sonuna ekliyoruz.   ilgili komutun calistirilmasini sagliyor ve bize komut satırına aktarıyor, calismasi gereken app beklemeden arka tarafta kodu calstirip bize komut satırı kulanmamızı saglıyor. 

- `|` pipe ile de birinci komutun ciktisini ikinci komut olarak girdi haline getirmemizei sagliyor.

- `cat abc.txt | grep 3` not:grep belirlenen ifadeyi ilgili çıktıda bulup ekrana getirir.

- `;` semicolon bize tek bir satırda birden fazla komut girmemizi sağlar

- `date; ls` gibi

- `&&` iki komutun durumunu degerlendirme yaparak calistirir., sol taraftaki komut true ise soldaki ve sagdaki komutu calıstırır.

- `cat test.txt && echo "dosya bulundu"` 


- `||` DOUBLE PIPE, komut1 || komut2, komut1 calisirsa komut2 calistirma, komut1 calismazsa komut2 calistir.


**Docker image olusturma-5**


- kisim5/bolum52/hello-docker bulunan app kullanarak image olusturduk

- Dockerfile `HEALTHCHECK` isimli talimat tanımladık, bu talimat olusturdugumuz image container olustugunda app de bir hata ve sorun olustugunda test etmesi icin tanımlanmistir.
   
     `HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 CMD curl -f http://localhost/ || exit 1`

     ```
     --interval=30s => 30s aralıklarla calistir.
     --timeout=10s => 10s boyunca calistir.
     --start-period=5s => 5s sonra baslat
     --retries=3 =>3 kere bu islemi yap
     CMD curl -f http://localhost/  => bu adresi kontrol et
     ```

- Ayrıca hostname ve kullanici bilgilerini dinamik hale getirmek icin CMD talimatından faydalandık

   `CMD sed -e s/Kullanici/"$KULLANICI"/ Hello_docker.html > index1.html && sed -e s/Hostname/"$HOSTNAME"/ index1.html > index.html ; rm index1.html Hello_docker.html; nginx -g 'daemon off;'`

**Docker image olusturma-6**  



- `\` backslash ile uzun talimat satırlarını alt satıra alabiliriz.

- her yazılan talimat satiri ayri bir katman olur, ortak talimatları tek bir satirda yazabiliriz.`&&` ve `||` faydlanabiliriz.

- image olusturacagimiz base yapidan sabit gelen talimatlar override yapilabilmektedir. Override yapildigindaki son talimat dikkate alinir.

**ADD and COPY difference** 


- `COPY`

- `ADD` copy ile aynı işi yapar dosya yada klasor kopyalamamizi saglar. 
      - ADD ayrıca;
      - dosya kaynaginin URl olmasina izin verir. uzak sunucudan kopyalamaya izin verir fakar zip dosyasi ise acmaz
      - kaynak olarak belirtilen .tar dosyasını image dosyasına kopyalar ve acar 


      ```

     FROM alpine:latest
     WORKDIR /app1
     COPY wordpress.tar.gz .    => dosyayı kopyalar fakat acmaz
     ADD wordpress.tar.gz .     => dosyayı kopyalar acar
     ADD https://wordpress.org/latest.tar.gz .  => dosyayı uzak sunucudan kopyalar fakat acmaz!!!!!!

      ```


**ENTRYPOINT and CMD**   


<img src="notes/CMD_ENTRYPOINT.png"> 

- `CMD` talimati ile bir imageden container olusturuldugunda uygulanmasi istenilen komut yazilir, 

- CMD yazilan container olusturulma asamasında RUN da degistirilebilir.


- `ENTRYPOINT` talimati aynı CMD gibi container olusturuldugunda istenilen kumut yazilir, fakat RUN komutu ile degistirilmez.


- `CMD` and `ENTRYPOINT` aynı anda kullanildiginda ise CMD ENTRYPOINT'in parametresi gibi davranir.

     - example => entrypoint de app ismini belirttik, cmd ile de version belirledik, istenilen durumlara göre farklı versiom calistirilabiliyor


- ` docker image build -t pingimaj -f .\Dockerfile.Centos .` dockerfile dosyasinin ismini farklı olarak yazdigimizda `-f file dosyasinin ismini yazdik`



**Exec and Shell** 


<img src="notes/exec_shell.png"> 

- `EXEC` json array formatında yazilan komutlardir, komut exec formatinda girildiginden dolayi shell acilmaz, komut direk process olarak calisir. Container in pid1'i o process olur.


- `SHELL` komut shell formunda girildiyse docker bu imajdan container olusturuldugunda varsayılan shell i calistirarak icerisine girer. bu nedenle calisan 1.process yani pid1 bu shell process i olur.


- Exec komut formunda calistirilan komutlar her hangi bir shell process calismadigi icin Environment Variable gibi bazı degerlere ulasamaz.

- Eger entrypoint and cmd birlikte kullanılacaksa exec form kullanilmalidir. shell formu kullanildiginda cmd komutlari entrypoint e parametre olarak aktarılmıyor.

- example, bolum56/Dockerfile ile CMD yi,
  
  
   - shell formatında yazarsak $TEST icerigine ulasabiliyoruz. 

     ```
    FROM centos:latest
    ENV TEST="Bu bir denemedir"
    CMD echo $TEST
     ```


   - exec formatında yazarsak ENV ulaşamadık.
      ```
    FROM centos:latest
    ENV TEST="Bu bir denemedir"
    CMD ["echo", "$TEST"]
     ```

- shell formunda yazdığımızda asagida belirtildigi gibi shell satırı acarak devam etti, exec formunda ise process olarak ele aldi.

<img src="notes/exec_shell_2.png">
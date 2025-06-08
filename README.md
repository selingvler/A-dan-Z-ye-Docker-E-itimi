# A'dan Z'ye Docker

## İçindekiler
- [Giriş](#giriş)
- [Container 101](#container-101)
- [Container 102](#container-102)

## Giriş
#### İşletim Sistemi
- Anakart: Tüm elektronik parçaları birbirine bağlar ve tüm ünitelerin (cpu, ram gibi) birbirleriyle haberleşmelerini sağlar.
- BIOS bilgisayar açıldığı anda çalışan bir yazılımdır. Öncellikle anakarta bağlı cihazları test eder, hangi port'a bağlı olduğuna bakar. Sonrasında hard-disk gibi bir sürücüden işletim sistemi yüklenir.
#### Kernel
- BIOS donanımı test ettikten sonra bootloader'ı yükler. Bootloader ise Kernel'ı yükler.
- İşletim sistemi bize bir arayüz sağlar. Kernel, system call aldığı zaman istenilen uygulamayı çalıştırır ve donanım kaynaklarının doğru şekilde kullanılmasını sağlar.
#### Sanallaştırma
- Eskiden bir sunucu üzerine uygulamaların birbirini etkilememesi için(uygulama izolasyonu) sadece bir tane yazılım yüklenebiliyordu. Bu durum kaynak israfına yani atıl kapasite sorununa yol açıyordu.
- Sanallaştırma bir fiziksel makine üzerinde birden fazla sanal makine kurulmasına olanak verir. Böylece kaynaklar daha verimli kullanılır. Her uygulama için ayrı bir işletim sistemi kurularak uygulama izolasyonu da sağlanmış olur.
#### Container
- Sanallaştırmada her uygulama için farklı bir işletim sistemi kurduğumuzda kaynak tüketimi artıyor.
- Container ile uygulama izolasyonu ayrı ayrı işletim sistemleri kurmadan yapılabiliyor.
- Kernel, arayüz ve kullanıcıya lazım olan programlar ile birlikte distro oluşturulur. Örnek: Ubuntu, CentOS
- İzolasyon namescape ve control groups ile sağlanıyor. Namescape her container'ın kendi process listesini görmesini sağlıyor. Control groups ise processlerin CPU gibi kaynakları kullanımını sınırlandırıyor.
- control groups procesllerin kaynak kullanımını sınırlandırır.
#### Docker Engine
- Uygulama geliştirmek, dağıtmak ve çalıştırmak içindir. Uygulamaları altyapıdan bağımsız kılmayı sağlar.
- Docker Deamon: İmage, container, network, volume gibi docker nesenelerini oluşturmayı ve yönetmeyi sağlar
- REST API: İletişim RESTful API ile sağlanır. Harici uygulamalarda bu API üzerinden iletişime geçer.
- Docker CLI: Client uygulamasıdır. Bu arayüz ile kendi bilgisayarımızdan bir bulut sunucusundaki Docker Deamon'ı yönetebiliriz.
#### Image ve Container
- Java ile yazılmış bir uygulamayı kurmak için işletim sistemi, üstüne Java Runtime Environment, gerekli kütüphaneler ve uygulamalanın kendisini yüklemek gerekir.
- Bu yapıdan kernel'ı çıkarttığımızda oluşan pakete Docker Image denir. Image'larda kernel yoktur çünkü container'lar üzerinde çalıştıkları çekirdeği kullanırlar.
- Image'ı indirip istediğim kadar container oluşturabilirim. Image bir şablondur, container ise çalışan bir kopyadır.
- Uygulamadan Docker Image elde edilir ve bu image'lar Image Registery'de depo edilir. Uygulama gerektiği zaman pull edillip çalıştırılabilir.
```
#image indirme
docker image pull ozgurozturknet/app1

#image'tan container olusturma
docker container run --name ilkcontainer ozgurozturknet/app1
docker container run --name ikincicontainer ozgurozturknet/app1
```
#### Container ve Sanal Makine Farkı
- Sanal makinede tam bir işletim sistemi barındırır ve fiziksel makine sanallaştırılır. Container'da ise içinde işletim sistemi bulunmaz, uygulama sanallaştırılır/ izole edilir.
- Container'lar çok daha hızlı çalışmaya başlar.
- Bir sanal makineyi paketleyip aynı şekilde başka yerde çalıştırmak zordur. Container image olarak paketlendiğinde başka makineye taşımak ve çalıştırmak çok kolaydır.
#### Windows Container
- Windows için yazılmış uygulamaları container olarak çalıştırmak için kullanılır.

## Container 101
#### Docker CLI
Docker'ı yönetmek için CLI kullanılır.
- ```docker version``` Docker'ın doğru çalışıp çalışmadığını kontrol etmek içindir. Server'daki işletim sisteminin Linux olduğunu burada görebilirsin.
- ```docker info``` ile temel bilgilere erişebiliriz. Kaç container ya da image var, hangi storage driver kullanılıyor gibi bilgilere erişebiliriz.
- ```docker``` Docker CLI'da kullanabileceğimiz komutları listeler. **Options** kısmında Docker Deamon'a bağlanırken kullanabileceğimiz opsiyonları görürüz. **Management Command** + **Command** kullanarak komutları çalıştırırız.
- ```docker image --help``` ile image için kullanabileceğimiz komutları listeleyebiliriz.
- ```docker image rm -f hello-world``` image'ı silmek için
#### Container Temelleri-1
- ```docker container run --help``` container run komutu container çalıştırmak ve yoksa oluşturup çalıştırmak için kullanılır
```
Usage:  docker container run [OPTIONS] IMAGE [COMMAND] [ARG...] 
docker container run --name ilkcontainer ozgurozturknet/app1
```
- ```docker container ls -a``` = ```docker ps -a```
- Uygulama çalışmayı bıraktığında container da çalışmayı bırakır.
#### Container Temelleri-2
- Docker image'larda birden fazla uygulama bulunabilir fakat container başlatılırken tek bir uygulama çalıştırılmasına izin verilir. Sonrasında ise aynı container'ın içinde birkaç uygulama çalıştırılabilir.
- ```docker container run -p 80:80 ozgurozturknet/adanzyedocker``` belirtilen port'ta web servisi çalıştırıyor
- control+C'ye basarak shell ile docker container arasındaki bağlantıyı kapattık
- ```docker container logs 95d``` id'nin ilk üç karakteri yeterli, log'ları görmek için kullanılır
- ```docker container run --name denemecon ozgurozturknet/adanzyedocker java app1``` başka bir komut ile container'ı başlattık
- ```docker container stop 95d```
- container'ı sadece arka planda çalıştırmak = detach container (az önce control+c kullanarak bağlantıyı kesmiştik)
- ```docker container run -d -p 80:80 ozgurozturknet/adanzyedocker``` bunun için kullandığım opsiyon -d
- container silmek için ```docker container rm ...``` bu komutla çalışan bir container'ı silemeyiz. ya container'ı durdurmamız gerekir ```docker container stop``` ya da ```docker container rm -f 658 a4c 95d ab2``` komutunu kullanırız.
#### Container Temelleri-3
- ```docker container run --name websunucu -p 80:80 -d ozgurozturknet/adanzyedocker```
- çalışan bir container'a bağlanmak için ```docker container exec```
- çalışan bir container üzerinde sh komutunu (bu container'ın shell'ine bağlan - alpine olduğu için sh) uygula ```docker container exec -it websunucu sh``` -it ise --interactive ve --tty opsiyonlarının birleşimi
- pwd hangi folder'da olduğumu gördüm, ls -l ile bu folder içindeki dosyaları gördüm, sonrasında java app1 uygulamasını çalıştırdım(container'da başka bir uygulama çalışırken içinde yeni bir uygulama çalıştırabildim) 
```
cd /usr/local/apache2/htdocs
echo "bunu ben ekledim" >> index.html
```
```
/usr/local/apache2/htdocs # ps //container içinde çalışan uygulamaları gördük
/usr/local/apache2/htdocs # exit //container'ın içinden çıktık
```
- _kill 1_ (1 root olduğu için container çalışmayı bırakır)
#### Docker Katmanlı Dosya Yapısı Sistemi
- Docker image'ları base image üzerine kurulur.
- Yapılan her değişiklik sırayla katman olarak eklenir ve ayrı bir dosya olarak depolanır. Union file system sayesinde bu dosyaların hepsini bir arada görürüz.
- Container oluşturulurken R/O Layers üzerine yazılabilir bir katman eklenir. Yapılan her işlem burada tutulur. Image'daki hiçbir dosya değiştirilmez.
- Copy on Write: Alt katmanda bulunan bir dosyayı değiştirmek istediğimde bu dosya yazılabilir katmana kopyalanır ve oradaki dosya gösterilir.
```
docker image pull alpine
docker image pull ozgurozturknet/hello-app  //burada ilk katman için Already exists dediğini gördük
```
#### Docker Container Yaşam Süresi
- Container ile ilgili gereklilikler image seviyesinde tamamlanmalı
- Ana uygulama durduğunda container da durur. Container durduğunda aynı image aynı ayarlarla yeni bir container yaratılarak sistem devam ettirilir.
- Container'da sıkıntı oluşursa container durdurulup sıfırdan yeni container yaratılır, sorun container'a bağlanarak çözülmez. Sorun ayarlar veya config ile ilgiliyse sorun image yaratma aşamasında çözülür ve yeni image kullanılarak container oluşturulur.
- Container'lar sürekli silinip tekrar oluşturulur. Yazılabilir katmandaki verileri kaybetmemek için volume kullanacağız.
#### Docker Volume: Container Dışı Veri Saklama
```
docker volume create ilkvolume
docker volume inspect ilkvolume
```
- ```docker container run -it -v ilkvolume:/uygulama alpine sh``` -it ile interaktif bağlantı , -v ile volume bağlamak için , : volume'u hangi klasöre bağlamak istediğim , alpine imajını kullan , sh ile shell aç ** ``` -v <VolumeName>:<ContainerİçindekiKlasör> ``` **
```
cd /uygulama
touch abc.txt
echo "bunu ben ilk containerdan ekledim" >> abc.txt
cat abc.txt
exit
```
- container'ı silsem de volume silinmez
```
docker container run -it -v ilkvolume:/deneme alpine sh
cd /deneme
cat abc.txt
```
- volume sayesinde container dışından da dosyama erişebildim.
```
docker container run -it -v ilkvolume:/deneme2 centos:8 sh  //yeni bir container oluşturdum
cd deneme2
touch buucuncu.txt
echo "test" >> buucuncu.txt // diğer container'ın shellinde bu işlemi yaptım
cat buucuncu.txt //test yazısını görebildim
```
- bir volume'u birden fazla container'a aynı anda bağlayabildim.
- ```docker container run -it -v ilkvolume:/deneme3:ro centos sh``` read only olduğu için deneme3 klasörü içinde dosya oluşturamam.
#### Docker Volume: Boş-Dolu Volume Davranışı
- Volume sadece container'a değil image içerisinde mevcut bir klasöre de mount edilebilir.
- 1. Klasör boşsa volume içerisindeki dosyalar klasörde görünür. 2. Klasör dolu, volume boş ise klasördeki dosyalar volume'e kopyalanır. 3. Klasör boş dolu fark etmez, volume doluysa volume'deki dosyaları klasör içinde görürüz.
- ```docker container run --rm -it ozgurozturknet/adanzyedocker sh``` container durdurulduğunda silinir --rm opsiyonu sayesinde
```
docker volume create deneme1
docker container run --rm -it -v deneme1:/test ozgurozturknet/adanzyedocker sh
cd /
ls
cd test
ls 
```
- deneme1 adında volume oluşturduk, container içindeki test klasörüne mount ettik. (boş bir dosyaya mount ettim)
``` 
docker container run --rm -it -v deneme1://usr/src/myapp ozgurozturknet/adanzyedocker sh

docker container run --rm -it -v deneme1:/xyz alpine sh //yeni yaratılacak bir folder'a mount ediyorum
ls
cd /xyz
ls // klasördeki dosyanın bu volume'a kopyalandığını gördük
touch adanzyedocker.txt
```
- boş olan volume'u dolu klasöre mount ettik. myapp içindeki tüm dosyalar volume'e kopyalandı.
```
docker container run --rm -it -v deneme1://usr/src/myapp ozgurozturknet/adanzyedocker sh
ls  //  adanzyedocker.txt  app1.class         app1.java
```
- dolu bir volume'u dolu bir dosyaya mount ettik. volume'un içerisinde ne varsa dosyanın içinde de onu gördük, dosyanın boş/dolu olması fark etmez
#### Bind Mounts
- Host üzerindeki bir klasör ya da dosyayı Container içerisine map edebiliriz.
```
docker container run -d -p 80:80 --name ilkweb nginx  //web sunucu oluşturduk
docker exec -it 65c sh  //container'a bağlandık
cd /usr/share/nginx/html
ls
cat index.html
```
```
docker container run -d -p 80:80 -v /Users/selinguler/Documents/GitHub/AdanZyeDocker/kisim3/bolum28/websitesi:/usr/share/nginx/html nginx
```
#### Alıştırma
```
docker image pull ozgurozturknet/app1
docker container run ozgurozturknet/app1
docker container run -d httpd:alpine //sonrasında container'ın id'sini görebilmek için detach
docker ps
docker logs 7b4
docker container stop 7b4
docker container start 7b4
docker container rm -f 7b4

docker container run -d -p 80:80 ozgurozturknet/adanzyedocker
docker container exec -it 425 sh
cd /usr/local/apache2/htdocs
echo "denemedir" >> index.html
docker container rm -f 425

docker container run alpine ls  //varsayılan olarak çalışması gereken uygulama yerine “ls”

docker volume create alistirma1
docker container run --name birinci -it -v alistirma1:/test alpine
cd /test
touch abc.txt
echo "deneme" >> abc.txt
cat abc.txt

docker container run --name ikinci -it -v alistirma1:/test alpine
cd /test
ls
cat abc.txt

docker container run --name ucuncu -it -v alistirma1:/test:ro alpine
cd /test
ls
touch abc1.txt //ReadOnly file system

docker container run -d --name websunucu1 -p 80:80 -v /Users/selinguler/Desktop/deneme:/usr/local/apache2/htdocs ozgurozturknet/adanzyedocker
```

## Container 102
#### Docker Network Driver
- Bridge: varsayılan
- Host: network izolasyonu olmadan çalışmasına ihtiyaç duyduğumuz zaman kullanırız, her sistemde bulunur, sanki o host üzerinde çalışan bir process'miş gibi host'un ağ kaynaklarını kullanır
- Macvlan: container'lar fiziksel ağlara birer fiziksel ağ adaptörüne sahipmişçesine bağlanabilirler
- Overlay: 5 farklı Docker sunucu var % farklı container var, aynı anda çalışıyormuş gibi çalışmasını istediğim zaman bunu kullanıyorum
#### Docker Network Objeleri - 1
- ```docker network ls```
- ```docker network inspect bridge```
- bir container oluşturdum ```docker exec -it <CONTAINER_ID> sh``` ```ifconfig``` dediğimde çıkan inet addr = bridge'inki ile aynı
- Docker Engine kurulduğunda 3 tane network objesi (bridge, host, none) yaratılır
- ```docker container run -it --name deneme ozgurozturknet/adanzyedocker sh``` yeni bir container yaratıp bu container'ın içinden az öncekine erişebildim ```ping 172.17.0.2``` aynı bridge network'e bağlı container'lar birbirleriyle direkt haberleşebilirler
- bridge'te ifconfig dediğim zaman eth0 ve lo ; host'ta ifconfig dediğim zaman docker0, eth0, eth1 ve lo geliyor. host üzerine bağlı olduğu sistemin direkt altyapısını kullanıyor. none'da ifconfig dendiğinde sadece lo geliyor ve ping'lemeye çalıştığımızda network unreachable oluyor.
#### Docker Network Objeleri - 2 - Port Publish
- Port publish: dış dünyadan container içerisindeki servise erişmede kullanılır. Bu yaklaşımda container hangi port'tan dinliyorsa onu fiziksel port ile eşleştiriyoruz. Böylece belirlenmiş port'tan gelen istekler container'ın port'una iletiliyor. -p parametresi ile
- ```-p <HostPort>:<ContainerPort>``` ```docker container run -d --publish 8080:80 -p 53:53/udp ozgurozturknet/adanzyedocker```
#### Docker Network Objeleri - 3
- Kullanıcı Tanımlı Bridge: container'lar arasında network izolasyonu (iki farklı bridge oluşturmak mesela), Kullanıcı tanımlı bridge network'e bağlı container'lar birbirleriyle isimler üzerinden haberleşebilirler. Dns çözümlemesi sağlar, Container'lar çalışır durumdayken de kullanıcı tanımlı bridge network'lere bağlanıp, bağlantıyı kesebilirler
```
docker container run -d --name websunucu ozgurozturknet/adanzyedocker
docker container run -it -d --name database1 ozgurozturknet/adanzyedocker sh
/usr/src/myapp # ping websunucu //id üzerinden erişebilirim ama isim üzerinden erişemiyorum burada bunu gördük

docker network create kopru1
docker container run -dit --name websunucu --net kopru1 ozgurozturknet/adanzyedocker sh  //-dit yaptığım için terminal bağlantısını kurdu fakat direkt container'ın içine girmedi istediğim zaman bağlanabiliim
docker container run -dit --name database --net kopru1 ozgurozturknet/adanzyedocker sh

docker attach websunucu // attach ile container'a bağlandım şu an container'ın içerisindeyim
/usr/src/myapp # ping database //aynı kullanıcı tanımlı bridge network2e bağlı oldukları için isimleriyle haberleşebililyorlar


docker network create --driver=bridge --subnet=10.10.0.0/16 --ip-range=10.10.10.0/24 --gateway=10.10.10.10 kopru2 //bridge'ın ağ özelliklerini belirledim

//kullanıcı tanımlı bridge'lere container'lar çalışırken bağlanabilir
docker network connect kopru2 database
docker attach database //database container'a bağlandım
ifconfig //çalıştırdığımda eth0 ve eth1 gördüm. bir container birden fazla network'e de aynı anda bağlanabilir.

docker network disconnect kopru2 database //bağlantıyı kestik
docker network rm kopru1 //kopru1'e container bağlı olduğu için silemedim
docker network rm kopru2
```
#### Logging
- ```# cd var/log/nginx #ls access.log error.log ```
```
docker container run -d --name appbir ozgurozturknet/app1 //terminalleri birbirine bağlamadığımız için ekranda mesajı göremedik fakat mesaj oluştu
docker logs appbir  //ile şu ana kadar oluşturulmuş logları görüyoruz

docker container run -d --name con1 -p 80:80 nginx
docker logs con1
```
#### Docker Logs
```
docker container run -d --name con1 -p 80:80 nginx
docker logs --details con1
docker logs -t con1  //--until ve --since , --tail 3 parametreleri de var
docker logs -f con1  // tekrar console'a geri dönmez, logları canlı olarak takip edebilirim

docker container run --log-driver splunk nginx //json yerine farklı bir logging driver atayabiliyorum
```
#### Docker Stats ve Top
```
docker top con1 //container'da çalışan process'leri listeler
docker stats  // host'un üzerinde çalışan tüm container'ları listeler, CPU% | MEM USAGE | MEM% | NET I/O | BLOCK I/O | PIDS'i görebiliriz
docker stats con1
```
#### Container Cpu ve Memory Limitleri
- sistem kaynaklarınının kullanımına sınır getirmek
- --memory = <LIMIT>
```
docker container run -d --memory=100m ozgurozturknet/adanzyedocker
docker container run -d --memory=100m --memory-swap=200m ozgurozturknet/adanzyedocker // alan aşılmışsa bile swap olarak 200mb daha kullanabilir

docker container run -d --cpus="1.5" ozgurozturknet/adanzyedocker  //toplam core'ların 1.5 tanesini kullan
docker container run -d --cpus="1.5" --cpuset-cpus="0,3" ozgurozturknet/adanzyedocker //0 ve 3. core'u kullan
```
#### Environment Variables
- ```Get-ChildItem Env: ``` tüm env variable'ları listeledik
- ```$Env:TEMP``` temp'in değerine bakmak için
- ```$Env:test = "denemedir"``` biz tanımladık
- ```printenv``` ```echo$TEMP``` ```export test = "denemedir"``` Linux için
#### Docker Environment Variables 
- image ya da container yaratılırken tanımlanır
- ```docker container run -it --env VAR1=deneme1 --env VAR2=deneme2 ubuntu bash``` --env VARIABLE = değer , -e VARIABLE = değer
- çalıştığı host üzerindeki env variable'ın değerini container içerisine aktarmak  ```docker container run -it --env TEMP ubuntu bash``` ```printenv```
- ```docker container run -it --env-file ./env.list ubuntu bash``` --env-file dosya_ismi, dosyanın içindeki env değerlerini dosya üzerinden container'a attık (bunu envlist'in bulunduğu dosyanın içindeyken yaptık)
```
docker container run --env veritabani=testveritabani.pizzadukkani.com ozgurozturknet/env1
docker container run --env veritabani=prod.pizzadukkani.com ozgurozturknet/env1
```

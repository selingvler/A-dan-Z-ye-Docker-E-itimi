# A'dan Z'ye Docker

## İçindekiler
- [Giriş](#giriş)
- [Container 101](#container-101)

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
- ```docker container run -it -v ilkvolume:/uygulama alpine sh``` -it ile interaktif bağlantı , -v ile volume bağlamak için , : volume'u hangi klasöre bağlamak istediğim , alpine imajını kullan , sh ile shell aç
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

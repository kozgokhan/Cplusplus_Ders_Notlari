# Numaralandırma Sınıfları

_C++11_ ile gelen araçlardan biri de numaralandırma sınıfları. 
Standartların kullandığı ingilizce resmi terim _"strongly typed enums"_. 
"Güçlü tür özelliği kazandırılmış numaralandırma türleri" olarak Türkçeye çevirebiliriz.
Numaralandırma sınıflarının dile eklenmesiyle birlikte artık _C++_'da iki ayrı numaralandırma aracı var. 
Geçmişten gelen ve bazı eklemelerle halen varlığını koruyan numaralandırma türlerine bundan sonra "düz numaralandırma türleri" _(plain enum)_ ya da "kapsamsız numaralandırma türleri" _(unscoped enums)_ diyeceğiz. 
Numaralandırma sınıflarının dile neden eklendiğini anlayabilmek için, kapsamsız numaralandırma türlerinin _C++11_ öncesinde yol açtığı tipik sorunları anlayabilmemiz gerekiyor:

## numaralandırma sabitlerinin bilinirlik alanları
Düz numaralandırma türlerinde tanıtılan numaralandırma sabitlerinin kapsamları _(scope)_ numaralandırma türünün bilinirlik alanıdır. 
Bu durum numaralandırma sabitleri olan isimlerin aynı kapsamda bulunan diğer isimlerle çakışma riskini arttırır. 
Çok sayıda kütüphanenin bir arada kullanıldığı projelerde numaralandırma sabitleri olarak kullanılan isimlerin çakışması çok sık karşılaşılan bir durumdur:

```
//traffic.h
enum TrafficLight {Yellow, Green, Red};

//screen.h
enum ScreenColor {White, Gray, Green, Red, Magenta, Brown, Black};

//simulation.cpp
//#include "traffic.h"
//#include "screen.h"
```

Yukarıdaki kodda _traffic.h_ başlık dosyasında tanıtılan _enum TrafficLight_ isimli bir tür var. 
Bu başlık dosyası ile doğrudan bir ilişkisi olmayan _screen.h_ isimli başlık dosyası ise _enum ScreenColor_ isimli bir türü tanıtmış. 
_simulation.cpp_ isimli kod dosyası her iki modülün sağladığı hizmetlerden faydalanabilmek için iki başlık dosyasını da _#include_ önişlemci komutlarıyla kendi kaynak dosyasına eklemiş. 
Bu durumu derleyici bir sentaks hatası olarak işaretleyecek. _Green_ ve _Red_ isimleri çakışıyor. 
Aynı kapsamda birden fazla farklı varlık aynı ismi taşıyamaz, değil mi? 
Numaralandırma sınıflarının sağladığı faydalardan ilki burada. 
Numaralandırma sınıfı türlerinin kendi bilinirlik alanları var ve tanıtılan numaralandırma sabitleri, numaralandırma sınıfının kendi bilinirlik alanında yer alıyor. 
Yukarıdaki kodda şimdi numaralandırma sınıflarını kullanıyoruz:

```
//traffic.h
enum class TrafficLight {Yellow, Green, Red};

//screen.h
enum class ScreenColor {White, Gray, Green, Red, Magenta, Brown, Black};

//simulation.h
//#include "traffic.h"
//#include "screen.h"

void func()
{
	auto traffic_light = TrafficLight::Green;
	auto screen_color = ScreenColor::Green;
	//
}
```

Numaralandırma sınıflarına ilişkin sabit isimleri numaralandırma sınıf ismiyle nitelenmek zorunda. 
Aşağıdaki gibi bir kullanım geçerli değil:

```
ScreenColor scr_color = Magenta; //geçersiz
```
Kapsamsız numaralandırma türleri ile tanıtılan numaralandırma sabitlerinin bir kapsamı olmamasına karşın 
_C++11_ artık onları da numaralandırma tür ismiyle niteleyerek kullanabiliyoruz:

```
enum Suit {Club, Diamond, Heart, Spade};

Suit s = Suit::Diamond;
```

_C++11_ öncesi numaralandırma sabitlerinin isim çakışmalarından korunması için bir yapı ile sarmalanması sık başvurulan bir yoldu:

```
struct SuitWrapper {
	enum Suit { Club, Diamond, Heart, Spade };
};

SuitWrapper::Suit s = SuitWrapper::Diamond;
```

Böyle bir sarmalamanın görece bir avantajı, sarmalayan sınıftan türetme yapılabilmesi. 
Ancak numaralandırma sınıflarından türetme yapılması olanağı yok.
İsim çakışmasından korunmak için kullanılan bir başka yöntem de numaralandırma türünün tanımını bir isim alanı içine almaktı:

```
namespace Neco {
    enum Suit {Club, Diamond, Heart, Spade};
}

Neco::Suit s = Neco::Diamond;
```

## kapsamsız numaralandırma türlerine ilişkin sorunlu tür dönüşümleri
Kapsamsız numaralandırma türlerine yalnızca aynı türden değerler atanabilir, yani diğer türlerden kapsamsız numaralandırma türlerine otomatik _(implicit)_ tür dönüşümü yoktur:

```
enum Color { White, Yellow, Gray, Green, Brown, Black };
enum Font { Arial, Verdana, TimesNewRoman, Courier, Helvetica };

int main()
{
	Color c1 = Yellow; //geçerli
	Color c2 = 3; //geçersiz
	Font f1 = Verdana; //geçerli
	Font f2 = Black; //geçersiz
	c1 = f1; //geçersiz
}
```

Yukarıdaki kodda geçersiz atamaları görüyorsunuz. 
Numaralandırma türlerine diğer türlerden otomatik tür dönüşümü olmaması yanlış yazımlara karşı önemli bir koruma sağlar. 
Eğer küçük bir olasılıkla da olsa, geçersiz olan ilk değer verme ya da atama işlemleri istenerek yapılıyorsa *static_cast* tür dönüştürme operatörü kullanılabilir:

```
enum Color { White, Yellow, Gray, Green, Brown, Black };
enum Font { Arial, Verdana, TimesNewRoman, Courier, Helvetica };

int main()
{
	Color c1 = Yellow; 
	Color c2 = static_cast<Color>(3);
	Font f1 = Verdana; //geçerli
	Font f2 = static_cast<Font>(Black); 
	c1 = static_cast<Color>(f1);
	//
}
```
Ancak kapsamsız numaralandırma türlerinden tamsayı ve gerçek sayı türlerine otomatik _(implicit)_ tür dönüşümü var. 
Aşağıdaki koda bakalım:

```
enum Color { White, Yellow, Gray, Green, Brown, Black };

int main()
{
	Color c = Green;
	int cnt = 0;
	//
	int ival = c;
	//
}
```

Yukarıdaki kodda _int_ türden _ival_ değişkenine _Color_ türünden _c_ değişkeninin değeriyle ilk değer verilmiş. 
Dilin kurallarına göre kod geçerli. 
_Color_ türünden _c_ derleyici tarafından _int_ türden _3_ değerine dönüştürülecek. 
Belki de kodlayıcımız ilk değer verme işleminde _cnt_ ismini kullanmak yerine yanlışlıkla _c_ ismini yazmıştı. 
Numaralandırma sınıflarında artık diğer türlere otomatik tür dönüşümü geçerli değil:

```
enum class Color { White, Yellow, Gray, Green, Brown, Black };

int main()
{
	Color c = Color::Green;
	int cnt = 0;
	//
	//int i = c; //geçersiz
	int j = static_cast<int>(c); //geçerli
	//
}
```

Yukarıdaki kodda _i_ değişkenine verilen ilk değer geçerli değil. 
Çünkü _Color_ türünden _int_ türüne otomatik tür dönüşümü yok. 
Ancak _j_ değişkenine ilk değer verilirken _Color_ türünden değer *static_cast* operatörü _int_ türüne dönüştürülüyor. 
Kod geçerli.

## numaralandırma türlerinin baz türleri
Hem _C_'de hem de _C++_'ta da numaralandırma türleri için derleyici arka planda bir tamsayı türü kullanır. 
Bir numaralandırma türüne ilişkin derleyici tarafından arka planda kullanılan tamsayı türüne İngilizcede _"underlying type"_ deniyor. 
Biz bu anlamda "baz tür" terimini kullanacağız. 
_C_'de numaralandırma türlerinin baz türü her zaman _int_ türüdür. 
Yani bir _C_ kodunda _enum Data_ bir tür olmak üzere

```
assert(sizeof(enum Data) == sizeof(int))
```
ifadesi her zaman doğrulanır.

Ancak _C++03_'de bir numaralandırma türüne ilişkin baz türün ne olacağına karar veren derleyicidir. 
Derleyici numaralandırma türü karşılığında işaretli ya da işaretsiz _char, short, int, long_ türlerinden birini baz tür olarak seçebilir. 
Yine derleyici _C++03_'de farklı numaralandırma türleri için farklı baz türler seçebilir. 
Baz türün seçiminde derleyici yalnızca şu kurallara uymak zorundadır:
Tüm numaralandırma sabitleri seçilen baz türde temsil edilebilmelidir. 
Tüm numaralandırma sabitleri eğer _int_ ya da _unsigned int_ türünde ifade edilebiliyor ise baz tür olarak daha büyük bir tür seçilmeyecektir.
_int_ ürünün 32 bit olduğu bir sistem için kodların derlendiğini düşünelim:

```
enum Pos {Off, On};
```

Kullanılan numaralandırma sabitleri _char, signed char, unsigned char_ türlerine sığıyor. 
_C++03_'te derleyici baz tür olarak bu türlerden birini seçebileceği gibi doğrudan _int_ türünü de baz tür olarak kullanılabilir.

```
enum BufferSize{DefaultSize = 10000000, LargeSize = 20000000};
```
Yukarıdaki bildirimde ise BufferSize türü için derleyici baz tür olarak _C++03_'te _int_ türünü seçecektir. 
Numaralandırma türlerine ilişkin baz türlerinin derleyiciden derleyiciye değişebilmesi hem taşınabilirlik sorunları oluşturuyor 
hem de bir numaralandırma türünün ön bildiriminin _(forward declaration)_ yapılmasını engelliyor.

_C++11_ standartlarıyla bu konuda önemli değişiklikler yapıldı:

+ Hem kapsamsız numaralandırma türleri hem de numaralandırma sınıfları için bildirimde ya da tanımda baz tür belirlenebiliyor. 
Aşağıdaki örneklere bakalım:

```
enum Suit : unsigned char {Club, Diamond, Heart, Spade};

enum class BufferSize : unsigned int {
	SmallSize   = 10000u, 
	DefaultSize = 100000u, 
	LargeSize   = 1000000u 
};

enum Color : unsigned char;

enum Font; //Geçersiz

enum class ErrorCode;

enum class ImageWidth : unsigned long;
```

Yukarıdaki kodda tanımlanan kapsamsız _enum Suit_ türünün baz türü olarak _unsigned char_ türü seçilmiş. 
Baz türün _enum_ etiketi olarak seçilen isimden sonra gelen _":"_ atomunu izlediğini görüyorsunuz.
Numaralandırma sınıfı olarak tanımlanan _BufferSize_ türünde ise baz tür olarak _unsigned int_ seçilmiş.
Kapsamsız numaralandırma türü olan _Color_ yalnızca bildirilmiş ancak tanımlanmamış. 
_C++11_ öncesinde numaralandırma türlerine ilişkin ön bildirim yapılamıyordu. 
Bildirimde baz tür olarak _unsigned char_ türü seçilmiş.
Kapsamsız numaralandırma türü olan _Font_ yalnızca bildirilmiş ancak tanımlanmamış. 
Bildirimde baz tür belirtilmediği için bildirim geçersiz.
Numaralandırma sınıfı olarak bildirilen _ErrorCode_ türünde baz tür belirtilmemiş. 
Baz tür varsayılan biçimde _int_ türü kabul edilecek.
Numaralandırma sınıfı olarak bildirilen _ImageWidth_ türünde baz tür olarak _unsigned long_ türünün seçildiği belirtilmiş.

## ön bildirim neden önemli?
Kodla tanımlanan türler _(user defined types)_ söz konusu olduğunda derleyici bir türün tanımını görmese de o türün bildirimine dayanarak belirli bağlamlarda kullanımını geçerli kabul eder. 
Derleyicinin varlığından haberdar olduğu ancak henüz tanımını görmediği bir türe "tamamlanmamış tür" _(incomplete type)_ denir. 
Tamamlanmamış türlerden gösterici değişkenler tanımlanabilir ya da tamamlanmamış türler fonksiyon bildirimlerinde kullanılabilir. 
Tamamlanmamış türleri belirli bağlamlarda kullanabilmek için derleyici bu türlerin ön bildirimini görmelidir. 
Bir türü ön bildirimle kullanabildiğimiz durumlarda bu türün tanımını içeren başlık dosyasını kendi kodumuza dahil etmemiz gerekmez. 
Bu durumda başlık dosyalarının birbirine bağımlılığı ortadan kaldırıldığı gibi derleme süreleri kısalır. 
Ayrıca başlık dosyasından gelecek isimlerin çakışma riski ortadan kalkmış olur. 
Aslında tıpki gösterici değişkenlerde olduğu gibi numaralandırma türlerinden değişkenleri de numaralandırma türünün tanımını derleyiciye göstermeden tanımlayabiliriz. 
Ancak bunun için derleyicinin söz konusu numaralandırma türü için bellekte kaç _byte_ yer ayrılacağını bilmesi gerekir. Aşağıdaki koda bakalım:

```
//fileoperations.h

enum ErrorCode : unsigned int;

class File {
private:
	ErrorCode m_ec;
	///
};
```

_fileoperations.h_ isimli başlık dosyasında tanımlanan _File_ isimli sınıfın _private_ veri öğelerinden biri _ErrorCode_ numaralandırma türünden. 
_ErrorCode_ türünün ön bildirimi yapılmış ve bildirimde baz türün _unsigned int_ türü olduğu belirtilmiş. 
_C++11_ öncesinde _File_ sınıfının tanımının geçerli olabilmesi için derleyicinin _ErrorCode_ türünün de tanımını görmesi gerekirdi. 
_ErrorCode_ türünün _error.h_ isimli bir başlık dosyasında tanımlandığını kabul edelim. Bu durumda _fileoperations.h_ başlık dosyası _error.h_ başlık dosyasını dahil edecekti:

```
//fileoperations.h

#include "error.h"

class File {
private:
	ErrorCode m_ec;
	///
};
```

Oysa _C++11_ kurallarına göre artık _ErrorCode_ türünün ön bildirimi bu türden bir veri öğesini kullanabilmemiz için yeterli. 
Şimdi kazandığımız avantajlara bir bakalım:
_fileoperations.h_ başlık dosyasını dahil eden müşteri kodları _ErrorCode_ türünün tanımını içeren başlık dosyasını dahil etmeyecekler. 
Böylece

+ Derleme süresi kısalacak.
+ _error.h_ başlık dosyasında tanımlanan numaralandırma sabitleri olan isimler müşteri kodlar tarafından görülmeyeceği için isim çakışması riski söz konusu olmayacak: 
_ErrorCode_ türünde onlarca numaralandırma sabiti tanımlanmış olabilir, değil mi?
+ _error.h_ başlık dosyasındaki değişimler _fileoperations.h_ dosyasını dahil eden kodların değiştirilmesini ya da bu kodların yeniden derlenmesini gerektirmeyecek.

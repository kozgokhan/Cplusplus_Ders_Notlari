## Fonksiyon Yüklemesi (Function Overloading)

C++ dilinde, bir kapsamda _(scope)_ aynı isimli birden fazla fonksiyon bildirilebilir ya da tanımlanabilir. 
Programcıların işini kolaylaştıran bu araç İngilizce'de _"function overloading"_ (fonksiyon yüklemesi) olarak isimlendirilir. 
Önce şu sorulara yanıt aramakla başlayalım: 
Neden iki ya da daha fazla sayıda fonksiyonun isimlerinin aynı olmasını isteyelim? 
İki ayrı fonksiyona aynı isim vermenin nasıl bir faydası olabilir? 
Bir örnekle başlayalım:

C'nin aşağıdaki standart işlevlerini hatırlayalım:

```
int abs(int x);
double fabs(double x);
long labs(long x);
```

C'nin standart başlık dosyalarından biri olan _math.h_ içinde bildirimleri yer alan yukarıdaki fonksiyonların hepsi aslında aynı işlemi yapar. 
Bu fonksiyonlar kendilerine argüman olarak gönderilen değerin mutlak değerini döndürür. Bu fonksiyonların çağıran koddan aldıkları 
değerlerin türleri farklıdır. 
Buna bağlı olarak geri dönüş değerlerinin türleri de farklıdır. 
C dilinde aynı kapsamda aynı isimli fonksiyonlar tanımlanamayacağı için bu işlevlere ayrı isimler verilmiştir. 
Bu fonksiyonların hepsinin isminin aynı olması bir fayda sağlar mıydı? 
Örneğin hepsinin isimleri _abs_ olsaydı, mutlak değer alma işlemi yapacak programcının birden fazla fonksiyon 
ismini bilmesi ya da anımsaması gerekmezdi, değil mi? Aslında dilin temel operatörlerini düşündüğünüz zaman benzer bir aracın kullanıma hazır 
bir biçimde sunulduğunu görebilirsiniz.

Toplama operatörünü ele alalım. Toplama operatörü ne iş yapar? 
İki değerin toplanmasını sağlar, değil mi?

```
i1 + i2
```

_i1_ ve _i2_'nin _int_ türden değişkenler olduğunu düşünelim. 
_i1_ ve _i2_ toplama toplama operatörünün terimleri yapılmış. 
Toplama operatörü bir işlemin yapılmasını sağlıyor. 
Yapılan işlemin sonucunda bir tamsayı değeri üretiyor. 
Şimdi de aşağıdaki işleme bakalım:

```
d1 + d2
```

_d1_ ve _d2_'nin _double_ türden değişkenler olduğunu düşünelim. 
_d1_ ve _d2_ toplama toplama operatörünün terimleri yapılmış. 
Toplama operatörü yine bir işlemin yapılmasını sağlıyor. 
Yapılan işlemin sonucunda bir gerçek sayı değeri üretiliyor. 
Oysa işlemci düzeyinde bakıldığında, tamsayı türünden iki değerin birbiriyle toplanmasıyla, 
gerçek sayı türünden iki değerin birbiri ile toplanması bambaşka işlemlerdir. 
Bize çok doğal görünen bu iki örnekte yapılan işlemi, aslında ayrıntılarından soyutlayarak "toplama" olarak ifade ediyoruz.
Matematikte olduğu gibi _C_ dilinde de, bu işlemleri yapmak için bir soyutlama kullanılarak 
aynı operatör yani aynı simge kullanılıyor.
İki işlem için farklı iki simge kullanılmış olsaydı algılama bu kadar kolay olur muydu?
Şimdi de _m1_ ve _m2_ değişkenleri _Matrix_ isimli bir sınıf türünden olsun.

```
m1 + m2
```

gibi bir ifade karşılığı iki matrisin toplanması işleminin yaptırılması şüphesiz kodu yazanın işini kolaylaştırırdı.

Fonksiyon yüklemesinden söz edilebilmesi için iki koşulun sağlanması gerekir:
+ Aynı isimli birden fazla fazla fonksiyon aynı kapsamda _(scope)_ var olmalı.
+ Bu fonksiyonların parametrik yapıları birbirinden farklı olmalı.

Anlatım kolaylığı olsun diye, 
fonksiyonların parametre değişkenlerinin sayısı ile her bir parametre değişkeninin türünü kapsayan bilgiye _"fonksiyonun imzası"_ diyelim. 
Fonksiyonun geri dönüş değerinin türünü fonksiyonun imzasının bir parçası olarak görmeyeceğiz.
Aynı kapsamda aynı isimli iki ya da daha fazla sayıda fonksiyona ilişkin bildirim varsa aşağıdaki üç durumdan biri söz konusudur:

+ Aynı kapsamdaki aynı isimli iki fonksiyonun hem parametre değişkeni sayısı aynı hem de parametre değişkenlerinin türleri aynı ise 
bu durum fonksiyonun yeniden bildirimidir _(redeclaration)_. 
_C_'de olduğu gibi _C++_'ta da bir fonksiyonun bildirimi özdeş olması koşuluyla birden fazla kez yapılabilir. 
Aşağıda aynı fonksiyon bildirimi iki kez yapılıyor:

```
int func(int);
int func(int);
```

+ Fonksiyonların parametrik yapıları (imzaları) tamamen aynı, 
fakat bildirilen geri dönüş değerlerinin türleri farklı ise bu durum sentaks hatasıdır.

```
int func(int)
double func(int); //geçersiz - fonksiyon bildirimi çelişkili biçimde yineleniyor
```

Geri dönüş değeri türü dışında tüm parametrik yapısı aynı olan aynı iki fonksiyonun var olduğunu düşünelim. 
Bu isimle bir fonksiyon çağrısı yapıldığında derleyici hangi fonksiyonun çağrıldığını anlayabilir miydi? 
Yukarıdaki her iki bildirim de geçerli olsaydı hangi _foo_ fonksiyonunun çağrıldığı nasıl anlaşılırdı?

+ Aynı kapsamdaki aynı isimli fonksiyonların parametik yapıları birbirinden farklı ise 
(yani fonksiyonların imzaları farklı ise) bu durum fonksiyon yüklemesidir. 
Bu durumda fonksiyonların geri dönüş türlerinin bir önemi yoktur:

```
//function overloading (fonksiyon yüklenmesi)
int func(int);
int func(int, int);
```

Aynı isimli iki fonksiyonun bildiriminde parametrik yapı aynı olabilir ve bildirimlerin birinde varsayılan argüman belirtilebilir. 
Bu durum yine "yeniden bildirim"dir, fonksiyon yüklemesi değildir:

```
int max(int *ptr, int size);
int max(int *, int = 10); //fonksiyon bildirimi yineleniyor (function redeclaration)
```

Tür eş isim _(type alias)_ bildirimleri ile _(typedef ya da using bildirimi)_ ile yeni bir tür oluşturulmuş olmaz. 
_typedef_ ya da _using_ bildirimi ile var olan bir türe yeni bir isim _(tür eş ismi)_ verilebilir. 
Aynı isimli iki fonksiyonun bildirimlerinde parametre değişkenlerinin türlerinin yazımında 
aynı türe ilişkin eş isim kullanılşması durumu fonksiyon yüklemesi değildir. 
Fonksiyonun yeniden bildirimidir.

Aşağıdaki örneği inceleyin:

```
typedef unsigned char Byte;

unsigned char foo(unsigned char); 
Byte foo(Byte); //yeniden bildirim
```

Peki _C++_'ta aynı kapsamda parametrik yapıları birbirlerinden farklı aynı isimli fonksiyonlar bildirilebildiğine göre 
derleyici aynı isimli işlevlerden hangisinin çağrıldığını nasıl anlar? 
Bir fonksiyon çağrısının hangi fonksiyona ilişkin olduğunun saptanması işlemine _"function overload resolution"_ 
(Yüklenmiş fonksiyonun belirlenmesi) denir. 
_"function overload resolution"_ derleyici tarafından üç aşamada yapılan bir işlemdir:

+ Birinci aşamada derleyici söz konusu fonksiyon çağrısı için ele alınacak aynı isimli işlevleri saptayarak 
bu fonksiyonların parametrik yapısı hakkında bilgi edinir. 
Çağrılması söz konusu olan aynı isimli işlevlere aday fonksiyonlar _(candidate functions)_ denir. 
Aday fonksiyonlar, fonksiyon çağrı ifadesinde kullanılan isimle aynı isme sahip olan ve 
fonksiyon çağrı ifadesinin bulunduğu yerde görülebilen _(visible)_ işlevlerdir. 
Birinci aşamada derleyici aday fonksiyonlar hakkında gerekli bilgiyi de edinir. 
Aday fonksiyonların parametre değişkeni sayılarını, parametre değişkenlerinin türlerini öğrenir.

+ İkinci aşamada fonksiyon çağrı ifadesinde yer alan argümanlar kullanılarak hangi aday fonksiyonların geçerli biçimde çağrılabileceği saptanır. 
Yapılan fonksiyon çağrısıyla geçerli biçimde çağrılabilen işlevlere "uygun fonksiyonlar" _(viable functions)_ denir. 
Bir fonksiyonun uygun fonksiyon olarak belirlenebilmesi için aşağıdaki koşulları sağlaması gerekir:

1. Fonksiyon çağrı ifadesindeki argüman sayısı ile, fonksiyonun parametre değişkeni sayısının aynı olması zorunludur.

2. Fonksiyon çağrı ifadesindeki argüman sayısı, fonksiyonun parametre değişkeni sayısından daha az ise, 
bu durumda fonksiyonun argüman gönderilmeyen parametre değişkenleri varsayılan argüman almalıdır. 
Argüman olan ifadenin türünden parametre değişkeninin türüne geçerli bir tür dönüşümünün _(type conversion)_ yapılabilmesi gerekir. 
Bir fonksiyonun uygun olup olmadığını pratik olarak şöyle anlayabiliriz. 
Bu fonksiyon tek başına olsaydı (aynı isimli başka fonksiyon olmasaydı) çağrı geçerli olur muydu? 
Örnek olarak, aşağıda bildirimleri verilen işlevleri inceleyelim:

```
void foo();					//1
void foo(int);					//2 
void foo(double, double = 3.4)			//3
void foo(char _);				//4

void func()
{
	foo(5);
}
```

_func_ fonksiyonu içinde _foo_ isimli fonksiyona yapılan çağrı hangi fonksiyona bağlanır?

__1. aşama__

Fonksiyonun çağrıldığı noktada, tüm fonksiyon bildirimleri görülebilir _(visible)_. 
Bu aşamada dört fonksiyon da aday olarak belirlenir.
Derleyici bu fonksiyonların parametrik yapıları hakkında bilgi edinir.

__2. aşama__

+ 1 numaralı fonksiyonun parametre değişkeni sayısı _(0)_ ile fonksiyon çağrısındaki argüman sayısı _(1)_ birbirine eşit değildir.
Bu fonksiyon uygun _(viable function)_ değildir. Bu fonksiyon tek başına olsaydı sentaks hatası oluşurdu.

+ 2 numaralı fonksiyon uygundur. 
Parametre değişkeni ile argüman sayısı uyumludur. 
Argümandan parametre değişkenine geçerli bir dönüşüm söz konusudur. 
Bu fonksiyon tek başına bulunsaydı çağrı geçerli olurdu.

+ 3 numaralı fonksiyon uygundur. 
Fonksiyonun iki parametre değişkeni vardır. 
Ancak ikinci parametre değişkeni varsayılan argüman aldığından fonksiyon tek argüman ile çağrılabilir. 
Argüman olan ifade _int_ türden  bu argümanın kopyalanacağı parametre değişkeni _double_ türdendir. 
_int_ türünden _double_ türüne geçerli dönüşüm vardır.

+ 4 numaralı fonksiyon uygun değildir.
Fonksiyonun parametre değişkeni sayısı ile fonksiyon çağrısındaki argüman sayısı birbirine eşittir. 
Ancak int türünden _char__ türüne geçerli bir tür dönüşümü yoktur. 
İkinci aşamada uygun bir fonksiyon bulunmaz ise fonksiyon çağrısı geçersiz kabul edilir.
Bu duruma İngilizcede _"no match"_ durumu _(çağrılacak uygun bir fonksiyonun bulunmaması)_ denir.

__Üçüncü aşama__

Üçüncü yani son aşamada uygun fonksiyonlar içinde en uygun olan fonksiyon belirlenir. 
Bu aşamada ya çağrıya karşı bir fonksiyon seçilir ya da sentaks hatası oluşur. 
Uygun fonksiyonlar içinden seçilerek çağrılacak fonksiyona "best viable function" ya da _"best match_ function" _(en uygun fonksiyon)_ denir. 
Dilin kurallarına göre iki ya da daha fazla fonksiyon arasında seçim yapılaması durumu sentaks hatasıdır. 
Üçüncü aşamada en uygun fonksiyonun seçilebilmesi için aşağıdaki koşulun sağlanması gerekir:
+ Argümandan fonksiyonların parametre değişkenlerine yapılan dönüşüm belirli kalitelere ayrılmıştır. 
Her bir fonksiyon için argümandan o fonksiyonun parametre değişkenine yapılacak dönüşümün kalitesine bakılır. 
Kalitesi en yüksek olan fonksiyon seçilecektir.

+ Birden fazla uygun fonksiyon içinden, en uygun fonksiyonun seçilememesi durumunda 
_ambiguity_ _(çift anlamlılık hatası)_ denilen bir hata durumu oluşur. 

__variadic dönüşüm__

Kalitesi en düşük olan dönüşüm _variadic_ dönüşümdür. 
_"variadic"_ fonksiyonlara farlı sayıda argüman gönderilebileceğini hatırlayınız. 
Aşağıdaki fonksiyona bakalım:

```
void func(int, ...);
```

Fonksiyonun son parametresinin üç nokta atomu _(ellipsis)_ ile belirtildiğini görüyorsunuz. 
Bu fonksiyonun birinci parametresine bir argüman gönderilmek zorundadır. 
_variadic_ parametre _(ellipsis)_ için  istenilen sayıda argüman gönderilebilir. 
Yani _func_ fonksiyonu bir ya da birden fazla argüman ile çağrılabilir.

__Programcının tanımladığı dönüşümler _(user-defined conversions)___

Programcının tanımladığı dönüşümlerin kalitesi _"variadic"_ dönüşümün kalitesine göre daha iyidir. 
_C_'den farklı olarak _C++_ dilinde normalde geçerli olmayan tür dönüşümleri bazı fonksiyonların tanımlanması ile geçerli hale gelir. 
Derleyici bildirilen bir fonksiyona çağrı yaparak bir dönüşümü gerçekleştirir. 
Yani dönüşümü gerçekleştiren aslında derleyicin çağıracağı bir fonksiyondur. 
Böyle fonksiyonlara çağrı yapılması ile, sınıf türleri ile temel türler arasında ya da farklı sınıf türleri arasında tür dönüşümleri yapılabilir. 
Böyle dönüşümlerin yapılmasını sağlayan fonksiyonlar şunlardır:

+ Dönüştüren kurucu fonksiyon _"(conversion constructor)_
+ (Tür dönüştürme operatör fonksiyonları _(type-cast operator functions)_

Bu fonksiyonları ileride ayrıntılı olarak inceleyeceğiz.

__standart dönüşümler__

Bu dönüşümler dilin kurallarına göre örtülü _(implicit)_ olarak yapılabilen dönüşümlerdir. 
Standart dönüşümlerin kalitesi programcı tarafından tanımlanan dönüşümlerin kalitesinden daha yüksektir. 
Standart dönüşümler de keni içinde 3 kategoriye ayrılır.

+ Tam uyum _(exact match)_
+ Yükseltme _(promotion)_
+ Diğer dönüşümler _(conversion)_

Kurallara göre, "tam uyum"'un seçilebilirliği "yükseltme"den, "yükseltme" durumu standart dönüşümden, 
standart dönüşüm de programcının tanımladığı dönüşümden daha iyi olarak kabul edilir. 
Yukarıdaki derecelendirmeleri ayrıntılı biçimde inceleyelim:

__Tam uyum durumu__

Argüman olan ifadenin türü ile bu argümanın kopyalanacağı parametre değişkeninin türü tamamen aynı ise, 
bu durum tam uyum _(exact match)_ olarak ele alınır. 
Ancak aşağıdaki durumlar da tam uyum olarak ele alınır:

+ Argüman olan nesne bir sol taraf değeri yani bir nesne ise, parametre değişkenine kopyalanacak değerin bu nesneden alınması.
Bu duruma sol taraf değerinden sağ taraf değerine dönüşüm denir. _(L-value to R-value transformation)_

+ Parametre değişkeninin bir gösterici olması ve fonksiyonun da aynı türden bir dizinin ismi ile çağrılması. 
Dizi isimlerinin bir ifade içinde kullanılmaları durumunda otomatik olarak dizinin ilk elemanının başlangıç adresine dönüştürüldüğünü 
_(array to pointer conversion_ biliyorsunuz.

+ Parametre değişkeninin bir fonksiyon göstericisi _(function pointer)_ olması ve 
fonksiyonun da aynı türden bir fonksiyonun ismi ile çağrılması. 
Fonksiyon isimlerinin bir işleme sokulduğunda işlem öncesinde otomatik olarak fonksiyonun adresine dönüştürüldüğünü hatırlayın. 
_(function to pointer conversion)_

+ Fonksiyon parametre değişkeninin, _const_ nesne göstericisi olması 
ve fonksiyonun aynı türden ancak _const_ olmayan bir adres ile çağrılması _(const qualification conversion)_.

__Yükseltme _(promotion)_ durumu__

Yükseltme aşağıdaki durumları kapsar:

__char, signed char, unsigned char, bool, short, unsigned short türlerinden int türüne yapılacak dönüşüm.__ 
Bu duruma "_int_ türüne yükseltme" _(integral promotion)_ denir. 
_int_ türünden küçük türler _int_ türüne yükseltilirler.

```
void func(int);

int main()
{
	func('A');	_// yükseltme (integral promotion)_
	func(true); 	_// yükseltme (integral promotion)_
	//...
}
```

__float türünden _double_ türüne yapılan dönüşüm.__

```
void func(double);

int main()
{
	float fx = 2.3f;
	//...
	func(fx); _//yükseltme_
	//...
}
```

_float_ türünden _long double_ türüne ya da _double_ türünden _long double_ türüne yapılan dönüşümler "yükseltme" değildir.
Bir numaralandırma _(enum)_ türünden o numaralandırma türüne baz olan _(underlying type)_ türe yapılan dönüşüm de yükseltme olarak değerlendirilir. 
Aşağıdaki örneği inceleyin:

```
enum Color
{
	Blue, Red, Green, Yellow
};

void f(int);	//1
void f(long);	//2
void f(double);	//3

int main()
{
	f(Blue);
}
```

_Color_ türünün baz türü _int_ türüdür. 
Bu durumda _1_ numaralı fonksiyon çağrılacaktır.

__Diğer standart dönüşümler__

Dilin kurallarınca geçerli olan ve örtülü _(implicit)_ olarak yapılabilen diğer dönüşümlerdir. 
Standart dönüşüm _(standard conversions)_ başlığı altında toplanan _5_ grup dönüşüm söz konusudur:

__1. Tamsayı türlerine ilişkin dönüşümler__
Bir tamsayı türünden ya da bir numaralandırma (enum) türünden başka bir tamsayı türüne yapılan dönüşümler.

__2. Gerçek sayı dönüşümleri__
Bir gerçek sayı türünden başka bir gerçek sayı türüne yapılan dönüşümler.

__3. Gerçek sayı türleri ile tamsayı türleri arasında yapılan dönüşümler.__

__4. Adres türlerine ilişkin dönüşümler__

_0_ tamsayı sabitinin herhangi türden bir adres türüne kopyalanması için _nullptr_ sabitine dönüştürülmesi.
_void_ türden olmayan herhangi bir adresin _void_ türden bir adrese dönüştürülmesi.

__5. bool türüne yapılan dönüşümler__

Herhangi bir tamsayı, gerçek sayı, numaralandırma ya da adres türünden _bool_ türüne yapılan dönüşümler.

Aşağıda standart dönüşümlere ilişkin bazı örnekler veriliyor:

```
void func(int);
void foo(long);
void f(float);
void pf(int _);
void vfunc(void _);

int main()
{
	int x = 10;
	foo(x);		//standart dönüşüm (int türden long türüne)
	foo('A');	//standart dönüşüm (char türden long türüne)
	func(20U);	//standart dönüşüm (unsigned int türünden int türüne)
	f(7.5);		//standart dönüşüm (double türden float türüne)
	pf(0);		//standart dönüşüm (0 tamsayı sabitinin bir göstericiye kopyalanması
	vfunc(&x)	//standart dönüşüm (int _ türden void _ türüne)
}
```

Yüklenmiş fonksiyon çözümlemesine ilişkin bir örnek verelim:

```
int foo(int);		//1
int foo(double);	//2
void foo(char);		//3
long foo(long);		//4
void foo(int, int);	//5
void foo(char _);	//6
void foo(int _);	//7

void func()
{
	foo(10);
	foo(3.4F);
	foo((double _)0x1FC0);
	foo(6U);
}
```

_func_ fonksiyonu içinde yapılan fonksiyon çağrılarını teker teker ele alalım:

```
foo(10);
```

Çağrısı için

+ 1, 2, 3, 4, 5, 6, 7 numaralı fonksiyonlar adaydır.
+ 1, 2, 3, 4 numaralı fonksiyonlar uygundur.
+ Tam uyum sağladığı için, _1_ numaralı fonksiyon en uygun olanıdır.

```
foo(3.4F)
```

Çağrısı için

+ 1, 2, 3, 4, 5, 6, 7 numaralı fonksiyonlar adaydır.
+ 1, 2, 3, 4 numaralı fonksiyonlar uygundur.
+ Yükseltme durumu olarak değerlendirildiğinden _2_ numaralı fonksiyon en uygun olanıdır.

```
foo((double _) 0x1FC0)
```
Çağrısı için
1, 2, 3, 4, 5, 6, 7 numaralı fonksiyonlar aday işlevlerdir. 
Uygun fonksiyon yoktur _(no match)_. 
Fonksiyon çağrısı geçersizdir.

```
foo(6U)
```

Çağrısı için
+ 1, 2, 3, 4, 5, 6, 7 numaralı fonksiyonlar adaydır.
+ 1, 2, 3, 4 numaralı fonksiyonlar uygundur.
+ 1, 2, 3 ve 4 numaralı fonksiyonlar için standart dönüşüm uygulanabilir. 
Çift anlamlılık hatası _(ambiguity)_ söz oluşur.
Fonksiyon çağrısı geçersizdir.

___const_ yüklemesi _(const overloading)___

Aşağıdaki bildirimlere bakalım:

```
void func(int _ptr);		//1
void func(const int _ptr);	//2
```

Bu bir fonksiyon yüklemesidir _(function overloading_). 
Bu şekilde yapılan bir yüklemeye "_const_ yüklemesi" _(const overloading)_ denir.
Her iki işlevin de parametresi bir gösterici (pointer). 
Ancak ikinci işlevin parametresi gösterdiği yer _const_ olan bir gösterici _(pointer to const object / low level const)_. 
_func_ işlevi bir adresle çağrıldığında derleyici hangi işlevin çağrıldığını nasıl anlayacak? 
Eğer fonksiyon çağrısı bir _const_ nesne adresi ile yapılırsa zaten birinci func işlevi uygun _(viable)_ olmaktan çıkıyor. 
Bu durumda çağrılan ikinci fonksiyon olur. 
Çağrının _const_ olmayan bir nesne adresi ile yapılması durumunda her iki fonksiyon çağrıya uygun olsa da dilin kurallarına göre 
çağrılan birinci fonksiyon olur. 
Aşağıdaki koda bakalım:

```
void func(int _ptr);		//1
void func(const int _ptr);	//2

int main()
{
	int x = 10;
	const int y = 20;

	func(&x); //1
	func(&y); //2
}
```

Bu durumu pratik olarak şöyle ifade edebiliriz:
Fonksiyon _const_ nesne adresi ile çağrılırsa parametresi _const T*_ olan
Fonksiyon _const_ olmayan nesne adresi ile çağrılırsa parametresi _T*_ olan fonksiyon çağrılır.

_const_ yüklemesi gösterici semantiği ile yapılabildiği gibi referans semantiği ile de gerçekleştirilebilir. 
_T_ bir tür olmak üzere:

```
void func(T &);		//1
void func(const T &);	//2
```

Yukarıdaki bildirimlere göre _func_ işlevine _T_ türünden _const_ olmayan bir nesne gönderildiğinde birinci fonksiyon 
_const_ bir nesne gönderildiğinde ise ikinci fonksiyon çağrılır.

İşlevlerin parametreleri gösterici ya da referans değilse bildirimde kullanılan const anahtar sözcüğü bir anlam farklılığı yaratmaz:

```
void func(int x);		//1
void func(const int x);		//2  yeniden bildirim (function redeclaration)
```

Yukarıdaki bildirimler bir fonksiyon yüklemesi oluşturmuyor. 
Derleyici ikinci deyimi bir "yeniden bildirim" _(function redeclaration)_ kabul eder. 
Eğer her iki fonksiyon da tanımlansaydı sentaks hatası oluşurdu. 
Aşağıda yer alan programda çift anlamlılık hatasının olduğu bir başka tipik durum gösteriliyor:

```
#include <iostream>

void foo(int &r)
{
	std::cout << "void foo(int &)\n";
}

void foo(int x)
{
	std::cout << "void foo(int)\n";
}

int main()
{
	int a = 20;

	//foo(a); çift anlamlılık hatası
	foo(5); //void foo(int);
}
```

Yukarıdaki kodda tanımlanan birinci _foo_ fonksiyonunun parametre değişkeni _int &_ türünden iken ikinci _foo_ 
fonksiyonunun parametre değişkeni _int_ türdendir. 
Bunlar farklı işlevdir.
_main_ fonksiyonu içinde yorum satırı içine alınmış birinci fonksiyon çağrısı çift anlamlılık hatasına neden olur.
Yani bu durumda değerle çağrı _(call by value)_ ya da referansla çağrı _(call by reference)_ birbirine göre öncelikli değildir. 
Ancak ikinci fonksiyon çağrısında argüman olan ifade bir sabittir yani bir sağ taraf değeri ifadesidir.
Bir sol taraf referansına bir sağ taraf değeri ifadesi ile ilk değer verilemeyeceğine göre çağrılabilecek tek bir fonksiyon vardır. 
İkinci fonksiyon çağrılacaktır.
Birinci fonksiyonun parametre değişkeni _const_ sol taraf referansı olsaydı ikinci fonksiyon çağrısı da 
çift anlamlılık hatası _(ambiguity)_ durumuna düşerdi.

Yüklemeye konu iki fonksiyonun parametresi için de standart dönüşümün olduğu durumlarda oluşan çift anlamlılık hatası 
programcıları yanıltabilmektedir. 
Aşağıdaki koda bakalım:

```
void f(long double);
void f(char);

void func()
{
	f(12.5); //geçersiz
}
```

Yukarıdaki kodda _func_ fonksiyonu içinde yapılan çağrıda çift anlamlılık hatası oluşur. 
_long double_ parametreli fonksiyonun çağrılması gerektiğini düşünebilirsiniz. 
Argüman olan ifade _double_ türündendir. 
_double_ türünden _long double_ türüne ve _char_ türüne yapılacak dönüşümlerin her ikisi de standart dönüşümdür. 
Aralarında bir seçilebilirlik farkı yoktur. 
Çözümlemeye ilişkin bazı özel durumları da inceleyelim:

```
void f(void _);		//1
void f(bool);		//2

void func()
{
	int x = 10;
	f(&x);
	//...
}
```

Yukarıdaki kodda _f_ isimli fonksiyona yapılan çağrıda her iki fonksiyon için de yapılacak dönüşümün kalitesi aynı. 
Bu durumda parametresi _void__ olan fonksiyon (birinci fonksiyon) çağrılır.

_C++11_ standartları ile, dile eklenen sağ taraf referanslarına ilişkin de yeni çözümleme kuralları getirilmiştir.

```
void f(const T &);
void f(T &&);
```

Yukarıdaki fonksiyonlar farklı fonksiyonlardır. 
Fonksiyon yüklemesi söz konusudur. 
_f_ fonksiyonuna çağrı yapıldığında hangisinin seçileceği çağrıda kullanılacak argüman olan ifadenin değer kategorisine _(value category)_ bağlıdır. 
Fonksiyon bir sol taraf değeri ifadesi ile çağrılırsa  _T&&_ parametreli fonksiyon zaten uygun olmayacağından const _T&_ parametreli olan çağrılır. 
Fonksiyon bir sağ taraf değeri ifadesi ile çağrılırsa  her iki fonksiyon da uygundur. 
Ancak bu durumda _T&&_ parametreli fonksiyonun seçilir.

Yüklenen fonksiyonların birden fazla parametre değişkenine sahip olması durumunda bir fonksiyonun seçilebilmesi için şu şartları sağlaması gerekir:

+ Seçilecek fonksiyon az bir parametrede diğer fonksiyonlardan daha iyi olmalıdır.
+ Seçilecek fonksiyon kalan parametrelerde diğer fonksiyonlardan daha kötü olmamalıdır.
+ Fonksiyonlardan hiçbiri bu koşulu sağlamıyorsa çift anlamlılık hatası oluşur.

Aşağıdaki örneğe bakalım:

```
void f(int, double, float);			//1
void f(int, int, char);				//2
void f(double, long, long double);		//3

void func()
{
	f(12u, 'C', 8.6);
}
```

_f_ fonksiyonu yapılan çağrı _2_ numaralı fonksiyona bağlanır. 
Fonksiyonların parametrelerine yapılan dönüşümler söz konusu olduğunda _2._ fonksiyonun _2_. 
parametresine yapılan dönüşüm diğerlerine göre daha iyidir. 
_2._ fonksiyonun diğer parametrelerine yapılan dönüşümler de diğerlerinden daha kötü değildir. 
Bir de şu örneğe bakalım:

```
void f(double, double);		//1
void f(int, int);		//2

void func()
{
	f(1.2, 'A');
}
```

_f_ fonksiyonuna yapılan çağrı çift anlamlılık hatası oluşturur. 
Birinci parametre için birinci fonksiyon ikinci parametre için ikinci fonksiyon daha iyidir.

Hangi fonksiyonun çağrılmış olduğunu saptamak derleme zamanında yapılan bir işlemdir. 
Yani kaynak kod derlenip hedef kod _(object code)_ haline getirildiğinde artık hangi fonksiyonun çağrılmış olduğu bilinmektedir. 
Çünkü çağrılan fonksiyonun kimliği bir şekilde hedef koda yazılmış olur. 
Başka bir deyişle _"fonksiyon yüklemesi"_ için programın çalışma zamanı açısından bir ek maliyet söz konusu değildir. 
Ancak böyle bir araç derleyici üzerindeki yükü de artırır. 
Derleyici programın boyutunun büyümesine neden olur.
Küçük bir dil olarak tasarlanan _C_ dilinde bu aracın bulunmamasının önemli bir nedeni de budur. 
Aynı isimli fonksiyonlar gereksiz yere tanımlanmamalıdır. 
Fonksiyon yüklemesinin ana amacı özünde aynı işi yapan ancak kodları farklı fonksiyonları aynı isim altında soyutlamaktır. 
Birden fazla fonksiyonun aynı ismi taşıması kullanıcı kodların işini kolaylaştırmaya yöneliktir. 
Farklı işler gören fonksiyonların, aynı ismi taşıması kodun okunmasını zorlaştırır.

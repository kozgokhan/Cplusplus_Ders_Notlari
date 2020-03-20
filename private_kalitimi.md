_Java_, _C#_ gibi dillerden biraz farklı olarak _C++_ dilinde 3 ayrı kalıtım _(inheritance)_ biçimi var: _public_, _private_ ve _protected_ kalıtımları. Aslında bunlardan yalnızca _public_ kalıtımı, _Nesne Yönelimli Programlama_'daki kalıtım kavramına karşı geliyor. _public_ kalıtımı ile ingilizcede "_is a_" ilişkisi denilen modeli gerçekliyoruz. _private_ ve _protected_ kalıtımları ise tamamen farklı amaçlarla kullanılıyorlar. Daha sonra bu konuya geri dönmek üzere önce _private_ kalıtımına ilişkin kuralları bir gözden geçirelim:

_C++_'da kalıtımın hiçbir biçiminde taban sınıfın _private_ bölümüne türemiş sınıfların erişim hakkı yok. Yani taban sınıfın _private_ bölümü hem taban sınıfın _(parent class)_ kendi müşterilerine _(clients)_ hem de taban sınıftan kalıtım yoluyla elde edilecek sınıflara _(child classes)_ kapalı. _private_ kalıtımında taban sınıfın _public_ ve _protected_ bölümleri türemiş sınıfın _private_ bölümü gibi ele alınıyor.  Taban sınıfın _public_ ya da _protected_ bölümüne türemiş sınıf müşterilerinin erişim hakkı yok. Aşağıdaki kodu inceleyelim:

```
class Base {
public:
	void pub_func();
protected:
	void pro_func();
private:
	void pri_func();
};

class Der : private Base {
public:
	void derfunc()
	{
		pub_func(); //gecerli
		pro_func(); //geçerli
		pri_func(); //geçersiz
	}
};

int main()
{
	Der myder;

	myder.pri_func();  //geçersiz
	myder.pro_func();  //geçersiz
	myder.pub_func();  //geçersiz
}
```

_Der_ sınıfı _Base_ sınıfından _private_ kalıtımı yoluyla oluşturulmuş. _':'_ atomundan sonra _private_ anahtar sözcüğü kullanılmasaydı da yine kod geçerli olacak ancak _private_ kalıtımı anlamına gelecekti. Yani sınıflar söz konusu olduğunda varsayılan kalıtım biçimi _private_:

```
class Base {
//
};

class Der : Base {  //private kalıtımı
//
};

Yapılar için ise varsayılan kalıtım biçimi public:

class Base {
//
};

struct Der : Base {  //public kalıtımı
//
};
```

_public_ kalıtımında türemiş sınıf türünden bir nesne aynı zamanda taban sınıf türünden bir nesne kabul edildiğinden türemiş sınıftan taban sınıfa _(upcasting)_ dönüşüme izin veriliyor:

```
class Base {
	//
};

struct Der : public Base {
public:
	
};

int main()
{
	Der myder;
	Base *base_ptr = &myder;  //geçerli
	Base &base_ref = myder;  //geçerli
}
```

Ancak _private_ kalıtımında bu tür dönüşümler yalnızca taban sınıftan türeyen sınıflar ve bunların arkadaşları için geçerli:

```
class Base {
	//
};

struct Der : private Base {
public:
	void func()
	{
		Der myder;
		Base *base_ptr = &myder;  //geçerli
		Base &base_ref = myder;  //geçerli
	}	
};

int main()
{
	Der myder;
	Base *base_ptr = &myder;  //geçersiz
	Base &base_ref = myder;  //geçersiz
}
```

Kalıtım biçiminin _public_, _protected_ ya da _private_ olması taban sınıfın _private_ olmayan sanal işlevlerinin türemiş sınıflar tarafından ezilmesine _(override)_ engel bir durum değil:

```
class Base {
public:
	virtual void func();
	//
};

struct Der : private Base {
        void func()override;
	//
};
```

Yukarıdaki kodda _Base_ sınıfından _private_ kalıtımı yoluyla elde edilen _Der_ sınıfı _Base_ sınıfının _public_ sanal işlevi olan _func_ işlevini ezmiş _(override etmiş)_.

#### private kalıtımı neden kullanılır?
_private_ kalıtımına ilişkin kuralları gözden geçirdiğimize göre artık bu kalıtım biçiminin ne işe yaradığını ya da ne fayda sağladığını incelemeye başlayabiliriz. _public_ kalıtımı ile bir taban sınıfın _(parent class)_ _public_ arayüzünü devralan yeni bir sınıf _(child class)_ oluşturuyoruz.  Böyle iki sınıf arasındaki ilişkiye İngilizcede popüler olarak _"is a"_ ilişkisi deniyor. 

Eğer _X_ sınıfı _Y_ sınıfından kalıtım yoluyla elde edildi ise<br>
Her bir _X_ aynı zamanda bir _Y_'dir. Yani _Y_ nesnesi gereken her yerde bir _X_ nesnesi de kullanılabilir:

* Her satış görevlisi bir çalışandır<br>
* Her aslan bir hayvandır.<br>
* Her politikacı bir yalancıdır. <br>

Bu ne anlama geliyor? Çalışan gereken her yerde bir satış görevlisi de kullanılabilir. Bir hayvan gereken her yerde bir aslan kullanılabilir. Yalancı gereken her yerde bir politikacı bu işi görebilir. Ancak _private_ ve _protected_ kalıtımları aslında bambaşka bir amaçla kullanılıyorlar. Yani artık _"is a"_ ilişkisi söz konusu değil.

Bir nesnenin başka bir nesneyi onun sahibi olarak kullanmasına ingilizcede _"composition"_ deniyor. _Composition_, nesne yönelimli programlamanın en önemli araçlarından biri. Sınıflar arasında _composition_ gösteren bir ilişkiye ingilizcede popüler olarak _"has a"_ ilişkisi deniyor:

Eğer her _X_'in bir _Y_ türünden bir öğesi var ise, bir _X_ nesnesi belirli hizmetleri kendi müşterilerine sağlamak için sahibi olduğu _Y_ nesnesini kullanabilir:

* Bilgisayarın ana kartı var.
* Savaşçının silahları var.
* Arabanın motoru var

_C++_ gibi bir dilde _composition_ ilişkisini kodlamanın en basit ve en sık tercih edilen yolu bir sınıfın başka bir sınıf türünden veri öğesi ya da öğelerine sahip olması. Gelin bu yola "içerme" _(containment)_ diyelim. Her arabanın bir motoru var, değil mi?

```
class Engine {
public:
	void start();
	////
};

class Car {
	Engine its_engine;
public:
	void start()
	{
		its_engine.start();
	}
	///
};
```

Yukarıdaki kodda _Car_ sınıfının _Engine_ sınıfı türünden bir öğesi var. 
_Car_ sınıfı kendi müşterilerine hizmet veririken bu iş için _Engine_ sınıfının _public_ arayüzünü kullanarak _Engine_ sınıfının kodlarından faydalanabilir. 
Eğer _Car_ sınıfını _Engine_ sınıfından _private_ kalıtımı ile elde etsek de sonuç benzer olacak. 
Yani duruma _Car_ sınıfının işlevselliği açısından içerme ile _private_ kalıtımı arasında bir fark yok.

```
class Engine {
public:
	void start();
	////
};

class Car : private Engine{
public:
	using Engine::start;
	///
};
```

Şimdi bu iki yapıyı, yani içerme ile _private_ kalıtımını birbiriyle karşılaştıralım. Önce ortak noktalara değinelim:

1. İki yapıda da her _Car_ nesnesinin içinde bir _Engine_ nesnesi var ve _Car_ nesnesi bu engine nesnesini kullanabiliyor.
2. İki yapıda da _Car_ sınıfının müşterilerine için _Car *_ türünden _Engine *_ türüne dönüşüm izini verilmiyor. (Çünkü her araba aynı zamanda bir motor değildir).
3. İki yapıda da _Car_ sınıfı _Engine_ sınıfının _public_ arayüzünü kendi arayüzüne eklemiyor.
4. İki yapıda da _Car_ sınıfı _Engine_ sınıfının _public_ arayüzünün istediği kısım ya da kısımlarını kendi _public_ arayüzüne seçerek katabilir.

Şimdi de farklılıklara bakalım:
1. Eğer bir arabanın birden fazla motoru olacak ise tercihimiz içerme olurdu. Bu durumda _private_ kalıtımının kullanılması çoklu kalıtım gerektirecekti.

2. _private_ kalıtımında _Car_ sınıfının kendi kodlarına ve arkadaşlarına _Car *_ türünden _Engine *_ türüne dönüşüm izni veriliyor. Ancak "içerme" durumunda böyle bir izin söz konusu değil.

3. _private_ türetmesinde _Car_ sınıfı _Engine_ sınıfının _protected_ bölümüne erişebiliyor. Ancak "içerme" durumunda _Car_ sınıfının _Engine_ sınıfın _protected_ bölümüne erişim hakkı yok.

4. İçerme durumunda _Engine_ sınıfının _public_ arayüzündeki bir işlevi _Car_ sınıfının public arayüzüne katmak için bu işlevi çağıracak yeni bir işlev _(forwarding function)_ oluşturmak gerekiyor:

```
void Car::start()
{
        itsEngine.start();
}
```

_private_ kalıtımında ise bu işi bir sınıf içi _using_ bildirimiyle gerçekleştirebiliyoruz:

```using Engine::start;```

5. _private_ türetmesinde _Car_ sınıfı _Engine_ sınıfının sanal işlevlerini ezebiliyor ama içerme durumunda bu doğrudan mümkün değil. 
Bu dolaylı olarak şöyle gerçekleştirebilir:

```
class Engine {
public:
	virtual void maintain();
};

class Car {
	class SpecialEngine : public Engine{
		void maintain()override;
	};
	SpecialEngine m_se;
	//
};
```

Şimdi önemli soru şu: _Composition_ gereken bir durumda oluşturacağımız sınıfa istediğimiz işlevselliği hem _"içerme"_ hem de _"private kalıtımı"_ ile sağlayabiliyoruz. 
Bu durumda neden _private_ kalıtımını tercih edelim? _Composition_ açısından baktığımızda "içerme", _private_ kalıtımının bir alt kümesi olarak görülebilir. _Composition_'ı gerçeklerken _private_ kalıtımı bize daha fazla araç sunuyor. İçerme yerine _private_ kalıtımını tercih etmemizi gerektiren nedenler şunlar olabilir:

* Kullanılacak sınıfın _protected_ kısmına (özellikle de _protected_ kurucu işlevlere) erişmek istiyoruz.
* Kullanılacak sınıfın sanal işlev ya da işlevlerini işlevlerini ezmek _(override)_ istiyoruz (ya da buna mecburuz). Eğer arayüzünü kullanacağımız sınıf soyut _(abstract)_ ise bu sınıfın tüm saf sanal _(pure virtual)_ işlevlerini ezmez isek bizim oluşturduğumuz sınıf da soyut olacaktı. Sınıfımız türünden nesneler oluşturabilmek _(instantiate)_ için somut bir sınıf oluşturmak zorundayız.

Eğer bu iki olanaktan faydalanma gibi bir amaç söz konusu değilse tercih edilmesi gereken "içerme" yapısı. Kalıtıma göre sınıfların birbirine bağımılığı bu yapıda daha az. 
Diğer taraftan _private_ kalıtım tek bir öğe sayısıyla sınırlı.

_private_ kalıtımı _OOP_ açısından bir kalıtım değil. Kalıtımdaki amaç taban sınıf olarak alınan sınıfın kodlarını kullanmak. 
_A_ sınıfını _B_ sınıfından _private_ kalıtımıyla oluşturmak _A_'yı _B_ türünden yapmıyor ve _A_'ya _B_'nin arayüzünü katmıyor. 
Bu yüzden _private inheritance_ tasarım ile değil gerçekleştirim _(implementasyon)_ ile ilgili.

Eğer içerme ile _private_ kalıtım arasında tereddütte kalıyorsanız şu ilkeye bağlı kalabilirsiniz: 
Kullanabildiğiniz her yerde içerme yapısını kullanın yalnızca zorunlu olduğunuz durumlarda _private_ kalıtımı kullanın.

_private_ kalıtımın içermeye tercih edileceği bir senaryo daha var:

C++'da statik olmayan _(non static)_ bir veri öğesine sahip olmayan, yani boş sınıflar _(empty class)_ olabiliyor. 
Standart kütüphane de bazı nedenlerden boş sınıfları kullanıyor. 
Boş bir sınıf türünden bir sınıf nesnesi tanımlandığında derleyici belirli işlemleri yapabilecek kodları üretebilmek için bu sınıf nesnesine bellekte bir yer ayırmak zorunda. 
Böylesi durumlarda derleyiciler boş sınıf nesneleri için tipik olarak _1_ byte'lık bir yer ayırıyorlar.
Ancak boş bir sınıf nesnesi başka nesnelerle birlikte aynı bellek bloğunda yer aldığında hizalama _(alignment)_ nedeniyle daha fazla bir bellek alanı kullanılabiliyor. 
Aşağıdaki koda bakalım:

```
#include <iostream>

class A {
	
};

class B {
	A a;
	int x;
};

int main()
{
	std::cout << "sizeof(int) = " << sizeof(int) << "\n";
	std::cout << "sizeof(A)   = " << sizeof(A) << "\n";
	std::cout << "sizeof(B)   = " << sizeof(B) << "\n";
}
```

Benim çalıştığım sistemde yukarıdaki programın çıktısı şu şekilde oldu :

```
sizeof(int) = 4
sizeof(A)   = 1
sizeof(B)   = 8
```

Oysa derleyiciler kalıtımla boş bir sınıftan yeni bir sınıf oluşturulduğunda, popüler olarak _"boş taban sınıf optimizasyonu"_ olarak bilinen _(EBO - empty base optimization)_ bir tekniği uygulayarak boş taban sınıf nesnesi için bir yer ayırmıyorlar:

```
#include <iostream>

class A {

};

class B : private A{
	int x;
};

int main()
{
	std::cout << "sizeof(int) = " << sizeof(int) << "\n";
	std::cout << "sizeof(A)   = " << sizeof(A) << "\n";
	std::cout << "sizeof(B)   = " << sizeof(B) << "\n";
}
```

Benim çalıştığım sistemde yukarıdaki programın çıktısı şu şekilde oldu :

```
sizeof(int) = 4
sizeof(A)   = 1
sizeof(B)   = 4
```

Bu şu anlama geliyor: 
Eğer sınıfınız boş bir sınıf nesnesini kullanacak ise bu nesneyi sınıfınızın veri öğesi yapmak (içerme) yerine, sınıfınızı bu nesnenin ait olduğu boş sınıf türünden _private_ kalıtımı ile oluşturmak, sınıf nesneleri için ihtiyaç duyulan bellek alanını azaltabilir.

## private kalıtımının "is a" ilişkisi için kullanılması
Sözünü edeceğimz duruma popüler olarak _"kısıtlı"_ ya da _"kontrollü çok biçimlilik"_ deniyor.
Bazı durumlarda çalışma zamanı çok biçimliliğinden yalnızca seçilmiş belirli işlevlerin faydalanmasını ve bu aracın diğer işlevlere kapatılmasını istiyoruz. 
Aşağıdaki kodu inceleyelim:

```
class Base {
public:
	virtual void vfunc();
};

class Der : public Base {
public:
	void vfunc()override;
};

void gfunc(Base &r)
{
	r.vfunc();
	//
}


void foo1()
{
	Der der;
	gfunc(der); //geçerli
	//
}

void foo2()
{
	Der der;
	gfunc(der); //geçerli
	//
}
```

_Der_ sınıfı _public_ kalıtımıyla _Base_ sınıfından oluşturulmuş ve taban sınıfın sanal _vfunc_ işlevini ezmiş _(override etmiş)_. 
Global _gfunc_ işlevi _Base_ nesnelerini çokbiçimli _(polimorfik)_ olarak kullanıyor. 
Şimdi amacımızın şunu sağlamak olduğunu düşünelim: Yalnızca _foo1_ işlevi çok biçimlilikten faydalansın fakat diğer işlevler, örneğin _foo2_ işlevi, bu yapıdan faydalanamasın. 
Yukarıdaki kodda hem _foo1_ işlevinde hem de _foo2_ işlevinde _gfunc_ işlevine _Der_ türünden nesnelerle yapılan çağrılar geçerli. 
Oysa biz _foo2_ işlevine (ve muhtemelen bazı başka işlevlere) kalıtımla elde edilmiş _Der_ sınıf nesnelerinin gönderilmesini geçersiz kılmak istiyoruz.
Reçete hazır: _Der_ sınıfı _Base_ sınıfından _private_ kalıtımıyla oluşturulsun ve çok biçimlilikten faydalanacak işlevlere arkadaşlık _(friend)_ versin. 
_private_ kalıtımında türemiş sınıftan _(child class)_ taban sınıfa _(Base class)_ otomatik _(implicit)_ tür dönüşümlerinin yalnızca türemiş sınıfların kodlarına ve bu sınıfların arkadaşları olan kodlara tanınan bir hak olduğunu hatırlayalım:

```
class Base {
public:
	virtual void vfunc();
};

class Der : private Base {
public:
	void vfunc()override;
	friend void foo1();
};

void gfunc(Base &r)
{
	r.vfunc();
	//
}

void foo1()
{
	Der der;
	gfunc(der); //geçerli
	//
}

void foo2()
{
	Der der;
	gfunc(der); //geçersiz
	//
}
```

Bu kez _Der_ sınıfı _Base_ sınıfından _private_ kalıtımıyla oluşturuldu. 
_Der_ sınıfı yine _Base_ sınıfının sanal işlevini eziyor. 
Kalıtımın _private_ olmasının taban sınıfın sanal işlevlerini ezmeye engel olmadığını hatırlayalım. 
_Der_ sınıfı artık çok biçimlilikten faydalanacak işlevelere arkadaşlık verebilir. 
_Der_ sınıfı içinde yapılan

```
friend void foo1();
```

bildirimiyle _foo1_ işlevine arkadaşlık veriliyor. 
_Der_ sınıfınına arkadaş olan işlevlerin _Der_ sınıfından _Base_ sınıfına dönüşüm izninin olduğunu diğer işlevlerin bu hakka sahip olmadığını biliyoruz. 
_foo1_ işlevinde yapılan

```
gfunc(der);
```

çağrısı geçerli iken _foo2_ işlevi içinde yapılan aynı çağrı geçerli değil.

# Referans Semantiği

Referans semantiği gösterici _(pointer)_ semantiğine alternatif olan ancak C++ dilinin diğer araçlarına daha uygun bir yapıdır.
_C++_ programcılarına verilen ilk tavsiyelerden biri şöyledir: 
> "Referans kullanabildiğin her yerde referans yalnızca zorunlu olduğun yerlerde gösterici _pointer_ kullan."

Eski _C++_'ta yalnızca tek bir referans kategorisi vardı. 
_C++11_ ile birlikte dile "sağ taraf referansı" _(R value reference)_ olarak isimlendirilen ikinci bir referans kategorisi eklendi. 
Sağ taraf referansları, C++ dilinde daha önce doğrudan mümkün olmayan _taşıma semantiği_"ni _(move semantics)_ ve "mükemmel gönderim"i _(perfect forwarding)_ gerçekleştirmek üzere dile eklenmiştir.

Modern C++ referansları ikiye ayırıyor:

+ _L value reference_ (Sol taraf referansları)
+ _R value reference_ (Sağ taraf referansları)

Biz şimdilik yalnızca sol taraf referanslarını inceleyeceğiz. 
Sağ taraf referansları çok önemli bir konu olmakla birlikte ayrı bir yazımızın konusu olacak. 
Aksi belirtilmedikçe bundan sonra (şimdilik) referans dendiğinde sol taraf referansı anlaşılmalıdır.

## referans nedir?
Refererans bir nesnenin yerine geçen bir isim olarak düşünülebilir. 
Öyle ki biz referans olan ismi kullandığımızda aslında o ismin bağlandığı (yerine geçtiği) nesneyi kullanmış oluyoruz. 
Referanslar & atomu _(declarator)_ ile tanımlanır. _T_ bir tür olmak üzere

```
T &r = nesne;
```

_r_, "nesne"nin yerine geçen bir referanstır.

referanslara ilk değer vermek _(initialize)_ zorunludur.

```
int x = 10;
int &r = x;
```

İlk değer verilmeden bir referansın tanımlanması kesinlikle geçersizdir.

```
int main()
{
	int &r;  //geçersiz
}
```

Bir referansın aynı türden bir nesneye bağlanması zorunludur.

```
void func
{
	double dval = 10.5;
	int &r = dval; //geçersiz
}
```

Tanımlanan bir referans bir nesnenin yerine geçer. 
Artık söz konusu referans isim, kendi kapsamı _(scope)_ içinde hep aynı nesnenin yerine geçecektir. 
Tanımlanmış bir referans bir başka nesnenin yerine geçemez yani referansın daha sonra bir başka nesneye bağlanması mümkün değildir:

```
#include <iostream>

int main()
{
	int a = 10;
	int b = 20;

	int &r = a;
	r = b;

	std::cout << "a = " << a << "\n";
}
```

Yukarıdaki _main_ işlevinde tanımlanan _r_ referansı, _a_ değişkeninin yerine geçiyor. 
Daha sonra yapılan 

```
r = b;
```
ataması, _r_ referansının _b_ değişkeninin yerine geçtiği anlamına gelmiyor. 
Bu atamanın anlamı şudur: _r_ referansının bağlandığı nesneye _b_ değişkeninin değeri atanmaktadır. 
Referanslar kendisi _const_ olan göstericilere _(const pointer)_ benzetilebilir. 
Yukarıdaki kodun bir referans ile değil de bir gösterici değişken kullanılarak yazıldığını düşünelim:


```
#include <iostream>

int main()
{
	int a = 10;
	int b = 20;
	int *const ptr = &a;
	*ptr = b;
	std::cout << "a = " << a << "\n";
	
	ptr = &b; //geçersiz
}
```


Yukarıdaki örnekte _ptr_, kendisi _const_ olan bir gösterici değişkendir _(const pointer)_. 
_ptr_ değişkeninin tanımından sonra, _ptr_'ye artık başka bir nesnenin adresi atanamaz. 
Aşağıdaki kodu derleyip çalıştırınız:

```
#include <iostream>

int main()
{
	int x = 10;
	int &r = x;
	int y = 45;

	++r;

	std::cout << "x = " << x << "\n";
	r = y;  //x = y;
	cout << "x = " << x << "\n";

	std::int *ptr = &r;
	*ptr = 8754;
	std::cout << "x = " << x << "\n";
}
```

Şüphesiz birden fazla referans aynı nesneye bağlanabilir:

```
#include <iostream>

int main()
{
	int x = 10;
	int& r1 = x;
	int& r2 = x;
	int& r3 = x;
	int& r4 = x;

	++r1, ++r2, ++r3, ++r4;
	
	std::cout << "x = " << x << "\n";
}
```

_C++_'ta referansın yerine geçen referans _reference to reference_ yoktur. 
Aşağıdaki kodu inceleyin:

```
#include <iostream>

int main()
{
	int x = 10;

	int &r1 = x;
	int &r2 = r1; //int &r2 = x;

	++r1;
	r2 *= 5;
	std::cout << "x = " << x << "\n";
}
```

Herhangi türden bir nesneye bir referans bağlanabilir. 
Referansın bağlandığı nesne şüphesiz bir gösterici de _(pointer)_ olabilir.

```
#include <iostream>


int main()
{
	int x = 10;
	int y = 50;

	int *p = &x;
	int* &r = p;

	std::cout << *r << endl;
	r = &y;
	std::cout << *r << endl;
}
```

Bir yapı türünden (ileride bir sınıf türünden) nesneye de referans bağlanabilir:

```
struct Point {
	int x, y;
};

void clear_point(Point &p)
{
	p.x = 0;
	p.y = 0;
}

#include <iostream>

int main()
{
	Point p = { 12, 67 };
	std::cout << "p.x = " << p.x << " p.y = " << p.y << "\n";
	clear_point(p);
	std::cout << "p.x = " << p.x << " p.y = " << p.y << "\n";
}
```

Bir sol taraf referansı herhangi bir sol taraf değeri ifadesine bağlanabilir:

```
#include <iostream>

int main()
{
	int x = 10;
	int *ptr = &x;
	int &r = *ptr;
	++r;
	std::cout << "x = " << x << "\n";
}
```

Bir başka örnek:

```
#include <iostream>

int main()
{
	int a[4][4] = { {0, 0, 0, 0}, {1, 7, 9, 3} , {0, 0, 0, 0} , {0, 0, 0, 0} };
	int &r = a[1][2];

	std::cout << r << "\n";
}
```

Bir sol taraf referansının bir sağ taraf ifadesine bağlanması geçersizdir. 
Aşağıdaki bildirimlere bakınız:

```
int foo();

int main()
{
	int x = 10;
	int &r1 = x; //gecerli
	int &r2 = 20; //gecersiz, sağ taraf degeri
	int &r3 = x + 5; //gecersiz, sağ taraf degeri
	int &r4 = foo(); //gecersiz, sağ taraf degeri
}
```

## referansların const olarak bildirilmesi _(const referanslar)_

Bir referans _const_ anahtar sözcüğü ile tanımlanabilir. 
_const_ anahtar sözcüğü _"&"_ atomundan önce yazılır. 
Böyle referanslara _const referans_ diyeceğiz. 
_T_ bir tür olmak üzere

```
const T &r = x;
```
yazmak ile

```
T const &r = x;
```
yazmak aynı anlamdadır. 
Bu şekilde tanımlanmış bir referansın bağlandığı (yerine geçtiği) nesne, bu referans yoluyla değiştirilemez. 
Örneğin:

```
int main()
{
	int a = 10;
	const int &r = a;
	r = 20; // geçersiz
}
```

_main_ işlevi içinde yer alan

```
r = 20
```

ifadesi geçerli değildir. 
Atama aslında _a_ değişkenine yapılır. 
_const_ referans kullanılarak, referansın bağlandığı nesne değiştirilemez. 
Referans isim, salt okuma erişimli olarak işlemlere sokulabilir. 
Yukarıdaki program parçasının göstericilerle oluşturulan eşdeğer _C_ karşılığı şöyle olabilir:

```
int main()
{
	int x = 10;
	const int *const ptr = &x;	
	*ptr = 20; //Geçersiz
}
```

Burada _ptr_, hem kendisi _const_ hem de gösterdiği nesne _const_ olan bir gösterici değişkendir. 
_ptr_'nin kendisine yapılan atamalar geçersiz olduğu gibi _ptr_'nin gösterdiği nesneye yapılan atamalar da geçersizdir. 
Peki _const_ anahtar sözcüğü _'&'_ atomundan sonra, referansın isminden önce kullanabilir mi? 
Gösterici isminden önce _'*'_ atomundan sonra _const_ anahtar sözcüğü kullanıldığında, 
bu durum göstericinin kendisinin _const_ olduğu anlamına geliyordu.

```
int x;
int *const ptr = &x;
int &const r = x; 
```

Yukarıdaki kodda *r* isimli referansın tanımı doğru değildir. 
Çünkü referanslar zaten tanımları gereği yalnızca belirli bir nesneye bağlanmak üzere oluşturulur. 
Yani bir referans, zaten bir nesne ile ilk değerini aldıktan sonra artık başka bir nesnenin yerine geçemez.
Referanslar bu anlamlarıyla zaten kendileri _const_ nesnedir. 
Dolayısıyla, *const* anahtar sözcüğünün yukarıdaki biçimde kullanılmasına gereksizdir.

_const_ bir nesne adresinin, ancak gösterdiği nesne _const_ olan bir göstericiye atanabileceğini anımsayın. 
_T_ bir tür olmak üzere _const T *_ türünden _T *_ türüne örtülü _(implicit)_ tür dönüşümü _(type conversion)_ yoktur.

```
const int x = 10;
int *p1 = &x; //geçersiz
const int *p2 = &x; //geçerli
```

Benzer şekilde, _const_ olmayan bir sol taraf referansı _const_ bir nesneye bağlanamaz. 
_const_ bir nesnenin yerine ancak _const_ bir referans geçebilir.

```
const int x = 10;
int &r1 = x; //geçersiz!
const int &r2 = x; //geçerli
```

Aşağıdaki örneği inceleyin:

```
struct Data {
	int x, y, z;
};

int main()
{
	Data mydata = { 1, 2, 3 };
	const Data &rd = mydata;

	rd.x = 10;  //gecersiz

	int x = 10;
	const int &r = x;

	int a = r; //geçerli
	r = 34; //gecersiz
	++r; //gecersiz
}
```

_const_ bir sol taraf referansı bir sağ taraf değeri ifadesine _(R value expression)_ bağlanabilir.

```
void func()
{
	int &r = 10; // geçersiz!
	const int &r = 10; // geçerli
	//...
}
```

Bu durumda derleyici önce geçici bir nesneyi _(temporary object)_ sağ taraf değeri ifadesi ile oluşturur. 
Aslında referansı geçici nesneye bağlayan bir kod üretir. 
Yukarıdaki kodda _r_ referansının tanımı için derleyicinin aşağıdaki gibi bir kod ürettiğini düşünebilirsiniz:

```
const int temp = 10; // geçici nesne 10 değeri ile oluşturuluyor.
const int &r = temp; //r referansı derleyicinin oluşturduğu nesneye bağlanıyor.
```

_const_ bir sol taraf referansına başka türden bir nesne ile (eğer geçerli bir tür dönüşümü var ise) ilk değer verilmesi geçerlidir:
Derleyicilerin hemen hepsi bu durumu şüpheyle ile karşılayarak bir uyarı mesajı verirler.

```
void func()
{
	double dval = 10.5;
	const int &r = dval; // geçerli ama muhtemelen yanlış
	//...
}
```

Bu durumda yine aslında derleyici bir geçici nesne oluşturur. 
_const_ referansın bağlandığı aslında derleyicinin oluşturduğu geçici nesnedir. 
Derleyicinin aşağıdaki gibi bir kod ürettiğini düşünebilirsiniz:

```
int main()
{
	double d = 10.5;
	const int temp = (int)d;
	const int &r = temp;
}
```

## _const_ referanslar neden kullanılır?
_const_ nesne göstericileri _(pointer to const object)_ ne amaçla kullanılıyorlarsa, _const_ referanslar da aynı amaçla kullanılır. 
_const_ referanslar yoluyla bunların bağlandıkları nesneler değiştirilemez. 
Böyle referanslar, yalnızca okuma amaçlı _(access - get)_ nesnelerin yerine geçerler.

## referanslar ve fonksiyonların parametre değişkenlerinin referans olması
Referansların en fazla kullanıldığı yer referansla çağrı _(call by reference)_ fonksiyon yapısıdır. 
Bir fonksiyonun parametresi/parametreleri referans yapılır ve fonksiyon nesnenin kendisi ile çağrılır. 
Aşağıda _int_ türden iki nesneyi takas edecek _swap_c_ isimli fonksiyon gösterici semantiği ile yazılıyor:

```
void swap_c(int *p1, int *p2)
{
	int temp = *p1;
	*p1 = *p2;
	*p2 = temp;
}

#include <iostream>

int main()
{
	int x = 10, y = 34;

	swap_c(&x, &y);

	std::cout << "x = " << x << "\n";
	std::cout << "y = " << y << "\n";
}
```

Bu kez _int_ türden iki nesneyi takas edecek fonksiyon referans semantiği ile yazılıyor:

```
void swap_r(int &r1, int &r2)
{
	int temp = r1;
	r1 = r2;
	r2 = temp;
}

#include <iostream>

int main()
{
	int x = 10, y = 34;

	swap_r(x, y);

	std::cout << "x = " << x << "\n";
	std::cout << "y = " << y << "\n";
}
```

Aşağıdaki gibi bir çağrıya bakarak "değerle çağrı" ya da "referansla çağrı" olduğunu anlayamayız. 
Bunu anlamak için fonksiyonun bildirimini ve tanımını görmemiz gerekir. 
(Tabi fonksiyonun isimlendirilmesi bir ipucu verebilir)

```
int main()
{
	int x = 10;

	func(x);
}
```

Aşağıda _foo_ işlevi "değerle çağrı" biçiminde, _func_işlevi ise referansla çağrı biçiminde tanımlanıyor.

```
#include <iostream>

void foo(int a)
{
	a = 9999;
}

void func(int &a)
{
	a = 76;
}

int main()
{
	int x = 10;

	foo(x); //call by value
	func(x); //call by reference
}
```

_C++_ dilinin semantik yapısı büyük ölçüde const doğruluğuna _(const correctness)_ dayanır. 
_const_ olması gereken her varlık _const_ olmalıdır. 
_C_'den aşağıdaki iki fonksiyonun farklı iki arayüzü temsil ettiğini hatırlayalım. 
_T_ bir tür olmak üzere

```
void getter(const T *ptr);
void setter(T *ptr);
```

+ _getter_ isimli işlev _T_ türünden bir nesneye salt okuma _(read/get/access)_ amaçlı erişim talep etmektedir. 
Böyle fonksiyonlara İngilizcede _getter/accessor/get function_ gibi terimler yakıştırıldığını hatırlayalım. 
_const T*_ biçiminde tanımlanan parametrelere _"input paramete"_ de denmektedir.

+ Ancak _setter_ isimli işlev _T_ türünden bir nesneye bir değer aktarma onu değiştirme _(write/set/mutate)_ amaçlı erişim talep etmektedir. 
Yine böyle fonksiyonlara İngilizcede _setter/mutator/set function_ gibi terimler yakıştırıldığını hatırlayalım. 
_T*_ biçiminde tanımlanan parametrelere _"output parameter"_ de denmektedir.

Arayüzdeki bu farklılık referans semantiği için de geçerlidir:

```
void getter(const T &ptr);
void setter(T &ptr);
```

Bir fonksiyonun geri dönüş türü bir referans türü olabilir. 
Geri dönüş türü bir referans türü olan bir fonksiyon bir nesnenin kendisini döndürmektedir. 
Böyle bir fonksiyona yapılan çağrı ifadesi sol taraf değeridir _(L value expression)_. 
Aşağıdaki koda bakınız:

```
#include <iostream>

int g = 10;

int &func()
{
	//...
	return g;
}

int main()
{
	std::"cout << "g = " << g << "\n";
	func() = 76;
	std::cout << "g = " << g << "\n";
	++func();
	std::cout << "g = " << g << "\n";
}
```

Bir fonsiyonun referans semantiği ile aldığı nesneyi yine referans semantiği ile deri döndürmesi tipik bir durumdur:

```
T bir tür olmak üzere

T& foo(T &r)
{
	//...
	return r;
}
```

biçiminde tanımlanan bir fonksiyon aldığı nesneyi döndürmektedir.

```
int &func(int &r)
{
	//...
	r = 777;

	return r;
}
```

Adres döndüren bir fonksiyonun otomatik ömürlü bir nesne adresi döndürmesi tanımsız davranıştır _(undefined behavior)_. 
Benzer şekilde referans döndüren bir işlevin de otomatik ömürlü nesneye bağlanmış bir referans döndürmesi tanımsız davranıştır. 
Sentaks hatası olmayan bu durum derleyicilerin hemen hepsinin lojik kontrolüne takılır ve derleyiciler bir uyarı mesajı verirler.

Referans döndüren işlevler
+ Statik ömürlü bir nesneyi _(global bie değişkeni ya da statik bir yerel değişkeni)_
+ Dinamik ömürlü bir nesneyi
+ Kendisini çağıran koddan yine referans semantiği ile aldığı bir nesneyi
döndürebilir. Ancak dinamik ömürlü bir nesnenin döndürülmesi durumunda daha çok gösterici semantiği tercih edilmektedir.

Bir fonksiyonun geri dönüş değeri _const_ referans da olabilir:

```
const T &foo();
```

_foo_ işlevi bir nesnenin kendisini döndürmekle birlikte onun yalnızca okuma _(read/access/get)_ amaçlı kullanımını şart koşmaktadır.
Bu durumun gösterici semantiğindeki karşılığının

```
const T* foo();
```

olduğunu düşünebilirsiniz.

## referanslar ile göstericilerin karşılaştırılması
Her durumda olmasa da birçok durumda referans semantiği ile _pointer_ semantiği birbirlerinin yerine kullanılabilir. 
Ancak gösterici semantiği ile referans semantiğinin birebir eşdeğer olduğunu söyleyemeyiz. 

Bir referans isim her zaman aynı nesnenin yerine geçmek zorundadır. 
Bir referans ilk değerini aldıktan sonra bir başka nesnenin yerine geçemez. 
Yani referanslar kendisi const olan göstericilere _(const pointer - top level const)_ benzetilebilir. 
Oysa bir gösterici değişken *const* olmak zorunda değildir. 
Bir gösterici değişkene başka bir nesnenin adresi atanabilir.

```
	int x = 10;
	int y = 20;
	int &r = x;
	r = y;
```

işlemleri yapıldığında, _r_ referansı _x_'in yerine geçmiş olmaz, _x_ değişkenine _y_ değişkeninin değeri atanmaktadır.

_C_'de diziler fonksiyonlara adres semantiği ile gönderilebilir. 
Bir dizi üzerinde işlem yapan fonksiyon tipik olarak dizinin adresini boyutunu alır. 
Belirli türde dizi üzerinde işlem yapan bir fonksiyonun parametresi referans olabilir mi? 
Diziler fonksiyonlara referans semantiği ile gönderilebilir mi? 
Hayır! Referanslarla bu iş göstericilerle olduğu gibi yapılamaz. 
Ancak, örneğin _10_ elemanlı _int_ türden bir diziyi gösteren gösterici olduğu gibi _10_ elemanlı _int_ türden bir dizinin yerine geçecek bir referans da tanımlanabilir. 
Aşağıdaki kodu inceleyin:

```
#include <iostream>

void display(int(&r)[10])
{
	int k;

	for (k = 0; k < 10; ++k)
		std::cout << r[k] << " ";
	std::cout << "\n";
}

int main()
{
	int a[10] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
	int b[5] = {1, 2, 3, 4, 5};

	display(a);
	display(b); //geçersiz
}
```

Yukarıdaki kodda yer alan _display_ isimli fonksiyona yalnızca _10_ elemanlı bir _int_ dizi gönderilebilir. 
Örneğin _display_ fonksiyonuna _b_ dizisinin gönderilmesi bir sentaks hatasıdır.

+ Elemanları gösterici olan bir dizi _(pointer array)_ olabilir ama elemanları referans olan bir dizi olamaz.

```
#include <iostream>

int x = 1;
int y = 1;
int z = 1;
int t = 1;

int main()
{
	int *p[] = { &x, &y, &z, &t };
	
	for (int i = 0; i < 4; ++i) {
		++*p[i];
	}

	std::cout << x << y << z << t << "\n";
}
```

+ Bir göstericiyi gösteren bir gösterici _(pointer to pointer)_ olabilir ama bir referansın yerine geçen referans olamaz.
Bir gösterici değişkenin adresini bir başka göstericide tutabiliriz.

```
void func()
{
	int x = 10;
	inr y = 20;
	int* ptr = &x;
	int** p = &ptr;
	*p = &y;
	**p = 999;
	//...
}
```
Bu durumun referans semantiğinde doğrudan bir karşılığı yoktur. 
Ancak şüphesiz bir göstericinin yerine geçen bir referans olabilir. 
Aşağıdaki kodu inceleyin:

```
#include <iostream>

int main()
{
	int x = 10;
	int *ptr = &x;
	int*& r = ptr;

	*r = 20;
	std::cout << "x = " << x << "\n";
	int a[] = { 10, 20, 30, 40, 50};
	r = a; //ptr = a;
	++r;   //++ptr;
	++*r;  //++*ptr;

	std::cout << "a[1] = " << a[1] << "\n";
}
```

## _NULL_ adresi ve referanslar

C'de değeri _NULL_ adresi olan bir gösterici değişkenin hiçbir nesneyi göstermeyen bir gösterici değişken olduğunu hatırlayalım.
_NULL_ adresinin _C_'de ne kadar yaygın bir biçimde kullanıldığını biliyorsunuz. 
Bu arada _C++11_ standartları ile artık modern C++'ta _NULL pointer_ olarak _nullptr_ sabiti kullanılmaktadır. _(ileride ayrıntılı olarak göreceğiz)._ 
_nullptr_ _C++_'ta bir anahtar sözcüktür ve türü _nullptr_t_ olan bir sabittir _(constant)_.

+ C'de bir fonksiyonun bir adres döndürmesi durumunda başarısızlık belirtmek amacıyla _NULL_ adresi döndürmesi çok yaygın bir konvensiyondur. 
Örneğin standart C fonksiyonu olan *fopen* bir dosyayı açamaz ise _NULL_ adresi döndürür. 
Standart C fonksiyonu _malloc_ başarısız olduğunda _NULL pointer_ döndürür.

+ C Dilinde bir veri yapısında arama yapan fonsiyonlar aranan değeri buamadıklarında tipik olarak _NULL_ gösterici döndürürler. 
Örneğin standart bir C işlevi olan _strchr_ işlevinde bir yazı içinde bir karakter arar. 
Aranan karakteri yazıda bulursa bulduğu yerin adresini bulamazsa _NULL_ adresi döndürür.

+ Çağıran koddan bir nesne adresi isteyen bir fonksiyon kendisine _NULL_ adresi gönderilmesini çağıran koda bir seçenek olarak verebilir. 
Örneğin standart _C_ fonksiyonu olan time kendisine _NULL_ adresi gönderilirse takvim zamanı değerini yani _time_t_ türünden değeri bir nesneye yazmaz yalnızca geri dönüş değeri olarak üretir. 

+ Yine _C_'de _NULL_ adresi bir gösterici için bir bayrak değeri olarak kullanılabilir. 
Örneğin bir kontrol deyiminde bir göstericinin değerinin _NULL_ adresi olup olmamasına göre farklı işler yapılabilir.

Bu tür temaların hiçbirinde referans semantiği kullanılamaz. Hiçbir nesnenin yerine geçmeyen bir referans tanımlanamaz. 
_NULL_ referans diye bir kavram yoktur. 
Bir referans tanımlandığı zaman bir nesnenin yerine geçmelidir. 

_C++_'ta referans semantiğinin bulunması göstericilere olan tüm ihtiyacı ortadan kaldırmamıştır. 
Ancak _C++_ dilinin _C_ dilinde olmayan birçok aracıyla uyumlu olması için gösterici yerine referans kullanımı tercih edilir.

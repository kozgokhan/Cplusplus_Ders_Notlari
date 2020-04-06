_C++_ dilinin en karmaşık ve en fazla öğrenme zorluğu içeren araçlarından biri sınıfların özel işlevleri _(special members)_. 
_C++11_ standartlarına göre sınıfların _6_ özel işlevi var. 
Peki sınıfların bu işlevlerini özel yapan ne? 
Bu işlevlerin yazılması derleyiciye bırakılabiliyor. 
Yani yeterli koşullar oluştuğunda derleyici bizim adımıza sınıfın özel işlevlerini yazabiliyor. 
Önce sınıfların bu özel işlevlerini listeleyelim:

```
class A {
public:
	A(); //varsayılan kurucu işlev (default constructor)
	~A(); //sonlandırıcı işlev (destructor)
	A(const A &); //kopyalayan kurucu işlev (copy constructor)
	A &operator=(const A &); //kopyalayan atama işlevi (copy assignment operator function)
	A(A &&); //taşıyan kurucu işlev (move constructor)
	A &operator=(A &&); //taşıyan atama işlevi (move assignment operator function)
};
```

Yukarıdaki özel işlevlerden herhangi biri aşağıdaki durumlardan birinde olabilir.

1. Hiç bildirilmeyebilir _(not declared)_. Bu durumda söz konusu işlev yoktur.
2. Derleyici tarafından örtülü olarak bildirilebilir _(implicitly declared)_. 
Bu durum söz konusu işlevin derleyici tarafından _default_ edilmesi ya da _delete_ edilmesi ile mümkündür.
3. Programcı tarafından bildirilebilir _(user declared)_. 
Bu durum söz konusu işlevin programcı tarafından bildirilmesi, tanımlanması, _default_ edilmesi ya da _delete_ edilmesi ile mümkündür.

Aşağıda _A_ isimli bir sınıfın varsayılan kurucu işlevinin _(default constructor)_ programcı tarafından bildirilme senaryoları gösteriliyor:

```
//a.h
class A {
public:
	A(); //1
	A(){}; //2
	A() = default; //3
	A() = delete; //4
};
```

* 1 Sınıfın varsayılan kurucu işlevi programcı tarafından sınıf tanımı içinde bildirilmiş ancak işlevin tanımı kod dosyasına bırakılmış.
* 2 Sınıfın varsayılan kurucu işlevi programcı tarafından sınıf tanımı içinde _inline_ olarak tanımlanmış.
* 3 Sınıfın varsayılan kurucu işlevi programcı tarafından _default_ edilmiş.
* 4 Sınıfın varsayılan kurucu işlevi programcı tarafından _delete_ edilmiş.

Bir özel işlevin hiç bildirilmemesi _(not declared)_ ile _delete_ edilmesi _(deleted)_ aynı şey değildir.
Bildirilmeyen bir işlev olmayan bir işlevdir, yoktur. Bildirilmeyen işlevler işlev yükleme çözümlemesine _(function overload resolution)_ dahil edilmezler. 
_delete_ edilen işlevler ise bildirilmişlerdir; işlev yükleme çözümlemesine dahil edilirler. 
Ancak _delete_ edilmiş bir işleve bağlanmış bir çağrı sentaks hatası oluşturur. 
Aradaki farkı gösteren bir örnek verelim:

```
class A{
public:
	template <class ...Args>
	A(Args&& ...args);	
};
```

Yukarıda _A_ isimli bir sınıf için varsayılan kurucu işlev bildirilmemiş. 
Sınıfta değişken sayıda parametreli _(variadic)_ bir kurucu işlev şablonu _(template)_ bildirildiğini görüyorsunuz. 
Varsayılan kurucu işlevin çağrılması gereken bir durumda, derleyici bu şablondan hareketle varsayılan kurucu işlevin kodunu yazar. 
Şimdi de aşağıdaki tanıma bakalım:

```
class A{
public:
	template <class ...Args>
	A(Args&& ...args);
	A() = delete;	
};
```

Burada ise _A_ sınıfının varsayılan kurucu işlevi _delete_ edilmiş. 
Bu işlev var ve işlev yüklenmesi çözümlenmesine dahil edilir. 
Ancak bu işleve yapılan bir çağrı sentaks hatası olarak kabul edilir. 
Bu durumda _A_ sınıfı türünden bir nesnenin varsayılan şekilde hayata getirilmesi artık mümkün değildir.

Peki hangi durumlarda özel işlevler derleyici tarafından örtülü _(implicit)_ olarak bildirilirler?
Bir sınıf için ne bir özel işlev ne de bir (özel olmayan) kurucu işlev bildirilmiş ise sınıfın tüm özel işlevleri _default_ edilmiş olur. Yani

```
class A{
public:
};
```

gibi sınıf tanımı ile

```
class A {
public:
	A() = default;
	~A() = default; 
	A(const A &) = default;
	A &operator=(const A &) = default;
	A(A &&) = default;
	A &operator=(A &&) = default;
};
```

Yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir. 
Bir başka deyişle bu durumda sınıfın tüm özel işlevleri derleyici tarafından örtülü olarak bildirilir.

Eğer programcı tarafından sınıf için özel olmayan bir kurucu işlev bildirilirse bu durumda derleyici, varsayılan kurucu işlev dışında tüm işlevleri _default_ eder.

```
class A{
public:
    A(int);
};
```

gibi bir sınıf tanımı ile

```
class A {
public:
	A(int);
	~A() = default; 
	A(const A &) = default;
	A &operator=(const A &) = default;
	A(A &&) = default;
	A &operator=(A &&) = default;
};
```

yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir.

Programcı tarafından bir varsayılan kurucu işlevin bildirilmesi durumunda yine diğer tüm özel işlevler derleyici tarafından _default_ edilir.

```
class A{
public:
    A();
};
```

gibi bir sınıf tanımı ile

```
class A {
public:
	A();
	~A() = default; 
	A(const A &) = default;
	A &operator=(const A &) = default;
	A(A &&) = default;
	A &operator=(A &&) = default;
};
```

yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir.

Eğer programcı tarafından sınıfın sonlandırıcı işlevi bildirilirse derleyici sınıfın taşıma işlevleri _(move members)_ dışındaki tüm özel işlevlerini _default_ eder.

```
class A{
public:
    ~A();
};
```

gibi bir sınıf tanımı ile

```
class A {
public:
	A() = default;
	~A();
	A(const A &) = default;
	A &operator=(const A &) = default;
};
```


yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir. 
Bu durumda sınıfın taşıma işlevleri bildirilmemiş durumdadır. 
Aslına bakılırsa sonlandırıcı işlevin bildirilmesi durumunda derleyicinin sınıfın kopyalayan işlevlerini _default_ etmesi doğru sayılmamalıdır. 
Eğer programcı sınıfın sonlandırıcı işlevini bildirmişse muhtemelen sınıfın kopyalayan işlevlerinin de programcı tarafından yazılması için bir gereklilik söz konusudur. 
Bu yüzden bu durum _C++11_ standartlları tarafından eskimiş _(deprecated)_ olarak değerlendirilmektedir. 
Gelecekteki standartlarda bu kuralın değiştirilmesi gündemdedir. 
Yani programcı tarafından bir sonlandırıcı işlevin bildirilmesi durumunda gelecekteki standartlara göre, 
derleyici ileride bu durumda sınıfın kopyalayan işlevlerini de _default_ etmeyecektir.

Eğer programcı tarafından sınıfın kopyalayan kurucu işlevi bildirirse derleyici sınıfın kopyalayan atama işlevini ve sonlandırıcı işlevlerini _default_ eder. 
Bu durumda sınıfın varsayılan kurucu işlevi ve taşıyan işlevleri bildirilmemiş durumdadır.

```
class A{
public:
    A(const A &);
};
```

gibi bir sınıf tanımı ile

```
class A {
public:
	~A() = default; 
	A(const A &);
	A &operator=(const A &) = default;
};
```

yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir.
Eğer programcı tarafından sınıfın kopyalayan atama işlevi bildirilirse derleyici sınıfın varsayılan kurucu işlevini, 
kopyalayan kurucu işlevini ve sınıfın sonlandırıcı işlevini _default_ eder.
Bu durumda sınıfın ve taşıyan işlevleri yine bildirilmemiş durumdadır.

```
class A{
public:
    A &operator=(const A &);
};
```
gibi bir sınıf tanımı ile

```
class A {
public:
	A() = default;
	~A() = default;
	A(const A&) = default;
	A& operator=(const A&);
};
```

yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir.

Eğer programcı tarafından sınıfın taşıyan kurucu işlevi bildirilirse derleyici yalnızca sınıfın sonlandırıcı işlevini _default_ eder. 
Bu durumda sınıfın kopyalayan işlevleri _(copy members)_ derleyici tarafından _delete_ edilir. 
Sınıfın kurucu işlevi ve sınıfın taşıyan atama işlevi ise bildirilmemiş durumdadır.

```
class A {
public:
	A(A &&);
};
```
gibi bir sınıf tanımı ile

```
class A {
public:
	~A() = default; 
	A(const A &) = delete;
	A &operator=(const A &) = delete;
	A(A &&);
};
```

yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir.
Eğer programcı tarafından sınıfın taşıyan atama işlevi bildirilirse derleyici sınıfın varsayılan kurucu işlevini ve sınıfın sonlandırıcı işlevini _default_ eder. 
Bu durumda yine sınıfın kopyalayan işlevleri derleyici tarafından _delete_ edilir. 
Sınıfın taşıyan kurucu işlevi ise bildirilmemiş durumdadır.

```
class A {
public:
	A &operator=(A &&);
};
```
gibi bir sınıf tanımıyla

```
class A {
public:
	A() = default;
	~A() = default; 
	A(const A &) = delete;
	A &operator=(const A &) = delete;
	A &operator=(A &&);
};
```

yukarıdaki gibi bir sınıf tanımı eşdeğerdir.
Nesneleri taşınabilir fakat kopyalanamaz bir sınıf oluşturmak için sınıfın taşıyan işlevlerini bildirmemiz yeterlidir. 
Bu durumda derleyici sınıfın kopyalama işlevlerini _delete_ edeceğinden sınıf nesnelerinin kopyalanmasına neden olan durumlarda sentaks hatası oluşur.

Buraya kadar derleyicinin hangi koşullarda sınıfın özel işlevlerini içsel olarak oluşturduğunu inceledik. 
Peki ya derleyicinin bu özel işlevleri oluşturmasını engelleyen bir durum söz konusu ise ne olacak? 
Bu durumda derleyici yazması gereken işlevi _delete_ eder. 
Yani _default_ edilmiş bir özel işlev derleyicinin bu işlevi yazmaması durumunda derleyicinin _delete_ ettiği bir işleve dönüşür. 
Derleyicinin özel bir işlevi yazamaması farklı nedenlerle söz konusu olabilir:

#### Derleyicinin sınıfın varsayılan kurucu işlevini _delete_ etmesi
* Derleyicinin varsayılan kurucu işlevi yazması sürecinde, sınıfın başka bir sınıf türünden öğesinin ya da taban sınıf alt nesnesinin varsayılan kurucu işlevine yaptığı çağrı, 
bu işlevin _delete_ edilmiş olması ya da bu işlevin ilgili sınıfın _private_ bölümünde olması nedeniyle erişilmez durumda olması ya da bu işlevin var olmaması nedeniyle geçersiz durumuna düşüyorsa derleyici yazması gereken varsayılan kurucu işlevi _delete_ eder.
* Sınıfın başka sınıf türünden _static_ olmayan bir veri öğesinin ya da sınıfın taban sınıfının sonlandırıcı işlevi _delete_ edilmiş ise ya da erişilemez durumda ise derleyici yazması gereken varsayılan kurucu işlevi _delete_ eder.
* Yine sınıfın _static_ olmayan bir veri öğesinin _const_ ya da referans olması durumunda, bu öğeye sınıf içinde ilk değer verilmemiş ise derleyici yazması gereken varsayılan kurucu işlevi _delete_ eder.

#### Derleyicinin sınıfın sonlandırıcı işlevini _delete_ etmesi
* Derleyicinin bir sınıf için sonlandırıcı işlevi yazması sürecinde, sınıfın başka sınıf türünden _static_ olmayan bir veri öğesinin ya da sınıfın taban sınıfının sonlandırıcı işlevine yaptığı çağrı, 
söz konusu işlevin _delete_ edilmiş olması ya da erişilemez durumda olması yüzünden sentaks hatası durumuna düşüyor ise, derleyici yazması gereken sonlandırıcı işlevi _delete_ eder.

#### Derleyicinin sınıfın kopyalayan kurucu işlevini _delete_ etmesi
* Derleyicinin kopyalayan kurucu işlevi yazması sürecinde, sınıfın başka bir sınıf türünden static olmayan bir veri öğesinin ya da taban sınıf alt nesnesinin kopyalayan kurucu işlevine yaptığı çağrı, 
bu işlevin _delete_ edilmiş olması ya da bu işlevin ilgili sınıfın _private_ bölümünde olması nedeniyle erişilmez durumda olması nedeniyle geçersiz durumuna düşüyorsa derleyici yazması gereken kopyalayan kurucu işlevi _delete_ eder.
* Sınıfın başka sınıf türünden static olmayan bir veri öğesinin ya da sınıfın taban sınıfının sonlandırıcı işlevi delete edilmiş ise ya da erişilemez durumda ise derleyici yazması gereken kopyalayan kurucu işlevi _delete_ eder.

#### Derleyicinin sınıfın kopyalayan atama işlevini _delete_ etmesi
* Derleyicinin kopyalayan atama işlevini yazması sürecinde, sınıfın başka bir sınıf türünden öğesinin ya da taban sınıf alt nesnesinin kopyalayan atama işlevine yaptığı çağrı, bu işlevin _delete_ edilmiş olması ya da bu işlevin ilgili sınıfın _private_ öğesi olması yüzünden geçersiz durumuna düşüyorsa derleyici yazması gereken kopyalayan atama işlevini delete eder.
* Sınıfın static olmayan bir veri öğesinin _const_ ya da referans olması durumunda, derleyici yazması gereken kopyalayan atama işlevini delete eder.

#### Derleyicinin sınıfın taşıyan kurucu işlevini _delete_ etmesi
* Derleyicinin sınıfın taşıyan kurucu işlevini yazması sürecinde, sınıfın başka bir sınıf türünden static olmayan bir veri öğesinin ya da taban sınıf alt nesnesinin taşıyan kurucu işlevine yaptığı çağrı, söz konusu işlevin _delete_ edilmiş
olması ya da söz konusu işlevin ilgili sınıfın private bölümünde olması nedeniyle erişilmez durumda olması ya da bu işlevin var olmaması nedeniyle geçersiz durumuna düşüyorsa, derleyici yazması gereken taşıyan kurucu işlevi _delete_ eder.
Derleyici tarafından _delete_ edilmiş sınıfın taşıyan kurucu işlevi yüklenmiş işlev çözümlenmesi sürecine katılmaz, yani bildirilmemiş olarak ele alınır.

#### Derleyicinin sınıfın taşıyan atama işlevini _delete_ etmesi
* Derleyicinin sınıfın taşıyan atama işlevini yazması sürecinde, sınıfın başka bir sınıf türünden öğesinin ya da taban sınıf alt nesnesinin taşıyan atama işlevine yaptığı çağrı, 
söz konusu işlevin delete edilmiş olması ya da söz konusu işlevin ilgili sınıfın _private_ öğesi olması yüzünden geçersiz durumuna düşüyorsa derleyici yazması gereken taşıyan atama işlevini _delete_ eder.
* Sınıfın static olmayan bir veri öğesinin const ya da referans olması durumunda, derleyici yazması gereken taşıyan atama işlevini _delete_ eder.
Derleyici tarafından _delete_ edilmiş sınıfın taşıyan atama işlevi yüklenmiş işlev çözümlenmesi sürecine katılmaz, yani bildirilmemiş olarak ele alınır.

Derleyicinin özel işlevleri _delete_ etmesine örnekler verelim:

```
class Member {
	Member();
};


class A {
	Member m;
};


int main()
{
	A a;
}
```

Yukarıdaki kodda _A_ sınıfının _Member_ sınıfı türünden bir öğeye sahip olduğunu görüyorsunuz. 
_Member_ sınıfının varsayılan kurucu işlevi _Member_ sınıfının _private_ bölümünde bildirilmiş. 
_main_ işlevi içinde _A_ sınıfı türünden bir nesne tanımlanıyor. 
Derleyicinin bu durumda _A_ sınıfının varsayılan kurucu işlevini içsel olarak tanımlaması gerekiyor. 
Bu işlevin tanımında derleyicinin m veri öğesi için _Member_ sınıfının _private_ varsayılan kurucu işlevine çağrı yapması gerekiyor. 
Bu durum geçersiz olduğu için derleyici _A_ sınıfının varsayılan kurucu işlevini _delete_ eder. 
Yukarıdaki kod için kullandığım derleyicinin verdiği hata iletisi şu oldu:

```
A::A()' is implicitly deleted because the default definition would be ill-formed:
```

Şimdi de aşağıdaki koda bakalım:

```
class A {
	const int mcx = 0;
	//
};

int main()
{
	A a1, a2;
	a1 = a2;
}
```

_A_ sınıfının _mcx_ isimli veri öğesinin _const_ olarak bildirildiğini görüyorsunuz. 
_main_ işlevi içinde iki _A_ nesnesi birbirine atanıyor. 
Derleyicinin yazması gereken kopyalayan atama operatör işlevi _const_ bir veri öğesine atama yapmak geçerli olmadığından derleyici tarafından _delete_ edilir. 
Yukarıdaki kod için kullandığım derleyicinin hata iletisi şöyleydi:

```
error: use of deleted function 'A& A::operator=(const A&)'
```

Sınıfın taşıyan kurucu işlevi ya da taşıyan atama işlevi derleyici tarafından yazılamadığı için _delete_ edilirse hiç bildirilmemiş olarak ele alınır. 
Aşağıdaki koda bakalım:

```
#include <iostream>

using namespace std;

class Member {
	Member(Member &&){}
public:
	Member(){}
	Member(const Member &){cout << "Member Copy Ctor";}
};

class A {
	Member m;
};

int main()
{
	A a1;
	A a2(move(a1));
}
```

_main_ işlevi içinde tanımlanan _A_ sınıfı türünden _a2 _isimli nesneye bir sağ tarafı değeri ifadesi ile ilk değer veriliyor. 
Bu durumda _a2_ nesnesi için sınıfın taşıyan kurucu işlevinin çağrılması gerekiyor. 
Derleyici tarafından yazılacak olan _A_ sınıfının taşıyan kurucu işlevi _A_ sınıfının veri öğesi olan Member türünden _m_'nin taşınamaması yüzünden _delete_ ediliyor. 
Bu durumda derleyici tarafından _delete_ edilmiş _A_ sınıfının taşıyan kurucu işlevi bildirilmemiş olarak ele alınıyor ve _a2_ sınıf nesnesi için sınıfın kopyalayan kurucu işlevi işlevi çağrılıyor.
Özel işlevler hakkında bir noktayı daha vurgulayalım: 
Sınıfın taşıyan işlevleri olmayabilir ama bir sınıfın kopyalayan işlevleri her zaman vardır. 
Bir sınıfın kopyalayan işlevleri ya programcı tarafından bildirilir ya derleyici tarafından _default_ edilir ya da derleyici tarafından _delete_ edilir.

Kaynak: Everything You Ever Wanted to Know About Move Semantics, Howard Hinnant, Accu 2014

`C++` dilinin en karmaşık ve en fazla öğrenme zorluğu içeren araçlarından biri sınıfların özel işlevleri `(special members)`. 
`C++11` standartlarına göre sınıfların `6` özel işlevi var. 
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

a. Hiç bildirilmeyebilir `(not declared)`. Bu durumda söz konusu işlev yoktur.
b. Derleyici tarafından örtülü olarak bildirilebilir `(implicitly declared)`. 
Bu durum söz konusu işlevin derleyici tarafından `default` edilmesi ya da delete edilmesi ile mümkündür.
c. Programcı tarafından bildirilebilir `(user declared)`. 
Bu durum söz konusu işlevin programcı tarafından bildirilmesi, tanımlanması, `default` edilmesi ya da `delete` edilmesi ile mümkündür.

Aşağıda `A` isimli bir sınıfın varsayılan kurucu işlevinin `(default constructor)` programcı tarafından bildirilme senaryoları gösteriliyor:

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
* 2 Sınıfın varsayılan kurucu işlevi programcı tarafından sınıf tanımı içinde inline olarak tanımlanmış.
* 3 Sınıfın varsayılan kurucu işlevi programcı tarafından default edilmiş.
* 4 Sınıfın varsayılan kurucu işlevi programcı tarafından delete edilmiş.

Bir özel işlevin hiç bildirilmemesi `(not declared)` ile `delete` edilmesi `(deleted)` aynı şey değildir.
Bildirilmeyen bir işlev olmayan bir işlevdir, yoktur. Bildirilmeyen işlevler işlev yükleme çözümlemesine `(function overload resolution)` dahil edilmezler. 
`delete` edilen işlevler ise bildirilmişlerdir; işlev yükleme çözümlemesine dahil edilirler. 
Ancak `delete` edilmiş bir işleve bağlanmış bir çağrı sentaks hatası oluşturur. 
Aradaki farkı gösteren bir örnek verelim:

```
class A{
public:
	template <class ...Args>
	A(Args&& ...args);	
};
```

Yukarıda `A` isimli bir sınıf için varsayılan kurucu işlev bildirilmemiş. 
Sınıfta değişken sayıda parametreli `(variadic)` bir kurucu işlev şablonu `(template)` bildirildiğini görüyorsunuz. 
Varsayılan kurucu işlevin çağrılması gereken bir durumda, derleyici bu şablondan hareketle varsayılan kurucu işlevin kodunu yazar. Şimdi de aşağıdaki tanıma bakalım:

```
class A{
public:
	template <class ...Args>
	A(Args&& ...args);
	A() = delete;	
};
```

Burada ise `A` sınıfının varsayılan kurucu işlevi `delete` edilmiş. Bu işlev var ve işlev yüklenmesi çözümlenmesine dahil edilir. 
Ancak bu işleve yapılan bir çağrı sentaks hatası olarak kabul edilir. 
Bu durumda `A` sınıfı türünden bir nesnenin varsayılan şekilde hayata getirilmesi artık mümkün değildir.

Peki hangi durumlarda özel işlevler derleyici tarafından örtülü `(implicit)` olarak bildirilirler?
Bir sınıf için ne bir özel işlev ne de bir (özel olmayan) kurucu işlev bildirilmiş ise sınıfın tüm özel işlevleri `default` edilmiş olur. Yani
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

yukarıdaki gibi bir sınıf tanımı birbirine eşdeğerdir. 
Bir başka deyişle bu durumda sınıfın tüm özel işlevleri derleyici tarafından örtülü olarak bildirilir.

Eğer programcı tarafından sınıf için özel olmayan bir kurucu işlev bildirilirse bu durumda derleyici, varsayılan kurucu işlev dışında tüm işlevleri `default eder`.

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

Programcı tarafından bir varsayılan kurucu işlevin bildirilmesi durumunda yine diğer tüm özel işlevler derleyici tarafından `default` edilir.

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

Eğer programcı tarafından sınıfın sonlandırıcı işlevi bildirilirse derleyici sınıfın taşıma işlevleri `(move members)` dışındaki tüm özel işlevlerini `default` eder.

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
Aslına bakılırsa sonlandırıcı işlevin bildirilmesi durumunda derleyicinin sınıfın kopyalayan işlevlerini `default` etmesi doğru sayılmamalıdır. 
Eğer programcı sınıfın sonlandırıcı işlevini bildirmişse muhtemelen sınıfın kopyalayan işlevlerinin de programcı tarafından yazılması için bir gereklilik söz konusudur. 
Bu yüzden bu durum `C++11` standartlları tarafından eskimiş `(deprecated)` olarak değerlendirilmektedir. Gelecekteki standartlarda bu kuralın değiştirilrmesi gündemdedir. 
Yani programcı tarafından bir sonlandırıcı işlevin bildirilmesi durumunda gelecekteki standartlara göre, derleyici ileride bu durumda sınıfın kopyalayan işlevlerini de `default` etmeyecektir.

Eğer programcı tarafından sınıfın kopyalayan kurucu işlevi bildirirse derleyici sınıfın kopyalayan atama işlevini ve sonlandırıcı işlevlerini `default` eder. 
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
Eğer programcı tarafından sınıfın kopyalayan atama işlevi bildirilirse derleyici sınıfın varsayılan kurucu işlevini, kopyalayan kurucu işlevini ve sınıfın sonlandırıcı işlevini `default` eder.
Bu durumda sınıfın ve taşıyan işlevleri yine bildirilmemiş durumdadır.


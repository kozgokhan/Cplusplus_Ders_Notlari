_C++11_ öncesinde bir türe eş isim _(type alias)_ oluşturmanın tek yolu _C_'den gelen _typedef_ bildirimleriydi:

```
#include <vector>
#include <string>

typedef int Word;
typedef int *Iptr;
typedef int SMatrix10[10][10];
typedef int(*Fptr)(int, int);
typedef const std::vector<std::string> Csvec;
```

_C++11_ standartları ile türlere eş isim oluşturmak için ikinci bir araç daha geldi. 
Artık eş isim bildirimlerini _using_ anahtar sözcüğü ile yapabiliyoruz. 
Sentaks çok basit: _using_ anahtar sözcüğünü seçilen eş isim izliyor ve _=_ atomundan sonra ise eş ismin hangi türe karşılık geldiği yazılıyor. 
Yukarıdaki _typedef_ bildirimleri yerine _using_ bildirimleri yazalım:

```
using Word = int;
using Iptr = int *;
using SMatrix10 = int [10][10];
using Fptr = int(*)(int, int);
using Csvec = const std::vector<std::string>;
```

Ancak eş isim bildirimleri konusunda yeni bir aracın daha dile eklenmesinin ana nedeni, 
daha önce _typedef_ bildirimleriyle mümkün olmayan eş isim şablonlarının _(alias templates)_ oluşturabilmesini mümkün kılmak:

```
template <class T>
struct Alloc {
	//...
};

#include <vector>

template<class T>
using Vec = std::vector<T, Alloc<T>>;	

Vec<int> v;
```

Yukarıdaki kodda_ Allocator_ olarak kullanılacak _Alloc_ isimli bir sınıf tanımlanıyor. 
Daha sonra _Vec_ isimli bir eş isim şablonu oluşturuluyor. 
Böylece kod içinde _vector_ sınıf şablonunda ikinci şablon tür parametresi olarak _Alloc_ sınıf şablonunun kullanılması durumunda, 
şablon tür argümanı olarak _Alloc_ sınıfını belirtmeye gerek kalmayacak. 
Örneğin

```
std::vector<int, Alloc<int>>
```

yazmak yerine, yalnızca

```
Vec<int>
```

yazılabilecek. 
Birkaç örnek daha verelim:

```
#include <map>
#include <string>

template<typename T>
using Smap = std::map<std::string, T>;

Smap<int> simap;
```

Yukarıdaki kodda oluşturulan _Smap_ eş isim şablonunun tür parametresi, standart _map_ sınıfının ikinci şablon tür parametresini belirleyecek. 
Birinci şablon tür parametresi standart _string_ sınıfı olacak. 
Bu durumda

```
Smap<int>
```
yazmak ile

```
std::map<std::string, int>
```

yazmak aynı anlama gelecek. 
Şimdi de aşağıdaki koda bakalım:

```
template<typename T>
using Ptr = T *;

double dval = 2.3;
Ptr<double> p = &dval;
```

Yukarıdaki kodda _p_ _double_ türden bir nesneyi gösteren bir _pointer_ değişken.

Eş isim şablonları da varsayılan tür argümanı alabilir:

```
#include <set>
#include <functional>

template<typename T, typename C = std::greater<T>, typename A = std::allocator<T>>
using Gset = std::set<T, C, A>;

int main()
{
	Gset<int> myset;
	//...
}
```

Yukarıdaki kodda tanımlanan _myset_ değişkeni

```
std::set<double, std::greater<int>, std::allocator<int>>
```
türünden.

Şablon sabit parametreleri de _(non type parameters)_ eş isim şablonlarında kullanılabilir:

```
template<typename T, size_t low, size_t high>
class Rand {
	//...
};

template<size_t high>
using IRand = Rand<int, 0, high>;

int main()
{
	IRand<100> x;
        //...
}
```

Yukarıdaki kodda, _Irand_ şablon ismi _100_ argüman değeri ile kullanıldığında bu şablon açılımı

```
Rand<int, 0, 100>
```
açılımı anlamına geliyor.

Sınıf şablonlarında ya da işlev şablonlarında yapılabilen açık özelleştirme _(explicit specialization)_ ya da 
yalnızca sınıf şablonlarında mümkün olan kısmi özelleştirme _(partial specialization)_ araçları eş isim şablonlarında kullanılamıyor. 
Eş isim şablonlarında tür çıkarımı da söz konusu değil.
Eş isim şablonları isim alanı kapsamında _(namespace scope)_ ya da sınıf kapsamında _(class scope)_ bildirilebiliyor. 
Ancak sınıf şablonlarında ve işlev şablonlarında olduğu gibi isim alanı şablonlarının da yerel bir blok içinde bildirilmesi geçerli değil.

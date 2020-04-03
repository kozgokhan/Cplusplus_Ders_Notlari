Bu yazımızın konusu _C++11_ ilestandartları ile standart kütüphaneye eklenmiş basit ama faydalı 3 algoritma.

_any_of_ algoritmasıyla bir aralık _(range)_ içindeki değerlerden herhangi birinin bir koşulu sağlayıp sağlamadığını sınayabiliyoruz:

```
template<class InIter, class UnPred>
bool any_of(InIter first, InIter last, UnPred f);
```

Şablondan üretilecek işlevin ilk iki parametresi ilgili aralığa ilişkin adımlayıcılar _(iterator)_. 
İşlevin son parametresi tek parametreli bir sınayıcı _(predicate)_. 
Eğer _\[first last)_ aralığındaki herhangi bir öğe için sınayıcımız olan _f_, _"true"_ değer üretirse işlevimiz _"true"_ değer döndürecek. 
Eğer aralıktaki hiçbir değer için _f_, _"true"_ değer üretmez ise işlevimiz _"false"_ değerini döndürecek. 
Kısaca bu işlev ile _\[first, last)_ aralığında en az bir değerin verilen bir koşulu sağlayıp sağlamadığını sınayabiliyoruz:

```
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

bool isprime(int);

int main()
{
	vector<int> ivec{ 138651, 43523, 462231, 2340097, 20414123 };
	
	if (any_of(ivec.begin(), ivec.end(), &isprime)) {
		cout << "en az bir asal sayi var\n";
	}
	else {
		cout << "sayilardan hicbiri asal degil\n";
	}
	//
}
```

Yukarıdaki kodda _ivec_ _vector_'ünde bulunan tamsayılar içinde en az bir asal sayı bulunup bulunmadığını test ediyoruz. 
Bu sınamayı pekala `find_if` algoritmasıyla da gerçekleştirebilirdik, değil mi?

```
template<class InIter, class UnPred>
InIter find_if(InIter first, InIter last, UnPred f)
{
	while (first != last) {
		if (f(*first))
			return first;
		++first;
	}
	return first;
}
```

_find\_if_ algoritması bir aralık içinde bir koşulu sağlayan ilk öğeyi arıyor ve koşulu sağlayan ilk öğenin konumunu döndürüyor. 
Eğer öğelerden hiçbiri ilgili koşulu sağlamıyor ise algoritmanın geri dönüş değeri kendisine geçilen _last_ konumu.

```
template<class InIter, class UnPred>
bool any_of(InIter first, InIter last, UnPred f)
{
	while (first != last) {
		if (f(*first))
			return true;
		++first;
	}

	return false;
}
```

_any\_of_ algoritmasını şu şekilde de kodlayabilirdik:

```
template<class InIter, class UnPred>
bool any_of(InIter first, InIter last, UnPred f)
{
	return find_if(first, last, f) != last;
}
```

_all\_of_ algoritması ile bir aralıktaki tüm değerlerin bir koşulu sağlayıp sağlamadığını sınayabiliyoruz:

```
template<class InIter, class UnPred>
bool all_of(InIter first, InIter last, UnPred f);
```

Aşağıdaki kod parçasına bakalım:

```
#include <list>
#include <algorithm>
#include <iostream>

using namespace std;

int main()
{
	int n;
	list<int> ls {25, 32, 43, 19, 6, 8, 20, 11, 9, 27, 5, 7};
	cout << "pozitif bir tamsayi girin : ";
        cin >> n;
	if (all_of(ls.begin(), ls.end(), [n](int x){return x % n == 0; }))
		cout << "listedeki tum degerler " << n << " ile tam bolunuyor\n";
	//...

}
```

_ls_ isimli listedeki tüm sayıların _n_ tamsayısına tam olarak bölünüp bölünmediğini sınamak için _all_of_ algoritmasının kullanıldığını görüyorsunuz.  Bu örnekte sınayıcı parametresine bir _lambda_ ifadesi geçiliyor. 
 _lambda_ ifadesinde yerel değişken olan _n_ yakalanıyor _(capture ediliyor)_.
_all_of_ işlev şablonu şu şekilde kodlanabilir:

```
template<class InIter, class UnPred>
bool all_of(InIter first, InIter last, UnPred f)
{
	while (first != last) {
		if (!f(first))
			return false;
		++first;
	}
	return true;
}
```

_all\_of_ algoritmasının başka bir gerçekleştirimi şöyle olabilirdi:

```
template<class InIter, class UnPred>
bool all_of(InIter first, InIter last, UnPred f)
{
	return find_if_not(first, last, f) == last;
}
```

Bu arada _find\_if\_not_ algoritmasının da _C++11_ ile geldiğini hatırlatalım. 
_find_if_not_ algoritması ile bir aralıkta bulunan değerlerden bir koşulu sağlamayan ilk öğenin konumunu bulabiliyoruz:

```
template<class InIter, class UnPred>
InIter find_if_not(InIter first, InIter last, UnPred f)
{
	while (first != last) {
		if (!f(*first))
			return first;
		++first;
	}
	return first;
}
```

Şimdi de _none\_of_ algoritmasını inceleyelim. 
_none\_of_ algoritması bir aralıktaki değerlerden hiçbirinin verilen bir koşulu gerçeklemiyor ise _"true"_ değer döndürüyor:

```
template<class InIter, class UnPred>
bool none_of(InIter first, InIter last, UnPred f);
```

Aşağıdaki _main_ işlevini inceleyelim:

```
#include <algorithm>
#include <vector>
#include <string>

using namespace std;

int main()
{
	vector<string> svec{ "selim", "can", "berrin", "murat", "kayhan", "nedim" };
	//
	if (none_of(svec.begin(), svec.end(), [](const string &s){return s.length() < 6; })) {
		//
	}
	//
}
```

Yukarıdaki kodda _svec_ içinde tutulan isimlerden hiçbirinin uzunluğu _6_'dan fazla değilse programın akışı _if_ deyiminin içine girecek. 
_none_of_ algoritması aşağıdaki gibi kodlanabilir:

```
template<class InIter, class UnPred>
bool none_of(InIter first, InIter last, UnPred f)
{
	while (first != last) {
		if (f(*first)) {
			return false;
		}
		++first;
	}
        return true;
}
```

_none\_of_ algoritmasının _any\_of_ algoritmasının lojik değili olduğu açık, değil mi?

```
template<class InIter, class UnPred>
bool none_of(InIter first, InIter last, UnPred f)
{
	return find_if_not(first, last, f) == last;
}
```

Şimdi de aşağıdaki programı derleyerek çalıştırın:

```
#include <algorithm>
#include <vector>
#include <iostream>

using namespace std;

int main()
{
	vector<int> ivec{ 4, 6, 2, 7, 3, 8, 1, 7, 3 };
	int ival;

	cout << "bir sayi giriniz : ";
	cin >> ival;
	auto f = [ival](int x) {return x < ival; };
	cout.setf(ios_base::boolalpha);
	cout << any_of(ivec.begin(), ivec.end(), f) << "\n";
	cout << all_of(ivec.begin(), ivec.end(), f) << "\n";
	cout << none_of(ivec.begin(), ivec.end(), f) << "\n";
}
```

Peki, verilen aralık boş ise işlevlerimiz hangi değerleri üretecek. Aşağıdaki kod bunu gösteriyor:

```
#include <iostream>
#include <vector>
#include <algorithm>

int main()
{
	using namespace std;

	vector<int> ivec; //ivec boş
	auto pred{ [](int x) {return x == 5; } };
	cout << boolalpha;
	cout << any_of(ivec.begin(), ivec.end(), pred) << "\n";
	cout << none_of(ivec.begin(), ivec.end(), pred) << "\n";
	cout << all_of(ivec.begin(), ivec.end(), pred) << "\n";
}
```

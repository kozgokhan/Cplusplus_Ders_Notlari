# std::byte Türü

_C++17_ standartları ile dile eklenen yeni türlerden biri __std::byte__. Yazdığımız programlar bellekteki _byte_'ları kullanıyor. __std::byte__ türünün amacı bu bellek _byte_'larını oldukları gibi temsil etmek. Burada amaç _byte_'lardaki tutulan değerlere ne karakter kodu olarak bakmak (ki bunun için zaten _char_ türleri var) ne de onları doğrudan tam sayı değerleri olarak kullanmak (bunun için de tam sayı türlerimiz var). Amaçlanan ham bellek _byte_'larını temsil etmek. Sayısal bir hesaplamanın söz konusu olmadığı durumlarda, _std::byte_ türünün kullanılması ile derleme zamanına yönelik katı bir tür kontrolü hedefleniyor.

_std::byte_ türüne ilişkin sentaks kurallarını ve bu türün kullanım olanaklarını tam olarak kavrayabilmek için öncelikle _C++17_ standartları ile _enum_ türleri konusunda temel sentaksa yapılan bir eklentiyi anlamamız gerekiyor: Artık baz türü _(underlying type)_ belirli olan bir _enum_ nesnesine küme parantezi içinde doğrudan _enumarator_ olmayan bir tam sayı ile ilk değer verilebiliyor. Aşağıdaki koda bakalım:

```
enum Color : unsigned char {red, green, yellow};
enum class Pos {off, on, hold};

int main()
{
	Color cx{ 5 }; //geçerli
	Pos mypos{20}; //geçerli
	//...
}
```` 
Bu şekilde ilk değer vermenin geçerli olabilmesi için

Baz türün belirlenmiş olması gerekiyor. Geleneksel _enum_ türleri söz konusu olduğunda baz tür bildirimde belirtilmeli. _enum class_'lar söz konusu olduğunda ise baz tür belirtilmese de varsayılan baz türün _int_ kabul edildiğini hatırlayalım.
Bildirimde ilk değer küme parantezi içinde ve doğrudan verilmeli. Küme parantezi içinde ilk değer verme _(brace initialization)_ kuralları gereği daraltıcı bir tür dönüşümü _(narrowing conversion)_ söz konusu olmamalı. Aşağıdaki kodu inceleyelim:

```
enum Nec : char { };
Nec i1{ 42 }; // C++17'de gecerli (daha önce geçersizdi) 
Nec i2 = 42; // geçersiz
Nec i3(42); // geçersiz
Nec i4 = { 42 }; // geçersiz

enum class Weekday { mon, tue, wed, thu, fri, sat, sun };
Weekday s1{ 0 }; // C++17'de gecerli (daha önce geçersizdi)
Weekday s2 = 0; // geçersiz
Weekday s3(0); // geçersiz
Weekday s4 = {0}; // geçersiz

enum class Pos : char {on, off, hold, standby};
Pos pos1{ 0 }; //C++17'de gecerli (daha önce geçersizdi) 
Pos pos2 = 0; // geçersiz
Pos pos3 = {0}; // geçersiz

enum BitFlag { bit1 = 1, bit2 = 2, bit3 = 4 };
BitFlag flag{ 0 }; // halen geçersiz (baz tür belli degil)

enum Erg : char { };
Erg i5{ 42.2 }; //geçersiz (daraltıcı dönüşüm)
```

Yukarıdaki kodda _enum_ nesnelerine ilk değer vermede _(initialization)_ yeni kurallar, geçerli ve geçersiz bildirimlerle örnekleriyle gösteriliyor. Başlangıçta en azından tuhaf olarak karşılanacak bu yeni sentaksın ana amacı _std::byte_ türünün (ya da ileride oluşturulacak özel bazı türlerin) gerçekleştirilmesine olanak sağlamak.

_std::byte_ aslında aşağıdaki gibi tanımlanmış bir _enum class_ türü:

```
enum class byte : unsigned char {};
```

Tanımdan da görüldüğü gibi _std::byte_ için baz tür olarak _unsigned char_ türü seçilmiş. Bu durumda _std::byte_ türünden bir nesneye ilk değer verebilmek için çok sınırlı sayıda seçenek söz konusu. Zaten _std::byte_ türü ile hedeflenen bu şekilde katı bir tür denetimi sağlamak:

```
#include <cstddef>

int main()
{
	std::byte b1; //geçerli (belirlenmemiş değer)
	std::byte b2{ 0 }; //geçerli
	std::byte b3{}; //geçerli (b2 ile aynı)
	std::byte b4{ 12 }; //geçerli
	std::byte b5{ 0xFF }; //geçerli
	std::byte b6{ 0b1001'0101 }; //geçerli
	std::byte b7{ 256 }; //geçersiz (daraltıcı dönüşüm)
	std::byte b8(45); //geçersiz
	std::byte b9 = 68; //geçersiz
	std::byte b10 = { 45 }; //geçersiz
}
```
İlk değer verilmemiş otomatik ömürlü bir _std::byte_ nesnesinin tanımlanması geçerli olsa da böyle bir nesnenin hayata belirlenmemiş bir değer ile _(indetermined value)_ geldiğine dikkat etmemiz gerekiyor. Öğeleri _std::byte_ türünden olan bir diziye de tam sayılarla ilk değer veremiyoruz:

```
#include <cstddef>

int main()
{
	std::byte a[] = { 2, 4, 5}; //geçersiz
	std::byte b[] = { std::byte{3}, std::byte{7}, std::byte{9} };
}
```

## std::byte sizeof değeri
_std::byte_ baz türü _unsigned char_ olan bir enum türü olduğundan _sizeof_ değerinin _1_ olması garanti altında. _unsigned char_ sistemlerin çoğunda _8_ bitlik bir tam sayı türü. Ancak farklı sistemlerde _char_ türlerinin bit sayısı farklı olabileceğinden bu değerin kullanılması gereken durumlarda taşınabilirlik sağlamak amacıyla standart _<limits>_ başlık dosyasında tanımlanan

```
std::numeric_limits<unsigned char>::digits
```
_static constexpr_ sabiti kullanılmalı.

## `to_integer<>` işlev şablonu
_std::byte_ türünden tam sayı türlerine ve tam sayı türlerinden _std::byte_ türüne otomatik tür dönüşümü _(implicit type conversion)_ yok. Ancak bir _std::byte_ nesnesini herhangi bir tam sayı türüne dönüştürmek için _to_integer_ isimli işlev şablonunu kullanabiliyoruz:

```
#include <cstddef>
#include <iostream>

int main()
{
	std::byte b{ 0xAB };
	int ival = std::to_integer<int>(b);

	std::cout << "ival = " << ival << '\n';
}
```
_std::byte_ türünden _bool_ türüne de otomatik tür dönüşümü olmadığından lojik ifade beklenen yerlerde _std::byte_ nesnelerini doğrudan kullanamıyoruz:

```
#include <cstddef>

int main()
{
	std::byte b{ 16 };
	//...
	//if (b) {} //gecersiz
	if (std::to_integer<bool>(b)) {}
	//...
	if (b != std::byte{ 0 }) {}
	//...
}
```

## bitsel işlemler
Bir _std::byte_ nesnesine yalnızca atama ya da kopyalama yapabilir ya da onu bitsel işlemlere sokabiliriz.
_std:byte_ türü için bitsel işlemler, operatör yüklemesi _(operator overloading)_ mekanizması ile mümkün kılınmış:

```
template<typename Integer>
constexpr byte operator<< (byte b, Integer shift) noexcept;

template<typename Integer>
constexpr byte& operator<<= (byte& b, Integer shift) noexcept;

template<typename Integer>
constexpr byte operator>> (byte b, Integer shift) noexcept;

template<typename Integer>
constexpr byte& operator>>= (byte& b, Integer shift) noexcept;

constexpr byte& operator|= (byte& left, byte right) noexcept;
constexpr byte& operator&= (byte& left, byte right) noexcept;
constexpr byte& operator^= (byte& left, byte right) noexcept;

constexpr byte operator| (byte left, byte right) noexcept;
constexpr byte operator& (byte left, byte right) noexcept;
constexpr byte operator^ (byte left, byte right) noexcept;
constexpr byte operator~ (byte b) noexcept;
```
Aşağıdaki kodu derleyip çalıştırın:

```
#include <cstddef>
#include <iostream>

int main()
{
	std::byte bb{0XAF};
	std::cout << std::hex << std::uppercase;

	std::cout << std::to_integer<int>(bb) << "\n";
	std::byte bl = (bb << 4) | (bb >> 4);
	std::cout << std::to_integer<int>(bl) << "\n";
}
```

## std::byte türü ve formatlı giriş çıkış işlemleri
_std::byte_ türü için standart kütüphanenin sağladığı formatlı giriş ve çıkış işlevleri _(inserter / extractor)_ bulunmuyor:

```
#include <cstddef>
#include <iostream>

int main()
{
	std::byte b{ 0xA5 };

	std::cout << b; // geçersiz
	std::cin >> b; //geçersiz
	//...
}
```
Ancak bu işlevleri istersek kodlarını kendimiz dilediğimiz gibi yazabiliriz. Önce formatlı çıkış işlemi için bir işlev `(inserter)` tanımlayalım:

```
#include <cstddef>
#include <ostream>
#include <bitset>

std::ostream &operator<<(std::ostream &os, std::byte b)
{
	using btype = std::bitset<std::numeric_limits<unsigned char>::digits>;
	return os << btype(std::to_integer<unsigned char>(b));
}

#include <iostream>

int main()
{
	std::byte b1{ 0xB5 }, b2{ 0xE4 };
	std::cout << b1 << '\n' << b2 << '\n';
}
```

Şimdi de formatlı giriş işlemi için bir işlev _(extractor)_ tanımlayalım:

```
#include <istream>
#include <bitset>
#include <cstddef>

std::istream &operator>>(std::istream &is, std::byte &b)
{
	using btype = std::bitset<std::numeric_limits<unsigned char>::digits>;
	btype bs;
	is >> bs;

	if (!is.fail())
		b = std::byte{ static_cast<unsigned char>(bs.to_ulong()) };

	return is;
}

#include <iostream>

int main()
{
	std::byte b;

	std::cout << "bir byte giriniz: ";
	std::cin >> b;

	std::cout << "deger : " << std::to_integer<int>(b);
}
```
_std::bitset_ sınıfını kullanarak _std::byte_ nesnesinin taşıdığı değeri _std::string_ olarak da ifade edebiliriz:

```
#include <cstddef>
#include <string>
#include <bitset>
#include <limits>

std::byte b{ 198 };
using bitset_b = std::bitset<std::numeric_limits<unsigned char>::digits>;
auto str = bitset_b{ std::to_integer<unsigned char>(b) }.to_string();
```

Standart kütüphanenin _std::byte_ türü için bir "sabit operatör işlevi"  _(literal operator function)_ tanımlaması belki iyi bir fikir olabilirdi. Böyle bir ihtiyaç söz konusu olduğunda kendi _std::byte_ sabitlerimizi oluşturabilmek için bir işlev tanımlayabiliriz. Aşağıdaki kodu inceleyelim:

```
#include <cstddef>
#include <iostream>

constexpr std::byte operator ""_b(unsigned long long val)
{
	return std::byte{ static_cast<unsigned char>(val) };
}

int main()
{
	constexpr std::byte b1 = 10_b;
	constexpr std::byte b2 = 48_b;
	std::cout << std::hex << std::uppercase;

	std::cout << std::to_integer<int>(b1 | b2) << "\n";
	std::cout << std::to_integer<int>(b1 & b2) << "\n";
	std::cout << std::to_integer<int>(b1 ^ 0xF_b) << "\n";
}
```

# shared_ptr sınıfı

`shared_ptr` `C++11` standartları ile dile eklenen bir akıllı gösterici `(smart pointer)` sınıfıdır. 
Bu sınıf paylaşımlı sahiplik `(shared ownership)` stratejisini gerçekler. 
Birden fazla `shared_ptr` nesnesi aynı kaynağı gösterebilir. 
Bir kaynağı gösteren son `shared_ptr` nesnesinin hayatı bittiğinde ya da ona başka bir kaynak bağlandığında yani kaynağı gösteren başka bir `shared_ptr` nesnesi kalmadığında kaynak sonlandırılır.
`shared_ptr` sınıfı aynı kaynağın birden fazla kod tarafından güvenli bir şekilde paylaşılmasını sağlar.
Sınıfın tanımı `<memory>` başlık dosyasındadır:

```
template<typename T>
class shared_ptr {
	//...
};
```


`unique_ptr` sınıf şablonundan farklı olarak kullanılacak `deleter` türü burada şablon tür parametresi değildir.
Kaynağı sonlandırmak için kullanılacak `deleter` sınıfın kurucu işlevinin parametresine gönderilir.
Herhangi bir argüman gönderilmez ise kaynak sonlandırıcısı olarak standart `default_delete` sınıfının kullanılmasıyla kaynak `delete` edilir.

`shared_ptr` sınıfı bir kaynağın ne zaman sonlandırılacağını anlamak için bir referans sayacı `(reference count)` kullanmaktadır. 
Aynı kaynağı paylaşan tüm `shared_ptr` nesneleri aynı referans sayacına erişebilir.
Kaynağı paylaşan her yeni `shared_ptr` nesnesi için referans sayacı bir arttırılır.
Bir `shared_ptr` nesnesinin hayatı bittiğinde eğer bir kaynağı paylaşıyor ise o kaynağa ilişkin refeans sayacı bir eksiltilir.
Bir `shared_ptr` nesnesi bir kaynağı gösterirken ona başka bir `shared_ptr` nesnesi atanırsa önce göstermekte olduğu kaynağın referans sayacı bir eksiltilir sonra göstereceği kaynağın referans sayacı bir arttırılır.
Aşağıdaki kodu inceleyelim:

```
#include <memory>
#include <iostream>

class Myclass {
public:
	Myclass() { std::cout << "Myclass constructor\n"; }
	~Myclass() { std::cout << "Myclass destructor\n"; }
};

int main()
{
	std::cout << "[1] main basladi\n";
	if (1) {
		std::shared_ptr<Myclass> sp1{ new Myclass };
		if (1) {
			auto sp2 = sp1;
			auto sp3 = sp1;
			std::cout << "[2] main devam ediyor\n";
		}
		std::cout << "[3] main devam ediyor\n";
	}
	std::cout << "[4[ main sona eriyor\n";
}
```

Yukarıdaki kodu derleyip çalıştırın. `new` operatörü ile hayata getirilen nesne ne zaman `delete` ediliyor? Onu gösteren son `shared_ptr` nesnesinin yani `sp1`'in hayatı bittiğinde.

Bir `shared_ptr` nesnesi içinde tipik olarak iki gösterici tutulamaktadır.
(`C++` standartları böyle bir gerçekleştirimi zorunlu kılmamaktadır.)
Göstericilerden biri dinamik ömürlü nesneyi gösteririr.
Eğer `shared_ptr` nesnesi henüz bir kaynağı göstermiyor ise bu göstericinin değeri `nullptr`'dir.
Diğer gösterici ise "kontrol bloğu" `(control block)` denilen bir bellek bloğunun adresini tutar.
Bir dinamik bellek fonksiyonu `(allocator)` ile yeri elde edilen `(allocate)` kontrol bloğunda tipik olarak aşağıdaki öğeler yer alır:

+ Referans sayacı
+ Zayıf referans sayacı (daha sonra göreceğiz)
+ `deleter` (kaynağı sonlandırma işini üstlenen)
+ `allocator` (bellek alanını elde edecek ve geri verecek sınıf nesnesi)
+ kaynağı gösteren bir gösterici

Aşağıdaki kodda bir `shared_ptr` nesnesinin `sizeof` değeri standart çıkış akımına yazdırılıyor:
```
#include <memory>
#include <iostream>
#include <string>

int main()
{
	using namespace std;

	cout << "sizeof (string *)            : " << sizeof(string *) << '\n';
	cout << "sizeof (sharede_ptr<string>) : " << sizeof(shared_ptr<string>) << '\n';
}
```

`shared_ptr` tarafından gösterilen nesne kendisine ilişkin referans sayacını bilmez.
Yani referans sayacı fiziksel olarak gösterilen nesnenin içinde değildir.
Bu durumda her nesnenin içinde bir referans sayacı tutmak gerekirdi.(Bu nasıl sağlanırdı?)
Eğer böyle olsaydı temel türlerden `(int, double, vs.)` dinamik ömürlü nesnelere ilişkin referans sayacı nasıl tutulabilirdi?
Referans sayacı ayrı bir kontrol bloğu içinde tutulmaktadır.

Bir kaynağı gösterewn ilk `shared_ptr`nesnesi hayata getirildiğinde bir kontrol bloğu oluşturulur.
Aynı nesneyi göstren `shared_ptr` nesneleri aynı kontrol bloğunun adresini tutarlar.

### Kontrol bloğu ne zaman oluşturulur?
`shared_ptr` göstericileri ile hayatı kontrol edilen bir nesneye ilişkin, içinde referans sayacının da bulunduğu bir kontol bloğunun mutlaka oluşturulması gerekmektedir.


+ Eğer `shared_ptr` nesnesi bir adres ile oluşturulur ise yeni bir kontrol bloğu oluşturulur.
+ Eğer dinamik ömürlü nesne `make_shared` fonksiyonu ile oluşturulur ise hem new operatörü ile dinamik ömürlü nesnenin
hayata getirilmesi hem de kontrol bloğunun oluşturulması bu fonksiyon tarafından gerçekleştirilir.
`make_shared` işlevi hem dinamik ömürlü nesnenin kendisinin hem de kontrol bloğunun yer alacağı tek bir bellek bloğu elde ederek dinamik bellek yönetimi maliyetini azaltabilir.
+ Eğer `shared_ptr` nesnesi bir `unique_ptr` nesnesinden ilk değerini alarak hayata getirilirse kontrol bloğu oluşturulur.


-bu maddenin yazımı devam ediyor-

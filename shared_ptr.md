`shared_ptr` `C++11` standartları ile dile eklenen bir akıllı gösterici `(smart pointer)` sınıfıdır. 
Bu sınıf paylaşımlı sahiplik stratejisini gerçekler. 
Birden fazla `shared_ptr` nesnesi aynı kaynağı gösterebilir. 
bir `shared_ptr` nesnesinin sonlandırıcı işlevi çağrıldığında, eğer kaynağı gösteren son nesne ise 
kaynağı delete eder.
`shared_ptr` sınıfı aynı kaynağın birden fazla kod tarafından güvenli bir şekilde paylaşılmasını sağlar.
Sınıfın tanımı `<memory>` başlık dosyasındadır:

```
template<typename T>
class shared_ptr {
	//...
};
```


`unique_ptr` sınıf şablonundan farklı olarak kullanılacak deleter türü burada şablon tür parametresi değildir.
Kaynağı sonlandırmak için kullanılacak `deleter` sınıfın kurucu işlevinin parametresine gönderilir.
Herhangi bir argüman gönderilmez ise kaynak sonlandırıcısı olarak standart `default-delete` sınıfının kullanılmasıyla kaynak `delete` edilir.

`shared_ptr` sınıfı bir kaynağın ne zaman sonlandırılacağını anlamak için bir referans sayacı kullanmaktadır. 
Aynı kaynağı paylaşan tüm `shared_ptr` nesneleri aynı referans sayacına erişebilir.
Kaynağı pylaşan her yeni `shared_ptr` nesnesi için referans sayacı bir arttırılır.
Bir `shared_ptr` nesnesinin hayatı bittiğinde eğer bir kaynağı paylaşıyor ise o kaynağa ilişkin refeans sayacı bire eksiltilir.
Bir `shared_ptr` nesnesi bir kaynağı gösterirken ona başka bir `shared_ptr` nesnesi atanırsa önce göstermekte olduğu kaynağın sayacı bir eksiltilir sonra göstereceği kaynağın referans sayıcı bir arttırılır.

Bir `shared_ptr` nesnesi içinde tipik olarak iki gösterici tutulamaktadır.
(`C++` standartları böyle bir gerçekleştirimi zorunlu kılmamaktadır.)
Göstericilerden biri dinamik ömürlü nesneyi gösteririr.
Eğer `shared_ptr` nesnesi henüz bir kaynağı göstermiyor ise bu göstericinin değeri `nullptr`'dir.
Diğer gösterici ise "kontrol bloğu" `(control bşlock)` denilen bir bellek bloğunun adresini tutar.
Bir dinamik bellek fonksiyonu `(allocator)` ile yeri elde edilen `(allocate)` kontrol bloğunda tipik olarak aşağıdaki öğeler yer alır:

+ Referans sayacı
+ Zayıf referans sayacı (daha sonra göreceğiz)
+ deleter (kaynağı sonlandırma işini üstlenen)
+ allocator (bellek alanını elde edecek ve geri verecek sınıf nesnesi)
+ kaynağı gösteren bir gösterici

Aşağıdaki kodda bir shared_ptr nesnesinin sizeof değeri standart çıkış akımına yazdırılıyor:
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

-bu maddenin yazımı devam ediyor-

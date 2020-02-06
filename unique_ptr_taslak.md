## unique_ptr
`C++11` standartları ile birlikte standart kütüphaneye dahil edilmiş olan `unique_ptr` bir akıllı gösterici `(smart pointer)` sınıfıdır. 
Bu akıllı gösterici sınıfı genel olarak "tek sahiplik" `(exclusive ownership)` stratejisini gerçekleştirir.
Bir unique_ptr nesnesi bir dinamik sınıf nesnesini gösteren tek bir gösterici olarak kullanılır. `unique_ptr` nesnesi, kendi hayatı sona erince sahibi olduğu dinamik sınıf nesnesinin de hayatını sonlandırarak onun tutmakta olduğu kaynakların serbest bırakılmasını sağlar. 
Bu sınıfın temel varlık nedenlerinden biri, bir hata nesnesi `(exception)` gönderildiğinde söz konusu olabilecek kaynak sızıntısının `(resource leak)` engellenmesidir.

`unique_ptr` sınıfı `C++98` standartlarında var olan ancak dildeki araçların yetersizliğinden kaynaklanan kötü tasarımı nedeniyle eleştirilen `auto_ptr` sınıfının yerine getirilmiştir. `C++11` standartları ile `auto_ptr` sınıfı kullanımdan düşürülmüş `(deprecated)` onun yerine hem daha yalın ve daha net bir arayüze sahip olan hem de daha düşük kodlama hatası riski içeren `unique_ptr` sınıfı standart kütüphaneye eklenmiştir. `auto_ptr` sınıfının tasarlandığı dönemde `C++` dili taşıma semantiği, değişken sayıda tür parametresine sahip şablonlar `(variadic templates)` gibi araçlara sahip değildi. `C++11` standartlarıyla dile kazandırılan bu araçlar `unique_ptr` sınıfının güvenli bir biçimde tasarlanmasına olanak sağlamıştır.


#### unique_ptr sınıfının kullanımı
Bazı işlevler işlerini şu şekilde görür:

* Önce işlerini gerçekleştirebilmek için sınıf nesneleri yoluyla bazı kaynaklar edinirler.
* Sonra yüklendikleri işleri gerçekleştirirler.
* İşlerini tamamladıktan sonra edindikleri kaynakları geri verirler.

İşlev içinde edinilen kaynaklar, işlev içinde tanımlanan yerel sınıf nesnelerine bağlanmışlarsa, işlevin kodundan çıkıldığında yerel sınıf nesnelerinin sonlandırıcı işlevinin çağrılmasıyla tutulan kaynaklar geri verilmiş olur. Ancak kaynaklar yerel bir sınıf nesnesine bağlanmadan dinamik olarak dışsal biçimde edinildiğinde dinamik sınıf nesnesini yöneten gösterici değişkenler tarafından kontrol edilirler. Bu durumda dinamik nesnenin hayatı `delete` ifadesi ile sonlandırılır. Aşağıdaki örneği inceleyin:
```
void func()
{
    class ResourceUser *pd = new ResoruceUse; // dinamik bir nesne oluşturuluyor
    // kaynaklar kullanılarak bazı işlemler gerçekleştiriliyor.
    delete pd; // Dinamik nesnenin ömrü sonlandırılarak kaynaklar geri veriliyor.
}
```

Böyle bir işlev sorunlara yol açabilir. Sorunlardan biri dinamik nesnenin hayatının sonlandırılmasının `(delete edilmesinin)` unutulmasıdır. Öneğin `delete` işleminden önce bir `return` deyimi yürütülürse dinamik nesnenin hayatı sonlandırılmayacaktır.

Başlangıçta kolayca görülüyor olmasa da bir başka sorun da bir fonksiyondan hata nesnesinin `(exception)` gönderilmesidir. Bir hata nesnesi gönderildiğinde programın akışı işlevden çıkacak böylece `delete` deyimi yürütülmeyecektir. Bu durum bir bellek sızıntısına `(memory leak)` neden olabileceği gibi daha genel olarak bir kaynak sızıntısına `(resource leak)` yol açabilir.

Gönderilebilecek tüm hata nesnelerinin yine işlev tarafından yakalanması oluşabilecek kaynak sızıntısı engelleyebilir:
```
void f()
{
	class ResourceUser *ptr = new class ResourceUser; // bir nesne oluşturuluyor
	try {
		// bazı işlemler yapılıyor
	}
	catch (...) { // hata nesneleri yakalanıyor
		delete ptr; // kaynaklar geri veriliyor
		throw; // hata nesnesi yeniden gönderiliyor
	}
 
	delete ptr; // işlevden normal olarak çıkılırsa dinamik nesnenin hayatı sonlandırılıyor.
}
```

Bir hata nesnesinin gönderilmesi durumunda da kaynakların güvenli bir şekilde geri verilmesi sağlanmak istenirse hem daha fazla kod yazılması gerekir hem de  yazılan kod çok daha karmaşık hale gelir. Dinamik olarak yaratılan nesnelerin sayısı birden fazla ise çok daha karışık bir durumun oluşacağı açıktır. Hata oluşumuna açık olan ve gereksiz bir karmaşıklığa neden olan bu kötü kodlama stilinden kaçınılmalıdır.

Bu amaçla tasarlanabilecek bir akıllı gösterici `(smart pointer)` sınıfı sorunu çözebilir. Bir akıllı gösterici nesnesinin kendi sonlandırıcı işlevinin çağrılmasıyla, akıllı göstericinin yönettiği dinamik nesnenin de hayatı sonlandırılabilir. Yerel bir akıllı gösterici nesnesi söz konusu olacağından artık işlevden ister normal yollarla ister bir hata nesnesi gönderilmesi yoluyla `(exception)` çıkılsın akıllı gösterici nesnesine bağlanmış olan dinamik nesnenin hayatı sonlanacak, böylece kaynak sızıntısı oluşmayacaktır.
İşte `unique_ptr` sınıfı bu amaçla tanımlanmış bir akıllı gösterici sınıfıdır.

`unique_ptr` sınıfı türünden bir nesne, gösterdiği dinamik nesnenin tek sahibi durumundadır.
`unique_ptr` nesnenin hayatı sonlandığında yani bir unique_ptr nesnesinin sonlandırıcı işlevi çağrıldığında bu nesnenin sahip olduğu dinamik nesnenin de hayatı sonlandırılır, yani o dinamik nesne `delete` edilir.

`unique_ptr` nesnesinin mülkiyetindeki dinamik nesnenin tek bir sahibi vardır ve bu semantik yapı kullanıcı kodlar tarafından da sürdürülmeli ve korunmalıdır.

Daha önceki örneğe geri dönüyouz:


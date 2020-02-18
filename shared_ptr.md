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

-bu maddenin yazımı devam ediyor-

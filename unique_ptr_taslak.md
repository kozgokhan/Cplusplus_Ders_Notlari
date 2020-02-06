## unique_ptr
`C++11` standartları ile birlikte standart kütüphaneye dahil edilmiş olan `unique_ptr` bir akıllı gösterici `(smart pointer)` sınıfıdır. 
Bu akıllı gösterici sınıfı genel olarak "tek sahiplik" `(exclusive ownership)` stratejisini gerçekleştirir.
Bir unique_ptr nesnesi bir dinamik sınıf nesnesini gösteren tek bir gösterici olarak kullanılır. `unique_ptr` nesnesi, kendi hayatı sona erince sahibi olduğu dinamik sınıf nesnesinin de hayatını sonlandırarak onun tutmakta olduğu kaynakların serbest bırakılmasını sağlar. 
Bu sınıfın temel varlık nedenlerinden biri, bir hata nesnesi `(exception)` gönderildiğinde söz konusu olabilecek kaynak sızıntısının `(resource leak)` engellenmesidir.

`unique_ptr` sınıfı `C++98` standartlarında var olan ancak dildeki araçların yetersizliğinden kaynaklanan kötü tasarımı nedeniyle eleştirilen `auto_ptr` sınıfının yerine getirilmiştir. `C++11` standartları ile `auto_ptr` sınıfı kullanımdan düşürülmüş `(deprecated)` onun yerine hem daha yalın ve daha net bir arayüze sahip olan hem de daha düşük kodlama hatası riski içeren `unique_ptr` sınıfı standart kütüphaneye eklenmiştir. `auto_ptr` sınıfının tasarlandığı dönemde `C++` dili taşıma semantiği, değişken sayıda tür parametresine sahip şablonlar `(variadic templates)` gibi araçlara sahip değildi. `C++11` standartlarıyla dile kazandırılan bu araçlar `unique_ptr` sınıfının güvenli bir biçimde tasarlanmasına olanak sağlamıştır.


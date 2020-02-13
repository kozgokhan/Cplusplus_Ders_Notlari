## make_unique ve hata güvenliği

Aşağıdaki deyimlerde `exp1` ve `exp2` ifadelerinin ele alınma sırası ve `f`, `g` ve `h` isimli işlevlerinin çağrılma sırası hakkında ne söylenebilir? 
Şimdilik `exp1` ve `exp2` ifadelerinin işlev çağrısı içermediğini düşünelim.

Örnek 1(a)
```
f(exp1, exp2);
```

Örnek 1(b)
```
f(g(exp1), h(exp2));
```

Sorunun yanıtını verebilmemiz için bazı temel kuralları çok iyi anlamış olmamız gerekiyor.
* Bir işlev çağrısının fiilen gerçekleştirilmesinden önce, işleve gönderilen tüm argüman ifadelerin tamamen değerlendirilmiş olması gerekiyor. 
Eğer işlev çağrısında kullanılan argüman olan ifadeler yan etki(ler) `(side effect)` içeriyor ise bu yan etkiler işlev çağrısından önce gerçekleşmiş olmak zorunda.

* Çağrılmış olan bir işlevin kodunun yürütülmesinin başlamasından sonra, çağıran işlevin içinde yer alan hiç bir ifade ele alınmaz ya da bir ifadenin ele alınması sürdürülmez. 
İşlev kodlarının yürütülmesi kesiksizdir. Yani bir işlev kodu yürütülürken programın akışı aynı zamanda başka bir işlev koduna girmez.

* İşlevlere gönderilen argüman olarak kullanılan ifadeler (başka kurallar tarafından bir belirleyicilik oluşturulmamışsa) herhangi bir sırada ve geçişli `(interleaved)` olarak yürütülebilir. 
Burada geçişli sözcüğünü şu anlamda kullanıyoruz: Programın çalışması sırasında ifadelerden biri kısmen yapılabilir. 
Daha sonra bir başka ifade kısmen yapılabilir. Sonra daha önce kısmen yapılan ifade yeniden ele alınabilir.

```
f(exp1, exp2);
```

Bu deyim için tek söyleyebileceğimiz hem `exp1` hem de `exp2` ifadelerinin `f` işlevi çağrılmadan yürütülmüş olacaklarıdır.
Derleyici exp1 ifadesini, exp2 ifadesinden önce ya da sonra yürütebilecek bir kod oluşturabilir. 
Hatta derleyici `exp1` ve `exp2` ifadelerini geçişli olarak yürütecek bir kod da oluşturabilir. Örneğin üretilen kodun çalışma zamanındaki akışı şöyle olabilir:

1. exp1 kısmen yürütüldü
2. exp2 kısmen yürütüldü 
3. exp1 tamamen yürütülmüş oldu
4. exp2 tamamen yürütülmüş oldu
5. Şimdi de diğer deyime bakalım:

```
f(g(exp1), h(exp2));
```

Yukarıda açıkladığımız kurallardan aşağıdaki sonuçları çıkartabiliriz:

* `exp1` ifadesi programın akışı `g` işlevine girmeden önce yürütülmüş olmalı.
* `exp2` ifadesi programın akışı `h` işlevine girmeden önce yürütülmüş olmalı.
* `g` ve `h` işlevlerinin çalışması programın akışı `f` işlevine girmeden önce bitmiş olmalı.
* `exp1` ve `exp2` ifadeleri birbiriyle geçişli olarak yürütülebilir. 
Fakat bu ifadelerin birinin yürütülmesi sırasında bir işlev çağrısı gerçekleşirse çağrılan işlevin kodu kesiksiz ve geçişsiz yürütülür. 
Örneğin `g` işlevinin kodu çalışmakta iken `exp2` ifadesi kısmen yürütülemez. 
Ancak `g` ya da `h` çağrısının hangisinin daha önce gerçekleşeceği konusunda bir güvence söz konusu değildir.

Peki şimdi buradan hareketle konuyu hata güvenliğine `(exception safety)` getirelim. 
Bir başlık dosyasından aşağıdaki gibi bir işlev bildirimi gelmiş olsun. Bildirimdeki `T1` ve `T2`'nin sınıf türleri olduğunu düşünelim:

Örnek - 2

```
void f(T1 *, T2 *);
```
Bildirilen bu işlevin kodda herhangi bir yerde aşağıdaki gibi çağrıldığını düşünelim:

```
f(new T1, new T2);
```
Acaba yukarıdaki gibi bir çağrı hata güvenliği açısından bir soruna yol açar mı? Evet, işlev çağrısında hata güvenliğine ilişkin birden fazla sorun var:

```
new T1
```
gibi bir ifadeye teknik olarak `"new ifadesi"` deniyor. Bir `new` ifadesi karşılığında ne yapıldığını bir hatırlayalım. (Şimdilik `new` ifadesinin dizi  ya da diğer biçimlerinin söz konusu olmadığını düşünüyoruz):

* Bir bellek alanı elde edilir.
* Bu bellek alanında yeni bir nesne hayata getirilir. Yani ilgili sınıfın kurucu işlevi `(constructor)` çağrılır.
* Eğer hayata gelen nesne için çağrılan kurucu işlev bir hata nesnesi gönderirse `(exception throw ederse)` nesne için elde edilen bellek alanı geri verilir.

Bu şu anlama geliyor: Yukarıdaki çağrıdaki her iki new ifadesi de aslında iki ayrı işlev çağrısının yapılmasını sağlıyor:

1. `operator new` işlevine yapılan çağrı. (Çağrılan `operator new` işlevi global olabilir ya da sınıf tarafından sağlanmış `operator new` işlevi olabilir).
2. Sınıfın kurucu işlevine yapılan çağrı.

Peki, derleyici aşağıdaki sırayla yürütülecek bir kod oluşturursa neler olabilir?

1. `T1` türünden nesne için bellek alanı elde edilir.
2. `T1` nesnesi için kurucu işlev çağrılır.
3. `T2` türünden nesne için bellek alanı elde edilir.
4. `T2` nesnesi için kurucu işlev çağrılır.

Böyle bir işlem sıralamasında sorun şu:
Bir hata nesnesi `(exception)` gönderilmesi nedeniyle `3.` adım ya da `4.`adım başarısız olursa, `C++` standartları hayata getirilmiş olan `T1` nesnesi için sonlandırıcı işlevin çağrılmasını ve nesne için edinilmiş bellek alanının geri verilmesini zorunlu kılmıyor. 
Bu da hem kaynak sızıntısı `(resource leak)` hem de bellek sızıntısı anlamına geliyor.

Aşağıdaki gibi bir işlem sırası da söz konusu olabilir:

1. `T1` türünden nesne için bellek alanı elde edilir.
2. `T1` türünden nesne için bellek alanı elde edilir.
3. `T2` nesnesi için kurucu işlev çağrılır.
4. `T2` nesnesi için kurucu işlev çağrılır.

Böyle bir sıralamada bir değil farklı etkilere neden olabilecek iki ayrı hata güvenliği sorunu var:

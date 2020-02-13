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

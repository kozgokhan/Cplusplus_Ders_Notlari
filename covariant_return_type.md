## eş geri dönüş değeri türü (covariant return type)

Türemiş bir sınıfın taban sınıfının bir sanal işlevini ezecek _(override)_ bir işlevinin, 
taban sınıf işleviyle hem aynı imzaya hem de aynı geri dönüş türüne sahip olması gerekir:

```
class Car {
public:
	//...
	virtual double getCylinderVolume()const = 0;
};

class Mercedes : public Car {
public:
	//...
	virtual float getCylinderVolume()const override;
};
```

Yukarıdaki kodda _Mercedes_ sınıfı taban sınıfı olan _Car_ sınıfının saf sanal _getCylinderVolume_ işlevini ezerken _(ovverride)_ farklı bir geri dönüş değeri bildirmiş.
_C++_ dilinin kurallarına göre bu durum bir sentaks hatası oluşturuyor. 
Ancak bu durumun "eşdeğişken geri dönüş türü" _(covariant return type)_ denen bir istisnası var.
_B_ bir sınıf türü olmak üzere, taban sınıfın sanal işlevinin geri dönüş değeri _B*_ türünden ise, türemiş sınıfın bu işlevi ezecek işlevinin geri dönüş değeri, 
eğer _D_ sınıfı _B_ sınıfından public türetmesiyle elde edilmiş bir sınıf ise, _D*_ türünden olabilir.
Aynı istisna referans semantiği için de söz konusu:
_B_ bir sınıf türü olmak üzere, taban sınıfın sanal işlevinin geri dönüş değeri _B&_ türünden ise türemiş sınıfın bu işlevi ezecek işlevinin geri dönüş değeri, 
eğer _D_ sınıfı _B_ sınıfından _public_ türetmesiyle elde edilmiş bir sınıf ise, _D&_ türünden olabilir.
_Car_ sınıfının arayüzünde bulunan sanal kurucu işlev olarak görev yapacak saf sanal _(pure virtual)_ bir _clone_ işlevinin yer aldığını düşünelim:


```
class Car {
public:
	//...
	virtual Car *clone() = 0;
};

class Mercedes : public Car {
public:
	//...
	virtual Mercedes *clone()override; //covariant return type
};

void carGame(Car *p)
{
	Car *pNewCar = p->clone();
	//
}
```

_Mercedes_ sınıfı _Car_ sınıfının _clone_ işlevini ezerken işlevin geri dönüş türünü _Mercedes *_ olarak değiştirdiğini görüyorsunuz. 
Bu kod geçerli, çünkü her _Mercedes_ nesnesi aynı zamanda bir _Car_ nesnesi. 
Peki neden böyle bir istisnaya duyulmuş? 
Eşdeğişken geri dönüş türü, türemiş sınıf nesnelerinin türemiş sınıf arayüzüyle işlendiği durumlar için önemli bir avantaj sağlıyor:

```
Mercedes *getNewMercedes();

int main()
{
	Mercedes *pm1 = getNewMercedes();
	Mercedes *pm2 = pm1->clone();
	//...

}
```

Yukarıdaki kodda bildirilen _getNewMercedes_ isimli global işlev dinamik bir _Mercedes_ nesnesinin adresini döndürüyor. 
_main_ işlevi içinde bu işlevin geri dönüş değerinin doğrudan _Mercedes_ türünden bir göstericiye ilk değer verdiğini görüyorsunuz. 
Eğer _Mercedes_ sınıfının _clone_ işlevinin geri dönüş türü _Car *_ olsaydı, yani eşdeğişken geri dönüş türü istisnası olmasaydı bu kod geçersiz olacaktı. 
Kodun geçerliliğini sağlamak için _static_cast_ tür dönüştürme işlecini kullanmamız gerekecekti:

```
int main()
{
	Mercedes *pm1 = getNewMercedes();
	Mercede *pm2 = static_cast<Mercedes *>(pm1->clone());
	//...

}
```

Bir örnek de _FactoryMethod_ tasarım kalıbının gerçekleştiriminden verelim: 
Aşağıdaki kodda _Car_ sınıfının ara yüzünde fabrika metodu olarak görev yapacak _getServiceStation_ isimli bir saf sanal işlev yer alıyor. 
_Car_ sınıfından kalıtım yoluyla elde edilecek somut _(concrete)_ sınıflar bu işlevi ezerek kendilerine uygun somut bir _ServiceStatiton_ nesnesini referans semantiği ile geri döndürecekler:

```
class ServiceStation {
	///...
};

class Car {
public:
	//...
	virtual ServiceStation &getServiceStation() = 0;
};

class Mercedes;

class MercedesServiceStation : public ServiceStation {
	//...
};

class Mercedes : public Car {
public:
	MercedesServiceStation &getServiceStation()override;
	//...
};
```

Burada dikkat edilmesi gereken bir nokta daha var: 
Eş geri dönüş türünün kullanılabilmesi için _Mercedes_ sınıfının _getServiceStation_ işlevinin bildiriminden önce _MercedesServiceStation_ sınıfının sadece bildirilmiş olması _(forward declaration)_ yeterli değil, 
sınıf tanımlanmış olmalı. 
Derleyici _MercedesServiceStation_ referansını (ya da adresini) _ServiceStation_ referansına (ya da adresine) dönüştürebilmek için _MercedesServiceStation_ sınıfının belleğe yerleşim verilerine erişmek zorunda.

Eşdeğişken geri dönüş türünün avantajı bize her zaman uygun bir soyutlama _(abstraction)_ seviyesi sağlaması. 
Eğer _Car_ sınıfı türünden nesneler ile çalışıyor isek soyut _(abstract)_ bir _ServiceStation_ elde edeceğiz. 
Eğer spesifik bir _Car_ türü, örneğin _Mercedes_ türü ile çalışıyor isek somut bir _MercedesServiceStation_ nesnesi elde edeceğiz:

```
Car *getNewCar();
Mercedes *getNewMercedes();

int main()
{
	Car *pcar = getNewCar();
	ServiceStation &rss = pcar->getServiceStation();
	Mercedes *pm = getNewMercedes();
	MercedesServiceStation &rmss = pm->getServiceStation();
	//...
}
```

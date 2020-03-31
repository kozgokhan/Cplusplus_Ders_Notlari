_inline_ isim alanları _(inline namespaces)_ _C++11_ standartları ile dile eklenmiş bir özellik. 
Bir isim alanı _inline_ anahtar sözcüğü ile bildirildiğinde bu isim alanı içindeki isimler onu içine alan isim alanı içinde doğrudan görülür hale geliyor:

```
inline namespace Neco {
	int x = 10;
}
```

gibi bir isim alanı kullanımının

```
namespace Neco {
	int x = 10;
	//
}


using namespace Neco;
```

gibi bir koda karşılık geldiğini düşünebiliriz. Şimdi de aşağıdaki koda bakalım:

```
namespace A {
	inline namespace B {
		int x = 10;
	}
}

int main()
{
	A::B::x = 20;
	A::x = 20;
}
```

_x_ isimli değişkenin _A_ isim alanı içinde yer alan _B_ isim alanı içinde tanımlandığını görüyorsunuz. 
_B_ isim alanı _inline_ olarak tanımlandığından _main_ işlevi içinde _x_ ismini

```
A::B::x
```

biçiminde kullanabileceğimiz gibi doğrudan

```
A::x
```

biçiminde de kullanabiliyoruz. Birden fazla isim alanını da _inline_ olarak tanımlamak mümkün:

```
namespace A {
	inline namespace B {
		inline namespace C {
			int x = 10;
		}
	}

	inline namespace D {
		int y = 20;
	}
}

int main()
{
	A::x = 1;
	A::B::x = 2;
	A::B::C::x = 3;

	A::y = 4;
	A::D::y = 5;

}
```

Yukarıdaki kodda _B_, _C_ ve _D_ isim alanları _inline_ olarak tanımlandığından _main_ işlevi içinde _x_ ve _y_ isimlerinin doğrudan _A_ ismiyle nitelenerek kullanılması mümkün oluyor.

İyi de, ne işe yarıyor inline isim alanları? _inline_ isim alanlarının sağladığı en önemli avantajlardan biri sürüm _(version)_ kontrolü. Aşağıdaki koda bakalım:

```
namespace Networking {
	class TCPSocket {
		//
	};
	class UDPSocket {
		//
	};
}
```

_Networking_ isim alanı içinde _TcpSocket_ ve _UDPSocket_ sınıfları tanımlanmış. 
Bu isim alanı içinde tanımlanan bu sınıflar birçok kaynak dosya tarafından kullanılıyor olsun. 
Şimdi _TCPSocket_ sınıfında yaptığımız bazı geliştirmeler sonucunda yeni bir sınıf oluşturduğumuzu düşünelim. 
Yani _TCPSocket_ sınıfının ikinci bir sürümünü oluşturduk. 
Müşteri kodların eski _TCPSocket_ sınıfı yerine yeni _TCPSocket_ sınıfını kullanmalarını istiyoruz. 
Bunu sağlamaya yönelik birçok seçenek olabilir. 
Ancak en etkin çözümlerden biri bu sınıfları ayrı birer isim alanı içine koymak:

```
namespace Networking {
	namespace Version1 {
		class TCPSocket {
			//
		};
	}

	namespace Version2 {
		class TCPSocket {
			//
		};
	}

	class UDPSocket {
		//
	};
}
```

Ama müşteri kodlar _TCPSocket_ sınıfını

```
Networking::TCPSocket
```

biçiminde niteleyerek kullanıyorlardı değil mi? 
Müşteri kodları hiç değiştirmeden sürüm geçişini sağlayabilir miyiz? 
Evet, tahmin edebileceğiniz gibi _inline_ isim alanı burada devreye giriyor. 
Yeni sürümün yer aldığı _Version2_ isim alanını inline yapıyoruz:

```
namespace Networking {
	namespace Version1 {
		class TCPSocket {
			//
		};
	}

	inline namespace Version2 {
		class TCPSocket {
			//
		};
	}

	class UDPSocket {
		//
	};
}
```

Artık daha önce eksi sürümü kullanıyor olan müşteri kodlar yeniden derlendiklerinde yeni sürümü kullanıyor olacaklar. 
_inline_ isim alanları kütüphanenin gerçekleştirimini yapan programcıya varsayılan _(default)_ bir isim alanı belirleme olanağı sağlıyor. 
Yukarıdaki örnekte, tüm kullanıcı kodların  _TCPSocket_ sınıfının son sürümünü kullanmaları için _Version2_ isim alanını inline yaptık. 
Belirli bir nedenden dolayı eski _TCPSocket_ sınıfına geri dönmemiz gerekitse bu kez _Version1_ isim alanını _inline_ yapabiliriz. 
İstediğimiz sayıda isim alanını inline yapabiliriz. Örneğin kütüphanemizin _3._ sürümünde _UDPSocket_ sınıfının da yenilendiğini düşünelim:

```
namespace Networking {
	namespace Verion1 {
		class TCPSocket;
		class UDPSocket;
	}

	inline namespace Version2 {
		class TCPSocket;
	}

	inline namespace Version3 {
		class UDPSocket;
	}
}
```

_Version3_ isim alanı inline olarak tanımlandığından kullanıcı kodlar _UDPSocket_ sınıfının son sürümünü (bu isim alanı içindeki sürümünü) kullanıyor olacaklar. 
Dilediğimiz zaman bu içsel isim alanını _inline_ olmaktan çıkartıp _Version1_ isim alanını inline yaparak o sürümdeki sınıfı kullanmaya geri dönebiliriz.

_inline_ anahtar sözcüğünün kullanımını önişlemci koşullu derleme _(conditional compiling)_ komutlarına da bağlayabiliriz:

```
namespace Networking {
	namespace Version1 {
		class TCPSocket;
		class UDPSocket;
	}

	inline namespace Version2 {
		class TCPSocket;
	}

#ifndef USE_RAW_SOCKETS
	inline
#endif
	namespace Version3 {
		class UDPSocket;
	}

#ifdef USE_RAW_SOCKETS
	inline
#endif
	namespace RawUDPSockets {
		class UDPSocket;
}
```

Yukarıdaki kodda _USE_RAW_SOCKETS_ makrosu tanımlanmamış ise _Version3_ isim alanı _inline_ yapılmış olacak. 
Böylece bu isim alanı içindeki _UDPSocket_ sınıfı doğrudan _Networking_ isim alanında görülüyor olacak. 
Eğer _USE_RAW_SOCKETS_ makrosu tanımlanmış ise bu kez _RawUDPSockets_ isim alanı _inline_ yapılmış olacak. 
Bu durumda da bu isim alanı içindeki _UDPSocket_ sınıfı doğrudan _Networking_ isim alanında görülüyor olacak. 
Bir başka deyişle _UDPSocket_ sınıfının hangi sürümünün kullanılacağı _USE_RAW_SOCKETS_ makrosunun tanımlanmış olup olmamasına bağlı.

_inline_ isim alanlarının bir başka kullanım nedeni _ADL (argument dependant lookup)_. Bu konu biraz daha teknik. Aşağıdaki koda bakalım:

```
namespace Nec {
	namespace Nested {
		class C {
		    //...
		};
		//...
	}
    using namespace Nested;
    void func(Nested::C);
}

int main()
{
	Nec::Nested::C x;

	func(x); //gecersiz
}
```

 _Nec_ isim alanı içinde bildirilen _func_ isimli işlevin parametresinin _Nested_ isim alanı içinde tanımlanan _C_ sınıfı türünden olduğunu görüyorsunuz. _Nec_ isim alanı içinde yapılan _using namespace_ bildirimi ile _Nested_ isim alanı içindeki isimler _Nec_ isim alanı içinde görünür kılınmış. Ancak _main_ işlevi içinde yapılan _func_ çağrısı geçerli değil. Yapılan _using namespace_ bildirimine karşın argümana bağlı isim arama _(ADL)_ burada devreye girmiyor. Yeni kurallara göre _Nested_ isim alanının _inline_ olarak bildirilmesi durumunda argümana bağlı isim arama ile _func_ isminin _Nec_ isim alanında da aranması garanti altında.

---
layout: post
title:  "C de renkli printfler 🌈 (norminetteyi kızdırmadan)"
author: kımırtı
category: C
---

Aynı `\n` (newline) `\t` (tab) `\0` (C için EOL) gibi, terminalde renkler de ANSI escape kodları ile belirtilir.

Mesela `\e[0;31m` kırmızı rengini `\e[0;36m` mavi rengini temsil eder (en azından C için).

## Nasıl kullanılır?

Herhangi bir özel kodu yazdırdığınızda bir sonraki renk koduna kadar o renk kalır.

```c
int	main(void)
{
	printf("Ben renksizim\n");

	printf("\e[0;31m"); // RED

	printf("Uuuv ben kirmizi\n");

	printf("\e[0;36m"); // BLUE

	printf("Merhaba ben volkan konak\n");
}
```
![ex0](/assets/cde_renkli_terminaller/ex0.png)

Eğer renklerin taşmamasını isterseniz `\e[0m` (reset) kodunu kullanabilirsiniz.

```c
int main(void)
{
    printf("\e[0;31m"); // RED

    printf("Bu kırmızı\n");

    printf("\e[0m"); // RESET

    printf("Merhaba ben vol...\n");
}
```
![ex1](/assets/cde_renkli_terminaller/ex1.png)


Aynı satırda kullandığınız renkleri resetlemek her zaman iyidir. Hata mesajları ekrana pembe yazılınca ayırt etmek zor oluyor.

```c
printf("\e[0;34m G \e[0;31m o \e[0;33m o \e[0;34m g \e[0;32m l \e[0;31m e \e[0m\n");
```
![Deneme](/assets/cde_renkli_terminaller/google.png)

## Nasıl *güzel* kullanılır 

Tamam iyi güzel de böyle kullanmak acı verici. Her seferinde renkleri kopyala yapıştır. Ve de kodu incelerken hangi kodun hangi renk olduğunu göremiyoruz.

Bunun için çok güzel bir çözümümüz var. C de eğer iki string sabiti arasında sadece boşluk bırakırsanız. O bunları derleme sırasında birleştirir.

```c
// Derleme öncesi
printf("Hello" " " "World");
```
```c
// Derleme sonrası 🤯
printf("Hello World");
```

Bu da demek ki makroları kullanarak çok kullanışlı şeyler yapabiliriz.

Bir stringi cyan yapmak istiyorsak (`"Hello"`) başına ve sonun bu kodları boşluk bırakarak koymamız yeterli (`"\e[0;36m" "Hello" "\e[0m"`).
Ve eğer bu kodları tanımlayan makrolarımız da varsa (mesela `#define CYN "\e[0;36m"`) yerine yazabiliriz (`CYN "Hello" RST`).

```c
#include <stdio.h>

#define RED "\e[0;31m"
#define MAG "\e[0;35m"
#define RST "\e[0m"

int main(void)
{
    printf(RED "Ben kırmızı, " RST "ben beyaz, " MAG "ben de magenta O.O\n" RST);
}
```
![ex2](/assets/cde_renkli_terminaller/ex2.png)

## Moulinette notları

Şu ana kadar yaptığımız her şey norminetteye göre legal. Makro tanımlamak, stringleri birleştirmek vs... Molinetteye göre de legal.

Ancak dikkat etmek gereken şu ki renkli çıktı ile renksiz çıklı farklıdır. Eğer subjectte sizden hata durumunda ekrana *sadece* `"Error\n"` yazdırmanızı istiyorsa ve siz ekrana kırmızı yazdırırsanız patlayabilirsiniz. Çünki `"Error\n"` ve `"\e[0;36mError\n\e[0m"` farklı şeyler.

## Tatlı bir header dosyası 💅

ANSI kodları renklerle sınırlı değil. Bold yada underline yapabilirsiniz. Hatta yazının arkaplanını bile değiştirebilirsiniz. Bunların hepsini dahil eden [güzel bir gist](https://gist.github.com/RabaDabaDoba/145049536f815903c79944599c6f952a) buldum ve sadece header protection ekleyip norminetteye uygun hale getirdim. Kullanırken include etmeniz yeterli.

```c
#include <stdio.h>
#include "colors.h"

int main(void)
{
    printf(UMAG "Whoa\n" RST);
}
```

`colors.h` header dosyası. [Burdan](https://raw.githubusercontent.com/ytkimirti/libft/master/colors.h) raw olarak da indirebilirsiniz.
```c
#ifndef COLORS_H

# define COLORS_H

//Regular text
# define BLK "\e[0;30m"
# define RED "\e[0;31m"
# define GRN "\e[0;32m"
# define YEL "\e[0;33m"
# define BLU "\e[0;34m"
# define MAG "\e[0;35m"
# define CYN "\e[0;36m"
# define WHT "\e[0;37m"

//Regular bold text
# define BBLK "\e[1;30m"
# define BRED "\e[1;31m"
# define BGRN "\e[1;32m"
# define BYEL "\e[1;33m"
# define BBLU "\e[1;34m"
# define BMAG "\e[1;35m"
# define BCYN "\e[1;36m"
# define BWHT "\e[1;37m"

//Regular underline text
# define UBLK "\e[4;30m"
# define URED "\e[4;31m"
# define UGRN "\e[4;32m"
# define UYEL "\e[4;33m"
# define UBLU "\e[4;34m"
# define UMAG "\e[4;35m"
# define UCYN "\e[4;36m"
# define UWHT "\e[4;37m"

//Regular backgroundd
# define BLKB "\e[40m"
# define REDB "\e[41m"
# define GRNB "\e[42m"
# define YELB "\e[43m"
# define BLUB "\e[44m"
# define MAGB "\e[45m"
# define CYNB "\e[46m"
# define WHTB "\e[47m"

# define RST "\e[0m"
#endif
```

🐛

---
layout: post
title: "Kurs AVR: Wstęp i środowisko"
categories: kurs-avr
author: "Kamil Foryszewski"
---
Zaczynając przygodę z mikrokontrolerami musimy zdawać sobie sprawę z tego jakie jest ich rzeznaczenie, ogólny sposób działania i przede wszystkim możliwości.

Najważniejszym elementem każdego podzespołu elektronicznego jest jego nota katalogowa (datascheet). Znajdują się tam wszystkie niezbędne informacje na temat podzespołu i przykłady zastosowań.

Budowa mikrokontrolera to temat na osobny, obszerny artykuł. Na nasze potrzeby wystarczy zapamiętać poniższy schemat:

![Schemat mikrokontrolera](/static/uc.jpg)

Reasumując, możemy porównać mikrokontroler do komputera zamkniętego w układ scalony. Zasadnicza różnicą jest zastąpienie urzędzeń we/wy tak zwanymi układami peryferyjnymi. W zależności od posiadanego mikrokontrolera mamy ich więcej lub mniej. Są to głównie:

- timery(liczniki)
- GPIO (układy wejścia/wyjścia)
- różnego rodzaju przetworniki i ganeratory

Działanie mikrokontrolera polega na obsłudze tych peryferiów (odbieraniu, przetwarzaniu i wysyłaniu danych). Znając taki model mikrokontrolera znacznie łatwiej jest nam przejść do jego programowania, czyli opisania sekwencji czynności, które wykona mikrokotroler.

## Wybór mikrokontrolera

Wybór układu do programowania zależy przede wszystkim od potrzeb. Na potrzeby naszego kursu wybrałem mikrokontroler z rodziny AVR (warto poczytać o innych rozwiązaniach np. ARM, PIC), ze względu na mnogość przykładów i powszechność. Ważnymi parametrami, podobnie jak dla komputerów, są pamięć, peryferia i taktowanie. Mikrokontrolery AVR w większości programuje się podobnie, przykłady w kursie będą przedstawione na mikrokontrolerze ATmega8. Występuje on w różnych obudowach, jednak środek jest niezmienny.

![Atmega8 w obudowie TQFP](/static/mega1.jpg)
![Atmega8 w obudowie DIP](/static/mega2.jpg)

W zakładce _Niezbędne elementy_ zostały podane elementy, przy pomocy których będziemy mogli zacząć programowanie, jednak zanim przystąpimy do zakupu, warto przygotować nasz komputer do sprawnego pisania, kompilowania i wgrywania programu na mikrokontroler.

## Konfiguracja środowiska programistycznego

### MS Windows

#### Instalacja środowiska WinAVR

Pakiet WinAVR nie jest typowym IDE, zawiera w sobie wszystkie niezbędne narzędzia wymagane do skompilowania programu w języku C, a następnie wgrania go do procesora. Jest to więc zbiór narzędzi, przy pomocy których możemy zaprogramować mikrokontroler. Alternatywą, a raczaj bardziej rozbudowanym środowiskiem może być AVRstudio firmy Atmel. WinAVR należy pobrać i zainstalować korzystając z instrukcji instalatora.

Po zainstalowaniu w menu programowym powinniśmy ujrzeć taki oto zbiór narzędzi.

![WinAVR w Menu Start](/static/winavr.jpg)

Kolejnym etapem jest instalacja sterowników programatora, w naszym wypadku USBasp.

Należy pobrać [sterowniki](http://www.fischl.de/usbasp/usbasp-windriver.2011-05-28.zip) ([dla Windows 8 jest inna wersja](http://letsmakerobots.com/node/36841)), rozpakować do znanego folderu i podłączyć programator do portu USB. System powinien wykryć nowe urządzenie. Należy w trybie kreatora wybrać instalację sterowników z określonej lokalizacji. Wybierzmy folder, do którego rozpakowaliśmy nasze sterowniki. Operacja zakończy się potwierdzeniem pomyślnego zainstalowania sterowników. Jeżeli ktoś nie poradziłby sobie, w internecie jest dużo artykułów krok po kroku.

Po zainstalowaniu sterowników jesteśmy gotowi do napisania pierwszego programu i zaprogramowania nim pamięci nazego mikrokontrolera.


#### Pierwszy program na AVR

Teraz możemy przystapić do napisania pierwszego programu:

##### Konfiguracja pliku MAKEFILE

1. Plik MAKEFILE, czyli plik reguł, informuje kompilator o cechach szczególnych programu, czyli o rodzaju programowanego procesora, częstotliwości, używanego programatora, plikach składowych programu i wielu innych cechach.

2. Aby edytować plik MAKEFILE należy uruchomić program MFILE będący składową wcześniej zainstalowanego środowiska. Start→wszystkie programy→WinAVR→MFILE.

3. Następnie Makefile→main file name i wpisujemy nazwę pliku z rozszerzeniem .c, w którym będzie znajdował się kod naszego programu.

4. Teraz ustawiamy port programowania jako USB Makefile→port→usb.

5. Należy włączyć możliwość edycji : Makefile→enable editing of makefile

6. Następnie odnaleźć linijkę `AVRDUDE_PROGRAMMER = …` i wpisać

`AVRDUDE_PROGRAMMER = usbasp`

7. Na koniec zapisujemy plik, powinien się nazywać Mfile w folderze, w którym będziemy przechwywać kod naszego przyszłego programu.

##### Tworzenie programu sterującego w edytorze i kompilacja.

1. Start→wszystkie programy→WinAVR→Programers Notepad

2. Należy utworzyć nowy projekt File→new→project, a następnie nadać mu nazwę i wybrać folder przechowywania ten sam w którym jest zapisany plik Mfile.

3. W menu File wybieramy New File i klikamy c/c++.

4. Następnie zapisujemy plik w folderze naszego projektu i klikając prawym przyciskiem myszy na projekt wybieramy Add files... po czym przechodzimy do folderu projektu i wybieramy nasz zapisany plik.

5. Pora na nasz kod, na początku wklejmy do naszego pliku z rozszerzeniem .c poniższy listing:

```
	#include <avr/io.h> //dołączneie biblioteki obsługi mikrokontrolera  
	int main() //początek funkcji głównej  
	{  
	    DDRD = 0xFF; // ustawienie wszystkich nózek portu D jako wyjscia  
	    PORTD = 0b0000001; // stan wysoki na nóżce PD0  
	} // koniec funkcji  
```

6. Aby skompilować program należy w menu środowiska wybrać: Tools→Make All.

7. Kompilator powinien zwrócić komunikat > Process Exit Code: 0.

![Programmer's Notepad](/static/exit.jpg)

8. Aby wgrać skompilowany program do pamięci flash mikrokontrolera należy podłączyć moduł poprzez złącze programatora (ISP) i w menu środowiska wybrać Tools→Program, na programatorze powinna zapalić się czerwona dioda a program powinien zwrócić komunikat Process Exit Code: 0.

### Programowanie AVR w systemie Linux

Podczas pisania poradnika korzystałem z systemu Arch Linux, posiadałem mikrokontroler Atmega8.

#### Instalacja

Potrzebujemy następujących paczek (dla każdej dystrybucji będą one miały podobne nazwy, te przedstawione tutaj pochodzą z Debiana):
- gcc-avr
- binutils-avr (avr-binutils na Archu)
- avr-libc
- avrdude

Uruchamiać będziemy prosty kod, odpowiadający za miganie diodą.
    #include <avr/io.h>
    #include <util/delay.h>
    int main () {
        DDRB |= (1 << PB0);
        while (1) {
            PORTB |= (1 << PB0);
            _delay_ms(1000);
            PORTB &= ~(1 << PB0);
            _delay_ms(1000);
        }
    }       

#### Kompilacja
    # avr-g++ -mmcu=atmega8 dioda.cpp -o out
Argument -mmcu ustawia rodzaj mikrokontrolera, dokładną liste można zobaczyć w podręczniku (`# man avr-g++`).
Zmiana formatu:
    # avr-objcopy -O ihex out out.hex
Zmieniamy format z binarnego na szesnastkowy. Wejściem jest plik `out`, wynik zapisujemy do pliku `out.hex`.
Programowanie mikrokontrolera:
    # avrdude -c usbasp -p m8 -U flash:w:out.hex
Argument -c ustawia rodzaj programatora. Żeby zobaczyć możliwe wartości wystarczy uruchomić `# avrdude -c cosnieprawidlowego`
Argument -p ustawia rodzaj mikrokontrolera. Żeby zobaczyć możliwe wartości znowu wystarczy uruchomić avrdude z -p ustawionym na coś błędnego (jednak programatro musi być prawidłowy, np. `# avrdude -c usbasp -p cos`).
Argument -U ustawia operację, którą wykonujemy w formacie `rodzajpamięci:operacja:plik`, wszyskie opcje można zobaczyć w podręczniku avrdude (`# man avrdude`)

Avrdude wymaga uprawnień do korzystania z urządzeń USB. Możemy za każdym razem uruchamiaś avrdude jako root, albo odpowiednio skonfigurować udev (wystarczy skopiować zasady z [tej strony](https://wiki.archlinux.org/index.php/Udev#Accessing_firmware_programmers_and_USB_virtual_comm_devices), a następnie załadować nowe zasady `# udevadm control --reload-rules`, albo `# udevadm trigger`, jeżeli nie załadują się one automatycznie).

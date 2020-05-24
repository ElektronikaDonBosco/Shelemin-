# Sarrera
 Gure proiektua, theremin batean oinarrituriko musika instrumentu bat egitea zen. Guk egindako instrumentua theremin elektronikoa bat da, distantzia sentsoreak erabiliz tonoa eta bolumena kontrolatzeko antenak erabili ordez.

Zer da theremin bat? Lehenengoetako musika-tresna elektronikoetako bat da, musikariaren kontaktu fisikorik gabe kontrola daitekeena. Berezko instrumentua antenaz eta osziladorez osatuta dago, musikariaren eskuaren posizioa detektatzen dutenak eta frekuentzia eta bolumena kontrolatzen dutenak. Hau, instrumentu analogiko bat da eta guk gure erronkan digitalizatu egin dugu. 
# Eskaera
Proiektu honetan zenbait gauza eskatzen zitzaizkigun: Lehenengoa eta garrantzitsuena, bolumena eta tonoa kontrolatzea zen. Behin hau lortu ondoren, efektuak gehitu behar ziren, uhinen formak aldatuz pad batzuen bitartez eta potentziometro batzuen bitartez balioak estabilizatu.
# Proiektuaren zatiak
Ondorengo argazkian, gure proiektuko eskemako blokeak ikus ditzazkegu, txosten honetan zehar aztertuko eta esplikatuko ditugunak. ![](https://user-images.githubusercontent.com/64248787/80187030-40527a80-860f-11ea-9163-4b74fddd85d5.png)
## Teensy 4.0
Teensy 4.0-a gailuaren garuna da. Merkatuko mikrokontrolagailu azkarrenetako bat da bere ARM Cortex-M7 prozesagailuari esker. Arduino IDE-arekin erabili daiteke eta horri esker bere erabilera oso erraza da.
 
![](https://user-images.githubusercontent.com/64248787/80187390-da1a2780-860f-11ea-82e2-7b60e989e901.png)

Mikrokontrolagailu honen abiadura dela eta, audioarekin zerikusia duten proiektu eta sistemetan erabiltzen da gehien bat. Audio sistema sortzerakoan, bloke bidezko programazioa erabiltzen da. 
## Arduino Nano
Proiektuan erabilitako laser-distantzia sentsoreen irakurketa abiadura Teensy-ren irakurketa abiadura bainon askoz motelagoa zenez, audioan saltokako efektua nabaritzen zen. Hori dela eta, proiektuan arduino nanoa sartzea erabaki genuen. Honela, Arduinoak irakurtzen ditu distantzia sentsoreen datuak gero Teensy-ri bidaltzeko. 
## Sentsoreak
Adafruit MPR121 Capacitive touch sentsorea erabili dugu efektuak kontrolatzen dituzten pad-ak sortzeko. Honen ordez botoiak edo potentziometroak erabili daitezke, azken hauek etorkizun batean txertatzeko asmoa dugularik. 
Berezko thereminek dituzten antenak ordezkatzeko VL530X laser sentsoreak erabili ditugu, hauen zeregina eskuaren posizioa detektatu eta horrela frekuentzia eta bolumena kontrolatzea delarik. 
# Funtzionamendua
* VL530X Sentsoreek eskuen posizioa detektatzen dute. Hauetako bat bolumenari izendatua eta bestea audioaren frekuentziari izendatua dago. Ezker eskuaren parekoa frekuentziari izendaturik dago eta eskuinekoa berriz bolumenari. Eskuak bertikalki mugitu behar dira sentsorearen parean, ardatzetik gehiegi urrunduz gero sentsoreak ezer ez detektatzea gerta litekeelako.

* Posizioari buruzko informazioa Arduino Nanoak irakurtzen eta prozesatzen du, Teensy 4.0-ra pin analogiko bidez bidali aurretik. Deskargaturiko libreria batek egiten du prozesatzearen lan hau. Kalkulu matematiko batzuekin, sentsoreak botatzen duen laserrak parean duen eskua jo eta berriz jaso arte egiten duen denbora kontuan hartuz, distantzia ateratzen du bertatik, mm-tan ain zuzen ere. Lortutako emaitz hori map baten bitartez 0 eta 255 arteko pareko balio batera egokitzen dugu, seinale analogikoa lortzeko, eta balio hau bidaltzen da Teensy-ra frekuentzia eta bolumena egoki ditzan. 
```cpp
void loop() {
  read_dual_sensors();
    int x = map(distantzia1.RangeMilliMeter, 0, 500, 0, 255);
    if ( x >= 255) {
      x= 255;
      }
    int y = map(distantzia2.RangeMilliMeter, 0, 500, 0, 255);
        if ( y >= 255) {
      y= 255;
      }
    analogWrite (9,x);
    analogWrite (10,y);
  delay(100);
}
```

* Bi mikrokontrolagailuen pin analogikoen artean zirkuitu estabilizatzaile bat dugu, tentsio estabilizatzaile eta tentsio zatitzaile batez osatua: Beraz, Arduinok sentsoreen balioak irakurtzen ditu, RC iragazkien bidez azken irakurketen batez bestekoa egiten du eta gero Teensy-tik irakurtzen dira balio horiek. Baina Teensy-ren eta Arduino Nanoren arteko parametroak desberdinak direnez, tentsioa estabilizatu ondoren tentsio zatitzaileaz tentsioa txikitzen da Teensy-k bere sarreran balio egokiak jaso ditzan. 

* Bestalde, MPR121 sentsorea dugu Teensy-ra konektatua audio efektu ezberdinak kontrolatu ahal izateko, capacitive-touch izeneko teknologia bidez. Bertan, 6 pin ditugu konektaturik eta heuretako bat ikutu eta askatzean seinale bat bidaltzen dio Teensy-ari, honek aldaketak eragin ditzan ematen ari den audioko frekuentzian.

# Softwarea
Proiektu hau egiterakoan, Arduino Nano eta Teensy 4.0 erabili ditugunez, bi programa egin dira. Programa hauek egiteko erabili dugun programa Arduino izan da eta muntatu aurretik simulazioak egiteko erabili dugun programa Proteus. Gure denbora eta proiektuan zehar egin beharrekoak antolatzeko, trello herraminta erabili dugu. 
Gainera, gure proiektuaren audio sistema sortzeko “Teensy Audio Library” softwarea erabili dugu. 
## Teensy Audio Library
Soinua sintetizatzea, erreproduzitzea eta analisia erraztea da software honen helburu nagusia. Liburutegiak hainbat audio “objektu” eskeintzen ditu, Arduinoko sketch-a abian dagoen bitartean prozesa daitezkeenak. Objektu hauek Arduinoko funtzio simple bidez kontrola daitezke. 

![](https://user-images.githubusercontent.com/64248787/81096546-d9826a80-8f06-11ea-8129-5ba2de91252d.png)
## Arduino IDE
Java programazio hizkuntzan idatzitako aplikazio bat da, Arduino plaketan eta bateragarrietan erabili daitekeena C++ hizkuntzan oinarritutako programak idatzi eta kargatzeko. 
### Teensy 4.0-aren programa Arduino
Programa honen hasieran, “Teensy audio library-n” sortutako audio sistema txertatzen da hasteko. 
![](https://user-images.githubusercontent.com/64248787/81096204-56611480-8f06-11ea-8f3d-2e71e6663667.png)

Ondoren, bertan agertzen diren elementuak izendatu eta inizializatzen dira. 
Gero, Arduinoko informazioa jasotzen du, izendatu, map funtzioarekin limitatu eta audio sistemako seinalearekin lotzen da eta bukatzeko, MPR 121sensoreari egokitutako pad bakoitzari efektu bat izendatzen zaio. 
### Arduino Nano-ren programa
Programa hau bi VL530X sentsoreak irakurtzeko, eskuak sentsoreetatik ze distantziatara dauden kalkulatzeko, eta Teensy 4.0-arekin komunikatzeko erabiltzen da. Deskargatutako Libreria bat modifikatuta lortutako programa da, hau da, ez da guk hutsetik egiten hasia, eta bost zati nagusitan banaturik dago.

Lehen zatian pinak definitzen eta Libreriak deitzen dira, Adafruit_VL530X.h libreriari zehazki. Sentsoreekin konektatzeko 6, 7, A4(SDA0), A5(SCL0) eta GND pin-ak erabiltzen dira, eta Teensy-ra konektatzeko berriz 9 eta 10. Hala ere, 6 eta 7 pin-ak bakarrik izendatzen dira zati honetan, sarrera digitalekoak direlako.
```cpp
#include "Adafruit_VL53L0X.h" // libreria gehitzen da
```

Bigarren eta hirugarren zatiak, funtzio bana dira. Lehenengoan, setID() funtzioan, XSHUT pin-en bidez sentsoreak aktibatu eta desaktibatzen dira dena ondo doan zihurtatzeko eta bigarren funtzioan, hau da, read_dual_sensors() funtzioan eskuak sentsoreetatik ze distantziara dauden kalkulatzen du.

![](https://user-images.githubusercontent.com/64248787/82577463-50686600-9b8b-11ea-8733-27a32ca2e361.png)

Laugarren zatian, lehendik izendatu gabe zeuden 9 eta 10 irteerako pin-ak izendatzen dira, XSHUT itzalerako pin-ak abiarazi eta setID() funtzioari deitzen zaio.

Bostgarren eta azken zatian, lehenik read_dual_sensors() funtzioari deitzen zaio, sentsoreen balioak jakiteko eta hauek kalkulatu ondoren analogikoki bidaltzen dira Teensy-ra aurreko zatian izendatutako 9 eta 10 pin-en bitartez. Programa amaitzeko delay(100) bat jartzen zaio, segundu bat itxaron dezan, satura ez dadin.

## Proteus
Programa honi esker proiektuan zehar beharrezko zirkuituak simulatu ditzazkegu, montatu aurretik antzemanez arazo posibleak. Horrez gain, gure PCB-ak sortzeko herraminta bikaina da. 
## Trello
Proiektuak administratzeko software bat da, gure proiektua eta gure denbora antolatzeko erabili duguna. Aplikazioaren bista orokorra zutabetan banatutako taula bat da, zutabeetako bakoitza kaxetan banatua dagoelarik. Kaxa hauek zutabeetan zehar mugitu daitezke eta bakoitzak eginbehar bati egiten dio erreferentzia. Eginbehar hauek abisu bilakatu daitezke eta taldekide bati edo hainbati esleitu. 

# Muntatze prozesua eta doiketak
1. Teensy 4.0-arekin frogak egin eta erabiltzen ikasi genuen. Audio sistema sinple bat sortu genuen proiektuaren hasteko.

![](https://user-images.githubusercontent.com/64248787/80192917-37b27200-8618-11ea-9991-f67c60e7aa78.png)

2. Ultrasoinu eta laser sentsoreekin frekuentzia eta bolumena kontrolatu genituen programa separatuen bidez eta hainbat froga egin ondoren, erabaki genuen VL530X laser sentsoreak gure proiektuarentzat egokiagoak zirela eskeintzen zuten zehaztasuna zela eta. 

3. Programa bereiztuekin frekuentzia eta bolumena kontrolatzea lortu ondoren, programa bakar batean sartu genuen dena eta bertan arazo nagusi bat sortu zen: Teensy-aren irakurketa abiadura lasar sentsoreen irakurketa abiadura bainon askoz azkarragoa zenez, audioan saltokako efektua nabaritzen zen.

4. Arazo hori konpontzeko, proiektuan Arduino Nano eta zirkuitu estabilizatzaile bat sartu genituen: Arduinok irakurtzen ditu sentsoreen balioak, RC iragazki batzuez azken irakurketen batez bestekoa egiten dugu, eta Teensy-tik irakurri iragazki horien balioak.

5. Karkasa, metakrilatoz egin genuen. Beharrezko piezak konserjeak moztu zizkigun eta gainuntzeko zuloak taladro bidez egin genituen. Hona hemen neurriak: 
![](https://user-images.githubusercontent.com/64248787/80192962-4b5dd880-8618-11ea-84a2-11ea4a48b9a4.png)
6. Elikatze iturriari dagokionez, hasiera batean guk geuk egitea pentsatu genuen baina azkenean erostea erabaki genuen proiektuaren beste zati batzuetan pasatzeko denbora. Erositako elikatze iturria bi USB portukoa da, batak Arduino Nano-a eta besteak Teensy 4.0a elikatzen bait-ditu.  
# Kalkuluak
Proiektu honetan kalkulu gehienak Arduinoko libreriek edo sentsoreek egiten dituzte. Hala ere, badira egin behar izan ditugun zenbait kalkulu.

Arduinoak berak egindako kalkulu bat adibidez, map funtzioaren bitartez sentsoreekin kalkulaturiko distantzia balio analogiko bihurtzen du, hau da, eskua 0mm eta 500mm artean dagoen bitartean Teensy-ra 0 eta 255 arteko balio analogiko bat bidaliko da, baliokidea noski.

Eskuz egin beharreko kalkulua, Arduino Nanoa eta Teensy-a konektatzen zituen tentsio zatitzaileko erresistentzien eta tentsio estabilizatzaileko kondentsadorearen kalkuluak egitea zen. Kondentsadorearena probatzen joan ginen behi tentsio zatitzailea prest zegoela, eta emaitzarik honena ematen zuena aukeratu genuen, 470μF-koa. Tentsio zatitzaileko erresistentziak lortzeko ordea, nahikoa zen hasierako tentsioa eta irteerako tentsioa jakitea.
Beraz, Arduino Nanotik ateratzen zen tentsioa 5V-koa zela genekienez eta Teensy-ra heldu beharrekoa 3.3V-koa, hurrengo formula erabiliz atera genituen 1.700Ω eta 3.300Ω-eko erresistentziak erabili behar genituela:
![](https://user-images.githubusercontent.com/64248787/82574530-928fa880-9b87-11ea-8bd2-a5eabe2cc141.png)
# Ondorioak
Oso gustura gaude egindako proiektuarekin. Izan ere, eskatzen zitzaigun guztia bete dugu eskatutako denborarako, eta hori taldeko bi partaideak duale formakuntzan geundela, klase ordu gutxiago edukiz. Oraindik hobekuntza txikirenbat ere falta zaio, balioak estabilizatzeko potentziometroak jartzea adibidez. Baina hala eta guztiz ere, thereminak ematen digun soinua oso ona da, eduki ditugun arazoak kontuan harturik.

Edukitako lehen zailtasuna, ultrasoinu sentsoreak Teensy-ra konektatzerakoan baliorik ematen ez zuela zen, baina honen soluzioa erraza izan zen arazoa ultrasoinuaren elikaduran bait zegoen. 3V3 pinetik elikatzen genuen 5V pinetik beharrean, eta hori dela eta ez zuen funtzionatzen.

Sentsoreak banaka probatuta emaitza onak lortzen genituela ikusirik, binaka probatzea erabaki genuen. Ultrasoinu sentsoreekin erraz lortu genuen, baina bukaerako audioa oso eskalatuta ematen zuenez VL530X sentsoreekin probatu genuen. Kontuan harturik banaka emaitza hobea ematen zutela bonaka ere hobea eman behar zuten logikoki. Hala ere, hauen sistema ultrasoinu sentsoreena baino konplikatuagoa zenez, ez genuen lortzen, audio sistemak funtzionatzerik. Adafruit liburutegien eta Teensy-ren arteko konpabilitateaz oso zihur ez geundenez, Arduino Nano bat sartu genuen bien tartean, heuren arteko komunikazio arazorik ez izateko. Honek, sentsoreek emandako balioekin kalkuluak egiten zituen, eta behin kalkulu guztiak eginda Teensy-ra bidaltzen zituen, azken honi lana erraztuz.

Audioaren hobekuntza nabarmena zen ultrasoinu sentsoreekiko, baina hala eta guztiz ere, pixka bat eskalatuta ematen jarraitzen zuen oraindik irteerako audioak. Arazoa Arduino Nanoaren eta Teensy-aren konexioetan egon zitekeela pentxatu genuen, balioak parametro desberditenakoak izan zitezkeela Arduino Nanoaren irteeran eta Teensyaren sarreran, eta horretarako, bien arteko konexioan bi zirkuitu jarri genituen, bi zatiz osaturik. Lehen zatian Nanotik ateratako tentsioa estabilizatzen zen, eta bigarren zatian estabilizaturiko tentsio hori zatitu, Teensy-ren sarrerara Arduinotik ateratakoa baino tentsio estable eta txikiagoa lortuz.

PCB-a egiterakoan, Arduino Nanoko A4 eta A5 pin-ak erabili behar ziren VL530X sentsoreekin konektatzeko, eta arazoa izan zen nahastuta A3 eta A4 pin-etan egin zirela zuloak. Honen soluzioa “txapuzeroa” izan arren sinplea izan zen oso, izan ere, A3 pin-etik ateratzen zen pista moztu eta A5-etik pista horretara zubi bat egitea besterik ez zen.

Edukitako azken arazoa, etxean suertatu zen. Muntai guztia egin ondoren, etxean proba batzuk egiten funtzionatzeari utzi zion. Ez zen Arduino Nanoko seinalerik heltzen Teensy-ra eta hortaz ez zen inongo haudiorik entzuten. Hainbat froga egin ondoren, konturatu ginen potentziometroak jarri aurretik egin genituela ondo emandako froga guztiak, eta hortaz, arazoa potentziometroetan egongo zela zihurrenik. Hauek deskonektatu eta Arduino Nanoa eta Teensy-a berkonfiguratu ondoren proiektuak lehen bezala jarraitzen zuen, arazorik eman gabe. 
# Erreferentziak
https://www.pjrc.com/store/teensy40.html

https://hackaday.io/project/8292-microcontroller-audio-workshop-had-supercon-2015

https://www.arduino.cc/en/pmwiki.php?n=Main/ArduinoBoardNano

https://www.st.com/resource/en/datasheet/vl53l0x.pdf

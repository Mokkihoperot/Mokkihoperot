## Tilastokeskuksen Visualisointihackathon 15.9.2021 - 30.9.2021

Tämä on joukkue Mökkihöperöiden kilpailutyö Tilastokeskuksen Visualisointihackathonia varten. TIlastokeskuksen Visualisointihackathonin tehtävänä oli tehdä visualisointi pohjautuen TIlastokeskuksen kokeellisiin tilastoihin.

Joukkueemme visualisoi kokeellisista tilastoista vuokramökkitilaston lukuja ja täydensimme lukuja lisäämällä mukaan lukuja majoitustilastosta. Aikasarjoihin tehtiin aikasarjan tasoitus ja tiedot on visualisoitu viivakuvion avulla. Kilpailutyön alle on koottu vastaukset tuomariston esittämiin kysymyksiin ja myös käytetty koodi aineistoineen. 

### Selvitä alta miten vuokramökkiyöpymiset ja yöpymiset hotelleissa sekä muissa majoitusliikkeissä muuttuivat ennen koronakriisiä ja sen jälkeen ja miksi

Visualisoinnissa on esitetty aikasarjatasoitetut yöpymisluvut vuokramökkien sekä hotellien ja muiden majoitusliikkeiden osalta. Visualisoinnissa on mahdollista tarkastella yöpymisten määrän muutosta vuodesta 2018 vuoden 2021 huhtikuuhun. Vaihda visualisoitavaa matkailijaryhmää pudostusvalikosta ja kuljeta osoitinta pitkin kuviota ja saat lisätietoja kunkin ajanhetken tilanteesta.

<meta charset="utf-8">
<style>
body {
  background-color: #F1F3F3    
}
.axis {
	font: 12px sans-serif;
	
}

.axis path,
.axis line {
  fill: none;
  stroke: black;
  stroke-width: 1.5px;
  shape-rendering: crispEdges;
}

.line {
  fill: none;
  stroke: #0073B0;
  stroke-width: 5px;
}

.overlay {
  fill: none;
  pointer-events: all;
}

.focus circle {
  fill: #F1F3F3;
  stroke: #0073B0;
  stroke-width: 5px;
}
  
.hover-line {
  stroke: #0073B0;
  stroke-width: 2px;
  stroke-dasharray: 3,3;
}

div.tooltip {	
    position: absolute;			
    text-align: left;			
    width: 450px;					
    height: 480px;					
    padding: 15px;				
    font: 16px sans-serif;
    color: black;
    background: #ea7404;	
    border: 30 px;		
    border-radius: 30px;			
    pointer-events: none;			
}
	
.grid_line {
  stroke: #D4D8DA;
  stroke-opacity: 0.3;
  stroke-width: 0.4px;
  ;
}

</style>

<select id="selectButton"></select>
<script src="https://d3js.org/d3.v4.js"></script>

<div id="svg">
<script>
 //määritellään kuvion koko
var margin = {top: 10, right: 90, bottom: 70, left: 60},
    width = 700 - margin.left - margin.right,
    height = 700 - margin.top - margin.bottom;

 //määritellään aineisto   
 var data = [
{id: 'i1' , year:  '2018-01-31' , Kotimaiset_mokki: 160418.9, Ulkomaiset_mokki: 47536.07 , Kaikki_mokki: 206535.3 , Kotimaiset_majoitus: 1287633 , Ulkomaiset_majoitus: 553365.9 , Kaikki_majoitus: 1840836 , Kotimaiset_hotelli: 1001473 , Ulkomaiset_hotelli: 457678.1 , Kaikki_hotelli: 1455825, teksti:'<li>HS: Helsingin satama nousi Euroopan vilkkaimmaksi matkustaja­satamaksi</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 6,6 %.</li><li>Kotimaisten matkailijoiden yöpymiset olivat lähes viime vuoden tasolla. </li><li>HS: Kunnon talvi toi etelän hiihtokeskuksiin väkeä jopa ennätysmäärin – Pääsiäisestä veikataan nyt myös etelään vilkasta, eikä kausi lopu siihen</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/01/matk_2018_01_2018-03-08_tie_001_fi_001.gif', kuukausi:'Tammikuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 0,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 21,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 7,6 prosenttia.'},
{id: 'i2' , year:  '2018-02-28' , Kotimaiset_mokki: 159768.3, Ulkomaiset_mokki: 48892.7 , Kaikki_mokki: 206093.6 , Kotimaiset_majoitus: 1275745 , Ulkomaiset_majoitus: 557035.9 , Kaikki_majoitus: 1822661 , Kotimaiset_hotelli: 986020.7 , Ulkomaiset_hotelli: 459199.6 , Kaikki_hotelli: 1432630, teksti:'<li>HS: Matkailu kasvaa Helsingissä nyt paljon kovempaa kuin aina trendikkäissä Tukholmassa ja Kööpenhaminassa </li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 6,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/02/matk_2018_02_2018-04-12_tie_001_fi_001.gif', kuukausi:'Helmikuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 2,5 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 75,5 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 27,1 prosenttia.'},
{id: 'i3' , year:  '2018-03-31' , Kotimaiset_mokki: 180612.4, Ulkomaiset_mokki: 48591.92 , Kaikki_mokki: 212675.4 , Kotimaiset_majoitus: 1332794 , Ulkomaiset_majoitus: 563614.9 , Kaikki_majoitus: 1967698 , Kotimaiset_hotelli: 1060642 , Ulkomaiset_hotelli: 471964 , Kaikki_hotelli: 1468287, teksti:'<li>HS: Maailmalla on edessään turistiräjähdys, joka koettelee luonnon rajoja </li><li>Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 7,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,8 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/03/matk_2018_03_2018-05-09_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 4,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Espanja saapuneiden turistien määrä, 56,5 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 39,1 prosenttia.'},
{id: 'i4' , year:  '2018-04-30' , Kotimaiset_mokki: 223428.4, Ulkomaiset_mokki: 50172.47 , Kaikki_mokki: 198593.6 , Kotimaiset_majoitus: 1328933 , Ulkomaiset_majoitus: 568954.1 , Kaikki_majoitus: 1972526 , Kotimaiset_hotelli: 1072955 , Ulkomaiset_hotelli: 476471.4 , Kaikki_hotelli: 1504938, teksti:'<li>HS: Matkailu takoo nyt ennätyslukemia: aasialaisten yöpymiset Suomessa ylittivät miljoonan rajan</li><li>Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 4,2 %.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 1,2 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/04/matk_2018_04_2018-06-07_tie_001_fi_001.gif', kuukausi:'Huhtikuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,7 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 14,5 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 21,6 prosenttia.'},
{id: 'i5' , year:  '2018-05-31' , Kotimaiset_mokki: 191668.7, Ulkomaiset_mokki: 49664.87 , Kaikki_mokki: 210558.3 , Kotimaiset_majoitus: 1316079 , Ulkomaiset_majoitus: 557849.4 , Kaikki_majoitus: 1924369 , Kotimaiset_hotelli: 1044658 , Ulkomaiset_hotelli: 464587.3 , Kaikki_hotelli: 1486912, teksti:'<li>HS: Turistitulva kaipaisi kansainvälistä taakanjakoa siinä missä turvapaikanhakijoiden sijoittelukin</li><li>Ulkomaisten matkailijoiden yöpymisten määrä väheni 3,9 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,2 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/05/matk_2018_05_2018-07-06_tie_001_fi_001.gif', kuukausi:'Toukokuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 2,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Norja saapuneiden turistien määrä, 29,3 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 24,4 prosenttia.'},
{id: 'i6' , year:  '2018-06-30' , Kotimaiset_mokki: 110755.3, Ulkomaiset_mokki: 49470.74 , Kaikki_mokki: 200432.4 , Kotimaiset_majoitus: 1283119 , Ulkomaiset_majoitus: 573556.6 , Kaikki_majoitus: 1888354 , Kotimaiset_hotelli: 1007617 , Ulkomaiset_hotelli: 489440.1 , Kaikki_hotelli: 1460106, teksti:'<li>HS: Haittamaksu vähentäisi turhaa turistiliikennettä</li><li>Ulkomaisten matkailijoiden yöpymisten määrä väheni 4,8 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 4,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/06/matk_2018_06_2018-08-09_tie_001_fi_001.gif', kuukausi:'Kesäkuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 4,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Norja saapuneiden turistien määrä, 11,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 23,8 prosenttia.'},
{id: 'i7' , year:  '2018-07-31' , Kotimaiset_mokki: 118563.1, Ulkomaiset_mokki: 50309.05 , Kaikki_mokki: 198615.6 , Kotimaiset_majoitus: 1220485 , Ulkomaiset_majoitus: 585220.9 , Kaikki_majoitus: 1778818 , Kotimaiset_hotelli: 942092 , Ulkomaiset_hotelli: 500823.3 , Kaikki_hotelli: 1406870, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset vähenivät 2,2 %.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät prosentin.</li><li>HS: Trumpin ja Putinin tapaaminen Helsingissä nosti hotellien hinnat kattoon.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/07/matk_2018_07_2018-09-07_tie_001_fi_001.gif', kuukausi:'Heinäkuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Espanja saapuneiden turistien määrä, 12 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 11,9 prosenttia.'},
{id: 'i8' , year:  '2018-08-31' , Kotimaiset_mokki: 166623.6, Ulkomaiset_mokki: 50098.42 , Kaikki_mokki: 208593.6 , Kotimaiset_majoitus: 1260261 , Ulkomaiset_majoitus: 576107.4 , Kaikki_majoitus: 1811925 , Kotimaiset_hotelli: 985223.6 , Ulkomaiset_hotelli: 486391.3 , Kaikki_hotelli: 1502747, teksti:'<li>HS: Venetsialaiset laajenivat koko maan karnevaaliksi </li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät 2,6 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/08/matk_2018_08_2018-10-05_tie_001_fi_001.gif', kuukausi:'Elokuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 2,5 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, 12,9 prosenttia. Eniten väheni maasta Britannia tulleiden turistien määrä, 15,7 prosenttia.'},
{id: 'i9' , year:  '2018-09-30' , Kotimaiset_mokki: 146696.1, Ulkomaiset_mokki: 51321.94 , Kaikki_mokki: 208915.1 , Kotimaiset_majoitus: 1265878 , Ulkomaiset_majoitus: 565972.5 , Kaikki_majoitus: 1782481 , Kotimaiset_hotelli: 986353.9 , Ulkomaiset_hotelli: 476771.6 , Kaikki_hotelli: 1476797, teksti:'<li>HS: Kiinalaisia turisteja virtaa Lappiin vuosi vuodelta enemmän, mutta rajallinen majoituskapasiteetti hillitsee kasvua. </li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät 4,6 %.</li><li>Kotimaisten matkailijoiden yöpymiset säilyivät edellisvuoden tasolla. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/09/matk_2018_09_2018-11-08_tie_001_fi_001.gif', kuukausi:'Syyskuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 17,6 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 17 prosenttia.'},
{id: 'i10' , year:  '2018-10-31' , Kotimaiset_mokki: 149974.6, Ulkomaiset_mokki: 50025.57 , Kaikki_mokki: 207248.3 , Kotimaiset_majoitus: 1260322 , Ulkomaiset_majoitus: 563856.6 , Kaikki_majoitus: 1764041 , Kotimaiset_hotelli: 969510.4 , Ulkomaiset_hotelli: 465395.1 , Kaikki_hotelli: 1456138, teksti:'<li>HS: Rauman telakalle tähän asti suurin alustilaus Tallinkilta, aikoo palkata lisää työntekijöitä</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 6,0 %. </li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 1,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/10/matk_2018_10_2018-12-05_tie_001_fi_001.gif', kuukausi:'Lokakuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 1,6 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 35,3 prosenttia. Eniten väheni maasta Espanja tulleiden turistien määrä, 10,3 prosenttia.'},
{id: 'i11' , year:  '2018-11-30' , Kotimaiset_mokki: 177147.8, Ulkomaiset_mokki: 51673.85 , Kaikki_mokki: 212591.2 , Kotimaiset_majoitus: 1272496 , Ulkomaiset_majoitus: 570961.4 , Kaikki_majoitus: 1799344 , Kotimaiset_hotelli: 980278.2 , Ulkomaiset_hotelli: 473521.7 , Kaikki_hotelli: 1529248, teksti:'<li>HS: Brittilehdistö haukkuu lumettoman Suomen ”craplandiksi” ja marraskuisen Rovaniemen ”jingle helliksi”</li><li>Ulkomaisten matkailijoiden yöpymisten määrä säilyi vuoden 2017.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/11/matk_2018_11_2019-01-10_tie_001_fi_001.gif', kuukausi:'Marraskuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 33,3 prosenttia. Eniten väheni maasta Alankomaat tulleiden turistien määrä, 13,7 prosenttia.'},
{id: 'i12' , year:  '2018-12-31' , Kotimaiset_mokki: 163475.4, Ulkomaiset_mokki: 51709.09 , Kaikki_mokki: 215659 , Kotimaiset_majoitus: 1277142 , Ulkomaiset_majoitus: 578093.6 , Kaikki_majoitus: 1855253 , Kotimaiset_hotelli: 979531.8 , Ulkomaiset_hotelli: 481399.3 , Kaikki_hotelli: 1543477, teksti:'<li>HS: Brittilehti haukkui lumetonta Lappia, ja osa turisteista peruutti lomiaan – ”Tilanne on ollut kaoottinen”, sanoo yrittäjä</li><li>Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 6,9 %.</li><li>Kotimaisten matkailijoiden yöpymiset säilyivät edellisvuoden tasolla</li><li>Vuokramökkiyöpymisiä eniten maaliskuussa, näistä kotimaisia yöpymisiä oli 86 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/12/matk_2018_12_2019-02-07_tie_001_fi_001.gif', kuukausi:'Joulukuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 31,5 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 9,8 prosenttia.'},
{id: 'i13' , year:  '2019-01-31' , Kotimaiset_mokki: 170563.7, Ulkomaiset_mokki: 52062.89 , Kaikki_mokki: 215915.6 , Kotimaiset_majoitus: 1275081 , Ulkomaiset_majoitus: 561716.9 , Kaikki_majoitus: 1825999 , Kotimaiset_hotelli: 980264.6 , Ulkomaiset_hotelli: 461142.6 , Kaikki_hotelli: 1473875, teksti:'<li>HS: Helsinki on venäläisille kuin Tukholma suomalaisille: kallis mutta laadukas ja lähellä</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät vajaan prosentin.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 1,8 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/01/matk_2019_01_2019-03-01_tie_001_fi_001.gif', kuukausi:'Tammikuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 1,6 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 45,6 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 11 prosenttia.'},
{id: 'i14' , year:  '2019-02-28' , Kotimaiset_mokki: 171830, Ulkomaiset_mokki: 50462 , Kaikki_mokki: 215257.4 , Kotimaiset_majoitus: 1274252 , Ulkomaiset_majoitus: 564164.5 , Kaikki_majoitus: 1820058 , Kotimaiset_hotelli: 977323.5 , Ulkomaiset_hotelli: 461291.3 , Kaikki_hotelli: 1473581, teksti:'<li>HS: Matkailijoita kannustetaan sosiaalisessa mediassa ”vuokraamaan” suomalaisia</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 0,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/02/matk_2019_02_2019-03-29_tie_001_fi_001.gif', kuukausi:'Helmikuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 3,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Ranska saapuneiden turistien määrä, 16,4 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 23,3 prosenttia.'},
{id: 'i15' , year:  '2019-03-31' , Kotimaiset_mokki: 187816, Ulkomaiset_mokki: 56673.92 , Kaikki_mokki: 209195.3 , Kotimaiset_majoitus: 1392622 , Ulkomaiset_majoitus: 569644.9 , Kaikki_majoitus: 2021248 , Kotimaiset_hotelli: 1099210 , Ulkomaiset_hotelli: 467962.7 , Kaikki_hotelli: 1445087, teksti:'<li>HS: Näin hallitsematon brexit vaikuttaisi matkailijoihin – brittituristi joutuisi jonottamaan rajalla ja leimaamaan passinsa, jopa makkaran tuonti olisi kiellettyä</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät 0,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 1,4 %..</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/03/matk_2019_03_2019-04-26_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 21,8 prosenttia. Eniten väheni maasta Espanja tulleiden turistien määrä, 25,8 prosenttia.'},
{id: 'i16' , year:  '2019-04-30' , Kotimaiset_mokki: 205276.2, Ulkomaiset_mokki: 61561.46 , Kaikki_mokki: 224321.3 , Kotimaiset_majoitus: 1501451 , Ulkomaiset_majoitus: 573190.4 , Kaikki_majoitus: 2120289 , Kotimaiset_hotelli: 1186376 , Ulkomaiset_hotelli: 475896.6 , Kaikki_hotelli: 1532676, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 5,0 %. Tähän vaikutti osaltaan pääsiäinen, joka tänä vuonna ajoittui kokonaan huhtikuulle</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 2,8.</li><li>Yöpymisiä vuokramökeissä oli tammi-huhtikuussa 1 093 000. Näistä kotimaisia yöpymisiä oli 78 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/04/matk_2019_04_2019-05-29_tie_001_fi_001.gif', kuukausi:'Huhtikuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 4,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Japani saapuneiden turistien määrä, 49,1 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 16 prosenttia.'},
{id: 'i17' , year:  '2019-05-31' , Kotimaiset_mokki: 191162.1, Ulkomaiset_mokki: 63707.28 , Kaikki_mokki: 210119.4 , Kotimaiset_majoitus: 1439576 , Ulkomaiset_majoitus: 607307.5 , Kaikki_majoitus: 2094454 , Kotimaiset_hotelli: 1139725 , Ulkomaiset_hotelli: 508684.7 , Kaikki_hotelli: 1578474, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 12,3 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 4,2 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/05/matk_2019_05_2019-06-28_tie_001_fi_001.gif', kuukausi:'Toukokuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 4,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Venäjä saapuneiden turistien määrä, 73,8 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 7 prosenttia.'},
{id: 'i18' , year:  '2019-06-30' , Kotimaiset_mokki: 196037.3, Ulkomaiset_mokki: 58883.13 , Kaikki_mokki: 210634.5 , Kotimaiset_majoitus: 1403713 , Ulkomaiset_majoitus: 620523.5 , Kaikki_majoitus: 2057555 , Kotimaiset_hotelli: 1093237 , Ulkomaiset_hotelli: 511683.2 , Kaikki_hotelli: 1545131, teksti:'<li>HS: Ruotsi, Norja ja Tanska rökittävät Suomen kisassa turisteista: ”Emme osaa myydä Suomen kesää”</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 6,4 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 4,8 %</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/06/matk_2019_06_2019-07-26_tie_001_fi_001.gif', kuukausi:'Kesäkuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 7,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, 25 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 5 prosenttia.'},
{id: 'i19' , year:  '2019-07-31' , Kotimaiset_mokki: 143753.5, Ulkomaiset_mokki: 54079.8 , Kaikki_mokki: 212748.5 , Kotimaiset_majoitus: 1264837 , Ulkomaiset_majoitus: 629895.4 , Kaikki_majoitus: 1886380 , Kotimaiset_hotelli: 1066481 , Ulkomaiset_hotelli: 506800.8 , Kaikki_hotelli: 1574598, teksti:'<li>HS: Visit Finland valjasti maksettuja turisteja markkinointi­kampanjaansa, mutta sometuspakkoa Suomen-matkaan ei sisältynyt</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 7,0 %.</li><li>Ulkomaisten matkailijoiden yöpymiset pysyivät edelllisvuoden tasolla.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/07/matk_2019_07_2019-08-29_tie_001_fi_001.gif', kuukausi:'Heinäkuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 7,9 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 13,8 prosenttia. Eniten väheni maasta Britannia tulleiden turistien määrä, 18 prosenttia.'},
{id: 'i20' , year:  '2019-08-31' , Kotimaiset_mokki: 129825.7, Ulkomaiset_mokki: 50084.09 , Kaikki_mokki: 231148.5 , Kotimaiset_majoitus: 1245638 , Ulkomaiset_majoitus: 625926.9 , Kaikki_majoitus: 1854442 , Kotimaiset_hotelli: 1010104 , Ulkomaiset_hotelli: 517452 , Kaikki_hotelli: 1571492, teksti:'<li>HS: Helsingin keskustassa pyörii jatkuvasti turisteja, jotka eivät tiedä, missä maassa ovat</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,5 % viime vuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 4,0 %.</li><li>Yöpymisiä vuokramökeissä oli tammi-huhtikuussa 890 000. Näistä kotimaisia yöpymisiä oli 80 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/08/matk_2019_08_2019-09-26_tie_001_fi_001.gif', kuukausi:'Elokuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 3,7 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 19,8 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 2,8 prosenttia.'},
{id: 'i21' , year:  '2019-09-30' , Kotimaiset_mokki: 190946.6, Ulkomaiset_mokki: 49141.28 , Kaikki_mokki: 235246.6 , Kotimaiset_majoitus: 1244312 , Ulkomaiset_majoitus: 601957.7 , Kaikki_majoitus: 1796111 , Kotimaiset_hotelli: 988396.5 , Ulkomaiset_hotelli: 510867.2 , Kaikki_hotelli: 1536225, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 9,0 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/09/matk_2019_09_2019-10-31_tie_001_fi_001.gif', kuukausi:'Syyskuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 40,8 prosenttia. Eniten väheni maasta Ruotsi tulleiden turistien määrä, 2,8 prosenttia.'},
{id: 'i22' , year:  '2019-10-31' , Kotimaiset_mokki: 178551.9, Ulkomaiset_mokki: 46911.54 , Kaikki_mokki: 232159.8 , Kotimaiset_majoitus: 1245751 , Ulkomaiset_majoitus: 576653.7 , Kaikki_majoitus: 1778172 , Kotimaiset_hotelli: 986544.7 , Ulkomaiset_hotelli: 494961.8 , Kaikki_hotelli: 1558857, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 5,6 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 12,2 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/10/matk_2019_10_2019-11-28_tie_001_fi_001.gif', kuukausi:'Lokakuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 5,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Ranska saapuneiden turistien määrä, 46,4 prosenttia. Eniten väheni maasta Yhdysvallat tulleiden turistien määrä, 4,7 prosenttia.'},
{id: 'i23' , year:  '2019-11-30' , Kotimaiset_mokki: 154357.9, Ulkomaiset_mokki: 42137.24 , Kaikki_mokki: 239103.3 , Kotimaiset_majoitus: 1282198 , Ulkomaiset_majoitus: 562375.7 , Kaikki_majoitus: 1808948 , Kotimaiset_hotelli: 1004829 , Ulkomaiset_hotelli: 478089.6 , Kaikki_hotelli: 1601647, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 5,5 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 3,4 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/11/matk_2019_11_2020-01-03_tie_001_fi_001.gif', kuukausi:'Marraskuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 5,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 26,4 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 22,7 prosenttia.'},
{id: 'i24' , year:  '2019-12-31' , Kotimaiset_mokki: 165412.2, Ulkomaiset_mokki: 41987.47 , Kaikki_mokki: 236734.9 , Kotimaiset_majoitus: 1241354 , Ulkomaiset_majoitus: 557941.6 , Kaikki_majoitus: 1851926 , Kotimaiset_hotelli: 958584 , Ulkomaiset_hotelli: 469047.3 , Kaikki_hotelli: 1558284, teksti:'<li>HS: Helsingillä oli jälleen hyvä matkailuvuosi, mutta se jäi paljon jälkeen Tukholman ja Kööpenhaminan luvuista</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät  0,4 %</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 %</li><li>Yöpymisiä vuokramökeissä oli touko-elokuussa 688 000. Näistä kotimaisia yöpymisiä oli 78 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/12/matk_2019_12_2020-01-31_tie_001_fi_001.gif', kuukausi:'Joulukuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 2,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 25,1 prosenttia. Eniten väheni maasta Ruotsi tulleiden turistien määrä, 13,8 prosenttia.'},
{id: 'i25' , year:  '2020-01-31' , Kotimaiset_mokki: 189473.2, Ulkomaiset_mokki: 40038.93 , Kaikki_mokki: 261558.4 , Kotimaiset_majoitus: 1256693 , Ulkomaiset_majoitus: 554946.1 , Kaikki_majoitus: 1860629 , Kotimaiset_hotelli: 977116 , Ulkomaiset_hotelli: 470277.6 , Kaikki_hotelli: 1583406, teksti:'<li>HS: Suomi ei kiinnostanut brittejä viime vuoden tapaan, mutta venäläisten yöpymiset Suomessa lisääntyivät tammikuussa</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 10,4 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 5,3 %.</li><li>Kiinalaisella matkailijalla todettiin Lapissa käydessään vuoden 2020 tammikuun lopussa SARS-CoV-2-infektio</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/01/matk_2020_01_2020-02-27_tie_001_fi_001.gif', kuukausi:'Tammikuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 10,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 37,5 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 25,6 prosenttia.'},
{id: 'i26' , year:  '2020-02-29' , Kotimaiset_mokki: 176638.1, Ulkomaiset_mokki: 34341.23 , Kaikki_mokki: 266293 , Kotimaiset_majoitus: 1279388 , Ulkomaiset_majoitus: 522976.5 , Kaikki_majoitus: 1845713 , Kotimaiset_hotelli: 984917.7 , Ulkomaiset_hotelli: 441344.2 , Kaikki_hotelli: 1583938, teksti:'<li>HS: Koronavirus iski hotelleihin jo helmikuussa – kiinalaisturistien määrä romahti kesken sesongin</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 10,1 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 1,9 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/02/matk_2020_02_2020-03-26_tie_001_fi_001.gif', kuukausi:'Helmikuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 10,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 37,5 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 70,4 prosenttia.'},
{id: 'i27' , year:  '2020-03-31' , Kotimaiset_mokki: 87382.92, Ulkomaiset_mokki: 24174.76 , Kaikki_mokki: 64615.71 , Kotimaiset_majoitus: 1001947 , Ulkomaiset_majoitus: 230253.3 , Kaikki_majoitus: 1291412 , Kotimaiset_hotelli: 689824.5 , Ulkomaiset_hotelli: 195115.3 , Kaikki_hotelli: 692579.4, teksti:'<li>Koronavirus alkaa vaikuttaa maailmaanlaajuisesti matkailuun</li><li>Hallitus julisti voimaan valmiuslain 17. maaliskuuta.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 44,3 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 53,0 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/03/matk_2020_03_2020-04-29_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 43,7 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 81,5 prosenttia.'},
{id: 'i28' , year:  '2020-04-30' , Kotimaiset_mokki: 78552.07, Ulkomaiset_mokki: 17655.5 , Kaikki_mokki: 34261.4 , Kotimaiset_majoitus: 784004.6 , Ulkomaiset_majoitus: 202775.4 , Kaikki_majoitus: 935066.8 , Kotimaiset_hotelli: 471103.5 , Ulkomaiset_hotelli: 164653.5 , Kaikki_hotelli: 339715.3, teksti:'<li>Ravintolat päätettiin sulkea 4. huhtikuuta alkaen.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 86,4 %.  </li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 95,2 % ja ulkomaalaisille yöpymisiä tilastoitiin 0,02 miljoonaa.</li><li>Tammi-huhtikuun yöpymisten kokonaismäärä vuokramökeissä laski 27 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/04/matk_2020_04_2020-05-28_tie_001_fi_001.gif', kuukausi:'Huhtikuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 85,8 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 99,1 prosenttia.'},
{id: 'i29' , year:  '2020-05-31' , Kotimaiset_mokki: 178791.9, Ulkomaiset_mokki: 14359.42 , Kaikki_mokki: 197356.3 , Kotimaiset_majoitus: 857150.8 , Ulkomaiset_majoitus: 165105.6 , Kaikki_majoitus: 981397.1 , Kotimaiset_hotelli: 476857.9 , Ulkomaiset_hotelli: 123767.3 , Kaikki_hotelli: 426416.1, teksti:'<li>Rajojen ylitystä helpotettiin sallimalla työmatkat, mutta vapaa-ajan matkustamista ei suositeltu</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 74,4 %.</li><li> Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 95,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/05/matk_2020_05_2020-06-25_tie_001_fi_001.gif', kuukausi:'Toukokuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 74,7 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 98,8 prosenttia.'},
{id: 'i30' , year:  '2020-06-30' , Kotimaiset_mokki: 257735.7, Ulkomaiset_mokki: 12803.19 , Kaikki_mokki: 248268.9 , Kotimaiset_majoitus: 887714.6 , Ulkomaiset_majoitus: 129190.6 , Kaikki_majoitus: 993442.8 , Kotimaiset_hotelli: 542937.2 , Ulkomaiset_hotelli: 74966.52 , Kaikki_hotelli: 503203.2, teksti:'<li>Kotimaan matkailu sallittiin ja valmiuslain käytöstä luovuttiin</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 37,7 %. </li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 92,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/06/matk_2020_06_2020-07-30_tie_001_fi_001.gif', kuukausi:'Kesäkuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 36,1 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,9 prosenttia.'},
{id: 'i31' , year:  '2020-07-31' , Kotimaiset_mokki: 305116.4, Ulkomaiset_mokki: 11087.16 , Kaikki_mokki: 280641.5 , Kotimaiset_majoitus: 1008206 , Ulkomaiset_majoitus: 87732.4 , Kaikki_majoitus: 1105030 , Kotimaiset_hotelli: 913618 , Ulkomaiset_hotelli: 46539.75 , Kaikki_hotelli: 980095.4, teksti:'<li>Matkustaminen sallittiin määrätyistä Schengen-maista</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 2,2 %.</li><li>Pääkaupunkiseudulla kotimaiset yöpymiset vähenivät 41,2 %, mutta pääkaupunkiseudun ulkopuolella ne lisääntyivät 4,3 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 82,3 %</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/07/matk_2020_07_2020-08-27_tie_001_fi_001.gif', kuukausi:'Heinäkuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 1,6 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,2 prosenttia.'},
{id: 'i32' , year:  '2020-08-31' , Kotimaiset_mokki: 273887.8, Ulkomaiset_mokki: 9580.4 , Kaikki_mokki: 279019.9 , Kotimaiset_majoitus: 964583.7 , Ulkomaiset_majoitus: 71554.6 , Kaikki_majoitus: 1031394 , Kotimaiset_hotelli: 823893.7 , Ulkomaiset_hotelli: 27417.93 , Kaikki_hotelli: 903962.9, teksti:'<li>THL jakoi 26. elokuuta maat kolmeen ryhmään: vihreä, keltainen ja punainen.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 6,9 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät ainoastaan 0,6 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 81,6 %.</li><li> Vuokramökkiyöpymiset kasvoivat 25 %. Näistä kotimaisia yöpymisiä oli 95 %.  </li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/08/matk_2020_08_2020-09-24_tie_001_fi_001.gif', kuukausi:'Elokuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 6,3 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,6 prosenttia.'},
{id: 'i33' , year:  '2020-09-30' , Kotimaiset_mokki: 235597.5, Ulkomaiset_mokki: 7483.857 , Kaikki_mokki: 297346.3 , Kotimaiset_majoitus: 966808.6 , Ulkomaiset_majoitus: 190860.1 , Kaikki_majoitus: 1102774 , Kotimaiset_hotelli: 769409 , Ulkomaiset_hotelli: 158495.3 , Kaikki_hotelli: 981818.3, teksti:'<li>Hallitus salli 19. syyskuuta alkaen vapaan maahantulon niistä maista, joissa on kahden viikon aikana alle 25 tartuntaa 100 000 asukasta kohti</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 12,3 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 83,1 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/09/matk_2020_09_2020-10-28_tie_001_fi_001.gif', kuukausi:'Syyskuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 12,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 97,8 prosenttia.'},
{id: 'i34' , year:  '2020-10-31' , Kotimaiset_mokki: 247616.1, Ulkomaiset_mokki: 6222.034 , Kaikki_mokki: 322167.7 , Kotimaiset_majoitus: 981987.3 , Ulkomaiset_majoitus: 188308.3 , Kaikki_majoitus: 1125902 , Kotimaiset_hotelli: 732998.9 , Ulkomaiset_hotelli: 160921.5 , Kaikki_hotelli: 996483, teksti:'<li>Ravitsemisliikkeiden aukiolo- ja anniskeluaikoja rajoitettiin uudelleen koronavirusepidemiatilanteen johdosta.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 14,8 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 81,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/10/matk_2020_10_2020-11-26_tie_001_fi_001.gif', kuukausi:'Lokakuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 15,3 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 96,9 prosenttia.'},
{id: 'i35' , year:  '2020-11-30' , Kotimaiset_mokki: 247538.6, Ulkomaiset_mokki: 5111.616 , Kaikki_mokki: 228107 , Kotimaiset_majoitus: 948974.8 , Ulkomaiset_majoitus: 163497.3 , Kaikki_majoitus: 1049713 , Kotimaiset_hotelli: 663233.5 , Ulkomaiset_hotelli: 143834.9 , Kaikki_hotelli: 897222.1, teksti:'<li>Hallitus ilmoitti jatkavansa aikaisemmilla maahantulon rajoituksilla 13. joulukuuta asti.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 34,3 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,4 %, kun pääkaupunkiseudulla laskua kertyi 59,1 %.</li><li> Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 80,9 %.</li>', kuvio:'https://www.stat.fi/til/matk/2020/11/matk_2020_11_2020-12-23_tie_001_fi_001.gif', kuukausi:'Marraskuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 34,2 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 97,1 prosenttia.'},
{id: 'i36' , year:  '2020-12-31' , Kotimaiset_mokki: 253057.4, Ulkomaiset_mokki: 3850.003 , Kaikki_mokki: 244636.5 , Kotimaiset_majoitus: 1005327 , Ulkomaiset_majoitus: -164207 , Kaikki_majoitus: 943858.1 , Kotimaiset_hotelli: 680007 , Ulkomaiset_hotelli: -154012 , Kaikki_hotelli: 632842.2, teksti:'<li>Suomi keskeytti lennot Britanniaan, koska siellä oli alkanut ihmisiä tartuttaa uusi, nopeasti leviävä koronaviruksen muoto.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 29,4 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 21,3 %, kun pääkaupunkiseudulla laskua kertyi 57,5 % edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 91,8 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/12/matk_2020_12_2021-01-28_tie_001_fi_001.gif', kuukausi:'Joulukuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 29 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,2 prosenttia.'},
{id: 'i37' , year:  '2021-01-31' , Kotimaiset_mokki: 221476.1, Ulkomaiset_mokki: 2922.776 , Kaikki_mokki: 235947.9 , Kotimaiset_majoitus: 1031430 , Ulkomaiset_majoitus: -102114 , Kaikki_majoitus: 993676 , Kotimaiset_hotelli: 671258.3 , Ulkomaiset_hotelli: -87046.7 , Kaikki_hotelli: 682000.7, teksti:'<li>Hallitus päätti matkustusrajoituksista koronavirusmuunnosten leviämisen estämiseksi.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 34,3 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,7 %, kun pääkaupunkiseudulla laskua kertyi 60,4 % edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 92,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/01/matk_2021_01_2021-02-25_tie_001_fi_001.gif', kuukausi:'Tammikuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 34 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 99 prosenttia.'},
{id: 'i38' , year:  '2021-02-28' , Kotimaiset_mokki: 237563.2, Ulkomaiset_mokki: 2531.991 , Kaikki_mokki: 249064.5 , Kotimaiset_majoitus: 1048859 , Ulkomaiset_majoitus: -50832.7 , Kaikki_majoitus: 1027441 , Kotimaiset_hotelli: 660931.4 , Ulkomaiset_hotelli: -32685.2 , Kaikki_hotelli: 727142.4, teksti:'<li>Yli kuuden hengen kokoontumiset kiellettiin käytännössä koko maassa</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 32,1 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,4 %, kun pääkaupunkiseudulla laskua kertyi 58,5 % edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 90,3 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/02/matk_2021_02_2021-03-25_tie_001_fi_001.gif', kuukausi:'Helmikuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 32 prosenttia. Eniten väheni maasta Sveitsi tulleiden turistien määrä, 96,5 prosenttia.'},
{id: 'i39' , year:  '2021-03-31' , Kotimaiset_mokki: 293855.2, Ulkomaiset_mokki: 2351.775 , Kaikki_mokki: 261394.2 , Kotimaiset_majoitus: 1182833 , Ulkomaiset_majoitus: -23512.4 , Kaikki_majoitus: 1233390 , Kotimaiset_hotelli: 743123.3 , Ulkomaiset_hotelli: 347.5986 , Kaikki_hotelli: 527470.6, teksti:'<li>Euroopan komissio ilmoitti suunnitelmista ottaa käyttöön "koronapassin" eli todistuksen koronarokotuksen saamisesta.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 11,7 prosenttia</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset lisääntyivät 21,5 prosenttia, kun pääkaupunkiseudulla laskua kertyi 37,5 prosenttia edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 82,1</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/03/matk_2021_03_2021-04-29_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 11,5 prosenttia. Eniten väheni maasta Sveitsi tulleiden turistien määrä, 93,8 prosenttia.'},
{id: 'i40' , year:  '2021-04-30' , Kotimaiset_mokki: 246275.9, Ulkomaiset_mokki: 15546.27 , Kaikki_mokki: 259244 , Kotimaiset_majoitus: 1332308 , Ulkomaiset_majoitus: 201890.8 , Kaikki_majoitus: 1486882 , Kotimaiset_hotelli: 849560.5 , Ulkomaiset_hotelli: 189222.5 , Kaikki_hotelli: 778278.7, teksti:'<li>Hallitus kumosi 27. huhtikuuta poikkeusolot</li><li>Vuoden 2019 huhtikuun kotimaisten yöpymisten tasoon verrattaessa oltiin huhtikuussa 41 % alemmalla tasolla.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät vuoden 2019 huhtikuusta 85 %.</li><li>Vuokramökkiyöpymiset kasvoivat 42 %</li><li>Kotimaiset yöpymiset nousivat 87 % ja ulkomaiset yöpymiset laskivat 95 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/04/matk_2021_04_2021-05-27_tie_001_fi_003.gif', kuukausi:'Huhtikuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset kasvoivat 319 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Sveitsi saapuneiden turistien määrä, 439,8 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 28,9 prosenttia.'}
        ];

//muokataan aineiston päivämäärä stringit päivämäärämuotoisiksi
data.forEach( d => {
  d.Kotimaiset_mokki=+d.Kotimaiset_mokki;	
  d.Ulkomaiset_mokki=+d.Ulkomaiset_mokki;
  //d.Kaikki_mokki=+d.Kaikki_mokki;	
  d.Kotimaiset_majoitus=+d.Kotimaiset_majoitus;
  d.Ulkomaiset_majoitus=+d.Ulkomaiset_majoitus;	
  //d.Kaikki_majoitus=+d.Kaikki_majoitus;
  d.Kotimaiset_hotelli=+d.Kotimaiset_hotelli;	
  d.Ulkomaiset_hotelli=+d.Ulkomaiset_hotelli;
 // d.Kaikki_hotelli=+d.Kaikki_hotelli;	
  d.year= new Date(d.year);
  d.teksti=d.teksti;
  d.kuukausi=d.kuukausi;
  d.kuvio=d.kuvio;
})
var dataFilter = data.map(function(d){return {time: d.year, value: d.Kotimaiset_majoitus, teksti:d.teksti, kuukausi:d.kuukausi, kuvio:d.kuvio, altteksti:d.altteksti} });
//määritellään svg-kuvio, parametreinä yllä kerrotut "mitat" ja nimenä html-tiedostossa määritelty "svg"
var svg = d3.select("#svg")
  .append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform",
          "translate(" + margin.left + "," + margin.top + ")")

          // Lista ryhmistä update-funktiota varten
    var allGroup = ["Kotimaiset_majoitus", "Ulkomaiset_majoitus","Kotimaiset_mokki", "Ulkomaiset_mokki","Kotimaiset_hotelli", "Ulkomaiset_hotelli"]

//määritellään html:ssä määritellyn nimen mukaisen nappulan sisältö
  d3.select("#selectButton")
  .selectAll('myOptions')
  .data(allGroup)
  .enter()
  .append('option')
  .text(function (d) { return d; }) // text showed in the menu
  .attr("value", function (d) { return d; }) // corresponding value returned by the button

//jaotus x-akselia varten
var bisectDate = d3.bisector(function(d) { return d.time; }).left;
//määritellään x-akselin pituus
var x = d3.scaleTime().range([0, width]);
//määritellään y-akselin korkeus
var y = d3.scaleLinear().range([height, 0]);
//määritellään viiva
var line = d3.line()
    .x(function(d) { return x(d.time); })
    .y(function(d) { return y(d.value); });
	
// gridlines in x axis function
function make_x_gridlines() {		
    return d3.axisBottom(x)
        .ticks(3)
}

// gridlines in y axis function
function make_y_gridlines() {		
    return d3.axisLeft(y)
        .ticks(8)
}


// Define the div for the tooltip
var div = d3.select("body").append("div")	
    .attr("class", "tooltip")				
    .style("opacity", 0);

// yritetään määritellä html-tooltip


//määritellään marginaalit ja lisätään ne kuvioon?
var g = svg.append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

//määritellään x ja y -akseleiden maksimi ja minimiarvot
    x.domain(d3.extent(dataFilter, function(d) { return d.time; }));
    y.domain([d3.min(data, function(d) { return d.Ulkomaiset_majoitus-20000; }) / 1.005, d3.max(dataFilter, function(d) { return d.value; }) * 1.005]);


//lisätään x-akseli kuvioon
    g.append("g")
        .attr("class", "axis axis--x")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .append("text")
        .attr("class", "axis-title")
        
        .attr("y", 40)
        .attr("x", 280)
        .attr("dy", ".71em")
        .style("text-anchor", "end")
        //.attr("fill", "#5D6971")
	
	.attr("fill", "black")
        .text("Aika")
        .style("font-size","25px");
	


//lisätään y-akseli kuvioon ja lisätään sinne ominaisuuksia ja tekstiä
    g.append("g")
        .attr("class", "axis axis--y")
        
	 .call(d3.axisLeft(y).ticks(6).tickFormat(function(d) { if ((d) > 1000000) {
 	 
	 return parseInt(d / 1000000)+" "+ parseInt((d / 1000)-1000) + " 000";
	 }
	 else if ((d) === 1000000) {
 	 
	 return "1 000 000";
	 }
	 
	 else if ((d) < 1000000 && (d) > 0) {
 	 
	 return parseInt(d / 1000) + " 000";
	 } else {
 	 return "0";
	 }}
	 ))
	
        .append("text")
        .attr("class", "axis-title")
        .attr("transform", "rotate(-90)")
        //.attr("y", -90)
	.attr("y", -105)
        .attr("x", -200)
        .attr("dy", ".71em")
        .style("text-anchor", "end")
	//.attr("fill", "#5D6971")
        .attr("fill", "black")
        .text("Yöpymisten määrä")
        .style("font-size","25px");
	
// Lisätään x-akselin hilaviivat
  g.append("g")			
    .attr("class", "grid_line")
  .attr("transform", "translate(0," + height + ")")
  .call(make_x_gridlines()
    .tickSize(-height)
   .tickFormat("")
   )

// Lisätään y-akselin hilaviivat
	g.append("g")			
      .attr("class", "grid_line")
      .call(make_y_gridlines()
      .tickSize(-width)
      .tickFormat("")
      )
//viiva
      g.append("svg:line")
      .attr("class", "today")
      .attr("x1", (352))
      .attr("y1", 618)
      .attr("x2", (352))
      .attr("y2", 0)
      .attr("stroke", "black")
	//lisätty
      .attr("stroke-dasharray", "4")
      .style("stroke-width", 1.5);
//lisätään punainen laatikko	
g.append("svg:rect")
	.attr("class", "laatikko")
      .attr("x1", (900))
      .attr("y1", 618)
      .attr("x2", (352))
      .attr("y2", 0)
    .attr("fill", "red")
    .attr("opacity", 0.15);
	
	
//lisätään viiva kuvioon
    g.append("path")
        .datum(dataFilter)
        .attr("class", "line")
        .attr("d", line);
	


//määritellään viivaa seuraava ja hoveroiva tooltippi
    var focus = g.append("g")
        .attr("class", "focus")
        .style("display", "none");


    focus.append("line")
        .attr("class", "x-hover-line hover-line")
        .attr("y1", 0)
        .attr("y2", height);

    focus.append("line")
        .attr("class", "y-hover-line hover-line")
        .attr("x1", width)
        .attr("x2", width);

    focus.append("circle")
        .attr("r", 7.5);

    //focus.append("text")
        //.attr("x", 15)
      	//.attr("dy", ".31em");
//lisätään neliö jossa tooltippi liikkuu
    svg.append("rect")
        
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
        .attr("class", "overlay")
        .attr("width", width)
        .attr("height", height)

    svg.selectAll("rect")
        .data(dataFilter)
        .on("mouseover.pallo", function() { focus.style("display", null); })
   
        .on("mouseout.pallo", function() { focus.style("display", "none"); })
        .on("mouseout.teksti", function() {		
          d3.select(this).transition()
               .duration(1)
               .attr('opacity', '1');
          div.transition()
               .duration(1)
               .style("opacity", 0);
      })
        .on("mousemove.pallo", mousemove)
        .on("mousemove.teksti", function () {
          // recover coordinate we need

          d3.select(this).transition()
          
          .attr('opacity', '.85');
        div.transition()
        
          .duration(1)
          .style("opacity", 1);
          var x0 = x.invert(d3.mouse(this)[0]);
          var i = bisectDate(dataFilter, x0, 1);
          selectedData = dataFilter[i]
          focus
            .attr("cx", x(selectedData.x))  
            .attr("cy", y(selectedData.y))
            div
            .html(selectedData.kuukausi + "<br> Yöpymisiä:" + Number(Math.round(selectedData.value)).toLocaleString()+  "<br>" + selectedData.teksti +"<br> <img src='"+selectedData.kuvio+"' alt='"+selectedData.altteksti+"' width='407' height='250'> Kuvion lähde: Majoitustilasto, Tilastokeskus") 
            .style("left", (d3.event.pageX) + "px")		
            .style("top", (d3.event.pageY - 28) + "px");	
         });
//lisätään funktio joka seuraa hiirtä ja aktivoi alla olevan tooltipin
    function mousemove() {
      var x0 = x.invert(d3.mouse(this)[0]),
          i = bisectDate(dataFilter, x0, 1),
          d0 = dataFilter[i - 1],
          d1 = dataFilter[i],
          d = x0 - d0.time > d1.time - x0 ? d1 : d0;
      focus.attr("transform", "translate(" + x(d.time) + "," + y(d.value) + ")");
      //focus.select("html").html()  
      //focus.select("text").text(function() { return d.value; });
      //focus.select(function(){return tooltip2.html("top", (event.pageY-100)+"px").style("left",(event.pageX-100)+"px")});
      //console.log(focus.select("html").html());
	  
      focus.select(".x-hover-line").attr("y2", height - y(d.value));
      focus.select(".y-hover-line").attr("x2", width + width);
    }


    //päivitysfunktio
    function update(selectedGroup) {

        // Muodostaan uusi aineisto
        dataFilter = data.map(function(d){return {time: d.year, value:d[selectedGroup], teksti:d.teksti, kuukausi:d.kuukausi, kuvio:d.kuvio, altteksti:d.altteksti} });

        console.log(dataFilter);
        // määritellään päivitettävä objekti
        //  var u = svg.selectAll("line")
        //.data([dataFilter])
    
      // Updata the line
      g.selectAll(".line")

        .datum(dataFilter)
  
            .transition()


            .duration(1000)

       
            .attr("d", d3.line()
            .x(function(d) { return x(+d.time) })
            .y(function(d) { return y(+d.value) })
            )

           


      g.selectAll(".rect")
      mousemove()
      }

  
      // When the button is changed, run the updateChart function
      d3.select("#selectButton").on("change", function(d) {
          // recover the option that has been chosen
          var selectedOption = d3.select(this).property("value")
          // run the updateChart function with this selected option
          update(selectedOption)
       

      })
   
 </script> 
</div>

### Visualisoinnin sisältö

Visualisoinnissa on esitetty viivakuviolla tasoitettuna aikasarjana yöpymisten määrän kehitys tammikuusta 2018 huhtikuuhun 2021. Tälle aikajaksolle osui yksi viime aikojen suurimmista globaaleista kriiseistä, joka myös näkyy visualisoinnissa. Varsinkin ulkomaalaisten matkailijoiden yöpymiset ovat pudonneet erittäin merkittävästi. 

Kriisin jälkeen on ollut nähtävissä toipumista kesän 2020 aikana, varsinkin kotimaisten matkailijoiden keskuudessa, koska rajat ovat olleet suljettuina. Alhaiset matkailijaluvut näkyvät myös vuoden 2021 luvuissa suurina kasvuprosentteina, kun vertailuvuosien luvut olivat niin alhaisia.

Visualisoinnissa käyttäjällä on mahdollisuus valita pudotusvalikosta haluttu matkailijaryhmä ja saada tarkempaa tietoa eri maiden matkailijaryhmien yöpymisen kehittymisestä.
Käyttäjälle on lisäksi tarjolla lisätietoja sekä tekstimuodossa että majoitustilaston verkkosivulta suoraan linkitetyn kuvan muodossa. Tiedot perustuvat vuokramökkitilaston ja majoitustilaston kuukausittaisiin kysyntätietoihin. Ajankohtaa kuvaavia lisätietoja on kerätty tilastojulkaisuista, Helsingin sanomista ja Wikipediasta.
	
### Kausitasoitus
	
Kausitasoituksesta: käytettiin Tramo/Seats -menetelmää ja aikarajotteiden takia ei alettu estimoimaan parametreja käsin vaan annettiin r-funktion määrittää fiksuimmat parametrit. Perinteisesti ajatellaan että aikasarja koostuu kolmesta komponentista: kausivaihtelu, trendi ja satunnaisvaihtelu. Kun parametrit estimoidaan automaattisesti, malli pyrkii minimoimaan satunnaisvaihtelun määrää. Dataa annettiin mallille vain 2018 eteenpäin ja suuri osa havainnosta on tullut poikkeusaikana. Ulkomaisten yöpymisten määrä on ollut poikkeuksellisen alhaista ja kun niistä vähennetään mallin arvioimat kausitasoitus- ja trendikomponentit, niin tiputaan nollan alapuolelle. Jos tätä kehitellään pidemmälle, niin sitten mallia voitaisiin parannella niin ettei näin kävisi.
	
### Käytetyt välineet
	
Visualisointityön toteutuksessa on käytetty D3js-javascript-kirjastoa. Aikasarjan tasoitus on tehty R-ohjelmistolla. Kilpailutyö julkaistiin Github-sivustolla. Valitsimme nämä välineet, koska ne ovat avoimia ja niillä saa näyttäviä visualisointeja. Avoimen lähdekoodin visualisoinnit voivat myös toimia matalan kynnyksen moderneina ja ilmaisina työkaluina edistäen osaltaan Tilastokeskuksen tietojen käyttöä teknisten rajapintojen kautta.  Github-sivustoa käytimme siksi että sinne on helppo lisätä Javascriptillä tehdyt kuviot ja tehdä helposti kotisivut sisäänrakennetun kotisivueditorin avulla. Visualisointiin on myös helppo lisätä lisäinformaatiota suoraan muihin verkkosivuihin linkittämällä tai niistä graafeja poimimalla.
	
### Saavutettavuus

Visualisoinnin pääsisältö on kuvattu visualisoinnin alle. Kuvioissa on alt-tekstit ja visualisointi on avointa lähdekoodia ja kaikkia elementtejä on mahdollista muokata saavutettavaksi saavutettavuusasiantuntijoiden avustuksella. HTML-koodi on lähtökohtaisesti helposti käytettävissä saavutettavuusohjelmistoilla, kuten ruudunlukijoilla. Visualisoinnin värit on otettu Tilastokeskuksen graafisesta ohjeesta, joten värien osalta saavutettavuus on kunnossa.

Kuvioiden "Alt-tekstit" on tuotettu PX-taulujen pohjalta automaattisesti excelissä ja ne ovat muotoa: 
* Kotimaisten matkailijoiden yöpymiset kasvoivat 10,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 37,5 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 70,4 prosenttia.

### Mökkihöperöt-joukkue ja työskentely

Joukkue kokoontui säännöllisesti hackathonin aikana ja kokoontumisissa edistettiin visualisointityötä. Työtä tehtiin myös iltaisin ja viikonloppuisin. Työ alkoi ideoimalla Excelissä ja käytiin läpi mitä aineistolla voi tehdä. Lopullinen visualisointi tuotettiin, kun visio lopputuloksesta oli hahmottunut ja toteutuskelpoisuus varmistunut.

Joukkue koostui Yhteiskuntatilastojen Liikenne ja matkailu -ryhmän henkilöistä ja siihen kuuluivat:

* Ville Keränen
* Matti Kokkonen
* Heidi Pukkila
* Sofia Pitkänen
	
### Toistettavuus

Visualisointi on kenen tahansa toistettavissa. Käytetyt aineisto ovat noudettavissa Tilastokeskuksen tietokannoista ja koodi löytyy alta. Visualisointi on toteutettu avoimen lähdekoodin välineillä. Visualisointi on yleistä verkkosivuilla käytettävää koodia, joten visualisointi on käytettävissä kaikilla selaimilla ja myös mobiilissa.

### Koodi

#### Visualisoinnin HTML- ja Javascript-koodi

Voit kopioda tämän koodin, liittää sen tekstieditoriin ja tallentaa html-muodossa. Tämän jälkeen pelkän visualisoinnin avaaminen on mahdollista selaimessa omalta levyltä ja sitä voi muokata.
	
```
<meta charset="utf-8">
<style>
body {
  background-color: #F1F3F3    
}
.axis {
	font: 10px sans-serif;
}

.axis path,
.axis line {
  fill: none;
  stroke: #D4D8DA;
  stroke-width: 2px;
  shape-rendering: crispEdges;
}

.line {
  fill: none;
  stroke: #0073B0;
  stroke-width: 5px;
}

.overlay {
  fill: none;
  pointer-events: all;
}

.focus circle {
  fill: #F1F3F3;
  stroke: #0073B0;
  stroke-width: 5px;
}
  
.hover-line {
  stroke: #0073B0;
  stroke-width: 2px;
  stroke-dasharray: 3,3;
}

div.tooltip {	
    position: absolute;			
    text-align: left;			
    width: 410px;					
    height: 430px;					
    padding: 2px;				
    font: 14px sans-serif;
    color: black;
    background: #ea7404;	
    border: 0px;		
    border-radius: 8px;			
    pointer-events: none;			
}

</style>

<select id="selectButton"></select>
<script src="https://d3js.org/d3.v4.js"></script>

<div id="svg">
<script>
 //määritellään kuvion koko
var margin = {top: 10, right: 90, bottom: 70, left: 60},
    width = 700 - margin.left - margin.right,
    height = 700 - margin.top - margin.bottom;

 //määritellään aineisto   
 var data = [
{id: 'i1' , year:  '2018-01-31' , Kotimaiset_mokki: 160418.9, Ulkomaiset_mokki: 47536.07 , Kaikki_mokki: 206535.3 , Kotimaiset_majoitus: 1287633 , Ulkomaiset_majoitus: 553365.9 , Kaikki_majoitus: 1840836 , Kotimaiset_hotelli: 1001473 , Ulkomaiset_hotelli: 457678.1 , Kaikki_hotelli: 1455825, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 6,6 %.</li><li>Kotimaisten matkailijoiden yöpymiset olivat lähes viime vuoden tasolla. </li><li>HS: Kunnon talvi toi etelän hiihtokeskuksiin väkeä jopa ennätysmäärin – Pääsiäisestä veikataan nyt myös etelään vilkasta, eikä kausi lopu siihen</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/01/matk_2018_01_2018-03-08_tie_001_fi_001.gif', kuukausi:'Tammikuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 21,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 7,6 prosenttia.'},
{id: 'i2' , year:  '2018-02-28' , Kotimaiset_mokki: 159768.3, Ulkomaiset_mokki: 48892.7 , Kaikki_mokki: 206093.6 , Kotimaiset_majoitus: 1275745 , Ulkomaiset_majoitus: 557035.9 , Kaikki_majoitus: 1822661 , Kotimaiset_hotelli: 986020.7 , Ulkomaiset_hotelli: 459199.6 , Kaikki_hotelli: 1432630, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 6,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/02/matk_2018_02_2018-04-12_tie_001_fi_001.gif', kuukausi:'Helmikuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2,5 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 75,5 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 27,1 prosenttia.'},
{id: 'i3' , year:  '2018-03-31' , Kotimaiset_mokki: 180612.4, Ulkomaiset_mokki: 48591.92 , Kaikki_mokki: 212675.4 , Kotimaiset_majoitus: 1332794 , Ulkomaiset_majoitus: 563614.9 , Kaikki_majoitus: 1967698 , Kotimaiset_hotelli: 1060642 , Ulkomaiset_hotelli: 471964 , Kaikki_hotelli: 1468287, teksti:'<li>Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 7,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,8 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/03/matk_2018_03_2018-05-09_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 4,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Espanja saapuneiden turistien määrä, 56,5 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 39,1 prosenttia.'},
{id: 'i4' , year:  '2018-04-30' , Kotimaiset_mokki: 223428.4, Ulkomaiset_mokki: 50172.47 , Kaikki_mokki: 198593.6 , Kotimaiset_majoitus: 1328933 , Ulkomaiset_majoitus: 568954.1 , Kaikki_majoitus: 1972526 , Kotimaiset_hotelli: 1072955 , Ulkomaiset_hotelli: 476471.4 , Kaikki_hotelli: 1504938, teksti:'<li>Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 4,2 %.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 1,2 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/04/matk_2018_04_2018-06-07_tie_001_fi_001.gif', kuukausi:'Huhtikuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,7 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 14,5 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 21,6 prosenttia.'},
{id: 'i5' , year:  '2018-05-31' , Kotimaiset_mokki: 191668.7, Ulkomaiset_mokki: 49664.87 , Kaikki_mokki: 210558.3 , Kotimaiset_majoitus: 1316079 , Ulkomaiset_majoitus: 557849.4 , Kaikki_majoitus: 1924369 , Kotimaiset_hotelli: 1044658 , Ulkomaiset_hotelli: 464587.3 , Kaikki_hotelli: 1486912, teksti:'<li>Ulkomaisten matkailijoiden yöpymisten määrä väheni 3,9 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,2 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/05/matk_2018_05_2018-07-06_tie_001_fi_001.gif', kuukausi:'Toukokuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Norja saapuneiden turistien määrä, 29,3 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 24,4 prosenttia.'},
{id: 'i6' , year:  '2018-06-30' , Kotimaiset_mokki: 110755.3, Ulkomaiset_mokki: 49470.74 , Kaikki_mokki: 200432.4 , Kotimaiset_majoitus: 1283119 , Ulkomaiset_majoitus: 573556.6 , Kaikki_majoitus: 1888354 , Kotimaiset_hotelli: 1007617 , Ulkomaiset_hotelli: 489440.1 , Kaikki_hotelli: 1460106, teksti:'<li>Ulkomaisten matkailijoiden yöpymisten määrä väheni 4,8 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 4,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/06/matk_2018_06_2018-08-09_tie_001_fi_001.gif', kuukausi:'Kesäkuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 4,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Norja saapuneiden turistien määrä, 11,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 23,8 prosenttia.'},
{id: 'i7' , year:  '2018-07-31' , Kotimaiset_mokki: 118563.1, Ulkomaiset_mokki: 50309.05 , Kaikki_mokki: 198615.6 , Kotimaiset_majoitus: 1220485 , Ulkomaiset_majoitus: 585220.9 , Kaikki_majoitus: 1778818 , Kotimaiset_hotelli: 942092 , Ulkomaiset_hotelli: 500823.3 , Kaikki_hotelli: 1406870, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset vähenivät 2,2 %.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät prosentin.</li><li>HS: Trumpin ja Putinin tapaaminen Helsingissä nosti hotellien hinnat kattoon.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/07/matk_2018_07_2018-09-07_tie_001_fi_001.gif', kuukausi:'Heinäkuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Espanja saapuneiden turistien määrä, 12 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 11,9 prosenttia.'},
{id: 'i8' , year:  '2018-08-31' , Kotimaiset_mokki: 166623.6, Ulkomaiset_mokki: 50098.42 , Kaikki_mokki: 208593.6 , Kotimaiset_majoitus: 1260261 , Ulkomaiset_majoitus: 576107.4 , Kaikki_majoitus: 1811925 , Kotimaiset_hotelli: 985223.6 , Ulkomaiset_hotelli: 486391.3 , Kaikki_hotelli: 1502747, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset vähenivät 2,6 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/08/matk_2018_08_2018-10-05_tie_001_fi_001.gif', kuukausi:'Elokuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2,5 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, 12,9 prosenttia. Eniten väheni maasta Britannia tulleiden turistien määrä, 15,7 prosenttia.'},
{id: 'i9' , year:  '2018-09-30' , Kotimaiset_mokki: 146696.1, Ulkomaiset_mokki: 51321.94 , Kaikki_mokki: 208915.1 , Kotimaiset_majoitus: 1265878 , Ulkomaiset_majoitus: 565972.5 , Kaikki_majoitus: 1782481 , Kotimaiset_hotelli: 986353.9 , Ulkomaiset_hotelli: 476771.6 , Kaikki_hotelli: 1476797, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset vähenivät 4,6 %.</li><li>Kotimaisten matkailijoiden yöpymiset säilyivät edellisvuoden tasolla. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/09/matk_2018_09_2018-11-08_tie_001_fi_001.gif', kuukausi:'Syyskuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 17,6 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 17 prosenttia.'},
{id: 'i10' , year:  '2018-10-31' , Kotimaiset_mokki: 149974.6, Ulkomaiset_mokki: 50025.57 , Kaikki_mokki: 207248.3 , Kotimaiset_majoitus: 1260322 , Ulkomaiset_majoitus: 563856.6 , Kaikki_majoitus: 1764041 , Kotimaiset_hotelli: 969510.4 , Ulkomaiset_hotelli: 465395.1 , Kaikki_hotelli: 1456138, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 6,0 %. </li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 1,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/10/matk_2018_10_2018-12-05_tie_001_fi_001.gif', kuukausi:'Lokakuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 1,6 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 35,3 prosenttia. Eniten väheni maasta Espanja tulleiden turistien määrä, 10,3 prosenttia.'},
{id: 'i11' , year:  '2018-11-30' , Kotimaiset_mokki: 177147.8, Ulkomaiset_mokki: 51673.85 , Kaikki_mokki: 212591.2 , Kotimaiset_majoitus: 1272496 , Ulkomaiset_majoitus: 570961.4 , Kaikki_majoitus: 1799344 , Kotimaiset_hotelli: 980278.2 , Ulkomaiset_hotelli: 473521.7 , Kaikki_hotelli: 1529248, teksti:'<li>Ulkomaisten matkailijoiden yöpymisten määrä säilyi vuoden 2017.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/11/matk_2018_11_2019-01-10_tie_001_fi_001.gif', kuukausi:'Marraskuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 33,3 prosenttia. Eniten väheni maasta Alankomaat tulleiden turistien määrä, 13,7 prosenttia.'},
{id: 'i12' , year:  '2018-12-31' , Kotimaiset_mokki: 163475.4, Ulkomaiset_mokki: 51709.09 , Kaikki_mokki: 215659 , Kotimaiset_majoitus: 1277142 , Ulkomaiset_majoitus: 578093.6 , Kaikki_majoitus: 1855253 , Kotimaiset_hotelli: 979531.8 , Ulkomaiset_hotelli: 481399.3 , Kaikki_hotelli: 1543477, teksti:'<li>Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 6,9 %.</li><li>Kotimaisten matkailijoiden yöpymiset säilyivät edellisvuoden tasolla</li><li>Vuokramökkiyöpymisiä eniten maaliskuussa, näistä kotimaisia yöpymisiä oli 86 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2018/12/matk_2018_12_2019-02-07_tie_001_fi_001.gif', kuukausi:'Joulukuu 2018', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 0,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 31,5 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 9,8 prosenttia.'},
{id: 'i13' , year:  '2019-01-31' , Kotimaiset_mokki: 170563.7, Ulkomaiset_mokki: 52062.89 , Kaikki_mokki: 215915.6 , Kotimaiset_majoitus: 1275081 , Ulkomaiset_majoitus: 561716.9 , Kaikki_majoitus: 1825999 , Kotimaiset_hotelli: 980264.6 , Ulkomaiset_hotelli: 461142.6 , Kaikki_hotelli: 1473875, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät vajaan prosentin.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 1,8 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/01/matk_2019_01_2019-03-01_tie_001_fi_001.gif', kuukausi:'Tammikuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 1,6 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 45,6 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 11 prosenttia.'},
{id: 'i14' , year:  '2019-02-28' , Kotimaiset_mokki: 171830, Ulkomaiset_mokki: 50462 , Kaikki_mokki: 215257.4 , Kotimaiset_majoitus: 1274252 , Ulkomaiset_majoitus: 564164.5 , Kaikki_majoitus: 1820058 , Kotimaiset_hotelli: 977323.5 , Ulkomaiset_hotelli: 461291.3 , Kaikki_hotelli: 1473581, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät 0,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,5 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/02/matk_2019_02_2019-03-29_tie_001_fi_001.gif', kuukausi:'Helmikuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 3,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Ranska saapuneiden turistien määrä, 16,4 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 23,3 prosenttia.'},
{id: 'i15' , year:  '2019-03-31' , Kotimaiset_mokki: 187816, Ulkomaiset_mokki: 56673.92 , Kaikki_mokki: 209195.3 , Kotimaiset_majoitus: 1392622 , Ulkomaiset_majoitus: 569644.9 , Kaikki_majoitus: 2021248 , Kotimaiset_hotelli: 1099210 , Ulkomaiset_hotelli: 467962.7 , Kaikki_hotelli: 1445087, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset vähenivät 0,7 %.</li><li>Kotimaisten matkailijoiden yöpymiset vähenivät 1,4 %..</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/03/matk_2019_03_2019-04-26_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 21,8 prosenttia. Eniten väheni maasta Espanja tulleiden turistien määrä, 25,8 prosenttia.'},
{id: 'i16' , year:  '2019-04-30' , Kotimaiset_mokki: 205276.2, Ulkomaiset_mokki: 61561.46 , Kaikki_mokki: 224321.3 , Kotimaiset_majoitus: 1501451 , Ulkomaiset_majoitus: 573190.4 , Kaikki_majoitus: 2120289 , Kotimaiset_hotelli: 1186376 , Ulkomaiset_hotelli: 475896.6 , Kaikki_hotelli: 1532676, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 5,0 %. Tähän vaikutti osaltaan pääsiäinen, joka tänä vuonna ajoittui kokonaan huhtikuulle</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 2,8.</li><li>Yöpymisiä vuokramökeissä oli tammi-huhtikuussa 1 093 000. Näistä kotimaisia yöpymisiä oli 78 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/04/matk_2019_04_2019-05-29_tie_001_fi_001.gif', kuukausi:'Huhtikuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 4,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Japani saapuneiden turistien määrä, 49,1 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 16 prosenttia.'},
{id: 'i17' , year:  '2019-05-31' , Kotimaiset_mokki: 191162.1, Ulkomaiset_mokki: 63707.28 , Kaikki_mokki: 210119.4 , Kotimaiset_majoitus: 1439576 , Ulkomaiset_majoitus: 607307.5 , Kaikki_majoitus: 2094454 , Kotimaiset_hotelli: 1139725 , Ulkomaiset_hotelli: 508684.7 , Kaikki_hotelli: 1578474, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 12,3 %.</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 4,2 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/05/matk_2019_05_2019-06-28_tie_001_fi_001.gif', kuukausi:'Toukokuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 4,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Venäjä saapuneiden turistien määrä, 73,8 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 7 prosenttia.'},
{id: 'i18' , year:  '2019-06-30' , Kotimaiset_mokki: 196037.3, Ulkomaiset_mokki: 58883.13 , Kaikki_mokki: 210634.5 , Kotimaiset_majoitus: 1403713 , Ulkomaiset_majoitus: 620523.5 , Kaikki_majoitus: 2057555 , Kotimaiset_hotelli: 1093237 , Ulkomaiset_hotelli: 511683.2 , Kaikki_hotelli: 1545131, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 6,4 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 4,8 % ja ulkomaalaisille yöpymisiä tilastoitiin 0,61 miljoonaa. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/06/matk_2019_06_2019-07-26_tie_001_fi_001.gif', kuukausi:'Kesäkuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 7,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, 25 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 5 prosenttia.'},
{id: 'i19' , year:  '2019-07-31' , Kotimaiset_mokki: 143753.5, Ulkomaiset_mokki: 54079.8 , Kaikki_mokki: 212748.5 , Kotimaiset_majoitus: 1264837 , Ulkomaiset_majoitus: 629895.4 , Kaikki_majoitus: 1886380 , Kotimaiset_hotelli: 1066481 , Ulkomaiset_hotelli: 506800.8 , Kaikki_hotelli: 1574598, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 7,0 %.</li><li>Ulkomaisten matkailijoiden yöpymiset pysyivät edelllisvuoden tasolla, lisäystä vain 0,3 %. Ulkomaalaisille yöpymisiä tilastoitiin 0,8 miljoonaa.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/07/matk_2019_07_2019-08-29_tie_001_fi_001.gif', kuukausi:'Heinäkuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 7,9 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 13,8 prosenttia. Eniten väheni maasta Britannia tulleiden turistien määrä, 18 prosenttia.'},
{id: 'i20' , year:  '2019-08-31' , Kotimaiset_mokki: 129825.7, Ulkomaiset_mokki: 50084.09 , Kaikki_mokki: 231148.5 , Kotimaiset_majoitus: 1245638 , Ulkomaiset_majoitus: 625926.9 , Kaikki_majoitus: 1854442 , Kotimaiset_hotelli: 1010104 , Ulkomaiset_hotelli: 517452 , Kaikki_hotelli: 1571492, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,5 % viime vuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 4,0 %.</li><li>Yöpymisiä vuokramökeissä oli tammi-huhtikuussa 890 000. Näistä kotimaisia yöpymisiä oli 80 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/08/matk_2019_08_2019-09-26_tie_001_fi_001.gif', kuukausi:'Elokuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 3,7 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 19,8 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 2,8 prosenttia.'},
{id: 'i21' , year:  '2019-09-30' , Kotimaiset_mokki: 190946.6, Ulkomaiset_mokki: 49141.28 , Kaikki_mokki: 235246.6 , Kotimaiset_majoitus: 1244312 , Ulkomaiset_majoitus: 601957.7 , Kaikki_majoitus: 1796111 , Kotimaiset_hotelli: 988396.5 , Ulkomaiset_hotelli: 510867.2 , Kaikki_hotelli: 1536225, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 9,0 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/09/matk_2019_09_2019-10-31_tie_001_fi_001.gif', kuukausi:'Syyskuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 40,8 prosenttia. Eniten väheni maasta Ruotsi tulleiden turistien määrä, 2,8 prosenttia.'},
{id: 'i22' , year:  '2019-10-31' , Kotimaiset_mokki: 178551.9, Ulkomaiset_mokki: 46911.54 , Kaikki_mokki: 232159.8 , Kotimaiset_majoitus: 1245751 , Ulkomaiset_majoitus: 576653.7 , Kaikki_majoitus: 1778172 , Kotimaiset_hotelli: 986544.7 , Ulkomaiset_hotelli: 494961.8 , Kaikki_hotelli: 1558857, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 5,6 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 12,2 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/10/matk_2019_10_2019-11-28_tie_001_fi_001.gif', kuukausi:'Lokakuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 5,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Ranska saapuneiden turistien määrä, 46,4 prosenttia. Eniten väheni maasta Yhdysvallat tulleiden turistien määrä, 4,7 prosenttia.'},
{id: 'i23' , year:  '2019-11-30' , Kotimaiset_mokki: 154357.9, Ulkomaiset_mokki: 42137.24 , Kaikki_mokki: 239103.3 , Kotimaiset_majoitus: 1282198 , Ulkomaiset_majoitus: 562375.7 , Kaikki_majoitus: 1808948 , Kotimaiset_hotelli: 1004829 , Ulkomaiset_hotelli: 478089.6 , Kaikki_hotelli: 1601647, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 5,5 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 3,4 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/11/matk_2019_11_2020-01-03_tie_001_fi_001.gif', kuukausi:'Marraskuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 5,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Italia saapuneiden turistien määrä, 26,4 prosenttia. Eniten väheni maasta Japani tulleiden turistien määrä, 22,7 prosenttia.'},
{id: 'i24' , year:  '2019-12-31' , Kotimaiset_mokki: 165412.2, Ulkomaiset_mokki: 41987.47 , Kaikki_mokki: 236734.9 , Kotimaiset_majoitus: 1241354 , Ulkomaiset_majoitus: 557941.6 , Kaikki_majoitus: 1851926 , Kotimaiset_hotelli: 958584 , Ulkomaiset_hotelli: 469047.3 , Kaikki_hotelli: 1558284, teksti:'<li>Ulkomaisten matkailijoiden yöpymiset vähenivät  0,4 %</li><li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 %</li><li>Yöpymisiä vuokramökeissä oli touko-elokuussa 688 000. Näistä kotimaisia yöpymisiä oli 78 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2019/12/matk_2019_12_2020-01-31_tie_001_fi_001.gif', kuukausi:'Joulukuu 2019', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 2,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 25,1 prosenttia. Eniten väheni maasta Ruotsi tulleiden turistien määrä, 13,8 prosenttia.'},
{id: 'i25' , year:  '2020-01-31' , Kotimaiset_mokki: 189473.2, Ulkomaiset_mokki: 40038.93 , Kaikki_mokki: 261558.4 , Kotimaiset_majoitus: 1256693 , Ulkomaiset_majoitus: 554946.1 , Kaikki_majoitus: 1860629 , Kotimaiset_hotelli: 977116 , Ulkomaiset_hotelli: 470277.6 , Kaikki_hotelli: 1583406, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 10,4 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 5,3 %.</li><li>Kiinalaisella matkailijalla todettiin Lapissa käydessään vuoden 2020 tammikuun lopussa SARS-CoV-2-infektio</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/01/matk_2020_01_2020-02-27_tie_001_fi_001.gif', kuukausi:'Tammikuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 10,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Kiina saapuneiden turistien määrä, 37,5 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 25,6 prosenttia.'},
{id: 'i26' , year:  '2020-02-29' , Kotimaiset_mokki: 176638.1, Ulkomaiset_mokki: 34341.23 , Kaikki_mokki: 266293 , Kotimaiset_majoitus: 1279388 , Ulkomaiset_majoitus: 522976.5 , Kaikki_majoitus: 1845713 , Kotimaiset_hotelli: 984917.7 , Ulkomaiset_hotelli: 441344.2 , Kaikki_hotelli: 1583938, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät 10,1 %.</li><li>Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 1,9 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/02/matk_2020_02_2020-03-26_tie_001_fi_001.gif', kuukausi:'Helmikuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 10,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Yhdysvallat saapuneiden turistien määrä, 37,5 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 70,4 prosenttia.'},
{id: 'i27' , year:  '2020-03-31' , Kotimaiset_mokki: 87382.92, Ulkomaiset_mokki: 24174.76 , Kaikki_mokki: 64615.71 , Kotimaiset_majoitus: 1001947 , Ulkomaiset_majoitus: 230253.3 , Kaikki_majoitus: 1291412 , Kotimaiset_hotelli: 689824.5 , Ulkomaiset_hotelli: 195115.3 , Kaikki_hotelli: 692579.4, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 44,3 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 53,0 %. </li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/03/matk_2020_03_2020-04-29_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 43,7 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Saksa saapuneiden turistien määrä, -38,8 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 81,5 prosenttia.'},
{id: 'i28' , year:  '2020-04-30' , Kotimaiset_mokki: 78552.07, Ulkomaiset_mokki: 17655.5 , Kaikki_mokki: 34261.4 , Kotimaiset_majoitus: 784004.6 , Ulkomaiset_majoitus: 202775.4 , Kaikki_majoitus: 935066.8 , Kotimaiset_hotelli: 471103.5 , Ulkomaiset_hotelli: 164653.5 , Kaikki_hotelli: 339715.3, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 86,4 %.  </li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 95,2 % ja ulkomaalaisille yöpymisiä tilastoitiin 0,02 miljoonaa.</li><li>Tammi-huhtikuun yöpymisten kokonaismäärä vuokramökeissä 27 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/04/matk_2020_04_2020-05-28_tie_001_fi_001.gif', kuukausi:'Huhtikuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 85,8 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -73,5 prosenttia. Eniten väheni maasta Norja tulleiden turistien määrä, 99,1 prosenttia.'},
{id: 'i29' , year:  '2020-05-31' , Kotimaiset_mokki: 178791.9, Ulkomaiset_mokki: 14359.42 , Kaikki_mokki: 197356.3 , Kotimaiset_majoitus: 857150.8 , Ulkomaiset_majoitus: 165105.6 , Kaikki_majoitus: 981397.1 , Kotimaiset_hotelli: 476857.9 , Ulkomaiset_hotelli: 123767.3 , Kaikki_hotelli: 426416.1, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 74,4 %.</li><li> Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 95,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/05/matk_2020_05_2020-06-25_tie_001_fi_001.gif', kuukausi:'Toukokuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 74,7 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -80,4 prosenttia. Eniten väheni maasta Venäjä tulleiden turistien määrä, 98,8 prosenttia.'},
{id: 'i30' , year:  '2020-06-30' , Kotimaiset_mokki: 257735.7, Ulkomaiset_mokki: 12803.19 , Kaikki_mokki: 248268.9 , Kotimaiset_majoitus: 887714.6 , Ulkomaiset_majoitus: 129190.6 , Kaikki_majoitus: 993442.8 , Kotimaiset_hotelli: 542937.2 , Ulkomaiset_hotelli: 74966.52 , Kaikki_hotelli: 503203.2, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 37,7 %. </li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 92,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/06/matk_2020_06_2020-07-30_tie_001_fi_001.gif', kuukausi:'Kesäkuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 36,1 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -52,8 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,9 prosenttia.'},
{id: 'i31' , year:  '2020-07-31' , Kotimaiset_mokki: 305116.4, Ulkomaiset_mokki: 11087.16 , Kaikki_mokki: 280641.5 , Kotimaiset_majoitus: 1008206 , Ulkomaiset_majoitus: 87732.4 , Kaikki_majoitus: 1105030 , Kotimaiset_hotelli: 913618 , Ulkomaiset_hotelli: 46539.75 , Kaikki_hotelli: 980095.4, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 2,2 %.</li><li>Pääkaupunkiseudulla kotimaiset yöpymiset vähenivät 41,2 % mutta pääkaupunkiseudun ulkopuolella ne lisääntyivät 4,3 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 82,3 %</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/07/matk_2020_07_2020-08-27_tie_001_fi_001.gif', kuukausi:'Heinäkuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 1,6 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Norja saapuneiden turistien määrä, -7,5 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,2 prosenttia.'},
{id: 'i32' , year:  '2020-08-31' , Kotimaiset_mokki: 273887.8, Ulkomaiset_mokki: 9580.4 , Kaikki_mokki: 279019.9 , Kotimaiset_majoitus: 964583.7 , Ulkomaiset_majoitus: 71554.6 , Kaikki_majoitus: 1031394 , Kotimaiset_hotelli: 823893.7 , Ulkomaiset_hotelli: 27417.93 , Kaikki_hotelli: 903962.9, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 6,9 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät ainoastaan 0,6 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 81,6 %.</li><li> Vuokramökkiyöpymiset kasvoivat 25 %. Näistä kotimaisia yöpymisiä oli 95 %.  </li><li>THL jakoi 26. elokuuta maat kolmeen ryhmään: vihreä, keltainen ja punainen.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/08/matk_2020_08_2020-09-24_tie_001_fi_001.gif', kuukausi:'Elokuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 6,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -6,8 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,6 prosenttia.'},
{id: 'i33' , year:  '2020-09-30' , Kotimaiset_mokki: 235597.5, Ulkomaiset_mokki: 7483.857 , Kaikki_mokki: 297346.3 , Kotimaiset_majoitus: 966808.6 , Ulkomaiset_majoitus: 190860.1 , Kaikki_majoitus: 1102774 , Kotimaiset_hotelli: 769409 , Ulkomaiset_hotelli: 158495.3 , Kaikki_hotelli: 981818.3, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 12,3 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 83,1 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/09/matk_2020_09_2020-10-28_tie_001_fi_001.gif', kuukausi:'Syyskuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 12,4 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -28,5 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 97,8 prosenttia.'},
{id: 'i34' , year:  '2020-10-31' , Kotimaiset_mokki: 247616.1, Ulkomaiset_mokki: 6222.034 , Kaikki_mokki: 322167.7 , Kotimaiset_majoitus: 981987.3 , Ulkomaiset_majoitus: 188308.3 , Kaikki_majoitus: 1125902 , Kotimaiset_hotelli: 732998.9 , Ulkomaiset_hotelli: 160921.5 , Kaikki_hotelli: 996483, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 14,8 %.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 81,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/10/matk_2020_10_2020-11-26_tie_001_fi_001.gif', kuukausi:'Lokakuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 15,3 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -48,3 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 96,9 prosenttia.'},
{id: 'i35' , year:  '2020-11-30' , Kotimaiset_mokki: 247538.6, Ulkomaiset_mokki: 5111.616 , Kaikki_mokki: 228107 , Kotimaiset_majoitus: 948974.8 , Ulkomaiset_majoitus: 163497.3 , Kaikki_majoitus: 1049713 , Kotimaiset_hotelli: 663233.5 , Ulkomaiset_hotelli: 143834.9 , Kaikki_hotelli: 897222.1, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 34,3 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,4 %, kun pääkaupunkiseudulla laskua kertyi 59,1 %.</li><li> Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 80,9 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/11/matk_2020_11_2020-12-23_tie_001_fi.html', kuukausi:'Marraskuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 34,2 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -47,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 97,1 prosenttia.'},
{id: 'i36' , year:  '2020-12-31' , Kotimaiset_mokki: 253057.4, Ulkomaiset_mokki: 3850.003 , Kaikki_mokki: 244636.5 , Kotimaiset_majoitus: 1005327 , Ulkomaiset_majoitus: -164207 , Kaikki_majoitus: 943858.1 , Kotimaiset_hotelli: 680007 , Ulkomaiset_hotelli: -154012 , Kaikki_hotelli: 632842.2, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 29,4 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 21,3 %, kun pääkaupunkiseudulla laskua kertyi 57,5 % edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 91,8 %.</li><li>Suomi keskeytti lennot Britanniaan, koska siellä oli alkanut ihmisiä tartuttaa uusi, nopeasti leviävä koronaviruksen muoto.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2020/12/matk_2020_12_2021-01-28_tie_001_fi_001.gif', kuukausi:'Joulukuu 2020', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 29 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -57,1 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 98,2 prosenttia.'},
{id: 'i37' , year:  '2021-01-31' , Kotimaiset_mokki: 221476.1, Ulkomaiset_mokki: 2922.776 , Kaikki_mokki: 235947.9 , Kotimaiset_majoitus: 1031430 , Ulkomaiset_majoitus: -102114 , Kaikki_majoitus: 993676 , Kotimaiset_hotelli: 671258.3 , Ulkomaiset_hotelli: -87046.7 , Kaikki_hotelli: 682000.7, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 34,3 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,7 %, kun pääkaupunkiseudulla laskua kertyi 60,4 % edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 92,7 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/01/matk_2021_01_2021-02-25_tie_001_fi_001.gif', kuukausi:'Tammikuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 34 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -45,4 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 99 prosenttia.'},
{id: 'i38' , year:  '2021-02-28' , Kotimaiset_mokki: 237563.2, Ulkomaiset_mokki: 2531.991 , Kaikki_mokki: 249064.5 , Kotimaiset_majoitus: 1048859 , Ulkomaiset_majoitus: -50832.7 , Kaikki_majoitus: 1027441 , Kotimaiset_hotelli: 660931.4 , Ulkomaiset_hotelli: -32685.2 , Kaikki_hotelli: 727142.4, teksti:'<li>Kotimaisten matkailijoiden yöpymiset vähenivät 32,1 %.</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,4 %, kun pääkaupunkiseudulla laskua kertyi 58,5 % edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 90,3 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/02/matk_2021_02_2021-03-25_tie_001_fi_001.gif', kuukausi:'Helmikuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 32 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -67,9 prosenttia. Eniten väheni maasta Sveitsi tulleiden turistien määrä, 96,5 prosenttia.'},
{id: 'i39' , year:  '2021-03-31' , Kotimaiset_mokki: 293855.2, Ulkomaiset_mokki: 2351.775 , Kaikki_mokki: 261394.2 , Kotimaiset_majoitus: 1182833 , Ulkomaiset_majoitus: -23512.4 , Kaikki_majoitus: 1233390 , Kotimaiset_hotelli: 743123.3 , Ulkomaiset_hotelli: 347.5986 , Kaikki_hotelli: 527470.6, teksti:'<li>Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 11,7 prosenttia</li><li>Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset lisääntyivät 21,5 prosenttia, kun pääkaupunkiseudulla laskua kertyi 37,5 prosenttia edellisvuodesta.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 82,1</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/03/matk_2021_03_2021-04-29_tie_001_fi_001.gif', kuukausi:'Maaliskuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 11,5 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Viro saapuneiden turistien määrä, -33 prosenttia. Eniten väheni maasta Sveitsi tulleiden turistien määrä, 93,8 prosenttia.'},
{id: 'i40' , year:  '2021-04-30' , Kotimaiset_mokki: 246275.9, Ulkomaiset_mokki: 15546.27 , Kaikki_mokki: 259244 , Kotimaiset_majoitus: 1332308 , Ulkomaiset_majoitus: 201890.8 , Kaikki_majoitus: 1486882 , Kotimaiset_hotelli: 849560.5 , Ulkomaiset_hotelli: 189222.5 , Kaikki_hotelli: 778278.7, teksti:'<li>Vuoden 2019 huhtikuun kotimaisten yöpymisten tasoon verrattaessa oltiin huhtikuussa 41 % alemmalla tasolla.</li><li>Ulkomaisten matkailijoiden yöpymiset vähenivät vuoden 2019 huhtikuusta 85 %.</li><li>Vuokramökkiyöpymiset kasvoivat 42 %</li><li>Kotimaiset yöpymiset nousivat 87 % ja ulkomaiset yöpymiset laskivat 95 %.</li>', kuvio:'https://tilastokeskus.fi/til/matk/2021/04/matk_2021_04_2021-05-27_tie_001_fi_003.gif', kuukausi:'Huhtikuu 2021', altteksti:'Kotimaisten matkailijoiden yöpymiset vähenivät 319 prosenttia. Ulkomaalaisten turistien yöpymisistä kasvoi eniten maasta Sveitsi saapuneiden turistien määrä, 439,8 prosenttia. Eniten väheni maasta Kiina tulleiden turistien määrä, 28,9 prosenttia.'}
        ];

//muokataan aineiston päivämäärä stringit päivämäärämuotoisiksi
data.forEach( d => {
  d.Kotimaiset_mokki=+d.Kotimaiset_mokki;	
  d.Ulkomaiset_mokki=+d.Ulkomaiset_mokki;
  //d.Kaikki_mokki=+d.Kaikki_mokki;	
  d.Kotimaiset_majoitus=+d.Kotimaiset_majoitus;
  d.Ulkomaiset_majoitus=+d.Ulkomaiset_majoitus;	
  //d.Kaikki_majoitus=+d.Kaikki_majoitus;
  d.Kotimaiset_hotelli=+d.Kotimaiset_hotelli;	
  d.Ulkomaiset_hotelli=+d.Ulkomaiset_hotelli;
 // d.Kaikki_hotelli=+d.Kaikki_hotelli;	
  d.year= new Date(d.year);
  d.teksti=d.teksti;
  d.kuukausi=d.kuukausi;
  d.kuvio=d.kuvio;
})
var dataFilter = data.map(function(d){return {time: d.year, value: d.Kotimaiset_majoitus, teksti:d.teksti, kuukausi:d.kuukausi, kuvio:d.kuvio, altteksti:d.altteksti} });
//määritellään svg-kuvio, parametreinä yllä kerrotut "mitat" ja nimenä html-tiedostossa määritelty "svg"
var svg = d3.select("#svg")
  .append("svg")
    .attr("width", width + margin.left + margin.right)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform",
          "translate(" + margin.left + "," + margin.top + ")")

          // Lista ryhmistä update-funktiota varten
    var allGroup = ["Kotimaiset_majoitus", "Ulkomaiset_majoitus","Kotimaiset_mokki", "Ulkomaiset_mokki","Kotimaiset_hotelli", "Ulkomaiset_hotelli"]

//määritellään html:ssä määritellyn nimen mukaisen nappulan sisältö
  d3.select("#selectButton")
  .selectAll('myOptions')
  .data(allGroup)
  .enter()
  .append('option')
  .text(function (d) { return d; }) // text showed in the menu
  .attr("value", function (d) { return d; }) // corresponding value returned by the button

//jaotus x-akselia varten
var bisectDate = d3.bisector(function(d) { return d.time; }).left;
//määritellään x-akselin pituus
var x = d3.scaleTime().range([0, width]);
//määritellään y-akselin korkeus
var y = d3.scaleLinear().range([height, 0]);
//määritellään viiva
var line = d3.line()
    .x(function(d) { return x(d.time); })
    .y(function(d) { return y(d.value); });


// Define the div for the tooltip
var div = d3.select("body").append("div")	
    .attr("class", "tooltip")				
    .style("opacity", 0);

// yritetään määritellä html-tooltip


//määritellään marginaalit ja lisätään ne kuvioon?
var g = svg.append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

//määritellään x ja y -akseleiden maksimi ja minimiarvot
    x.domain(d3.extent(dataFilter, function(d) { return d.time; }));
    y.domain([d3.min(data, function(d) { return d.Ulkomaiset_hotelli; }) / 1.005, d3.max(dataFilter, function(d) { return d.value; }) * 1.005]);


//lisätään x-akseli kuvioon
    g.append("g")
        .attr("class", "axis axis--x")
        .attr("transform", "translate(0," + height + ")")
        .call(d3.axisBottom(x))
        .append("text")
        .attr("class", "axis-title")
        
        .attr("y", 30)
        .attr("x", 400)
        .attr("dy", ".71em")
        .style("text-anchor", "end")
        .attr("fill", "#5D6971")
        .text("Aika")
        .style("font-size","35px");;

//lisätään y-akseli kuvioon ja lisätään sinne ominaisuuksia ja tekstiä
    g.append("g")
        .attr("class", "axis axis--y")
        
	 .call(d3.axisLeft(y).ticks(6).tickFormat(function(d) { if ((d) > 1000000) {
 	 
	 return parseInt(d / 1000000)+" "+ parseInt((d / 1000)-1000) + " 000";
	 }
	 else if ((d) === 1000000) {
 	 
	 return "1 000 000";
	 }
	 
	 else if ((d) < 1000000 && (d) > 0) {
 	 
	 return parseInt(d / 1000) + " 000";
	 } else {
 	 return "0";
	 }}
	 ))
	
        .append("text")
        .attr("class", "axis-title")
        .attr("transform", "rotate(-90)")
        .attr("y", -90)
        .attr("x", -270)
        .attr("dy", ".71em")
        .style("text-anchor", "end")
        .attr("fill", "#5D6971")
        .text("Yöpymisten määrä")
        .style("font-size","35px");

//lisätään viiva kuvioon
    g.append("path")
        .datum(dataFilter)
        .attr("class", "line")
        .attr("d", line);

//määritellään viivaa seuraava ja hoveroiva tooltippi
    var focus = g.append("g")
        .attr("class", "focus")
        .style("display", "none");


    focus.append("line")
        .attr("class", "x-hover-line hover-line")
        .attr("y1", 0)
        .attr("y2", height);

    focus.append("line")
        .attr("class", "y-hover-line hover-line")
        .attr("x1", width)
        .attr("x2", width);

    focus.append("circle")
        .attr("r", 7.5);

    //focus.append("text")
        //.attr("x", 15)
      	//.attr("dy", ".31em");
//lisätään neliö jossa tooltippi liikkuu
    svg.append("rect")
        
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
        .attr("class", "overlay")
        .attr("width", width)
        .attr("height", height)

    svg.selectAll("rect")
        .data(dataFilter)
        .on("mouseover.pallo", function() { focus.style("display", null); })
   
        .on("mouseout.pallo", function() { focus.style("display", "none"); })
        .on("mouseout.teksti", function() {		
          d3.select(this).transition()
               .duration(1)
               .attr('opacity', '1');
          div.transition()
               .duration(1)
               .style("opacity", 0);
      })
        .on("mousemove.pallo", mousemove)
        .on("mousemove.teksti", function () {
          // recover coordinate we need

          d3.select(this).transition()
          
          .attr('opacity', '.85');
        div.transition()
        
          .duration(1)
          .style("opacity", 1);
          var x0 = x.invert(d3.mouse(this)[0]);
          var i = bisectDate(dataFilter, x0, 1);
          selectedData = dataFilter[i]
          focus
            .attr("cx", x(selectedData.x))  
            .attr("cy", y(selectedData.y))
            div
            .html(selectedData.kuukausi + "<br> Yöpymisiä:" + Math.round(selectedData.value)+  "<br>" + selectedData.teksti +"<br> <img src='"+selectedData.kuvio+"' alt='"+selectedData.altteksti+"' width='407' height='250'> Kuvion lähde: Majoitustilasto, Tilastokeskus") 
            .style("left", (d3.event.pageX) + "px")		
            .style("top", (d3.event.pageY - 28) + "px");	
         });
//lisätään funktio joka seuraa hiirtä ja aktivoi alla olevan tooltipin
    function mousemove() {
      var x0 = x.invert(d3.mouse(this)[0]),
          i = bisectDate(dataFilter, x0, 1),
          d0 = dataFilter[i - 1],
          d1 = dataFilter[i],
          d = x0 - d0.time > d1.time - x0 ? d1 : d0;
      focus.attr("transform", "translate(" + x(d.time) + "," + y(d.value) + ")");
      //focus.select("html").html()  
      //focus.select("text").text(function() { return d.value; });
      //focus.select(function(){return tooltip2.html("top", (event.pageY-100)+"px").style("left",(event.pageX-100)+"px")});
      //console.log(focus.select("html").html());
	  
      focus.select(".x-hover-line").attr("y2", height - y(d.value));
      focus.select(".y-hover-line").attr("x2", width + width);
    }


    //päivitysfunktio
    function update(selectedGroup) {

        // Muodostaan uusi aineisto
        dataFilter = data.map(function(d){return {time: d.year, value:d[selectedGroup], teksti:d.teksti, kuukausi:d.kuukausi, kuvio:d.kuvio, altteksti:d.altteksti} });

        console.log(dataFilter);
        // määritellään päivitettävä objekti
        //  var u = svg.selectAll("line")
        //.data([dataFilter])
    
      // Updata the line
      g.selectAll(".line")

        .datum(dataFilter)
  
            .transition()


            .duration(1000)

       
            .attr("d", d3.line()
            .x(function(d) { return x(+d.time) })
            .y(function(d) { return y(+d.value) })
            )

           


      g.selectAll(".rect")
      mousemove()
      }

  
      // When the button is changed, run the updateChart function
      d3.select("#selectButton").on("change", function(d) {
          // recover the option that has been chosen
          var selectedOption = d3.select(this).property("value")
          // run the updateChart function with this selected option
          update(selectedOption)
       

      })
   
 </script> 
</div>
```

#### Kausitasoituksen R-koodi

```
library(RJDemetra)

havainnot = read.csv("/majoitustilasto.csv",header=TRUE,stringsAsFactors = FALSE)

kotimaiset = ts(havainnot$Kotimaiset,frequency=12,start=c(2018,1))
ulkomaiset = ts(havainnot$Ulkomaiset,frequency=12,start=c(2018,1))
total = ts(havainnot$Yhteensä, frequency=12,start=c(2018,1))

fi = tramoseats(kotimaiset,"RSAfull")
ul = tramoseats(ulkomaiset,"RSAfull")
kaikki = tramoseats(total,"RSAfull")

data1 = fi$final$series
data2 = ul$final$series
data3 = kaikki$final$series
```

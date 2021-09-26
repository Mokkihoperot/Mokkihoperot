## Tilastokeskuksen Visualisointihackathon 15.9.2021 - 30.9.2021

Tämä on joukkue Mökkihöperöiden kilpailytyö Tilastokeskuksen Visualisointihackathonia varten. TIlastokeskuksen Visualisointihackathonin tehtävänä oli tehdä visualisointi pohjautuen TIlastokeskuksen kokeellisiin tilastoihin.

Joukkueemme visualisoi kokeellisista tilastoista vuokramökkitilaston lukuja tekemällä aikasarjaan tasoituksen ja viemällä vuokramökkitilaston ja majoitustilaston luvut viivakuvioon. Kilpailutyö on alla ja sen alle on koottu vastaukset tuomariston esittämiin kysymyksiin ja myös käytetty koodi aineistoineen. Visualisointityön toteutuksessa on käytetty D3js-javascript-kirjastoa. Aikasarjan tasoitus on tehty R-ohjelmistolla.

### Selvitä alta miten vuokramökkitilaston ja majoitustilaston luvut muuttuivat ennen koronakriisiä ja sen jälkeen

Kuvion tiedot perustuvat vuokramökkitilaston ja majoitustilaston kuukausittaisiin kysyntätietoihin. Niihin on tehty aikasarjan tasoitus. Ajankohtaa kuvaavat tiedot on kerätty Helsingin sanomista ja vuokramökkitilaston ja majoitustilaston julkaisuista.
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
    width: 400px;					
    height: 280px;					
    padding: 2px;				
    font: 14px sans-serif;		
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
  {id: 'i1' , year:  '2018-01-31' , Kotimaiset_mokki: 160418.899397872, Ulkomaiset_mokki: 47536.0679702308 , Kaikki_mokki: 206535.348195943 , Kotimaiset_majoitus: 1287632.83657742 , Ulkomaiset_majoitus: 553365.903918954 , Kaikki_majoitus: 1840836.09952686 , Kotimaiset_hotelli: 1002645.79307038 , Ulkomaiset_hotelli: 553365.903918954 , Kaikki_hotelli: 1474079.62823602, teksti:'Ulkomaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 6,6 prosenttia viime vuodesta ja heille tilastoitiin lähes 670 000 yöpymisvuorokautta tammikuussa 2018. Kotimaisten matkailijoiden yöpymiset olivat lähes viime vuoden tasolla; lisäystä ainoastaan 0,5 prosenttia. ', kuukausi:'Tammikuu 2018'},
  {id: 'i2' , year:  '2018-02-28' , Kotimaiset_mokki: 159768.348849501, Ulkomaiset_mokki: 48892.6993162125 , Kaikki_mokki: 206093.549738274 , Kotimaiset_majoitus: 1275745.32463332 , Ulkomaiset_majoitus: 557035.922642505 , Kaikki_majoitus: 1822660.90348603 , Kotimaiset_hotelli: 995552.361809476 , Ulkomaiset_hotelli: 557035.922642505 , Kaikki_hotelli: 1468223.27797608, teksti:'Ulkomaisten matkailijoiden yöpymiset lisääntyivät edelleen Suomen majoitusliikkeissä, 6,7 prosenttia viime vuodesta, ja heille tilastoitiin 600 000 yöpymisvuorokautta helmikuussa 2018. Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,7 prosenttia ja he yöpyivät majoitusliikkeissä lähes 1,1 miljoonaa kertaa.', kuukausi:'Helmikuu 2018'},
  {id: 'i3' , year:  '2018-03-31' , Kotimaiset_mokki: 180612.361812326, Ulkomaiset_mokki: 48591.9222414404 , Kaikki_mokki: 212675.406139697 , Kotimaiset_majoitus: 1332794.47976957 , Ulkomaiset_majoitus: 563614.847448006 , Kaikki_majoitus: 1967698.39313397 , Kotimaiset_hotelli: 1011744.50167555 , Ulkomaiset_hotelli: 563614.847448006 , Kaikki_hotelli: 1488949.57561267, teksti:'Ulkomaisten matkailijoiden yöpymisten määrä jatkoi kasvuaan Suomen majoitusliikkeissä maaliskuussa 2018. Heille tilastoitiin runsaat 574 000 yöpymisvuorokautta, mikä oli 7,7 prosenttia enemmän kuin vuotta aiemmin. Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,8 prosenttia ja he yöpyivät majoitusliikkeissä 1,3 miljoonaa kertaa.', kuukausi:'Maaliskuu 2018'},
  {id: 'i4' , year:  '2018-04-30' , Kotimaiset_mokki: 223428.405629033, Ulkomaiset_mokki: 50172.4686045338 , Kaikki_mokki: 198593.579396405 , Kotimaiset_majoitus: 1328932.98741812 , Ulkomaiset_majoitus: 568954.067730214 , Kaikki_majoitus: 1972526.13447426 , Kotimaiset_hotelli: 1000288.26809782 , Ulkomaiset_hotelli: 568954.067730214 , Kaikki_hotelli: 1477240.04766766, teksti:'Ulkomaisten matkailijoiden yöpymisten määrä kasvoi hieman Suomen majoitusliikkeissä huhtikuussa 2018. Heille tilastoitiin runsaat 372 000 yöpymisvuorokautta, mikä oli 4,2 prosenttia enemmän kuin vuotta aiemmin. Sitä vastoin kotimaisten matkailijoiden yöpymiset vähenivät 1,2 prosenttia ja he yöpyivät majoitusliikkeissä vajaat 1,2 miljoonaa kertaa. ', kuukausi:'Huhtikuu 2018'},
  {id: 'i5' , year:  '2018-05-31' , Kotimaiset_mokki: 191668.709111412, Ulkomaiset_mokki: 49664.8694670685 , Kaikki_mokki: 210558.304750901 , Kotimaiset_majoitus: 1316079.20506816 , Ulkomaiset_majoitus: 557849.38651284 , Kaikki_majoitus: 1924368.70993186 , Kotimaiset_hotelli: 1000413.20387975 , Ulkomaiset_hotelli: 557849.38651284 , Kaikki_hotelli: 1465811.14298807, teksti:'Ulkomaisten matkailijoiden yöpymisten määrä väheni Suomen majoitusliikkeissä toukokuussa 2018. Heille tilastoitiin vajaat 413 000 yöpymisvuorokautta, mikä oli 3,9 prosenttia vähemmän kuin vuotta aiemmin. Sitä vastoin kotimaisten matkailijoiden yöpymiset lisääntyivät 2,2 prosenttia ja he yöpyivät majoitusliikkeissä runsaat miljoonaa kertaa.', kuukausi:'Toukokuu 2018'},
  {id: 'i6' , year:  '2018-06-30' , Kotimaiset_mokki: 110755.264152011, Ulkomaiset_mokki: 49470.7375341685 , Kaikki_mokki: 200432.376437017 , Kotimaiset_majoitus: 1283119.37963841 , Ulkomaiset_majoitus: 573556.613876702 , Kaikki_majoitus: 1888354.18797557 , Kotimaiset_hotelli: 997140.439563066 , Ulkomaiset_hotelli: 573556.613876702 , Kaikki_hotelli: 1473184.2094203, teksti:'Ulkomaisten matkailijoiden yöpymisten kasvu Suomen majoitusliikkeissä taittui toukokuussa ja väheni edelleen kesäkuussa 2018. Heille tilastoitiin lähes 590 000 yöpymisvuorokautta, mikä oli 4,8 prosenttia vähemmän kuin vuotta aiemmin. Sitä vastoin kotimaisten matkailijoiden yöpymiset lisääntyivät 4,5 prosenttia ja he yöpyivät majoitusliikkeissä lähes 1,6 miljoonaa kertaa. ', kuukausi:'Kesäkuu 2018'},
  {id: 'i7' , year:  '2018-07-31' , Kotimaiset_mokki: 118563.097637134, Ulkomaiset_mokki: 50309.0522841094 , Kaikki_mokki: 198615.616005876 , Kotimaiset_majoitus: 1220485.01327049 , Ulkomaiset_majoitus: 585220.851193028 , Kaikki_majoitus: 1778818.40635518 , Kotimaiset_hotelli: 966111.61561859 , Ulkomaiset_hotelli: 585220.851193028 , Kaikki_hotelli: 1447894.56027745, teksti:'Suurista kansainvälisistä tapahtumista huolimatta ulkomaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä heinäkuussa 2018. Heille tilastoitiin runsaat 798 000 yöpymisvuorokautta, mikä oli 2,2 prosenttia vähemmän kuin vuotta aiemmin.', kuukausi:'Heinäkuu 2018'},
  {id: 'i8' , year:  '2018-08-31' , Kotimaiset_mokki: 166623.560305308, Ulkomaiset_mokki: 50098.4154504198 , Kaikki_mokki: 208593.610065436 , Kotimaiset_majoitus: 1260260.67032535 , Ulkomaiset_majoitus: 576107.430907086 , Kaikki_majoitus: 1811924.62097328 , Kotimaiset_hotelli: 1002820.42849751 , Ulkomaiset_hotelli: 576107.430907086 , Kaikki_hotelli: 1477696.40108352, teksti:'Ulkomaisten matkailijoiden yöpymiset vähenivät edelleen Suomen majoitusliikkeissä elokuussa 2018. Heille tilastoitiin lähes 754 000 yöpymisvuorokautta, mikä oli 2,6 prosenttia vähemmän kuin vuotta aiemmin. Sitä vastoin kotimaisten matkailijoiden yöpymiset lisääntyivät 2,5 prosenttia ja he yöpyivät majoitusliikkeissä lähes 1,6 miljoonaa kertaa.', kuukausi:'Elokuu 2018'},
  {id: 'i9' , year:  '2018-09-30' , Kotimaiset_mokki: 146696.069038291, Ulkomaiset_mokki: 51321.9347985039 , Kaikki_mokki: 208915.142470026 , Kotimaiset_majoitus: 1265877.57495797 , Ulkomaiset_majoitus: 565972.537575567 , Kaikki_majoitus: 1782481.32571447 , Kotimaiset_hotelli: 1007170.85612249 , Ulkomaiset_hotelli: 565972.537575567 , Kaikki_hotelli: 1478515.19133946, teksti:'Ulkomaisten matkailijoiden yöpymisten kasvu kääntyi laskuun toukokuussa jatkuen koko kesän, ja väheni edelleen Suomen majoitusliikkeissä syyskuussa 2018. Heille tilastoitiin 466 000 yöpymisvuorokautta, mikä oli 4,6 prosenttia vähemmän kuin vuotta aiemmin. Sitä vastoin kotimaisten matkailijoiden yöpymiset säilyivät edellisvuoden tasolla ja he yöpyivät majoitusliikkeissä lähes 1,3 miljoonaa kertaa. ', kuukausi:'Syyskuu 2018'},
  {id: 'i10' , year:  '2018-10-31' , Kotimaiset_mokki: 149974.63726948, Ulkomaiset_mokki: 50025.5725470827 , Kaikki_mokki: 207248.275586979 , Kotimaiset_majoitus: 1260321.54712267 , Ulkomaiset_majoitus: 563856.626740325 , Kaikki_majoitus: 1764040.71150468 , Kotimaiset_hotelli: 1004321.3437931 , Ulkomaiset_hotelli: 563856.626740325 , Kaikki_hotelli: 1474248.559763, teksti:'Kesäkauden pienen notkahduksen jälkeen ulkomaisten matkailijoiden yöpymisten määrä kääntyi taas kasvuun lokakuussa 2018. Heille tilastoitiin Suomen majoitusliikkeissä lähes 390 000 yöpymisvuorokautta, mikä oli 6,0 prosenttia enemmän kuin vuotta aiemmin. Kotimaisten matkailijoiden yöpymiset lisääntyivät 1,5 prosenttia ja he yöpyivät majoitusliikkeissä lähes 1,2 miljoonaa kertaa.', kuukausi:'Lokakuu 2018'},
  {id: 'i11' , year:  '2018-11-30' , Kotimaiset_mokki: 177147.753443173, Ulkomaiset_mokki: 51673.8500995981 , Kaikki_mokki: 212591.188737768 , Kotimaiset_majoitus: 1272495.9983794 , Ulkomaiset_majoitus: 570961.374652404 , Kaikki_majoitus: 1799343.74614313 , Kotimaiset_hotelli: 1011593.01730805 , Ulkomaiset_hotelli: 570961.374652404 , Kaikki_hotelli: 1487765.11012854, teksti:'Ulkomaisten matkailijoiden yöpymisten määrä säilyi vuoden 2017 tasolla marraskuussa 2018. Heille tilastoitiin Suomen majoitusliikkeissä lähes 435 000 yöpymisvuorokautta, mikä oli ainoastaan 0,4 prosenttia enemmän kuin vuotta aiemmin. Kotimaisten matkailijoiden yöpymiset lisääntyivät 2,0 prosenttia ja he yöpyivät majoitusliikkeissä runsaat miljoonaa kertaa. ', kuukausi:'Marraskuu 2018'},
  {id: 'i12' , year:  '2018-12-31' , Kotimaiset_mokki: 163475.410174613, Ulkomaiset_mokki: 51709.0884991417 , Kaikki_mokki: 215659.008471515 , Kotimaiset_majoitus: 1277141.93383025 , Ulkomaiset_majoitus: 578093.622259747 , Kaikki_majoitus: 1855252.67043819 , Kotimaiset_hotelli: 1023474.44526699 , Ulkomaiset_hotelli: 578093.622259747 , Kaikki_hotelli: 1513071.94918551, teksti:'Yöpymisiä vuokramökeissä oli vuoden aikana arviolta 2 484 000. Näistä kotimaisia yöpymisiä oli 1 945 000 ja ulkomaisia yöpymisiä 539 000. Eniten vuokramökkiyöpymisiä oli maaliskuussa, jolloin vuokramökkiyöpymisiä oli 395 000. Näistä kotimaisia yöpymisiä oli 340 000 ja ulkomaisia yöpymisiä 55 000. Ulkomaisten matkailijoiden yöpymisten määrä kasvoi 6,9 prosenttia joulukuussa 2018. Heille tilastoitiin Suomen majoitusliikkeissä lähes 788 000 yöpymisvuorokautta. Kotimaisten matkailijoiden yöpymiset säilyivät edellisvuoden tasolla. He yöpyivät majoitusliikkeissä vajaat 966 000 kertaa, vähennystä ainoastaan 0,3 prosenttia. ', kuukausi:'Joulukuu 2018'},
  {id: 'i13' , year:  '2019-01-31' , Kotimaiset_mokki: 170563.748502708, Ulkomaiset_mokki: 52062.8854297549 , Kaikki_mokki: 215915.601603738 , Kotimaiset_majoitus: 1275081.46090252 , Ulkomaiset_majoitus: 561716.850432946 , Kaikki_majoitus: 1825998.8995311 , Kotimaiset_hotelli: 1017621.32935554 , Ulkomaiset_hotelli: 561716.850432946 , Kaikki_hotelli: 1497940.95072361, teksti:'Ulkomaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä vajaan prosentin viime vuodesta ja heille tilastoitiin runsaat 675 000 yöpymisvuorokautta tammikuussa 2019. Kotimaisten matkailijoiden yöpymiset lisääntyivät 1,8 prosenttia ja heille tilastoitiin majoitusliikkeissä noin 871 000 yöpymisvuorokautta.', kuukausi:'Tammikuu 2019'},
  {id: 'i14' , year:  '2019-02-28' , Kotimaiset_mokki: 171829.995887883, Ulkomaiset_mokki: 50461.9966458424 , Kaikki_mokki: 215257.447735897 , Kotimaiset_majoitus: 1274251.59791008 , Ulkomaiset_majoitus: 564164.476880247 , Kaikki_majoitus: 1820058.02493123 , Kotimaiset_hotelli: 1023500.31196546 , Ulkomaiset_hotelli: 564164.476880247 , Kaikki_hotelli: 1510719.06669381, teksti:'Ulkomaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 0,7 prosenttia viime vuodesta ja heille tilastoitiin vajaat 603 000 yöpymisvuorokautta helmikuussa 2019. Kotimaisten matkailijoiden yöpymiset lisääntyivät 3,5 prosenttia ja heille tilastoitiin majoitusliikkeissä runsaat 1,1 miljoonaa yöpymisvuorokautta.', kuukausi:'Helmikuu 2019'},
  {id: 'i15' , year:  '2019-03-31' , Kotimaiset_mokki: 187816.005856526, Ulkomaiset_mokki: 56673.9164381624 , Kaikki_mokki: 209195.32812249 , Kotimaiset_majoitus: 1392622.18993921 , Ulkomaiset_majoitus: 569644.899652901 , Kaikki_majoitus: 2021248.0601241 , Kotimaiset_hotelli: 1038656.11494008 , Ulkomaiset_hotelli: 569644.899652901 , Kaikki_hotelli: 1528002.6870682, teksti:'Ulkomaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 0,7 prosenttia viime vuodesta ja heille tilastoitiin noin 570 000 yöpymisvuorokautta maaliskuussa 2019. Kotimaisten matkailijoiden yöpymiset vähenivät 1,4 prosenttia ja heille tilastoitiin majoitusliikkeissä runsaat 1,3 miljoonaa yöpymisvuorokautta.', kuukausi:'Maaliskuu 2019'},
  {id: 'i16' , year:  '2019-04-30' , Kotimaiset_mokki: 205276.149913359, Ulkomaiset_mokki: 61561.4593384958 , Kaikki_mokki: 224321.313420137 , Kotimaiset_majoitus: 1501450.59193072 , Ulkomaiset_majoitus: 573190.397235085 , Kaikki_majoitus: 2120289.30077783 , Kotimaiset_hotelli: 1056410.29739044 , Ulkomaiset_hotelli: 573190.397235085 , Kaikki_hotelli: 1525687.3479877, teksti:'Yöpymisiä suurimpien kotimaisten kauppapaikkojen ja välityspalvelujen kohteissa oli tammi-huhtikuussa arviolta 1 093 000. Näistä kotimaisia yöpymisiä oli 862 000 ja ulkomaisia yöpymisiä 231 000. Eniten vuokramökkiyöpymisiä oli maaliskuussa, jolloin niitä oli 380 000. Maaliskuun yöpymisistä kotimaisia oli 324 000 ja ulkomaisia 55 000. Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 5,0 prosenttia viime vuodesta ja heille tilastoitiin 1,23 miljoonaa yöpymisvuorokautta huhtikuussa 2019. Tähän vaikutti osaltaan pääsiäinen, joka tänä vuonna ajoittui kokonaan huhtikuulle. Ulkomaisten matkailijoiden yöpymiset sen sijaan vähenivät edellisvuodesta 2,8 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,36 miljoonaa. ', kuukausi:'Huhtikuu 2019'},
  {id: 'i17' , year:  '2019-05-31' , Kotimaiset_mokki: 191162.069529533, Ulkomaiset_mokki: 63707.2814784566 , Kaikki_mokki: 210119.436320556 , Kotimaiset_majoitus: 1439575.98621645 , Ulkomaiset_majoitus: 607307.477746499 , Kaikki_majoitus: 2094453.6712219 , Kotimaiset_hotelli: 1058720.50094371 , Ulkomaiset_hotelli: 607307.477746499 , Kaikki_hotelli: 1542712.41246602, teksti:'Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 12,3 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,46 miljoonaa. Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 4,2 prosenttia viime vuodesta ja heille tilastoitiin 1,11 miljoonaa yöpymisvuorokautta toukokuussa 2019. ', kuukausi:'Toukokuu 2019'},
  {id: 'i18' , year:  '2019-06-30' , Kotimaiset_mokki: 196037.257062061, Ulkomaiset_mokki: 58883.1323714946 , Kaikki_mokki: 210634.491375907 , Kotimaiset_majoitus: 1403712.54752814 , Ulkomaiset_majoitus: 620523.532257141 , Kaikki_majoitus: 2057555.44294048 , Kotimaiset_hotelli: 1052938.5668031 , Ulkomaiset_hotelli: 620523.532257141 , Kaikki_hotelli: 1532825.01902494, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 6,4 prosenttia viime vuodesta ja heille tilastoitiin 1,67 miljoonaa yöpymisvuorokautta kesäkuussa 2019. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 4,8 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,61 miljoonaa. ', kuukausi:'Kesäkuu 2019'},
  {id: 'i19' , year:  '2019-07-31' , Kotimaiset_mokki: 143753.525366777, Ulkomaiset_mokki: 54079.7971274542 , Kaikki_mokki: 212748.45404822 , Kotimaiset_majoitus: 1264836.53569056 , Ulkomaiset_majoitus: 629895.349628559 , Kaikki_majoitus: 1886379.83881103 , Kotimaiset_hotelli: 1026330.89695594 , Ulkomaiset_hotelli: 629895.349628559 , Kaikki_hotelli: 1507369.34702672, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 7,0 prosenttia viime vuodesta ja heille tilastoitiin 2,45 miljoonaa yöpymisvuorokautta heinäkuussa 2019. Ulkomaisten matkailijoiden yöpymiset pysyivät edelllisvuoden tasolla, lisäystä vain 0,3 prosenttia. Ulkomaalaisille yöpymisiä tilastoitiin 0,8 miljoonaa.', kuukausi:'Heinäkuu 2019'},
  {id: 'i20' , year:  '2019-08-31' , Kotimaiset_mokki: 129825.678015024, Ulkomaiset_mokki: 50084.0910268816 , Kaikki_mokki: 231148.519717388 , Kotimaiset_majoitus: 1245637.73078241 , Ulkomaiset_majoitus: 625926.907298473 , Kaikki_majoitus: 1854441.78524166 , Kotimaiset_hotelli: 1017649.61487235 , Ulkomaiset_hotelli: 625926.907298473 , Kaikki_hotelli: 1513326.26343476, teksti:'Yöpymisiä suurimpien kotimaisten kauppapaikkojen ja välityspalvelujen kohteissa oli toisen kolmanneksen aikana arviolta 890 000. Näistä kotimaisia yöpymisiä oli 710 000 ja ulkomaisia yöpymisiä 180 000. Eniten vuokramökkiyöpymisiä oli heinäkuussa, jolloin yöpymisiä oli 367 000. Heinäkuun yöpymisistä kotimaisia oli 307 000 ja ulkomaisia 60 000. Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 3,5 prosenttia viime vuodesta ja heille tilastoitiin 1,62 miljoonaa yöpymisvuorokautta elokuussa 2019. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 4,0 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,78 miljoonaa. ', kuukausi:'Elokuu 2019'},
  {id: 'i21' , year:  '2019-09-30' , Kotimaiset_mokki: 190946.62890019, Ulkomaiset_mokki: 49141.2757584265 , Kaikki_mokki: 235246.599147761 , Kotimaiset_majoitus: 1244312.07654142 , Ulkomaiset_majoitus: 601957.648476284 , Kaikki_majoitus: 1796111.03019064 , Kotimaiset_hotelli: 1028662.00638893 , Ulkomaiset_hotelli: 601957.648476284 , Kaikki_hotelli: 1498027.98372311, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 2,0 prosenttia viime vuodesta ja heille tilastoitiin 1,3 miljoonaa yöpymisvuorokautta syyskuussa 2019. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 9,0 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,51 miljoonaa. ', kuukausi:'Syyskuu 2019'},
  {id: 'i22' , year:  '2019-10-31' , Kotimaiset_mokki: 178551.914308793, Ulkomaiset_mokki: 46911.539686738 , Kaikki_mokki: 232159.834146644 , Kotimaiset_majoitus: 1245751.43027128 , Ulkomaiset_majoitus: 576653.681875229 , Kaikki_majoitus: 1778171.6767872 , Kotimaiset_hotelli: 1048140.23025862 , Ulkomaiset_hotelli: 576653.681875229 , Kaikki_hotelli: 1524642.32458485, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 5,6 prosenttia viime vuodesta ja heille tilastoitiin 1,24 miljoonaa yöpymisvuorokautta lokakuussa 2019. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 12,2 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,43 miljoonaa.', kuukausi:'Lokakuu 2019'},
  {id: 'i23' , year:  '2019-11-30' , Kotimaiset_mokki: 154357.870420303, Ulkomaiset_mokki: 42137.2354636735 , Kaikki_mokki: 239103.317616779 , Kotimaiset_majoitus: 1282197.57177542 , Ulkomaiset_majoitus: 562375.710811564 , Kaikki_majoitus: 1808947.51459738 , Kotimaiset_hotelli: 1089191.86035152 , Ulkomaiset_hotelli: 562375.710811564 , Kaikki_hotelli: 1559858.00379988, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 5,5 prosenttia viime vuodesta ja heille tilastoitiin 1,11 miljoonaa yöpymisvuorokautta marraskuussa 2019. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 3,4 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,45 miljoonaa. ', kuukausi:'Marraskuu 2019'},
  {id: 'i24' , year:  '2019-12-31' , Kotimaiset_mokki: 165412.152893532, Ulkomaiset_mokki: 41987.4735325067 , Kaikki_mokki: 236734.946925283 , Kotimaiset_majoitus: 1241353.90994368 , Ulkomaiset_majoitus: 557941.577024038 , Kaikki_majoitus: 1851925.55688879 , Kotimaiset_hotelli: 1073033.56420313 , Ulkomaiset_hotelli: 557941.577024038 , Kaikki_hotelli: 1610567.60932406, teksti:'Yöpymisiä suurimpien kotimaisten kauppapaikkojen ja välityspalvelujen kohteissa oli kolmanneksen aikana noin 688 000. Näistä kotimaisia yöpymisiä oli 535 000 ja ulkomaisia yöpymisiä 153 000. Eniten vuokramökkiyöpymisiä oli joulukuussa, jolloin vuokramökkiyöpymisiä oli 266 000. Joulukuun yöpymisistä kotimaisia oli 185 000 ja ulkomaisia 81 000. Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 2,0 prosenttia viime vuodesta ja heille tilastoitiin 0,99 miljoonaa yöpymisvuorokautta joulukuussa 2019. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 0,4 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,79 miljoonaa.', kuukausi:'Joulukuu 2019'},
  {id: 'i25' , year:  '2020-01-31' , Kotimaiset_mokki: 189473.161743078, Ulkomaiset_mokki: 40038.9265544493 , Kaikki_mokki: 261558.390741335 , Kotimaiset_majoitus: 1256693.07730716 , Ulkomaiset_majoitus: 554946.100941445 , Kaikki_majoitus: 1860629.36557434 , Kotimaiset_hotelli: 1109308.24604813 , Ulkomaiset_hotelli: 554946.100941445 , Kaikki_hotelli: 1647611.78779264, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 10,4 prosenttia viime vuodesta ja heille tilastoitiin 0,95 miljoonaa yöpymisvuorokautta tammikuussa 2020. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 5,3 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,71 miljoonaa', kuukausi:'Tammikuu 2020'},
  {id: 'i26' , year:  '2020-02-29' , Kotimaiset_mokki: 176638.07622178, Ulkomaiset_mokki: 34341.230773168 , Kaikki_mokki: 266292.962331495 , Kotimaiset_majoitus: 1279388.22548343 , Ulkomaiset_majoitus: 522976.536911563 , Kaikki_majoitus: 1845713.08941734 , Kotimaiset_hotelli: 1135251.56576473 , Ulkomaiset_hotelli: 522976.536911563 , Kaikki_hotelli: 1667607.65517911, teksti:'Kotimaisten matkailijoiden yöpymiset lisääntyivät Suomen majoitusliikkeissä 10,1 prosenttia viime vuodesta ja heille tilastoitiin 1,23 miljoonaa yöpymisvuorokautta helmikuussa 2020. Ulkomaisten matkailijoiden yöpymiset lisääntyivät edellisvuodesta 1,9 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,61 miljoonaa.', kuukausi:'Helmikuu 2020'},
  {id: 'i27' , year:  '2020-03-31' , Kotimaiset_mokki: 87382.9183135135, Ulkomaiset_mokki: 24174.7617930785 , Kaikki_mokki: 64615.7093671322 , Kotimaiset_majoitus: 1001947.35461219 , Ulkomaiset_majoitus: 230253.287932186 , Kaikki_majoitus: 1291411.5760783 , Kotimaiset_hotelli: 646740.308376003 , Ulkomaiset_hotelli: 230253.287932186 , Kaikki_hotelli: 929602.717793361, teksti:'Koronaviruksesta johtuvan markkinatilanteen vuoksi kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 44,3 prosenttia viime vuodesta ja heille tilastoitiin 0,72 miljoonaa yöpymisvuorokautta maaliskuussa 2020. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 53,0 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,27 miljoonaa. ', kuukausi:'Maaliskuu 2020'},
  {id: 'i28' , year:  '2020-04-30' , Kotimaiset_mokki: 78552.0655414631, Ulkomaiset_mokki: 17655.5017565533 , Kaikki_mokki: 34261.4030747867 , Kotimaiset_majoitus: 784004.633050382 , Ulkomaiset_majoitus: 202775.372239001 , Kaikki_majoitus: 935066.784579854 , Kotimaiset_hotelli: 284644.307974629 , Ulkomaiset_hotelli: 202775.372239001 , Kaikki_hotelli: 439241.815776025, teksti:'Vuoteen 2019 verrattuna tammi-huhtikuun yöpymisten kokonaismäärä laski noin 297 000 yöpymisellä eli noin 27 prosenttia. Kotimaiset yöpymiset laskivat noin 260 000 yöpymisellä ja ulkomaiset yöpymiset laskivat noin 37 000 yöpymisellä. Eniten yöpymiset vähenivät huhtikuussa, jolloin yöpymiset vähenivät 193 000 yöpymisellä. Myös maaliskuussa yöpymiset laskivat merkittävästi 158 000 yöpymisellä. Koronaviruksesta johtuvan markkinatilanteen vuoksi kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 86,4 prosenttia viime vuodesta ja heille tilastoitiin 0,17 miljoonaa yöpymisvuorokautta huhtikuussa 2020. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 95,2 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,02 miljoonaa. ', kuukausi:'Huhtikuu 2020'},
  {id: 'i29' , year:  '2020-05-31' , Kotimaiset_mokki: 178791.855782677, Ulkomaiset_mokki: 14359.4154435756 , Kaikki_mokki: 197356.304417034 , Kotimaiset_majoitus: 857150.789888375 , Ulkomaiset_majoitus: 165105.548298961 , Kaikki_majoitus: 981397.058352592 , Kotimaiset_hotelli: 393114.446478033 , Ulkomaiset_hotelli: 165105.548298961 , Kaikki_hotelli: 476862.86236084, teksti:'Koronaviruksesta johtuvan markkinatilanteen vuoksi kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 74,4 prosenttia viime vuodesta ja heille tilastoitiin 0,28 miljoonaa yöpymisvuorokautta toukokuussa 2020. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 95,7 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,02 miljoonaa. ', kuukausi:'Toukokuu 2020'},
  {id: 'i30' , year:  '2020-06-30' , Kotimaiset_mokki: 257735.711667622, Ulkomaiset_mokki: 12803.1913751535 , Kaikki_mokki: 248268.937234094 , Kotimaiset_majoitus: 887714.638014055 , Ulkomaiset_majoitus: 129190.612346519 , Kaikki_majoitus: 993442.80207108 , Kotimaiset_hotelli: 484342.34032251 , Ulkomaiset_hotelli: 129190.612346519 , Kaikki_hotelli: 523422.978763013, teksti:'Koronaviruksesta johtuvan markkinatilanteen vuoksi kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 37,7 prosenttia viime vuodesta ja heille tilastoitiin 1,05 miljoonaa yöpymisvuorokautta kesäkuussa 2020. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 92,7 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,04 miljoonaa.', kuukausi:'Kesäkuu 2020'},
  {id: 'i31' , year:  '2020-07-31' , Kotimaiset_mokki: 305116.406069144, Ulkomaiset_mokki: 11087.163074751 , Kaikki_mokki: 280641.524079977 , Kotimaiset_majoitus: 1008205.58599094 , Ulkomaiset_majoitus: 87732.3961078963 , Kaikki_majoitus: 1105029.6396313 , Kotimaiset_hotelli: 717394.498938541 , Ulkomaiset_hotelli: 87732.3961078963 , Kaikki_hotelli: 752817.981636324, teksti:'Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 2,2 prosenttia viime vuodesta ja heille tilastoitiin 2,42 miljoonaa yöpymisvuorokautta heinäkuussa 2020. Pääkaupunkiseudulla kotimaiset yöpymiset kuitenkin vähenivät 41,2 prosenttia mutta pääkaupunkiseudun ulkopuolella ne lisääntyivät 4,3 prosenttia edellisvuodesta. Koronavirustilanteesta johtuen ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 82,3 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,14 miljoonaa', kuukausi:'Heinäkuu 2020'},
  {id: 'i32' , year:  '2020-08-31' , Kotimaiset_mokki: 273887.785223817, Ulkomaiset_mokki: 9580.39997253816 , Kaikki_mokki: 279019.871014485 , Kotimaiset_majoitus: 964583.725721695 , Ulkomaiset_majoitus: 71554.6023865033 , Kaikki_majoitus: 1031393.94539181 , Kotimaiset_hotelli: 705841.560489489 , Ulkomaiset_hotelli: 71554.6023865033 , Kaikki_hotelli: 715735.972405683, teksti:'Yöpymisiä suurimpien kotimaisten kauppapaikkojen ja välityspalvelujen kohteissa oli kolmanneksen aikana arviolta 1 112 000. Näistä kotimaisia yöpymisiä oli 1 062 000 ja ulkomaisia yöpymisiä 50 000. Eniten vuokramökkiyöpymisiä oli heinäkuussa, jolloin vuokramökkiyöpymisiä oli 477 000. Heinäkuun yöpymisistä kotimaisia oli 460 000 ja ulkomaisia 17 000. Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 6,9 prosenttia viime vuodesta ja heille tilastoitiin 1,51 miljoonaa yöpymisvuorokautta elokuussa 2020. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät ainoastaan 0,6%, kun pääkaupunkiseudulla laskua kertyi 36,9% edellisvuoden elokuusta. Koronavirustilanteesta johtuen ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 81,6 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,14 miljoonaa. ', kuukausi:'Elokuu 2020'},
  {id: 'i33' , year:  '2020-09-30' , Kotimaiset_mokki: 235597.517785576, Ulkomaiset_mokki: 7483.85706540072 , Kaikki_mokki: 297346.295052317 , Kotimaiset_majoitus: 966808.580815499 , Ulkomaiset_majoitus: 190860.061929288 , Kaikki_majoitus: 1102774.33560973 , Kotimaiset_hotelli: 719252.170996225 , Ulkomaiset_hotelli: 190860.061929288 , Kaikki_hotelli: 755391.00882343, teksti:'Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 12,3 prosenttia viime vuodesta ja heille tilastoitiin 1,15 miljoonaa yöpymisvuorokautta syyskuussa 2020. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 5,4 prosenttia, kun pääkaupunkiseudulla laskua kertyi 42,8 prosenttia edellisvuoden syyskuusta. Koronavirustilanteesta johtuen ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 83,1 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,09 miljoonaa. ', kuukausi:'Syyskuu 2020'},
  {id: 'i34' , year:  '2020-10-31' , Kotimaiset_mokki: 247616.054640097, Ulkomaiset_mokki: 6222.03407476946 , Kaikki_mokki: 322167.700162167 , Kotimaiset_majoitus: 981987.337616083 , Ulkomaiset_majoitus: 188308.263859378 , Kaikki_majoitus: 1125902.41900931 , Kotimaiset_hotelli: 737146.709566777 , Ulkomaiset_hotelli: 188308.263859378 , Kaikki_hotelli: 775145.592455066, teksti:'Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 14,8 prosenttia viime vuodesta ja heille tilastoitiin 1,05 miljoonaa yöpymisvuorokautta lokakuussa 2020. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 3,6 prosenttia, kun pääkaupunkiseudulla laskua kertyi 50,2 prosenttia edellisvuodesta. Koronavirustilanteesta johtuen ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 81,7 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 0,08 miljoonaa.', kuukausi:'Lokakuu 2020'},
  {id: 'i35' , year:  '2020-11-30' , Kotimaiset_mokki: 247538.570533162, Ulkomaiset_mokki: 5111.61565864334 , Kaikki_mokki: 228107.031824232 , Kotimaiset_majoitus: 948974.804760034 , Ulkomaiset_majoitus: 163497.320312234 , Kaikki_majoitus: 1049713.43822509 , Kotimaiset_hotelli: 728629.49067246 , Ulkomaiset_hotelli: 163497.320312234 , Kaikki_hotelli: 781977.318200372, teksti:'Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 34,3 prosenttia viime vuodesta ja heille tilastoitiin 0,73 miljoonaa yöpymisvuorokautta marraskuussa 2020. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,4 prosenttia, kun pääkaupunkiseudulla laskua kertyi 59,1 prosenttia edellisvuodesta. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 80,9 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 87 000.', kuukausi:'Marraskuu 2020'},
  {id: 'i36' , year:  '2020-12-31' , Kotimaiset_mokki: 253057.417846254, Ulkomaiset_mokki: 3850.00276670864 , Kaikki_mokki: 244636.515104395 , Kotimaiset_majoitus: 1005326.56825529 , Ulkomaiset_majoitus: -164207.464767788 , Kaikki_majoitus: 943858.139181385 , Kotimaiset_hotelli: 777668.388076228 , Ulkomaiset_hotelli: -164207.464767788 , Kaikki_hotelli: 772560.932872013, teksti:'Vuokramökkiyöpymiset nousivat kolme prosenttia vuonna 2020, kotimaiset yöpymiset kasvoivat 18 prosenttia. Vuoteen 2019 verrattuna yöpymisten kokonaismäärä nousi noin 90 000 yöpymisellä eli noin kolme prosenttia. Kotimaiset yöpymiset nousivat noin 390 000 yöpymisellä 18 prosenttia ja ulkomaiset yöpymiset laskivat noin 300 000 yöpymisellä 53 prosenttia. Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 29,4 prosenttia viime vuodesta ja heille tilastoitiin 0,7 miljoonaa yöpymisvuorokautta joulukuussa 2020. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 21,3 prosenttia, kun pääkaupunkiseudulla laskua kertyi 57,5 prosenttia edellisvuodesta. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 91,8 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 60 000. ', kuukausi:'Joulukuu 2020'},
  {id: 'i37' , year:  '2021-01-31' , Kotimaiset_mokki: 221476.105269715, Ulkomaiset_mokki: 2922.77626580988 , Kaikki_mokki: 235947.850660991 , Kotimaiset_majoitus: 1031430.03966838 , Ulkomaiset_majoitus: -102114.11723806 , Kaikki_majoitus: 993676.02474658 , Kotimaiset_hotelli: 797699.614290849 , Ulkomaiset_hotelli: -102114.11723806 , Kaikki_hotelli: 827331.747629707, teksti:'Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 34,3 prosenttia viime vuodesta ja heille tilastoitiin 0,63 miljoonaa yöpymisvuorokautta tammikuussa 2021. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,7 prosenttia, kun pääkaupunkiseudulla laskua kertyi 60,4 prosenttia edellisvuodesta. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 92,7 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 50 000.', kuukausi:'Tammikuu 2021'},
  {id: 'i38' , year:  '2021-02-28' , Kotimaiset_mokki: 237563.157383922, Ulkomaiset_mokki: 2531.9914688864 , Kaikki_mokki: 249064.495250972 , Kotimaiset_majoitus: 1048859.1383308 , Ulkomaiset_majoitus: -50832.729438796 , Kaikki_majoitus: 1027441.17612677 , Kotimaiset_hotelli: 827646.787181697 , Ulkomaiset_hotelli: -50832.729438796 , Kaikki_hotelli: 886284.788990759, teksti:'Kotimaisten matkailijoiden yöpymiset vähenivät Suomen majoitusliikkeissä 32,1 prosenttia viime vuodesta ja heille tilastoitiin 0,84 miljoonaa yöpymisvuorokautta helmikuussa 2021. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset vähenivät 25,4 prosenttia, kun pääkaupunkiseudulla laskua kertyi 58,5 prosenttia edellisvuodesta. Ulkomaisten matkailijoiden yöpymiset vähenivät edellisvuodesta 90,3 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 55 000.', kuukausi:'Helmikuu 2021'},
  {id: 'i39' , year:  '2021-03-31' , Kotimaiset_mokki: 293855.153313703, Ulkomaiset_mokki: 2351.77509333701 , Kaikki_mokki: 261394.236082652 , Kotimaiset_majoitus: 1182833.11114898 , Ulkomaiset_majoitus: -23512.4186170811 , Kaikki_majoitus: 1233389.74508567 , Kotimaiset_hotelli: 844614.652494822 , Ulkomaiset_hotelli: -23512.4186170811 , Kaikki_hotelli: 917130.739501621, teksti:'Edellisvuoden huhtikuussa koettu poikkeuksellisen suuri majoituskysynnän romahtaminen näkyy nyt korkeina kasvulukuina. Kuitenkin vuoden 2019 huhtikuun kotimaisten yöpymisten tasoon verrattaessa oltiin huhtikuussa 41 prosenttia alemmalla tasolla. Pääkaupunkiseudun ulkopuolella kotimaiset yöpymiset olivat 35 prosenttia ja pääkaupunkiseudulla 64 prosenttia alemmalla tasolla kuin huhtikuussa 2019. Ulkomaisten matkailijoiden yöpymiset vähenivät vuoden 2019 huhtikuusta 85 prosenttia ja ulkomaalaisille yöpymisiä tilastoitiin 52 000.', kuukausi:'Maaliskuu 2021'},
  {id: 'i40' , year:  '2021-04-30' , Kotimaiset_mokki: 246275.8776229, Ulkomaiset_mokki: 15546.2691385566 , Kaikki_mokki: 259244.025541975 , Kotimaiset_majoitus: 1332307.74939238 , Ulkomaiset_majoitus: 201890.791382121 , Kaikki_majoitus: 1486881.91092882 , Kotimaiset_hotelli: 887697.111528615 , Ulkomaiset_hotelli: 201890.791382121 , Kaikki_hotelli: 994794.183570181, teksti:'Vuoden 2021 tammi-huhtikuun yöpymisten kokonaismäärä nousi noin 337 000 yöpymisellä eli noin 42 prosenttia vuoteen 2020 verrattuna. Kotimaiset yöpymiset nousivat noin 522 000 yöpymisellä 87 prosenttia ja ulkomaiset yöpymiset laskivat noin 185 000 yöpymisellä 95 prosenttia.', kuukausi:'Huhtikuu 2021'},

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
})
var dataFilter = data.map(function(d){return {time: d.year, value: d.Kotimaiset_majoitus, teksti:d.teksti, kuukausi:d.kuukausi} });
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
        .call(d3.axisLeft(y).ticks(6).tickFormat(function(d) { return parseInt(d)}))
        .append("text")
        .attr("class", "axis-title")
        .attr("transform", "rotate(-90)")
        .attr("y", -90)
        .attr("x", -250)
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
      //  .on("mouseover.teksti", function() {	
        //d3.select(this).transition()
         
          //.attr('opacity', '.85');
        //div.transition()
          //.duration(1)
          //.style("opacity", 1);
        //div.html("<p>  Tällä päivällä tapahtui tälläistä"+ Date.d.time+"</p>")	
        //div.html("<p>  Tällä päivällä tapahtui tälläistä</p>")	
          //.style("left", (d3.event.pageX) + "px")		
        //.style("top", (d3.event.pageY - 28) + "px");	
       //})
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
            .html(selectedData.kuukausi + "<br> Yöpymisiä:" + Math.round(selectedData.value)+  "<br>" + selectedData.teksti)
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
        dataFilter = data.map(function(d){return {time: d.year, value:d[selectedGroup], teksti:d.teksti, kuukausi:d.kuukausi} })
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

### Mökkihöperöt-joukkue
* Ville Keränen
* Matti Kokkonen
* Heidi Pukkila
* Sofia Pitkänen

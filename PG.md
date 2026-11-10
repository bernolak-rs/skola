# Prednaska 5
Kontrast - aky siroky je histogram farieb obrazu
Dynamicky rozsah vnasa do obrazu vacsiu skalu hodnot
Dynamicky rozsah sa da menit pomocou gama korekcie (gama funkcie)

Filtracia sluzi na odstranenie sumu

Pri gaussovom filtrovani pri okrajoch sa okraje bud ignoruju alebo sa robi interpolacia (vezme okraje z druhej strany)

3sigma = r
pre sigma = 1 sa r = 3 a dame 2 x r + 1 = 7, cize konvolucna matica je 7x7

Kroky:
- zmensenie obrazu pre znizenie rozlisenia
- gama korekcia - nelinearna uprava jasu a kontrastu
- filtracia
- adaptivne prahovanie
- binarizacia
- detekciu hran
- vektorizacia hran
- planarna homografia
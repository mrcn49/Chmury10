# Chmury10

### Spis treści
[Stworzenie sieci mostkowej](https://github.com/mrcn49/Chmury10/edit/main/README.md#stworzenie-sieci-mostkowej) <br>
[Utworzenie katalogów przechowujących logi kontenerów](https://github.com/mrcn49/Chmury10/edit/main/README.md#utworzenie-katalog%C3%B3w-przechowuj%C4%85cych-logi-kontener%C3%B3w) <br>
[Uruchamianie kontenerów](https://github.com/mrcn49/Chmury10/edit/main/README.md#utworzenie-katalog%C3%B3w-przechowuj%C4%85cych-logi-kontener%C3%B3w)  <br>
[Sprawdzenie poprawności działania aplikacji](https://github.com/mrcn49/Chmury10/edit/main/README.md#sprawdzenie-poprawno%C5%9Bci-dzia%C5%82ania-aplikacji)

Plik index.html: [index.html](https://github.com/mrcn49/Chmury10/blob/main/Laboratorium10/index.html)

## Stworzenie sieci mostkowej
![image](https://github.com/mrcn49/Chmury10/assets/106474992/bd245705-49f7-43ce-8b15-6d0ea5303b07) <br> 
Polecenie `docker network create --driver=bridge --subnet=10.0.10.0/24 lab10net` tworzy nową sieć Docker `lab10net` z użyciem sterownika `bridge` i zakresem adresów IP `10.0.10.0/24`. 

Wynik `e53cf62494fa771a0d425bdeb68d83f5dcb38435998045d2bbf969cb2ec1a6c2` to ID tej sieci.

## Utworzenie katalogów przechowujących logi kontenerów
![image](https://github.com/mrcn49/Chmury10/assets/106474992/8e36d406-b5df-4c08-8771-2681e05d72b4) <br> 
Polecenie `mkdir -p lab10/web1_logs | mkdir -p lab10/web2_logs | mkdir -p lab10/web3_logs` tworzy trzy katalogi: 
- web1_logs 
- web2_logs
- web3_logs

w katalogu lab10.  Parametr `-p` sprawia, że mkdir tworzy również wszystkie nieistniejące katalogi nadrzędne. Na przykład, jeśli katalog lab10 nie istnieje, zostanie utworzony

## Uruchamianie kontenerów

### Uruchomienie pierwszego kontenera wraz z wolumenem
![image](https://github.com/mrcn49/Chmury10/assets/106474992/963116b0-57e6-4fdc-b74d-296c3cd1ac6a) <br> 

Polecenie `docker run -dt --rm --name web1 --network lab10net --ip 10.0.10.10 -p 8081:80 -v $(pwd)/lab10/web1_logs:/var/log/nginx -v $(pwd)/index.html:/usr/share/nginx/html/index.html:ro nginx:latest` uruchamia kontener Docker z obrazu `nginx:latest` o nazwie `web1`.

Opcje są następujące:
- `-dt` uruchamia kontener w tle (`d`) i przydziela pseudo-TTY (`t`).
- `--rm` automatycznie usuwa kontener po zakończeniu.
- `--network lab10net` podłącza kontener do sieci `lab10net`.
- `--ip 10.0.10.10` przydziela kontenerowi adres IP `10.0.10.10` w sieci `lab10net`.
- `-p 8081:80` mapuje port 8081 hosta na port 80
- `-v $(pwd)/lab10/web1_logs:/var/log/nginx` montuje katalog `web1_logs` z bieżącego katalogu roboczego hosta jako `/var/log/nginx` w kontenerze.
- `-v $(pwd)/index.html:/usr/share/nginx/html/index.html:ro` montuje plik `index.html` z bieżącego katalogu roboczego hosta jako `/usr/share/nginx/html/index.html` w kontenerze, tylko do odczytu (`ro`).

Wynik `2b6a556536c66259ba2a8ad9c9f7a2baae2237f154ee239bc6666467254ac47d` to unikalny identyfikator (ID) uruchomionego kontenera.

#
### Uruchomienie drugiego kontenera ze sklonowanym wolumenem z pierwszego kontenera
![image](https://github.com/mrcn49/Chmury10/assets/106474992/ec72b466-b767-4433-b0a2-e051a62a0aaf) <br>

Polecenie `docker run -dt --rm --name web2 --network lab10net --ip 10.0.10.11 -p 8082:80 --volumes-from web1 -v $(pwd)/lab10/web2_logs:/var/log/nginx nginx:latest` uruchamia kontener Docker z obrazu `nginx:latest` o nazwie `web2`.

Opcje są następujące:
- `-dt` uruchamia kontener w tle (`d`) i przydziela pseudo-TTY (`t`).
- `--rm` automatycznie usuwa kontener po zakończeniu.
- `--name web2` nadaje kontenerowi nazwę `web2`.
- `--network lab10net` podłącza kontener do sieci `lab10net`.
- `--ip 10.0.10.11` przydziela kontenerowi adres IP `10.0.10.11` w sieci `lab10net`.
- `-p 8082:80` mapuje port 8082 hosta na port 80
- `--volumes-from web1` kopiuje wszystkie woluminy z kontenera `web1` do nowego kontenera `web2`.
- `-v $(pwd)/lab10/web2_logs:/var/log/nginx` montuje katalog `web2_logs` z bieżącego katalogu roboczego hosta jako `/var/log/nginx` w kontenerze.

Wynik `75c2d92bf30f7064751784349e28f942e029e770f789639bb1fd8d1edaa6a281` to unikalny identyfikator (ID) uruchomionego kontenera.
#
### Uruchomienie trzeciego kontenera ze sklonowanym wolumenem z pierwszego kontenera
![image](https://github.com/mrcn49/Chmury10/assets/106474992/34f3c2a7-a080-4edd-9d52-3f7db23cddfa) <br>

Polecenie `docker run -dt --rm --name web3 --network lab10net --ip 10.0.10.12 -p 8083:80 --volumes-from web1 -v $(pwd)/lab10/web3_logs:/var/log/nginx nginx:latest` uruchamia kontener Docker z obrazu `nginx:latest` o nazwie `web3`.

Opcje są następujące:
- `-dt` uruchamia kontener w tle (`d`) i przydziela pseudo-TTY (`t`).
- `--rm` automatycznie usuwa kontener po zakończeniu.
- `--name web3` nadaje kontenerowi nazwę `web3`.
- `--network lab10net` podłącza kontener do sieci `lab10net`.
- `--ip 10.0.10.12` przydziela kontenerowi adres IP `10.0.10.12` w sieci `lab10net`.
- `-p 8083:80` mapuje port 8083 hosta na port 80
- `--volumes-from web1` kopiuje wszystkie woluminy z kontenera `web1` do nowego kontenera `web3`.
- `-v $(pwd)/lab10/web3_logs:/var/log/nginx` montuje katalog `web3_logs` z bieżącego katalogu roboczego hosta jako `/var/log/nginx` w kontenerze.

Wynik `0c1d214fbdf2ab5d98a71939a89dbd1a27d42aba7dbe90eeab0a62d645316a0b` to unikalny identyfikator (ID) uruchomionego kontenera.

## Sprawdzenie poprawności działania aplikacji

### Za pomocą polecenia curl
![image](https://github.com/mrcn49/Chmury10/assets/106474992/f0e5063c-071d-4d8d-a136-070082ae745d)

#
### Sprawdzanie zawartości logów kontenerów
Kontener **web1**: <br>
![image](https://github.com/mrcn49/Chmury10/assets/106474992/ecdfeba0-b164-4e3f-8224-0413af44501d) <br>
Kontener **web2**: <br>
![image](https://github.com/mrcn49/Chmury10/assets/106474992/e2ac822a-ac28-4e18-ab33-1be407351cd3) <br>
Kontener **web3**: <br>
![image](https://github.com/mrcn49/Chmury10/assets/106474992/46ba5899-5fbf-4813-94f3-8551a8944336) <br>

#
### Sprawdzenie poprawności uprawnień dostępu 
Wolumen **index.html** ma wartość **RW** ustawioną na **false**, co za tym idzie wolumen ten ma dostęp **read-only**.

Wolumen przechowujący logi ma wartość **RW** ustawioną na **true**, ponieważ musimy zapewnić możliwość edycji tego pliku
- web1
  
  ![image](https://github.com/mrcn49/Chmury10/assets/106474992/86234305-0203-4ace-8617-51a5f451b74b)

- web2

  ![image](https://github.com/mrcn49/Chmury10/assets/106474992/3a46ff78-ddf6-4f72-acc7-90e91016c6e2)

- web3

  ![image](https://github.com/mrcn49/Chmury10/assets/106474992/132de065-d6f2-4fe7-ade7-f5b8e191f787)

#
### Sprawdzenie połączenia kontenerów do sieci
![image](https://github.com/mrcn49/Chmury10/assets/106474992/41550aa6-9394-4d0f-871b-e23d981f4025)




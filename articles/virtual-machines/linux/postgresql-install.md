---
title: Konfigurowanie PostgreSQL na maszynie wirtualnej z systemem Linux
description: Dowiedz się, jak zainstalować i skonfigurować PostgreSQL na maszynie wirtualnej z systemem Linux na platformie Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: bbfad994de663881e3aa03292fc0d0611a0d0933
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747800"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Instalowanie i konfigurowanie rozwiązania PostgreSQL na platformie Azure
PostgreSQL to zaawansowana baza danych open source podobna do Oracle i DB2. Obejmuje ona funkcje gotowe dla przedsiębiorstwa, takie jak Pełna zgodność ze standardami KWASowymi, niezawodne przetwarzanie transakcyjne i wielostronicowa kontrola współbieżności. Obsługuje ona również standardy, takie jak ANSI SQL i SQL/MED (w tym obce otoki danych dla systemów Oracle, MySQL, MongoDB i wielu innych). Jest wysoce rozszerzalny z obsługą ponad 12 języków proceduralnych, indeksów ĄTEK i dzienników, obsługi danych przestrzennych i wielu funkcji podobnej do NoSQL dla aplikacji opartych na kluczach JSON lub wartościowych.

Ten artykuł zawiera informacje na temat instalowania i konfigurowania PostgreSQL na maszynie wirtualnej platformy Azure z systemem Linux.


## <a name="install-postgresql"></a>Zainstaluj PostgreSQL
> [!NOTE]
> Aby ukończyć ten samouczek, musisz mieć już maszynę wirtualną platformy Azure z systemem Linux. Aby utworzyć i skonfigurować maszynę wirtualną z systemem Linux przed kontynuowaniem, zobacz [Samouczek dotyczący maszyny wirtualnej z systemem Linux](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

W takim przypadku należy użyć portu 1999 jako portu PostgreSQL.  

Nawiąż połączenie z maszyną wirtualną z systemem Linux utworzoną za pomocą wykorzystanych funkcji. Jeśli korzystasz z maszyny wirtualnej z systemem Linux na platformie Azure po raz pierwszy, zobacz [jak używać protokołu SSH z systemem Linux na platformie Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , aby dowiedzieć się, jak korzystać z funkcji tworzenia połączenia z maszyną wirtualną z systemem Linux.

1. Uruchom następujące polecenie, aby przełączyć się do katalogu głównego (administratora):
   
        # sudo su -
2. Niektóre dystrybucje mają zależności, które należy zainstalować przed zainstalowaniem PostgreSQL. Sprawdź dystrybucji na tej liście i uruchom odpowiednie polecenie:
   
   * Red Hat Base Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian podstawowy system Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Pobierz PostgreSQL do katalogu głównego, a następnie Rozpakuj pakiet:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Powyższy kod jest przykładem. Bardziej szczegółowy adres do pobrania można znaleźć w [indeksie/pub/Source/](https://ftp.postgresql.org/pub/source/).
4. Aby rozpocząć kompilację, uruchom następujące polecenia:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Jeśli chcesz skompilować wszystko, co można skompilować, łącznie z dokumentacją (strony HTML i Man) i dodatkowymi modułami (`contrib`), uruchom następujące polecenie zamiast:
   
        # gmake install-world
   
    Powinien zostać wyświetlony następujący komunikat potwierdzający:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurowanie PostgreSQL
1. Obowiązkowe Utwórz link symboliczny, aby skrócić odwołanie PostgreSQL do nieuwzględnienia numeru wersji:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Utwórz katalog dla bazy danych:
   
        # mkdir -p /opt/pgsql_data
3. Utwórz użytkownika innego niż root i zmodyfikuj profil tego użytkownika. Następnie przejdź do nowego użytkownika (o nazwie *Postgres* w naszym przykładzie):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Ze względów bezpieczeństwa PostgreSQL używa użytkownika niebędącego elementem głównym do inicjowania, uruchamiania lub wyłączania bazy danych.
   > 
   > 
4. Edytuj plik *bash_profile* , wprowadzając poniższe polecenia. Te wiersze zostaną dodane na końcu pliku *bash_profile* :
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Wykonaj plik *bash_profile* :
   
        $ source .bash_profile
6. Sprawdź poprawność instalacji przy użyciu następującego polecenia:
   
        $ which psql
   
    Jeśli instalacja zakończy się pomyślnie, zostanie wyświetlona następująca odpowiedź:
   
        /opt/pgsql/bin/psql
7. Możesz również sprawdzić wersję PostgreSQL:
   
        $ psql -V

8. Zainicjuj bazę danych:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Powinny pojawić się następujące dane wyjściowe:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurowanie PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Uruchom następujące polecenia:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Zmodyfikuj dwie zmienne w pliku/etc/init.d/PostgreSQL. Prefiks jest ustawiany na ścieżkę instalacji PostgreSQL: **/opt/pgsql**. PGDATA jest ustawiona na ścieżkę magazynu danych PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Zmień plik w taki sposób, aby był on wykonywalny:

    # chmod +x /etc/init.d/postgresql

Rozpocznij PostgreSQL:

    # /etc/init.d/postgresql start

Sprawdź, czy punkt końcowy elementu PostgreSQL jest włączony:

    # netstat -tunlp|grep 1999

Powinny zostać wyświetlone następujące dane wyjściowe:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Łączenie z bazą danych Postgres
Ponownie Przełącz do Postgres użytkownika:

    # su - postgres

Utwórz bazę danych Postgres:

    $ createdb events

Połącz się z utworzoną bazą danych zdarzeń:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Tworzenie i usuwanie tabeli Postgres
Teraz, gdy masz połączenie z bazą danych, możesz utworzyć w niej tabele.

Na przykład utwórz nową przykładową tabelę Postgres za pomocą następującego polecenia:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Teraz można skonfigurować tabelę z czterema kolumnami z następującymi nazwami kolumn i ograniczeniami:

1. Wartość kolumny "name" została ograniczona przez polecenie VARCHAR, która nie może mieć więcej niż 20 znaków.
2. Kolumna "żywność" wskazuje element żywności, który zostanie przeniesieny przez każdą osobę. VARCHAR ogranicza ten tekst do mniej niż 30 znaków.
3. Kolumna "potwierdzone" rejestruje, czy osoba ma RSVP do zatwierdzenia. Dopuszczalne wartości to "Y" i "N".
4. Kolumna "date" jest wyświetlana po zarejestrowaniu się na potrzeby zdarzenia. Postgres wymaga, aby daty były zapisywane jako rrrr-mm-dd.

Jeśli tabela została pomyślnie utworzona, powinny zostać wyświetlone następujące elementy:

![image](./media/postgresql-install/no4.png)

Możesz również sprawdzić strukturę tabeli przy użyciu następującego polecenia:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Dodawanie danych do tabeli
Najpierw Wstaw informacje do wiersza:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Powinny być widoczne następujące dane wyjściowe:

![image](./media/postgresql-install/no6.png)

Możesz również dodać kilka więcej osób do tabeli. Poniżej przedstawiono niektóre opcje lub można utworzyć własne:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Pokaż tabele
Użyj następującego polecenia, aby wyświetlić tabelę:

    select * from potluck;

Dane wyjściowe wyglądają następująco:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Usuwanie danych w tabeli
Użyj następującego polecenia, aby usunąć dane w tabeli:

    delete from potluck where name=’John’;

Spowoduje to usunięcie wszystkich informacji w wierszu "Jan". Dane wyjściowe wyglądają następująco:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualizowanie danych w tabeli
Użyj następującego polecenia, aby zaktualizować dane w tabeli. W takim przypadku Piaskowobrązowy potwierdził, że uczestniczą, więc zmienimy wartość RSVP z "N" na "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Uzyskaj więcej informacji na temat PostgreSQL
Po zakończeniu instalacji programu PostgreSQL na maszynie wirtualnej z systemem Linux na platformie Azure możesz korzystać z niego na platformie Azure. Aby dowiedzieć się więcej na temat PostgreSQL, odwiedź [witrynę sieci Web PostgreSQL](https://www.postgresql.org/).


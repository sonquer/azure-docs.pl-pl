---
title: Konfigurowanie aplikacji PHP
description: Dowiedz się, jak skonfigurować wstępnie zbudowany kontener PHP dla swojej aplikacji. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: a3de4769193d95a3ef483924c4d65c4fa1cc9f8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671835"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Konfigurowanie aplikacji PHP w systemie Linux dla Azure App Service

W tym przewodniku pokazano, jak skonfigurować wbudowane środowisko uruchomieniowe PHP dla aplikacji sieci Web, zaplecza mobilnego i API Apps w Azure App Service.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla deweloperów języka PHP, którzy używają wbudowanego kontenera systemu Linux w App Service. Jeśli nie korzystasz z Azure App Service, najpierw postępuj zgodnie [z samouczkiem dotyczącym](tutorial-php-mysql-app.md) języka [php](quickstart-php.md) i środowiska php z programem MySQL.

## <a name="show-php-version"></a>Pokaż wersję języka PHP

Aby wyświetlić bieżącą wersję PHP, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Aby wyświetlić wszystkie obsługiwane wersje języka PHP, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Ustaw wersję języka PHP

Uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić wersję PHP na 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Uruchom układacz

Domyślnie kudu nie uruchamia [układacz](https://getcomposer.org/). Aby włączyć automatyzację kompozytora podczas wdrażania kudu, należy podać [niestandardowy skrypt wdrożenia](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

W oknie lokalnego terminalu Zmień katalog na katalog główny repozytorium. Postępuj zgodnie z [instrukcjami instalacji wiersza polecenia](https://getcomposer.org/download/) , aby pobrać *Composer. PHAR*.

Uruchom następujące polecenia:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Katalog główny repozytorium ma teraz dwa nowe pliki oprócz *Composer. PHAR*: *. Deployment* i *Deploy.sh*. Te pliki działają zarówno w przypadku systemów Windows, jak i Linux App Service.

Otwórz *Deploy.sh* i znajdź sekcję `Deployment`. Zastąp całą sekcję następującym kodem:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Zatwierdź wszystkie zmiany i ponownie Wdróż swój kod. Układacz powinien teraz działać w ramach automatyzacji wdrażania.

## <a name="customize-start-up"></a>Dostosowywanie uruchamiania

Domyślnie wbudowany kontener PHP uruchamia serwer Apache. Na początku działa `apache2ctl -D FOREGROUND"`. Jeśli chcesz, możesz uruchomić inne polecenie podczas uruchamiania, uruchamiając następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W App Service można [ustawić ustawienia aplikacji](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) poza kodem aplikacji. Następnie możesz uzyskać do nich dostęp przy użyciu standardowego wzorca [getenv ()](https://secure.php.net/manual/function.getenv.php) . Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `DB_HOST`, użyj następującego kodu:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Zmień katalog główny witryny

Wybrana platforma sieci Web może używać podkatalogu jako katalogu głównego witryny. Na przykład [platformy laravel](https://laravel.com/)używa podkatalogu `public/` jako katalogu głównego witryny.

Domyślny obraz PHP dla App Service korzysta z platformy Apache i nie pozwala na dostosowywanie katalogu głównego witryny dla aplikacji. Aby obejść to ograniczenie, należy dodać plik *. htaccess* do katalogu głównego repozytorium o następującej zawartości:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Jeśli nie chcesz używać ponownego zapisywania pliku *.htaccess*, zamiast tego możesz wdrożyć aplikację platformy Laravel przy użyciu [niestandardowego obrazu platformy Docker](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. Na platformie [CodeIgniter](https://codeigniter.com/) element [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) domyślnie sprawdza wartość `X_FORWARDED_PROTO`.

## <a name="customize-phpini-settings"></a>Dostosuj ustawienia PHP. ini

Jeśli konieczne jest wprowadzenie zmian w instalacji PHP, można zmienić dowolne z [dyrektyw php. ini](https://www.php.net/manual/ini.list.php) , wykonując następujące kroki.

> [!NOTE]
> Najlepszym sposobem wyświetlenia wersji języka PHP i bieżącej konfiguracji *php. ini* jest wywołanie [phpinfo ()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Dostosowywanie — dyrektywy inne niż PHP_INI_SYSTEM

Aby dostosować dyrektywy PHP_INI_USER, PHP_INI_PERDIR i PHP_INI_ALL (zobacz [dyrektywy php. ini](https://www.php.net/manual/ini.list.php)), Dodaj plik *. htaccess* do katalogu głównego aplikacji.

W pliku *. htaccess* Dodaj dyrektywy przy użyciu składni `php_value <directive-name> <value>`. Na przykład:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Ponownie Wdróż aplikację ze zmianami, a następnie uruchom ją. Jeśli zostanie wdrożony za pomocą kudu (na przykład przy użyciu usługi [git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), zostanie on automatycznie uruchomiony ponownie po wdrożeniu.

Jako alternatywę dla korzystania z *. htaccess*można użyć [ini_set ()](https://www.php.net/manual/function.ini-set.php) w aplikacji, aby dostosować te dyrektywy niePHP_INI_SYSTEMowe.

### <a name="customize-php_ini_system-directives"></a>Dostosuj dyrektywy PHP_INI_SYSTEM

Aby dostosować dyrektywy PHP_INI_SYSTEM (zobacz sekcję [php. ini dyrektywy](https://www.php.net/manual/ini.list.php)), nie można użyć metody *. htaccess* . App Service zapewnia oddzielny mechanizm przy użyciu ustawienia aplikacji `PHP_INI_SCAN_DIR`.

Najpierw uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby dodać ustawienie aplikacji o nazwie `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` jest domyślnym katalogiem, w którym znajduje się *plik php. ini* . `/home/site/ini` jest katalogiem niestandardowym, w którym dodasz niestandardowy plik *. ini* . Wartości są oddzielane za pomocą `:`.

Przejdź do sesji protokołu SSH sieci Web przy użyciu kontenera systemu Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Utwórz katalog w `/home/site` o nazwie `ini`, a następnie utwórz plik *. ini* w katalogu `/home/site/ini` (na przykład *Settings. ini)* z dyrektywami, które chcesz dostosować. Użyj tej samej składni, która będzie używana w pliku *php. ini* . 

> [!TIP]
> W wbudowanych kontenerach systemu Linux w App Service */Home* jest używany jako utrwalony magazyn udostępniony. 
>

Na przykład aby zmienić wartość [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) Uruchom następujące polecenia:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aplikację.

## <a name="enable-php-extensions"></a>Włącz rozszerzenia PHP

Wbudowane instalacje języka PHP zawierają najczęściej używane rozszerzenia. Dodatkowe rozszerzenia można włączyć w taki sam sposób, w jaki [dostosowuje się dyrektywy php. ini](#customize-php_ini_system-directives).

> [!NOTE]
> Najlepszym sposobem wyświetlenia wersji języka PHP i bieżącej konfiguracji *php. ini* jest wywołanie [phpinfo ()](https://php.net/manual/function.phpinfo.php) w aplikacji.
>

Aby włączyć dodatkowe rozszerzenia, wykonaj następujące czynności:

Dodaj katalog `bin` do katalogu głównego aplikacji i umieść w nim pliki rozszerzeń `.so` (na przykład *MongoDB.so*). Upewnij się, że rozszerzenia są zgodne z wersją języka PHP na platformie Azure i są zgodne z VC9 i niebezpiecznym wątkem (nkty przerwania).

Wdróż zmiany.

Wykonaj kroki opisane w sekcji [dostosowywanie PHP_INI_SYSTEM dyrektyw](#customize-php_ini_system-directives), Dodaj rozszerzenia do pliku Custom *. ini* z [rozszerzeniem](https://www.php.net/manual/ini.core.php#ini.extension) lub [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) dyrektyw.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Aby zmiany zaczęły obowiązywać, należy ponownie uruchomić aplikację.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otwórz sesję SSH w przeglądarce

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Gdy działająca aplikacja PHP działa inaczej w App Service lub zawiera błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dzienników](#access-diagnostic-logs).
- Przetestuj aplikację lokalnie w trybie produkcyjnym. App Service uruchamia aplikacje Node. js w trybie produkcyjnym, dlatego należy się upewnić, że projekt działa zgodnie z oczekiwaniami w trybie produkcyjnym lokalnie. Na przykład:
    - W zależności od pliku *Composer. JSON*można zainstalować różne pakiety dla trybu produkcyjnego (`require` a `require-dev`).
    - Niektóre platformy sieci Web mogą wdrażać pliki statyczne inaczej w trybie produkcyjnym.
    - Niektóre platformy sieci Web mogą używać niestandardowych skryptów uruchamiania podczas pracy w trybie produkcyjnym.
- Uruchom aplikację w App Service w trybie debugowania. Na przykład w [platformy laravel](https://meanjs.org/)można skonfigurować aplikację do wyprowadzania komunikatów debugowania w środowisku produkcyjnym, [ustawiając ustawienie aplikacji `APP_DEBUG` na `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

W dziennikach kontenerów może zostać wyświetlony następujący komunikat:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Możesz bezpiecznie zignorować ten komunikat. `/robots933456.txt` to fikcyjna ścieżka URL, której App Service używa do sprawdzenia, czy kontener może obsługiwać żądania. Odpowiedź na 404 wskazuje na to, że ścieżka nie istnieje, ale pozwala App Service wiedzieć, że kontener jest w dobrej kondycji i jest gotowy do reagowania na żądania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja PHP z bazą danych MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux — często zadawane pytania](app-service-linux-faq.md)

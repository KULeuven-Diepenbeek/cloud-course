---

title: "Host je Laravel project in een live omgeving"
weight: 10
author: Arne Duyver
draft: false
------------

### Introductie

In dit deel van de cursus leer je hoe je een **Laravel applicatie lokaal live kan hosten** als een echte webserver.
We doen dit met behulp van **NGINX**, **PHP**, en **MySQL**. Daarnaast bekijken we kort het nut van een **reverse proxy** en maken we de website **publiek toegankelijk** via een gratis **DuckDNS-domeinnaam**.

## Vereisten

Installeer de volgende software op je computer:

| Software                                                                                         | Beschrijving                            |
| ------------------------------------------------------------------------------------------------ | --------------------------------------- |
| [PHP](https://www.php.net/downloads.php) (min. 8.1)                                              | Nodig om Laravel uit te voeren          |
| [Composer](https://getcomposer.org/download/)                                                    | Laravel's dependency manager            |
| [MySQL](https://dev.mysql.com/downloads/mysql/) of MariaDB                                       | Database                                |
| [NGINX](https://nginx.org/en/download.html) of [XAMPP](https://www.apachefriends.org/index.html) | Webserver (Apache in XAMPP of NGINX)    |
| [Git](https://git-scm.com/downloads)                                                             | Om je project te clonen                 |

> 💡 **Tip (Windows-gebruikers)**: Als je al **XAMPP** hebt geïnstalleerd, gebruik dan de ingebouwde Apache-server in plaats van NGINX. Plaats je Laravel-project in de map `C:\xampp\htdocs\mijnproject` en open het in je browser via `http://localhost/mijnproject/public`. Voor meer controle en performantie raden we echter aan om later over te schakelen naar NGINX.

## Laravel project opzetten

Clone een bestaand project of maak een nieuw aan en test of alles werkt met de ingebouwde PHP-server:

```bash
$ php artisan serve
```

Je zou de applicatie nu moeten zien op [http://localhost:8000](http://localhost:8000).

## NGINX installeren en configureren

### Installatie

```bash
# Ubuntu/Debian
$ sudo apt install nginx

# macOS (met Homebrew)
$ brew install nginx
```

Start de service:

```bash
$ sudo service nginx start
```

Controleer of NGINX draait door te surfen naar [http://localhost](http://localhost).
Je zou een “Welcome to NGINX” pagina moeten zien.

### 3.2 Laravel configureren met NGINX

Maak een nieuw configuratiebestand aan in `/etc/nginx/sites-available/laravel`:

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/myapp/public;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Activeer de site:

```bash
$ sudo ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/
$ sudo nginx -t
$ sudo service nginx reload
```

Je Laravel app draait nu via **NGINX** op [http://localhost](http://localhost).

**PHP-FPM (PHP FastCGI Process Manager)** is een speciaal onderdeel van PHP dat instaat voor het efficiënt uitvoeren van PHP-code op een webserver. In plaats van bij elke inkomende webrequest een nieuwe PHP-interpreter te starten (wat traag en inefficiënt is), houdt PHP-FPM een pool van processen actief die klaarstaan om PHP-scripts uit te voeren. Wanneer een webserver zoals NGINX een verzoek ontvangt naar een .php-bestand, stuurt hij dat via het FastCGI-protocol door naar PHP-FPM, dat het script verwerkt en het resultaat (meestal HTML) terugstuurt. Dit zorgt voor snellere laadtijden, betere schaalbaarheid en stabielere prestaties bij drukbezochte websites. Bovendien laat PHP-FPM toe om meerdere pools met verschillende configuraties te beheren — handig als je meerdere webapps met verschillende PHP-instellingen op één server wilt draaien.

```bash
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-mbstring php8.2-pdo php8.2-mysql
sudo apt install php8.2-fpm
sudo service php8.2-fpm start
sudo nginx -t
sudo service nginx reload
sudo service php8.2-fpm restart
sudo service nginx restart
```

#### Wat doet dit configuratiebestand precies?

- `listen 80;`: Geeft aan dat NGINX inkomend verkeer op poort **80 (HTTP)** moet behandelen.
- `server_name localhost;`: Definieert voor welke domeinnaam deze configuratie bedoeld is. Je kunt dit later vervangen door bijvoorbeeld `mijnlaravel.duckdns.org`.
- `root /var/www/myapp/public;`: Geeft aan waar de **rootmap** van de website zich bevindt. In Laravel is dit **altijd de `public`-map**, omdat hierin het `index.php`-bestand staat dat alle requests afhandelt.
- `index index.php index.html;`: Definieert welke bestanden als standaardindex worden gebruikt.
- `location / { ... }`: Zorgt ervoor dat alle inkomende requests gecontroleerd worden.
- Met `try_files $uri $uri/ /index.php?$query_string;` probeert NGINX eerst te zien of een bestand of map bestaat. Als die niet bestaan, stuurt het de request door naar `index.php`, die dan via Laravel verder verwerkt wordt.
- `location ~ \.php$ { ... }`: Dit blok zorgt ervoor dat PHP-bestanden niet rechtstreeks getoond worden, maar worden uitgevoerd via **PHP-FPM** (FastCGI Process Manager).
- De lijn `fastcgi_pass unix:/run/php/php8.2-fpm.sock;`: verwijst naar het socketbestand waar PHP-FPM op luistert.
- `location ~ /\.ht { deny all; }`: Blokkeert alle `.htaccess` bestanden voor extra veiligheid. Deze zijn overbodig bij NGINX (ze worden enkel door Apache gebruikt).

**Samengevat:**<br/>
Deze configuratie vertelt NGINX **waar Laravel zich bevindt**, **hoe het PHP-bestanden moet uitvoeren** en **hoe requests moeten worden doorgegeven aan Laravel's router**.

---

## Reverse Proxy

Een **reverse proxy** is een NGINX- of Apache-server die **verkeer verdeelt of doorstuurt naar andere servers of applicaties**.
Bijvoorbeeld, je kunt één publieke server hebben met meerdere Laravel-projecten, en de proxy beslist welk project getoond wordt afhankelijk van de URL:

| URL                           | Doel                                     |
| ----------------------------- | ---------------------------------------- |
| `api.mijnlaravel.duckdns.org` | stuurt verkeer door naar je Laravel API  |
| `app.mijnlaravel.duckdns.org` | stuurt verkeer door naar je frontend-app |

Voordelen:
- Biedt **één toegangspunt** voor meerdere applicaties.
- Maakt **SSL-beveiliging en caching** eenvoudiger.
*- Verbergt interne netwerken of poorten van je systeem.

Je hoeft dit lokaal meestal niet in te stellen, tenzij je met meerdere projecten werkt of wil testen hoe load balancing werkt.

---

## Je app publiek toegankelijk maken

Om je Laravel-app te tonen buiten je eigen netwerk moet je:

1. **Je publiek IP-adres vinden:**
   Bezoek [https://whatismyipaddress.com](https://whatismyipaddress.com).

2. **Poorten openzetten op je router:**
   Log in op je router (meestal via `192.168.0.1` of `192.168.1.1`)

   - Open **poort 80 (HTTP)**
   - Open eventueel **poort 443 (HTTPS)**

3. **Poorten toestaan op je computer (firewall):**

   * **Windows (XAMPP):** zorg dat Apache poort 80 en 443 mag gebruiken. Dit kan in XAMPP via de Control Panel -> Config -> Service and Port Settings.
   * **Linux:**

     ```bash
     $ sudo ufw allow 80
     $ sudo ufw allow 443
     ```

---

## Gratis domeinnaam met DuckDNS

[DuckDNS](https://www.duckdns.org) biedt gratis _dynamische_ DNS aan.
Dit betekent dat jouw domein (zoals `mijnlaravel.duckdns.org`) altijd wijst naar jouw publieke IP, zelfs als dat IP wijzigt.

1. Maak een account aan: Ga naar [https://www.duckdns.org](https://www.duckdns.org) en log in met GitHub of Google.

2. Maak een subdomein: Bijvoorbeeld: `mijnlaravel`

Je krijgt dan een domein als:

```
mijnlaravel.duckdns.org
```

3. Update je IP automatisch

Voeg een cronjob toe (Linux/macOS):

```bash
*/5 * * * * curl -k "https://www.duckdns.org/update?domains=mijnlaravel&token=<YOUR_TOKEN>&ip="
```

---

## SSL-certificaat (optioneel, aanbevolen)

Een SSL-certificaat (Secure Sockets Layer) is een digitaal certificaat dat de verbinding tussen jouw webserver en de browser van een gebruiker encrypteert. Hierdoor kunnen gegevens zoals wachtwoorden of betaalinformatie niet worden onderschept.

**Verschil tussen HTTP en HTTPS:**
- HTTP (HyperText Transfer Protocol): verzendt gegevens onversleuteld, wat betekent dat iemand met toegang tot het netwerk de communicatie kan inzien.
- HTTP**S** (HyperText Transfer Protocol Secure): gebruikt een SSL/TLS-certificaat om de verbinding te beveiligen en te authenticeren. Dit zorgt voor vertrouwelijkheid en integriteit van de gegevens. _(HTTPS = HTTP + beveiliging via SSL/TLS.)_

Je kan applicaties zoals [Certbot](https://certbot.eff.org/) gebruiken om gratis **HTTPS** te activeren. Via NGINX reverse proxy kan je dit ook zeer makkelijk per domain aanvragen.

## Met Docker

Je kan je Laravel dus ook hosten met nginx in een docker container. Het voordeel hiervan is ook dat je simpel meerdere applicaties kan hosten op 1 device door port 80,443 gewoon te pipen naar andere poorten van je host device. Via een reverse-proxy (die je ook kan draaien in een docker container, maar deze poorten moeten wel dezelfde poorten behouden) kan je dan verschillende domain names naar de verschillende applicaties laten verwijzen. Dit is dus een simpele manier om meerdere applicaties te hosten op 1 device.
---
explains: Hoe kunnen de Docker images van onze projecten worden gebruikt
---

# Docker

## Zaakregistratiecomponent en Alfresco

Deze set van Docker images kan gebruikt worden voor demonstratie doeleinden en door ontwikkelaars. De images zijn **niet** geoptimaliseerd voor gebruik in productie. Op dit moment zijn Docker images beschikbaar voor versie 0.9.7 van de zaakregistratie component.

Voor het gebruik van deze images zijn [Docker](https://www.docker.com/community-edition "Docker") en Docker-compose benodigd.

### Docker compose

Maak in een lege directory het bestand *docker-compose.yml* met deze inhoud aan:

```

version: '3'
services:
  zr-db:
    image: dockerhaarlem/zaakregistratie-db:1.0      
    cap_add:
      - SYS_ADMIN
    security_opt:
      - seccomp:unconfined
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    ports:
      - "5432:5432"
  alfresco:
    image: dockerhaarlem/alfresco:1.0      
    cap_add:
      - SYS_ADMIN
    security_opt:
      - seccomp:unconfined
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    ports:
      - "8080:8080"
  zr:
    image: dockerhaarlem/zaakregistratiecomponent:1.1      
    cap_add:
      - SYS_ADMIN
    security_opt:
      - seccomp:unconfined
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    ports:
      - "80:80"     

```

Voer vanuit deze directory vervolgens het commando *docker-compose up -d*  commando uit:

```

PS C:\dev\docker\compose> docker-compose up -d
Creating network "compose_default" with the default driver
Creating compose_zr-db_1    ... done
Creating compose_alfresco_1 ... done
Creating compose_zr_1       ... done

```

Door het *docker ps* commando uit te voeren is te zien welke containers er gestart zijn:

```

PS C:\dev\docker\compose> docker ps
CONTAINER ID        IMAGE                                        COMMAND                  CREATED             STATUS              PORTS                    NAMES
57779d416916        dockerhaarlem/alfresco:1.0                   "/entry.sh"              2 hours ago         Up About an hour    0.0.0.0:8080->8080/tcp   compose_alfresco_1
dece676333e1        dockerhaarlem/zaakregistratiecomponent:1.1   "/bin/sh -c /usr/sbi…"   2 hours ago         Up About an hour    0.0.0.0:80->80/tcp       compose_zr_1
77a3c09f5dd4        dockerhaarlem/zaakregistratie-db:1.0         "/usr/sbin/init"         2 hours ago         Up About an hour    0.0.0.0:5432->5432/tcp   compose_zr-db_1

```

- **compose_zr_1**: De zaakregistratiecomponent
- **compose_alfresco_1**: Alfresco
- **compose_zr-db_1**: De zaakregistratiecomponent database

#### Docker compose commando's
De set van containers kan worden beheerd door vanuit de directory met het docker-compose.yml bestand docker-compose commando's uit te voeren.

- **docker-compose stop**: Stop de containers maar laat de data in de databases bestaan
- **docker-compose start**: Start de containers op nadat deze gestopt waren
- **docker-compose restart**: Herstart draaiende containers
- **docker-compose down**: Stopt en verwijderd de containers, de data in de databases raakt verloren.

### Zaakregistratiecomponent
De beheer-omgeving van de zaakregistratie component is nu te benaderen op: [http://localhost/admin/](http://localhost/admin/ "Admin"). 

Log in met gebruikersnaam **admin** en wachtwoord **demo1234**.

### Alfresco
Alfresco is te benaderen op [http://localhost:8080/share](http://localhost:8080/share).

Het opstarten van de Alfresco container duurt langer. Het kan dan ook zijn dat de inlog pagina eerst nog een timeout geeft, ververs dan de pagina totdat het inlog scherm in beeld komt. 

Log in met gebruikersnaam **Admin** en wachtwoord **password**.

Zodra Alfresco opgestart is, is de zaakregistratie component klaar voor gebruik en kunnen alle services gebruikt worden.

### Zaakregistratiecomponent database
De RSGB 2.0 compliant database van de zaakregistratiecomponent is geïmplementeerd in PostgreSQL en kan benaderd worden op poort 5432. Owner van de database is gebruiker **zaakmagazijn** met wachtwoord **zaakmagzijn**

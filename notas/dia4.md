Hemos de tratar de garantizar que si el playbook no hace nada, que no haya tareas con estado changed!


URL: http://            miservidor: 80          /index.html
                                                /carpeta1/menchu.html
                                                /.git/y mis mierdas
     protocolo          dominio     puerto      path

APACHE:80???
El apache responde con lo que haya en el path asociado a ese dominio / puerto
    Eso es lo que configuramos en el virtual host:
        dominio / puerto
    Y ese dominio / puerto (virtualhost) tiene asociado un Document ROOT???
        ROOT? La carpeta raiz... que en POSIX se define como:  /
        
        Entonces, apache devuelve lo que hay en DOCUMENT_ROOT/.git/y mis mierdas!!!
        Entonces, apache devuelve lo que hay en DOCUMENT_ROOT/index.html
        Entonces, apache devuelve lo que hay en DOCUMENT_ROOT/carpeta1/menchu.html













---

Virtual host
0- Setup:
    Leyendo los facts
        custom facts


1. Mirar si ya existe la capreta del virtualhost (document root)
4. Clonamos repo en una carpeta de despliegues: /var/apache/despliegues
    Cuando clono el repo, si el repo ya existe en la misma versión que dice el modulo? ok
    Cuando clono el repo, si el repo ya existe en otra versión que dice el modulo? changed
    Cuando clono el repo, si el repo no existe que dice el modulo? changed
        Y en cualquier caso, el repo queda en la verisón que yo he pedido. IDEMPOTENCIA
#1.5 Leer el Commit ID anterior
#        - Puedo leerlo del ficherito de texto
2. Si la tarea de antes acaba con un CommitID distinto al que acabamos de leer (el del play anterior)
    - Crear carpeta de backups
    - Backup document root ( si existe)
    - Borramos la caperta document root
3. Crear la carpeta del document root
4. Si la tarea de antes acaba con un CommitID distinto al que acabamos de leer (el del play anterior)
    - copiar lo que tengo en la carpeta de despliegues (sin el directorio .git) al document_root   
    - Guardar el nuevo CommitID
        - Puedo guardarlo en un ficherito de texto
        - Guardarlo en CUSTOM FACT de Ansible en mi host
           /etc/ansible/facts.d/{{virtualhost}}.fact
5. Borro la carpeta del /var/apache/despliegues...


6. Copiar el fichero de la configurción en /tmp
   Y no marco:
        changed_when: False
7. Aplicar la plantilla del virtual host
    Cuando aplico la plantilla y la escribo en un fichero... si ese fichero no existe, 
        qué dice el modulo? changed
    Cuando aplico la plantilla y la escribo en un fichero... si ese fichero ya existe y tiene
        un contenido distinto, qué dice el modulo? changed
    Cuando aplico la plantilla y la escribo en un fichero... si ese fichero ya existe y tiene
        el mismo contenido, qué dice el modulo? ok
8. Si la tarea de antes provoca un changed, muevo el fichero de tmp a /backups
    changed!!!  




Los custom facts de Ansible no tienen nada que ver con lo que hace ese modulito del SET_FACT

Un custom fact es un fichero que guardo en la carpeta: /etc/ansible/facts.d de cada host.
Con el nombre que me venga en gana.
Con extensión:
    .yaml
        asdf:   1
        bar:    2
    .json
        {
            "asdf": 1,
            "bar": 2
        }
    .fact
        asdf=1
        bar=2

También puedo guardarlo como fichero con permisos de ejecución...
Entonces ansible lo ejecuta a nivel de shell (y en la shell se mirará el shebang)
    #/usr/local/bin/python3
    
    #/bin/bash

Y ansible captura la STDOUT de ese procgrama al ejecutarlo..

El valor que devuelva el programa (En JSON) o el contenido de los dicheros de ahi arriba es lo que se pone como valor
de una variable (que se guarda dentro de la variable custom_facts) con el nombre del fichero.

    /etc/ansible/facts.d/federico.yaml
                                        asdf:   1
                                        bar:    2

    custom_facts.federico.asdf ---> 1
    custom_facts.federico.bar ---> 2
    
    custom_facts.federico 
        asdf:   1
        bar:    2




/etc/ansible/facts.d/{{virtualhost}}.fact
 ID DEL COMMIT
























---

# Git??

En git, tenemos un REPO... y en ese REPO guardamos COMMITS
    
                        Esto es un commit: en GIT
                        vvv
Qué es un commit? Un snapshot.. backup COMPLETO del estado del proyecto en un momento del tiempo
                        II
                  El conjunto de cambios que se han metido en el proyecto en un momento del tiempo 
                        ^^^^
                        Esto es un commit: Si trabajase con CSV o con subversión
    El commit tiene asociado:
        - ID??? HASH (huella) del contenido de ese ZIP
                        
Qué es lo único que yo necesito tener guardado de la jugada (PLAY) anterior para 
saber si ha cambiado algo en la app??? EL ID DEL COMMIT
    
                        
----

Algoritmo de HUELLA(HASH)

- función que al recibir un valor devuelve otro, de forma que:
    - El valor devuelto siempre es el mismo para el mismo valor de entrada
    - Hay muy pocas probaiblidades(% colisión) de que 2 valores de entrada diferentes generen el mismo valor de salida
         4%
            MD5
            SHA-1024 ( 1 entre trillones % de colisión )
    - El dato de salida se considera un RESUMEN del dato de entrada:
        - Partiendo del dato de salida es imposible REGENERAR el dato de entrada.

Algoritmo más sencillo que podeís haber usado de tipo hash es la letra del DNI

    23.000.001 T?
    
    23.000.000 | 23
               +------------
             1   1.000.000
             |
             v
             Estará entre 0-22. En nuestro caso: Resto 1: Letra: R
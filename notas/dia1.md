
# Ansible

Ansible no es un programa ni una herramienta, es una familia de productos de software, para la automatización de tareas.

Hay muchos programas/servicios que forman parte de esta familia:
- Ansible project: Programa opensource que nos permite ejecutar playbooks
  - Ansible engine: Motor de ejecución de playbooks opensorce pero no gratuito
- Ansible Galaxy: Repositorio de roles, junto con un programa par su gestión (ansible-galaxy)
- AWX: Orquestador de playbooks opensource
- Redhat Automation platform (Ansible tower): Orquestador de playbooks...

---

# Ansible project (Ansible engine)

Programa que permite la ejecución de playbooks ... contra una selección de un inventario.
Ese inventario, como se define?
- .ini .... pa' un ejercicio de clase... bien
- .yaml ... misma mierda.. con alguna ventaja y alguna desventaja
- formato híbrido: .ini + .yaml             BASTANTE BIEN!
- generarlos dinamicamente por programas... que saquen los inventarios de un cmdb, de un cloud...


$ ansible-playbook -i inventario playbook.yml --limit servidor1 --tags "tag1,tag2"

## Playbook

Colección de plays

### Play

Script con tareas... igual que los que hacemos con python o con powershell... IGUAL... o casi...
Diferencia??? En Ansible se habla (o eso nos venden... aunque no lo tienen muy claro) lenguaje DECLARATIVO.
Realmente Ansible habla lenguaje DECLARATIVO? NO LO HABLA... mentira!!! Habla lenguaje IMPERATIVO... aunque las tareas que se definen en los plays son declarativas.
Toda tarea en Ansible es ejecutada por un MODULO... Los módulos son los que hablan lenguaje DECLARATIVO...
o al menos se intenta... ya que queda a la buena voluntad del desarrollador del MODULO...
La comunidad SUELE RESPETAR ESTO!!! Pero no siempre...

### Welcome to the programming world:

Os suenan los PARADIGMAS DE PROGRAMACIÓN???
- Lenguaje imperativo: IF; FOR, FOR-EAF, WHILE, DO-WHILE, SWITCH, GOTO
- Lenguaje procedural
- Lenguaje funcional
- Lenguaje Orientado a objetos
- Lenguaje Declarativo

FELIPE, IF hay algo que no sea una silla debajo de la ventana,
    FELIPE, lo quitas IMPERATIVO
FELIPE, IF(SI) no hay silla debajo de la ventana, entonces (THEN)
    IF not silla, GOTO IKEA y compra silla! IMPERATIVO
    FELIPE, pon una silla debajo de la ventana?     IMPERATIVO

Al usar el lenguaje imperativo, me centro en lo que quiero que haga FELIPE... perdiendo de vista mi objetivo (ese estado final del que habláis)

Cuando uso lenguaje declarativo, me centro en lo que quiero conseguir.... y delego en FELIPE la responsabilidad de conseguirlo.

FELIPE, DEBAJO DE LA VENTANA TIENE QUE HABER UNA SILLA! DECLARATIVO

Cuando creamos un programa (script) en python on powershell, que paradigma usamos principalmente? IMPERATIVO

Una consecuencia del lenguaje DECLARATIVO es la IDEMPOTENCIA.
Da igual el estado de partida, siempre llegaré al mismo estado final.
==> Da igual el número de veces que ejecute esto, siempre llegaré al mismo estado.

Al trabajar con Ansible y escribir un playbook... yo puedo tranquilamente ROMPER CON LA IDEMPOTENCIA... y hacer que mi playbook no sea idempotente.

```yaml

- name: Crear ventana
  hosts: all
  tasks:
    - name: Crear ventana
      file:
        path: /ventana
        owner: root
        group: root
        mode: '1777'
        state: directory
```

## Quién más habla lenguaje declarativo? Todas las herramientas que hoy en día lo petan, hablan lenguaje declarativo!!!

- TERRAFORM !
- Kubernetes
- Docker compose
- Azure devops
- Ansible
- Spring / JAVA
- Angular (Framework para JS)


Qué es eso de Ansible?
- Una herramienta para automatizar
- Herramienta que no necesita instalación de agentes (puppet, chef)
- Buena documentación / comunidad grande y activa
- Opensource
- Lenguaje basado en YAML / jinja2 / Python (Ps1)
- Idempotente
- Escalabilidad
- Redhat
    RHEL <----- Fedora
    JBOSS <---- wildfly
    OPENSHIFT CONTAINER PLATFORM <------ OKD (openshift origin)

---

YAML es un lenguaje de marcado de información, similar a (conceptualmente) JSON, XML, HTML.

YAML
 XML
HTML

YAML ain't Markup Language.

Hoy en día JSON es un subconjunto de YAML... YAML que está en versión 2.1 de la espec. engloba la especificación de JSON.


---

# Qué es DEV--->OPS?

Es una cultura, un movimiento, una filosofía en pro de la AUTOMATIZACION.

                Automatizable?                                      HERRAMIENTAS
PLAN                POCO
CODE                DIGAMOS QUE POCO 
BUILD               SI, TOTALMENTE                                      JAVA: MAVEN, GRADLE
    EMPAQUETAR UN CODIGO (compilarlo, juntar archivos, linkarlo)        JS: NPM, YARN, WEBPACK
                                                                        .net: NUGET, DOTNET, MSBUILD
                                                                        PYTHON: PIP, PYPI, SETUP.PY
-----> Si automatizo hasta aquí se llama: Hacer un desarrollo ágil
TEST
 Definición         DIGAMOS QUE POCO
 Ejecución          SI, TOTALMENTE                                      JUNIT, NUNIT, XUNIT
                                                                        JEST, MOCHA, CHAI
                                                                        SELENIUM
                                                                        READY API
                                                                        KATALON STUDIO
                                                                        KARMA
                                                                        SOAPUI
                                                                        POSTMAN
                                                                        KARATE
                                                                        SONARQUBE
--------> INTEGRACION CONTINUA.
Tengo continuamente la última versión EMPAQUETADA EN AUTOMATICO instalada en el entorno de INTEGRACION, estando sometida a pruebas automatizadas
RELEASE             SI, TOTALMENTE                                      
    Liberar / poner una versión en manos de mi cliente
        Subir una imagen de contenedor a un registry
        Subir una librería JAVA a un repo de artefactos (nexus)
        Dejar el app Android en el Google Play
        Dejar el app IOS en la App Store o en el App Store
--------> ENTREGA CONTINUA (Continuous Delivery)
DEPLOY              SI, TOTALMENTE
    Adquirir una infra: TERRAFORM, VAGRANT, CLOUDFORMATION
    Configurar una infra: ANSIBLE, CHEF, PUPPET
    Desplegar una app: ANSIBLE, DOCKER, KUBERNETES, OPENSHIFT
--------> DESPLIEGUE CONTINUA (Continuous Deployment)
OPERATION           SI, TOTALMENTE
    Kubernetes/Openshift/Tanzu,/Karbon de forma automatica
      Operan un entorno de producción mediante contenedores
    
MONITOR             SI, TOTALMENTE
    Monitorizar un entorno de producción
        Prometheus
        Grafana
        ELK
        ...
---------> He adoptado una cultura DEVOPS completa

Lo que hacemos al automatizar es MONTAR PROGRAMAS... 
- Con ansible (playbooks)
- Con terraform (srcipt)
- Con Maven
- Con selenium/Python (script de pruebas)
Pero alguien tiene que darles al PLAY... y no solo darle a PLAY... orquestarlos.
PRIMERO EMPAQUETAS, LUEGO Compras una infra, luego la planchas, luego instalkas
Luego ejecutas las pruebas... luego....
Quién le da al play? Un humano? Pues ya he jodido la automatización.
JENKINS, GITLAB CI/CD, TRAVIS, BAMBOO, CIRCLE CI, AZURE DEVOPS, TEAMCITY... GITHUB ACTIONS

Quién maneja estas herramientas? Quien configura estos programas?
- El desarrollador? No sabe de pruebas.. ni de sistemas
- El tester? No sabe de desarrollo ni de sistemas
- El sysadmin? Ese no sabe de desarrollo ni de pruebas

Necesito un tio nuevo.. que sepa un poquito de todo... y como le llamo, DEVOPS
----

# Qué es Automatizar?

Hacer/construir una máquina (o modificar el comportamiento de una ya existente mediante un programa) que haga lo que antes hacía un ser humano a mano.

Automatizamos el lavado de ropa... LAVADORA
Hoy en día automatizo?
- Empaquetado de un código
- Adquisición de una infra: TERRAFORM, VAGRANT, CLOUDFORMATION
- Configuración de una infra: ANSIBLE, CHEF, PUPPET


# Qué son los facts?

Datos de la máquina sobre la que ejecuto el playbook.
Qué tipo de datos se incluyen en el concepto de facts?
- Información de red
- Información de hardware
- Información del sistema operativo
- Información de ejecución (procesos que están corriendo)
- Información de usuarios
- Si la máquina tiene docker: información de los contenedores que están corriendo
- Custom Facts: Son datos que yo puedo dejar en la máquina, para que al ejecutar un playbook, pueda leerlos y obrar en consecuencia:
  La forma más sencilla de usarlos es mediante archivos que dejo en la carpeta /etc/ansible/facts.d/
  El fact se llamará como el archivo y el valor será el contenido del archivo. El formato del archivo puede ser cualquier cosa, pero lo más común es JSON.

  También podemos crear archivos cuya primera linea sea un SHEBANG !!!!! y el archivo tiene permisos de ejecución...
    #!/bin/bash
    #!/usr/bin/python
    #!/usr/bin/awk

  Ansible, al hacer el gather facts ejecuta el programa y su salida estandar es el valor del fact.


----

# Tareas en Ansible

Una tarea de ansible, al ejecutarse, puede acabar en estado:
- ok        La tarea se ha ejecutado correctamente y no ha provocado cambios en el sistema... o eso creemos!
            Quién cuenta eso? Quién dice si una tarea ha provocado o no cambios en un sistema? EL MODULO RESPONSABLE DE SU EJECUCION
            Y el módulo informará de lo que quiera.! Si está bien hecho... y puede... nos informará correctamente.
- changed   La tarea se ha ejecutado correctamente y ha provocado cambios en el sistema... o eso creemos!

            - name: "Tarea que muestra un mensaje en la consola"
              shell: 
                cmd: mi-programa.sh
            
            En qué estado acabaría esa tarea? ok... pero acabaría en CHANGED!
            Lo que pasa es que el módulo shell, ejecuta lo que tu pongas ahí... y el módulo no tiene forma de comprobar si la tarea ha provocado cambios en el sistema o no.
        
- failed
- skipped
- ignored

---

Control de flujo en nuestro script:

- Las tareas se ejecutan en orden secuencial:
  - Primero las del bloque pre_tasks
  - Luego las del bloque tasks
  - Luego las del bloque post_tasks
- Cuando acaba cada bloque, se evalúan los handlers.. a ver si alguno ha sido disparado... y entonces se ejecutan.
- Handlers... Un handler es disparado si:
  - Una tarea provoca CAMBIOS y tiene un notify 
  - En el notify puede:
    - Usar el nombre del handler (NO SE HACE.. feo)
    - Usar un nombre de evento (GUAY).
  - Los handlers pueden subscribirse a varios eventos... y se ejecutarían si alguno de esos eventos se dispara.
- Las tareas pueden agruparse en bloques: 
  - Tenemos un compartamiento tipo try-catch-finally
    - block, rescue, always
- Podemos ejecutar tareas condicionalmente:
  - when: "ansible_os_family == 'Debian'"
  - handlers
  - tags... no son dinamicos (se pasan en tiempo de ejecución.. pero de forma fija)
- Podemos influir en el estado de finalización de una tarea:
  - changed_when
  - failed_when
  - ignore_errors
- Bucles:
  - loop
  - (Antiguamente: with_XXXXXXXX)

---

Lo que definimos en el playbook... al final resulta que es lenguaje IMPERATIVO.
Ahora... hace ya muchos años, que nos dimos cuenta que montar un programa usando sólo lenguaje imperativo era un follón... Y quedaba un código INMANTENIBLE.
Y surgen nuevos paradigmas de programación: PROCEDURAL
Cuando el lenguaje me permite definir PROCEDIMIENTOS (métodos, rutinas, funciones, subrutinas...) que agrupen código IMPERATIVO...
Y posteriormente ejecutar esos procedimientos, entonces hablamos de que tenemos un lenguaje que soporta el paradigma PROCEDURAL.

Para qué quiero esto? El crear procedimientos:
- Re-usar código
- Mejorar la estructura del programa => Código más fácil de mantener.

Existe en Ansible el concepto de programación PROCEDURAL? SI EXISTE... y se llama ROLES.


Un ROL de ansible es básicamente lo que sería una función/procedimiento en otros lenguajes de programación.
Un ROL de ansible permite definir una serie de tareas... dentro... mediante programación IMPERATIVA... y luego poder ejecutarlo... una y otra vez... en el mismo o en diferentes playbooks.

Cuando creamos un procedimiento/función.... además del código que hace, definimos:
- Nombre
- Argumentos de entrada / parámetros
- Código
- Lo que devuelve / salida

Y eso mismo es lo que definimos en un ROL.

No queremos un playbook con 20 tareas... 30 tareas... eso es inmantenible.
Prefiero un playbook con 5 tareas (invocar a un role)... y tener 5 roles con 6 tareas cada uno.
ESO SI ES MANTENIBLE

Además, de paso, quizás en el futuro pueda reaprovechar esos roles para ejecutarlos en otros playbooks.

Cuando creamos roles, ANSIBLE-GALAXY, nos ofrece una estructura de carpetas predefinida, que todo el que trabaja con Ansible conoce.
De forma que cual sea la persona que venga en el futuro, entenderá rápidaemente la estructura de mis archivos.
---

Habitualmente la gente que se dedica al mundo de sistemas lo hace porque le gustan las computadoras... un montón... pero en un momento dado cató el tema de la programación... y no le moló nada.. o poco.

Sea como fuere, hoy en día a los sysadmin les han cambia'o el contrato... sin aviso previo. Ahora en su contrato pone: PROGRAMADORES
Sea como fuere, hoy en día a los testers les han cambia'o el contrato... sin aviso previo. Ahora en su contrato pone: PROGRAMADORES

Porque hoy en día, lo que queremos es AUTOMATIZAR TODO...
- lo de sistemas
- lo de pruebas

Y hemos dicho que automatizar es hacer programas.

Los desarrolladores, llevan décadas construyendo programas... Levamos más de 70-80 años haciendo programas. Algo hemos aprendido.
Y los syadmins y los testers... estaría bien que mirásemos un poco las buenas prácticas que usan los desarrolladores a la hora de construir programas.

    Una cosa que muchas veces ni los desarrolladores tienen interiorizado es que un programa POR DEFINICION, es un producto sujeto a mantenimientos y evoluciones.


            DESARROLLO  <->  PRUEBAS  ->  OK  ->  REFACTORIZACION  <->  PRUEBAS -> OK (libero)

            <------------------------------>      <---------------------------------> 
            Esto es sólo el 50% del trabajo         Esto es el otro 50% del trabajo
                    8 horas                                     8 horas












---

Lo que le pido a ansible no es que tenga el paquete Mariadb en versión latest... usando el módulo apt.
---
Lo que le pido a ansible es que EJECUTE (imperativo) el módulo latest...
Y que ese módulo le diga que quiero tener mariadb en su ultima versión (DECLARATIVO)
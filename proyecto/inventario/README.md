# Qué incluye un archivo de inventario?

Los potenciales host a los que vamos a conectar donde ejecutar las tareas(módulos) de nuestros playbooks.
Esos hosts, los tendremos habitualmente ORGANIZADOS en una estructura JERARQUICA de GRUPOS.
Además, podemos tener variables configuradas para los hosts... de forma que cuando ejecutemos un playbook en ese host, ansible inyecte las variables que se hayan definido en el host al entorno de ejecución del host en cuestión.
    NOTA:
        Cuando ejecutamos un playbooks, ese playbook se ejecuta de forma independiente en cada host. Y cada host tiene su propio scope de variables.(ambito de variables)
        - Al inyectar variables desde linea de comandos, o mediante un archivo, o dentro del playbook, esas variables se aplican a todos los hosts.
        - Al inyectar variables desde un archivo de inventario, esas variables se aplican a nivel de cada host.

## Formatos con los que definir un archivo de inventario:

Tenemos 3:

## .ini (RUINA !!!!!)

```ini
host_suelto_1 ansible_connection#=local
                                #=winrm
                                =ssh
              ansible_python_interpreter=/usr/bin/python3
# cuando ansible se conecte a ese host mediante ese protocolo... 
# a qué dirección de red se conectará?
# $ ssh IP:22 ???
              ansible_host= IP HOST | FQDN RED
              # Si no ponemos esto, ansible intentará conectarse al nombre con el que hayamos defionido el host: host_suelto_1 
              # En los playbooks puedo preguntar por la variable: ansible_host y por la variable: inventory_hostname
              # inventory_hostname: host_suelto_1
              # ansible_host: IP HOST | FQDN RED ... Si no he puesto nada, se usa inventory_hostname
              ansible_port=22 #  por defecto, si no lo pongo se usan los puertos estandar de:
                        # ssh: 22
                        # winrm: 5985
              ansible_user=usuario
              ansible_password=contraseña # TERMINANTEMENTE PROHIBIDO USAR SIN ENCRIPTAR
              # Podemos encriptarlo con ansible-vault
              # Si me conecto con claves ssh, que sería mucho mejor, no necesito poner la contraseña... pero necesito poner la clave:
             ansible_ssh_private_key_file=/ruta/a/clave/privada
              # Aún así, la forma recomendada es tener definidas las credenciales en Redhat Automation Platform... Ahí tenemos un bloquecito especial en el menú para las credenciales.
              # No vamos a querer que ansible conecte on cada host con un usuario diferenete y una clave diferente.
              # Tendremos un usuario ansible | o similar... que será el que se conecte a todos los hosts.
              ansible_become=True
              ansible_become_method=sudo
              ansible_become_user=root
              ansible_become_password=contraseña


host_suelto_2

[webservers]
webserverA
webserverB

[apache]
apache[01:17].webserver.com

[apache:vars]
webserver_port=83 # esta variable me la he inventao... mis playbooks podrán usarla.

[nginx]
nginx[01:17].webserver.com

[nginx:vars]
webserver_port=80

[webservers:children]
nginx
apache

[bbdds]

```

Estos ficheros tienen un par de problemitas grandes... o 3...
- Si tengo muchas variables en un host, las variables NO PUEDO partirlas en lineas.
  Tendré lineas gigantescas(la edición es una tortura).

    host_suelto_1 ansible_connection=ssh ansible_host= IP HOST | FQDN RED ansible_port=22 ansible_user=usuario ansible_password=contraseña ansible_ssh_private_key_file=/ruta/a/clave/privada ansible_become=True ansible_become_method=sudo ansible_become_user=root ansible_become_password=contraseña ansible_python_interpreter=/usr/bin/python3
    
- Lo que escribimos aquí es TEXTO!
  Qué pasa si una variable tiene un valor que tiene una estructura de datos compleja? Jerarquica? 

Aunque tiene cosas guays... en general son sencillos de escribir.

## Opción 2: YAML (PEOR QUE LA OTRA !!!!!!)

```yaml
all:
  hosts:
    host_suelto_1:
      ansible_connection: ssh
      ansible_host: IP HOST | FQDN RED
      ansible_port: 22
      ansible_user: usuario
      ansible_password: contraseña
      ansible_ssh_private_key_file: /ruta/a/clave/privada
      ansible_become: True
      ansible_become_method: sudo
      ansible_become_user: root
      ansible_become_password: contraseña
      ansible_python_interpreter: /usr/bin/python3
    host_suelto_2:
    webserverA:
    webserverB:
    apache[01:17].webserver.com:
    nginx[01:17].webserver.com:
  children:
    webservers:
      hosts:
        webserverA:
        webserverB:
      children:
        nginx:
          hosts:
            nginx[01:17].webserver.com:
        apache:
          hosts:
            apache[01:17].webserver.com:
      vars:
        webserver_port: 83
    bbdds:
```
Es un latazo de escribir... y de leer... y de editar... y de mantener... y de todo.
Tiene una gran ventaja:
- Las variables se manejan mejor
- Y encima, podemos ponerles tipos complejos

Hay que tomar una decisión: Opción 1 u Opción 2 ???? LA 3... las 2 de antes son RUINA !!!

## Opción 3: Ni un .ini... ni un .yaml... una CARPETA : GUAY !!!!

    inventario-produccion/
    |-group_vars
    |  |-apache.yml             <- Con variables en YAML ! TIO !!! GUAY !!!!
    |  |-nginx.yml
    |  |-webservers.yml
    |-host_vars
    |  |-host_suelto_1.yml
    |  |-host_suelto_2.yml
    |  
    |-inventario-webservers.ini
    |-inventario-bbdd.yaml
    |-inventario-clientes.py

Y los hosts definidos ahí se acumulan
    

Acabaremos con una cantidad de playbooks enorme.
Esos playbooks se desplegarán / ejecutarán en chorricientos sistemas...
    En cada uno tendremos unas variables
Vamos a tener un inventario... o muchos.
Credenciales
Quien? puede ejecutar qué? playbooks donde?

---

Vamos a trabajar con un Ansible Tower (Redhat Automation Platform) | AWX

---

Proyecto upstream de Ansible Tower

REDHAT
    
    CentOS < ----- RHEL < ------ Fedora
                                  ^^^
                                Nueva funcionalidad
                
---

Se instala mediante un Operador (de Kubernetes)

---

# Kubernetes?

Gestor de contenedores: FALSO
Quién es un gestior de contenedor? docker, podman, crio, containerd
Los gestores de contenedores me permiten: CREAR conenedores, BORRAR contenedores, ARRANCAR contenedores.

Hoy en día, la forma preferida de instalar cualquier software en un servidor es mediante CONTENEDORES.

Contenedor ~ Máquina virtual

# Contenedor?

Un entorno dentro de un Sistema Operativo con kernel LINUX donde ejecutar procesos de forma aislada
(al resto de procesos que corren en ese SO). Qué se aisla? Qué tiene un contenedor independiente?
- Su propia configuración de red -> su propia IP(s)
- Su propio sistema de archivos, independencie (más o menos) al del host
- Sus propias variables de entorno
- Puede tener limitaciones de acceso a los recursos de HW del hierro(host)

# Imagen de contenedor?

Una imagen de contenedor es un triste fichero comprimido (tar) que tiene dentro:
- Una estructura de carpetas compatible con POSIX (/bin/opt/var/home/...) HABITUALMENTE
- Un programa (o varios)... normalmente cientos de ellos PREINSTALADOS DE ANTEMANO
---

## Procedimientos de instalación de software

### Procedimiento tradicional

     App 1 + App 2 + App 3      Problemas grandes?
    ------------------------        - App 1 (bug): CPU -> 100%... App1 -> OFFLINE
        Sistema operativo                                         App2 -> OFFLINE
    ------------------------                                      App3 -> OFFLINE
            HIERRO                  - App1 tiene unos requerimientos (paquetería, configuración de SO) 
                                      incompatibles con App2
                                    - Potencialmente App1 puede acceder a todos los datos de App2

### Máquinas virtuales

      App1  |   App2 + App3
    ------------------------    Aunque resuelve los problemas de arriba viene con sus propios problemas
      SO 1  |      SO 2
    ------------------------    Problemas importantes:
      MV 1  |      MV 2             - Instalación / configuración se complica un huevo
    ------------------------        - Mantenimiento se coimplica un huevo
        Hipervisor                  - Merma en el rendimiento de las apps
    ------------------------        - Pérdida de recursos
        Sistema operativo          
    ------------------------
            HIERRO

### Contenedores

      App1  |   App2 + App3
    ------------------------    Resuelve los problemas (casi todos) de las Máquinas virtuales sin sus problemas
      C 1   |      C 2         
    ------------------------    
     Gestor de contenedores          
     docker, crio, podman...
    ------------------------    
    Sistema operativo Linux
    ------------------------
            HIERRO
            
        
---
Linux? Kernel de SO... 
Cuál es el kernel de SO más usado en el mundo? Linux
    1 -> Android
    
    
---

# Entorno de producción

- Alta disponibilidad   \ Cluster / Redundancia
- Escalabilidad         / Cluster / Escalabilidad

CLUSTER: Grupo de máquinas

    Apache1     <---
    Apache2     <---    BC  <---      Proxy Reverso     <----   Proxy  <-- Usuarios?

    ^^^
    Procesos instalados a hierro
    Procesos corriendo en MV
    Procesos corriendo en Contenedores
    
    
KUBERNETES (Openshift, Tanzu, Karbon, AKS, ...K8S, K3S, Minishift, OKD, Minikube)

Operador automatizado de entornos de producción (con software basado en contendores)


Cluster de Kubernetes

    NodoA
        crio
        los programas de kubernetes
    NodoB
        crio
        los programas de kubernetes
    NodoC
        crio
        los programas de kubernetes PLANO DE CONTROL DE KUBERNETES

    Nodo1:
        crio
            apache2
    Nodo2:
        crio
    Nodo3:
        crio
            apache1
    NodoN:
        crio

Kubernetes quiero tener en el entorno de producción entre 1 y 5 contenedores con apache...      DEPLOYMENT
Y quiero un balanceador de carga delante                                                        SERVICE
Y quiero un proxy reverso delante del balanceador                                               INGRESS CONTROLLER

Kubernetes más que un orquestador de contenedores, es un gestor de gestores de contenedores.


AWX Tower vienen preparados parea correr en KUBERNETES... con preferencia a hacerlo sobre Openshift

Una de las formas de instalar Software en Kubernetes es mediante OPERADORES ---> Después aplico ficheros de manifiesto YAML en Kubernetes para desplegar un tower.. o 17 towers.
                                                                 Archivos de manifiesto
                                                                 Charts de HELM
De hecho lo que nos da redhat es un Operador de RAP que lo que hace es desplegar en un cluster de kubernetes
la posibilidad de automatizadamente desplegar instalaciones de Ansible Tower.

Instalado el operador puedo comenzar a decirle a Kubernetes:
- Quiero un cluster de ansible tower dentro del entorno de producción, con 3 nodos
- Y ahora otro de 5 nodos



---

En Tower/AWX/AutomationPlatform lo que registramos son repositorios de GIT.

GIT es un sistema de control de versiones de software.

# Un mínimo del funcionamiento interno:

Git nos ofrece un repositorio... y en ese repo guaramos COMMITS.
- COMMIT: Una foto completa (un zip) del proyecto en un momento dado del tiempo.
Si eso es así, en que se diferencia GIT de un sistemas de copias de seguridad?
- RAMA:  Una linea de evolución en el tiempo de nuestro proyecto paralela a otras lineas de evolución.
  
Esto será en una linea de evolución: CS1(Foto1) -> Foto2 -> Foto3 -> Foto4
                                                     \
                                                    Foto2 -> Foto5 -> Foto6

Hemos de acogernos a un WORKFLOW de git. Básicamente el decidir qué ramas creamos , y donde vamos haciendo trabajo.

    PRINCIPAL: MASTER -> MAIN
        Hay una regla de oro con respecto a esta rama: Nunca, nadie, bajo ningún concepto puede hacer un commit en ella.
        Qué tenemos en ella? COMMITS.. pero esos commits... que incluyen? CODIGO APTO PARA PRODUCCION
        
    DESARROLLO: desa, dev, development: Para proyectos más livianos (como un playbook de ansible) en esta rama es donde solemos hacer los commits.
    

Estamos creando programas! (scripts)... hemos de intentar acogernos a las buenas prácticas que los desarrolladores de software
(que son la gente experta en crear software) han ido desarrollando.

    Los desarrolladores trabajan contra un entorno de DESARROLLO
                                    vvvv
                            Tenemos el entorno de PRE / PRUEBAS / TEST / Q&A / Integración
                                    vvvv
                           Y luego está el entorno de PRODUCCION


Tenemos 2 opciones:

            Desarrollo -> Producción
            (desarrollo)   (main)
            
            Desarrollo -> PRE       -> Producción
            (desarrollo)  (release)    (main)

De ansible Tower / AWX / RAP... debo tener al menos 2 entornos SIEMPRE.

            DESARROLLO -> Producción.
            Desarrollo -> PRE       -> Producción
            

Git es un sistema de control de versión DISTRIBUIDO: 
Cada persona tiene su propio repositorio local.... TOTALMENTE INDEPENDIENTE y normalmente diferentes a los de otras personas.
Un proyecto sería la SUMA de todos los repos de cada participante.

Con git trabajamos en LOCAL... y tenemos 3 cosas en local:
    
    WORKINGDIR                  STAGING             REPO
    (carpeta: archivos)         (carpeta)           BBDD (.git) donde guardamos RAMAS (Commits)
                                 oculta

# Operativa normal al trabajar con git:
    
    
            WORKING DIR                             STAGING                                 REPO
    IVANPC:
        c:\Usuarios\Ivan\proyecto1
            
            playbook.yaml (v4) ----> git add --->  playbook.yaml (v3) ---> git commit ----> Rama(main): C1 [playbook.yaml(v1) ]
            variables.yaml(v1) ---->               variables.yaml(v1)                                   C2 [playbook.yaml(v1), variables.yaml(v1)]
                                                                                                        C3 [playbook.yaml(v2), variables.yaml(v1)]
            
Yo estoy trabajhando en mis mierdas... en mi máquina.
Y ahora otra persona quier colaborar:
    
    
                                        master: C1->C2->C3->C4***
                                            |
                                        REPO REMOTO: URL: https://github.com/proyecto1
                                            |
                    Servidor de alojamiento de repositorios remotos: Gitlab, Github, Bitbucket
                                            |
    +---------------------------------------+-----------------------------------+--red
    |                                                                           |
    IvanPC                                                                   MenchuPC
    |- Workingdir                                                               |- Working dir
    |- Staging                                                                  |- Staging
    |- Repo:                                                                    |- Repo:
        master: C1-> C2 -> C3 -> C4                                                      origin/master: C1->C2->C3->C4
          V                         ^                                                       V
        origin/master: C1->C2->C3->C4 ***                                             master: C1->C2->C3->C4
    
    
    origin = https://github.com/proyecto1
    
    
    IvanPC -> push RAMA -> REMOTO
    Ivan < --- FETCH < --- REMOTO
    
Cómo se copia un commit de una rama a otra?             git merge
                                                        git rebase
                                                        
git pull = git fetch + [git merge | git rebase]


        
rama1                C1 ---> C2 ---> C3 -------------> C5
                                       \             /  merge FAST-FORWARD (copiar commit)
desa                                    C3 -> C4 -> C5


rama1                C1 ---> C2 ---> C3 -----> C6 -----> C7             merge RECURSIVO (git merge)
                                       \              /             
desa                                    C3 -> C4 -> C5              
    
                C7:  (C6 + (C5-C3))
                            diff = PATCH


C1: Inicio playbook
C2: Obtengo los facts e instalo paquetería
C3: Arreglar un error (Estaba sacando demasiados facts)
C4: Hacer copia de seguridad de los virtualhost
C5: Hacer copia seguridad del document root
C6: Handlers hay un problemilla... Hay que hacer un flush... se reinicia el apache 2 veces en algunas circustancias.
C7: ??? Junto lo de Hacer copia de seguridad del document root y de los virtual host con lo de los handlers ?? EIN???



rama1                C1 ---> C2 ---> C3 -----> C6                       rebase
                                                \         
        desa                                     C6 -> C4' -> C5'

Por donde empiezo???
- Playbook
- Roles... nos saldrán... Para eso existe la etapa de REFACTORIZACION

Por donde empiezo a crear cualquier programa en el mundo mundial! API
El API es la forma de comunicarme con un programa.

Nosotros estamos creando un programa...
Y debo saber cómo comunicarme con él!

Cómo me voy a comunicar con mi programa... para alterar/configurar su comportamiento?

$ ansible-playbook -i inventario.ini playbook.yml --extra-vars "@variables.yml" \
                                                  --tags "instalar,configurar"

Osea que ese comando me sirve para instalar un apache en 200 servidores diferentes con 200 configuraciones diferentes

## Archivo de variables

```yaml

apache:
    version: 2.4
    repos:
        - apt: "ppa:ondrej/apache2"
        - yum: "https://repos.fedorapeople.org/repos/jkaluza/httpd24/epel-httpd24.repo"
    virtual_hosts:
        - puerto: 80
          domain: "www.midominio.com"
          document_root: "/var/www/html"
          repo_git:
            url:
            usuario:
            password:
    dependencias:
        - name: git
          version: 2.30
          repos:
            - apt: "ppa:git-core/ppa"
            - yum: "https://repos.fedorapeople.org/repos/jkaluza/git/epel-git.repo"
```

## Tags

| Tag              | Descripción                                            |
|------------------|--------------------------------------------------------|
| instalacion      | Instala el apache                                      |
| pruebas          | Realiza pruebas de conexión                            |
| configuracion    | Configura el apache                                    |
| requisitos       | Asegura que el sistema es apto para instalar el apache |


---

Yo ahora me tengo que poner el gorro de desarrollador del playbook.
Otra cosa será QUIEN EJECUTE ESTE PLAYBOOK... que potencialmente y deseablemente no seré yo.

---

### Principios de desarrollo de software:

#### SoC: Separation of Concerns

Cuando estoy metido en un berenjenal... me centro en ese berenjenal.... y no me pongo a pensar en otras cosas.


---

Mi playbook tiene una funcionalidad.
Por ejemplo, mi playbook permite instalar Apache en:
- Ubuntu
- Fedora
Si vienes con un Debian... te vas como has venido. BUSCATE UN PLAYBOOK QUE SOPORTE FEDORA.


---

Ansible me define un lenguaje de programación... basado en YAML.

Me da un schema yaml para que yo pueda escribir mi playbook.
Y además.. tengo un monton de modulos con su propio lenguaje.


---

Meter más de un return en una función es una mala práctica.

function algo(){
  hago una cosa
  hago otra cosa
  si(tal cosa):
    hago otrea cosa mas
  sino si (otra cosa mas):
    me piro (return)
  sino 
    una ultima cosa
    me piro (return)
  otra coita que se me olvidaba
  me piro (return)
}

funcion algo() {
  if(pasaloquesea())
    return 0;
  else
    return 33;
}


funcion algo() {
  var elDatoQueVoyADevolver= null;
  if(pasaloquesea())
    elDatoQueVoyADevolver= 0;
  else
    elDatoQueVoyADevolver= 33;
  return elDatoQueVoyADevolver;
}

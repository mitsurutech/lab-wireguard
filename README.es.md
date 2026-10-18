# Laboratorio de WireGuard: un túnel VPN entre dos sedes, en una sola máquina virtual Ubuntu

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-wireguard/actions/workflows/check.yml)

Un **laboratorio de WireGuard** listo para usar en clase, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde los centros educativos dan a cada alumno sus propias máquinas y un terminal en el navegador. Cada alumno recibe su propia máquina virtual Ubuntu con dos pequeñas sedes construidas dentro, cada una con una pasarela y un equipo, unidas por un enlace de «internet» que no sabe nada de ninguna de las dos redes privadas. El alumno genera un **par de claves de WireGuard** en cada pasarela, escribe `wg0.conf`, levanta el **túnel VPN** y encamina las dos redes a través de él, hasta que un equipo de una sede puede hacer ping a un equipo de la otra. No se instala nada en el ordenador del alumno.

Una pasarela necesita una máquina propia y un alumno tiene una sola máquina virtual, así que cada equipo y cada pasarela es un **espacio de nombres de red** (network namespace) y cada enlace es un par de cables virtuales. Los comandos reales funcionan, que es lo importante: `wg genkey`, `wg pubkey`, `wg show`, `wg-quick`, `ip route`, `tcpdump`.

## El laboratorio

| Laboratorio | Archivo | Qué hace el alumno | Página de ayuda |
| --- | --- | --- | --- |
| Túnel WireGuard entre dos sedes | [`wireguard.lab.yaml`](wireguard.lab.yaml) | Crear un par de claves en cada pasarela, mantener privadas las claves privadas, escribir la configuración, levantar el túnel, encaminar las dos LAN a través de él, observar los paquetes cifrados en el enlace de internet y conseguir que todo sobreviva a un reinicio | [Laboratorio de WireGuard](https://mitsurutech.co.uk/es/help/lab-wireguard) |

El laboratorio deja un README en la carpeta personal del alumno que explica qué hacer y no da ninguna respuesta. Tiene una **comprobación**, un script que califica una máquina virtual entregada sin que el profesor tenga que revisarla: se ejecuta sobre una copia de la máquina guardada tras un arranque limpio, así que solo ve lo que el alumno guardó en `wg0.conf`, y solo se supera si el estado real es correcto: un handshake reciente en las dos pasarelas, el tráfico de un ping pasando por `wg0`, nada más que UDP en el enlace de internet y ninguna dirección privada visible en él, las claves privadas legibles solo por su dueño y ninguna ruta que envíe una red privada por el enlace de internet. Una simple ruta estática entre las dos redes no la supera.

## Qué hay en el archivo

`wireguard.lab.yaml` es una **definición de laboratorio de máquina virtual** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la máquina virtual de cada alumno: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es el terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la máquina virtual de cada alumno: instala `wireguard-tools` y `tcpdump` desde el archivo de Ubuntu, y escribe el comando `wglab` y el README del laboratorio |
| `check` | el script de calificación, que se ejecuta como root sobre una copia del disco guardado tras un arranque limpio; su estado de salida es el resultado y `0` significa superado |

El comando `wglab` que instala: `wglab status` lista las máquinas, `wglab shell gwA` abre un shell dentro de una pasarela, y `wglab down` y después `wglab up` reconstruye el laboratorio y vuelve a levantar el túnel a partir de los archivos guardados.

## Usarlo en la plataforma

**Para asignarlo como trabajo a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio de máquina virtual Ubuntu**.
2. Pulsa el ejemplo, o pega el contenido del archivo de aquí. Usar un ejemplo reemplaza el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada alumno tiene una máquina virtual propia, que se conserva entre sesiones.
4. Asígnala como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un alumno de la lista de la clase: arranca una copia de su máquina guardada que no se vuelve a guardar, y muestra si se superó y qué imprimió.

La [guía de laboratorios de máquina virtual Ubuntu](https://mitsurutech.co.uk/es/help/labs) del manual y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) cuentan el resto.

¿Todavía no tienes una cuenta para tu centro? [Tu primer año es gratis](https://mitsurutech.co.uk/es/pricing) hasta 50 alumnos, y [crear una cuenta](https://mitsurutech.co.uk/es/signup) solo requiere una dirección de correo electrónico.

## Comprobación del archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es un YAML válido con las cinco claves anteriores y que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma. No arranca ninguna máquina virtual: eso se hace en las propias máquinas de la plataforma.

## Qué no funciona en un laboratorio

La máquina virtual llega al archivo de paquetes de Ubuntu, a PyPI, a npm y a GitHub, y a nada más, así que un laboratorio no puede apuntar a un equipo en internet. Es una sola máquina virtual, por lo que las dos sedes se construyen dentro y el «internet» entre ellas es un enlace virtual.

## Acerca de

Creado por [Mitsuru Technologies](https://mitsurutech.co.uk) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para colegios y centros educativos, con laboratorios de contenedores, laboratorios de máquinas virtuales, aplicaciones web desplegadas desde GitHub y cuestionarios que se califican solos. Prueba código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin) y [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea).

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`.

[WireGuard](https://www.wireguard.com) está bajo licencia GPL-2.0 (las herramientas y el módulo del kernel de Ubuntu) y se instala desde el archivo de Ubuntu cuando arranca una máquina virtual; nada de él se distribuye aquí.

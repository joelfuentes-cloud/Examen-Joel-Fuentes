# Cluster Web con Balanceo de Carga y Proxy Inverso

Este proyecto implementa una solución de infraestructura web de alta disponibilidad utilizando **Docker Compose**. Se despliega un entorno compuesto por un **Proxy Inverso (Nginx)** que gestiona y distribuye el tráfico hacia dos servidores de **Backend**, garantizando aislamiento y escalabilidad.

---

## 🏗️ Análisis de la Infraestructura y Diseño

El diseño de este entorno ha sido concebido bajo principios de seguridad, eficiencia y alta disponibilidad. A continuación se detallan las decisiones técnicas clave:

### 1. Elección de la Imagen (Nginx)
Se ha seleccionado la imagen oficial de **Nginx** tanto para el Proxy como para los Backends por las siguientes razones:
* **Rendimiento:** Es extremadamente ligero y capaz de manejar miles de conexiones simultáneas con un consumo mínimo de RAM.
* **Versatilidad:** Permite funcionar como servidor de contenido estático (Backend) y como balanceador de carga (Proxy) utilizando la misma base tecnológica, lo que simplifica el mantenimiento.
* **Fiabilidad:** Al usar imágenes oficiales de Docker Hub, garantizamos un entorno estable y actualizado con los últimos parches de seguridad.

### 2. Arquitectura de Red Aislada
Se ha definido una red personalizada de tipo `bridge` llamada `red_interna`. Los servidores de backend **no exponen puertos al host**. Esta decisión de diseño crea una "DMZ" (Zona Desmilitarizada) donde:
* El **Proxy** es el único punto de contacto con el mundo exterior (Puerto 80).
* Los **Backends** están protegidos, eliminando vectores de ataque directos y asegurando que todo el tráfico sea filtrado y registrado por el Proxy.

### 3. Gestión de Contenido y Multimedia
Para cumplir con los requisitos de contenido (texto, imagen y multimedia), se ha optado por:
* **Volumen compartido:** El uso de un volumen `bind mount` permite que cualquier actualización en la carpeta local `html/` se refleje instantáneamente en todos los servidores sin reiniciar contenedores.
* **hola.png:** Se ha integrado una imagen personalizada para validar la correcta carga de activos estáticos a través de rutas relativas en el cluster.
* **Elemento Multimedia:** Se incluye un reproductor de audio nativo HTML5 para demostrar la capacidad del proxy de manejar diferentes tipos de MIME-Types y streaming de datos.

---

## 🚀 Guía de Uso

### 1. Preparación de Archivos
Asegúrate de tener la siguiente estructura:
* `docker-compose.yml`
* `nginx.conf`
* `html/index.html`
* `html/hola.png`

### 2. Despliegue
Ejecuta el siguiente comando en tu terminal, estando en el directorio del proyecto:

docker compose up -d



Verificacion del Volumen Compartido:
Al modificar el archivo index.html en la carpeta local, los cambios se reflejan de manera inmediata y simultánea en todos los nodos del clúster. Esto demuestra que ambos servidores leen de la misma fuente de datos, facilitando la actualización de contenido sin necesidad de reconstruir las imágenes de Docker.
<img width="525" height="114" alt="Volumen compartido" src="https://github.com/user-attachments/assets/c06ff17c-3c7d-443c-a315-9956bc435ff3" />
<img width="550" height="185" alt="Captura de pantalla 2026-04-24 162036" src="https://github.com/user-attachments/assets/670f0226-2ae8-4f44-9898-94f34fb9bd6b" />

Verificacion Red Aislada:
Al intentar acceder directamente a través de puertos que no sean el del Proxy (como el 8080), el sistema deniega la conexión. Esto confirma que la infraestructura está protegida y que solo el tráfico legítimo canalizado por el Proxy puede alcanzar los servidores internos. (En la parte superior se ve claramente que no hay fallos de ningun tipo, y que de mitad para abajo da fallos y esta en rojo por haber intentado acceder desde el puerto 8080).
<img width="630" height="499" alt="Red aislada" src="https://github.com/user-attachments/assets/537b563d-8b44-4947-944b-e510f29dd42d" />


Verificacion Balanceo de carga:
La verificación se realiza inspeccionando la cabecera personalizada X-Backend-Server inyectada por el Proxy. Al realizar peticiones sucesivas, se observa cómo el balanceador alterna el tráfico entre las IPs internas de los contenedores (por ejemplo, de la terminada en .2 a la .3), demostrando un reparto de carga equitativo.
<img width="511" height="537" alt="Balanceo de carga" src="https://github.com/user-attachments/assets/d681c35b-4a20-4220-aab5-7111c761fe46" />


Verificacion funcionamiento de Proxy:
En esta prueba se verifica que el servidor que responde es efectivamente el Proxy (identificado como Nginx) y que este es capaz de servir no solo el código HTML, sino también los recursos estáticos como la imagen hola.png y el archivo de audio, actuando como un intermediario transparente.
<img width="581" height="357" alt="Funcionamiento proxy" src="https://github.com/user-attachments/assets/65f93849-745d-4440-b416-a8cefa06904c" />

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
Ejecuta el siguiente comando en tu terminal:
```bash
docker compose up -d

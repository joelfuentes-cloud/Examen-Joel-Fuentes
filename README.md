# Cluster Web con Balanceo de Carga y Proxy Inverso en Docker

Este proyecto despliega una infraestructura web de alta disponibilidad utilizando **Docker** y **Docker Compose**. La arquitectura consta de un proxy inverso que gestiona el tráfico hacia dos servidores de backend aislados, utilizando un volumen compartido para el contenido estático.

## 🚀 Características Técnicas

- **Proxy Inverso:** Nginx configurado como punto de entrada único (Puerto 80).
- **Balanceo de Carga:** Distribución de tráfico tipo *Round Robin* entre dos backends para garantizar disponibilidad.
- **Aislamiento de Red:** Los servidores web residen en una red privada virtual (`red_interna`), siendo inaccesibles desde el host exterior.
- **Persistencia de Datos:** Volumen compartido que sincroniza el contenido (`index.html`, `hola.png`) entre todos los nodos en tiempo real.
- **Identificación de Tráfico:** Cabecera personalizada `X-Backend-Server` inyectada por el proxy para rastrear qué contenedor responde a cada petición.

---

## 🛠️ Cómo poner el proyecto en funcionamiento

Sigue estos pasos para desplegar el entorno en tu máquina local:

1. **Preparar la estructura:** Asegúrate de tener los archivos organizados de la siguiente forma:
   - `docker-compose.yml`
   - `nginx.conf`
   - `html/index.html`
   - `html/hola.png`
2. **Despliegue:** Abre una terminal (CMD o PowerShell) en la carpeta raíz del proyecto y ejecuta:
   ```bash
   docker compose up -d

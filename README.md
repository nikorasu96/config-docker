# mi-proyecto

Este repositorio contiene el entorno de trabajo en Docker para el proyecto, permitiendo ejecutar la aplicación sin necesidad de disponer de todos los archivos del proyecto base.

---

## 3️⃣ Estructura del Entorno de Trabajo

Este es el entorno donde se ejecuta el proyecto en Docker.

### 📂 Estructura de Archivos

```bash
mi-proyecto/
├── docker-compose.yml       # Configuración de servicios en Docker
├── .dockerignore            # Archivos a ignorar en el entorno
└── .env                     # Variables de entorno
⚙️ Configuración de docker-compose.yml
yaml
Copiar
Editar
# La configuración de Docker Compose define los servicios necesarios
services:
  d_next:
    image: nikoxing/mi-proyecto:v1.0.0  # Usa la imagen ya construida en Docker Hub
    restart: always  # Reinicia en caso de fallo
    ports:
      - "127.0.0.1:4000:3001"
    depends_on:
      database:
        condition: service_healthy
    networks:
      - app_network

  database:
    image: mysql:8.0
    container_name: database
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    command: --authentication_policy=caching_sha2_password
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      retries: 3
    tmpfs:
      - /var/run/mysqld  # Mejora rendimiento al evitar escritura en disco
    networks:
      - app_network

# Definición de volúmenes
volumes:
  mysql_data:

# Definición de redes
networks:
  app_network:
📂 Configuración de .dockerignore
dockerignore
Copiar
Editar
# Este archivo especifica los archivos y directorios que deben ser ignorados al construir la imagen

# Ignorar dependencias de Node.js
node_modules

# Ignorar archivos de compilación de Next.js
.next

# Ignorar archivos de entorno
.env
.env.local
.env.development
.env.production

# Ignorar archivos de Git
.git
.gitignore

# Ignorar logs innecesarios
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Ignorar caches de npm y yarn
.npm
.yarn
.pnpm-store

# Ignorar configuraciones de editores de código
.vscode/
.idea/
.DS_Store

# Ignorar archivos de compilación y pruebas
dist
out
coverage

# Ignorar archivos de configuración innecesarios
.dockerignore
docker-compose.override.yml
⚙️ Configuración de .env
ini
Copiar
Editar
# Define las variables de entorno para el entorno de desarrollo
NODE_ENV=development

# Credenciales de MySQL
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=my_database
MYSQL_USER=user
MYSQL_PASSWORD=password
🔥 Descargar y Usar la Imagen desde Docker Hub
sh
Copiar
Editar
# 🛠️ Construcción, Subida y Uso de la Imagen Docker en Docker Hub

# Inicia sesión en Docker Hub
docker login

# Construye la imagen con la etiqueta correspondiente
docker build -t nikoxing/mi-proyecto .

# Verifica que la imagen se haya construido
docker images

# Sube la imagen a Docker Hub
docker push nikoxing/mi-proyecto

# Descarga la imagen desde Docker Hub
docker pull nikoxing/mi-proyecto

# Levanta los servicios en segundo plano, reconstruyendo si es necesario
docker-compose up -d --build

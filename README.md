# Proyecto CI/CD con Jenkins, SonarQube y PostgreSQL


## 👥 Autores

- **Nombres**: Daniela Londoño - Isabella Huila


Este proyecto implementa un pipeline completo de CI/CD utilizando Jenkins, SonarQube y PostgreSQL desplegados en máquinas virtuales de Azure mediante Ansible.

## Descripción

Pipeline automatizado que:
- ✅ Descarga código desde GitHub en cada push
- ✅ Valida herramientas (Node.js, npm)
- ✅ Analiza código estático (archivos JavaScript)
- ✅ Valida estructura del proyecto
- ✅ Despliega automáticamente a servidor Nginx

## Arquitectura

```
┌─────────────────┐      ┌─────────────────┐
│   Jenkins VM    │      │    Nginx VM     │
│  172.191.74.240 │─────▶│  52.170.95.59   │
│                 │ SSH  │                 │
│  - Jenkins      │      │  - Nginx        │
│  - SonarQube    │      │  - App Web      │
│  - PostgreSQL   │      │                 │
└─────────────────┘      └─────────────────┘
```


## Estructura del Proyecto

```
ansible-pipeline/
├── .gitignore              # Archivos ignorados por Git
├── docker-compose.yml      # Definición de servicios Docker
├── inventory               # Inventario de hosts Ansible
├── playbook.yml           # Playbook principal de Ansible
├── README.md              # Este archivo
├── requirements.txt       # Dependencias de Ansible
└── jenkins/
    ├── casc.yaml         # Configuración as Code de Jenkins
    ├── Dockerfile        # Imagen personalizada de Jenkins
    └── plugins.txt       # Lista de plugins de Jenkins
```

## Despliegue

### Prerrequisitos

1. **VMs de Azure** configuradas con Ubuntu 22.04
2. **Ansible** instalado localmente (>= 2.10)
3. **Acceso SSH** a las VMs
4. **Credenciales** configuradas en `inventory`

### Instalación

1. **Clonar el repositorio**:
```bash
git clone <repository-url>
cd ansible-pipeline
```

2. **Configurar inventario**:
```bash
nano inventory
```

3. **Ejecutar el playbook**:
```bash
ansible-playbook -i inventory playbook.yml
```

### Acceso a Servicios

- **Jenkins**: http://172.191.74.240
  - Usuario: `admin`
  - Contraseña: `admin123`
  
- **SonarQube**: http://172.191.74.240:9000
  - Usuario: `admin`
  - Contraseña: `admin`

- **Aplicación desplegada**: http://52.170.95.59/teclado

## Pipeline de Jenkins

El pipeline ejecuta las siguientes etapas:

1. **Checkout**: Descarga código desde GitHub
2. **Verificar Herramientas**: Valida Node.js y npm
3. **Análisis Estático**: Busca archivos JavaScript
4. **Validación**: Verifica package.json
5. **Reporte**: Genera estadísticas del proyecto
6. **Despliegue**: Copia archivos a servidor Nginx vía SSH/rsync

### Configuración del Pipeline

El pipeline está configurado en el repositorio de la aplicación: [Teclado](https://github.com/DaniLond/Teclado.git)

Archivo: `Jenkinsfile`

## Evidencias

### Ejecución Exitosa del Pipeline

![alt text](<Imagen de WhatsApp 2025-11-17 a las 18.00.14_7a7e362f.jpg>)
*Imagen: Pipeline de Jenkins ejecutado exitosamente con las 6 etapas completadas*

---

### Aplicación Desplegada

![alt text](<Imagen de WhatsApp 2025-11-17 a las 18.01.28_3175d137.jpg>)

*Imagen: Aplicación Teclado desplegada y accesible en http://52.170.95.59/teclado*

---

## 🔧 Configuración

### Jenkins

- **Plugins instalados**: 
  - configuration-as-code
  - workflow-aggregator
  - git
  - credentials
  - matrix-auth
  - sonar

- **JVM Options**:
  - `-Xms256m -Xmx512m`

### SonarQube

- **Memoria**: 
  - Search (Elasticsearch): 128MB-256MB
  - Compute Engine: 256MB
  - Web: 256MB

### SSH para Despliegue

El pipeline usa autenticación por clave SSH:
- Clave privada: `/var/jenkins_home/.ssh/id_rsa`
- Usuario remoto: `adminuser`
- Servidor destino: `52.170.95.59`

## 🔒 Seguridad

- ✅ Credenciales de base de datos en variables de entorno
- ✅ Archivo `inventory` en `.gitignore`
- ✅ Autenticación SSH por claves
- ✅ Jenkins con autenticación básica



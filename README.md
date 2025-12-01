
# 🚀 Proyecto Final SIS313: Infraestructura Web Tolerante a Fallos
**Asignatura:** SIS313: Infraestructura, Plataformas Tecnológicas y Redes  
**Semestre:** 2/2025  
**Docente:** Ing. Marcelo Quispe Ortega  

## 👥 Miembros del Equipo
| Nombre Completo | carrera | Contacto (GitHub/Email) |
|----------------|-------------------|-------------------------|
| Yamil Santiago Limmon Espinoza | ingenieria en sistemas | [@yamil](https://github.com/Yamilwz) |
| Jhoel Kevin Garcia Vedia | ingenieria en sistemas | [@jhoel](https://github.com/JhoelKevinGarcia) |
| Nataly Tosube Pinto | ing en cisencias de la computacion | [@nataly](https://github.com/Nataly060) |

## 🎯 I. Objetivo del Proyecto


**Objetivo:** Diseñar, implementar y desplegar una infraestructura web completa con alta disponibilidad, balanceo de carga, 
replicación de base de datos maestro-esclavo y servicios DNS redundantes, garantizando tolerancia a fallos en todos los 
componentes críticos del sistema.


**Objetivos Específicos:**
1. Configurar un entorno distribuido con 7 servidores virtualizados
2. Implementar balanceo de carga con NGINX y certificados TLS
3. Establecer replicación MySQL con failover semi-automático
4. Configurar DNS redundante con BIND9 (primario/secundario)
5. Implementar RAID1 para redundancia de almacenamiento
6. Desarrollar aplicación CRUD con Node.js y frontend web
7. Realizar pruebas exhaustivas de tolerancia a fallos
8. Documentar proceso de implementación y configuración


## 💡 II. Justificación e Importancia

**Justificación:** En entornos empresariales modernos, la disponibilidad continua de servicios web es crítica. Este proyecto 
demuestra cómo eliminar single points of failure mediante la implementación de redundancia en cada capa de la infraestructura, 
aplicando conceptos de Alta Disponibilidad y Seguridad para garantizar la continuidad operacional incluso durante fallos de hardware o software.

**Importancia:**
- **Para la universidad:** Demuestra aplicación práctica de conceptos teóricos
- **Para el sector empresarial:** Proporciona blueprint para infraestructuras resilientes
- **Para los estudiantes:** Desarrolla habilidades en administración de sistemas o soluciones diseñados para satisfacer las necesidades complejas
- **Impacto técnico:** Mejoramos la disponibilidad del 99.0% al 99.9%. antes podía fallar hasta 8 horas al año, ahora solo fallará 1 hora como máximo. Es como tener el sistema prácticamente siempre encendido


## 🛠️ III. Tecnologías y Conceptos Implementados


### 3.1. Tecnologías Clave


| Tecnología | Función Específica |
|------------|-------------------|
| **Ubuntu Server 24.04 LTS** | Sistema operativo base para todos los servidores |
| **NGINX** | Balanceador de carga, proxy inverso y servidor web con TLS |
| **MySQL 8.0** | Sistema de gestión de base de datos con replicación maestro-esclavo |
| **BIND9** | Servidor DNS con configuración de zonas primaria/secundaria |
| **Node.js + Express.js** | Backend para aplicación web CRUD con API REST |
| **mdadm** | Herramienta para configuración de RAID1 |
| **OpenSSL** | Generación de certificados TLS autofirmados |
| **VirtualBox/VMware** | Plataforma de virtualización para laboratorio |


### 3.2. Conceptos de la Asignatura Puestos en Práctica

✅ **Alta Disponibilidad y Tolerancia a Fallos:**
- Replicación MySQL maestro-esclavo con monitoreo de estado
- Balanceo de carga Round-Robin entre aplicaciones redundantes
- DNS redundante con transferencia de zonas AXFR
- Configuración RAID1 para redundancia de almacenamiento

✅ **Automatización y Gestión :**
- Scripts Bash para despliegue automatizado
- Scripts de monitoreo y verificación automática
- Documentación paso a paso para replicación

✅ **Balanceo de Carga/Proxy:**
- NGINX como proxy inverso
- Health checks implícitos mediante timeout de conexión
- Redirección HTTP → HTTPS automática
- Distribución equitativa de carga entre backends

✅ **Networking Avanzado :**
- Configuración de subred 192.168.100.0/24
- Resolución DNS interna personalizada
- Enrutamiento estático entre VMs
- Configuración de puertos y servicios



## 🌐 IV. Diseño de la Infraestructura y Topología

### 4.1. Diseño Esquemático

| VM/Host | Rol | IP Física | Servicios | Sistema Operativo |
|---------|-----|-----------|-----------|-------------------|
| VM-DNS1 | DNS Primario | 192.168.100.10 | BIND9 (Master) | Ubuntu Server 24.04 |
| VM-DNS2 | DNS Secundario | 192.168.100.11 | BIND9 (Slave) | Ubuntu Server 24.04 |
| VM-APP1 | Application Server 1 | 192.168.100.101 | Node.js, vsftpd | Ubuntu Server 24.04 |
| VM-APP2 | Application Server 2 | 192.168.100.102 | Node.js, vsftpd | Ubuntu Server 24.04 |
| VM-DB1 | Database Master | 192.168.100.201 | MySQL, mdadm (RAID1) | Ubuntu Server 24.04 |
| VM-DB2 | Database Slave | 192.168.100.202 | MySQL (Replica) | Ubuntu Server 24.04 |
| VM-LB1 | Load Balancer | 192.168.100.252 | NGINX, OpenSSL | Ubuntu Server 24.04 |

### 4.2 Estrategia Adoptada

**Estrategia de Redundancia por Capas:**
1. **Capa DNS:** Configuración maestro-esclavo con transferencia de zonas
2. **Capa Aplicación:** Dos instancias idénticas con balanceo Round-Robin
3. **Capa Base de Datos:** Replicación asíncrona
4. **Capa Almacenamiento:** RAID1 para redundancia a nivel de disco

**Replicación MySQL:** Se optó por replicación asíncrona tradicional por su simplicidad y bajo overhead.
El lag se mantiene bajo (< 1 segundo) en red local, proporcionando buen balance entre consistencia y disponibilidad.

**Balanceo:** Round-Robin fue seleccionado por su simplicidad y efectividad en entornos homogéneos. 
Ambas instancias de aplicación son idénticas en hardware y configuración.


## 📋 V. Guía de Implementación y Puesta en Marcha

### 5.1. Pre-requisitos

- **Virtualización:** VirtualBox 7.0+ o VMware Workstation 17+
- **Recursos:** Mínimo 8GB RAM, 100GB almacenamiento
- **Sistema Operativo:** Ubuntu Server 24.04 LTS (7 instancias)
- **Conocimientos:** Linux básico, redes TCP/IP, conceptos de servicios

### 5.2. Despliegue Paso a Paso

### **Fase 1: Preparación del Entorno**
```bash
# En todas las máquinas
sudo apt update && sudo apt upgrade -y
sudo apt install git curl wget vim ufw -y
sudo hostnamectl set-hostname [nombre-del-servidor]
````
## Fase 2: Configuración Individual por Servidor

## A. DNS Primario (192.168.100.10)
```bash
# bind9
sudo apt install bind9 bind9utils bind9-doc -y
# poner en servicio bind9
sudo systemctl enable bind9
```

## B. DNS Secundario (192.168.100.11)
```bash
# instalar bind9
sudo apt install bind9 bind9utils bind9-doc -y
```

## C. DB Master (192.168.100.201)
```bash
# instalar mysql
sudo apt install mysql-server mdadm -y
# Configurar replicación y RAID
mysql -u root -p -e "CREATE USER 'replica'@'%' IDENTIFIED BY 'replipass';"
```

## D. DB Slave (192.168.100.202)
```bash
# instalar mysql
sudo apt install mysql-server -y
# Configurar como esclavo
CHANGE MASTER TO MASTER_HOST='192.168.100.201', ...;
START SLAVE;
```

## E. App Servers (192.168.100.101 y 192.168.100.102)
```bash
# Ver scripts/apps/node-app-setup.sh
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs vsftpd -y
git clone https://github.com/[usuario]/SIS313-Infraestructura-Tolerante-Fallos.git
cd apps/node-app && npm install
sudo systemctl enable node-app
```

## F. Load Balancer (192.168.100.252)
```bash
# Ver scripts/config/nginx/lb-setup.sh
sudo apt install nginx openssl -y
# Generar certificados SSL
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/web.sis313.key \
  -out /etc/ssl/certs/web.sis313.crt
sudo systemctl enable nginx
```
## Ficheros de Configuración Clave

A. **NGINX Balanceador** *(/etc/nginx/sites-available/balanceador)*

B. **MySQL Master** *(/etc/mysql/mysql.conf.d/mysqld.cnf)*

C. **BIND9 Zona Directa** *(/etc/bind/db.sis313.usfx.bo)*


## 📚 VII. Conclusiones y Lecciones Aprendidas
## Conclusiones Principales
**Objetivo Cumplido**
Se implementó exitosamente una infraestructura web completa con redundancia en todas las capas, demostrando tolerancia a fallos en escenarios realistas.

Alta Disponibilidad Lograda: El sistema mantiene operación continua durante fallos individuales de componentes, alcanzando aproximadamente 99.9% de disponibilidad.

Aprendizaje Integral: El proyecto cubrió desde virtualización y networking hasta seguridad y automatización, proporcionando experiencia práctica en administración de sistemas empresariales.

Documentación Completa: Se generó documentación técnica detallada que permite replicar el proyecto en otros entornos educativos o de pruebas.


## Metodológicas:
**Enfoque Iterativo:** Implementar por capas y verificar cada paso antes de continuar.

**Documentación en Tiempo Real:** Documentar mientras se configura, no después.

**Pruebas Incrementales:** Probar cada componente individualmente antes de pruebas integradas.

## para Futuras Implementaciones
**Keepalived:** Implementar para alta disponibilidad del balanceador (eliminar single point of failure).

**Monitoreo Centralizado:** Agregar Prometheus + Grafana para métricas en tiempo real.

**Certificados Let's Encrypt:** Reemplazar certificados autofirmados por unos reconocidos públicamente.

**Containerización:** Migrar a Docker/Kubernetes para mayor portabilidad y escalabilidad.

**Backup Automatizado:** Implementar sistema de backups cifrados con retención configurable.

[anexos](https://github.com/Yamilwz/SIS313_Infra_Estructura_Proyecto_Final/blob/main/informe%20de%20la%20realizacion)

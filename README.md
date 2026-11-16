# Laboratorio de Ansible con contenedores Docker

**Estudiante:** Robert Plaza (2024-2174)
**Institución:** Instituto Tecnológico de las Américas (ITLA)


Evidencia

<img width="867" height="455" alt="a8d60108-a204-455b-aea6-892649d1d113" src="https://github.com/user-attachments/assets/ac8db62c-909d-4817-8ea5-00b57856b6ca" />

## Objetivo de la práctica

El objetivo de esta práctica es aprender a automatizar la configuración de servidores con Ansible usando dos contenedores Docker basados en Ubuntu 24.04 como nodos administrados. Con el inventario definimos los servidores y la forma de conectarse a ellos (`ansible_connection=docker`, sin SSH). Luego el playbook ejecuta tareas comunes de administración: recolecta información del sistema, crea directorios y archivos, usa un `loop` para repetir una tarea y un condicional `when` para que una tarea se ejecute solo si se cumple una condición.

## Estructura del repositorio

```text
ansible-lab/
├── docker-compose.yml   # Define los contenedores server1 y server2
├── inventory.ini        # Inventario de Ansible (grupo [docker])
├── playbook.yml         # Tareas a ejecutar en ambos servidores
└── README.md
```

## Requisitos

- Docker y Docker Compose
- Ansible (el paquete completo `ansible` incluye la colección `community.docker`, necesaria para la conexión `docker`). Si solo tienes `ansible-core`, instálala con:

```bash
ansible-galaxy collection install community.docker
```

## Comandos para iniciar los contenedores

Levantar los servidores:

```bash
docker compose up -d
```

Verificar que ambos estén en ejecución:

```bash
docker ps
```

Instalar Python en cada contenedor (Ansible lo necesita para ejecutar sus módulos):

```bash
docker exec -it server1 bash -c "apt update && apt install -y python3"
docker exec -it server2 bash -c "apt update && apt install -y python3"
```

Comprobar la conexión con Ansible:

```bash
ansible docker -i inventory.ini -m ping
```

## Comando para ejecutar el playbook

```bash
ansible-playbook -i inventory.ini playbook.yml
```

El playbook realiza, en ambos servidores:

1. Muestra el nombre del host.
2. Muestra el sistema operativo y su versión.
3. Crea el directorio `/tmp/ansible-demo`.
4. Crea el archivo `/tmp/ansible-demo/info.txt` con el nombre del servidor y el sistema operativo.
5. Crea mediante un `loop` los directorios `logs`, `backup` y `config` dentro de `/tmp/ansible-demo`.
6. Muestra un mensaje únicamente cuando el sistema operativo es Ubuntu (`when`).

Para verificar el resultado dentro de un contenedor:

```bash
docker exec server1 cat /tmp/ansible-demo/info.txt
docker exec server1 ls /tmp/ansible-demo
```

## Captura de la ejecución exitosa

![Ejecución exitosa del playbook](captura-playbook.png)

## Limpieza

```bash
docker compose down
```

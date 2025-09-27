# Grupo4_Docker_TareaFinal

# Despliegue de Flowise con PostgreSQL en Docker Compose

## 🚀 Integrantes
| Nro. | Nombre | Link |
|------|---------|---------|
| 1 | Giovanni Xavier Baño Jaya | https://github.com/Giovanni26101982/Grupo4_Docker_TareaFinal |
| 2 | Portero Salas Onofre Lolislao | https://github.com/oportero/Grupo4_Docker_TareaFinal |
| 3 | Jara Pauta Cesar Paúl | https://github.com/PaulJara84/Grupo4_Docker_TareaFinal |
| 4 | Maldonado Flores Oscar Alexander | https://github.com/Oscar112248/Grupo4_Docker_TareaFinal |
| 5 | Balarezo Leon Ricardo Martin | https://github.com/TinchoXD/Grupo4_Docker_TareaFinal |

---

## 📖 Introducción

Este proyecto despliega **Flowise** con una base de datos **PostgreSQL** dedicada, utilizando **Docker Compose**.

El objetivo de este trabajo es desplegar una aplicación con Flowise, integrada con su propia base de datos PostgreSQL, utilizando Docker Compose. 

---

## 🚀 Requisitos previos
- Docker instalado
- Docker Compose instalado

---

## 📂 Estructura
```bash
flowise-postgres/
│── docker-compose.yml
│── .env
└── README.md
```

---

## 🛠 Desarrollo - Procedimiento

--- 
1. **Ver espacio disponible**

   Antes de ejecutar, verificar que existe espacio en disco (opcional se pueden remover los volúmenes que ya no se utilizan)

```bash
docker system df
```
<img width="886" height="170" alt="image" src="https://github.com/user-attachments/assets/76604ba6-2ab3-44c0-aac6-3a2e5d7c0bc7" />

--- 

2. **Liberar espacio de imágenes/volúmenes huérfanos**

```bash
docker system prune -af --volumes
```
<img width="886" height="789" alt="image" src="https://github.com/user-attachments/assets/ed539125-df58-4177-84b1-f838522829ef" />
  
  - Se eliminará:
  
    - contenedores detenidos
    - imágenes no usadas
    - volúmenes no usados
    - redes no usadas

--- 

3. **Verificar imágenes grandes**

```bash
docker images --digests --no-trunc --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```
<img width="886" height="70" alt="image" src="https://github.com/user-attachments/assets/06fb3f4c-e57b-4171-af7a-577a08e41db3" />

--- 

4. **Eliminar imágenes si existen:**

```bash
docker rmi <image_id>
```

--- 

5. **Ejecutar docker compose:**

```bash
docker compose up --build -d
```
<img width="886" height="783" alt="image" src="https://github.com/user-attachments/assets/129cef40-9929-4eda-885d-4307f5f0d347" />

--- 

6. **Verificar el estado de los contenedores:**

```bash
docker ps -a
```
<img width="886" height="102" alt="image" src="https://github.com/user-attachments/assets/3c72451e-be44-42ad-ac7b-02f621ceacc9" />

   - En este caso la base de datos (flowise-db) está `Up (healthy)`, pero el contenedor de `Flowise` aparece en: `Restarting (0) 23 secongs ago`
---

7. **Verificar el Log**

```bash
docker logs -f flowise
```
<img width="886" height="760" alt="image" src="https://github.com/user-attachments/assets/eefe7c5a-f518-42bb-8115-903d95cff636" />

   - Se repite en bucle
```bash
Flowiseai Server
VERSION
  flowise/1.6.3 linux-x64 node-v18.20.1

USAGE
  $ flowise [COMMAND]
COMMANDS
  start
Flowiseai Server
VERSION
  flowise/1.6.3 linux-x64 node-v18.20.1
USAGE
  $ flowise [COMMAND]
COMMANDS
  Start
.
.
.
```
   - Eso significa que la `imagen oficial de Flowise` no arranca en el servidor automáticamente, sino que requiere que le pases explícitamente el comando start.
---

8. **Se modifica el .yml para solucionar el error**

```bash
Antes -> command: ["flowise"]
Nuevo -> command: ["flowise", "start"]
```
---

9. **Volver a crear el contenerdo**

```bash
docker compose down
```
<img width="886" height="105" alt="image" src="https://github.com/user-attachments/assets/a8795069-4d2c-4d74-a4e3-16bb55252814" />

```bash
docker compose up -d -build
```
<img width="886" height="121" alt="image" src="https://github.com/user-attachments/assets/d497b4e0-af40-40fb-be4c-ba1b259d9071" />

```bash
docker ps -a
```
<img width="886" height="103" alt="image" src="https://github.com/user-attachments/assets/3486b1b1-1ec6-4ac3-8daa-ab88ec282de7" />

---

10. **Validar la navegación a la dirección local del contenedor en el host**

```bash
http://localhost:3000/
```
<img width="886" height="716" alt="image" src="https://github.com/user-attachments/assets/8b069289-49ef-47db-a526-4144849234bd" />

---

11. **Accesar utilizando las credenciales del .env**

<img width="886" height="693" alt="image" src="https://github.com/user-attachments/assets/1a14e45d-9a39-441f-b158-022fe8e47c24" />

---

12. **Validar la aplicación de Flowise**

<img width="886" height="673" alt="image" src="https://github.com/user-attachments/assets/279384b7-444d-41af-9f24-6e1247f84295" />

---



## ✅ Conclusiones

1. **Compatibilidad de versión de Compose**

   -	La instrucción name: solo es reconocida en la especificación moderna de Compose (ejecutando docker compose en lugar de docker-compose).
     <img width="886" height="117" alt="image" src="https://github.com/user-attachments/assets/9d6de6da-b703-4fc5-a47b-2afc23410a30" />

   -	Se detectó que version: ya es obsoleto en Compose V2, por lo que puede omitirse para evitar advertencias.
     <img width="711" height="441" alt="image" src="https://github.com/user-attachments/assets/a68e5832-6cc0-4540-a947-db94d9bbd159" />

2. **Gestión de espacio en disco**

   - Durante la descarga de la imagen flowiseai/flowise:1.6.3 se produjo un error de “no space left on device”.
   - Se resolvió mediante limpieza de imágenes, volúmenes y contenedores no utilizados, confirmando la importancia de tener espacio disponible antes de la instalación.

3.	**Ejecución del contenedor Flowise**
   
      - El contenedor entraba en bucle de reinicios porque la imagen oficial requiere ejecutar explícitamente el comando flowise start.
      - Al añadir command: ["flowise", "start"] en el docker-compose.yml, el servicio se inicializó correctamente y quedó accesible en el puerto configurado.
     
4.	**Base de datos y persistencia**

      - El contenedor PostgreSQL respondió como healthy, lo que confirma que la configuración de credenciales (POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_DB) fue correcta.
      - Se definieron volúmenes (flowise_db_data y flowise_home_data) para garantizar persistencia de datos y configuraciones de Flowise.

## 🛠️ Recomendaciones para el despliegue correcto

1. **Requisitos previos de la máquina**
      
      - Contar con al menos `5 GB libres` en disco para imágenes, capas y volúmenes de Docker.
      - Memoria RAM mínima: `2 GB disponibles` (Flowise + Postgres son relativamente livianos, pero requieren cierto buffer).
      - Tener instalado `Docker Engine y Docker Compose V2` (docker compose).

2.	**Archivo .env bien configurado**
   
      - Definir correctamente:
         - POSTGRES_IMAGE, POSTGRES_DB, POSTGRES_USER, POSTGRES_PASSWORD.
         - FLOWISE_IMAGE, FLOWISE_PORT, FLOWISE_USERNAME, FLOWISE_PASSWORD.

3.	**Archivo docker-compose.yml validado**

      - Confirmar que la sintaxis YAML es correcta (docker compose config).
      - Incluir el command: ["flowise", "start"] en el servicio flowise.
      - Definir restart: unless-stopped para garantizar alta disponibilidad.


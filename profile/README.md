# Repositorio ETL de Airflow para RBMA Movilidad

## 1. Visión General del Proyecto

Este repositorio contiene las implementaciones de los pipelines de **ETL (Extracción, Transformación y Carga)** para el proyecto de **Movilidad Sostenible en la Reserva de la Biosfera del Macizo de Anaga (RBMA)**. La iniciativa, impulsada por el Cabildo de Tenerife en colaboración con la Universidad de La Laguna y con financiación de los fondos Next Generation EU, tiene como objetivo principal gestionar el flujo de más de 1.8 millones de visitantes anuales para mitigar la saturación de vehículos, reducir el impacto ambiental y mejorar la experiencia de residentes y turistas.

El núcleo técnico de este proyecto se basa en la instalación de una red de sensores y cámaras para monitorizar en tiempo real los patrones de movilidad. Los datos recopilados son procesados a través de los pipelines de Airflow definidos en este repositorio. El objetivo final es generar un mapa de movilidad, crear matrices origen-destino y desarrollar algoritmos que permitan a las autoridades tomar decisiones informadas para optimizar el transporte público y proponer alternativas sostenibles al vehículo privado.

Este repositorio se encarga de la orquestación de todos los procesos de datos, desde la ingesta inicial hasta su almacenamiento en un **Data Lake** centralizado, listo para su posterior análisis y visualización.

## 2. Arquitectura y Estructura del Repositorio

El proyecto está organizado siguiendo una estructura modular y escalable para facilitar el desarrollo, mantenimiento y la reutilización de código.

```
└── airflow-rbma/
    ├── docs/
    │   └── release-it_guide.md
    ├── lib/
    │   ├── params/
    │   │   └── default_params.py
    │   ├── utils/
    │   │   ├── dag_tasks/
    │   │   │   ├── data_retrieving.py
    │   │   │   ├── data_saving.py
    │   │   │   ├── datalake.py
    │   │   │   └── db_multiextract.py
    │   │   ├── scripts/
    │   │   │   ├── airflow/
    │   │   │   │   └── schemas_dags_serializer.py
    │   │   │   ├── auto_doc/
    │   │   │   │   └── generate.py
    │   │   │   └── local_airflow_manager.py
    │   │   ├── basic.py
    │   │   ├── date.py
    │   │   ├── mail.py
    │   │   └── params_maker_utils.py
    │   └── airflow_loggin.py
    ├── org_simulator/
    │   └── bz_0010_sim_extract.py
    ├── airflow_global_variables_DEV.json
    ├── package-lock.json
    └── package.json
```

*   **`docs/`**: Contiene toda la documentación relevante del proyecto, incluyendo guías de despliegue, configuración de herramientas y notas sobre las versiones.
*   **`lib/`**: Es el corazón del framework ETL. Alberga código reutilizable y módulos compartidos que son utilizados por todos los DAGs. Su objetivo es abstraer la lógica común y estandarizar el desarrollo.
*   **`org_simulator/`**: Un ejemplo de directorio de "ámbito" o "esquema". Cada carpeta en la raíz del proyecto (como esta) agrupa DAGs relacionados con una fuente de datos o un propósito lógico específico. El fichero `bz_0010_sim_extract.py` representa un DAG de ejemplo que utiliza el simulador de datos de cámaras.
*   **`airflow_global_variables_DEV.json`**: Fichero de configuración que contiene las variables globales de Airflow para el entorno de desarrollo (DEV).
*   **`package.json`**: Define los metadatos del proyecto, las dependencias de Node.js para herramientas de desarrollo y los scripts para automatizar tareas como el versionado o la ejecución de utilidades.

## 3. Componentes Principales del Framework (`lib/`)

La carpeta `lib/` proporciona un conjunto de herramientas robustas para construir pipelines ETL de manera consistente.

####  **Logging Centralizado (`airflow_loggin.py`)**
Proporciona clases personalizadas (`Logger` y `ETLDebugger`) que extienden el sistema de logging de Airflow. Permite registrar mensajes con niveles de detalle configurables y con un formato estandarizado para cada etapa del proceso (Extracción, Transformación, Carga), facilitando la depuración y monitorización de los DAGs.

#### 🏞️ **Gestión del Data Lake (`lib/utils/dag_tasks/datalake.py`)**
Este módulo es fundamental para la interacción con el Data Lake.
*   **`multi_saving`**: Una función clave que permite guardar un DataFrame en múltiples destinos de forma simultánea (ej. base de datos PostgreSQL, ficheros en un recurso compartido Samba, o en local). Su comportamiento se configura mediante variables de Airflow, permitiendo flexibilidad entre entornos.
*   **`DatalakeDSTView`**: Una clase que facilita la creación y gestión de vistas en la base de datos del Data Lake, permitiendo crear "espejos" (mirroring) de tablas de origen o vistas personalizadas a partir de ficheros SQL.
*   **`register_update`**: Registra cada actualización en una tabla de metadatos, llevando un control sobre los cambios en el número de registros de cada tabla del Data Lake.

#### 💽 **Abstracción de Datos (`data_retrieving.py` y `data_saving.py`)**
Estos módulos abstraen la lógica para conectarse y operar con distintas fuentes y destinos de datos (PostgreSQL, Oracle, ficheros CSV, Excel, Parquet). Proporcionan funciones simplificadas para que los DAGs no necesiten conocer los detalles de implementación de las conexiones. Incluye validadores para asegurar la integridad de los datos al escribirlos en bases de datos.

#### 🎛️ **Configuración de DAGs (`lib/params/default_params.py`)**
Centraliza la configuración por defecto para todos los DAGs (`default_args`), como el propietario, el número de reintentos, o la fecha de inicio. Esto asegura consistencia y reduce código repetido en la definición de cada DAG.

## 4. Flujo de Trabajo y Herramientas

El repositorio está equipado con herramientas que automatizan y estandarizan el ciclo de vida del desarrollo.

#### ⚙️ **Gestión de la Configuración**
El proyecto utiliza **Variables de Airflow** para gestionar la configuración de manera centralizada. El fichero `airflow_global_variables_DEV.json` sirve como plantilla para los distintos entornos. Variables como `DALAKE_DB_CONN` o `DATALAKE_MULTISAVING_OPTIONS` (conexiónes directas entre el servicio de Airflow y la instancia desplegada de Postgres -datalake- para este proyecto) permiten adaptar el comportamiento de los DAGs sin modificar el código.

#### 📦 **Versionado y Despliegue con `release-it`**
Se utiliza `release-it` junto con el estándar de **Conventional Commits** para automatizar el proceso de creación de versiones. Cada vez que se necesita una nueva release, el siguiente comando se encarga de:
1.  Determinar la siguiente versión semántica (major, minor, patch) basándose en los mensajes de commit.
2.  Generar y/o actualizar el fichero `CHANGELOG.md`.
3.  Crear un tag de Git y un commit de versión.

```bash
npm run release
```

#### ✍️ **Estándar de Commits con `commitlint`**
Para garantizar un historial de commits limpio y legible, se utiliza `commitlint`. Este fuerza a que cada mensaje de commit siga la especificación de Conventional Commits (ej. `feat:`, `fix:`, `chore:`, etc.). Esto es crucial para el funcionamiento del versionado automático.

#### 🚀 **Scripts de Desarrollo**
`package.json` incluye scripts útiles para el desarrollo diario:
*   **`npm run airflow -- refresh <nombre_del_esquema>`**: Ejecuta el script `local_airflow_manager.py`, que permite "refrescar" o reserializar los DAGs de un esquema específico directamente en el scheduler de Airflow sin necesidad de reiniciarlo. Esto acelera enormemente el ciclo de desarrollo.
*   **`npm run auto-doc`**: Un script avanzado que utiliza la **API de Gemini de Google** para analizar los commits entre dos tags de Git y generar automáticamente una propuesta de texto para la nueva sección del `CHANGELOG.md`.

## 5. Cómo Empezar

1.  **Clonar el Repositorio**:
    ```bash
    git clone https://github.com/RBMA-Movilidad-ULL-CabildoTNF/airflow-rbma.git
    cd airflow-rbma
    ```

2.  **Instalar Dependencias de Node.js**:
    Estas dependencias son para las herramientas de desarrollo.
    ```bash
    npm install
    ```

3.  **Configurar el Entorno de Airflow**:
    *   Asegúrate de que tu entorno de Airflow tiene acceso a las librerías Python definidas en la carpeta `lib/`.
    *   Importa las variables desde `airflow_global_variables_DEV.json` en la sección `Admin -> Variables` de la UI de Airflow y ajústalas según tu entorno.

4.  **Desarrollar un Nuevo DAG**:
    *   Crea una nueva carpeta en la raíz para agrupar tus DAGs si pertenecen a un nuevo ámbito.
    *   Dentro de tu DAG, importa los módulos necesarios de la carpeta `lib/` para reutilizar la lógica de logging, extracción y carga. El DAG en `org_simulator/bz_0010_sim_extract.py` es un excelente punto de partida.

## 6. Licencia

Este proyecto está distribuido bajo la licencia **ISC**. Para más detalles, consulta el fichero `package.json`.

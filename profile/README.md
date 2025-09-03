# Organización Técnica del Proyecto de Movilidad Sostenible RBMA

![Anaga Banner](https://github.com/RBMA-Movilidad-ULL-CabildoTNF/.github/blob/main/profile/assets/anaga_banner.png)

## Quiénes Somos

Somos el equipo técnico responsable del diseño, construcción y mantenimiento de la infraestructura digital para la **Estrategia de Movilidad Sostenible en la Reserva de la Biosfera del Macizo de Anaga (RBMA)**. Nacimos de la colaboración estratégica entre el **Cabildo de Tenerife** y la **Universidad de La Laguna (ULL)**, con el objetivo de traducir las necesidades del proyecto en soluciones tecnológicas robustas, escalables y eficientes.

Nuestra misión es desarrollar las herramientas internas que permitan capturar, procesar, analizar y exponer los datos de movilidad, convirtiéndolos en información accionable para la gestión inteligente y sostenible del Parque Rural de Anaga.

[Información del proyecto al completo](https://sites.google.com/ull.edu.es/movilidad-anaga)

---

## Nuestros Pilares Tecnológicos

Para alcanzar nuestros objetivos, hemos estructurado nuestro trabajo en torno a cuatro pilares tecnológicos fundamentales, cada uno materializado en un conjunto de herramientas y repositorios específicos.

### 1. 🏞️ Data Lake Centralizado

Es el **corazón de nuestra estrategia de datos**. Hemos diseñado y mantenemos un Data Lake centralizado que actúa como la única fuente de verdad para toda la información de movilidad.

*   **Función**: Almacenar de forma segura y estructurada tanto los datos crudos (provenientes de sensores y cámaras) como los datos procesados y enriquecidos (matrices origen-destino, conteos, etc.).
*   **Tecnología**: Principalmente basado en **PostgreSQL**, con un esquema de organización que distingue entre datos de origen (`org_*`), datos de destino (`dst_*`) y metadatos de operación.
*   **Impacto**: Proporciona a los analistas, investigadores y aplicaciones una base de datos consolidada y fiable, eliminando silos de información y garantizando la calidad y consistencia de los datos.

### 2. ⚙️ Orquestación de ETLs con Apache Airflow

Es el **cerebro operativo** de nuestra plataforma. Utilizamos Apache Airflow para automatizar, programar y monitorizar todos los flujos de trabajo de datos (ETL).

*   **Función**: Orquestar de manera fiable los procesos de **Extracción** de datos desde las fuentes (simulador, APIs de cámaras), **Transformación** (limpieza, agregación, cálculo de métricas) y **Carga** en nuestro Data Lake.
*   **Framework Propio**: Hemos desarrollado una librería interna (`lib/`) que estandariza la creación de DAGs, la gestión del logging, la interacción con el Data Lake y la notificación de errores, acelerando el desarrollo y minimizando la duplicación de código.
*   **Impacto**: Garantiza que los datos se procesen de forma regular, resiliente y monitorizada, asegurando que la información en el Data Lake esté siempre actualizada y sea precisa.

### 3. 🚗 Simulador de Movilidad

Es nuestra **herramienta estratégica para la planificación y el análisis proactivo**. Ante la ausencia inicial de datos reales de las cámaras, hemos construido un simulador avanzado.

*   **Función**: Generar datos sintéticos realistas sobre los patrones de movilidad en Anaga. El simulador utiliza la ubicación teórica de las cámaras y se calibra con datos del Cabildo y fuentes externas (Google, TomTom) para modelar flujos de vehículos.
*   **Propósito**: Permite a los técnicos y gestores probar hipótesis y escenarios ("¿qué pasaría si cerramos una carretera?" o "¿cómo afectaría un nuevo parking?") antes de implementar cambios en el mundo real. También es crucial para el desarrollo y validación de nuestros pipelines ETL antes de la llegada de los datos definitivos.
*   **Impacto**: Reduce la incertidumbre en la toma de decisiones, permite un diseño de políticas basado en evidencia y acelera el desarrollo de toda la plataforma de datos.

### 4. 🌐 APIs de Interfaz y Explotación

Son el **puente entre nuestros datos y los usuarios finales**. Desarrollamos un conjunto de APIs que exponen de forma controlada y segura la información del Data Lake.

*   **Función**: Servir los datos procesados a diferentes consumidores, como pueden ser:
    *   **Dashboards de visualización** para los técnicos del Cabildo.
    *   **Herramientas de análisis** para los investigadores de la ULL.
    *   Potenciales **aplicaciones públicas** que informen sobre el estado del tráfico o la ocupación de aparcamientos.
*   **Tecnología**: Basadas en estándares REST, garantizan un acceso rápido, seguro y bien documentado a los datos.
*   **Impacto**: Democratizan el acceso a la información, permitiendo que el valor generado por el procesamiento de datos sea efectivamente utilizado por los distintos actores del proyecto para cumplir sus objetivos.

---

## Nuestra Arquitectura y Flujo de Datos

Estos pilares se integran en un flujo de datos coherente:

**Sensores/Cámaras/Simulador** → **Ingesta de Datos** (ETLs en Airflow) → **Procesamiento y Transformación** (ETLs en Airflow) → **Almacenamiento en Data Lake** → **APIs de Explotación** → **Visualización y Análisis**

## Nuestros Repositorios

Esta organización de GitHub alberga los repositorios de cada uno de los componentes mencionados, siguiendo una política de "un repositorio por producto" para mantener el código desacoplado y especializado.

*   [`airflow-rbma`](https://github.com/RBMA-Movilidad-ULL-CabildoTNF/airflow-rbma): Contiene todos los DAGs de Airflow y el framework ETL compartido.
*   **`simulator` (Próximamente)**: Albergará el código fuente del simulador de movilidad.
*   **`simulator-api` (Próximamente)**: Contendrá el desarrollo de la API de recolecta de datos simulando la API final de las cámaras.
*   **`datalake-api` (Próximamente)**: Contendrá el desarrollo de la API de interfaz al Datalake y explotación de datos.

Nuestro compromiso es construir una base tecnológica sólida que no solo resuelva los desafíos actuales, sino que también sea lo suficientemente flexible para adaptarse a las futuras necesidades del proyecto de movilidad sostenible en Anaga.

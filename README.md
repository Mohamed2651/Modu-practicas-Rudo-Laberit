# 🛍️ Modu — Aplicación de compra de ropa

Aplicación móvil Android desarrollada durante mi periodo de prácticas en **Rudo / Laberit**.

El proyecto consiste en una aplicación de comercio electrónico enfocada en la **compra de ropa**, permitiendo al usuario consultar productos, navegar por las diferentes secciones y gestionar la información relacionada con la tienda.

El objetivo principal del proyecto fue trabajar en un entorno de desarrollo Android utilizando **Kotlin**, aplicando una arquitectura organizada y escalable basada en **Clean Architecture** y utilizando diferentes tecnologías y librerías del ecosistema Android.

---

## 📱 Descripción

**Modu** es una aplicación Android orientada al comercio electrónico de ropa.

La aplicación consume información desde una API externa para obtener los productos y utiliza una base de datos local para almacenar información de forma persistente.

Durante el desarrollo se trabajó con diferentes componentes habituales en aplicaciones Android modernas:

* Consumo de APIs REST.
* Gestión de datos remotos y locales.
* Navegación entre pantallas.
* Carga y visualización de imágenes.
* Persistencia de datos.
* Inyección de dependencias.
* Paginación de resultados.
* Gestión del estado de la aplicación.
* Separación de responsabilidades mediante arquitectura por capas.

---

## 🛠️ Tecnologías utilizadas

### Lenguaje

* **Kotlin**

### Android

* Android SDK 36
* AndroidX
* ViewBinding
* Material Components
* Navigation Component
* Android Splash Screen

### Arquitectura

* **Clean Architecture**
* Separación por capas
* Patrón Repository
* ViewModel
* Casos de uso
* Inyección de dependencias

### Datos

* **Retrofit** — comunicación con la API REST.
* **Gson** — conversión de JSON a objetos Kotlin.
* **OkHttp** — gestión de las comunicaciones HTTP y logging.
* **Room** — persistencia de datos local.
* **Paging** — carga paginada de información.

### Librerías adicionales

* **Hilt** — inyección de dependencias.
* **Coil** — carga de imágenes.
* **Flexbox** — creación de layouts flexibles.
* **Kotlin Coroutines** — operaciones asíncronas.

---

# 🏗️ Arquitectura

El proyecto utiliza **Clean Architecture**, buscando separar la aplicación en diferentes responsabilidades y evitar que la lógica de negocio dependa directamente de Android, de la interfaz gráfica o de una implementación concreta de acceso a datos.

La estructura se puede representar de forma simplificada de la siguiente manera:

```text
┌──────────────────────────────┐
│       PRESENTATION           │
│                              │
│  Fragments / UI / ViewModels │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          DOMAIN              │
│                              │
│  Models / Use Cases /        │
│  Repository interfaces       │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│           DATA               │
│                              │
│  Repositories / API / Room   │
│  DTOs / Local Data Sources   │
└──────────────────────────────┘
```

La idea principal es que **las capas externas dependen de las internas**, pero la lógica de negocio no necesita conocer los detalles de implementación de la capa de datos.

---

## 📂 Capas de la aplicación

### 🎨 Presentation

Es la capa encargada de la interacción con el usuario.

Aquí se encuentran elementos como:

* Fragments.
* ViewModels.
* Adaptadores.
* ViewBinding.
* Navegación.
* Componentes de interfaz.

Los **ViewModels** se encargan de preparar y gestionar la información que necesita la interfaz, evitando colocar la lógica de negocio directamente dentro de los Fragments.

Ejemplo conceptual:

```text
Fragment
   │
   ▼
ViewModel
   │
   ▼
UseCase
```

De esta forma, la interfaz se mantiene principalmente centrada en representar el estado de la aplicación.

---

### 🧠 Domain

La capa **Domain** contiene la lógica de negocio de la aplicación.

Es la capa más independiente del proyecto y no debería depender de detalles concretos como Retrofit, Room o componentes específicos de Android.

En esta capa se encuentran principalmente:

* Modelos de dominio.
* Interfaces de Repository.
* Use Cases.

Los **Use Cases** representan acciones concretas que puede realizar la aplicación.

Por ejemplo:

```text
Obtener productos
       ↓
GetProductsUseCase
       ↓
Repository
```

Esto permite que la lógica de negocio pueda reutilizarse independientemente de dónde procedan los datos.

---

### 💾 Data

La capa **Data** se encarga de obtener, transformar y almacenar los datos.

En este proyecto se utilizan diferentes fuentes de información:

```text
          ┌───────────────┐
          │   REST API    │
          │   Retrofit    │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │ Repository    │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │ Domain Model  │
          └───────────────┘

          ┌───────────────┐
          │     Room      │
          │ Local Database│
          └───────────────┘
```

La capa de datos contiene las implementaciones necesarias para comunicarse con las diferentes fuentes.

Entre ellas:

* API REST mediante Retrofit.
* Conversión de respuestas JSON mediante Gson.
* Comunicación HTTP mediante OkHttp.
* Base de datos local mediante Room.
* DTOs para representar los datos procedentes de la API.
* Mappers para transformar los datos entre capas.

---

# 🔄 Flujo de datos

Un ejemplo del flujo para obtener productos sería:

```text
Usuario
   │
   ▼
Fragment
   │
   ▼
ViewModel
   │
   ▼
GetProductsUseCase
   │
   ▼
ProductRepository
   │
   ▼
Remote Data Source
   │
   ▼
Retrofit
   │
   ▼
REST API
```

La respuesta vuelve siguiendo el camino inverso hasta llegar al ViewModel, que actualiza el estado que utiliza la interfaz.

Este enfoque permite que cada componente tenga una responsabilidad concreta.

---

# 💉 Inyección de dependencias

Para gestionar las dependencias de la aplicación se utiliza **Hilt**.

En lugar de crear manualmente objetos como repositories, servicios de Retrofit o bases de datos dentro de cada pantalla, Hilt se encarga de proporcionar las dependencias necesarias.

Por ejemplo:

```text
ViewModel
    │
    └── UseCase
          │
          └── Repository
                 │
                 ├── Retrofit
                 └── Room
```

Esto facilita:

* El mantenimiento del código.
* La reutilización de componentes.
* Las pruebas unitarias.
* La sustitución de implementaciones.
* La escalabilidad del proyecto.

---

# 🌐 Comunicación con la API

La aplicación utiliza **Retrofit** para realizar las peticiones HTTP a la API.

La comunicación sigue una estructura similar a:

```text
API
 ↓
Retrofit
 ↓
DTO
 ↓
Mapper
 ↓
Domain Model
 ↓
Repository
 ↓
Use Case
 ↓
ViewModel
 ↓
UI
```

**Gson** se utiliza para convertir las respuestas JSON de la API en objetos Kotlin.

Además, **OkHttp** permite gestionar las comunicaciones HTTP y proporciona herramientas como el logging de las peticiones durante el desarrollo.

---

# 🗄️ Persistencia local

El proyecto utiliza **Room** como solución de persistencia local.

Room permite trabajar con una base de datos SQLite de una forma más estructurada y segura dentro del ecosistema Android.

La estructura sigue conceptualmente:

```text
Room Database
      │
      ├── Entities
      │
      ├── DAO
      │
      └── Database
```

De esta forma, los datos locales quedan aislados de la lógica de negocio y de la interfaz.

---

# 📄 Paginación

Para gestionar grandes cantidades de productos se utiliza **Android Paging**.

En lugar de cargar todos los productos de una sola vez, la información puede obtenerse progresivamente.

```text
Producto 1
Producto 2
Producto 3
      ↓
Cargar más
      ↓
Producto 4
Producto 5
Producto 6
```

Esto permite reducir la cantidad de información cargada inicialmente y mejorar el comportamiento de la aplicación cuando se trabaja con listas grandes.

---

# 🖼️ Carga de imágenes

Para la carga de imágenes se utiliza **Coil**, una librería optimizada para Android y desarrollada en Kotlin.

Su utilización permite cargar imágenes de forma asíncrona sin bloquear el hilo principal y facilita su integración con la interfaz.

---

# 🧭 Navegación

La aplicación utiliza **Android Navigation Component** para gestionar la navegación entre las diferentes pantallas.

Esto permite centralizar y controlar las rutas de la aplicación, evitando gestionar manualmente las transiciones entre Fragments.

---

# 📦 Estructura conceptual

La organización del proyecto sigue una separación similar a:

```text
app/
└── src/
    └── main/
        └── java/
            └── com.example.modu/
                │
                ├── data/
                │   ├── remote/
                │   ├── local/
                │   ├── repository/
                │   └── dto/
                │
                ├── domain/
                │   ├── model/
                │   ├── repository/
                │   └── usecase/
                │
                ├── presentation/
                │   ├── fragments/
                │   ├── viewmodel/
                │   └── adapter/
                │
                └── di/
```

La estructura concreta puede variar según el módulo o funcionalidad, pero el objetivo es mantener separadas las responsabilidades de cada parte de la aplicación.

---

# ⚙️ Requisitos

Para ejecutar el proyecto se recomienda disponer de:

* Android Studio.
* JDK 21.
* Android SDK 36.
* Gradle.
* Dispositivo Android físico o emulador.

El proyecto establece **Android 28 como versión mínima (Android 9)** y utiliza **SDK 36 como versión de compilación y objetivo**.

---

# 🚀 Instalación

Clonar el repositorio:

```bash
git clone https://github.com/Mohamed2651/Modu-practicas-Rudo-Laberit.git
```

Abrir el proyecto con **Android Studio** y esperar a que Gradle sincronice todas las dependencias.

Después, seleccionar un dispositivo físico o emulador y ejecutar la aplicación.

---

# 🎯 Objetivos del proyecto

Este proyecto fue desarrollado durante las prácticas con el objetivo de adquirir experiencia en un entorno profesional de desarrollo Android.

Los principales objetivos fueron:

* Mejorar el desarrollo de aplicaciones Android con Kotlin.
* Trabajar con una arquitectura escalable.
* Aplicar los principios de Clean Architecture.
* Consumir APIs REST.
* Trabajar con bases de datos locales.
* Utilizar inyección de dependencias.
* Implementar navegación entre pantallas.
* Trabajar con listas y paginación.
* Gestionar imágenes remotas.
* Familiarizarse con un flujo de desarrollo más cercano al utilizado en proyectos profesionales.

---

# 📚 Aprendizaje

El desarrollo de **Modu** permitió trabajar con diferentes partes del ciclo de desarrollo de una aplicación Android, desde la creación de la interfaz hasta la comunicación con servicios externos y la persistencia local.

Uno de los principales aprendizajes del proyecto fue entender la importancia de **separar responsabilidades** mediante una arquitectura organizada.

La aplicación no se limita únicamente a mostrar información, sino que integra diferentes componentes:

```text
        ┌─────────────┐
        │     UI      │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │  ViewModel  │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │  Use Cases  │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │ Repository  │
        └──────┬──────┘
             ┌─┴─┐
             ▼   ▼
          API      Room
```

Esto permite mantener el proyecto más organizado y facilita su evolución a medida que aumentan las funcionalidades.

---

## 👨‍💻 Autor

**Mohammed Shahin**

Desarrollador de aplicaciones multiplataforma (DAM).

GitHub:
https://github.com/Mohamed2651

---

## 🏢 Contexto

Proyecto desarrollado durante el periodo de prácticas en **Rudo / Laberit** como parte de la formación profesional en Desarrollo de Aplicaciones Multiplataforma (DAM).

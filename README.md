[README.md](https://github.com/user-attachments/files/28739274/README.md)
# ☕ Aurora Coffee — Sistema de Gestión para Cafetería

> Proyecto académico desarrollado para la asignatura de Programación en Java  
> Universidad — 2026

---

## 📋 Descripción

Sistema web completo para la gestión de la cafetería **Aurora Coffee**, desarrollado con **Spring Boot**. Permite a los clientes explorar el catálogo de productos, agregar items al carrito, generar pedidos y hacer reservas de mesa. Los administradores cuentan con un panel de control para gestionar productos, reservas y pedidos en tiempo real.

---

## 👥 Integrantes

| Nombre | Rol |
|--------|-----|
| Jordan Alejandro Herrera Gómez | Desarrollador Full Stack |
| Jaider Augusto Niño Badillo | Desarrollador Full Stack |
| Julio Cesar Silva Hernández | Desarrollador Full Stack |

---

## 🚀 Funcionalidades

### 👤 Cliente
- Registro e inicio de sesión con autenticación segura
- Navegación del catálogo por categorías (11 categorías, +100 productos)
- Carrito de compras con gestión de cantidades por sesión
- Generación de factura con detalle de productos y total
- Historial de pedidos realizados
- Formulario de reserva de mesa con fecha, hora y número de personas
- Consulta y cancelación de reservas propias

### 🛠️ Administrador
- Panel de control con sidebar de navegación
- CRUD completo de productos (crear, editar, bloquear/activar, eliminar)
- Subida de imágenes de productos con drag & drop
- Buscador en tiempo real sobre la tabla de productos
- Gestión de reservas con cambio de estado (Pendiente → Confirmada → Cancelada)
- Gestión de pedidos con resumen estadístico (total pedidos, pendientes, pagados, recaudado)
- Marcado de pedidos como pagados

---

## 🏗️ Arquitectura del Proyecto

El proyecto sigue la arquitectura en capas estándar de Spring Boot:

```
com.aurora.coffee
│
├── controller/      → Controladores MVC (@Controller)
├── model/           → Entidades JPA (@Entity)
├── repository/      → Interfaces Spring Data JPA
├── service/         → Lógica de negocio (@Service)
├── security/        → Configuración Spring Security
└── config/          → Configuración Web (recursos estáticos, multipart)
```

```
src/main/resources/
│
├── templates/       → Vistas Thymeleaf (.html)
│   └── admin/       → Vistas del panel administrativo
├── static/
│   ├── CSS/         → Hojas de estilo
│   ├── JS/          → Scripts
│   └── IMG2/        → Imágenes originales del catálogo
└── application.properties
```

---

## 🗄️ Modelo de Base de Datos

El sistema utiliza **6 tablas** relacionadas en MySQL:

| Tabla | Descripción |
|-------|-------------|
| `usuarios` | Clientes y administradores del sistema |
| `categorias` | Categorías del menú (Cappuccinos, Frappes, Cocteles, etc.) |
| `productos` | Productos del catálogo con precio, imagen y disponibilidad |
| `reservas` | Reservas de mesa por usuario con fecha, hora y estado |
| `facturas` | Pedidos generados desde el carrito |
| `detalle_factura` | Líneas de cada pedido (producto, cantidad, subtotal) |

### Relaciones principales
- Un `Usuario` puede tener múltiples `Reservas` y `Facturas`
- Un `Producto` pertenece a una `Categoria`
- Una `Factura` contiene múltiples `DetalleFactura`
- Cada `DetalleFactura` referencia un `Producto`

---

## 🔐 Seguridad

Implementada con **Spring Security 7**:

- Autenticación basada en formulario con usuario y contraseña
- Contraseñas cifradas con **BCrypt**
- Dos roles definidos: `ROLE_ADMIN` y `ROLE_CLIENT`
- Rutas protegidas por rol:
  - `/admin/**` → solo `ROLE_ADMIN`
  - `/carrito/**`, `/reservas/**`, `/facturas/**` → usuarios autenticados
  - `/`, `/catalogo/**`, `/login`, `/registro` → acceso público
- Redirección automática post-login según rol
- Protección CSRF activa en todos los formularios
- Gestión de sesión con invalidación al cerrar sesión

---

## 🛒 Flujo del Carrito y Facturación

1. El cliente navega el catálogo y agrega productos al carrito
2. El carrito se almacena en la **sesión HTTP** (`HttpSession`)
3. Al confirmar el pedido, `FacturaService` genera una `Factura` con sus `DetalleFactura`
4. Se calcula el subtotal por ítem y el total general automáticamente
5. El cliente es redirigido a la vista de detalle de su factura
6. El administrador puede ver todos los pedidos y marcarlos como pagados

---

## 🖼️ Gestión de Imágenes

El sistema maneja dos orígenes de imágenes:

- **Imágenes originales**: almacenadas en `src/main/resources/static/IMG2/` y servidas en `/IMG2/`
- **Imágenes subidas por el admin**: almacenadas en `C:\Users\{usuario}\aurora-coffee-imagenes\` (carpeta externa al JAR/WAR) y servidas en `/imagenes-subidas/`

Este diseño garantiza que las imágenes persistan correctamente al empaquetar el proyecto como `.war` para despliegue en Tomcat.

---

## 🧰 Tecnologías Utilizadas

| Tecnología | Versión | Uso |
|------------|---------|-----|
| Java | 17 | Lenguaje principal |
| Spring Boot | 4.0.6 | Framework base, servidor embebido Tomcat |
| Spring Security | 7.0.5 | Autenticación, autorización y protección CSRF |
| Spring Data JPA | — | Persistencia y consultas a BD con Hibernate |
| Thymeleaf | 3.1.5 | Motor de plantillas HTML del lado servidor |
| MySQL | 8.x | Base de datos relacional |
| Hibernate | 7.2.x | ORM para mapeo objeto-relacional |
| Lombok | — | Reducción de código boilerplate (getters, setters, builders) |
| Maven | 3.x | Gestión de dependencias y construcción del proyecto |
| Font Awesome | 6.5 | Íconos en la interfaz |
| Tailwind CSS | CDN | Estilos en vistas del catálogo |
| DBeaver | — | Cliente gráfico para administración de MySQL |
| Git & GitHub | — | Control de versiones y colaboración |
| Spring Tools Suite (STS) | 4.x | IDE de desarrollo |

---

## ⚙️ Instalación y Ejecución

### Prerrequisitos
- Java 17 o superior
- MySQL 8.x corriendo en `localhost:3306`
- Maven 3.x
- Spring Tools Suite 4 (o IntelliJ IDEA)

### Pasos

**1. Clonar el repositorio**
```bash
git clone https://github.com/usuario/aurora-coffee.git
cd aurora-coffee
```

**2. Crear la base de datos en MySQL**
```sql
CREATE DATABASE aurora_coffee CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

**3. Configurar credenciales**  
Editar `src/main/resources/application.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/aurora_coffee
spring.datasource.username=root
spring.datasource.password=TU_CONTRASEÑA
```

**4. Ejecutar el proyecto**  
Desde STS: clic derecho en el proyecto → `Run As` → `Spring Boot App`  
O desde terminal:
```bash
mvn spring-boot:run
```

**5. Acceder a la aplicación**  
Abrir en el navegador: `http://localhost:8080`

### Usuarios de prueba

| Correo | Contraseña | Rol |
|--------|-----------|-----|
| `admin@auroracoffee.com` | `admin123` | Administrador |
| `cliente@auroracoffee.com` | `cliente123` | Cliente |

---

## 📁 Estructura de Archivos Relevantes

```
aurora-coffee/
├── pom.xml
├── README.md
└── src/
    └── main/
        ├── java/com/aurora/coffee/
        │   ├── AuroraCoffeeApplication.java
        │   ├── config/
        │   │   └── WebConfig.java
        │   ├── controller/
        │   │   ├── AuthController.java
        │   │   ├── CarritoController.java
        │   │   ├── FacturaController.java
        │   │   ├── HomeController.java
        │   │   ├── ImagenController.java
        │   │   ├── ProductoController.java
        │   │   └── ReservaController.java
        │   ├── model/
        │   │   ├── Categoria.java
        │   │   ├── DetalleFactura.java
        │   │   ├── Factura.java
        │   │   ├── Producto.java
        │   │   ├── Reserva.java
        │   │   └── Usuario.java
        │   ├── repository/
        │   │   ├── CategoriaRepository.java
        │   │   ├── FacturaRepository.java
        │   │   ├── ProductoRepository.java
        │   │   ├── ReservaRepository.java
        │   │   └── UsuarioRepository.java
        │   ├── security/
        │   │   ├── SecurityConfig.java
        │   │   └── UserDetailsServiceImpl.java
        │   └── service/
        │       ├── CategoriaService.java
        │       ├── FacturaService.java
        │       ├── ProductoService.java
        │       ├── ReservaService.java
        │       └── UsuarioService.java
        └── resources/
            ├── application.properties
            ├── data.sql
            └── templates/
                ├── admin/
                │   ├── editar-producto.html
                │   ├── facturas.html
                │   ├── productos.html
                │   └── reservas.html
                ├── carrito.html
                ├── catalogo.html
                ├── factura-detalle.html
                ├── fragments.html
                ├── index.html
                ├── login.html
                ├── mis-facturas.html
                ├── mis-reservas.html
                ├── registro.html
                └── reserva.html
```

---

## 📌 Notas Académicas

- El proyecto fue migrado desde una versión estática en HTML/CSS/JS con datos en JSON hacia una arquitectura completa MVC con Spring Boot y MySQL.
- Se implementó el patrón Repositorio con Spring Data JPA, eliminando la necesidad de escribir SQL manualmente para las operaciones CRUD.
- La seguridad fue implementada siguiendo las mejores prácticas de Spring Security 7, incluyendo hashing BCrypt y protección CSRF.
- El carrito de compras fue implementado usando sesiones HTTP en lugar de `localStorage` para garantizar consistencia entre dispositivos y compatibilidad con el backend.

---

*Aurora Coffee — Donde cada taza cuenta una historia* ☕

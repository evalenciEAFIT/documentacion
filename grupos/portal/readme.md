# Portal Web (Main Shell)

> **Interfaz Unificada de Acceso.**
> Este proyecto es la aplicación principal (Single Page Application) que actúa como contenedor para los módulos de negocio (Geoportal, Dashboards, Estudios, etc.). Gestiona la autenticación, la navegación global y el layout base.

![Status](https://img.shields.io/badge/status-active-success)
![Framework](https://img.shields.io/badge/framework-React%20%7C%20Vite-blue)
![Auth](https://img.shields.io/badge/auth-OAuth2%20%2F%20OIDC-orange)

## Visión General

El **Portal Web** no contiene lógica de negocio pesada. Su responsabilidad es:
1.  **Autenticar** al usuario.
2.  **Renderizar la estructura base** (Navbar, Sidebar, Footer).
3.  **Enrutar** el tráfico hacia los módulos específicos (Lazy Loading).
4.  **Mantener el estado global** (Usuario, Tema, Idioma).

### Arquitectura de UI (Shell Pattern)

```mermaid
graph TD
    Entry(index.html) --> App(App.jsx)
    App --> AuthProvider[Contexto de Autenticación]
    AuthProvider --> MainLayout[Layout Principal]
    
    subgraph "Layout Shell"
        MainLayout --> Navbar
        MainLayout --> Sidebar
        MainLayout --> ContentArea[Router Outlet]
    end
    
    ContentArea -.->|Lazy Load| Home[Vista Inicio]
    ContentArea -.->|Lazy Load| Geo[Vista Geoportal]
    ContentArea -.->|Lazy Load| Dash[Vista Dashboards]
    ContentArea -.->|Lazy Load| Admin[Vista Administración]
````

-----

## 📂 Estructura del Proyecto

La estructura está diseñada para separar el "Shell" de los "Módulos".

```text
src/
├── assets/             # Logos, fuentes e imágenes estáticas del portal
├── components/         # Componentes GLOBALES del Layout
│   ├── layout/         # MainLayout, AuthLayout, EmptyLayout
│   ├── navigation/     # Navbar, Sidebar, Breadcrumbs
│   └── common/         # Loaders, ErrorBoundaries (Pantallas de error)
├── context/            # Estado global de la aplicación
│   ├── AuthContext.jsx # Manejo de token y sesión
│   └── UIContext.jsx   # Estado del Sidebar (abierto/cerrado), Tema
├── router/             # Definición de rutas y protección
│   ├── routes.js       # Mapa de rutas
│   └── PrivateRoute.jsx # Guard (Verifica si está logueado)
├── views/              # Páginas de alto nivel
│   ├── auth/           # Login, Recuperar Contraseña
│   ├── home/           # Landing Page interna
│   └── errors/         # 404, 500
├── App.jsx             # Punto de entrada
└── main.jsx            # Montaje de React
```

-----

## 🛠 Características Técnicas Clave

### 1\. Enrutamiento y Lazy Loading

Para optimizar el rendimiento, los módulos pesados (como el Geoportal o Dashboards) no se cargan al inicio. Se cargan bajo demanda usando `React.lazy`.

*Archivo: `src/router/routes.js`*

```javascript
import { lazy } from 'react';

// Carga perezosa de módulos
const GeoportalView = lazy(() => import('@/modules/geoportal/pages/MainView'));
const DashboardView = lazy(() => import('@/modules/dashboards/pages/Overview'));

export const appRoutes = [
  {
    path: '/mapa',
    component: GeoportalView,
    layout: 'main',
    roles: ['user', 'admin'] // RBAC
  },
  {
    path: '/tableros',
    component: DashboardView,
    layout: 'main',
    roles: ['analyst', 'admin']
  }
];
```

### 2\. Gestión de Autenticación (Auth)

El portal maneja el token JWT. Si el token expira o no existe, el `PrivateRoute` redirige al usuario a `/login`.

  * **Login:** Almacena token en `localStorage` / `Cookie`.
  * **Interceptor:** Axios está configurado para inyectar el token en cada petición a los microservicios.

### 3\. Layouts Dinámicos

El portal soporta múltiples layouts según la ruta:

  * **`MainLayout`:** Con Sidebar y Navbar (para la app interna).
  * **`AuthLayout`:** Centro limpio (para Login/Registro).
  * **`FullscreenLayout`:** Sin bordes (para el modo inmersivo del Geoportal).

-----

## 🚀 Instalación y Ejecución

### Prerrequisitos

  * Node.js v18+
  * Acceso al `.npmrc` (si hay librerías privadas).

### Pasos

1.  **Instalar dependencias:**

    ```bash
    npm install
    ```

2.  **Configurar entorno:**
    Crear archivo `.env` en la raíz.

    ```bash
    VITE_API_URL=http://localhost:3000/api
    VITE_APP_NAME="Portal Integrador"
    VITE_DEFAULT_THEME=light
    ```

3.  **Correr en desarrollo:**

    ```bash
    npm run dev
    ```

    El portal estará disponible en `http://localhost:5173`.

-----

## 🎨 Guía de Estilos (Theming)

El portal utiliza **CSS Variables** o **Tailwind** para definir el tema global que heredarán los módulos.

```css
:root {
  /* Colores Corporativos */
  --primary-color: #0056b3;
  --secondary-color: #17a2b8;
  
  /* Layout */
  --sidebar-width: 260px;
  --navbar-height: 64px;
}
```

Si desarrollas un nuevo módulo, **NO** hardcodes colores hexadecimales. Usa las variables CSS para asegurar consistencia visual si cambiamos el branding.

-----

## 📦 Build y Despliegue

Para producción, el portal se compila en archivos estáticos optimizados.

```bash
npm run build
```

Esto generará la carpeta `/dist`.

  * **Servidor Web:** Configurar Nginx para redirigir todas las rutas (`/*`) al `index.html` (necesario para SPA Routing).
  * **Docker:** Usa el `Dockerfile` ubicado en la raíz para generar la imagen de producción.

<!-- end list -->

```dockerfile
# Ejemplo simplificado
FROM nginx:alpine
COPY ./dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

-----

## 🤝 Contribución

1.  Las vistas principales deben agregarse en `src/views` solo si no pertenecen a un módulo de negocio específico.
2.  Si necesitas agregar un ítem al menú lateral, edita `src/config/navigation.js`.
3.  Respeta las reglas de ESLint configuradas.

<!-- end list -->

```
```

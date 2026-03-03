---
sidebar_position: 2
---

# Instalación del frontend

## Dependencias

### `Framework y renderizado`

Next.js + React + React DOM (_next, react, react-dom_): Construyen la aplicación web, gestionan páginas, rutas, renderizado y la interfaz de usuario.

### `Estilos y UI`

Tailwind CSS stack (_tailwindcss, @tailwindcss/postcss, tailwind-merge, tw-animate-css_): Sistema de estilos por clases, animaciones y gestión de conflictos entre estilos.

Sistema de componentes (_radix-ui, shadcn, class-variance-authority, clsx_): Creación de componentes reutilizables, accesibles y con estilos dinámicos según estado/props.

Iconos (_lucide-react_): Iconos SVG listos para usar como componentes React.

### `PWA / Offline / Caché`

Service Workers / Offline (_serwist, @serwist/next_): Permiten que la app cargue más rápido, funcione sin conexión y guarde datos en caché.

### `Tipado`

TypeScript + tipos (_typescript, @types/node, @types/react, @types/react-dom_): Tipado estático, autocompletado y detección de errores antes de ejecutar la app.

### `Calidad de código`

Linting (_eslint, eslint-config-next_): Detectan errores, malas prácticas y mantienen consistencia en el código.

### Configurar el entorno

Para el frontend actualmente necesita la dirección del backend para hacer llamadas a este.

Para ello es necesario copiar la plantilla `.env.example`:

```bash
cp .env.example .env
```

por defecto, la ruta del backend puesta en el example es *https://localhost:8080*. 
Si se desea cambiar por la suya del backend, simplemente ir al .env creado y cambiar la URL por la deseada

## Instalación

Todas las dependencias están en el package.json, ejecutando el siguiente comando se debería de instalar todo lo necesario para ejecutar el frontend:

```bash
npm install
```

Luego para ejecutar en local:

```bash
npm run dev
```

Tambien se tiene un archivo _docker-compose_ por si se quiere ejecutar en un contenedor de docker.

Para ello, se debera tener docker instalado e iniciado (para usuarios de Windows se necesita tener [Docker Desktop](https://docs.docker.com/desktop/setup/install/windows-install/))

Luego de tener iniciado Docker, ejecutar para iniciar el contenedor:

```bash
docker compose -d up
```

para apagarlo:

```bash
docker compose down
```

### Hooks

Para mantener consistencia en el desarrollo y hacer que lo que llegue a Github siga buenas prácticas se hará uso de ciertos hooks pre-commit. A continuación, una lista con aquellos disponibles en la carpeta `.husky/`:

- **pre-commit**: Para detectar errores de linting.

- **commit-msg**: Para detectar no seguimientos de Conventional Commits.

#### Configuración:

Para que ambos empiecen a funcionar, ejecute el siguiente comando:

```bash
npm run prepare-husky
```

Por último, antes de comenzar, se aconseja encarecidamente consultar la sección de [linting automático para el frontend](../guia-de-desarrollo/lint.md#linting-del-frontend).

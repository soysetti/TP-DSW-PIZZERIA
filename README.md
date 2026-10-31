# TP DSW — Pizzería

Aplicación web para la gestión de una pizzería, con catálogo de productos, pedidos, control de ingredientes, envíos a domicilio y pagos de prueba con Mercado Pago.

El proyecto está dividido en dos repositorios independientes: frontend y backend. Cada uno incluye su propia documentación de instalación, configuración y pruebas.

## Repositorios

- **Frontend:** [tp-dsw-pizzeria-frontend](https://github.com/jcolumbich/tp-dsw-pizzeria-frontend)
- **Backend:** [tp-dsw-pizzeria-backend](https://github.com/jcolumbich/tp-dsw-pizzeria-backend)

## Aplicación desplegada

- **Frontend (Vercel):** [tp-dsw-pizzeria-frontend.vercel.app](https://tp-dsw-pizzeria-frontend.vercel.app/)
-  **Backend:** Render.
- **Base de datos:** MySQL en Aiven.

## Grupo

| Legajo | Integrante |
| --- | --- |
| 53723 | Columbich, Julian |
| 55158 | Setti, Francisco |

## Propuesta

### Tema

**Pizzería**

### Descripción

Sistema de gestión para pizzerías que integra pedidos y envíos a domicilio. Controla el stock de ingredientes y la disponibilidad de productos para optimizar la operativa del negocio.

El sistema tiene dos tipos de usuario:

- **Cliente (nivel 0):** consulta la carta, se registra, arma pedidos y sigue su estado.
- **Administrador (nivel 1):** gestiona pizzas, ingredientes, repartidores, clientes, pedidos y envíos.

Al crear un pedido se descuenta de forma transaccional el stock de ingredientes. Al cancelarlo, se repone de acuerdo con las reglas del sistema.

### Modelo

[Ver diagrama de clases](https://drive.google.com/file/d/1xWHBcCBiaYEYtk39Oxa8nYyFgnX6Xuhz/view?usp=sharing).

### Video
[Ver video prueba](https://drive.google.com/file/d/13SYLFEC47d4B_zVKcOgjAeWfLTgKnigb/view?usp=sharing).

## Alcance funcional

### Alcance mínimo

| Requisito | Detalle |
| --- | --- |
| CRUD simple | 1. CRUD Ingrediente.<br>2. CRUD Repartidor. |
| CRUD dependiente | CRUD Pizza, que depende de Ingrediente a través de la entidad intermedia `IngredientePizza`, donde se registra la cantidad de cada ingrediente por pizza. |
| Listado y detalle | Listado de pedidos filtrado por estado, con número de pedido, fecha y total. El detalle muestra pizzas, cantidades, precios y datos del cliente. |
| CUU / Epic | Registrar un nuevo pedido para un cliente con sus respectivos ítems. |

### Adicionales para aprobación

Estos requerimientos complementan el alcance mínimo.

| Requisito | Detalle |
| --- | --- |
| CRUD | 1. Ingrediente.<br>2. Repartidor.<br>3. Cliente.<br>4. Pizza.<br>5. Pedido y sus ítems (`DetallePedido`). Los ítems se crean junto con el pedido y se consultan como parte de él. El módulo `detalle-pedido` es de solo lectura. La operación `DELETE /api/pedidos/:id` cancela el pedido, repone el stock una sola vez y conserva el historial.<br>6. Envío: operaciones CRUD disponibles en el backend; en el frontend, su gestión está integrada al detalle del pedido. |
| CUU / Epic | 1. Registrar un nuevo pedido calculando el total automáticamente, validando disponibilidad y descontando stock de manera transaccional.<br>2. Asignar un envío a un repartidor, registrando su costo y actualizando el estado del pedido. El pedido debe estar *En preparación*, no ser para retiro y no tener envío previo; el repartidor debe estar activo. |
| Autenticación y permisos | Registro e inicio de sesión con JWT y contraseñas protegidas con bcrypt. Dos niveles: cliente (0) y administrador (1). El catálogo es público; la administración está restringida por permisos. Los clientes pueden crear y consultar sus propios pedidos. |
| Validación y errores | Validación de datos de entrada mediante Zod y validaciones de parámetros, con manejo centralizado de errores y códigos HTTP apropiados. |
| Pruebas | Backend: Jest, con pruebas unitarias y de integración contra MySQL. Frontend: Vitest, Testing Library y Playwright para pruebas end-to-end. |
| Ambientes | Configuración mediante archivos `.env`, `.env.test` y sus ejemplos correspondientes. |
| Documentación de la API | Referencia de endpoints y archivos `.http` con ejemplos de las operaciones. |

### Alcance adicional voluntario

Funcionalidades incorporadas para completar el flujo del negocio.

| Requisito | Detalle |
| --- | --- |
| Listados | Búsqueda, filtros y paginación de pizzas; historial de pedidos con filtros por estado, actividad y repartidor; búsqueda y paginación de clientes y repartidores. |
| Gestión de pedidos | Ciclo de estados: *Esperando pago*, *Pendiente*, *En preparación*, *En camino*, *Entregado* y *Cancelado*. Cancelación con reposición de ingredientes, control transaccional de stock, retiro en local o envío a domicilio y propina al repartidor. |
| Pagos | Integración con Mercado Pago en modo prueba, con checkout y webhook firmado. |
| Imágenes | Carga y procesamiento de imágenes de pizzas con `sharp`, almacenadas en la base de datos. |
| Frontend | Carta pública, creación y seguimiento de pedidos, detalle de pedidos, pantallas de administración y asignación de envíos desde el detalle del pedido. |

## Tecnologías

| Frontend | Backend | Base de datos |
| --- | --- | --- |
| React, TypeScript, Vite | Node.js, Express, TypeScript, MikroORM | MySQL 8 |

También se utilizan JWT y bcrypt para autenticación, Zod para validaciones, Mercado Pago en modo prueba y herramientas de pruebas como Jest, Vitest y Playwright.

## Instalación y ejecución

Clonar los dos repositorios por separado:

```bash
git clone https://github.com/jcolumbich/tp-dsw-pizzeria-backend.git
git clone https://github.com/jcolumbich/tp-dsw-pizzeria-frontend.git
```

Cada aplicación se configura y ejecuta desde su propia carpeta. La documentación técnica corresponde a cada repositorio:

1. [README del backend](https://github.com/jcolumbich/tp-dsw-pizzeria-backend#readme).
2. [README del frontend](https://github.com/jcolumbich/tp-dsw-pizzeria-frontend#readme).

Para ejecutar el sistema localmente se necesitan Node.js, pnpm y MySQL. Se debe iniciar el backend con la base de datos configurada y luego el frontend.

## Documentación de la API

- [Referencia de endpoints](https://github.com/jcolumbich/tp-dsw-pizzeria-backend/blob/main/docs/api.md).
- [Ejemplos de solicitudes HTTP](https://github.com/jcolumbich/tp-dsw-pizzeria-backend/tree/main/src) (archivos `.http` en los módulos del backend).

## Pull requests

- [Backend — PR #4](https://github.com/jcolumbich/tp-dsw-pizzeria-backend/pull/4).
- [Frontend — PR #1](https://github.com/jcolumbich/tp-dsw-pizzeria-frontend/pull/1).

## Material de la cátedra

- [Consigna del TP DSW](https://github.com/utnfrrodsw/tp).
- [Material oficial de backend](https://github.com/utnfrrodsw/material-be).
- [Material de frontend](https://github.com/aotaduy/curso-frontend-react).

## Contacto para la defensa

- **Columbich, Julian:** [jcolumbich@gmail.com](mailto:jcolumbich@gmail.com).
- **Setti, Francisco:** [settifrancisco.esc5032@gmail.com](mailto:settifrancisco.esc5032@gmail.com).

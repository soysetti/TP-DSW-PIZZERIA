# Pruebas automatizadas — TP DSW Pizzería

Fecha de los resultados: 30 de octubre de 2026.

Este documento presenta las pruebas automatizadas del backend y frontend, los comandos para ejecutarlas y sus resultados. Incluye ejemplos de lógica de negocio, integración con MySQL, componentes de React y recorridos de navegador.

## Versiones de referencia

| Repositorio | Commit |
| --- | --- |
| [Backend](https://github.com/jcolumbich/tp-dsw-pizzeria-backend) | `8e3ab45` |
| [Frontend](https://github.com/jcolumbich/tp-dsw-pizzeria-frontend) | `48e0499` |

Los resultados unitarios corresponden a esas versiones publicadas. La integración y Playwright se completaron posteriormente en Windows según las salidas aportadas por el equipo; sus commits exactos no figuran en esas salidas.

## Ejemplos seleccionados

| Requisito | Archivo | Caso y resultado esperado |
| --- | --- | --- |
| Backend: ejemplo 1 | [pedido.logic.test.ts](https://github.com/jcolumbich/tp-dsw-pizzeria-backend/blob/8e3ab45/src/pedido/pedido.logic.test.ts) | `calcularTotalPedido`: para 3 unidades de precio 1500 devuelve 4500; también verifica la suma de varios ítems. |
| Backend: ejemplo 2 | [pedido.validaciones.test.ts](https://github.com/jcolumbich/tp-dsw-pizzeria-backend/blob/8e3ab45/src/pedido/pedido.validaciones.test.ts) | `validarPizzaDisponible`: una pizza disponible no produce error; una no disponible genera `HttpError` con estado 400. |
| Backend: integración | [ingrediente.integration.test.ts](https://github.com/jcolumbich/tp-dsw-pizzeria-backend/blob/8e3ab45/src/ingrediente/ingrediente.integration.test.ts) | Integra API, autenticación, permisos, ORM y MySQL. Comprueba 401 sin sesión, 403 para cliente, rechazo de stock negativo y CRUD de ingredientes con stock fraccionario. |
| Frontend: componente | [IngredientesList.test.tsx](https://github.com/jcolumbich/tp-dsw-pizzeria-frontend/blob/48e0499/src/views/ingrediente/IngredientesList.test.tsx) | Renderiza el componente con el servicio simulado y verifica que aparezcan los nombres de los dos ingredientes devueltos. |
| Frontend: recorrido de navegador | [checkout.spec.ts](https://github.com/jcolumbich/tp-dsw-pizzeria-frontend/blob/48e0499/e2e/checkout.spec.ts) | Agrega una pizza y confirma el pedido. Cubre retiro y delivery, combinados con efectivo y Mercado Pago; verifica los datos enviados y la confirmación o redirección simulada. |

### Alcance de los tests de navegador

Playwright utiliza Chromium real, pero intercepta las solicitudes y simula las respuestas de la API y el checkout de Mercado Pago. No necesita el backend iniciado, cuentas reales ni realizar pagos. Su evidencia demuestra los recorridos del frontend bajo esas respuestas; no demuestra una compra completa contra el backend y el proveedor reales.

La suite contiene siete casos en tres archivos: cuatro de checkout, dos de búsquedas/filtros y uno de login.

## Ejecución

Requisitos generales: Node.js compatible con el proyecto, pnpm y dependencias instaladas con `pnpm install --frozen-lockfile` en cada repositorio.

### Backend: pruebas unitarias

Desde la carpeta del backend:

```powershell
pnpm run test:unit
```

Compila TypeScript y ejecuta las pruebas que no requieren una base MySQL ni llamadas reales a Mercado Pago.

### Backend: integración

Configurar una base exclusiva para pruebas siguiendo el README del backend. Crear `.env.test` a partir de `.env.test.example` y definir `DB_TEST_URL`. El nombre de la base debe terminar en `_test` y ser distinto de la base habitual.

```powershell
pnpm run test:integration
```

La integración crea usuarios e ingredientes temporales y elimina sus propias filas al finalizar. `pnpm test` ejecuta juntas las pruebas unitarias y de integración, por lo que también necesita esa configuración.

### Frontend: componentes y lógica

Desde la carpeta del frontend:

```powershell
pnpm test
```

### Frontend: navegador

Instalar Chromium si todavía no está disponible y ejecutar:

```powershell
pnpm exec playwright install chromium
pnpm run test:e2e
```

Playwright inicia Vite automáticamente cuando es necesario.

## Resultados de ejecución

Entorno de ejecución de las pruebas unitarias: Linux, Node.js `24.22.1`, pnpm `11.25.0` y dependencias instaladas desde los lockfiles de los commits indicados.

| Grupo | Resultado confirmado | Estado |
| --- | --- | --- |
| Backend, `pnpm run test:unit` | 9 suites aprobadas, 63 tests aprobados; compilación TypeScript correcta | Ejecutado satisfactoriamente |
| Frontend, `pnpm test` | 9 archivos aprobados, 28 tests aprobados | Ejecutado satisfactoriamente |
| Backend, integración | 1 suite y 4 tests aprobados con MySQL real, en 6.209 s | Ejecutado por el equipo en Windows |
| Frontend, Playwright | 7 tests aprobados, en 21.6 s | Ejecutado por el equipo en Windows |

Resumen de pruebas unitarias del backend:

```text
Test Suites: 9 passed, 9 total
Tests:       63 passed, 63 total
Snapshots:   0 total
Time:        1.471 s
Ran all test suites.
```

Resumen de pruebas de componentes y lógica del frontend:

```text
Test Files  9 passed (9)
     Tests  28 passed (28)
  Duration  3.27s
```

### Integración y navegador: ejecución del equipo

Las siguientes ejecuciones se realizaron en Windows PowerShell. No se informaron los commits exactos ni las versiones del entorno local.

Comando de backend: `pnpm run test:integration`.

```text
PASS dist/ingrediente/ingrediente.integration.test.js (5.961 s)
API de ingredientes con MySQL real
  √ rechaza la consulta sin sesión (13 ms)
  √ rechaza un cliente autenticado sin permisos de administrador (22 ms)
  √ rechaza stock negativo antes de persistir (23 ms)
  √ crea, consulta, lista, actualiza y elimina un ingrediente con stock fraccionario (107 ms)

Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        6.209 s, estimated 18 s
Ran all test suites matching integration.
```

Comandos de frontend: `pnpm exec playwright install chromium` y `pnpm run test:e2e`.

```text
Running 7 tests using 6 workers
7 passed (21.6s)
```

Las pruebas de navegador utilizan Chromium real con API y checkout de Mercado Pago simulados.

5. Guardar este documento como `docs/pruebas.md`, enlazarlo desde `docs/README.md` y enlazar ese índice desde el README principal.

Los campos recomendados —fecha, commit, comando, entorno, resultado y alcance de mocks— permiten interpretar y reproducir la evidencia. La consigna exige evidencia de ejecución, pero no impone esta plantilla concreta ni un porcentaje mínimo de cobertura.

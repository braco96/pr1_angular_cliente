
# 📦 Basico — Chat en tiempo real con Angular + WebSockets

Aplicación Angular que permite enviar y recibir mensajes en tiempo real usando **ngx-socket-io** contra un servidor WebSocket configurable. Incluye control de sesión mínima (usuario en `localStorage`), guard de rutas y componentes básicos para **login**, **mensajes**, **lista de usuarios** y **estado del socket**.

---

## ✨ Características

- 🧑‍💻 Login simple por nombre de usuario (persistido en `localStorage`).
- 🔌 Conexión a WebSocket usando `ngx-socket-io`.
- 💬 Envío y recepción de mensajes en tiempo real.
- 🛡️ Guard de ruta (`UsuarioGuard`) que bloquea `/mensajes` si no hay usuario.
- 📡 Indicador de estado de conexión (Online/Offline) en el footer.
- 🧪 Estructura de pruebas unitarias generadas con Angular CLI.

---

## 🏗️ Tecnologías

- **Angular** (CLI)
- **RxJS**
- **ngx-socket-io**
- **Bootstrap 4** (CDN)
- **Karma + Jasmine** (tests)

---

## 🚀 Puesta en marcha

> Requisitos: Node.js LTS, Angular CLI, y un **servidor WebSocket** disponible.

1. **Instalar dependencias**
   ```bash
   npm install
   ```

2. **Configurar el endpoint del WebSocket**

   Edita `src/environments/environment.ts` (desarrollo) y/o `src/environments/environment.prod.ts` (producción):

   ```ts
   export const environment = {
     production: false,
     wsUrl: 'http://localhost:5000' // <— Cambia a tu servidor
   };
   ```

3. **Arrancar la app**
   ```bash
   ng serve
   ```
   Visita: http://localhost:4200

4. **Servidor WebSocket**
   - La app espera un servidor en `environment.wsUrl` que escuche y emita:
     - `configurar-usuario` (para registrar el usuario)
     - `mensaje` (evento de salida al enviar)
     - `mensaje-nuevo` (evento de entrada para mensajes públicos)
     - `mensaje-privado` (evento de entrada para privados)
   - Ajusta los nombres de eventos en `WebsocketService`/`ChatService` si tu backend usa otros.

---

## 🗺️ Estructura del proyecto (resumen)

```
src/
├─ index.html
├─ styles.css
├─ polyfills.ts
├─ main.ts
├─ environments/
│  ├─ environment.ts
│  └─ environment.prod.ts
└─ app/
   ├─ app.module.ts
   ├─ app-routing.module.ts
   ├─ app.component.{ts,html,css,spec.ts}
   ├─ classes/
   │  └─ usuario.ts
   ├─ guards/
   │  └─ usuario-guard.service.ts (+ spec)
   ├─ services/
   │  ├─ websocket.service.ts (+ spec)
   │  └─ chat.service.ts (+ spec)
   ├─ components/
   │  ├─ footer/
   │  └─ chat/
   │     ├─ chat.component.{ts,html,css,spec.ts}
   └─ pages/
      ├─ login/
      │  ├─ login.component.{ts,html,css,spec.ts}
      └─ mensajes/
         ├─ mensajes.component.{ts,html,css,spec.ts}
```

---

## 🔌 Configuración de sockets (frontend)

- **Registro de usuario**: `WebsocketService.loginWS(nombre)` emite `configurar-usuario` y guarda `Usuario` en `localStorage`.  
- **Estado de conexión**: se actualiza con los eventos nativos `connect`/`disconnect`.  
- **Enviar mensaje**: `ChatService.sendMessage(texto)` emite `mensaje` con `{ de, cuerpo }`.  
- **Suscripciones**:
  - Públicos: `getMessages()` → escucha `mensaje-nuevo`.
  - Privados: `getMessagesPrivate()` → escucha `mensaje-privado`.

> **Nota**: si cambias el nombre de los eventos del backend, actualiza los métodos en `WebsocketService` y `ChatService`.

---

## 🧭 Rutas

- `/` → **LoginComponent**
- `/mensajes` → **MensajesComponent** (protegida por `UsuarioGuard`)
- `**` → fallback a **LoginComponent**

El guard **permite** la navegación sólo si `WebsocketService.getUsuario()` devuelve un objeto válido.

---

## 🧩 Componentes clave

- **ChatComponent**
  - Muestra una lista de mensajes (`mensajes`) que se actualiza al suscribirse a `getMessages()`.
  - Formulario para enviar mensajes (`enviar()`).
  - Auto-scroll al último mensaje.

- **FooterComponent**
  - Muestra `Online/Offline` según `wsService.socketStatus`.

- **ListaUsuariosComponent**
  - Placeholder (plantilla); listo para integrar lista remota de usuarios.

- **LoginComponent**
  - Captura `nombre` y ejecuta `loginWS(nombre)`, luego navega a `/mensajes`.

- **MensajesComponent**
  - Vista contenedora: agrupa `<app-lista-usuarios>` y `<app-chat>`.

---

## 🔄 Flujo básico

```text
[Usuario] --(nombre)--> LoginComponent --loginWS()--> WebsocketService
    └── guarda usuario en localStorage y backend
AppRouting + UsuarioGuard --> /mensajes
ChatComponent:
  - subscribe(mensaje-nuevo) --> render lista
  - enviar() --> ChatService.sendMessage() --emit('mensaje')--> servidor
FooterComponent:
  - muestra Online/Offline según connect/disconnect
```

---

## 🧪 Pruebas

- Configuradas con **Karma + Jasmine**.
- Archivos `*.spec.ts` generados por Angular CLI.
- Correr:
  ```bash
  ng test
  ```

---

## 🧼 Estilo y compatibilidad

- Soporte de navegadores configurado en `browserslist`.
- Reglas TSLint específicas para selectores de directivas/componentes en `tslint.json`.
- `polyfills.ts` incluye `zone.js` (Angular).

---

## 🛠️ Scripts útiles (Angular CLI)

```bash
# Servir en desarrollo
ng serve

# Build producción
ng build --configuration production

# Ejecutar pruebas
ng test
```

---

## 🧰 Problemas comunes

- **No conecta al servidor**: verifica `environment.wsUrl` y CORS del backend.
- **Ruta /mensajes bloqueada**: asegúrate de hacer login; el guard requiere `usuario` válido.
- **Sin scroll al final**: el elemento `#chat-mensajes` debe existir (ID correcto en la plantilla).

---

## 📄 Licencia

MIT (o la que elijas).

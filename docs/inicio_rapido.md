# SIGAF — Inicio Rápido (Vue 3)

## Requisitos previos

- **Node.js** v18 o superior (el proyecto fue desarrollado con v24)
- **npm** v9 o superior (incluido con Node.js)

Verificar versiones instaladas:

```bash
node --version
npm --version
```

---

## Levantar el proyecto localmente

### 1. Ir a la carpeta del proyecto

```bash
cd "ruta/a/SIGAF_VUE"
```

### 2. Instalar dependencias

```bash
npm install
```

> La primera vez puede tardar 1-2 minutos. Crea la carpeta `node_modules/`.

### 3. Iniciar el servidor de desarrollo

```bash
npm run dev
```

Vite levanta el servidor y muestra algo como:

```
  VITE v6.x.x  ready in 500ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
```

### 4. Abrir en el navegador

```
http://localhost:5173
```

Se muestra la pantalla de login. Usar los botones de demo en la parte inferior para ingresar con cualquier rol.

---

## Comandos disponibles

| Comando | Descripción |
|---|---|
| `npm run dev` | Servidor de desarrollo con hot-reload |
| `npm run build` | Genera la versión de producción en `dist/` |

---

## Stack tecnológico

| Tecnología | Versión | Rol |
|---|---|---|
| Vue 3 | 3.x | Framework principal |
| TypeScript | 5.x | Tipado estático |
| Vite 6 | 6.x | Build tool y dev server |
| Tailwind CSS | 4.x | Estilos utilitarios |
| lucide-vue-next | latest | Iconos |
| clsx + tailwind-merge | latest | Utilidades de clases CSS |

---

## Compartir el prototipo por internet (opciones)

El proyecto es una app estática (Vue 3 + Vite). Para compartirlo temporalmente no se necesita servidor ni base de datos — solo alojar los archivos del build.

### Opción 1 — Netlify Drop (más fácil, sin cuenta)

1. Ejecutar el build:
   ```bash
   npm run build
   ```
2. Ir a **[drop.netlify.com](https://drop.netlify.com)**
3. Arrastrar la carpeta `dist/` al área de la página
4. Netlify genera una URL pública al instante, por ejemplo:
   ```
   https://random-name-123.netlify.app
   ```
5. Compartir esa URL. El sitio queda activo por tiempo indefinido (con cuenta gratuita) o hasta que se elimine manualmente.

**Ventajas:** Sin registro, inmediato, URL pública estable.  
**Desventajas:** Sin cuenta, el sitio expira en 24 horas. Con cuenta gratuita es permanente.

---

### Opción 2 — Vercel (recomendado para uso continuo)

1. Instalar la CLI de Vercel:
   ```bash
   npm install -g vercel
   ```
2. Desde la carpeta del proyecto:
   ```bash
   vercel
   ```
3. Seguir el asistente (acepta los valores por defecto). Vercel detecta Vite automáticamente.
4. Al finalizar entrega una URL como:
   ```
   https://sigaf-vue.vercel.app
   ```

Para actualizar el deploy después de hacer cambios:
```bash
vercel --prod
```

**Ventajas:** URL estable, deploys rápidos, cuenta gratuita sin límite de tiempo.  
**Desventajas:** Requiere crear cuenta en [vercel.com](https://vercel.com).

---

### Opción 3 — GitHub Pages (si el proyecto está en Git)

1. Instalar el plugin de GitHub Pages para Vite:
   ```bash
   npm install --save-dev gh-pages
   ```

2. Agregar en `vite.config.ts` la base del repositorio:
   ```ts
   export default defineConfig({
     base: '/nombre-del-repositorio/',
     // ... resto de config
   })
   ```

3. Agregar en `package.json`:
   ```json
   "scripts": {
     "deploy": "npm run build && gh-pages -d dist"
   }
   ```

4. Ejecutar:
   ```bash
   npm run deploy
   ```

La URL queda en:
```
https://tu-usuario.github.io/nombre-del-repositorio/
```

---

### Opción 4 — Tunnel local con ngrok (sin build, para demos rápidas)

1. Instalar ngrok desde [ngrok.com/download](https://ngrok.com/download) o con:
   ```bash
   npm install -g ngrok
   ```

2. Levantar el servidor de desarrollo:
   ```bash
   npm run dev
   ```

3. En otra terminal, exponer el puerto 5173:
   ```bash
   ngrok http 5173
   ```

4. ngrok genera una URL temporal como:
   ```
   https://abc123.ngrok-free.app
   ```

**Ventajas:** Sin build, sin deploy, funciona al instante.  
**Desventajas:** La URL cambia cada vez que se reinicia ngrok.

---

## Comparativa rápida

| Opción | Requiere cuenta | Tiempo de setup | URL estable | Ideal para |
|---|---|---|---|---|
| Netlify Drop | No (24h) / Sí (permanente) | 2 minutos | Sí (con cuenta) | Revisión puntual |
| Vercel CLI | Sí (gratis) | 5 minutos | Sí | Uso continuo |
| GitHub Pages | Sí (gratis) | 10 minutos | Sí | Proyecto en Git |
| ngrok | No (limitado) / Sí | 1 minuto | No | Demo en vivo |

**Recomendación:** Para compartir con el usuario final para revisión, usar **Netlify Drop** si es una revisión puntual, o **Vercel** si se va a iterar y compartir varias veces.

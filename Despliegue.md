# Despliegue de la Pokédex en Azure

Aquí explico cómo publicamos la app en la nube y **por qué** tomamos cada decisión.

## Idea general

1. El código vive en **GitHub** (`GEliasDev/pokedex`, rama `main`).
2. En Azure creamos un **Static Web App** y lo conectamos a ese repo.
3. Azure crea solito un *workflow* de **GitHub Actions**. Cada vez que hacemos `push` a `main`, GitHub compila la app Angular y Azure la publica.

**Decisión:** usamos **Static Web Apps** y no una Máquina Virtual ni App Service, porque la Pokédex es solo frontend (archivos estáticos) y consume una API externa (PokéAPI). No necesitamos servidor propio, y así sale gratis y más fácil.

## Antes de empezar

- Cuenta de Azure lista (ver [README.md](README.md)).
- Código subido a GitHub.
- Que compile en local: `npm install` y luego `npm run build`.

## Paso 1: Crear el recurso en Azure

En [portal.azure.com](https://portal.azure.com) busca **"Static Web Apps"** → **Crear**.

| Campo | Qué pusimos | Por qué |
|---|---|---|
| Suscripción | La activa de la cuenta | Es la que paga (o el crédito gratis). |
| Grupo de recursos | `rg-pueblopaletainc-prod` | La "carpeta" del proyecto, fácil de borrar al final. Nombre: `rg` (resource group) + proyecto + ambiente `prod`. |
| Nombre | `swa-pokedex-portal-prod` | `swa` = *Static Web App*, `pokedex-portal` = qué es, `prod` = ambiente. Mismo estilo de nombres que el grupo de recursos. |
| Tipo de plan | **Gratuito (Free)** | Alcanza de sobra para un proyecto de la uni. |
| Región | La más cercana disponible | Menos latencia. En el plan gratis la app igual se sirve globalmente. |
| Origen del despliegue | **GitHub** | Para que se actualice solo con cada `push`. |

## Paso 2: Conectar GitHub

Azure te pide **iniciar sesión con GitHub** y autorizar. Después eliges:

- **Organización:** tu usuario (`GEliasDev`)
- **Repositorio:** `pokedex`
- **Rama:** `main`

**Decisión:** rama `main` porque es la principal; así lo que esté ahí es lo que se publica.

## Paso 3: Detalles de compilación

Esta fue la parte que más dudas dio. Esto es lo que va:

| Campo | Valor | Por qué |
|---|---|---|
| Valores preestablecidos | **Angular** | Azure ya sabe cómo compilar Angular. |
| Ubicación de la aplicación | `/` | El `package.json` y `angular.json` están en la raíz del repo, no en una subcarpeta. |
| Ubicación de la API | *(vacío)* | No tenemos backend propio; la API es la externa PokéAPI. |
| Ubicación de salida | `dist/pokedex-angular` | Es donde Angular deja los archivos ya compilados. Sale de `outputPath` en `angular.json`. |

> ⚠️ Si pones mal la ubicación de salida, el despliegue "funciona" pero la página sale en blanco o con error 404. Esta ruta tiene que coincidir con `angular.json`.

## Paso 4: Revisar y crear

Le das a **Revisar y crear** → **Crear**. Azure:

1. Crea el recurso.
2. Mete un archivo en `.github/workflows/` de tu repo (un commit automático).
3. Lanza el primer build.

Puedes ver el avance en la pestaña **Actions** del repo en GitHub.

## Paso 5: Ajustes para que Angular funcione bien en Azure

### a) No usar el `base-href` de GitHub Pages

El script `predeploy` del `package.json` usa `--base-href=/pokedex-angular/`, pensado para GitHub Pages. **En Azure la app vive en la raíz (`/`)**, así que ese script no se usa. Azure compila con el comando normal de build.

**Decisión:** dejamos el script como está (no estorba) pero no lo usamos para Azure.

### b) Rutas de Angular (evitar 404 al recargar)

Angular maneja las rutas en el navegador. Si recargas en `/pokemon/25`, Azure busca ese archivo, no lo encuentra y da 404. Se arregla con un archivo `staticwebapp.config.json` que mande todo a `index.html`:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/*.{css,js,png,jpg,svg,ico,webp,woff2}"]
  }
}
```

Va en `src/` y hay que agregarlo a `assets` en `angular.json` para que termine dentro de `dist/pokedex-angular`.

### c) Reto 1: las imágenes no cargaban (error 404)

**El problema:** al abrir la página principal, las imágenes de los Pokémon no salían y la consola del navegador mostraba:

```
GET https://delightful-hill-0f1051410.5.azurestaticapps.net/pokedex-angular/assets/images/pokemon-green.png 404 (Not Found)
```

**La causa:** fíjate en la URL: tiene `/pokedex-angular/` en medio. El proyecto original estaba pensado para GitHub Pages, donde la app se sirve en una subcarpeta (`usuario.github.io/pokedex-angular/`). Por eso en `src/environments/environment.prod.ts` la ruta de las imágenes estaba escrita a mano así:

```ts
imagesPath: '/pokedex-angular/assets/images',
```

En Azure la app vive en la raíz del dominio, entonces esa carpeta `/pokedex-angular/` no existe y da 404.

**La decisión:** cambiar la ruta para que apunte a la raíz, igual que ya estaba en el ambiente de desarrollo (`environment.ts`):

```ts
imagesPath: '/assets/images',
```

Cambiamos `environment.prod.ts` porque es el archivo que Angular usa cuando compila en modo producción (que es lo que hace Azure). En local con `npm start` nunca falló porque ahí se usa `environment.ts`, que ya tenía la ruta bien.

**Después del cambio:** hacer `git commit` y `git push` a `main` para que GitHub Actions vuelva a compilar y Azure publique la versión corregida. Luego recargar la página con `Ctrl + F5` (para saltarse la caché) y comprobar que las imágenes cargan.

## Paso 6: Probar

En el portal, dentro del recurso, aparece la **URL** (algo como `https://xxxx.azurestaticapps.net`). Ábrela y revisa:

- Carga el listado de Pokémon.
- Entra al detalle de uno.
- Recarga en el detalle y no sale 404.

## Cómo se actualiza después

Haces cambios → `git push` a `main` → GitHub Actions compila → Azure publica. No hay que hacer nada más.

## Problemas comunes

| Síntoma | Causa probable |
|---|---|
| Página en blanco | Ubicación de salida mal puesta o `base-href` incorrecto. |
| 404 al recargar una ruta | Falta `staticwebapp.config.json`. |
| Falla el build en Actions | Versión de Node distinta; Angular 14 funciona con Node 18 o menor. Revisar el log. |
| No cargan los Pokémon | Revisar la consola del navegador; puede ser un problema con PokéAPI. |

## Limpieza (importante)

Cuando termine la entrega, borra el grupo de recursos `rg-pueblopaletainc-prod` desde el portal para que no quede nada activo.

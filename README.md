# Pokédex Angular

La aplicación muestra el listado y el detalle de los Pokémon de las primeras 3 generaciones.

La imagen que representa un Pokémon en el listado muestra las variaciones que estos tuvieron durante las primeras versiones, desde la versión Green (1996) hasta la versión Emerald (2005).

Los detalles de un Pokémon individual muestran sus estadísticas base y los registros de la Pokédex de las diferentes versiones.

El proyecto está hecho con [Angular](https://angular.io/) para la interfaz y se comunica con la API RESTful [PokéAPI](https://pokeapi.co/).

> Proyecto original de [keilermora/pokedex-angular](https://github.com/keilermora/pokedex-angular). Aquí lo usamos para practicar el despliegue en la nube (Microsoft Azure).

## Requisitos mínimos

- [Node.js](https://nodejs.org) LTS (el pipeline usa Node 18).
- Un navegador web.
- Una cuenta de GitHub (el código vive ahí).
- Una cuenta de Microsoft Azure (el tutorial de abajo explica cómo crearla).

## Correr la app en local

En la raíz del proyecto:

```
npm install
npm start
```

Se abre en `http://localhost:4200`.

---

# Tutorial: cómo crear la cuenta en la nube (Microsoft Azure)

## ¿Por qué Azure?

Elegimos **Azure** porque tiene el servicio **Static Web Apps**, que está hecho justo para apps de frontend como esta (Angular, React, Vue...). Además tiene plan gratuito, se conecta directo con GitHub y despliega solo cada vez que hacemos `push`. Para una Pokédex que solo son archivos estáticos (HTML, JS, CSS) no necesitamos pagar un servidor completo.

## Paso a paso

### 1. Entrar a la página de Azure

Abre [https://azure.microsoft.com/free](https://azure.microsoft.com/free) y dale a **"Comenzar gratis"** (o *Start free*).

### 2. Iniciar sesión con una cuenta Microsoft

Azure se amarra a una cuenta Microsoft (Outlook, Hotmail, o la de la universidad/trabajo). Si no tienes, le das a **"Crear una"** y listo.

**Decisión:** usamos el **correo institucional** de la universidad (`gabrielvaldelamarc@tecnocomfenalco.edu.co`). Con correo institucional se puede acceder a **Azure for Students** (crédito gratis sin tarjeta), así que es mejor que usar uno personal.

### 3. Llenar el perfil

Te pide país, nombre, correo y teléfono. Después hay una verificación por mensaje de texto o llamada.

### 4. Verificar identidad

- Con **Azure for Students**: solo verificas que eres estudiante con tu correo institucional, sin tarjeta.
- Con la **cuenta gratuita normal**: piden una tarjeta de crédito/débito solo para comprobar que eres una persona real. No te cobran mientras te mantengas dentro de lo gratis.

### 5. Aceptar los términos

Marcas la casilla del acuerdo de suscripción y le das a **Registrarse**.

### 6. Entrar al portal

Cuando termine, entras a [https://portal.azure.com](https://portal.azure.com). Esa es la consola principal donde manejamos todo.

### 7. Revisar la suscripción

En el buscador de arriba escribe **"Suscripciones"** y confirma que aparece una activa (por ejemplo *Azure for Students* o *Free Trial*). Sin suscripción activa no deja crear recursos.

### 8. (Opcional pero recomendado) Crear un grupo de recursos

Un **grupo de recursos** es como una carpeta donde Azure guarda todo lo de un proyecto. Así, cuando acabemos, borramos la carpeta y se va todo de una vez (y no se nos queda nada cobrando).

**Decisión:** creamos uno llamado `rg-pueblopaletainc-prod`. El nombre sigue una convención: `rg` = *resource group*, `pueblopaletainc` = el nombre del "proyecto/empresa" (Pueblo Paleta Inc., que es de donde sale Ash en Pokémon 😄) y `prod` = ambiente de producción. Así, cuando haya más recursos, se entiende de un vistazo qué es cada cosa.

> 📸 Aquí conviene pegar capturas de pantalla de cada paso para la entrega.

---

Siguiente: la guía de cómo subir la app está en [Despliegue.md](Despliegue.md).

## Referencias

- [Angular](https://angular.io/)
- [PokéAPI](https://pokeapi.co/)
- [Azure Static Web Apps](https://learn.microsoft.com/azure/static-web-apps/)
- [Azure for Students](https://azure.microsoft.com/free/students)

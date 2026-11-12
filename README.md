# Práctica 3 — Desalineamiento de ramas

Guía de trabajo para simular un hotfix que llega a `main`, pero no a `develop`,
y observar qué pasa cuando una funcionalidad creada antes del hotfix intenta
llegar a producción.

> Esta carpeta es un repositorio local independiente. No modifica la web de F1
> que está en la raíz del repositorio padre.

## Ramas y objetivo

- `main`: versión estable publicada.
- `develop`: integración de cambios de desarrollo.
- `hotfix-pending`: corrección urgente de producción.
- `feature/dev_1`: nueva funcionalidad creada antes del hotfix.

El error `Bienvenio` del HTML inicial es intencionado. El enunciado contiene
una errata distinta (`Bienvendio`) en el ejemplo del conflicto; en esta guía
seguiremos el texto del HTML de cada fase y resolveremos conservando el arreglo
`Bienvenido`.

## Fase 1 — Versión inicial y ramas

El HTML inicial ya está en `index.html`. Al preparar el repositorio, los
comandos locales son:

```powershell
git init -b main
git add README.md index.html
git commit -m "Práctica 3: versión inicial"
git tag -a v1.0.0 -m "Versión 1.0.0"
git branch develop main
git branch hotfix-pending main
git branch feature/dev_1 main
```

Cuando exista el repositorio remoto de GitHub:

```powershell
git remote add origin <URL-del-repositorio>
git push -u origin main
git push origin --tags
git push -u origin develop
git push -u origin hotfix-pending
git push -u origin feature/dev_1
```

## Fase 2 — Hotfix en producción

En `hotfix-pending`, cambia `index.html`: corrige el título a `Bienvenido a la
tienda` y la versión a `1.0.1`. Después:

```powershell
git switch hotfix-pending
git add index.html
git commit -m "Hotfix: corregir bienvenida en producción"
git push origin hotfix-pending
```

En GitHub hay que abrir una Pull Request desde `hotfix-pending` hacia `main`,
revisarla y completar el merge. Tras el merge, sincroniza la rama local:

```powershell
git switch main
git pull origin main
```

La creación y el merge de la PR se hacen en GitHub con tu cuenta; cuando
lleguemos a ese punto tendrás las instrucciones detalladas.

## Fase 3 — Feature y desalineamiento

En `feature/dev_1`, deja el título como `Bienvenio a la tienda online`, añade
`<p>Nuevo: catálogo de productos</p>` y conserva la versión `1.0.0`. Registra
el cambio:

```powershell
git switch feature/dev_1
git add index.html
git commit -m "Feature: añadir catálogo de productos"
git push origin feature/dev_1
```

Integra la feature en `develop`:

```powershell
git switch develop
git merge --no-ff feature/dev_1
git push origin develop
```

Después intenta integrar `develop` en `main`:

```powershell
git switch main
git merge --no-ff develop
```

Este último merge debe detenerse por un conflicto en `index.html`: `main` tiene
el arreglo del hotfix y `develop` conserva la errata de su feature.

## Fase 4 — Resolver el conflicto

Edita `index.html` para que el resultado conserve las dos mejoras y no
reintroduzca el error:

```html
<h1>Bienvenido a la tienda online</h1>
<p>Nuevo: catálogo de productos</p>
<p>Versión 1.0.1</p>
```

Finaliza el merge:

```powershell
git add index.html
git commit -m "Merge develop en main: resolver conflicto conservando hotfix"
git push origin main
```

## Preguntas para la entrega

1. El merge de la feature en `develop` no chocó porque `develop` aún no tenía
   el cambio del hotfix. El merge de `develop` en `main` sí chocó porque ambas
   ramas modificaron la misma línea del título de forma diferente.
2. Si se aceptaba la versión de `develop`, se podía volver a publicar la
   errata corregida por el hotfix y perder la versión `1.0.1`.
3. Justo después de integrar el hotfix en `main` y antes de empezar los cambios
   de la feature, había que llevar `main` a `develop` y a `feature/dev_1`:
   `git switch develop` y `git merge main`; después
   `git switch feature/dev_1` y `git merge main`. Así ambas ramas parten del
   arreglo antes de divergir de nuevo.

## Evidencias y entrega

Guarda capturas de la PR del hotfix, el merge feature→develop, el conflicto al
integrar develop→main y su resolución. Añade el enlace del repositorio remoto y
las respuestas a las preguntas en el PDF final. Las capturas y la creación del
PDF las tendrás que hacer tú; te guiaré al llegar a esa parte.

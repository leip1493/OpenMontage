# AGENTS.override.md — OpenMontage (copia local, NO versionada)

Este checkout es un clon de **terceros**. No es código nuestro.

- `upstream` → `github.com/calesthio/OpenMontage` (proyecto original). Solo lectura.
- `origin` → `github.com/leip1493/OpenMontage` (fork propio). Solo para ramas locales.

Este archivo **reemplaza** al `AGENTS.md` versionado de este directorio (convención
`AGENTS.override.md`: en Hermes y Codex gana sobre `AGENTS.md` en el mismo directorio).
No está en git: vive en `.git/info/exclude`. Por eso repite abajo el contrato de upstream,
que ya no se carga solo.

Si trabajás desde una subcarpeta, este archivo se sigue cargando (Hermes arma la cadena
`AGENTS.md` desde la raíz git hasta el cwd).

## Contrato de upstream — obligatorio

Antes de actuar, lee `AGENT_GUIDE.md` y respetá su **Rule Zero: toda producción pasa por un
pipeline**. Para arquitectura y convenciones: `PROJECT_CONTEXT.md`. Onboarding cuando el
pedido es vago: `skills/meta/onboarding.md`. Si el usuario pasa un video de referencia:
`skills/meta/video-reference-analyst.md`.

## Reglas de este checkout

1. **No modifiques archivos versionados.** Ni `scripts/`, `lib/`, `pipeline_defs/`,
   `skills/`, `docs/`, `AGENT_GUIDE.md`, `PROJECT_CONTEXT.md`, `config.yaml`. Cualquier
   cambio ahí se pierde en el próximo update o lo rompe.
2. **Nunca commitees en `main`.** `main` es espejo de `upstream/main` y debe poder
   actualizarse con `--ff-only` en cualquier momento.
3. **Tus cambios van en ramas `local/*`**, siempre creadas desde `upstream/main`:
   `local/config` (provider, modelo, budget, paths) y `local/<tema>` para parches.
4. **Nunca hagas push a `upstream`.** Tenés que pushear a `origin` (el fork).
5. **No abras PR desde `local/config`.** Es configuración personal; un PR mandaría el
   provider y el presupuesto propios al proyecto de ellos.
6. **La producción va en `projects/`** (gitignored, hoy ~1 GB). No se versiona: se respalda
   fuera de git. `reports/` también es output.
7. **No edites `AGENTS.md` ni `CLAUDE.md` de upstream**: las reglas locales van acá.
8. **Si el pedido implica cambiar el comportamiento del pipeline**, no toques `skills/` ni
   `pipeline_defs/` en `main`: proponé `local/<tema>`, o un PR a upstream desde el fork.

## Ritual de actualización — antes de tocar código

```bash
cd ~/repositorios/OpenMontage
git status --short                  # DEBE estar vacío
git fetch upstream
git switch main
git merge --ff-only upstream/main
git switch local/config
git rebase upstream/main
```

- Si `git status --short` no está vacío, **pará y avisá**: un archivo versionado modificado
  rompe el `--ff-only`. Los patrones que sí son locales (`.tmp/`, `.tmp-reference/`) ya están
  en `.git/info/exclude`.
- Si el rebase choca en `config.yaml`, se resuelve a mano conservando el provider local.
  Nunca `git checkout --theirs` a ciegas.
- Espejar el fork en GitHub (opcional, cuando quieras que se vea):
  `git push origin main local/config`.

Rama equivocada = update fallido. Verificá siempre con `git branch -vv`:
`main` debe mostrar `[upstream/main]` y `local/config` `[origin/local/config]`.

## Version fijada

El repo **no tiene tags**. La única referencia reproducible es el SHA:

```bash
git rev-parse --short HEAD    # anotarlo en ../agency-videos/docs/stack.md al actualizar
```

## Contexto de negocio — no se carga solo

Este repo es el motor de render. El criterio de negocio (qué se vende, cómo se entrega, qué
entra a git, precios) vive en **`../agency-videos/`**:
`AGENTS.md`, `docs/contexto.md`, `docs/mision-vision.md`, `docs/flujo.md`,
`docs/paquetes-y-precios.md`.

Si la tarea toca producto, precio, entrega o nomenclatura, leelos antes de decidir. Este
override **no** los inyecta: hay que abrirlos.

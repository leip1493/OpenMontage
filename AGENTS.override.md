# AGENTS.override.md — OpenMontage (copia local, NO versionada)

Este checkout es un clon de **terceros**. No es código nuestro.

- `upstream` → `github.com/calesthio/OpenMontage` (proyecto original). Solo lectura.
- `origin` → `github.com/leip1493/OpenMontage` (fork propio). Solo para ramas locales.

Este archivo **reemplaza** al `AGENTS.md` versionado de este directorio (convención
`AGENTS.override.md`: en Hermes y Codex gana sobre `AGENTS.md` en el mismo directorio).
Está versionado **solo en la rama `local/config`**, no en `main`: por eso, mientras trabajás
acá, la rama activa debe ser `local/config` (ahí este archivo existe y se carga). Repite
abajo el contrato de upstream, porque en este directorio reemplaza al suyo.

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
3. **Tus cambios van en `local/config` o en ramas `local/*` creadas desde ella** (así
   heredan este archivo): `local/config` lleva los ajustes de provider/modelo/budget/paths y
   este override; `local/<tema>` para parches puntuales.
4. **Nunca hagas push a `upstream`.** Tenés que pushear a `origin` (el fork).
5. **No abras PR desde `local/config`.** Es configuración personal; un PR mandaría el
   provider y el presupuesto propios al proyecto de ellos.
6. **La producción va en `projects/`** (gitignored, hoy ~1 GB). No se versiona: se respalda
   fuera de git. `reports/` también es output.
7. **No edites `AGENTS.md` ni `CLAUDE.md` de upstream**: las reglas locales van acá.
8. **Si el pedido implica cambiar el comportamiento del pipeline**, no toques `skills/` ni
   `pipeline_defs/` en `main`: proponé `local/<tema>`, o un PR a upstream desde el fork.
9. **No commitees ni hagas push sin autorización explícita del usuario.** Proponé el mensaje
   y esperá el OK, incluso en `local/config`. Sin líneas `Co-Authored-By` ni atribución a IA.

## Ritual de actualización — antes de tocar código

Se hace **sin cambiar de rama**: te quedás en `local/config` y rebaseás sobre upstream. Así
este archivo nunca desaparece (existe solo en `local/config`).

```bash
cd ~/repositorios/OpenMontage
git status --short                  # DEBE estar vacío
git branch --show-current           # DEBE decir local/config
git fetch upstream
git rebase upstream/main
```

- Si `git status --short` no está vacío, **pará y avisá**: hay cambios sin commitear.
- Si el rebase choca en `config.yaml`, se resuelve a mano conservando el provider local.
  Nunca `git checkout --theirs` a ciegas.
- Espejar en GitHub (opcional): `git push origin local/config`. Para mantener el espejo de
  `main` al día también: `git push origin upstream/main:main`.

Verificá siempre con `git branch -vv`: `main` debe mostrar `[upstream/main]` y
`local/config` `[origin/local/config]`. Rama equivocada = update fallido.

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

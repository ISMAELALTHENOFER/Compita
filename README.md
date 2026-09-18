# Instalación de Compita para una IA

Después de clonar este repositorio, instalá Compita para el runtime que está activo. `compita.md` en la raíz es la única fuente del protocolo; no requiere skills del usuario.

## Orden obligatorio

1. Identificá el runtime activo: OpenCode, Claude Code o Cursor.
2. Leé por completo `compita.md` en la raíz.
3. Creá exactamente **un** adaptador llamado `compita.md` en la ruta correspondiente. Para OpenCode y Claude Code, elegí alcance local o global, nunca ambos. Cursor solo admite el alcance del proyecto.
4. Anteponé el frontmatter exacto de este README y usá como cuerpo el protocolo completo de `compita.md` raíz, sin resumirlo ni agregar frontmatter al cuerpo.
5. Recargá o reiniciá el runtime si corresponde.
6. Verificá que el agente sea descubierto por el mecanismo normal del runtime.

Creá solo el adaptador del runtime activo, nunca los tres. Los datos de conexión y credenciales MCP de GitLab, Jira/Tempo y Oracle son específicos del entorno: no los inventes ni los inspecciones. Las conexiones configuradas para MCP de solo lectura están autorizadas por defecto exclusivamente para consultas de solo lectura; no requieren aprobación previa. La inspección semántica de ZIP o binarios y publicar, aprobar o comentar remotamente no están disponibles.

## Adaptador único por runtime

### OpenCode

Ruta: `.opencode/agent/compita.md`

Para instalarlo globalmente y usarlo desde cualquier proyecto OpenCode, usá en cambio: `~/.config/opencode/agent/compita.md`.

```yaml
---
description: Revisión ejecutiva, trazable y de solo lectura de un MR de GitLab contra un issue de Jira.
mode: primary
permission:
  edit: deny
  bash: deny
  task: deny
---
```

OpenCode carga agentes al iniciar: después de crear o modificar el adaptador, cerrá y reiniciá OpenCode. El alcance global sirve para toda la instalación de OpenCode; el local sirve solo para el repositorio actual.

### Claude Code

Ruta: `.claude/agents/compita.md`

Para instalarlo globalmente y usarlo desde cualquier proyecto Claude Code, usá en cambio: `~/.claude/agents/compita.md`.

```yaml
---
name: compita
description: Revisión ejecutiva, trazable y de solo lectura de un MR de GitLab contra un issue de Jira.
tools: [Read, Grep, Glob]
model: inherit
---
```

Recargá o reiniciá Claude Code si no descubre el agente. El alcance global sirve para toda la instalación de Claude Code; el local sirve solo para el repositorio actual.

### Cursor

Ruta: `.cursor/agents/compita.md`

Cursor no documenta un directorio global para subagentes: instalalo por proyecto en esta ruta. No uses una ruta global no documentada.

```yaml
---
name: compita
description: Revisión ejecutiva, trazable y de solo lectura de un MR de GitLab contra un issue de Jira.
model: inherit
readonly: true
---
```

Recargá o reiniciá Cursor si no descubre el agente.

## Verificación

- Existe `compita.md` en la raíz y fue leído por completo.
- Existe un solo adaptador en la ruta del runtime activo; en OpenCode y Claude Code, es local o global, no ambos. Cursor usa solo el alcance del proyecto.
- El adaptador contiene el frontmatter exacto anterior seguido del protocolo raíz completo.
- El runtime reconoce el agente `compita`.
- Las consultas remotas se realizaron solo mediante MCP configurados de solo lectura, sin mutaciones remotas ni inspección o invención de credenciales.

# Instalación de Compita para una IA

Compita es un líder técnico de solo lectura para `rentax/extranet` y `rentax/intranet`. Tiene cinco modos:

- pase a Test (MR hacia `Developer`);
- pase a Producción (MR hacia `main`/`master`);
- revisión de MR;
- revisión de SQL de Jira;
- estado y riesgo con Jira, GitLab y Tempo.

Reemplaza al agente `crack-de-la-revision` y a la skill `lead-tech`: todo su contenido está en `compita.md`, así que este repositorio es la única fuente.

Después de clonar este repositorio, instalá Compita para el runtime que está activo. `compita.md` en la raíz es la única fuente del protocolo; no requiere skills del usuario.

## Requisitos

Servidores MCP de `config-mcp` registrados con estos nombres exactos (el agente y sus permisos dependen de ellos):

- `jira-tempo`
- `gitlab-mcp`
- `oracle-db`, con las conexiones `dev` (Desarrollo) y `test` (Test), ambas solo con permiso `select`.
- `context7` es opcional.

Los datos de conexión y las credenciales son específicos de cada entorno: no los inventes ni los inspecciones. Si un servidor tiene otro nombre, avisá al usuario en lugar de adaptar el protocolo.

## Orden obligatorio

1. Identificá el runtime activo. OpenCode, Claude Code y Cursor tienen adaptador propio abajo; cualquier otro usa el adaptador genérico.
2. Leé por completo `compita.md` en la raíz.
3. Creá exactamente **un** adaptador llamado `compita.md` en la ruta correspondiente. Para OpenCode y Claude Code, elegí alcance local o global, nunca ambos. Cursor solo admite el alcance del proyecto.
4. Anteponé el frontmatter exacto de este README y usá como cuerpo el protocolo completo de `compita.md` raíz, sin resumirlo ni agregar frontmatter al cuerpo. Si ya existía un adaptador, reemplazalo completo.
5. Si existen el agente anterior `crack-de-la-revision` (en `opencode.json` o como archivo de agente) o la skill `lead-tech`, quitalos: Compita los reemplaza y no depende de ellos.
6. Recargá o reiniciá el runtime si corresponde.
7. Verificá que el agente sea descubierto por el mecanismo normal del runtime.

Creá solo el adaptador del runtime activo, nunca los tres.

## Adaptador único por runtime

### OpenCode

Ruta: `.opencode/agent/compita.md`

Para instalarlo globalmente y usarlo desde cualquier proyecto OpenCode, usá en cambio: `~/.config/opencode/agent/compita.md`.

```yaml
---
description: "Líder técnico de solo lectura para rentax/extranet y rentax/intranet: pases a Test y a Producción, revisión de MR y SQL, y estado y riesgo con Jira, GitLab, Oracle y Tempo."
mode: primary
permission:
  "*": deny
  read: allow
  grep: allow
  glob: allow
  list: allow
  external_directory: ask
  "jira-tempo_*": allow
  "gitlab-mcp_*": allow
  oracle-db_list_connections: allow
  oracle-db_query: allow
  oracle-db_describe_table: allow
  oracle-db_list_tables: allow
  "context7_*": allow
---
```

`"*": deny` va primero porque en OpenCode gana la última regla que coincide: así el agente solo ve estas herramientas (ni edición, ni bash, ni `oracle-db_execute`, ni otros MCP) y no gasta contexto en esquemas que no usa. OpenCode carga los agentes al iniciar: después de crear o modificar el adaptador, cerrá y reiniciá OpenCode.

### Claude Code

Ruta: `.claude/agents/compita.md`

Para instalarlo globalmente y usarlo desde cualquier proyecto Claude Code, usá en cambio: `~/.claude/agents/compita.md`.

```yaml
---
name: compita
description: "Líder técnico de solo lectura para rentax/extranet y rentax/intranet: pases a Test y a Producción, revisión de MR y SQL, y estado y riesgo con Jira, GitLab, Oracle y Tempo."
tools: Read, Grep, Glob, mcp__jira-tempo__*, mcp__gitlab-mcp__*, mcp__oracle-db__list_connections, mcp__oracle-db__query, mcp__oracle-db__describe_table, mcp__oracle-db__list_tables, mcp__context7__*
model: inherit
---
```

Recargá o reiniciá Claude Code si no descubre el agente.

### Cursor

Ruta: `.cursor/agents/compita.md`

Cursor no documenta un directorio global para subagentes: instalalo por proyecto en esta ruta. No uses una ruta global no documentada.

```yaml
---
name: compita
description: "Líder técnico de solo lectura para rentax/extranet y rentax/intranet: pases a Test y a Producción, revisión de MR y SQL, y estado y riesgo con Jira, GitLab, Oracle y Tempo."
model: inherit
readonly: true
---
```

Cursor no permite restringir herramientas MCP por agente: la protección contra escrituras en la base es que las conexiones de `oracle-db` sean solo `select`. Recargá o reiniciá Cursor si no descubre el agente.

### Cualquier otro runtime

Por ejemplo Codex, Gemini CLI, GitHub Copilot o Windsurf.

1. Usá el mecanismo documentado por ese runtime para agentes, subagentes o modos personalizados, en la ruta y con el formato de frontmatter que documente. Si no tiene agentes, usá su archivo de instrucciones o reglas con alcance de proyecto. No inventes rutas ni campos no documentados.
2. Nombre `compita` y esta descripción: `Líder técnico de solo lectura para rentax/extranet y rentax/intranet: pases a Test y a Producción, revisión de MR y SQL, y estado y riesgo con Jira, GitLab, Oracle y Tempo.`
3. Aplicá las restricciones que el runtime permita, en este orden de preferencia: solo lectura; sin edición, shell ni delegación; solo las herramientas `jira-tempo`, `gitlab-mcp`, las de lectura de `oracle-db` (`list_connections`, `query`, `describe_table`, `list_tables`) y `context7`. Si el runtime no permite restringir herramientas, avisá al usuario que la protección depende de que las conexiones de `oracle-db` sean solo `select`.
4. Cuerpo: el protocolo completo de `compita.md` raíz, igual que en los demás adaptadores.

## Uso

- **Pases:** pasale la clave o la URL del issue (por ejemplo `REM-12345`); el MR es opcional. Compita busca el MR por la clave del issue en los dos proyectos y elige el modo según la rama destino: `Developer` es Test y `main`/`master` es Producción. Pregunta solo si no encuentra el MR o hay más de uno.
- **Revisión de MR:** pedile que revise un MR, con su URL o IID y el issue.
- **Revisión de SQL:** el issue y el adjunto.
- **Estado y riesgo:** el proyecto, el sprint o el equipo y el período. Para carga horaria de Tempo, también los `accountIds`, la zona horaria y el umbral.

## Verificación

- Existe `compita.md` en la raíz y fue leído por completo.
- Existe un solo adaptador en la ruta del runtime activo; en OpenCode y Claude Code es local o global, no ambos. Cursor usa solo el alcance del proyecto.
- El adaptador contiene el frontmatter exacto anterior, o el del adaptador genérico en otros runtimes, seguido del protocolo raíz completo.
- No quedan el agente `crack-de-la-revision` ni la skill `lead-tech`.
- El runtime reconoce el agente `compita`.
- Las consultas remotas se hicieron solo con los MCP de solo lectura configurados, sin mutaciones remotas ni inspección o invención de credenciales.

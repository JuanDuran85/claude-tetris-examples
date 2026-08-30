---
description: Crea un git worktree aislado y ejecuta ahí las instrucciones dadas
---

Instrucciones del usuario: $ARGUMENTS

1. Deriva un `name` corto en kebab-case que resuma esas instrucciones.
2. Ejecuta: `git worktree add .trees/<name>` (crea rama `<name>` si no existe: `git worktree add .trees/<name> -b <name>`).
3. Cambia el contexto de trabajo a `.trees/<name>` y ejecuta ahí las instrucciones del usuario, de forma aislada del working tree principal — sin tocar archivos fuera de ese worktree.

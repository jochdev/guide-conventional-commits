# Nuxt UI Starter

Look at [Nuxt docs](https://nuxt.com/docs/getting-started/introduction) and [Nuxt UI docs](https://ui.nuxt.com) to learn more.

## Setup

Make sure to install the dependencies:

```bash
# npm
npm install

# pnpm
pnpm install

# yarn
yarn install

# bun
bun install
```

## Development Server

Start the development server on `http://localhost:3000`:

```bash
# npm
npm run dev

# pnpm
pnpm run dev

# yarn
yarn dev

# bun
bun run dev
```

## Production

Build the application for production:

```bash
# npm
npm run build

# pnpm
pnpm run build

# yarn
yarn build

# bun
bun run build
```

Locally preview production build:

```bash
# npm
npm run preview

# pnpm
pnpm run preview

# yarn
yarn preview

# bun
bun run preview
```

Check out the [deployment documentation](https://nuxt.com/docs/getting-started/deployment) for more information.

## Commits, versionado y changelog automáticos

Este proyecto usa Conventional Commits, versionado automático y changelog. Puedes replicar este flujo en cualquier proyecto siguiendo estos pasos:

### Instalación de dependencias necesarias

```bash
npm install --save-dev commitizen cz-conventional-changelog standard-version husky @commitlint/cli @commitlint/config-conventional
```

### Configuración básica

- En `package.json` agrega:
  - **Asegúrate de tener el campo `"version"` (ejemplo: `"version": "1.0.0"`). Si no existe, agrégalo manualmente la primera vez.**
  - Script para commits: `"commit": "cz"`
  - Script para release: `"release": "standard-version && git push --follow-tags origin main"`
  - Configuración de commitizen:

```json
"config": {
  "commitizen": {
    "path": "cz-conventional-changelog"
  }
}
```

- Crea el archivo `commitlint.config.js` con:

```js
export default { extends: ["@commitlint/config-conventional"] };
```

- Inicializa husky y agrega el hook para validar mensajes:

```bash
npx husky install
npx husky add .husky/commit-msg "npx --no -- commitlint --edit $1"
```

### Ejemplo de mensaje de commit válido

```bash
feat: agrega componente de login
fix: corrige error en validación de usuario
chore: actualiza dependencias
```

### Flujo de trabajo recomendado

1. Haz tus cambios en el código.
2. Ejecuta:
   ```bash
   npm run commit
   ```
   Esto te guiará para escribir un mensaje de commit válido.
3. Cuando quieras actualizar la versión y el changelog según los commits, ejecuta:
   ```bash
   npm run release
   ```
   Esto actualizará la versión, el changelog y subirá los tags a tu repo.

### ¿Cómo deshacer un release o tag erróneo?

Si generaste un release o tag por error, puedes eliminarlo así:

```bash
git tag -d vX.Y.Z
git push origin :refs/tags/vX.Y.Z
```

---

### Troubleshooting

- Si standard-version no sube la versión, revisa que el campo `version` esté presente en `package.json` y que existan commits válidos.
- Si tienes conflictos en el changelog, resuélvelos manualmente y vuelve a ejecutar el release.
- Si necesitas limpiar **todos** los tags locales y remotos (¡precaución!):

```bash
npm run clean:tags
```

---

### Importante si usas la extensión de Control de Código Fuente de VS Code

Si realizas commits desde la interfaz gráfica de VS Code (o cualquier otra herramienta que no use el comando `npm run commit`):

- **Debes escribir el mensaje de commit manualmente siguiendo el formato Conventional Commits** (por ejemplo: `feat: agrega login`, `fix: corrige bug`, etc).
- Si el mensaje no es válido, el commit será rechazado automáticamente por la validación de commitlint y husky.
- El resto del flujo (release, changelog, versionado) funciona igual.

> **Recomendación:** Si tienes dudas sobre el formato, ejecuta `npm run commit` al menos una vez para ver cómo debe ser el mensaje.

---

### Integración continua (CI) con GitHub Actions

Puedes automatizar la validación de commits, tests y releases usando GitHub Actions. Ejemplo de workflow `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm install
      - run: npm run lint
      - run: npm test # si tienes tests
      - run: npx commitlint --from=HEAD~10 --to=HEAD --verbose
```

### Pre-lanzamientos (pre-release)

Para crear versiones beta, alpha o rc:

```bash
npm run release -- --prerelease beta
```

Esto generará una versión como `1.0.0-beta.0`.

### Cómo contribuir

1. Haz un fork del repositorio y crea una rama para tu feature o fix.
2. Sigue el flujo de commits convencional.
3. Haz un PR a la rama principal.
4. El equipo revisará y hará merge siguiendo el mismo flujo de versionado.

### Enlaces útiles

- [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/)
- [standard-version](https://github.com/conventional-changelog/standard-version)
- [commitizen](https://commitizen-tools.github.io/commitizen/)
- [commitlint](https://commitlint.js.org/)
- [cz-conventional-changelog](https://github.com/commitizen/cz-conventional-changelog)

### Ejemplo de changelog generado

```md
## [1.0.0] - 2025-05-27

### Added

- feat: agrega componente de login

### Fixed

- fix: corrige error en validación de usuario
```

### FAQ

**¿Por qué mi commit fue rechazado?**

- Porque no sigue el formato Conventional Commits. Usa `npm run commit` o revisa los ejemplos.

**¿Por qué no sube la versión?**

- Asegúrate de tener commits válidos y el campo `version` en `package.json`.

**¿Puedo usar este flujo en cualquier proyecto Node.js?**

- Sí, solo copia la configuración y scripts.

---

### Scripts npm útiles

Puedes agregar estos scripts en tu `package.json` para facilitar tareas comunes:

```json
"scripts": {
  // ...otros scripts...
  "changelog": "cat CHANGELOG.md",
  "clean:tags": "git tag -l | xargs git tag -d && git fetch --tags",
  "release": "standard-version && git push --follow-tags origin main"
}
```

- `npm run changelog`: muestra el changelog por consola.
- `npm run clean:tags`: elimina todos los tags locales y los vuelve a sincronizar con el remoto (¡úsalo con precaución!).
- `npm run release`: solo versiona y sube los tags, no publica en npm.

Este README está listo para copiar y pegar en cualquier proyecto Node.js y tener un flujo de Conventional Commits, versionado y changelog automático.

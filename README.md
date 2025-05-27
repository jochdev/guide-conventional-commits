# Guía Práctica: Versionado y Commits Profesionales en Node.js con Nuxt 3

![Repo Banner](./public/repo-card.png)

> **Creado por jochdev**
>
> **Nota:** Esta guía fue implementada y probada en un proyecto limpio de Nuxt 3, pero puedes adaptarla a cualquier proyecto Node.js.

---

## Tabla de Contenido

- [¿Qué aprenderás?](#qué-aprenderás)
- [Instalación y configuración inicial](#1-instalación-y-configuración-inicial)
- [¿Cómo hacer un commit profesional?](#2-cómo-hacer-un-commit-profesional)
- [Flujo de trabajo recomendado](#3-flujo-de-trabajo-recomendado)
- [Ejemplo práctico](#4-ejemplo-práctico)
- [¿Cómo deshacer un release o tag erróneo?](#5-cómo-deshacer-un-release-o-tag-erróneo)
- [Troubleshooting (Solución de problemas)](#6-troubleshooting-solución-de-problemas)
- [Buenas prácticas y tips](#7-buenas-prácticas-y-tips)
- [Integración continua (CI) con GitHub Actions](#8-integración-continua-ci-con-github-actions)
- [Recursos útiles](#9-recursos-útiles)
- [Ejemplo de changelog generado](#10-ejemplo-de-changelog-generado)
- [Scripts npm útiles](#11-scripts-npm-útiles)

---

## ¿Qué aprenderás?

- Cómo estructurar tus commits de forma profesional usando Conventional Commits.
- Cómo automatizar el versionado y el changelog de tu proyecto.
- Cómo evitar errores comunes y mantener tu historial limpio.

---

## 1. Instalación y configuración inicial

### Paso 1: Instala las dependencias necesarias

```bash
npm install --save-dev commitizen cz-conventional-changelog standard-version husky @commitlint/cli @commitlint/config-conventional
```

### Paso 2: Configura tu `package.json`

Asegúrate de tener el campo `"version"` (ejemplo: `"version": "1.0.0"`). Si no existe, agrégalo manualmente la primera vez.

Agrega estos scripts y configuración:

```json
"scripts": {
  "commit": "cz",
  "release": "standard-version && git push --follow-tags origin main",
  "changelog": "cat CHANGELOG.md",
  "clean:tags": "git tag -l | xargs git tag -d && git fetch --tags"
},
"config": {
  "commitizen": {
    "path": "cz-conventional-changelog"
  }
}
```

### Paso 3: Configura commitlint

Crea el archivo `commitlint.config.js` con:

```js
export default { extends: ["@commitlint/config-conventional"] };
```

### Paso 4: Configura Husky para validar los commits

```bash
npx husky install
npx husky add .husky/commit-msg "npx --no -- commitlint --edit $1"
```

---

## 2. ¿Cómo hacer un commit profesional?

- Usa el comando:
  ```bash
  npm run commit
  ```
  o desde la extensión de VS Code, pero siempre con el formato:
  ```bash
  feat: agrega validación de email
  fix: corrige bug en login
  chore: actualiza dependencias
  docs: mejora documentación de uso
  ```
- Si el mensaje no es válido, el commit será rechazado.

---

## 3. Flujo de trabajo recomendado

1. Haz tus cambios en el código.
2. Por cada cambio o grupo de cambios, haz un commit siguiendo Conventional Commits.
3. Cuando termines tus cambios y confirmaciones, ejecuta:
   ```bash
   npm run release
   ```
   Esto:
   - Analiza todos los commits desde el último release/tag.
   - Actualiza la versión en package.json según los tipos de commit.
   - Genera/actualiza el CHANGELOG.md.
   - Crea un nuevo tag y lo sube a tu repositorio.

---

## 4. Ejemplo práctico

Supón que realizas 4 cambios en tu proyecto:

```bash
git add archivo1.js
git commit -m "feat: agrega validación de email"

git add archivo2.js
git commit -m "fix: corrige bug en login"

git add archivo3.js
git commit -m "chore: actualiza dependencias"

git add archivo4.js
git commit -m "docs: mejora documentación de uso"
```

Luego ejecuta:

```bash
npm run release
```

¡Y listo! Tu versión y changelog se actualizan automáticamente.

---

## 5. ¿Cómo deshacer un release o tag erróneo?

```bash
git tag -d vX.Y.Z
git push origin :refs/tags/vX.Y.Z
```

---

## 6. Troubleshooting (Solución de problemas)

- Si standard-version no sube la versión, revisa que el campo `version` esté presente en `package.json` y que existan commits válidos.
- Si tienes conflictos en el changelog, resuélvelos manualmente y vuelve a ejecutar el release.
- Si necesitas limpiar **todos** los tags locales y remotos (¡precaución!):

```bash
npm run clean:tags
```

---

## 7. Buenas prácticas y tips

- Realiza los releases solo en la rama principal (`main` o `master`).
- Si usas la extensión de VS Code para commits, asegúrate de escribir el mensaje en formato Conventional Commits.
- Si tienes dudas sobre el formato, ejecuta `npm run commit` al menos una vez para ver cómo debe ser el mensaje.

---

## 8. Integración continua (CI) con GitHub Actions

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

---

## 9. Recursos útiles

- [Conventional Commits](https://www.conventionalcommits.org/es/v1.0.0/)
- [standard-version](https://github.com/conventional-changelog/standard-version)
- [commitizen](https://commitizen-tools.github.io/commitizen/)
- [commitlint](https://commitlint.js.org/)
- [cz-conventional-changelog](https://github.com/commitizen/cz-conventional-changelog)

---

## 10. Ejemplo de changelog generado

```md
## [1.0.0] - 2025-05-27

### Added

- feat: agrega componente de login

### Fixed

- fix: corrige error en validación de usuario
```

---

## 11. Scripts npm útiles

- `npm run changelog`: muestra el changelog por consola.
- `npm run clean:tags`: elimina todos los tags locales y los vuelve a sincronizar con el remoto (¡úsalo con precaución!).
- `npm run release`: solo versiona y sube los tags, no publica en npm.

---

Este guía/README está listo para copiar y pegar en cualquier proyecto Node.js y tener un flujo profesional de Conventional Commits, versionado y changelog automático.

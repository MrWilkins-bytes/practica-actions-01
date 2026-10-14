# Práctica 1: Tu primer workflow con GitHub Actions

## 1. Objetivo

Crear un workflow de GitHub Actions que se ejecute al subir cambios a `main`, manualmente y al abrir una Pull Request dirigida a `main`.

## 2. Crear y clonar el repositorio

Crear el repositorio remoto con GitHub CLI:

```bash
gh repo create practica-actions-01 --public --add-readme --description "Práctica 1 de GitHub Actions"
```

Ir a la carpeta de trabajo y clonar el repositorio:

```bash
cd ~/Documents/1ºDAM/EntornoDesarrollo
git clone https://github.com/MrWilkins-bytes/practica-actions-01.git
cd practica-actions-01
```

Comprobar la ubicación, el estado del repositorio y el remoto:

```bash
pwd
git status
git remote -v
```

## 3. Crear el workflow

Crear las carpetas donde GitHub Actions busca los workflows:

```bash
mkdir -p .github/workflows
```

Crear el archivo `ci.yml`:

```bash
cat > .github/workflows/ci.yml <<'YAML'
name: CI

on:
  push:
    branches: [main]

jobs:
  saludo:
    runs-on: ubuntu-latest
    steps:
      - name: Descargar el código
        uses: actions/checkout@v4
      - name: Saludar
        run: echo "Hola desde GitHub Actions"
YAML
```

Consultar el contenido y comprobar los cambios:

```bash
cat .github/workflows/ci.yml
git status
```

## 4. Ejecutar el workflow al hacer push

Añadir el archivo, crear un commit y subirlo:

```bash
git add .github/workflows/ci.yml
git commit -m "ci: primer workflow"
git push
```

En GitHub, abrir la pestaña **Actions**, seleccionar el workflow **CI** y comprobar que el trabajo `saludo` termina correctamente.

## 5. Añadir la ejecución manual

Añadir `workflow_dispatch` dentro de `on` en `.github/workflows/ci.yml`:

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
```

Guardar los cambios y subirlos:

```bash
git add .github/workflows/ci.yml
git commit -m "ci: añadir ejecución manual"
git push
```

En **Actions**, seleccionar **CI**, pulsar **Run workflow** y comprobar el resultado.

## 6. Ejecutar el workflow mediante una Pull Request

Añadir el evento `pull_request` al workflow:

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
```

Crear una rama para la prueba y cambiar a ella:

```bash
git checkout -b feature/prueba-ci
```

Añadir una línea al README para generar un cambio:

```bash
echo "Prueba de GitHub Actions mediante Pull Request." >> README.md
cat README.md
git status
```

Guardar y subir los cambios de la rama:

```bash
git add .github/workflows/ci.yml README.md
git commit -m "ci: añadir disparador pull request"
git push origin feature/prueba-ci
```

Crear la Pull Request desde GitHub CLI:

```bash
gh pr create --base main --head feature/prueba-ci --title "Prueba de GitHub Actions" --body "Pull Request para comprobar el workflow CI."
gh pr view --web
```

En GitHub, comprobar que la Pull Request está dirigida a `main` y que el check `CI / saludo (pull_request)` finaliza correctamente.

**Importante:** la Pull Request se deja abierta, sin fusionar, ya que la práctica pide comprobar su ejecución, no hacer merge.

## 7. Comandos de consulta utilizados

```bash
gh --version
gh auth status
ls -la .github
ls -la .github/workflows
git branch --show-current
```

## 8. Evidencias

- Captura 1: ejecución del workflow al hacer push.
- Captura 2: ejecución manual mediante `workflow_dispatch`.
- Captura 3: ejecución del workflow al abrir la Pull Request.

## 9. Resultado

Se ha configurado un workflow llamado `CI` con tres eventos de ejecución: `push` sobre `main`, `workflow_dispatch` y `pull_request` dirigido a `main`. El trabajo `saludo` descarga el código y muestra el mensaje `Hola desde GitHub Actions`.

# libreria-api

API mínima de libros hecha con Flask. Es el proyecto de ejemplo de la clase de Jenkins.

## Correr local

```bash
python3 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
python app.py
```

Probar:

```bash
curl http://localhost:5000/health
curl -X POST http://localhost:5000/books -H "Content-Type: application/json" -d '{"title":"Dune"}'
curl http://localhost:5000/books
```

## Tests y lint

```bash
pytest
ruff check .
```

## Docker

```bash
docker build -t libreria-api .
docker run --rm -p 5000:5000 libreria-api
```

## Jenkins

El archivo `Jenkinsfile` define el pipeline. En lugar de archivar archivos, el
pipeline construye la imagen Docker y la publica en Docker Hub como
`$usuario/libreria-api:$VERSION` (y `:latest`).

Crea en Jenkins una credencial *Username with password* con ID `dockerhub`
(usuario de Docker Hub y un [access token](https://hub.docker.com/settings/security)).

Stages: dependencias, lint, test, build image, aprobación (solo prod), push y
deploy. El deploy corre un contenedor en el host (`dev` → 5000, `qa` → 5001,
`prod` → 5002).

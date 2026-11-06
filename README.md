# fullstack_developer_capstone
# Full Stack Developer Capstone - Guía de Arranque

## Arquitectura

```text
React
   ↓
Django
   ↓
Node.js (Express)
   ↓
MongoDB

Django
   ↓
Sentiment Analyzer (Code Engine)
```

---

# 1. Clonar proyecto

```bash
git clone https://github.com/edgardov29/xrwvm-fullstack_developer_capstone.git
cd xrwvm-fullstack_developer_capstone
```

---

# 2. Arrancar Django

Ir a:

```bash
cd server
```

Crear entorno virtual (si no existe):

```bash
virtualenv djangoenv
```

Activar entorno:

```bash
source djangoenv/bin/activate
```

Instalar dependencias:

```bash
python3 -m pip install -U -r requirements.txt
```

Crear migraciones:

```bash
python3 manage.py makemigrations
```

Aplicar migraciones:

```bash
python3 manage.py migrate --run-syncdb
```

Ejecutar servidor:

```bash
python3 manage.py runserver
```

---

# 3. Crear superusuario

```bash
python3 manage.py createsuperuser
```

Credenciales utilizadas en el laboratorio:

```text
username: root
password: root
```

---

---

# 5. Construir Frontend React

Nueva terminal:

```bash
cd server/frontend
```

Instalar dependencias:

```bash
npm install
```

Construir aplicación:

```bash
npm run build
```

---

# 6. Arrancar Node.js + MongoDB

Nueva terminal:

```bash
cd server/database
```

Construir imagen Docker:

```bash
docker build . -t nodeapp
```

Arrancar contenedores:

```bash
docker compose up
```

Verificar:

```bash
docker ps
```

Deben aparecer los contenedores:

```text
mongo_db
api
```

o similares.

---

# 7. Verificar API Node.js

Probar:

```bash
curl -X GET "https://TU-URL-3030/fetchDealers"
```

Debe devolver JSON.

---

# 8. Poblar CarMake y CarModel

Abrir:

```text
/djangoapp/get_cars
```

La primera vez ejecutará:

```python
initiate()
```

si la tabla está vacía.

Se insertan automáticamente:

```text
NISSAN
Mercedes
Audi
Kia
Toyota
```

y sus modelos asociados.

Si los datos ya existen:

```text
No hará nada.
```

---

# 9. Registrar modelos en Admin

Archivo:

```python
admin.py
```


---

# 10. Sentiment Analyzer (Code Engine)

Ir a:

```bash
cd server/djangoapp/microservices
```

Construir imagen:

```bash
docker build . -t us.icr.io/${SN_ICR_NAMESPACE}/senti_analyzer
```

Subir imagen:

```bash
docker push us.icr.io/${SN_ICR_NAMESPACE}/senti_analyzer
```

Crear aplicación:

```bash
ibmcloud ce application create \
--name sentianalyzer \
--image us.icr.io/${SN_ICR_NAMESPACE}/senti_analyzer \
--registry-secret icr-secret \
--port 5000
```

Resultado esperado:

```text
https://sentianalyzer.xxxxxxxxx.us-south.codeengine.appdomain.cloud
```

Guardar esta URL.

---

# 11. Configurar .env

Archivo:

```text
server/djangoapp/.env
```

Configurar:

```env
backend_url=https://TU-URL-3030
```

```env
sentiment_analyzer_url=https://sentianalyzer.xxxxxxxxx.us-south.codeengine.appdomain.cloud/
```

IMPORTANTE:

✅ Correcto

```env
sentiment_analyzer_url=https://...
```

❌ Incorrecto

```env
sentiment_analyzer_url= https://...
```

(no dejar espacios después del "=")

---

# 12. Reiniciar Django

Cada vez que se modifique:

```text
.env
settings.py
restapis.py
```

reiniciar:

```bash
Ctrl + C
```

```bash
python3 manage.py runserver
```

---

# 13. Comprobaciones rápidas

## Obtener coches

```text
/djangoapp/get_cars
```

Debe devolver:

```json
{
  "CarModels": [...]
}
```

---

## Obtener dealers

```text
/djangoapp/get_dealers
```

Debe devolver:

```json
{
  "status": 200,
  "dealers": [...]
}
```

---

## Dealer individual

```text
/dealer/1
```

Debe mostrar:

```text
Dealer + Reviews
```

---

## Analizador de sentimiento

Probar:

```text
https://sentianalyzer.xxxxx.us-south.codeengine.appdomain.cloud/analyze/great
```

Debe devolver algo parecido a:

```json
{
  "sentiment": "positive"
}
```

---

# Problemas comunes

## dealers = null

Causa:

```text
Node.js o MongoDB no están levantados
```

Solución:

```bash
cd server/database

docker build . -t nodeapp

docker compose up
```

---

## Error

```text
Network exception occurred
```

Causa:

```text
Node.js no responde
```

Comprobar:

```bash
docker ps
```

---

## Error

```text
object null is not iterable
```

en Dealers.jsx

Causa:

```text
retobj.dealers = null
```

Normalmente provocado por:

```text
Node.js apagado
```

---

## Error

```text
TypeError: 'NoneType' object is not subscriptable
```

en:

```python
response['sentiment']
```

Causa:

```text
Microservicio de sentimiento no disponible
```

Verificar:

```env
sentiment_analyzer_url
```

y el despliegue de Code Engine.

---

## Error CSRF

```text
403 Forbidden
```

Verificar:

```python
CSRF_TRUSTED_ORIGINS
```

y

```python
ALLOWED_HOSTS
```

---

## Error

```text
manifest.json 404
```

Puede ignorarse.

No afecta al funcionamiento del laboratorio.

---

# Comandos que normalmente ejecuto tras un reinicio

## Terminal 1

```bash
cd server

source djangoenv/bin/activate

python3 manage.py runserver
```

---

## Terminal 2

```bash
cd server/frontend

npm run build
```

---

## Terminal 3

```bash
cd server/database

docker build . -t nodeapp

docker compose up
```

---

# Verificación final

✅ Login

✅ Logout

✅ Registro

✅ Dealers

✅ Dealer Details

✅ Dealer Reviews

✅ Sentiment Analysis

✅ Admin Django

✅ CarMake

✅ CarModel

✅ Node.js

✅ MongoDB

✅ Code Engine
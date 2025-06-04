# Backend - Project Pizzería Mamma Mía

Install ```sh npm install``` / Run ```sh npm run dev```
(Change PORT in *index.js* to 5001 and check endpoints)

**Database:** 
- ../db/pizzas.json (Contiene todas las pizzas y prop id para llamar de a una)
- ../db/users.json (Test user)


## Endpoints
http://localhost:5001/

**Pizzas** 
- ```GET /api/pizzas``` / Obtener todas las pizzas
- ```GET /api/pizza/:id``` / Obtener una pizza por ID
**Autenticación** 
- ```POST /api/auth/login``` / Iniciar sesión
- ```POST /api/auth/register``` / Registrar nuevo usuario
- ```GET /api/auth/me``` / Obtener el perfil del usuario autenticado (requiere JWT).
- **Checkout** ```POST /api/checkout```  Procesar compra (requiere JWT)


## JWT
API para consumir un servicio de Auth con JWT.

1. Instalar ```npm install jsonwebtoken``` 
(en carpeta de proyecto backend)

2. Definir clave secreta: ```const SECRET_KEY = "tu_clave_secreta";``` 
(en un archivo de configuración o en .env)

3. Generar token (cuando usuario hace login o se registra)
```sh 
const jwt = require("jsonwebtoken");
const token = jwt.sign({ userId: usuario.id }, SECRET_KEY, { expiresIn: "1h" });";
```

4. Enviar el token en los headers con cada solicitud protegida:
```sh
Authorization Bearer token_jwt
```
5. Protege rutas con middleware. Crea un middleware para verificar el token JWT:
```sh
function authMiddleware(req, res, next) {
  const authHeader = req.headers.authorization;
  if (!authHeader) return res.status(401).json({ error: "Token requerido" });

  const token = authHeader.split(" ")[1];
  try {
    const decoded = jwt.verify(token, SECRET_KEY);
    req.user = decoded;
    next();
  } catch {
    res.status(401).json({ error: "Token inválido" });
  }
}
```
Usa este middleware en rutas protegidas:
```sh
app.post("/api/checkouts", authMiddleware, (req, res) => {
  // lógica de checkout
});
```

### Ejemplo JWT con Fetch

```js
await fetch("http://localhost:5000/api/checkout", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer token_jwt`,
  },
  body: JSON.stringify({
    cart: carrito,
  }),
});
```


## Checkout & Profile

### Checkout ```POST /api/checkout```
- Requiere token JWT en el header
- Body de la solicitud:
```json
{
  "cart": [
    {
      "id": 1,
      "cantidad": 2
    },
    ...
  ]
}
```

### Perfil ```POST /api/auth/me```
- Requiere token JWT en el header
- Devuelve la información del usuario autenticado.
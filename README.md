# 🛡️ Refresh Token API

Este proyecto es un backend en Node.js que implementa un sistema de autenticación con **JWT**, almacenamiento de tokens en **PostgreSQL** y un sistema de historial de operaciones.

---

## 📦 Variables de entorno

Configura un archivo `.env` en la raíz del proyecto con las siguientes variables:

```bash
# Credenciales de autenticación
AUTH_USERNAME=JULIAN
AUTH_PASSWORD=1234
JWT_SECRET=supersecret

# Configuración de PostgreSQL
PGUSER=postgres
PGPASSWORD=
PGHOST=localhostNo hay código seleccionado para mejorar. Sin embargo, se puede agregar un ejemplo de código para implementar la autenticación con JWT y almacenamiento de tokens en PostgreSQL. Aquí hay un ejemplo:

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');
const { Pool } = require('pg');

const app = express();
app.use(express.json());

const pool = new Pool({
  user: process.env.PGUSER,
  host: process.env.PGHOST,
  database: process.env.PGDATABASE,
  password: process.env.PGPASSWORD,
  port: process.env.PGPORT,
});

const jwtSecret = process.env.JWT_SECRET;

app.post('/login', async (req, res) => {
  const { username, password } = req.body;
  const user = await pool.query('SELECT * FROM users WHERE name = $1 AND password = $2', [username, password]);
  if (user.rows.length === 0) {
    return res.status(401).json({ message: 'Credenciales inválidas' });
  }
  const accessToken = jwt.sign({ username }, jwtSecret, { expiresIn: '1h' });
  const refreshToken = jwt.sign({ username }, jwtSecret, { expiresIn: '7d' });
  await pool.query('INSERT INTO tokens (username, access_token, refresh_token) VALUES ($1, $2, $3)', [username, accessToken, refreshToken]);
  return res.json({ accessToken, refreshToken });
});

app.post('/refresh-token', async (req, res) => {
  const { refreshToken } = req.body;
  try {
    const decoded = jwt.verify(refreshToken, jwtSecret);
    const accessToken = jwt.sign({ username: decoded.username }, jwtSecret, { expiresIn: '1h' });
    return res.json({ accessToken });
  } catch (error) {
    return res.status(401).json({ message: 'Token de refresco inválido' });
  }
});

app.listen(3000, () => {
  console.log('Servidor escuchando en el puerto 3000');
});
```
PGPORT=
PGDATABASE=

🔑 Tabla tokens
Almacena los tokens de acceso y refresh generados para cada usuario.

CREATE TABLE tokens (
  id SERIAL PRIMARY KEY,
  username VARCHAR(50) UNIQUE NOT NULL,
  access_token TEXT,
  refresh_token TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

👤 Tabla users
Almacena los usuarios registrados, sus contraseñas (en texto plano por ahora, se recomienda hashearlas en producción) y sus roles.

CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) UNIQUE NOT NULL,
  password TEXT NOT NULL,
  role VARCHAR(20) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

📝 Tabla historial
Registra las acciones realizadas en el sistema (por ejemplo, inicio de sesión exitoso o fallido).

CREATE TABLE historial (
  id SERIAL PRIMARY KEY,
  username VARCHAR(50),
  accion VARCHAR(100),
  estado VARCHAR(20),
  mensaje TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

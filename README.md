# 🛡️ Refresh Token API

Este proyecto es un backend en Node.js que implementa un sistema de autenticación con **JWT**, almacenamiento de tokens en **PostgreSQL** y un sistema de historial de operaciones.

---

## 📦 Variables de entorno

Configura un archivo `.env` en la raíz del proyecto con las siguientes variables:


# Credenciales de autenticación
AUTH_USERNAME=JULIAN
AUTH_PASSWORD=1234
JWT_SECRET=supersecret
ROLE = admin, user
# Configuración de PostgreSQL
PGUSER=postgres
PGPASSWORD=
PGHOST=localhost
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

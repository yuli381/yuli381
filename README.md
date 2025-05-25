/proyecto-gestion-usuarios
│
├── backend/
│   ├── index.js
│   ├── routes/users.js
│   ├── swagger.json
│   └── package.json
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── context/
│   │   ├── pages/
│   │   ├── App.js
│   │   └── index.js
│   └── package.json
│
└── README.md

const express = require('express');
const cors = require('cors');
const usersRouter = require('./routes/users');
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');

const app = express();
app.use(cors());
app.use(express.json());

app.use('/api/users', usersRouter);
app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));

app.listen(4000, () => console.log("Servidor ejecutándose en http://localhost:4000"));

const express = require('express');
const router = express.Router();

let users = [{ id: 1, name: 'Juan Pérez' }];

router.get('/', (req, res) => res.json(users));

router.post('/', (req, res) => {
  const user = { id: Date.now(), name: req.body.name };
  users.push(user);
  res.status(201).json(user);
});

router.delete('/:id', (req, res) => {
  users = users.filter(u => u.id !== parseInt(req.params.id));
  res.status(204).send();
});

module.exports = router;

{
  "openapi": "3.0.0",
  "info": {
    "title": "API de Usuarios",
    "version": "1.0.0"
  },
  "paths": {
    "/api/users": {
      "get": {
        "summary": "Obtener lista de usuarios",
        "responses": {
          "200": {
            "description": "Lista de usuarios"
          }
        }
      },
      "post": {
        "summary": "Crear usuario",
        "requestBody": {
          "required": true,
          "content": {
            "application/json": {
              "schema": { "$ref": "#/components/schemas/User" }
            }
          }
        },
        "responses": {
          "201": {
            "description": "Usuario creado"
          }
        }
      }
    }
  },
  "components": {
    "schemas": {
      "User": {
        "type": "object",
        "properties": {
          "name": { "type": "string" }
        }
      }
    }
  }
}

import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import AddUser from './pages/AddUser';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/agregar" element={<AddUser />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;

import { useEffect, useState } from 'react';
import axios from 'axios';

function Home() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    axios.get('http://localhost:4000/api/users')
      .then(res => setUsers(res.data));
  }, []);

  return (
    <div>
      <h2>Lista de Usuarios</h2>
      <ul>
        {users.map(u => <li key={u.id}>{u.name}</li>)}
      </ul>
    </div>
  );
}

export default Home;
import { useState } from 'react';
import axios from 'axios';

function AddUser() {
  const [name, setName] = useState('');

  const handleAdd = () => {
    axios.post('http://localhost:4000/api/users', { name })
      .then(() => alert('Usuario agregado'));
  };

  return (
    <div>
      <h2>Agregar Usuario</h2>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button onClick={handleAdd}>Agregar</button>
    </div>
  );
}

export default AddUser;

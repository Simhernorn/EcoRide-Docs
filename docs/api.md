# Documentació de rutes fictícies de l'API d'EcoRide

Totes les rutes comencen per `/api` i requereixen un token JWT a la capçalera `Authorization: Bearer <token>`. Les respostes són en format JSON.

## Resum de rutes

| Mètode | Ruta | Finalitat |
|---|---|---|
| GET | `/api/stations` | Llistar estacions |
| GET | `/api/bikes` | Consultar bicicletes |
| POST | `/api/reservations` | Crear una reserva |
| GET | `/api/users/{id}` | Consultar un usuari |

---

## 1. Llistar estacions

- **Mètode HTTP:** `GET`
- **Ruta:** `/api/stations`
- **Finalitat:** retorna totes les estacions d'EcoRide amb la seua ubicació i la disponibilitat de bicis i llocs lliures.

**Exemple de resposta (200 OK):**

```json
{
  "total": 2,
  "estacions": [
    {
      "id": 12,
      "nom": "Estació Centre",
      "latitud": 39.4699,
      "longitud": -0.3763,
      "bicisDisponibles": 2,
      "llocsLliures": 13,
      "activa": true
    },
    {
      "id": 13,
      "nom": "Estació Ciutat de les Arts",
      "latitud": 39.4543,
      "longitud": -0.3507,
      "bicisDisponibles": 0,
      "llocsLliures": 15,
      "activa": true
    }
  ]
}
```

---

## 2. Consultar bicicletes

- **Mètode HTTP:** `GET`
- **Ruta:** `/api/bikes`
- **Finalitat:** retorna les bicicletes del sistema amb el seu tipus, l'estat i la bateria (si és elèctrica). Admet filtres opcionals, com ara `?estacioId=12&estat=disponible`.

**Exemple de resposta (200 OK):**

```json
{
  "total": 2,
  "bicis": [
    {
      "id": 301,
      "tipus": "electrica",
      "bateria": 87,
      "estacioId": 12,
      "estat": "disponible"
    },
    {
      "id": 302,
      "tipus": "convencional",
      "bateria": null,
      "estacioId": 12,
      "estat": "disponible"
    }
  ]
}
```

---

## 3. Crear una reserva

- **Mètode HTTP:** `POST`
- **Ruta:** `/api/reservations`
- **Finalitat:** reserva una bicicleta per a un usuari durant 10 minuts. Passat este temps sense desbloquejar-la, la reserva caduca automàticament.

**Exemple de cos de la petició:**

```json
{
  "usuariId": 45,
  "bicicletaId": 301,
  "estacioId": 12
}
```

**Exemple de resposta (201 Created):**

```json
{
  "id": 9001,
  "usuariId": 45,
  "bicicletaId": 301,
  "estacioId": 12,
  "estat": "activa",
  "creadaEn": "2026-11-12T09:45:00+01:00",
  "caducaEn": "2026-11-12T09:55:00+01:00"
}
```

---

## 4. Consultar un usuari

- **Mètode HTTP:** `GET`
- **Ruta:** `/api/users/{id}`
- **Finalitat:** retorna el perfil d'un usuari, amb els viatges fets, els punts verds acumulats i el CO₂ evitat. Per exemple, `/api/users/45`.

**Exemple de resposta (200 OK):**

```json
{
  "id": 45,
  "nom": "Marta Soler",
  "correu": "marta.soler@example.com",
  "viatgesTotals": 57,
  "puntsVerds": 320,
  "co2EvitatKg": 18.4,
  "creatEn": "2026-03-14"
}
```
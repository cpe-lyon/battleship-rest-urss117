# Conception

Retranscrivez ici le résultat de votre conception de l'API REST.

## Liste des Endpoints

### 1. Authentification

#### POST /auth/register
- **Requête :**
  - **URL :** `/auth/register`
  - **Méthode HTTP :** `POST`
  - **En-têtes HTTP :** `Content-Type: application/json`
  - **Paramètres d'URL :** aucun
  - **Corps de message :** `{ "username": "...", "password": "..." }`
- **Réponse :**
  - **Cas succès :** Code `201 Created`, corps : `{ "message": "Utilisateur créé" }`
  - **Cas erreur :** Code `400 Bad Request`, corps : `{ "error": "Utilisateur déjà existant" }`
- **Authentification requise :** Non

#### POST /auth/token
- **Requête :**
  - **URL :** `/auth/token`
  - **Méthode HTTP :** `POST`
  - **En-têtes HTTP :** `Authorization: Basic <base64(username:password)>`
  - **Paramètres d'URL :** aucun
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `{ "access_token": "...", "issued": "...", "expires": "..." }`
  - **Cas erreur :** Code `401 Unauthorized`, corps : `{ "error": "Invalid credentials" }`
- **Authentification requise :** Non

#### GET /auth/token
- **Requête :**
  - **URL :** `/auth/token`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** `Authorization: Bearer <jwt>`
  - **Paramètres d'URL :** aucun
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas JWT valide :** Code `200 OK`, corps : `{ "status": "valid", "payload": { "user_id": 1, "username": "bean" } }`
  - **Cas JWT expiré :** Code `200 OK`, corps : `{ "status": "expired" }`
  - **Cas JWT invalide :** Code `200 OK`, corps : `{ "status": "invalid" }`
- **Authentification requise :** Non

---

### 2. Référentiel

#### GET /ships
- **Requête :**
  - **URL :** `/ships`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** aucun
  - **Paramètres d'URL :** aucun
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `[{ "id": 1, "name_fr": "Porte-avion", "name": "aircraft-carrier", "length": 5 }, ...]`
- **Authentification requise :** Non

---

### 3. Utilisateurs

#### GET /users
- **Requête :**
  - **URL :** `/users`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** aucun
  - **Paramètres d'URL :** aucun
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `[{ "id": 1, "name": "bean" }, ...]`
- **Authentification requise :** Non

---

### 4. Gestion des Parties

#### POST /games
- **Requête :**
  - **URL :** `/games`
  - **Méthode HTTP :** `POST`
  - **En-têtes HTTP :** aucun *(à terme : `Authorization: Bearer <jwt>`)*
  - **Paramètres d'URL :** aucun
  - **Corps de message :** aucun *(les 2 joueurs existants sont utilisés par défaut)*
- **Réponse :**
  - **Cas succès :** Code `201 Created`, corps : `{ "id": 1, "player1_id": 1, "player2_id": 2, "status": "placement", "created_at": "..." }`
- **Authentification requise :** Non *(temporaire)*

#### GET /games
- **Requête :**
  - **URL :** `/games`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** aucun
  - **Paramètres d'URL :** aucun
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : tableau de parties (même format que `GET /games/{id}`)
- **Authentification requise :** Non

#### GET /games/{id}
- **Requête :**
  - **URL :** `/games/{id}`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** aucun
  - **Paramètres d'URL :** `id` — identifiant de la partie
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `{ "id": 1, "player1_id": 1, "player2_id": 2, "status": "placement", "created_at": "..." }`
  - **Cas erreur :** Code `404 Not Found`, corps : `{ "error": "Game not found" }`
- **Authentification requise :** Non

---

### 5. Placement des Navires

#### PUT /games/{gameId}/players/{playerId}/ships/{shipId}
- **Requête :**
  - **URL :** `/games/{gameId}/players/{playerId}/ships/{shipId}`
  - **Méthode HTTP :** `PUT`
  - **En-têtes HTTP :** `Content-Type: application/json`
  - **Paramètres d'URL :** `gameId`, `playerId` *(temporaire)*, `shipId` (1–5)
  - **Corps de message :** `{ "x": 2, "y": 3, "orientation": "H" }`
- **Réponse :**
  - **Cas création :** Code `201 Created`, corps : `{ "ship_id": 1, "x": 2, "y": 3, "orientation": "H" }`
  - **Cas mise à jour :** Code `200 OK`, corps : `{ "ship_id": 1, "x": 2, "y": 3, "orientation": "H" }`
  - **Cas erreur position :** Code `400 Bad Request`, corps : `{ "error": "Invalid position or orientation" }`
  - **Cas introuvable :** Code `404 Not Found`, corps : `{ "error": "Not found" }`
- **Authentification requise :** Non *(temporaire)*

#### GET /games/{gameId}/players/{playerId}/ships
- **Requête :**
  - **URL :** `/games/{gameId}/players/{playerId}/ships`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** aucun
  - **Paramètres d'URL :** `gameId`, `playerId`
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `[{ "ship_id": 1, "x": 2, "y": 3, "orientation": "H" }, ...]` (tableau vide si aucun placement)
- **Authentification requise :** Non *(à terme : restreint pour éviter la triche)*

---

### 6. Tirs

#### POST /games/{gameId}/shots
- **Requête :**
  - **URL :** `/games/{gameId}/shots`
  - **Méthode HTTP :** `POST`
  - **En-têtes HTTP :** `Content-Type: application/json`
  - **Paramètres d'URL :** `gameId`
  - **Corps de message :** `{ "player_id": 1, "x": 4, "y": 6 }` *(`player_id` temporaire)*
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `{ "x": 4, "y": 6, "result": "miss" }` ou `{ "x": 4, "y": 6, "result": "hit", "ship_id": 3 }`
  - **Cas case déjà tirée :** Code `400 Bad Request`, corps : `{ "error": "Already shot at this position" }`
  - **Cas mauvais tour :** Code `403 Forbidden`, corps : `{ "error": "Not your turn" }`
- **Authentification requise :** Non *(temporaire)*

#### GET /games/{gameId}/shots
- **Requête :**
  - **URL :** `/games/{gameId}/shots`
  - **Méthode HTTP :** `GET`
  - **En-têtes HTTP :** aucun
  - **Paramètres d'URL :** `gameId`
  - **Corps de message :** aucun
- **Réponse :**
  - **Cas succès :** Code `200 OK`, corps : `[{ "shooter_id": 1, "x": 4, "y": 6, "result": "miss" }, ...]`
- **Authentification requise :** Non

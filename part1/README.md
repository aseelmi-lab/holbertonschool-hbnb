# HBnB - UML Technical Documentation

## Overview
This document contains the UML diagrams for the HBnB application Business Logic layer.

---

## Task 1 - Class Diagram

\`\`\`mermaid
classDiagram
  class BaseModel {
    +UUID4 id
    +DateTime created_at
    +DateTime updated_at
    +save() None
    +to_dict() dict
    +update(data) None
  }
  class User {
    +String first_name
    +String last_name
    +String email
    +String password_hash
    +Boolean is_admin
    +register() None
    +authenticate(password) Boolean
    +update_profile(data) None
    +delete_account() None
  }
  class Place {
    +String title
    +String description
    +Float price_per_night
    +Float latitude
    +Float longitude
    +Integer max_guests
    +UUID4 owner_id
    +create_listing() None
    +update_listing(data) None
    +delete_listing() None
    +get_reviews() list
    +get_amenities() list
    +calculate_average_rating() Float
  }
  class Review {
    +Integer rating
    +String comment
    +UUID4 user_id
    +UUID4 place_id
    +create_review() None
    +update_review(data) None
    +delete_review() None
    +validate_rating() Boolean
  }
  class Amenity {
    +String name
    +String description
    +create() None
    +update(data) None
    +delete() None
  }
  BaseModel <|-- User : Inheritance
  BaseModel <|-- Place : Inheritance
  BaseModel <|-- Review : Inheritance
  BaseModel <|-- Amenity : Inheritance
  User "1" --> "0..*" Place : owns
  User "1" --> "0..*" Review : writes
  Place "1" --> "0..*" Review : has
  Place "0..*" --> "0..*" Amenity : includes
\`\`\`

---

## Task 2 - Package Diagram

\`\`\`mermaid
graph TD
  subgraph Presentation["Presentation Layer"]
    API[REST API Endpoints]
    SVC[Service Classes]
  end
  subgraph Business["Business Logic Layer"]
    USER[User Model]
    PLACE[Place Model]
    REVIEW[Review Model]
    AMENITY[Amenity Model]
  end
  subgraph Persistence["Persistence Layer"]
    REPO[Repository Interface]
    DB[(Database)]
  end
  Presentation --> Business
  Business --> Persistence
\`\`\`

---

## Task 3 - Sequence Diagrams

### 3.1 User Registration

\`\`\`mermaid
sequenceDiagram
  actor User
  participant API
  participant UserModel
  participant DB
  User->>API: POST /api/users/register
  API->>UserModel: validate(data)
  UserModel-->>API: valid
  API->>UserModel: create_user(data)
  UserModel->>DB: INSERT INTO users
  DB-->>UserModel: success
  UserModel-->>API: user_object
  API-->>User: 201 Created
\`\`\`

### 3.2 Place Creation

\`\`\`mermaid
sequenceDiagram
  actor Host
  participant API
  participant PlaceModel
  participant DB
  Host->>API: POST /api/places
  API->>PlaceModel: validate(data)
  PlaceModel-->>API: valid
  API->>PlaceModel: create_listing(data)
  PlaceModel->>DB: INSERT INTO places
  DB-->>PlaceModel: success
  PlaceModel-->>API: place_object
  API-->>Host: 201 Created
\`\`\`

### 3.3 Review Submission

\`\`\`mermaid
sequenceDiagram
  actor Guest
  participant API
  participant ReviewModel
  participant DB
  Guest->>API: POST /api/places/{id}/reviews
  API->>ReviewModel: validate_rating(data)
  ReviewModel-->>API: valid
  API->>ReviewModel: create_review(data)
  ReviewModel->>DB: INSERT INTO reviews
  DB-->>ReviewModel: success
  ReviewModel-->>API: review_object
  API-->>Guest: 201 Created
\`\`\`

### 3.4 Fetching List of Places

\`\`\`mermaid
sequenceDiagram
  actor User
  participant API
  participant PlaceModel
  participant DB
  User->>API: GET /api/places
  API->>PlaceModel: get_all_places()
  PlaceModel->>DB: SELECT * FROM places
  DB-->>PlaceModel: places_list
  PlaceModel-->>API: places_objects
  API-->>User: 200 OK + JSON list
\`\`\`

---

## Entity Descriptions

| Entity | Role |
|--------|------|
| BaseModel | Abstract base with id, timestamps, and shared methods |
| User | Represents guests and hosts; handles auth and profile |
| Place | A rental listing owned by a User |
| Review | A rating and comment left by a User on a Place |
| Amenity | A feature that can be offered at a Place |

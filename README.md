# Software Dev. OLA2-db 
# Timothy Busk Mortensen - cph-tm246@cphbusiness.dk 

# Relationer i Fitnesscenterets ER-model

## 1. Medlemmer og Træningshold (M:N-relation via Booking)
- Et medlem kan deltage i flere træningshold.
- Et træningshold kan have flere medlemmer.
- Dette håndteres via **Booking**, som skaber en mange-til-mange-relation.
- **Foreign Keys:**
  - `bookings.member_id → members.member_id`
  - `bookings.class_id → classes.class_id`

## 2. Instruktør og Træningshold (1:M-relation)
- En instruktør kan undervise på flere træningshold.
- Hvert træningshold har præcis én ansvarlig instruktør.
- **Foreign Key:**  
  - `classes.instructor_id → instructors.instructor_id`

## 3. Medlem og Betalinger (1:M-relation)
- Et medlem kan foretage flere betalinger over tid.
- Hver betaling er knyttet til ét bestemt medlem.
- **Foreign Key:**  
  - `payments.member_id → members.member_id`

## 4. Booking (Mange-til-mange-relation mellem Medlem og Træningshold)
- Booking-tabel forbinder medlemmer og træningshold.
- Et medlem kan have flere bookinger.
- Et træningshold kan have flere bookinger.
- **Foreign Keys:**  
  - `bookings.member_id → members.member_id`
  - `bookings.class_id → classes.class_id`

---

# Forretningsregler og Attributter

- **Medlemstype (`membership_type`)**  
  - Et medlem har én fast medlemskabstype (Basis, Premium, Elite).  
  - Dette lagres som en **attribut** i `members`-tabellen frem for en separat tabel, da medlemskabstypen ikke har yderligere egenskaber.

- **Rabat (`discount`)**  
  - Et medlem kan modtage en rabat i særlige tilfælde.  
  - Lagres som en **attribut** i `members`-tabellen, da der kun er én rabat per medlem.  
  - Hvis rabatter bliver mere komplekse (fx flere rabatter per medlem over tid), kan de flyttes til en separat `discounts`-tabel.

- **Maksimalt antal deltagere (`max_participants`)**  
  - Hvert træningshold har et fast maksimum af deltagere.  
  - Dette lagres som en **attribut** i `classes`-tabellen frem for en separat tabel, da det er en fast egenskab pr. hold.  
  - Hvis deltagergrænsen skulle variere per session, ville det kræve en `class_sessions`-tabel.

![ER Diagram](ER-diagram.png)

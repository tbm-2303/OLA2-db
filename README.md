# Software Dev. OLA2-db 
# Timothy Busk Mortensen - cph-tm246@cphbusiness.dk 

## Kommentarer til ER design:

### Vi har brugt en Booking-tabel til at håndtere mange-til-mange-relationen mellem medlemmer og træningshold.

### Betaling håndterer medlemskabsbetalinger samt eventuelle rabatter.

### Instruktør er knyttet til træningshold som en 1:M-relation, da hver klasse har en instruktør, men en instruktør kan undervise flere hold.


# Relationer i Fitnesscenterets ER-model

## 1. Medlem og Træningshold (M:N-relation via Booking)
- Et medlem kan deltage i flere træningshold.
- Et træningshold kan have flere medlemmer.
- Dette håndteres via **Booking**, som skaber en mange-til-mange-relation.

## 2. Instruktør og Træningshold (1:M-relation)
- En instruktør kan undervise på flere træningshold.
- Hvert træningshold har præcis én ansvarlig instruktør.
- **Foreign Key:** `classes.instructor_id → instructors.instructor_id`

## 3. Medlem og Betalinger (1:M-relation)
- Et medlem kan foretage flere betalinger over tid.
- Hver betaling er knyttet til ét bestemt medlem.
- **Foreign Key:** `payments.member_id → members.member_id`

## 4. Booking (Mange-til-mange-relation mellem Medlem og Træningshold)
- Booking-tabel forbinder medlemmer og træningshold.
- Et medlem kan have flere bookinger.
- Et træningshold kan have flere bookinger.
- **Foreign Keys:**  
  - `bookings.member_id → members.member_id`  
  - `bookings.class_id → classes.class_id`

## 5. Medlem og Medlemstyper (1:M-relation implicit via attribut)
- Medlemstype (`membership_type`) er en attribut i `members`-tabellen.
- Et medlem har én medlemstype (Basis, Premium, Elite).

## 6. Medlem og Rabat (1:1 eller 1:M-relation)
- Et medlem kan modtage rabat i særlige tilfælde.
- Rabat er en attribut i `members`-tabellen (`discount`).

## 7. Træningshold og Maks Deltagere (1:1-relation)
- Hvert træningshold har et maksimalt antal deltagere (`max_participants`).

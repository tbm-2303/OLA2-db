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

## ER Diagram
![ER Diagram](ER-diagram.png)

## Fordele og ulemper ved at oprette en `membership_type` tabel

### Fordele
1. **Bedre dataintegritet og mindre redundans**  
   - Hvis vi beholder `membership_type` som en simpel attribut i `members`, skal vi gentage information om medlemskab for hver medlem.  
   - Ved at oprette en `memberships`-tabel sikrer vi, at medlemskabstyper kun lagres ét sted, hvilket forhindrer inkonsistens.  

2. **Lettere at ændre medlemskabspriser**  
   - Hvis vi senere ændrer prisen for Premium, behøver vi kun at opdatere én række i `memberships` i stedet for alle medlemmer med Premium-medlemskab.  

3. **Gør systemet mere fleksibelt**  
   - Hvis vi vil tilføje flere medlemskabstyper i fremtiden, kan vi bare tilføje en ny række i `memberships`, uden at ændre database-strukturen.   

4. **Mulighed for at kontrollere adgang til klasser dynamisk**  
   - Med en `membership_classes` tabel kan vi styre præcist, hvilke klasser et medlemskab giver adgang til.  

---

### Ulemper
1. **Flere JOINs i forespørgsler**  
   - For at få information om et medlems medlemskab, skal vi lave en `JOIN` mellem `members` og `memberships`, hvilket kan påvirke ydeevnen ved mange forespørgsler.  

2. **Lidt mere kompleksitet i database-design**  
   - Vi skal oprette en ekstra tabel (`memberships`) og sikre, at alle medlemmer refererer til en gyldig `membership_id`.  
   - Kræver lidt mere administration af foreign-keys (FK).  

3. **Måske unødvendigt ved små databaser**  
   - Hvis fitnesscentret kun har 3 faste medlemskaber, og de sjældent ændres, kan det være overkill at oprette en ekstra tabel.  
   - Hvis der sjældent ændres i medlemskaber, kan en simpel ENUM-type i `members` være tilstrækkelig.  

---
### konklusion 
  - Modellen overholder de tre første normalformer i forvejen, men det er besluttet at oprette en memberships tabel. Selv om det introducerer en grad a kompleksitet, så er fordelene større end ulemperne. Jeg vil gerne være mere fleksibel i designet, da vi stadig kan nå at ændre mening på nuværende tidspunkt i processen. 
## NYT ER Diagram
![ER Diagram](ER-diagram2.png)




# Database Normalisering: 1NF, 2NF og 3NF
---
## **Første Normalform (1NF)**

### **Definition**
En tabel er i Første Normalform (1NF) hvis:
1. Alle kolonner indeholder atomare værdier** (dvs. ingen kolonne må have flere værdier i én celle).
2. Hver kolonne indeholder værdier af samme datatype** (f.eks. må en kolonne ikke blande tal og tekst).
3. Hver række har en unik identifikation**, ofte ved hjælp af en primærnøgle.

**Eksempel på brud på 1NF:**  
| member_id | name  | phone_numbers     | membership_type |  
|-----------|-------|------------------|----------------|  
| 1         | Anna  | 12345678, 87654321 | Premium        |  
| 2         | Peter | 23456789          | Basis          |  

**Løsning:**  
Opdel `phone_numbers` i en ny tabel:  

**Opdateret tabel `members` (1NF):**  
| member_id | name  | membership_type |  
|-----------|-------|----------------|  
| 1         | Anna  | Premium        |  
| 2         | Peter | Basis          |  

**Ny tabel `member_phones`:**  
| phone_id | member_id | phone_number |  
|----------|-----------|-------------|  
| 1        | 1         | 12345678     |  
| 2        | 1         | 87654321     |  
| 3        | 2         | 23456789     |  

---
## **Anden Normalform (2NF)**

### **Definition**
En tabel er i **Anden Normalform (2NF)** hvis:
1. **Den er i 1NF**.
2. **Alle ikke-nøgleattributter er fuldstændigt funktionelt afhængige af hele primærnøglen**.  
   - Dvs. der må ikke være **partielle afhængigheder**, hvor en attribut kun afhænger af en del af en sammensat primærnøgle.


**Eksempel på brud på 2NF (før normalisering):**  
| booking_id | member_id | class_id | member_name | class_name |  
|------------|----------|---------|-------------|------------|  
| 1          | 101      | 5001    | Anna        | Yoga       |  
| 2          | 102      | 5002    | Peter       | Spinning   |  

**Problemet:**  
- `member_name` afhænger kun af `member_id`, ikke hele primærnøglen (`booking_id`).  
- `class_name` afhænger kun af `class_id`, ikke hele primærnøglen (`booking_id`).  

**Løsning:**  
- Opdel tabellen i separate tabeller for medlemmer og klasser.  

**Opdaterede tabeller (2NF):**  

**`bookings`:**  
| booking_id | member_id | class_id |  
|------------|----------|---------|  
| 1          | 101      | 5001    |  
| 2          | 102      | 5002    |  

**`members`:**  
| member_id | member_name |  
|----------|-------------|  
| 101      | Anna        |  
| 102      | Peter       |  

**`classes`:**  
| class_id | class_name  |  
|---------|------------|  
| 5001    | Yoga       |  
| 5002    | Spinning   |  

---

## **Tredje Normalform (3NF)**

### **Definition**
En tabel er i Tredje Normalform (3NF) hvis:
1. Den er i 2NF.
2. Den har ingen transitive afhængigheder, dvs. ingen ikke-nøgleattribut afhænger af en anden ikke-nøgleattribut.

### **Hvad er en transitiv afhængighed?**
En `transitiv afhængighed` opstår, når en ikke-nøgleattribut afhænger af en anden ikke-nøgleattribut, i stedet for at afhænge direkte af primærnøglen.

Hvis vi har følgende afhængigheder:

- **A → B** (A bestemmer B)
- **B → C** (B bestemmer C)

Så er der en transitiv afhængighed: **A → C**.

**Eksempel på brud på 3NF (før normalisering):**  
| member_id | name  | membership_type | membership_price |  
|-----------|------|----------------|-----------------|  
| 1         | Anna | Premium        | 299            |  
| 2         | Peter | Basis          | 199            |  

**Problemet:**  
- `membership_price` afhænger af `membership_type`, ikke direkte af `member_id`.  
- Hvis prisen ændres, skal vi opdatere **alle rækker** med den medlemskabstype.  

**Løsning:**  
- Flyt `membership_type` og `membership_price` til en separat `memberships`-tabel.  

**Opdaterede tabeller (3NF):**  

**`members`:**  
| member_id | name  | membership_id |  
|-----------|------|--------------|  
| 1         | Anna | 1            |  
| 2         | Peter | 2            |  

**`memberships`:**  
| membership_id | membership_type | membership_price |  
|--------------|----------------|-----------------|  
| 1            | Premium        | 299            |  
| 2            | Basis          | 199            |  

---
## **Opsummering af Normalformer**
| Normalform | Krav |
|------------|------|
| **1NF** | Ingen gentagende grupper eller multi-værdier i en enkelt celle. |
| **2NF** | Ingen partielle afhængigheder; hver ikke-nøgleattribut afhænger af hele primærnøglen. |
| **3NF** | Ingen transitive afhængigheder; ikke-nøgleattributter må ikke afhænge af andre ikke-nøgleattributter. |

---


## Boyce-Codd Normal Form (BCNF)

### Definition
En tabel er i Boyce–Codd Normalform (BCNF), hvis:
1. For hver `ikke-triviel funktionel afhængighed` (dvs. en afhængighed X → Y, hvor Y ikke er en del af X) er X en supernøgle for tabellen.
2. Det betyder, at alle `determinanter` (de attributter, der bestemmer andre attributter) skal være supernøgler.
3. BCNF fjerner alle redundanser, der opstår på grund af funktionelle afhængigheder, hvor determinanten ikke er en supernøgle. Ved at sikre, at alle funktionelle afhængigheder er “baseret på” supernøgler, forhindres opdaterings-, indsætnings- og sletningsanomalier.


I tabellen **members** er `member_id` primærnøgle, og alle andre attributter (navn, email, phone, start_date, membership_id og discount) bør afhænge af member_id. Et potentielt problem kunne opstå, hvis `membership_id` (remmednøgle) funktionelt bestemmer discount, dvs. hvis rabatten er ens for alle medlemmer med samme medlemskabstype, vil der være en funktionel afhængighed: membership_id → discount. Men i tabellen members er membership_id ikke en supernøgle (den unikt identificerer ikke en række, da flere medlemmer kan have samme medlemskabstype). Hvis discount derimod er en individuel egenskab for hvert medlem (f.eks. baseret på personlige forhandlinger), er denne afhængighed ikke problematisk, og tabellen forbliver i BCNF.


---






























## Fjerde normalform (NF4)

### Definition
Fjerde normalform sigter mod at fjerne `multiværdiafhængigheder` i en tabel, hvilket betyder, at hvis en bestemt nøgle(eller attribut)bestemmer flere uafhængige sæt værdier for andre attributter, skal disse opdeles for at undgå redundans og opdateringsanomalier.



`Multiværdiafhængighed` sker, når en attribut `A` i en tabel entydigt bestemmer et sæt værdier for en anden attribut `B`(noteret som A →→ B).
Denne afhængighed er `ikke-trivial`, hvis `B` ikke er en del af `A`, og `B` ikke omfatter alle attributter i tabellen.










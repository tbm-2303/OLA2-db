# Software Dev. OLA2-db 
# Timothy Busk Mortensen - cph-tm246@cphbusiness.dk 

## Kommentarer til ER design:

### Vi har brugt en Booking-tabel til at håndtere mange-til-mange-relationen mellem medlemmer og træningshold.

### Betaling håndterer medlemskabsbetalinger samt eventuelle rabatter.

### Instruktør er knyttet til træningshold som en 1:M-relation, da hver klasse har en instruktør, men en instruktør kan undervise flere hold.


# Relationer i Fitnesscenterets ER-model

- **Et medlem kan deltage i flere træningshold**  
  - *M:N-relation via Booking*  
  - Et medlem kan være tilmeldt flere træningshold, og et træningshold kan have flere medlemmer.

- **En instruktør kan være ansvarlig for flere træningshold**  
  - *1:M-relation*  
  - En instruktør kan undervise på flere hold, men hvert hold har kun én ansvarlig instruktør.

- **Et medlem kan foretage flere betalinger**  
  - *1:M-relation*  
  - Et medlem kan lave flere betalinger over tid, men hver betaling hører til ét medlem.
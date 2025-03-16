# Software Dev. OLA2-db 
# Timothy Busk Mortensen - cph-tm246@cphbusiness.dk 

## Kommentarer til design:

### Vi har brugt en Booking-tabel til at håndtere mange-til-mange-relationen mellem medlemmer og træningshold.

### Betaling håndterer medlemskabsbetalinger samt eventuelle rabatter.

### Instruktør er knyttet til træningshold som en 1:M-relation, da hver klasse har en instruktør, men en instruktør kan undervise flere hold.
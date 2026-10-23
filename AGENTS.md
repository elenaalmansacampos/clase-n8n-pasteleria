# Pastelería La Cuchara: automatizaciones con n8n

- La app (http://pasteleria:8000 dentro de Docker) guarda encargos y pagos. Se usa por API con la cabecera `X-API-Key` (credencial **API Pastelería** en n8n).
- n8n corre en Docker con zona horaria **Europe/Madrid**. Los correos salen por la credencial SMTP **Mailpit** (http://localhost:8025).
- Los flujos se guardan como JSON en `mis-flujos/` con el formato `NN-nombre.json` y se importan con *Import from file*.
- Los importes siempre los calcula la app: n8n solo los muestra (`toFixed(2)`), nunca los suma, reparte ni corrige por su cuenta.
- Las fechas de la API vienen como `yyyy-MM-dd`: compáralas así y calcula "mañana" con la zona Europe/Madrid (`$now`).
- La IA solo extrae datos de un texto desordenado. Las decisiones (a qué encargo va un pago, si se puede registrar) van en nodos **Code** con reglas claras; si hay cualquier duda, no se registra y se avisa a Marisa.
- Un flujo sin datos debe terminar sin enviar correos ni fallar.
- NO escribir credenciales ni claves dentro de los JSON. NO publicar flujos sin que Elena los haya probado.

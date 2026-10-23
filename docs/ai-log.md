# Bitácora de IA

## 2026-10-23 · Flujo 04 «Aviso de entrega mañana»

**Prompt que se le dio al agente** (resumen):

> Lee `AGENTS.md` y esta spec. Genera el flujo como un JSON importable en `mis-flujos/04-aviso-entrega.json`.

**Spec:**

```markdown
## Spec: aviso de entrega mañana
- Disparador: cada día a las 9:00.
- Pide a la API los encargos `pendiente` y `pagado` con `fecha_entrega` de mañana (GET /api/encargos).
- Si un encargo está `pagado`: correo al cliente "Mañana puedes recoger tu tarta".
- Si está `pendiente`: correo al cliente "Mañana es la entrega y aún te faltan X €".
- Si hay entregas mañana, un solo correo a Marisa con la lista. Si no hay ninguna, no se envía nada.
- Criterios de aceptación: con los datos de ejemplo, el resultado coincide con lo que dice la app; no falla si no hay entregas.
```

**Qué hizo bien el agente:**

- Resolvió todo con un solo nodo **Code** que prepara los correos y un único **Send Email**, en vez de llenar el flujo de IF: para cada encargo genera un item y añade otro para Marisa; si no hay entregas devuelve `[]` y no se envía nada.
- Tomó los importes de la app (`pendiente`) y solo los formateó con `toFixed(2)`; no calculó ni repartió dinero.
- Usó `$now` con la zona horaria de n8n para calcular «mañana» y comparó fechas en `yyyy-MM-dd`.
- Reutilizó las credenciales que ya funcionan (`Header Auth account` y `SMTP account`) y no escribió ninguna clave en el JSON.

**Qué hubo que corregir o decidir:**

- La spec pedía «pendiente y pagado»: se pidió `GET /api/encargos` sin filtro y el filtrado por estado/fecha se hace en el Code (la API no permite pedir dos estados a la vez).
- Se eliminó cualquier dependencia del *Error workflow* en el JSON para que sea importable en otra instancia; hay que asignárselo a mano (`00 Avisar de errores`) al publicarlo.
- Se comprobó que el nodo de correo no lleve la firma de n8n (`appendAttribution: false`).

**Pruebas realizadas:** se ejecutó el código del nodo `Preparar avisos` con los datos reales de la app:

| Caso | Resultado |
|---|---|
| Mañana 24/10 (Carmen, pendiente 12 €) | 2 correos: cliente + resumen a Marisa |
| Mañana 28/10 (Ana, pagado) | 2 correos: cliente («puedes recoger») + resumen a Marisa |
| Mañana 25/10 (sin entregas) | 0 correos, sin error |

**Pendiente en n8n:** asignar el flujo `00` como *Error workflow*, probarlo con **Execute workflow** y publicarlo.

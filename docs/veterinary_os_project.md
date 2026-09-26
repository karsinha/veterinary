# Veterinary OS

## Proyecto de aprendizaje + producto

> **Idea:** construir un sistema web propio para una veterinaria cuyo núcleo diferencial sea un **Automation Engine** con IA capaz de entender conversaciones, consultar datos reales del negocio, ejecutar acciones seguras y derivar a humanos cuando sea necesario.
>
> La primera vertical será veterinaria. El objetivo no es solamente hacer un CRUD de clientes y mascotas: el proyecto debe convertirse progresivamente en un sistema completo de gestión + automatización + datos + IA.

---

# 1. Resumen ejecutivo

## Producto

**Veterinary OS** será una aplicación web multi-tenant para veterinarias.

Tendrá dos caras principales:

### Cara del negocio

- Dashboard
- Agenda
- Clientes
- Mascotas
- Historia clínica
- Vacunas
- Tratamientos
- Seguimientos
- Inventario
- Servicios y precios
- Pagos/ventas básicos
- AI Inbox
- Automatizaciones
- Analytics
- Usuarios y permisos
- Auditoría
- Configuración

### Cara del cliente

La experiencia pública será mucho más simple:

- Web de la veterinaria
- Información básica
- Servicios
- Profesionales
- Horarios
- Ubicación
- Reserva de turno
- WhatsApp

La mayor parte de la interacción conversacional debería poder ocurrir por **WhatsApp** o por un chat web integrado, sin obligar al cliente a instalar una app.

---

# 2. Problema que queremos resolver

Una veterinaria puede recibir una cantidad considerable de mensajes preguntando:

- precios;
- horarios;
- disponibilidad;
- turnos;
- vacunas;
- servicios;
- ubicación;
- cancelaciones;
- cambios de turno;
- seguimiento de tratamientos;
- etc.

El problema no es simplemente que el chatbot sea malo.

El problema es que frecuentemente el proceso es:

```text
cliente
  ↓
WhatsApp
  ↓
espera
  ↓
persona revisa mensaje
  ↓
busca información
  ↓
responde
  ↓
cliente vuelve a responder
  ↓
persona vuelve al sistema
  ↓
se crea el turno
```

El objetivo es:

```text
cliente
  ↓
WhatsApp / Web
  ↓
AI Orchestrator
  ↓
consulta datos
  ↓
ejecuta workflow
  ↓
acción real
  ↓
confirmación
```

Ejemplo:

> Cliente: “Hola, ¿tenés un turno mañana para Luna?”

El sistema debería poder:

1. identificar al cliente;
2. identificar a Luna;
3. consultar la agenda;
4. ofrecer horarios válidos;
5. recibir la elección;
6. reservar;
7. registrar el turno;
8. enviar la confirmación;
9. dejar un evento auditable.

**El producto no vende respuestas. Vende resolución y automatización.**

---

# 3. Hipótesis principal

> Una veterinaria puede ahorrar tiempo y recuperar oportunidades comerciales si las consultas repetitivas y operativas se resuelven automáticamente y las conversaciones complejas se derivan a una persona con todo el contexto ya preparado.

## Hipótesis secundarias

- Muchas consultas no requieren intervención humana.
- Una buena integración con los datos del negocio es más valiosa que un chatbot genérico.
- La velocidad de respuesta puede influir en la conversión de consultas a turnos.
- Los recordatorios y seguimientos pueden generar trabajo/ventas recurrentes.
- Una interfaz conversacional funciona mejor cuando el sistema puede ejecutar operaciones reales.
- El valor de la IA aumenta cuando existe un buen modelo de datos detrás.

Estas hipótesis deben validarse con veterinarias reales.

---

# 4. Qué NO asumir todavía

No asumir que:

- todas las veterinarias usan WhatsApp como canal principal;
- todas sufren exactamente el mismo cuello de botella;
- todas tienen el mismo software;
- todas quieren reemplazar su sistema actual;
- todos los dueños quieren usar un portal web;
- todas las consultas pueden automatizarse;
- la IA debería responder preguntas clínicas;
- un modelo más grande siempre produce mejores resultados.

El proyecto deberá validar estas cosas antes de convertirlas en requisitos.

---

# 5. Propuesta de valor

La frase interna del producto debería ser:

> **“No solamente respondemos clientes: automatizamos operaciones de la veterinaria.”**

Ejemplos de operaciones:

- reservar turnos;
- cancelar turnos;
- mover turnos;
- consultar disponibilidad;
- enviar recordatorios;
- recuperar cancelaciones;
- gestionar listas de espera;
- registrar clientes;
- asociar mascotas;
- hacer seguimiento de tratamientos;
- recordar vacunas;
- resumir conversaciones;
- escalar a humanos;
- generar métricas;
- detectar cuellos de botella.

---

# 6. Principio arquitectónico central

La IA NO debería ser la fuente de verdad.

Usar esta separación:

```text
                 AI
                  |
         interpreta / coordina
                  |
                  v
              Tools
                  |
        validaciones de backend
                  |
                  v
             PostgreSQL
                  |
             source of truth
```

## Responsabilidades

### LLM / IA

- interpretar lenguaje natural;
- clasificar intención;
- extraer parámetros;
- elegir herramientas;
- resumir información;
- redactar respuestas;
- mantener contexto conversacional.

### Backend

- autenticación;
- autorización;
- validación;
- lógica de negocio;
- transacciones;
- permisos;
- reglas críticas;
- acceso a datos;
- auditoría.

### Base de datos

- persistencia;
- relaciones;
- integridad;
- historial estructurado;
- eventos persistentes.

---

# 7. Arquitectura conceptual final

```text
                         CLIENTE
                            |
                +-----------+-----------+
                |                       |
             WhatsApp                  Web
                |                       |
                +-----------+-----------+
                            |
                            v
                    Channel Adapter
                            |
                            v
                    Conversation API
                            |
                            v
                    AI Orchestrator
                            |
             +--------------+---------------+
             |              |               |
          Context         Tools          Policies
             |              |               |
             +--------------+---------------+
                            |
                            v
                    Business Services
                            |
          +----------------+----------------+
          |                |                |
        Agenda          Patients         Billing
          |                |                |
          +----------------+----------------+
                            |
                            v
                       PostgreSQL
                            |
                       Event Log
                            |
             +--------------+--------------+
             |                             |
        Analytics                        ML futuro
```

---

# 8. Módulos del producto

## 8.1 Dashboard

Debe responder:

> “¿Qué está pasando en el negocio ahora?”

### KPIs iniciales

- turnos del día;
- turnos confirmados;
- cancelaciones;
- nuevos clientes;
- conversaciones pendientes;
- tareas pendientes;
- ingresos básicos;
- vacunas próximas;
- seguimientos activos.

### KPIs de IA

- conversaciones recibidas;
- porcentaje resuelto automáticamente;
- porcentaje derivado;
- tiempo medio de respuesta;
- tiempo medio hasta resolución;
- turnos creados por IA;
- cancelaciones recuperadas;
- errores/intervenciones.

---

## 8.2 Agenda

Debe manejar:

- profesionales;
- horarios;
- días de atención;
- servicios;
- duración por servicio;
- bloqueos;
- vacaciones;
- turnos;
- cancelaciones;
- reprogramaciones.

### Modelo conceptual

```text
Professional
   |
   +---- Availability
   |
   +---- Appointment
            |
            +---- Customer
            +---- Pet
            +---- Service
```

---

## 8.3 Clientes

Entidad humana que puede tener varias mascotas.

```text
Customer
  |
  +---- Pet
  +---- Pet
  +---- Appointment
  +---- Conversation
  +---- Payment
```

Datos posibles:

- nombre;
- teléfono;
- email;
- dirección opcional;
- consentimiento relevante;
- notas administrativas;
- fecha de alta;
- estado;
- preferencias de contacto.

---

## 8.4 Mascotas

La mascota es una entidad propia.

Datos base:

- nombre;
- especie;
- raza;
- sexo;
- fecha de nacimiento aproximada/exacta;
- peso;
- color/señas;
- microchip si corresponde;
- propietario;
- estado.

Relacionada con:

- historia;
- consultas;
- vacunas;
- tratamientos;
- medicamentos;
- documentos;
- estudios;
- turnos.

---

# 9. Historia clínica

Este módulo es sensible y requiere especial cuidado.

Debe permitir registrar eventos clínicos de manera estructurada.

Ejemplo:

```text
ClinicalEncounter
-----------------
pet_id
professional_id
date
reason
observations
assessment
plan
attachments
created_at
updated_at
```

No todos los usuarios deberían poder editar todo.

Debe existir:

- permisos;
- historial de cambios;
- audit log;
- trazabilidad.

## IA y salud

La IA no será un veterinario autónomo.

Puede:

- recopilar información;
- resumir conversaciones;
- recordar antecedentes;
- detectar que una consulta necesita atención profesional;
- derivar;
- utilizar reglas aprobadas por la clínica.

No deberá inventar diagnósticos ni prescripciones.

---

# 10. Vacunas

Modelo conceptual:

```text
Vaccination
  |
  +---- Pet
  +---- VaccineType
  +---- administered_by
  +---- administered_at
  +---- next_due_at
```

Automatización posible:

```text
next_due_at approaching
        ↓
workflow
        ↓
WhatsApp reminder
        ↓
customer replies
        ↓
show available slots
        ↓
appointment
```

---

# 11. Tratamientos y seguimiento

Una consulta puede generar un tratamiento.

```text
Treatment
  |
  +---- Pet
  +---- Encounter
  +---- Medication / Instruction
  +---- Start date
  +---- End date
  +---- Follow-up date
```

Ejemplo de workflow:

```text
tratamiento creado
        ↓
programar follow-up
        ↓
recordatorio
        ↓
cliente responde
        ↓
guardar respuesta
        ↓
notificar profesional si corresponde
```

---

# 12. Inventario

Manejar inicialmente:

- productos;
- categorías;
- stock;
- stock mínimo;
- proveedores;
- costo;
- precio;
- lotes si son relevantes;
- vencimientos cuando corresponda.

No intentar construir un ERP completo de entrada.

Primera versión:

```text
Product
StockMovement
Supplier
```

---

# 13. Servicios y precios

Ejemplos:

- consulta;
- vacunación;
- desparasitación;
- control;
- peluquería si la clínica ofrece;
- estudios;
- otros servicios.

Cada servicio debería poder tener:

- nombre;
- duración;
- precio;
- profesional permitido;
- si requiere equipamiento/espacio;
- descripción.

Esto es importante para que la IA pueda ofrecer turnos válidos.

---

# 14. AI Inbox

Uno de los módulos centrales.

Vista conceptual:

```text
+----------------------------------------------------+
| AI INBOX                                           |
+---------------------+------------------------------+
| Conversaciones      | Conversación actual          |
|                     |                              |
| Juan                | Cliente: Hola               |
| quiero turno        |                              |
|                     | AI: Claro...                 |
| María               |                              |
| vacuna              | [tomar control]             |
|                     |                              |
| ⚠ requiere humano   |                              |
+---------------------+------------------------------+
```

Funciones:

- recibir mensajes;
- identificar conversación;
- recuperar contexto;
- clasificar intención;
- usar herramientas;
- responder;
- escalar a humano;
- guardar transcript;
- crear eventos.

---

# 15. AI Orchestrator

La pieza más interesante del proyecto.

Entrada:

```text
message
customer context
pet context
business context
conversation history
```

Proceso:

```text
1. entender intención
2. recuperar contexto
3. identificar datos faltantes
4. decidir si puede resolver
5. seleccionar tools
6. ejecutar tools mediante backend
7. obtener resultado
8. generar respuesta
9. registrar outcome
```

---

# 16. Tools de IA

La IA no debería tener acceso directo ilimitado a SQL.

Definir tools explícitas.

Ejemplos:

```text
get_customer()
get_pet()
get_pet_history()
get_available_slots()
get_service_price()
create_appointment()
cancel_appointment()
reschedule_appointment()
create_customer()
create_pet()
add_to_waitlist()
get_opening_hours()
send_confirmation()
create_followup()
```

Cada tool debe tener:

- input schema;
- validación;
- autorización;
- lógica de negocio;
- output estructurado;
- logs.

---

# 17. Workflow Engine

El sistema debe permitir automatizaciones deterministas.

Ejemplo:

```text
EVENTO:
vaccine_due_soon
        |
        v
CONDICIÓN:
customer_opted_in
        |
        v
ACCIÓN:
send_whatsapp_message
        |
        v
ESPERAR:
3 days
        |
        v
SI no responde
        |
        v
crear reminder
```

Otros workflows:

- confirmación de turnos;
- recordatorio de turnos;
- cancelación;
- recuperación de slot;
- lista de espera;
- seguimiento;
- vacuna próxima;
- cliente inactivo;
- stock bajo.

---

# 18. Human handoff

Regla fundamental:

> **Cuando la IA no debe o no puede resolver, tiene que pasar el contexto al humano.**

No:

```text
“Un asesor se comunicará con usted.”
```

Sí:

```text
AI detecta excepción
      ↓
abre/escalona conversación
      ↓
humano ve resumen
      ↓
humano toma control
```

El profesional debería ver algo como:

```text
RESUMEN DE IA

Mascota: Luna
Motivo informado: ...
Última consulta: ...
Turnos anteriores: ...
La consulta parece requerir intervención profesional.
```

---

# 19. Seguridad y permisos

Aprender y aplicar:

- autenticación;
- autorización;
- RBAC;
- sesiones/tokens;
- hashing de contraseñas;
- gestión de secretos;
- validación de input;
- rate limiting;
- audit logs;
- control de acceso por tenant;
- backups;
- cifrado cuando corresponda.

Roles iniciales:

```text
Owner/Admin
Veterinarian
Receptionist
Assistant
```

Nunca confiar en el frontend para autorización.

El backend debe validar permisos en cada operación sensible.

---

# 20. Multi-tenancy

Desde el principio pensar que:

```text
Veterinaria A
  |
  +---- users
  +---- customers
  +---- pets
  +---- appointments

Veterinaria B
  |
  +---- users
  +---- customers
  +---- pets
  +---- appointments
```

La implementación inicial puede usar una sola base de datos con `tenant_id`.

Cada registro perteneciente a un negocio debe poder distinguirse por tenant.

Objetivo crítico:

> Un usuario de la veterinaria A nunca debe poder acceder a datos de B.

---

# 21. Modelo de datos inicial

Entidades principales:

```text
Tenant
User
Role
Customer
Pet
Professional
Service
Availability
Appointment
Conversation
Message
ClinicalEncounter
Vaccination
Treatment
Medication
Product
Supplier
StockMovement
Payment
Workflow
WorkflowRun
Notification
AuditLog
Event
```

No crear todas desde el primer día.

Orden recomendado:

```text
Tenant
User
Customer
Pet
Professional
Service
Availability
Appointment
```

Después:

```text
Conversation
Message
Event
Workflow
```

Después:

```text
ClinicalEncounter
Vaccination
Treatment
Product
StockMovement
Payment
```

---

# 22. Relaciones principales

```text
Tenant
 |
 +--- Users
 |
 +--- Customers
 |      |
 |      +--- Pets
 |             |
 |             +--- Appointments
 |             +--- ClinicalEncounters
 |             +--- Vaccinations
 |             +--- Treatments
 |
 +--- Professionals
 |
 +--- Services
 |      |
 |      +--- Appointments
 |
 +--- Conversations
 |      |
 |      +--- Messages
 |
 +--- Workflows
 |
 +--- Products
 |
 +--- Suppliers
 |
 +--- Events
 |
 +--- AuditLogs
```

---

# 23. Stack de aprendizaje

## Backend

Aprender primero:

1. Python
2. HTTP
3. REST
4. FastAPI
5. Pydantic
6. SQL
7. PostgreSQL
8. SQLAlchemy
9. Alembic
10. Testing

## Frontend

Empezar con:

- HTML
- CSS
- JavaScript
- templating

Después evaluar React.

No empezar con React porque “es obligatorio”.

Usarlo cuando realmente ayude a manejar una interfaz compleja.

## Infraestructura

- Git
- GitHub
- Linux
- Docker
- Docker Compose
- environment variables
- logging
- deployment

## AI

Orden recomendado:

1. API de LLM
2. structured outputs
3. prompt design
4. tool calling
5. context management
6. evaluation
7. RAG
8. embeddings
9. observabilidad de IA
10. ML clásico

---

# 24. Roadmap ladrillo por ladrillo

## BLOQUE 0 — Python y fundamentos

Aprender:

- variables;
- tipos;
- condicionales;
- loops;
- funciones;
- listas/dicts/sets;
- clases básicas;
- módulos;
- excepciones;
- typing;
- archivos;
- JSON;
- virtual environments;
- paquetes.

Mini proyectos:

- agenda en memoria;
- CRUD de mascotas en JSON;
- calculadora de turnos;
- lector de datos CSV.

**Objetivo:** poder escribir pequeñas aplicaciones sin copiar código ciegamente.

---

## BLOQUE 1 — Git

Aprender:

- repository;
- commit;
- branch;
- merge;
- rebase básico;
- pull request;
- `.gitignore`;
- README.

Crear repositorio:

```text
veterinary-os/
```

Commit frecuente y descriptivo.

---

## BLOQUE 2 — HTTP y Web

Entender:

```text
Browser
   |
HTTP request
   |
Server
   |
HTTP response
```

Aprender:

- GET;
- POST;
- PUT/PATCH;
- DELETE;
- headers;
- status codes;
- JSON;
- cookies;
- sessions;
- APIs;
- webhooks.

Hacer primero una API extremadamente pequeña.

---

## BLOQUE 3 — FastAPI

Crear:

```text
GET /health
GET /customers
POST /customers
GET /customers/{id}
PATCH /customers/{id}
DELETE /customers/{id}
```

Aprender:

- routing;
- request models;
- response models;
- validation;
- dependency injection;
- error handling;
- OpenAPI.

---

## BLOQUE 4 — SQL

No saltar directamente al ORM.

Aprender SQL primero.

Conceptos:

- SELECT;
- WHERE;
- ORDER BY;
- JOIN;
- GROUP BY;
- INSERT;
- UPDATE;
- DELETE;
- constraints;
- indexes;
- transactions;
- foreign keys.

Ejercicio:

Construir en PostgreSQL:

```text
customers
pets
appointments
```

con relaciones reales.

---

## BLOQUE 5 — PostgreSQL + SQLAlchemy

Aprender:

- engine;
- session;
- models;
- relationships;
- migrations;
- transactions.

Usar Alembic para migraciones.

Nunca depender de modificar tablas manualmente en producción.

---

## BLOQUE 6 — Primer Veterinary OS

Implementar:

```text
Login
Tenants
Customers
Pets
Professionals
Services
Appointments
```

Todavía sin IA.

Objetivo:

> Tener un sistema de gestión funcional antes de introducir inteligencia artificial.

---

## BLOQUE 7 — Frontend administrativo

Crear dashboard para:

- agenda;
- cliente;
- mascota;
- turno;
- servicios.

Prioridad:

**usabilidad > estética extrema**.

Debe ser rápido para una recepción que trabaja todo el día.

---

## BLOQUE 8 — Authentication + RBAC

Implementar:

- login;
- password hashing;
- sesión/token;
- roles;
- permissions;
- tenant isolation.

Casos a probar:

- usuario A intenta acceder a tenant B;
- receptionist intenta modificar historia clínica;
- veterinarian intenta acceder a datos no permitidos;
- usuario desactivado intenta ingresar.

---

## BLOQUE 9 — AI mínima

Primera integración con LLM.

NO hacer chatbot todavía.

Primero aprender:

- prompts;
- system instructions;
- structured output;
- JSON schemas;
- errores;
- retries;
- timeouts.

Crear una función:

```text
classify_message(message) -> Intent
```

Ejemplo:

```json
{
  "intent": "appointment_request",
  "confidence": 0.96
}
```

---

## BLOQUE 10 — Tool calling

Agregar tools:

```text
get_customer
get_pet
get_available_slots
get_service_price
```

Después:

```text
create_appointment
cancel_appointment
reschedule_appointment
```

La IA no ejecuta SQL directamente.

---

## BLOQUE 11 — AI Inbox

Crear:

- conversaciones;
- mensajes;
- estados;
- intervención humana;
- asignación;
- historial.

Estados posibles:

```text
open
ai_handling
human_handling
resolved
escalated
```

---

## BLOQUE 12 — Primer workflow end-to-end

Implementar exactamente este caso:

```text
Cliente:
“Hola, quiero turno para Luna.”

AI
 ↓
identify customer
 ↓
identify pet
 ↓
get available slots
 ↓
offer slots

Cliente:
“17:30”

AI
 ↓
create appointment
 ↓
send confirmation
```

Este es el primer milestone grande.

---

## BLOQUE 13 — Webhooks / WhatsApp

Cuando el MVP local funcione:

- estudiar webhooks;
- recepción de mensajes;
- envío de respuestas;
- idempotencia;
- retries;
- manejo de errores;
- seguridad de webhooks.

No acoplar todo el sistema a WhatsApp.

Crear una abstracción:

```text
ChannelAdapter
  |
  +--- WebChatAdapter
  +--- WhatsAppAdapter
```

---

## BLOQUE 14 — Automatizaciones

Implementar workflows:

### Confirmación

```text
appointment_created
→ confirmation_message
```

### Recordatorio

```text
appointment_tomorrow
→ reminder
```

### Cancelación

```text
appointment_cancelled
→ find_waitlist
→ offer_slot
```

### Vacuna

```text
vaccine_due_soon
→ send_reminder
```

### Seguimiento

```text
treatment_end
→ followup_message
```

---

## BLOQUE 15 — Event system

Empezar a registrar eventos de negocio:

```text
customer_created
pet_created
appointment_created
appointment_cancelled
conversation_started
message_received
ai_response_sent
human_handoff
payment_created
vaccine_due
followup_created
```

Esto será la base futura de analytics y ML.

---

## BLOQUE 16 — Analytics

Primero SQL + agregaciones.

Dashboards:

### Operación

- turnos por día;
- ocupación;
- cancelaciones;
- no-show;
- duración media.

### Cliente

- nuevos;
- recurrentes;
- inactivos;
- mascotas por cliente.

### AI

- mensajes;
- tiempo de respuesta;
- resolución automática;
- derivaciones;
- turnos originados por IA.

### Conversión

```text
conversations
→ qualified
→ appointment
→ attended
→ completed
```

---

## BLOQUE 17 — Observabilidad

Aprender:

- logs estructurados;
- request IDs;
- correlation IDs;
- métricas;
- errores;
- latencias;
- AI latency;
- tool failures.

Un mensaje debería poder seguirse:

```text
message_id
  ↓
conversation_id
  ↓
ai_run_id
  ↓
tool_call_id
  ↓
event_id
```

Esto será importantísimo para depurar IA.

---

## BLOQUE 18 — Evaluation de IA

No confiar en “parece que funciona”.

Crear un dataset de pruebas:

```text
input
expected_intent
expected_tool
expected_behavior
```

Ejemplos:

- pedir turno;
- cancelar;
- preguntar precio;
- pedir ubicación;
- pedir información médica sensible;
- mensaje ambiguo;
- usuario hostil;
- cliente no identificado.

Medir:

- intent accuracy;
- tool accuracy;
- hallucination rate;
- escalation correctness;
- latency.

---

## BLOQUE 19 — Seguridad de IA

Investigar:

- prompt injection;
- indirect prompt injection;
- tool abuse;
- data leakage;
- excessive permissions;
- unsafe outputs;
- secret exposure.

Regla:

> El modelo nunca debe poder hacer algo que el usuario no podría hacer mediante los permisos normales del sistema.

---

## BLOQUE 20 — RAG / conocimiento de la clínica

Más adelante podría existir una base de conocimiento:

- horarios;
- políticas;
- servicios;
- precios;
- instrucciones administrativas;
- documentos internos.

RAG puede ayudar a responder preguntas específicas del negocio.

Pero siempre preferir datos estructurados cuando la información es estructurada.

Ejemplo:

Precio:

```text
NO → RAG
SÍ → database
```

Política extensa:

```text
Puede tener sentido → RAG
```

---

## BLOQUE 21 — Data pipeline

Empezar a pensar como data engineer.

```text
Application events
       ↓
Event storage
       ↓
Cleaning
       ↓
Aggregation
       ↓
Analytics tables
       ↓
ML dataset
```

Aprender:

- ETL/ELT;
- data cleaning;
- feature engineering;
- reproducibilidad.

---

## BLOQUE 22 — ML

No entrenar un modelo por entrenar.

Buscar primero problemas donde los datos realmente aporten.

### Posibles modelos

#### No-show prediction

Predicción de probabilidad de ausencia.

Features posibles:

- historial de asistencia;
- hora;
- día;
- tipo de turno;
- tiempo desde reserva;
- reminders recibidos.

#### Churn / reactivación

Identificar clientes que podrían estar inactivos.

#### Demanda

Predecir:

- horas de mayor demanda;
- tipos de servicios;
- cantidad de turnos.

#### Lead conversion

Predecir probabilidad de que una conversación termine en turno.

---

# 25. Roadmap de producto

## V0 — Gestión básica

```text
Customers
Pets
Professionals
Services
Appointments
```

## V1 — AI básica

```text
Intent classification
Structured outputs
Tool calling
```

## V2 — AI Inbox

```text
Conversations
Human handoff
Context
```

## V3 — Automatización

```text
Reminders
Confirmations
Waitlist
Followups
```

## V4 — WhatsApp

```text
Webhooks
Inbound
Outbound
```

## V5 — Analytics

```text
Events
Dashboards
Funnels
AI metrics
```

## V6 — Seguridad/enterprise readiness

```text
RBAC
Audit
Backups
Observability
```

## V7 — AI avanzada

```text
RAG
Knowledge base
Summarization
Better context
```

## V8 — ML

```text
No-show
Demand
Churn
Conversion
```

---

# 26. Cliente: qué debería ver

No construir un portal gigante inicialmente.

Web pública mínima:

```text
Veterinaria

[Reservar turno]

Servicios
Profesionales
Horarios
Ubicación
Contacto

[WhatsApp]
```

El cliente debería poder completar el proceso por WhatsApp.

Posteriormente se puede agregar un portal:

```text
Mi cuenta
  |
  +--- Mascotas
  +--- Próximos turnos
  +--- Historial
  +--- Vacunas
  +--- Documentos
  +--- Facturas
```

Pero no es prioridad inicial.

---

# 27. Admin View: debe ser excelente

El panel interno probablemente sea el corazón del producto.

Debe priorizar:

1. velocidad;
2. claridad;
3. pocas acciones para completar tareas;
4. búsqueda;
5. contexto;
6. accesos rápidos;
7. automatización.

Diseñar pensando en alguien que está trabajando 8 horas en recepción.

---

# 28. UX del AI Inbox

Debe evitar convertirse en una caja negra.

Mostrar:

- qué entendió la IA;
- qué herramienta utilizó;
- qué acción ejecutó;
- resultado;
- si necesita revisión.

No necesariamente mostrar razonamiento interno del modelo.

Mostrar **estado operativo**, no chain-of-thought.

Ejemplo:

```text
AI action

Intent: appointment_request
Tool: get_available_slots
Result: 3 slots found
Action: appointment_created
Status: success
```

---

# 29. Arquitectura de código sugerida

Una posible estructura inicial:

```text
veterinary-os/
│
├── app/
│   ├── main.py
│   ├── config.py
│   │
│   ├── api/
│   │   ├── auth.py
│   │   ├── customers.py
│   │   ├── pets.py
│   │   ├── appointments.py
│   │   ├── conversations.py
│   │   └── analytics.py
│   │
│   ├── models/
│   ├── schemas/
│   ├── services/
│   ├── repositories/
│   ├── workflows/
│   ├── ai/
│   │   ├── orchestrator.py
│   │   ├── prompts.py
│   │   ├── tools.py
│   │   └── evaluation.py
│   │
│   ├── security/
│   └── events/
│
├── tests/
├── migrations/
├── frontend/
├── scripts/
├── docs/
├── docker-compose.yml
├── .env.example
├── pyproject.toml
└── README.md
```

Esta estructura puede cambiar. La arquitectura debe evolucionar con las necesidades reales.

---

# 30. Testing

No esperar hasta el final.

## Unit tests

Testear:

- reglas;
- validaciones;
- funciones;
- servicios.

## Integration tests

Testear:

- API + DB;
- workflows;
- permisos;
- tools.

## End-to-end

Test importante:

```text
message
→ AI
→ tool
→ DB
→ response
```

## AI evals

Mantener un conjunto fijo de conversaciones de prueba.

Cada cambio del prompt/modelo debería poder compararse con el anterior.

---

# 31. Casos de prueba críticos

## Caso 1

Cliente conocido + mascota conocida + turno libre.

Resultado: reserva automática.

## Caso 2

Cliente nuevo + mascota nueva.

Resultado: crear registros y reservar.

## Caso 3

Horario ocupado.

Resultado: ofrecer alternativas.

## Caso 4

Cancelación.

Resultado: cancelar + liberar + iniciar recuperación.

## Caso 5

Pregunta médica sensible.

Resultado: no diagnosticar automáticamente; recopilar/derivar según políticas.

## Caso 6

Usuario intenta acceder a otro tenant.

Resultado: 403/denegación.

## Caso 7

Tool falla.

Resultado: no inventar resultado; informar y/o escalar.

## Caso 8

WhatsApp reenvía webhook.

Resultado: idempotencia; no duplicar turno ni mensaje.

---

# 32. Data model vs AI model

Una distinción que hay que aprender muy bien:

## Datos estructurados

Usar database.

Ejemplo:

```text
fecha de turno
precio
nombre
stock
horario
```

## Información documental

Puede usar RAG/document retrieval.

Ejemplo:

```text
política de cancelaciones
manual interno
texto extenso
```

## Lenguaje natural

Usar LLM.

Ejemplo:

```text
“Quería saber si mañana a la tarde hay algún turno para Luna”
```

No usar IA para resolver lo que una query SQL resuelve mejor.

---

# 33. Métricas del producto

## North Star inicial

Una posible métrica:

> **Porcentaje de solicitudes operativas resueltas sin intervención humana.**

Pero acompañarla con métricas de calidad.

No queremos maximizar automatización a cualquier costo.

## Métricas

### Velocidad

- first response time;
- time to resolution.

### Resolución

- automated resolution rate;
- human handoff rate.

### Negocio

- conversation → appointment;
- appointment → attendance;
- recovered cancellations.

### Calidad

- failed tool calls;
- incorrect actions;
- unsafe escalations;
- hallucination rate.

---

# 34. Principio de automatización

Automatizar primero tareas con:

- alta frecuencia;
- baja complejidad;
- reglas claras;
- bajo riesgo;
- alto costo humano.

Ejemplos:

```text
consultar horario       ✅
reservar turno          ✅
cancelar turno          ✅
recordar vacuna        ✅
reprogramar turno       ✅
ubicación               ✅
precio                  ✅ si está estructurado

Diagnóstico             ❌ autónomo
Prescripción            ❌ autónomo
Decisiones clínicas     ❌ autónomo
```

---

# 35. Integraciones

Pensar en adaptadores.

```text
ChannelAdapter
  ├── WebChat
  ├── WhatsApp
  ├── Email futuro
  └── otros
```

Y no meter lógica de negocio directamente en el módulo de WhatsApp.

Ejemplo correcto:

```text
WhatsApp webhook
      ↓
Channel layer
      ↓
Conversation service
      ↓
AI / workflow
```

No:

```text
WhatsApp webhook
      ↓
1500 líneas de negocio
```

---

# 36. Idempotencia

Tema importante para sistemas reales.

Si un webhook llega dos veces:

```text
message_id = 123
```

el sistema debe reconocer que ya procesó ese mensaje.

Lo mismo para operaciones de reserva y pagos.

Aprender:

- idempotency keys;
- unique constraints;
- retries;
- transaction boundaries.

---

# 37. Consistencia y concurrencia

Problema:

Dos personas/IA intentan reservar el mismo horario simultáneamente.

No basta con:

```text
if slot_free:
   book()
```

Hay que pensar en:

- transacciones;
- locking;
- constraints;
- race conditions.

Este es un excelente punto para aprender backend de verdad.

---

# 38. Observabilidad de AI

Guardar metadata de cada ejecución:

```text
ai_run
- model
- prompt_version
- latency
- tokens/usage
- intent
- tools_called
- success
- escalation
- error
```

Esto permitirá comparar:

```text
modelo A vs modelo B
prompt v1 vs v2
workflow v3 vs v4
```

sin depender de memoria subjetiva.

---

# 39. Versionado de prompts

Nunca editar prompts importantes sin versionado.

Ejemplo:

```text
appointment_agent_v1
appointment_agent_v2
appointment_agent_v3
```

Registrar qué versión produjo cada resultado.

---

# 40. Knowledge base de la veterinaria

Más adelante:

```text
BusinessKnowledge
  |
  +--- horarios
  +--- políticas
  +--- servicios
  +--- preguntas frecuentes
  +--- documentos
```

La IA puede consultarla cuando corresponda.

---

# 41. Métricas para demostrar el valor comercial

Cuando llegue la hora de probarlo con un negocio, medir antes/después:

```text
Tiempo de respuesta
Mensajes sin responder
Turnos generados
Cancelaciones
No-show
Horas del personal dedicadas a WhatsApp
Conversión de conversación a turno
```

La demo ideal no es:

> “Mirá qué inteligente es mi IA.”

Es:

> “Antes tardaban X. Ahora Y. Antes quedaban Z consultas sin resolver. Ahora quedan N.”

---

# 42. Estrategia de validación

Antes de construir demasiado:

## Paso 1

Hablar con 5–10 veterinarias.

## Paso 2

Documentar su flujo actual.

## Paso 3

Elegir un solo problema.

## Paso 4

Construir prototipo.

## Paso 5

Probar con datos ficticios.

## Paso 6

Pilotar con una veterinaria real si es posible.

## Paso 7

Medir resultado.

## Paso 8

Recién entonces agregar funciones.

---

# 43. Portfolio: qué debería poder mostrar el proyecto

Al finalizar una primera versión sólida, el README debería poder mostrar:

### Arquitectura

```text
Frontend
API
Database
AI
Workflow Engine
Event System
```

### Demo

```text
Cliente:
“Quiero turno para Luna mañana.”

Sistema:
consulta + reserva + confirmación
```

### Seguridad

Mostrar:

- RBAC;
- tenant isolation;
- audit logs;
- validación;
- tests.

### Data

Mostrar:

- dashboard;
- eventos;
- funnels;
- métricas.

### AI

Mostrar:

- tool calling;
- human handoff;
- evaluación;
- versionado.

Eso sería un portfolio técnico mucho más fuerte que una simple app CRUD.

---

# 44. Prompt para Tutor-IA

Usar el siguiente prompt como system prompt/base para otra IA que te acompañe durante el proyecto.

```text
Actuá como mi tutor técnico y arquitecto acompañante para mi proyecto “Veterinary OS”.

CONTEXTO
Estoy construyendo desde cero una aplicación web para gestionar y automatizar una veterinaria. Quiero utilizar el proyecto principalmente para aprender bien Python, backend, bases de datos, APIs, seguridad, arquitectura, automatización, IA y posteriormente data/ML.

El producto tendrá:
- gestión de clientes;
- mascotas;
- agenda;
- profesionales;
- servicios;
- historia clínica;
- vacunas;
- tratamientos;
- inventario;
- conversaciones;
- AI Inbox;
- tool calling;
- workflows;
- analytics;
- auditoría;
- eventualmente ML.

La idea central es que la IA no sea un simple chatbot. Debe poder interpretar solicitudes, consultar datos reales, utilizar herramientas controladas y ejecutar operaciones del negocio. Cuando una solicitud sea sensible o requiera criterio profesional, debe derivar a un humano.

OBJETIVO EDUCATIVO
No quiero simplemente terminar el software. Quiero entender por qué funciona.

REGLAS PARA ENSEÑARME

1. Priorizá aprendizaje sobre velocidad.

2. Antes de darme código grande, explicá qué problema resuelve y qué conceptos necesito entender.

3. Cuando exista una decisión arquitectónica, presentá las alternativas principales y explicá el trade-off.

4. No me entregues automáticamente una solución completa si el ejercicio puede dividirse en pasos que me hagan pensar.

5. Pedime que intente una parte antes de darme la solución, cuando sea razonable.

6. Si mi solución funciona pero es mala arquitectura, explicá por qué y cómo mejorarla.

7. Diferenciá siempre:
   - “esto funciona”
   - “esto está bien diseñado”
   - “esto escala”

8. Señalá deuda técnica cuando aparezca.

9. No introduzcas tecnologías innecesarias solo porque son populares.

10. No me hagas usar React, microservicios, Kubernetes, Redis, Kafka, etc. antes de que exista una razón concreta.

11. Ayudame a construir primero una versión sencilla y luego refactorizarla.

12. Quiero aprender SQL de verdad. No ocultes todo detrás del ORM.

13. Quiero comprender HTTP, APIs, autenticación, autorización, transacciones, concurrencia e idempotencia.

14. Para IA, explicá claramente la diferencia entre:
   - prompt;
   - structured output;
   - tool calling;
   - RAG;
   - embeddings;
   - workflow;
   - agent/orchestrator;
   - evaluación.

15. Nunca trates al LLM como fuente de verdad de datos.

16. La IA no debe tener acceso directo e ilimitado a SQL. Preferí tools explícitas y validadas.

17. Para temas sensibles de veterinaria, mantené una separación clara entre automatización administrativa y decisiones clínicas.

18. Cuando propongas una solución, indicá qué parte pertenece a:
   - frontend;
   - backend;
   - database;
   - AI;
   - workflow;
   - infraestructura.

19. Enseñame seguridad como parte del sistema, no como un bloque final.

20. Cuando haya bugs, no me des inmediatamente la respuesta. Primero ayudame a diagnosticarlo.

FORMATO DE RESPUESTA PREFERIDO

Cuando te pregunte cómo hacer algo:

1. Objetivo
2. Conceptos que necesito saber
3. Diseño simple
4. Mi tarea
5. Pistas
6. Solución solo si la necesito
7. Cómo verificar que funciona
8. Qué aprendí
9. Próximo paso

Cuando revise mi código:

- decime qué está bien;
- marcá errores;
- marcá riesgos de seguridad;
- marcá problemas de diseño;
- sugerí mejoras gradualmente;
- no reescribas todo sin explicarme primero por qué.

Cuando el proyecto se complique:

- reducí el problema;
- proponé el mínimo incremento funcional;
- mantené la arquitectura simple;
- explicá qué estamos sacrificando y por qué.

OBJETIVO FINAL
Quiero terminar con un proyecto funcional, pero especialmente quiero terminar entendiendo cómo construir sistemas backend reales con datos, automatización, IA y seguridad.
```

---

# 45. Prompt para que el Tutor no me deje copiar

Si querés un modo más estricto, añadir al prompt:

```text
MODO TUTOR ESTRICTO

No me des directamente la respuesta si el problema puede resolverse con razonamiento.

Primero haceme preguntas cortas para verificar que entiendo el problema.

Si te pido “dame el código”, preguntame primero:
1. qué creo que debería hacer;
2. qué datos entran;
3. qué salida espero;
4. qué errores podrían ocurrir.

Después de mi intento, recién ayudame con la implementación.

Si cometo un error conceptual, no lo tapes con código.
Primero corregí mi modelo mental.
```

---

# 46. Regla personal de aprendizaje

Para cada feature seguir:

```text
PROBLEMA
   ↓
MODELO MENTAL
   ↓
DISEÑO
   ↓
IMPLEMENTACIÓN
   ↓
TEST
   ↓
ERROR
   ↓
DEBUG
   ↓
REFACTOR
   ↓
DOCUMENTACIÓN
```

No:

```text
prompt
↓
copiar código
↓
funciona
↓
seguir
```

El segundo camino termina antes, pero enseña mucho menos.

---

# 47. Definition of Done

Una feature no se considera terminada solamente porque “funciona”.

Debe tener, cuando sea relevante:

- validación;
- manejo de errores;
- tests;
- permisos;
- logs;
- documentación;
- migración DB;
- UI utilizable;
- caso de borde considerado.

---

# 48. Orden recomendado para estudiar mientras construís

```text
Python
 ↓
Git
 ↓
HTTP
 ↓
REST
 ↓
FastAPI
 ↓
SQL
 ↓
PostgreSQL
 ↓
ORM
 ↓
Auth
 ↓
RBAC
 ↓
Testing
 ↓
Webhooks
 ↓
AI APIs
 ↓
Structured outputs
 ↓
Tool calling
 ↓
Workflows
 ↓
Events
 ↓
Analytics
 ↓
RAG
 ↓
Data engineering
 ↓
ML
 ↓
MLOps / AI observability
```

---

# 49. Qué aprender primero y qué dejar para después

## Ahora

- Python;
- Git;
- HTTP;
- FastAPI;
- SQL;
- PostgreSQL;
- HTML/CSS/JS básico.

## Después

- auth;
- RBAC;
- testing;
- Docker;
- webhooks;
- AI APIs;
- tool calling.

## Más adelante

- workflows;
- event architecture;
- observability;
- RAG;
- analytics avanzada.

## Mucho después

- ML;
- forecasting;
- no-show prediction;
- churn;
- modelos propios;
- optimización.

---

# 50. Primer milestone real

El primer gran objetivo del proyecto NO es tener un dashboard bonito.

Es conseguir que este escenario funcione:

```text
1. Existe una veterinaria.
2. Existe un cliente.
3. Existe una mascota.
4. Existe un profesional.
5. Existe una agenda.
6. Existe un servicio.
7. El cliente manda un mensaje.
8. La IA entiende que quiere un turno.
9. Consulta los horarios.
10. Propone opciones.
11. El cliente elige.
12. El backend valida.
13. Se crea el turno.
14. Se registra el evento.
15. Se confirma al cliente.
16. El turno aparece en el dashboard.
```

Cuando eso funcione de forma confiable, el proyecto deja de ser solamente una idea.

---

# 51. Segundo milestone

```text
Cancelación
 ↓
liberar slot
 ↓
consultar waitlist
 ↓
ofrecer turno
 ↓
cliente acepta
 ↓
rebook
```

Esto demuestra que ya existe un **workflow engine** real.

---

# 52. Tercer milestone

```text
Vacuna próxima
 ↓
automatic reminder
 ↓
cliente responde
 ↓
AI entiende
 ↓
consulta agenda
 ↓
crea turno
```

Acá el producto empieza a generar valor recurrente.

---

# 53. Cuarto milestone

Dashboard de AI:

```text
Mensajes
Respuesta media
Resolución automática
Derivaciones
Turnos creados por IA
Cancelaciones recuperadas
```

Ahora podemos demostrar valor con datos.

---

# 54. Quinto milestone

Primera función de ML:

> predecir no-show.

No importa que inicialmente el modelo sea simple.

Lo importante es aprender el ciclo:

```text
data
 ↓
cleaning
 ↓
features
 ↓
train
 ↓
evaluate
 ↓
predict
 ↓
log result
 ↓
measure business impact
```

---

# 55. Posible evolución futura

Una vez que la vertical veterinaria sea sólida:

```text
Veterinarias
    ↓
Automation Core
    ↓
Mecánicos
    ↓
Servicios técnicos
    ↓
Otros negocios verticales
```

El objetivo a largo plazo podría ser separar:

```text
CORE
├── Auth
├── Tenancy
├── Conversations
├── AI
├── Tools
├── Workflows
├── Events
├── Analytics
└── Notifications

VERTICAL
├── Veterinary
├── Automotive
├── Field Service
└── ...
```

Pero no construir ese framework antes de tener un vertical funcionando.

---

# 56. Principios de diseño que no hay que olvidar

1. **Construir para el problema, no para la tecnología.**
2. **La base de datos es la fuente de verdad.**
3. **El LLM no reemplaza las reglas de negocio.**
4. **Automatizar no significa eliminar al humano.**
5. **El human handoff debe conservar contexto.**
6. **Los errores deben ser visibles y recuperables.**
7. **Todo lo crítico debe ser auditable.**
8. **Los permisos se controlan en backend.**
9. **No introducir complejidad antes de necesitarla.**
10. **Cada feature debe enseñar algo.**
11. **Los datos son parte del producto.**
12. **La calidad de AI se mide, no se supone.**

---

# 57. Checklist de progreso

## Fundamentos

- [ ] Python
- [ ] Git
- [ ] HTTP
- [ ] SQL

## Backend

- [ ] FastAPI
- [ ] PostgreSQL
- [ ] SQLAlchemy
- [ ] Alembic
- [ ] Testing

## Seguridad

- [ ] Authentication
- [ ] RBAC
- [ ] Tenant isolation
- [ ] Audit logs
- [ ] Secrets

## Producto

- [ ] Customers
- [ ] Pets
- [ ] Professionals
- [ ] Services
- [ ] Appointments
- [ ] AI Inbox

## AI

- [ ] Structured output
- [ ] Tool calling
- [ ] Context
- [ ] Human handoff
- [ ] AI evaluation
- [ ] Prompt versioning

## Automation

- [ ] Reminder workflow
- [ ] Cancellation workflow
- [ ] Waitlist workflow
- [ ] Vaccine workflow
- [ ] Follow-up workflow

## Data

- [ ] Events
- [ ] Analytics
- [ ] Funnel
- [ ] AI metrics
- [ ] Dataset

## ML

- [ ] Feature engineering
- [ ] Train/test split
- [ ] Baseline
- [ ] Evaluation
- [ ] Prediction
- [ ] Business metric

---

# 58. Regla final del proyecto

No intentar hacer una startup completa desde el primer día.

Construir una pieza.

Entenderla.

Probarla.

Romperla.

Arreglarla.

Refactorizarla.

Documentarla.

Pasar a la siguiente.

La visión puede ser enorme:

> **un sistema operativo para negocios donde la IA no solamente conversa, sino que entiende el negocio y ejecuta operaciones.**

Pero el camino debe ser pequeño:

```text
Cliente
 ↓
Mascota
 ↓
Turno
 ↓
API
 ↓
Database
 ↓
AI
 ↓
Tool
 ↓
Workflow
 ↓
Analytics
 ↓
ML
```

Ese es el orden.

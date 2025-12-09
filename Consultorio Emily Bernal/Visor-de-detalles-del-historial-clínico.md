# Visor de detalles del historial clínico

> **Archivos fuente relevantes**
> * [Admin/ver_historia.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php)

## Propósito y alcance

El Visor de Detalles del Historial Clínico ( `ver_historia.php`) es una interfaz completa de solo lectura que muestra los historiales clínicos completos del paciente para una cita específica. Esta página reúne los datos demográficos del paciente, la anamnesis (historial médico), los detalles de la cita y los informes médicos en una vista unificada. Para editar informes médicos, consulte [el Editor de Informes Médicos](/axchisan/Consultorio_Emily_Bernal/2.3.3-medical-report-editor) . Para ver todos los historiales clínicos, consulte [la Vista de Lista del Historial Clínico](/axchisan/Consultorio_Emily_Bernal/2.3.1-clinical-history-list-view) . Para generar versiones en PDF, consulte [el Generador de PDF del Historial Clínico](/axchisan/Consultorio_Emily_Bernal/3.1-clinical-history-pdf-generator) .

**Fuentes:** [Admin/ver_historia.php L1-L302](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L1-L302)

---

## Flujo de solicitud y autenticación

La página sigue un estricto modelo de seguridad que valida tanto la existencia de la sesión como la autenticidad del token antes de mostrar cualquier dato del paciente.

### Secuencia de autenticación

```mermaid
sequenceDiagram
  participant Browser
  participant ver_historia.php
  participant Session
  participant validarToken
  participant Database
  participant historia_clinica.php

  Browser->>ver_historia.php: GET ?id_cita=X&id_paciente=Y
  ver_historia.php->>Session: session_start()
  ver_historia.php->>Session: Check $_SESSION['id_doctor']
  loop [Missing Parameters]
    ver_historia.php->>Session: Set error message
    ver_historia.php->>Browser: Redirect to ../index.php
    ver_historia.php->>validarToken: validarToken(link, id_doctor, 'Doctor', token)
    validarToken->>Database: Query session_tokens table
    validarToken-->>ver_historia.php: false
    ver_historia.php->>Session: session_unset(), session_destroy()
    ver_historia.php->>Browser: Redirect to ../index.php
    ver_historia.php->>ver_historia.php: Check GET parameters
    ver_historia.php->>Session: Set error message
    ver_historia.php->>historia_clinica.php: Redirect
    ver_historia.php->>Database: Fetch patient, appointment, report data
    ver_historia.php->>Browser: Render clinical history view
  end
```

**Fuentes:** [Admin/ver_historia.php L2-L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L2-L43)

---

## Arquitectura de agregación de datos

La página consolida datos de múltiples tablas de bases de datos y fuentes del sistema de archivos para crear una vista completa del historial clínico.

### Flujo de recopilación de datos

```mermaid
flowchart TD

ver_historia["ver_historia.php"]
id_cita["$_GET['id_cita']"]
id_paciente["$_GET['id_paciente']"]
consultarPaciente["consultarPaciente(link, id_paciente)"]
consultarDoctor["consultarDoctor(link, id_doctor)"]
query_citas["Prepared Statement: citas JOIN consultas JOIN doctor"]
query_informe["Prepared Statement: informe_medico"]
paciente_table["paciente table"]
citas_table["citas table"]
consultas_table["consultas table"]
doctor_table["doctor table"]
informe_table["informe_medico table"]
age_calc["Age Calculation: DateTime::diff"]
radiografias["../uploads/radiografias/"]
fotos_boca["../uploads/fotos_boca/"]

id_cita --> ver_historia
id_paciente --> ver_historia
ver_historia --> consultarPaciente
ver_historia --> consultarDoctor
ver_historia --> query_citas
ver_historia --> query_informe
consultarPaciente --> paciente_table
query_citas --> citas_table
query_citas --> consultas_table
query_citas --> doctor_table
query_informe --> informe_table
consultarDoctor --> doctor_table
paciente_table --> age_calc
informe_table --> radiografias
informe_table --> fotos_boca

subgraph subGraph4 ["File System"]
    radiografias
    fotos_boca
end

subgraph subGraph3 ["Computed Data"]
    age_calc
end

subgraph subGraph2 ["Database Tables"]
    paciente_table
    citas_table
    consultas_table
    doctor_table
    informe_table
end

subgraph subGraph1 ["Data Functions"]
    consultarPaciente
    consultarDoctor
    query_citas
    query_informe
end

subgraph subGraph0 ["Input Parameters"]
    id_cita
    id_paciente
end
```

**Fuentes:** [Admin/ver_historia.php L25-L78](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L25-L78)

---

## Implementación de consultas de base de datos

### Recuperación de datos de pacientes

La página utiliza la función de abstracción `consultarPaciente()`de la biblioteca de consultas para recuperar información completa del paciente.

```
// Function call
$patient = consultarPaciente($link, $id_paciente);
```

**Fuentes:** [Admin/ver_historia.php L37](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L37-L37)

### Recuperación de datos de citas

Una declaración preparada con múltiples JOIN recupera los detalles de la cita junto con el tipo de consulta y el nombre del médico:

| Fuente de la columna | Mesa | Objetivo |
| --- | --- | --- |
| `c.*` | `citas` | Todos los campos de cita |
| `con.tipo` | `consultas` | Descripción del tipo de consulta |
| `d.nombreD` | `doctor` | Nombre del doctor |

**Estructura de la consulta:**

```sql
SELECT c.*, con.tipo, d.nombreD 
FROM citas c 
LEFT JOIN consultas con ON con.id_consultas = c.id_consultas 
LEFT JOIN doctor d ON d.id_doctor = c.id_doctor 
WHERE c.id_cita = ? AND c.id_paciente = ?
```

**Fuentes:** [Admin/ver_historia.php L51-L66](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L51-L66)

### Recuperación de informes médicos

A separate prepared statement fetches the associated medical report if it exists:

```sql
SELECT * FROM informe_medico WHERE id_cita = ?
```

The result is handled with a ternary operator to return an empty array if no report exists.

**Sources:** [Admin/ver_historia.php L69-L75](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L69-L75)

---

## Age Calculation Logic

The system calculates patient age dynamically using PHP's `DateTime` class:

```
$birthDate = new DateTime($patient['fecha_nacimiento']);
$currentDate = new DateTime();
$age = $currentDate->diff($birthDate)->y;
```

This computed value is displayed in the patient information section and determines whether minor-specific fields (guardian information) are shown.

**Sources:** [Admin/ver_historia.php L46-L48](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L46-L48)

---

## UI Component Structure

The page uses Bootstrap 4 card components to organize information into distinct sections.

### Layout Architecture

```mermaid
flowchart TD

sidebar["Sidebar Navigation<br>(sidebar class)"]
main["Main Content<br>(main.bg.bg-white)"]
breadcrumb["Breadcrumb Navigation<br>(ol.breadcrumb)"]
card1["Card: Información General<br>(div.card.mb-4)"]
card2["Card: Anamnesis<br>(div.card.mb-4)"]
card3["Card: Información de la Cita<br>(div.card.mb-4)"]
card4["Card: Informe Médico<br>(div.card.mb-4)"]
pdf_form["PDF Download Form<br>(form action=descargar_historia.php)"]

sidebar --> main
main --> breadcrumb

subgraph subGraph0 ["Main Content Structure"]
    breadcrumb
    card1
    card2
    card3
    card4
    pdf_form
    breadcrumb --> card1
    card1 --> card2
    card2 --> card3
    card3 --> card4
    card4 --> pdf_form
end
```

**Sources:** [Admin/ver_historia.php L93-L296](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L93-L296)

---

## Patient Information Display

### Información General Card

The patient information is split into two columns within a Bootstrap grid:

**Left Column (Datos Principales):**

* Full name (`nombre` + `apellido`)
* Calculated age
* Birth date (`fecha_nacimiento`)
* Email (`correo_electronico`)
* Address (`lugar_direccion_residencia`)

**Right Column (Datos Adicionales):**

* Document number (`cedula`)
* Phone (`telefono`)
* Health insurance (`eps`)
* Gender (`sexo`)
* Occupation (`ocupacion`)
* Marital status (`estado_civil`)
* Emergency contact name and phone
* Blood type (`tipo_sangre`)
* Medical alerts (`alertas_medicas`)

**Conditional Minor Fields:**

For patients under 18 years old, additional guardian information is displayed:

| Field | Database Column | Condition |
| --- | --- | --- |
| Guardian name | `menor_acompanante` | `$age < 18` |
| Relationship | `menor_parentesco` | `$age < 18` |
| Guardian phone | `menor_telefono` | `$age < 18` |

**Sources:** [Admin/ver_historia.php L134-L168](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L134-L168)

 [Admin/ver_historia.php L158-L162](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L158-L162)

---

## Anamnesis Display Table

The anamnesis section presents family and personal medical history as a read-only table with checkmarks indicating yes/no responses.

### Medical History Fields

| History Type | Database Column | Display Label |
| --- | --- | --- |
| Cardiovascular diseases | `historia_cardiovasculares` | Enfermedades Cardiovasculares |
| Hemorrhagic diseases | `historia_hemorragicas` | Enfermedades Hemorrágicas |
| Dermatological diseases | `historia_dermatologicas` | Enfermedades Dermatológicas |
| Mental illnesses | `historia_mentales` | Enfermedades Mentales |
| Diabetes | `historia_diabetes` | Diabetes |
| Cancer | `historia_cancer` | Cáncer |
| Arthritis | `historia_artritis` | Artritis |
| Allergies | `historia_alergias` | Alergias |
| Surgeries | `historia_cirugias` | Cirugías |
| Other | `historia_otros` | Otros (free text) |

### Checkmark Rendering Logic

Each row uses conditional PHP to render FontAwesome check icons:

```php
<?php echo (isset($patient['historia_cardiovasculares']) && 
           $patient['historia_cardiovasculares'] == 'Sí') ? 
           '<i class="fas fa-check"></i>' : ''; ?>
```

**Sources:** [Admin/ver_historia.php L171-L237](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L171-L237)

---

## Appointment Information Display

### Displayed Appointment Fields

| Display Label | Data Source | Transformation |
| --- | --- | --- |
| Fecha | `$appointment['fecha_cita']` | None |
| Hora | `$appointment['hora_cita']` | None |
| Doctor | `$appointment['nombreD']` | From JOIN |
| Motivo de Consulta | `$appointment['tipo']` | From JOIN |
| Estado | `$appointment['estado']` | 'A' → "Realizada", else "Pendiente" |

The appointment data comes from the prepared statement that joins `citas`, `consultas`, and `doctor` tables.

**Sources:** [Admin/ver_historia.php L240-L251](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L240-L251)

---

## Medical Report and Image Display

### Text Fields Display

The medical report section displays multiple clinical examination fields:

| Field Label | Database Column | Default |
| --- | --- | --- |
| Examen Intraoral | `examen_intraoral` | 'N/A' |
| Examen Extraoral | `examen_extraoral` | 'N/A' |
| Examen ATM | `examen_atm` | 'N/A' |
| Evolución | `evolucion` | 'N/A' |
| Diagnóstico | `diagnostico` | 'N/A' |
| Plan de Tratamiento | `plan_tratamiento` | 'N/A' |
| Costo | `costo` | 0 (formatted as currency) |

**Sources:** [Admin/ver_historia.php L259-L277](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L259-L277)

### Image Display Logic

Images are conditionally displayed based on database field values:

```mermaid
flowchart TD

check_radio["medical_report['radiografia']<br>is not empty?"]
check_foto["medical_report['foto_boca']<br>is not empty?"]
display_radio["Display: class='uploaded-image' width='200'>"]
na_radio["Display:"]
display_foto["Display: class='uploaded-image' width='200'>"]
na_foto["Display:"]

check_radio --> display_radio
check_radio --> na_radio
check_foto --> display_foto
check_foto --> na_foto
```

**Image Display Implementation:**

**Radiograph:**

```php
<?php if (!empty($medical_report['radiografia'])) { ?>
    <img src="../uploads/radiografias/<?php echo htmlspecialchars($medical_report['radiografia']); ?>" 
         class="uploaded-image" alt="Radiografía" width="200">
<?php } else { ?>
    <p>N/A</p>
<?php } ?>
```

**Oral Photo:**

```php
<?php if (!empty($medical_report['foto_boca'])) { ?>
    <img src="../uploads/fotos_boca/<?php echo htmlspecialchars($medical_report['foto_boca']); ?>" 
         class="uploaded-image" alt="Foto de la Boca" width="200">
<?php } else { ?>
    <p>N/A</p>
<?php } ?>
```

**Sources:** [Admin/ver_historia.php L262-L273](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L262-L273)

---

## PDF Download Integration

The page includes a POST form that submits to `descargar_historia.php` to generate a PDF version of the clinical history.

### PDF Form Structure

```mermaid
flowchart TD

form["form action='descargar_historia.php'<br>method='POST'"]
hidden1["input type='hidden' name='id_cita'"]
hidden2["input type='hidden' name='id_paciente'"]
button["button type='submit'<br>class='btn btn-custom-secondary'"]
descargar_historia["descargar_historia.php<br>(PDF Generator)"]

form --> hidden1
form --> hidden2
form --> button
button --> descargar_historia
```

**Form Parameters:**

| Parameter | Source | Purpose |
| --- | --- | --- |
| `id_cita` | `$_GET['id_cita']` | Identifica la cita |
| `id_paciente` | `$_GET['id_paciente']` | Identifica al paciente |

**Fuentes:** [Admin/ver_historia.php L283-L290](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L283-L290)

---

## Navegación y migas de pan

### Navegación de la barra lateral

La barra lateral incluye enlaces a todas las secciones administrativas principales:

| Elemento del menú | Enlace | Estado activo |
| --- | --- | --- |
| Citas | `inicioAdmin.php` | No |
| Dentistas | `doctores.php` | No |
| Calendario | `calendar.php` | No |
| Mensajería | `consultarrepor.php` | No |
| Historia Clínica | `historia_clinica.php` | **Sí** |
| Cerrar sesión | `../php/cerrar.php` | No |

El elemento de menú "Historia Clínica" tiene la `active`clase aplicada.

**Fuentes:** [Admin/ver_historia.php L109-L118](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L109-L118)

### Ruta de migas de pan

La página incluye una ruta de navegación para el contexto:

```
Historia Clínica > Ver Historia
```

La ruta de navegación permite a los usuarios regresar a la vista de lista de historial clínico.

**Fuentes:** [Admin/ver_historia.php L128-L131](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L128-L131)

---

## Características de seguridad

### Sanitización de entrada

Todos los parámetros GET se desinfectan mediante `mysqli_real_escape_string()`:

```
$id_cita = mysqli_real_escape_string($link, $_GET['id_cita']);
$id_paciente = mysqli_real_escape_string($link, $_GET['id_paciente']);
```

**Fuentes:** [Admin/ver_historia.php L33-L34](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L33-L34)

### Escape de salida

Todos los datos mostrados se escapan utilizando `htmlspecialchars()`para evitar ataques XSS:

```php
echo htmlspecialchars($patient['nombre'] . ' ' . $patient['apellido']);
```

Este patrón se aplica a las más de 50 ubicaciones de salida en toda la página.

**Fuentes:** [Admin/ver_historia.php L142](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L142-L142)

(y en todo el archivo)

### Manejo de errores y redirecciones

La página implementa el manejo de errores defensivos con redirecciones para todos los escenarios de falla:

| Condición de error | Mensaje de sesión | Redirigir objetivo |
| --- | --- | --- |
| Sin sesión | "Error acceso al sistema: Sesión no iniciada." | `../index.php` |
| Token inválido | "Tu sesión ha sido cerrada por inicio en otro dispositivo". | `../index.php` |
| Parámetros faltantes | "Error: ID de cita o paciente no proporcionado." | `historia_clinica.php` |
| Paciente no encontrado | "Error: No se pudo obtener la información del paciente." | `historia_clinica.php` |
| Cita no encontrada | "Error: No se encontró la cita". | `historia_clinica.php` |

Todos los mensajes de error se almacenan `$_SESSION['MensajeTexto']`con el tipo `$_SESSION['MensajeTipo']`establecido en `"p-3 mb-2 bg-danger text-white"`.

**Fuentes:** [Admin/ver_historia.php L7-L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L7-L43)

 [Admin/ver_historia.php L62-L65](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L62-L65)

---

## Visualización del perfil del médico

La barra lateral muestra la información del perfil del médico que inició sesión:

### Fuentes de datos del perfil

| Elemento de visualización | Fuente de datos | Lógica |
| --- | --- | --- |
| Imagen de perfil | Selección basada en el género | `$row['sexo']`== 'Masculino' → `odontologo.png`, 'Femenino' →`odontologa.png` |
| Nombre | `$row['nombreD']`+`$row['apellido']` | UTF-8 decodificado |
| Ubicación | Texto estático | "Barbosa Santander" |

**Fuentes:** [Admin/ver_historia.php L100-L108](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L100-L108)

---

## Dependencias de CSS y JavaScript

### Dependencias de la hoja de estilo

| Hoja de estilo | Objetivo |
| --- | --- |
| `bootstrap.min.css` | Marco Bootstrap |
| `all.css`(Fuente Impresionante) | Fuente de icono |
| `admin.css` | Estilo de la interfaz de administración |
| `informe_paciente.css` | Estilos específicos de informes de pacientes |

**Fuentes:** [Admin/ver_historia.php L87-L90](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L87-L90)

### Dependencias de JavaScript

| Guion | Objetivo |
| --- | --- |
| `jquery.js` | biblioteca jQuery |
| `bootstrap.min.js` | Componentes de JavaScript de Bootstrap |
| `admin.js` | Funcionalidad específica del administrador |

**Fuentes:** [Admin/ver_historia.php L298-L300](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L298-L300)

---

## Resumen del flujo de datos

```mermaid
flowchart TD

start["User clicks 'Ver Historia'<br>from historia_clinica.php"]
auth["Session & Token Validation"]
params["GET Parameter Validation<br>(id_cita, id_paciente)"]
fetch_patient["consultarPaciente()<br>→ patient data"]
calc_age["Calculate age from fecha_nacimiento"]
fetch_appt["Query: citas JOIN consultas JOIN doctor<br>→ appointment data"]
fetch_report["Query: informe_medico<br>→ medical report data"]
fetch_doctor["consultarDoctor()<br>→ doctor profile"]
render["Render HTML with 4 card sections:<br>1. General Info<br>2. Anamnesis<br>3. Appointment<br>4. Medical Report"]
display_images["Conditionally display images<br>from uploads/ directories"]
pdf_button["PDF Download button<br>POST to descargar_historia.php"]

start --> auth
auth --> params
params --> fetch_patient
fetch_patient --> calc_age
calc_age --> fetch_appt
fetch_appt --> fetch_report
fetch_report --> fetch_doctor
fetch_doctor --> render
render --> display_images
display_images --> pdf_button
```

**Fuentes:** [Admin/ver_historia.php L1-L302](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/ver_historia.php#L1-L302)
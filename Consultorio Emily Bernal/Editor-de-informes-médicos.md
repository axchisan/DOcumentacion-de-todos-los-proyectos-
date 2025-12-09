# Editor de informes médicos

> **Archivos fuente relevantes**
> * [Admin/eliminar_imagen.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php)
> * [Admin/informe.php](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php)

## Propósito y alcance

El Editor de Informes Médicos ( `informe.php`) es la interfaz administrativa más compleja del sistema y funciona como una herramienta integral para la gestión de datos de pacientes e informes médicos. Esta página permite a los médicos:

* Editar la información demográfica del paciente y la información de contacto de emergencia
* Actualizar el historial médico (anamnesis) incluyendo antecedentes familiares y personales de enfermedades
* Crear y editar informes médicos detallados (informe_medico) con exámenes clínicos
* Cargar y administrar imágenes médicas (radiografías y fotografías orales)
* Generar informes en PDF a partir de los datos recopilados

Para ver las historias clínicas completas en formato de solo lectura, consulte [el Visor de detalles de la historia clínica](/axchisan/Consultorio_Emily_Bernal/2.3.2-clinical-history-detail-viewer) . Para obtener información sobre el proceso de generación de PDF, consulte [el Generador de informes médicos en PDF](/axchisan/Consultorio_Emily_Bernal/3.2-medical-report-pdf-generator) .

Fuentes:[Admin/informe.php L1-L435](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L1-L435)

## Acceso a la página y carga de datos

### Punto de entrada y autenticación

La página requiere una sesión médica válida y un parámetro de identificación del paciente. El control de acceso sigue un proceso de validación de tres niveles:

```mermaid
flowchart TD

Start["User accesses informe.php?id=PATIENT_ID"]
CheckSession["session id_doctor<br>exists?"]
SetError1["Set error message<br>MensajeTexto"]
Redirect1["Redirect to ../index.php"]
CheckID["GET parameter<br>id exists?"]
SetError2["Set error message"]
EscapeID["mysqli_real_escape_string<br>patient_id"]
Redirect2["Redirect to inicioAdmin.php"]
QueryPatient["consultarPaciente<br>link, patient_id"]
PatientExists["Patient<br>found?"]
SetError3["Set error message"]
Redirect3["Redirect to inicioAdmin.php"]
QueryAppointment["SELECT citas c<br>WHERE id_paciente = patient_id<br>AND id_doctor = doctor_id<br>ORDER BY fecha_cita DESC<br>LIMIT 1"]
AppointmentExists["Appointment<br>found?"]
SetError4["Set error message"]
Redirect4["Redirect to inicioAdmin.php"]
QueryReport["SELECT * FROM informe_medico<br>WHERE id_cita = appointment_id"]
RenderPage["Render page with<br>patient, appointment,<br>medical_report data"]

Start --> CheckSession
CheckSession --> SetError1
SetError1 --> Redirect1
CheckSession --> CheckID
CheckID --> SetError2
CheckID --> EscapeID
SetError2 --> Redirect2
EscapeID --> QueryPatient
QueryPatient --> PatientExists
PatientExists --> SetError3
SetError3 --> Redirect3
PatientExists --> QueryAppointment
QueryAppointment --> AppointmentExists
AppointmentExists --> SetError4
SetError4 --> Redirect4
AppointmentExists --> QueryReport
QueryReport --> RenderPage
```

**Entidades de código clave:**

* Variables de sesión:`$_SESSION['id_doctor']` [Admin/informe.php L11-L16](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L11-L16)
* Parámetro GET:`$_GET['id']` [Admin/informe.php L19-L24](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L19-L24)
* Funciones de consulta:`consultarPaciente()` [Admin/informe.php L30](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L30-L30)
* Consulta de cita más reciente:[Admin/informe.php L47-L56](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L47-L56)
* Recuperación de informes médicos:[Admin/informe.php L428-L434](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L428-L434)

Fuentes:[Admin/informe.php L1-L64](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L1-L64)

 [Admin/informe.php L428-L435](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L428-L435)

## Arquitectura de dos formas

La página implementa un patrón de diseño de formato dual, con controladores POST separados para actualizaciones de datos de pacientes versus actualizaciones de informes médicos:

| Forma | Nombre del botón Enviar | Tabla de base de datos | Objetivo |
| --- | --- | --- | --- |
| Información y anamnesis del paciente | `update_patient` | `pacientes` | Demografía, contactos de emergencia, historial médico |
| Informe médico | `update_medical` | `informe_medico` | Clinical examinations, treatment plans, images |

```mermaid
flowchart TD

Page["informe.php<br>Main Interface"]
PatientFields["Demographics<br>Emergency Contacts<br>Anamnesis Table"]
PatientSubmit["update_patient button"]
MedicalFields["Clinical Exams<br>Treatment Plans<br>Image Uploads"]
MedicalSubmit["update_medical button"]
UpdatePatient["UPDATE pacientes<br>WHERE id_paciente = ?"]
CheckExists["Informe<br>exists?"]
UpdateMedical["UPDATE informe_medico<br>WHERE id_cita = ?"]
InsertMedical["INSERT INTO informe_medico"]
PRG1["PRG Redirect<br>Location: informe.php?id=X"]
PRG2["PRG Redirect<br>Location: informe.php?id=X"]

PatientSubmit --> UpdatePatient
MedicalSubmit --> CheckExists
CheckExists --> UpdateMedical
CheckExists --> InsertMedical
UpdatePatient --> PRG1
UpdateMedical --> PRG2
InsertMedical --> PRG2

subgraph Form2 ["Medical Report Form"]
    MedicalFields
    MedicalSubmit
end

subgraph Form1 ["Patient Data Form"]
    PatientFields
    PatientSubmit
end
```

Sources: [Admin/informe.php L68-L171](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L68-L171)

 [Admin/informe.php L174-L425](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L174-L425)

 [Admin/informe.php L508-L668](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L508-L668)

 [Admin/informe.php L690-L772](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L690-L772)

## Patient Information Management

### Form Structure

The patient information form spans lines 508-668 and consists of three main sections:

1. **General Information (Read-only Display)**: Name, age, date of birth, email [Admin/informe.php L516-L522](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L516-L522)
2. **Editable Medical Data**: Phone, ID number, address, EPS, gender, occupation, civil status [Admin/informe.php L525-L583](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L525-L583)
3. **Anamnesis Table**: Nine medical history conditions (ENUM 'Sí'/'No') plus free-text "Otros" field [Admin/informe.php L593-L660](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L593-L660)

```mermaid
flowchart TD

ENUMFields["historia_cardiovasculares<br>historia_hemorragicas<br>historia_dermatologicas<br>historia_mentales<br>historia_diabetes<br>historia_cancer<br>historia_artritis<br>historia_alergias<br>historia_cirugias"]
OtherField["historia_otros<br>(maxlength=17 characters)"]
ReadOnly["Read-Only Section<br>nombre, apellido, edad<br>fecha_nacimiento, correo"]
Demographics["cedula, telefono<br>lugar_direccion_residencia<br>eps, genero, ocupacion<br>estado_civil"]
EmergencyContact["emergencia_nombre<br>emergencia_telefono"]
MinorFields["menor_acompanante<br>menor_parentesco<br>menor_telefono<br>(conditional, age < 18)"]
BloodType["tipo_sangre"]
AlertField["alertas_medicas<br>(textarea)"]
SubmitButton["update_patient button"]

subgraph PatientForm ["Patient Information Form"]
    ReadOnly
    AlertField

subgraph AnamnesisTable ["Anamnesis Table"]
    ENUMFields
    OtherField
end

subgraph EditableFields ["Editable Fields"]
    Demographics
    EmergencyContact
    MinorFields
    BloodType
end
end
```

Sources: [Admin/informe.php L508-L668](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L508-L668)

### Patient Update Handler

The patient update POST handler validates ENUM fields and uses a prepared statement with 25 bound parameters:

**ENUM Field Validation Pattern:**

```
$historia_cardiovasculares = isset($_POST['historia_cardiovasculares']) 
    && in_array($_POST['historia_cardiovasculares'], ['Sí', 'No']) 
    ? $_POST['historia_cardiovasculares'] : 'No';
```

This pattern ensures that only valid ENUM values ('Sí' or 'No') are stored in the database, defaulting to 'No' if invalid input is received. The validation is applied to all nine medical history fields [Admin/informe.php L85-L94](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L85-L94)

**UPDATE Query Structure:**

```mermaid
flowchart TD

POST["POST Request<br>update_patient=1"]
Sanitize["Sanitize 24 fields<br>mysqli_real_escape_string"]
ValidateENUM["Validate 9 ENUM fields<br>in_array check"]
PrepareStmt["mysqli_prepare<br>UPDATE pacientes SET..."]
BindParams["mysqli_stmt_bind_param<br>25 's' parameters"]
Execute["mysqli_stmt_execute"]
Success["Success?"]
SetSuccess["MensajeTexto = success<br>MensajeTipo = bg-success"]
SetError["MensajeTexto = error<br>MensajeTipo = bg-danger"]
Redirect["header Location:<br>informe.php?id=X"]

POST --> Sanitize
Sanitize --> ValidateENUM
ValidateENUM --> PrepareStmt
PrepareStmt --> BindParams
BindParams --> Execute
Execute --> Success
Success --> SetSuccess
Success --> SetError
SetSuccess --> Redirect
SetError --> Redirect
```

Sources: [Admin/informe.php L68-L171](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L68-L171)

## Medical Report Management

### Clinical Examination Fields

The medical report form captures 12 textual clinical examination fields stored in the `informe_medico` table:

| Field Name | Purpose | HTML Element |
| --- | --- | --- |
| `examen_intraoral` | Intraoral clinical examination | textarea |
| `examen_extraoral` | Extraoral clinical examination | textarea |
| `examen_atm` | ATM (temporomandibular joint) examination | textarea |
| `observacion_intraoral` | Intraoral observation and palpation | textarea |
| `observacion_extraoral_atm` | Extraoral observation (ATM and masticatory muscles) | textarea |
| `descripcion_radiografica` | Radiographic description | textarea |
| `diagnostico_periodontal` | Periodontal diagnosis | textarea |
| `plan_tratamiento` | Treatment plan | textarea |
| `pronostico` | Prognosis | textarea |
| `evolucion` | Evolution/Progress | textarea |
| `diagnostico` | Diagnosis | textarea |
| `costo` | Cost | number input (step=0.01) |

Sources: [Admin/informe.php L691-L766](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L691-L766)

### Medical Report Submission Logic

The medical report submission handler implements conditional INSERT vs UPDATE logic based on whether a report already exists for the appointment:

```mermaid
flowchart TD

POST["POST Request<br>update_medical=1"]
SanitizeFields["Sanitize 12 text fields<br>mysqli_real_escape_string<br>+ trim"]
HandleRadio["Handle radiografia upload<br>FILES['radiografia']"]
HandleFoto["Handle foto_boca upload<br>FILES['foto_boca']"]
CheckRadioUpload["Upload<br>success?"]
SetRadioVar["radiografia = filename"]
RadioEmpty["radiografia = ''"]
CheckFotoUpload["Upload<br>success?"]
SetFotoVar["foto_boca = filename"]
FotoEmpty["foto_boca = ''"]
CheckExists["SELECT * FROM informe_medico<br>WHERE id_cita = ?"]
ReportExists["Report<br>exists?"]
DetermineUpdate["Which images<br>uploaded?"]
UpdateBoth["UPDATE informe_medico SET<br>...12 fields...<br>radiografia = ?<br>foto_boca = ?<br>WHERE id_cita = ?"]
UpdateRadio["UPDATE informe_medico SET<br>...12 fields...<br>radiografia = ?<br>WHERE id_cita = ?"]
UpdateFoto["UPDATE informe_medico SET<br>...12 fields...<br>foto_boca = ?<br>WHERE id_cita = ?"]
UpdateNeither["UPDATE informe_medico SET<br>...12 fields...<br>WHERE id_cita = ?"]
InsertNew["INSERT INTO informe_medico<br>id_cita, id_paciente<br>...12 fields...<br>radiografia, foto_boca<br>VALUES 16 parameters"]
SetMessage["Set success/error<br>MensajeTexto"]
RedirectPRG["header Location:<br>informe.php?id=X"]

POST --> SanitizeFields
SanitizeFields --> HandleRadio
SanitizeFields --> HandleFoto
HandleRadio --> CheckRadioUpload
CheckRadioUpload --> SetRadioVar
CheckRadioUpload --> RadioEmpty
HandleFoto --> CheckFotoUpload
CheckFotoUpload --> SetFotoVar
CheckFotoUpload --> FotoEmpty
SetRadioVar --> CheckExists
RadioEmpty --> CheckExists
SetFotoVar --> CheckExists
FotoEmpty --> CheckExists
CheckExists --> ReportExists
ReportExists --> DetermineUpdate
DetermineUpdate --> UpdateBoth
DetermineUpdate --> UpdateRadio
DetermineUpdate --> UpdateFoto
DetermineUpdate --> UpdateNeither
ReportExists --> InsertNew
UpdateBoth --> SetMessage
UpdateRadio --> SetMessage
UpdateFoto --> SetMessage
UpdateNeither --> SetMessage
InsertNew --> SetMessage
SetMessage --> RedirectPRG
```

**Conditional UPDATE Logic:**

The system generates four different UPDATE queries based on which images were uploaded. This conditional approach prevents overwriting existing images when no new upload is provided:

* **Both images uploaded**: 14 parameters (12 text + 2 images) [Admin/informe.php L238-L273](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L238-L273)
* **Radiograph only**: 13 parameters (12 text + 1 image) [Admin/informe.php L275-L308](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L275-L308)
* **Oral photo only**: 13 parameters (12 text + 1 image) [Admin/informe.php L310-L343](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L310-L343)
* **No images uploaded**: 12 parameters (text fields only) [Admin/informe.php L345-L377](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L345-L377)

Sources: [Admin/informe.php L174-L425](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L174-L425)

## Image Upload System

### Upload Flow and Filename Generation

The image upload system implements a standardized workflow for both radiographs and oral photos:

```mermaid
sequenceDiagram
  participant HTML Form
  participant informe.php POST Handler
  participant File System
  participant Database

  HTML Form->>informe.php POST Handler: POST with FILES['radiografia']
  informe.php POST Handler->>informe.php POST Handler: or FILES['foto_boca']
  loop [Directory Missing]
    informe.php POST Handler->>HTML Form: Check $_FILES['type']['error']
    informe.php POST Handler->>informe.php POST Handler: === UPLOAD_ERR_OK
    informe.php POST Handler->>File System: Set error message
    File System-->>informe.php POST Handler: redirect with PRG
    informe.php POST Handler->>File System: Determine path
    File System-->>informe.php POST Handler: radiografias/ or fotos_boca/
    informe.php POST Handler->>informe.php POST Handler: is_dir(path)?
    note over informe.php POST Handler: Example:
    informe.php POST Handler->>File System: false
    File System-->>informe.php POST Handler: mkdir(path, 0777, true)
    informe.php POST Handler->>HTML Form: success/failure
    File System-->>informe.php POST Handler: Generate unique filename
    informe.php POST Handler->>informe.php POST Handler: patientID_type_timestamp.ext
    note over informe.php POST Handler: Conditional UPDATE/INSERT
    informe.php POST Handler->>Database: move_uploaded_file
    Database-->>informe.php POST Handler: tmp_name to target_path
    informe.php POST Handler->>HTML Form: false
  end
```

**Filename Pattern:**

```
{patient_id}_{type}_{timestamp}.{extension}
```

* `{patient_id}`: Patient ID from `$patient_id` variable
* `{type}`: Either "radiografia" or "boca"
* `{timestamp}`: Unix timestamp from `time()`
* `{extension}`: Original file extension from `pathinfo()`

Examples:

* `45_radiografia_1701123456.jpg`
* `45_boca_1701123500.png`

**Code Implementation:**

* Radiograph upload: [Admin/informe.php L192-L206](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L192-L206)
* Oral photo upload: [Admin/informe.php L209-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L209-L223)
* Directory paths: `../uploads/radiografias/` and `../uploads/fotos_boca/`

Sources: [Admin/informe.php L188-L223](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L188-L223)

### Image Preview and Deletion UI

The page displays uploaded images with inline preview and AJAX-powered deletion:

```mermaid
flowchart TD

CheckRadio["medical_report<br>radiografia<br>field set?"]
CheckFoto["medical_report<br>foto_boca<br>field set?"]
ShowRadio["Display image preview<br><br>+ Remove button<br>data-type='radiografia'"]
ShowFoto["Display image preview<br><br>+ Remove button<br>data-type='foto_boca'"]
HideRadio["No preview shown"]
HideFoto["No preview shown"]
RemoveBtn["Remove button clicked"]
ConfirmDialog["JavaScript confirm dialog"]
AJAXCall["fetch delete_image.php<br>POST: type, file_name, id_cita"]
DeleteScript["delete_image.php<br>processes deletion"]
JSONResponse["JSON response<br>success: true/false<br>message: string"]
UpdateUI["Remove .image-preview<br>element from DOM"]

ShowRadio --> RemoveBtn
ShowFoto --> RemoveBtn
RemoveBtn --> ConfirmDialog
ConfirmDialog --> AJAXCall
AJAXCall --> DeleteScript
DeleteScript --> JSONResponse
JSONResponse --> UpdateUI

subgraph ImageDisplay ["Image Display Logic"]
    CheckRadio
    CheckFoto
    ShowRadio
    ShowFoto
    HideRadio
    HideFoto
    CheckRadio --> ShowRadio
    CheckFoto --> ShowFoto
    CheckRadio --> HideRadio
    CheckFoto --> HideFoto
end
```

Sources: [Admin/informe.php L734-L753](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L734-L753)

 [Admin/informe.php L829-L861](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L829-L861)

## Image Deletion System

### AJAX Endpoint (delete_image.php)

The `delete_image.php` file provides a dedicated AJAX endpoint for image deletion with comprehensive validation:

```mermaid
flowchart TD

Start["AJAX Request to delete_image.php"]
CheckSession["SESSION<br>id_doctor<br>exists?"]
Error1["JSON response<br>success: false<br>message: 'Acceso no autorizado'"]
CheckParams["POST params<br>type, file_name<br>id_cita exist?"]
Error2["JSON response<br>success: false<br>message: 'Datos incompletos'"]
ValidateType["Validate type in<br>['radiografia', 'foto_boca']"]
TypeValid["Valid type?"]
Error3["JSON response<br>success: false<br>message: 'Tipo no válido'"]
BuildPath["Build file path<br>../uploads/radiografias/<br>or ../uploads/fotos_boca/"]
CheckFile["file_exists<br>file_path?"]
Error4["JSON response<br>success: false<br>message: 'Archivo no encontrado'"]
UnlinkFile["unlink file_path"]
UnlinkSuccess["Unlink<br>success?"]
Error5["JSON response<br>success: false<br>message: 'No se pudo eliminar'"]
UpdateDB["UPDATE informe_medico<br>SET type = NULL<br>WHERE id_cita = id_cita"]
DBSuccess["Query<br>success?"]
Error6["JSON response<br>success: false<br>message: mysqli_error"]
Success["JSON response<br>success: true<br>message: 'Imagen eliminada'"]

Start --> CheckSession
CheckSession --> Error1
CheckSession --> CheckParams
CheckParams --> Error2
CheckParams --> ValidateType
ValidateType --> TypeValid
TypeValid --> Error3
TypeValid --> BuildPath
BuildPath --> CheckFile
CheckFile --> Error4
CheckFile --> UnlinkFile
UnlinkFile --> UnlinkSuccess
UnlinkSuccess --> Error5
UnlinkSuccess --> UpdateDB
UpdateDB --> DBSuccess
DBSuccess --> Error6
DBSuccess --> Success
```

**Proceso de eliminación en dos pasos:**

1. **Eliminación de archivos físicos** :`unlink($file_path)` [delete_image.php L37-L40](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L37-L40)
2. **Actualización de base de datos NULL** :`UPDATE informe_medico SET $type = NULL` [delete_image.php L43-L44](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L43-L44)

Esta orden garantiza que, si falla la actualización de la base de datos, el archivo ya se haya eliminado, lo que evita que queden archivos huérfanos. Sin embargo, si falla la desvinculación, la base de datos no se modifica, lo que evita las referencias huérfanas.

**Validaciones de seguridad:**

* Comprobación de autenticación de sesión[delete_image.php L6-L9](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L6-L9)
* Validación de parámetros requeridos[delete_image.php L12-L15](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L12-L15)
* Validación de lista blanca de tipos[delete_image.php L22-L26](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L22-L26)
* Comprobación de existencia de archivo[delete_image.php L33-L36](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L33-L36)

Fuentes:[delete_image.php L1-L50](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L1-L50)

### Controlador de eliminación de frontend

El controlador de eliminación de JavaScript utiliza la API Fetch para comunicarse con el punto final de eliminación:

```javascript
fetch('delete_image.php', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/x-www-form-urlencoded'
    },
    body: `type=${type}&file_name=${encodeURIComponent(fileName)}&id_cita=${idCita}`
})
.then(response => response.json())
.then(data => {
    if (data.success) {
        imagePreview.remove();
        alert(data.message);
    } else {
        alert(data.message);
    }
})
```

**Extracción de parámetros:**

* `type`: Desde `data-type`el atributo en el botón eliminar[Admin/informe.php L833](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L833-L833)
* `file_name`:Extraído de `src`la URL del atributo de imagen[Admin/informe.php L834](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L834-L834)
* `id_cita`:Variable PHP incrustada en JavaScript[Admin/informe.php L835](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L835-L835)

Fuentes:[Admin/informe.php L838-L858](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L838-L858)

## Componentes de interfaz

### Contador de caracteres para el campo "Otros"

El campo "Otros" en la tabla de anamnesis tiene un límite de 17 caracteres con un contador de caracteres activo:

```mermaid
flowchart TD

TextArea["textarea#historia_otros<br>maxlength='17'"]
EventListener["input event listener"]
Calculate["remaining =<br>maxLength - value.length"]
UpdateDisplay["Update span#char_count<br>textContent = remaining"]
CheckThreshold["remaining < 5?"]
AddClass["Add class 'low'<br>(visual warning)"]
RemoveClass["Remove class 'low'"]
PageLoad["DOMContentLoaded"]
InitCounter["Initialize counter display"]

TextArea --> EventListener
EventListener --> Calculate
Calculate --> UpdateDisplay
UpdateDisplay --> CheckThreshold
CheckThreshold --> AddClass
CheckThreshold --> RemoveClass
PageLoad --> InitCounter
```

El contador muestra los caracteres restantes y agrega una advertencia visual (clase `low`) cuando quedan menos de 5 caracteres.

Fuentes:[Admin/informe.php L792-L810](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L792-L810)

### Estilo de entrada de archivo personalizado

Los elementos de entrada de archivo utilizan un estilo personalizado con JavaScript para mostrar los nombres de archivos seleccionados:

```mermaid
sequenceDiagram
  participant User
  participant input.custom-file-input
  participant span.file-upload-text

  User->>input.custom-file-input: Select file via file picker
  input.custom-file-input->>input.custom-file-input: change event triggered
  input.custom-file-input->>input.custom-file-input: Get this.files[0]?.name
  loop [File selected]
    input.custom-file-input->>span.file-upload-text: textContent = fileName
    note over span.file-upload-text: "ejemplo_radiografia.jpg"
    input.custom-file-input->>span.file-upload-text: textContent = "Selecciona un archivo..."
  end
  User->>span.file-upload-text: Sees updated filename text
```

Esto proporciona retroalimentación al usuario sin depender de la apariencia de entrada de archivo predeterminada del navegador.

Fuentes:[Admin/informe.php L821-L827](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L821-L827)

### Visualización de mensajes flash

La página utiliza mensajes flash basados ​​en sesión siguiendo el patrón Post-Redirect-Get:

```mermaid
stateDiagram-v2
    [*] --> CheckMessage : "Page loads"
    CheckMessage --> MessageExists : "isset($_SESSION['MensajeTexto'])"
    CheckMessage --> NoMessage : "!isset"
    MessageExists --> RenderAlert : "Display Bootstrap alertwith $_SESSION['MensajeTipo'] class"
    RenderAlert --> ClearSession : "$_SESSION['MensajeTexto'] = null$_SESSION['MensajeTipo'] = null"
    ClearSession --> UserSees : "User views alert"
    NoMessage --> [*] : "Remove alert from DOM"
    UserSees --> AutoHide : "JavaScript timeout (5s)"
    UserSees --> ManualClose : "User clicks delete button"
    AutoHide --> RemoveDOM : "Remove alert from DOM"
    ManualClose --> RemoveDOM : "User clicks delete button"
    RemoveDOM --> [*] : "Remove alert from DOM"
```

**Tipos de mensajes:**

* Éxito:`"p-3 mb-2 bg-success text-white"`
* Error:`"p-3 mb-2 bg-danger text-white"`

Fuentes:[Admin/informe.php L497-L506](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L497-L506)

 [Admin/informe.php L813-L819](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L813-L819)

## Resumen de operaciones de base de datos

### Uso de la declaración preparada

Todas las operaciones de base de datos utilizan sentencias preparadas con enlace de parámetros para evitar la inyección de SQL:

| Operación | Mesa | Tipo de declaración | Parámetros | Ubicación |
| --- | --- | --- | --- | --- |
| Cargar paciente | `pacientes` | SELECCIONAR | 1 ( `id_paciente`) | A través de`consultarPaciente()` |
| Cargar cita | `citas` | SELECCIONAR | 2 ( `id_paciente`, `id_doctor`) | [Admin/informe.php L47-L56](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L47-L56) |
| Cargar informe médico | `informe_medico` | SELECCIONAR | 1 ( `id_cita`) | [Admin/informe.php L428-L434](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L428-L434) |
| Actualizar paciente | `pacientes` | ACTUALIZAR | 25 (24 campos + `id_paciente`) | [Admin/informe.php L96-L158](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L96-L158) |
| Comprobar que existe informe | `informe_medico` | SELECCIONAR | 1 ( `id_cita`) | [Admin/informe.php L225-L229](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L225-L229) |
| Actualizar informe médico (sin imágenes) | `informe_medico` | ACTUALIZAR | 13 (12 campos + `id_cita`) | [Admin/informe.php L345-L377](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L345-L377) |
| Actualizar informe médico (1 imagen) | `informe_medico` | ACTUALIZAR | 14 (12 campos + 1 imagen + `id_cita`) | [Admin/informe.php L275-L343](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L275-L343) |
| Actualización del informe médico (2 imágenes) | `informe_medico` | ACTUALIZAR | 15 (12 campos + 2 imágenes + `id_cita`) | [Admin/informe.php L238-L273](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L238-L273) |
| Insertar informe médico | `informe_medico` | INSERTAR | 16 (2 ID + 12 campos + 2 imágenes) | [Admin/informe.php L389-L411](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L389-L411) |
| Eliminar referencia de imagen | `informe_medico` | ACTUALIZAR | 1 ( `id_cita`) | [delete_image.php L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L43-L43) |

Fuentes:[Admin/informe.php L47-L434](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L47-L434)

 [delete_image.php L43](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/delete_image.php#L43-L43)

## Integración con la generación de PDF

La página proporciona un botón para generar un informe médico en PDF, que se publica en `generate_informe_pdf.php`:

```mermaid
flowchart TD

Page["informe.php"]
PDFForm["method='post'>"]
HiddenInput["name='patient_id'<br>value='$patient_id'>"]
SubmitButton["Submit button<br>'Guardar Informe en PDF'"]
PDFGenerator["generate_informe_pdf.php"]
QueryData["Query informe_medico,<br>citas, pacientes"]
RenderPDF["TCPDF renders PDF<br>with images"]
Download["Browser download<br>or inline display"]

Page --> PDFForm
PDFForm --> HiddenInput
PDFForm --> SubmitButton
SubmitButton --> PDFGenerator
PDFGenerator --> QueryData
QueryData --> RenderPDF
RenderPDF --> Download
```

Para obtener información detallada sobre el proceso de generación de PDF, consulte [Generador de PDF de informes médicos](/axchisan/Consultorio_Emily_Bernal/3.2-medical-report-pdf-generator) .

Fuentes:[Admin/informe.php L773-L778](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L773-L778)

## Navegación de página y flujo de trabajo

Se accede al Editor de Informes Médicos desde la Vista de Lista de Historial Clínico a través del botón "Editar" e incluye navegación con ruta de navegación:

```mermaid
flowchart TD

Dashboard["inicioAdmin.php<br>Dashboard"]
HistoryList["historia_clinica.php<br>Clinical History List"]
InformePage["informe.php?id=X<br>Medical Report Editor"]
UpdatePatient["Update patient data<br>POST with update_patient"]
UpdateMedical["Update medical report<br>POST with update_medical"]
DeleteImage["Delete image<br>AJAX to delete_image.php"]
GeneratePDF["Generate PDF<br>POST to generate_informe_pdf.php"]
PDFDownload["PDF Download"]
BackToDashboard["Breadcrumb:<br>Inicio link"]

Dashboard --> HistoryList
HistoryList --> InformePage
InformePage --> UpdatePatient
InformePage --> UpdateMedical
InformePage --> DeleteImage
InformePage --> GeneratePDF
UpdatePatient --> InformePage
UpdateMedical --> InformePage
DeleteImage --> InformePage
GeneratePDF --> PDFDownload
InformePage --> BackToDashboard
BackToDashboard --> Dashboard
```

Fuentes:[Admin/informe.php L492-L495](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L492-L495)

 [Admin/informe.php L476-L480](https://github.com/axchisan/Consultorio_Emily_Bernal/blob/589034b9/Admin/informe.php#L476-L480)
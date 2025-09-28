# Variables del Survey de AWX para Gestión de Usuarios Microsoft 365

## Descripción General

Los playbooks han sido adaptados para funcionar con **AWX/Tower Job Templates** utilizando **Survey** para solicitar las variables necesarias. Todos los playbooks utilizan la normalización del **UserPrincipalName** donde solo se solicita la parte local (antes del @) y se construye el UPN completo usando el dominio de M365.

## Variables de Inventario Requeridas

### A nivel de Inventario (Group Variables o Host Variables):
```yaml
# Dominio de Microsoft 365 (requerido)
m365_domain: "tuempresa.com"  # o tu dominio personalizado

# Credenciales (opcional, pueden venir de variables de entorno)
vault_tenant_id: "{{ vault_tenant_id }}"
vault_client_id: "{{ vault_client_id }}"
vault_client_secret: "{{ vault_client_secret }}"
```

## Variables de Survey por Playbook

### 🔵 CREATE USER - `create_users.yml`

**Variables requeridas del Survey:**
- `user_upn` (string): Parte local del UPN (ej: "juan.perez")
- `display_name` (string): Nombre completo (ej: "Juan Pérez")
- `temp_password` (password): Contraseña temporal

**Variables opcionales del Survey:**
- `mail_nickname` (string): Alias de correo (por defecto usa `user_upn`)
- `force_password` (boolean): Forzar cambio de contraseña (por defecto: true)
- `department` (string): Departamento
- `job_title` (string): Puesto de trabajo

**Ejemplo de Survey:**
```yaml
- question_name: "user_upn"
  question_description: "Nombre de usuario (parte antes del @)"
  variable: "user_upn"
  type: "text"
  required: true

- question_name: "display_name" 
  question_description: "Nombre completo del usuario"
  variable: "display_name"
  type: "text"
  required: true

- question_name: "temp_password"
  question_description: "Contraseña temporal"
  variable: "temp_password"
  type: "password"
  required: true

- question_name: "department"
  question_description: "Departamento (opcional)"
  variable: "department"
  type: "text"
  required: false

- question_name: "job_title"
  question_description: "Puesto de trabajo (opcional)"
  variable: "job_title"
  type: "text"
  required: false
```

---

### 🔵 UPDATE USER - `update_users.yml`

**Variables requeridas del Survey:**
- `target_user_upn` (string): UPN del usuario a actualizar (parte local)

**Variables opcionales del Survey (solo se actualizan si se proporcionan):**
- `new_display_name` (string): Nuevo nombre completo
- `new_department` (string): Nuevo departamento
- `new_job_title` (string): Nuevo puesto de trabajo
- `new_office_location` (string): Nueva ubicación de oficina
- `new_phone_number` (string): Nuevo número de teléfono
- `new_street_address` (string): Nueva dirección

**Ejemplo de Survey:**
```yaml
- question_name: "target_user_upn"
  question_description: "Usuario a actualizar (parte antes del @)"
  variable: "target_user_upn"
  type: "text"
  required: true

- question_name: "new_display_name"
  question_description: "Nuevo nombre completo (opcional)"
  variable: "new_display_name"
  type: "text"
  required: false

- question_name: "new_department"
  question_description: "Nuevo departamento (opcional)"
  variable: "new_department"
  type: "text"
  required: false

- question_name: "new_job_title"
  question_description: "Nuevo puesto (opcional)"
  variable: "new_job_title"
  type: "text"
  required: false
```

---

### 🔵 DELETE USER - `delete_users.yml`

**Variables requeridas del Survey:**
- `target_user_upn` (string): UPN del usuario a eliminar (parte local)
- `confirm_deletion` (boolean): Confirmación de eliminación

**Ejemplo de Survey:**
```yaml
- question_name: "target_user_upn"
  question_description: "Usuario a ELIMINAR (parte antes del @)"
  variable: "target_user_upn"
  type: "text"
  required: true

- question_name: "confirm_deletion"
  question_description: "CONFIRMO que deseo ELIMINAR este usuario"
  variable: "confirm_deletion"
  type: "multiplechoice"
  choices: ["false", "true"]
  default: "false"
  required: true
```

---

### 🔵 QUERY USERS - `query_users.yml`

**Variables opcionales del Survey (filtros de búsqueda):**
- `user_upn` (string): Buscar usuario específico por UPN (parte local)
- `search_term` (string): Término de búsqueda general
- `filter_department` (string): Filtrar por departamento
- `filter_enabled` (string): Filtrar por estado ("true"/"false")
- `generate_report` (boolean): Generar reporte en archivo

**Ejemplo de Survey:**
```yaml
- question_name: "search_type"
  question_description: "Tipo de búsqueda"
  variable: "search_type"
  type: "multiplechoice"
  choices: ["specific_user", "by_department", "by_status", "general_search", "all_users"]
  required: true

- question_name: "user_upn"
  question_description: "Usuario específico (parte antes del @)"
  variable: "user_upn"
  type: "text"
  required: false

- question_name: "search_term"
  question_description: "Término de búsqueda general"
  variable: "search_term"
  type: "text"
  required: false

- question_name: "filter_department"
  question_description: "Departamento a filtrar"
  variable: "filter_department"
  type: "text"
  required: false

- question_name: "generate_report"
  question_description: "Generar reporte en archivo"
  variable: "generate_report"
  type: "multiplechoice"
  choices: ["false", "true"]
  default: "false"
  required: false
```

## Normalización del UserPrincipalName

### Proceso de Normalización
```yaml
# Input del Survey: user_upn = "Juan.Perez"
# Normalización: upn_normalized = "juan.perez"
# UPN final: "juan.perez@tuempresa.com"

upn_normalized: "{{ user_upn | lower }}"
user_principal_name: "{{ upn_normalized }}@{{ m365_domain }}"
```

### Reglas de Normalización Aplicadas
1. **Conversión a minúsculas**: Todos los caracteres se convierten a minúsculas
2. **Dominio automático**: Se concatena automáticamente con `m365_domain`
3. **Validación**: Se verifica que la parte local no esté vacía

## Variables de Entorno (Opcionales)

Los playbooks también soportan credenciales via variables de entorno:
```bash
export TENANT_ID="your-tenant-id"
export CLIENT_ID="your-client-id"
export CLIENT_SECRET="your-client-secret"
```

## Configuración de Job Templates en AWX

### Template Settings
```yaml
Name: "Create M365 User"
Job Type: "Run"
Inventory: "Microsoft365"
Project: "ansible-awx"
Playbook: "playbooks/users/create_users.yml"
Credentials: "Microsoft Graph API" (Custom credential type)
```

### Inventory Variables
```yaml
# En Group Variables de tu inventario AWX:
m365_domain: "tuempresa.com"
```

### Custom Credential Type (Microsoft Graph API)
```yaml
# INPUT CONFIGURATION
fields:
  - id: tenant_id
    type: string
    label: Tenant ID
  - id: client_id  
    type: string
    label: Client ID
  - id: client_secret
    type: string
    label: Client Secret
    secret: true

# INJECTOR CONFIGURATION  
env:
  TENANT_ID: "{{ tenant_id }}"
  CLIENT_ID: "{{ client_id }}"
  CLIENT_SECRET: "{{ client_secret }}"
```

## Ejemplos de Uso

### Crear Usuario
- **user_upn**: `maria.garcia`
- **display_name**: `María García`
- **temp_password**: `TempPass123!`
- **department**: `Ventas`
- **Resultado**: Usuario `maria.garcia@tuempresa.com` creado

### Actualizar Usuario  
- **target_user_upn**: `juan.perez`
- **new_department**: `DevOps`
- **new_job_title**: `Senior Developer`
- **Resultado**: Usuario `juan.perez@tuempresa.com` actualizado

### Eliminar Usuario
- **target_user_upn**: `usuario.temporal`
- **confirm_deletion**: `true`
- **Resultado**: Usuario `usuario.temporal@tuempresa.com` eliminado

### Consultar Usuarios
- **filter_department**: `IT`
- **generate_report**: `true`
- **Resultado**: Lista de usuarios del departamento IT + reporte generado
# Documentación de Playbooks - Gestión de Usuarios Microsoft 365

## Descripción General

Esta documentación describe los playbooks disponibles para la gestión de usuarios en Microsoft 365 utilizando la API de Microsoft Graph a través de Ansible.

## Playbooks Disponibles

### 1. create_users.yml
**Propósito**: Crear nuevos usuarios en Microsoft 365

**Ubicación**: `playbooks/users/create_users.yml`

**Variables requeridas**:
- `vault_tenant_id`: ID del tenant de Microsoft 365
- `vault_client_id`: ID de la aplicación registrada en Azure AD
- `vault_client_secret`: Secret de la aplicación

**Variables opcionales**:
- `users_to_create`: Lista de usuarios a crear (puede ser sobrescrita)

**Ejemplo de uso**:
```bash
ansible-playbook playbooks/users/create_users.yml -e @vars/users.yml
```

### 2. update_users.yml
**Propósito**: Actualizar usuarios existentes en Microsoft 365

**Ubicación**: `playbooks/users/update_users.yml`

**Variables requeridas**:
- Mismas que create_users.yml
- `users_to_update`: Lista de usuarios a actualizar con sus modificaciones

**Ejemplo de uso**:
```bash
ansible-playbook playbooks/users/update_users.yml
```

### 3. delete_users.yml
**Propósito**: Eliminar usuarios de Microsoft 365

**Ubicación**: `playbooks/users/delete_users.yml`

**Variables requeridas**:
- Mismas que create_users.yml
- `users_to_delete`: Lista de UPNs de usuarios a eliminar

**Variables opcionales**:
- `confirm_deletion`: Boolean para confirmar eliminación (default: true)

**Ejemplo de uso**:
```bash
ansible-playbook playbooks/users/delete_users.yml -e confirm_deletion=false
```

### 4. query_users.yml
**Propósito**: Consultar y listar usuarios existentes

**Ubicación**: `playbooks/users/query_users.yml`

**Variables opcionales**:
- `filter_department`: Filtrar por departamento
- `filter_enabled`: Filtrar por estado habilitado/deshabilitado
- `search_term`: Término de búsqueda en el nombre
- `generate_report`: Generar reporte en archivo (default: false)

**Ejemplo de uso**:
```bash
ansible-playbook playbooks/users/query_users.yml -e filter_department="IT" -e generate_report=true
```

## Rol: microsoft_graph_user

### Descripción
Rol que encapsula la funcionalidad de gestión de usuarios mediante Microsoft Graph API.

### Estructura
```
roles/microsoft_graph_user/
├── tasks/
│   ├── main.yml          # Punto de entrada del rol
│   ├── create.yml        # Tareas para crear usuarios
│   ├── update.yml        # Tareas para actualizar usuarios
│   ├── delete.yml        # Tareas para eliminar usuarios
│   └── query.yml         # Tareas para consultar usuarios
├── defaults/
│   └── main.yml          # Variables por defecto
└── templates/
    └── user_report.j2    # Plantilla para reportes
```

### Variables del Rol

**Variables por defecto** (en `defaults/main.yml`):
- `graph_api_url`: URL base de la API de Graph
- `graph_auth_url`: URL de autenticación
- `api_timeout`: Timeout para las llamadas API
- `max_retries`: Número máximo de reintentos
- `default_usage_location`: Ubicación por defecto para usuarios
- `default_account_enabled`: Estado por defecto de la cuenta
- `default_force_change_password_next_sign_in`: Forzar cambio de contraseña

**Variables requeridas para ejecutar**:
- `tenant_id`: ID del tenant
- `client_id`: ID de la aplicación
- `client_secret`: Secret de la aplicación
- `action`: Acción a realizar (create, update, delete, query)

### Acciones Disponibles

1. **create**: Crear un usuario
   - Variables adicionales: `user_data`
2. **update**: Actualizar un usuario
   - Variables adicionales: `user_principal_name`, `user_updates`
3. **delete**: Eliminar un usuario
   - Variables adicionales: `user_principal_name`
4. **query**: Consultar usuarios
   - Variables adicionales: `filters`

## Configuración Previa Requerida

### 1. Registro de Aplicación en Azure AD
1. Acceder al portal de Azure
2. Ir a "Azure Active Directory" > "App registrations"
3. Crear una nueva aplicación
4. Configurar permisos de API:
   - User.ReadWrite.All
   - Directory.ReadWrite.All
5. Crear un client secret
6. Anotar: Tenant ID, Client ID, Client Secret

### 2. Configuración de Variables Secretas
Crear un archivo de variables encriptado para almacenar credenciales:

```bash
ansible-vault create vault/secrets.yml
```

Contenido del archivo:
```yaml
vault_tenant_id: "tu-tenant-id"
vault_client_id: "tu-client-id"
vault_client_secret: "tu-client-secret"
```

### 3. Archivo de Configuración de Ansible
Crear `ansible.cfg` básico:
```ini
[defaults]
host_key_checking = False
roles_path = roles
vault_password_file = vault/.vault_pass
```

## Ejemplos de Uso

### Ejemplo 1: Crear múltiples usuarios
```yaml
# vars/new_users.yml
users_to_create:
  - display_name: "Ana García"
    user_principal_name: "ana.garcia@empresa.com"
    mail_nickname: "ana.garcia"
    password: "TempPass123!"
    department: "Ventas"
    job_title: "Representante de Ventas"
  - display_name: "Carlos López"
    user_principal_name: "carlos.lopez@empresa.com"
    mail_nickname: "carlos.lopez"
    password: "TempPass456!"
    department: "Marketing"
    job_title: "Especialista en Marketing"
```

Ejecutar:
```bash
ansible-playbook playbooks/users/create_users.yml -e @vars/new_users.yml --ask-vault-pass
```

### Ejemplo 2: Consulta con filtros
```bash
ansible-playbook playbooks/users/query_users.yml \
  -e filter_department="IT" \
  -e generate_report=true \
  --ask-vault-pass
```

## Troubleshooting

### Errores Comunes

1. **Error de autenticación (401)**
   - Verificar credenciales (tenant_id, client_id, client_secret)
   - Confirmar que la aplicación tenga los permisos correctos

2. **Error de permisos (403)**
   - Verificar que la aplicación tenga permisos de Graph API
   - Confirmar que se han otorgado los permisos de administrador

3. **Usuario ya existe (409)**
   - El UPN ya está en uso
   - Verificar que el usuario no exista previamente

4. **Usuario no encontrado (404)**
   - Verificar que el UPN sea correcto
   - El usuario podría haber sido eliminado previamente

### Logs y Debugging

Para obtener más información de debug:
```bash
ansible-playbook playbooks/users/create_users.yml -vvv
```

## Mejores Prácticas

1. **Seguridad**:
   - Siempre usar ansible-vault para credenciales
   - Rotar regularmente los client secrets
   - Usar principio de menor privilegio en permisos

2. **Gestión de Contraseñas**:
   - Generar contraseñas seguras
   - Forzar cambio en primera conexión
   - Considerar políticas de contraseñas organizacionales

3. **Operaciones**:
   - Siempre probar en entorno de desarrollo primero
   - Usar confirmaciones para operaciones destructivas
   - Mantener logs de todas las operaciones

4. **Mantenimiento**:
   - Revisar y actualizar regularmente los playbooks
   - Monitorear cambios en la API de Microsoft Graph
   - Documentar modificaciones personalizadas
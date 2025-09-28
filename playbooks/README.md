# Playbooks Interactivos - Gestión de Usuarios Microsoft 365

## Descripción

Los playbooks han sido modificados para ser **completamente interactivos**. Ahora solicitan todos los datos necesarios directamente al usuario durante la ejecución, eliminando la necesidad de archivos de variables predefinidos.

## Playbooks Disponibles

### 🎯 Menú Principal
**Archivo**: `main_menu.yml`
**Descripción**: Menú interactivo que permite navegar entre todas las opciones disponibles.

**Uso**:
```bash
ansible-playbook playbooks/main_menu.yml --ask-vault-pass
```

### 👤 Crear Usuario
**Archivo**: `users/create_users.yml`
**Descripción**: Creación interactiva de un nuevo usuario.

**Información solicitada**:
- Nombre completo
- User Principal Name (email)
- Alias de correo
- Contraseña temporal (oculta)
- Departamento (opcional)
- Puesto de trabajo (opcional)
- Forzar cambio de contraseña

**Validaciones**:
- Campos requeridos no vacíos
- Confirmación antes de crear
- Resumen de datos antes de procesar

### ✏️ Actualizar Usuario
**Archivo**: `users/update_users.yml`
**Descripción**: Actualización interactiva de usuarios existentes.

**Proceso**:
1. Solicita UPN del usuario a actualizar
2. Verifica que el usuario existe
3. Permite actualizar campos específicos:
   - Nombre completo
   - Departamento
   - Puesto de trabajo
   - Ubicación de oficina
   - Número de teléfono
   - Dirección postal

**Características**:
- Solo actualiza campos que se especifican
- Permite mantener valores actuales (Enter para omitir)
- Confirmación antes de aplicar cambios

### 🗑️ Eliminar Usuario
**Archivo**: `users/delete_users.yml`
**Descripción**: Eliminación segura e interactiva de usuarios.

**Proceso de seguridad**:
1. Advertencia sobre la operación irreversible
2. Solicita UPN del usuario
3. Permite cancelar escribiendo "CANCELAR"
4. Verifica existencia del usuario
5. Requiere escribir exactamente "ELIMINAR USUARIO"
6. Segunda confirmación con "SI, ELIMINAR"

**Características de seguridad**:
- Múltiples confirmaciones
- Textos específicos requeridos
- Advertencias claras sobre consecuencias

### 🔍 Consultar Usuarios
**Archivo**: `users/query_users.yml`
**Descripción**: Búsqueda interactiva y flexible de usuarios.

**Opciones de búsqueda**:
1. **Buscar por nombre o email**: Término específico
2. **Filtrar por departamento**: Departamento específico
3. **Filtrar por estado**: Usuarios habilitados/deshabilitados
4. **Listar todos**: Sin filtros
5. **Búsqueda combinada**: Múltiples filtros

**Características**:
- Resultados limitados a 10 primeros (con contador total)
- Opción de generar reporte en archivo
- Filtros flexibles y combinables

## Características Generales

### 🔒 Seguridad
- Contraseñas ocultas durante entrada
- Múltiples confirmaciones para operaciones críticas
- Validación de datos requeridos
- Gestión segura de credenciales con ansible-vault

### ✅ Validaciones
- Campos requeridos no vacíos
- Formatos de email válidos
- Existencia de usuarios antes de modificar/eliminar
- Confirmaciones específicas para operaciones destructivas

### 📊 Retroalimentación
- Mensajes informativos en cada paso
- Resúmenes antes de ejecutar operaciones
- Resultados detallados post-ejecución
- Indicadores visuales (emojis) para mejor UX

### 🔄 Experiencia de Usuario
- Prompts claros y descriptivos
- Opciones para cancelar operaciones
- Valores por defecto inteligentes
- Navegación intuitiva

## Ejemplos de Uso

### Crear Usuario Completo
```bash
ansible-playbook playbooks/users/create_users.yml --ask-vault-pass

# El sistema solicitará:
# - Nombre: Juan Pérez
# - UPN: juan.perez@empresa.com
# - Alias: juan.perez
# - Contraseña: [oculta]
# - Departamento: IT
# - Puesto: Developer
# - Forzar cambio: s
```

### Actualizar Solo Departamento
```bash
ansible-playbook playbooks/users/update_users.yml --ask-vault-pass

# El sistema solicitará:
# - UPN: juan.perez@empresa.com
# - Nuevo nombre: [Enter para mantener]
# - Nuevo departamento: DevOps
# - Nuevo puesto: [Enter para mantener]
# - ... etc
```

### Búsqueda por Departamento
```bash
ansible-playbook playbooks/users/query_users.yml --ask-vault-pass

# Seleccionar opción 2
# Departamento: IT
# Generar reporte: s
```

### Eliminación Segura
```bash
ansible-playbook playbooks/users/delete_users.yml --ask-vault-pass

# UPN: usuario@empresa.com
# Confirmación 1: ELIMINAR USUARIO
# Confirmación 2: SI, ELIMINAR
```

## Configuración Requerida

### 1. Credenciales (archivo vault/secrets.yml)
```yaml
vault_tenant_id: "your-tenant-id"
vault_client_id: "your-client-id"
vault_client_secret: "your-client-secret"
```

### 2. Encriptar credenciales
```bash
ansible-vault encrypt vault/secrets.yml
```

### 3. Configuración Ansible (opcional)
```bash
cp examples/ansible.cfg.example ansible.cfg
```

## Mejores Prácticas

### 📋 Antes de Ejecutar
- Verificar credenciales de acceso
- Confirmar permisos en Microsoft 365
- Probar en entorno de desarrollo primero

### 🔍 Durante la Ejecución
- Leer cuidadosamente todos los prompts
- Verificar datos en resúmenes antes de confirmar
- Usar "CANCELAR" o Ctrl+C si algo no está bien

### 📝 Después de Ejecutar
- Verificar operaciones en el portal de Microsoft 365
- Revisar logs si hay errores
- Documentar cambios importantes

## Troubleshooting

### Errores Comunes
- **Error 401**: Credenciales incorrectas o expiradas
- **Error 403**: Permisos insuficientes en Graph API
- **Error 404**: Usuario no encontrado
- **Error 409**: Usuario ya existe (en creación)

### Soluciones
1. Verificar credenciales en vault/secrets.yml
2. Confirmar permisos de aplicación en Azure AD
3. Revisar sintaxis de UPN (email)
4. Ejecutar con -vvv para debug detallado

## Logs y Debug

Para obtener información detallada:
```bash
ansible-playbook playbooks/users/create_users.yml --ask-vault-pass -vvv
```

Los logs se almacenan en `logs/ansible.log` (si está configurado en ansible.cfg).
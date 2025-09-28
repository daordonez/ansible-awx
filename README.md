# Ansible AWX - Gestión de Usuarios Microsoft 365

Este repositorio contiene playbooks de Ansible diseñados específicamente para la **gestión de usuarios** en Microsoft 365 mediante la API de Microsoft Graph.

## Descripción

Los playbooks están enfocados en las operaciones CRUD (Crear, Leer, Actualizar, Eliminar) de usuarios en entornos Microsoft 365, proporcionando una interfaz automatizada y versionable para la administración de usuarios.

## Estructura del Repositorio

```
ansible-awx/
├── playbooks/
│   └── users/                    # Playbooks para gestión de usuarios
│       ├── create_users.yml      # Crear usuarios
│       ├── update_users.yml      # Actualizar usuarios
│       ├── delete_users.yml      # Eliminar usuarios
│       └── query_users.yml       # Consultar usuarios
├── roles/
│   └── microsoft_graph_user/     # Rol para interacción con Graph API
│       ├── tasks/
│       ├── defaults/
│       └── templates/
├── docs/                         # Documentación completa
│   ├── README.md                 # Documentación detallada
│   └── variables.md              # Guía de variables
└── README.md                     # Este archivo
```

## Características principales

- **🎯 Optimizado para AWX/Tower**: Job Templates con Survey para entrada de datos
- **🔧 Normalización automática de UPN**: Solo se requiere la parte local del email
- **✅ Operaciones CRUD completas**: Crear, leer, actualizar y eliminar usuarios
- **🔗 Integración con Microsoft Graph API**: Utilización de la API oficial de Microsoft
- **🏗️ Estructura modular**: Roles reutilizables y playbooks específicos
- **🛡️ Gestión segura de credenciales**: Soporte completo para ansible-vault y variables de entorno
- **🔒 Validaciones y confirmaciones**: Múltiples verificaciones para operaciones críticas
- **📚 Documentación completa**: Guías detalladas para configuración de Survey en AWX

## Tecnologías utilizadas

- **Ansible**: Plataforma de automatización para la gestión de configuraciones
- **Microsoft Graph API**: API unificada de Microsoft para acceder a datos de Microsoft 365
- **AWX** (opcional): Interfaz web para la gestión y ejecución de playbooks

## Casos de uso

Este repositorio permite realizar mediante **Job Templates de AWX** con Survey:
- **Creación de usuarios** con normalización automática del UPN
- **Actualización selectiva** de campos específicos de usuario
- **Eliminación controlada** con confirmaciones obligatorias
- **Consultas flexibles** con diferentes filtros y opciones
- **Generación de reportes** automáticos de usuarios
- **Gestión de dominio unificado** mediante variable de inventario

## Requisitos previos

- Acceso a un tenant de Microsoft 365
- Aplicación registrada en Azure AD con permisos de Graph API
- Ansible instalado (versión 2.9 o superior)
- Python requests library instalada

## Inicio Rápido

1. **Configurar inventario en AWX**:
   ```yaml
   # Group Variables del inventario
   m365_domain: "tuempresa.com"  # Tu dominio de Microsoft 365
   ```

2. **Configurar credenciales** (Custom Credential Type):
   ```yaml
   # Microsoft Graph API credentials
   tenant_id: "your-tenant-id"
   client_id: "your-client-id" 
   client_secret: "your-client-secret"
   ```

3. **Crear Job Templates** con Survey habilitado:
   ```yaml
   # Ejemplo: Create User Job Template
   Name: "Crear Usuario M365"
   Playbook: "playbooks/users/create_users.yml"
   Survey: Enabled (ver documentación)
   ```

4. **Ver la documentación de Survey**:
   ```bash
   cat docs/AWX_SURVEY_VARIABLES.md
   ```

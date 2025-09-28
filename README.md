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

- **🎯 Playbooks 100% Interactivos**: Todos los datos se solicitan durante la ejecución
- **✅ Operaciones CRUD completas**: Crear, leer, actualizar y eliminar usuarios
- **🔗 Integración con Microsoft Graph API**: Utilización de la API oficial de Microsoft
- **🔧 Estructura modular**: Roles reutilizables y playbooks específicos
- **🛡️ Gestión segura de credenciales**: Soporte completo para ansible-vault
- **🔒 Validaciones y confirmaciones**: Múltiples verificaciones para operaciones críticas
- **📚 Documentación completa**: Guías detalladas y ejemplos paso a paso

## Tecnologías utilizadas

- **Ansible**: Plataforma de automatización para la gestión de configuraciones
- **Microsoft Graph API**: API unificada de Microsoft para acceder a datos de Microsoft 365
- **AWX** (opcional): Interfaz web para la gestión y ejecución de playbooks

## Casos de uso

Este repositorio permite realizar de manera **interactiva**:
- **Creación individual de usuarios** con validación en tiempo real
- **Actualización selectiva** de campos específicos de usuario
- **Eliminación segura** con múltiples confirmaciones
- **Consultas flexibles** con diferentes filtros y opciones
- **Generación de reportes** automáticos de usuarios
- **Gestión guiada** paso a paso sin necesidad de archivos de configuración

## Requisitos previos

- Acceso a un tenant de Microsoft 365
- Aplicación registrada en Azure AD con permisos de Graph API
- Ansible instalado (versión 2.9 o superior)
- Python requests library instalada

## Inicio Rápido

1. **Configurar credenciales**:
   ```bash
   # Copiar plantilla y editar con tus credenciales
   cp examples/secrets_template.yml vault/secrets.yml
   # Editar el archivo con tus credenciales reales
   # Encriptar el archivo
   ansible-vault encrypt vault/secrets.yml
   ```

2. **Usar el menú interactivo**:
   ```bash
   ansible-playbook playbooks/main_menu.yml --ask-vault-pass
   ```

3. **O ejecutar playbooks individuales**:
   ```bash
   # Crear usuario (completamente interactivo)
   ansible-playbook playbooks/users/create_users.yml --ask-vault-pass
   
   # Consultar usuarios (con opciones de filtrado)
   ansible-playbook playbooks/users/query_users.yml --ask-vault-pass
   ```

4. **Ver la documentación detallada**:
   ```bash
   cat playbooks/README.md
   ```

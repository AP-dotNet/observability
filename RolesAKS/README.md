# Roles y Bindings para AKS con Entra ID

Este repositorio contiene varios **ClusterRoles** y **ClusterRoleBindings** diseñados para asignar permisos específicos a grupos de **Entra ID** en un cluster de **Azure Kubernetes Service (AKS)**. Cada rol cubre un conjunto de permisos que responden a diferentes necesidades dentro de un entorno de Kubernetes.

## Tabla de contenido

1. [Requisitos Previos](#requisitos-previos)  
2. [Arquitectura de Roles](#arquitectura-de-roles)  
3. [Roles Disponibles](#roles-disponibles)  
   - [Cluster Administrator](#cluster-administrator)  
   - [Read-Only User](#read-only-user)  
   - [Developer](#developer)  
   - [Log Viewer](#log-viewer)  
   - [Network Manager](#network-manager)  
   - [Observability Manager](#observability-manager)  
   - [Storage Manager](#storage-manager)  
   - [Certificate Manager](#certificate-manager)  
4. [Cómo Usar Estos Roles](#cómo-usar-estos-roles)  
5. [Notas Adicionales](#notas-adicionales)

---

## Requisitos Previos

1. **Azure Kubernetes Service (AKS):**  
   Un cluster AKS configurado y accesible mediante `kubectl`.

2. **Integración con Entra ID:**  
   El cluster debe estar configurado para la autenticación con Entra ID (Azure AD). Consulta la [documentación oficial de Azure](https://learn.microsoft.com/azure/aks/) para más detalles.

3. **Grupos de Entra ID:**  
   Debes contar con uno o varios grupos en Entra ID. Para cada grupo, obtén el **Object ID** (no el nombre amigable ni el correo). Este ID se usará en los **ClusterRoleBindings**.

---

## Arquitectura de Roles

En Kubernetes, un **ClusterRole** define un conjunto de permisos que se aplican a nivel de todo el cluster. Para que esos permisos tomen efecto, se asocian (o **bindean**) a un usuario o grupo a través de un **ClusterRoleBinding**.

La secuencia es la siguiente:

1. **ClusterRole**: Determina “qué acciones” se pueden realizar y sobre “qué recursos”.
2. **ClusterRoleBinding**: Asigna ese rol a un grupo de Entra ID, permitiendo a los miembros de ese grupo ejercer los permisos definidos.

---

## Roles Disponibles

### Cluster Administrator
- **Propósito**: Otorga control total sobre todos los recursos y namespaces del cluster.  
- **Ejemplo de Uso**: Administradores de TI con máxima autoridad para crear, modificar y eliminar cualquier objeto en Kubernetes.

### Read-Only User
- **Propósito**: Permite la visualización de recursos a nivel de cluster, sin modificar nada.  
- **Ejemplo de Uso**: Auditores o analistas que necesiten revisar el estado del cluster y las aplicaciones.

### Developer
- **Propósito**: Conjunto de permisos para crear, actualizar o eliminar recursos de aplicaciones (pods, servicios, deployments, etc.), pero sin acceso a configuraciones de cluster críticas.  
- **Ejemplo de Uso**: Equipos de desarrollo que publican e iteran sobre sus aplicaciones.

### Log Viewer
- **Propósito**: Habilita la lectura de logs de pods y la revisión de eventos en el cluster.  
- **Ejemplo de Uso**: Soporte técnico o desarrolladores que diagnostican problemas de forma puntual.

### Network Manager
- **Propósito**: Otorga permisos para gestionar servicios, ingresses, endpoints y políticas de red.  
- **Ejemplo de Uso**: Ingenieros que configuran y mantienen la capa de red de las aplicaciones.

### Observability Manager
- **Propósito**: Permite configurar y gestionar recursos de observabilidad como Prometheus, Alertmanager, y colectores de logs.  
- **Ejemplo de Uso**: Equipos de SRE o DevOps enfocados en monitorización y alertas.

### Storage Manager
- **Propósito**: Gestiona recursos relacionados con almacenamiento (PersistentVolumes, PersistentVolumeClaims, StorageClasses, etc.).  
- **Ejemplo de Uso**: Administradores o equipos responsables de la configuración y mantenimiento del almacenamiento persistente.

### Certificate Manager
- **Propósito**: Habilita la gestión de certificados y secretos relacionados (cuando se usa `cert-manager` u otras herramientas).  
- **Ejemplo de Uso**: Ingenieros que se encargan de la emisión y renovación de certificados SSL/TLS dentro del cluster.

---

## Cómo Usar Estos Roles

1. **Clonar el repositorio** (o copiar los archivos YAML) en tu entorno local.  
2. **Editar los YAMLs** para cada `ClusterRoleBinding` y reemplazar la cadena `<ID_DEL_GRUPO_ENTRA_ID>` con el **Object ID** real de tu grupo de Entra ID.
3. **Aplicar los manifiestos** en el cluster:
   ```bash
   kubectl apply -f nombre-de-tu-archivo.yaml
4. **Verificar la asignación:**
   ```bash
   kubectl get clusterrole
   kubectl get clusterrolebinding

5. **Probar acceso:**

   - Inicia sesión con un usuario que sea miembro del grupo asociado en Entra ID.
   - Intenta realizar acciones de acuerdo a los permisos asignados (por ejemplo, listar pods, crear ingresses, etc.).


## Notas Adicionales

- Cada rol está diseñado como un **ClusterRole** con **ClusterRoleBinding**; esto significa que sus permisos se aplican a todo el cluster. Si deseas limitar el alcance de los permisos a un namespace específico, debes usar **Role** y **RoleBinding**.
  
- Para conocer más sobre **RBAC** en Kubernetes, visita la [documentación oficial](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

- Si tu cluster AKS usa la integración nativa con Entra ID, es posible que necesites habilitar el modo de **Azure RBAC for Kubernetes**. Revísalo en la [documentación de Azure](https://learn.microsoft.com/azure/aks/manage-azure-rbac).

- Siempre revisa y ajusta los permisos de cada rol conforme a las políticas de tu organización para **aplicar el principio de menor privilegio**.



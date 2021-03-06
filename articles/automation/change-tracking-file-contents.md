---
title: Administración de Change Tracking e Inventario en Azure Automation
description: En este artículo se explica cómo utilizar Change Tracking e Inventario para realizar el seguimiento de los cambios en el software y el servicio de Microsoft que se producen en su entorno.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779304"
---
# <a name="manage-change-tracking-and-inventory"></a>Administración de Change Tracking e Inventario

Al agregar un nuevo archivo o clave del Registro para su seguimiento, Azure Automation lo habilita para la característica [Change Tracking e Inventario](change-tracking.md). En este artículo se incluyen procedimientos para usar esta característica.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Habilitación de la característica completa Change Tracking e Inventario

Si ha habilitado la [supervisión de la integridad de los archivos (FIM) de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), puede usar la solución completa Change Tracking e Inventario como se describe a continuación. Este proceso no quita los valores de configuración.

> [!NOTE]
> La habilitación de la solución completa Change Tracking e Inventario puede provocar cargos adicionales. Consulte [Precios de Automation](https://azure.microsoft.com/pricing/details/automation/).

1. Para quitar la solución de supervisión, desplácese hasta el área de trabajo y búsquela en la [lista de soluciones de supervisión instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Haga clic en el nombre de la solución para abrir su página de resumen y haga clic en **Eliminar**, según se detalla en [Eliminación de una solución de supervisión](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Para volver a habilitar Change Tracking e Inventario, vaya a la cuenta de Automation y seleccione **Change Tracking** en **Configuration Management** (Administración de configuración).
4. Elija el área de trabajo de Log Analytics y la cuenta de Automation, confirme la configuración del área de trabajo y haga clic en **Habilitar**.

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>Incorporación de máquinas a la solución Change Tracking e Inventario

Para iniciar el seguimiento de cambios, debe habilitar la solución Change Tracking e Inventory en Azure Automation. Las siguientes son las maneras recomendadas y admitidas para incorporar las máquinas a esta característica: 

* [Incorporación desde una máquina virtual](automation-onboard-solutions-from-vm.md)
* [Incorporación mediante la exploración de varias máquinas](automation-onboard-solutions-from-browse.md)
* [Incorporación desde la cuenta de Automation](automation-onboard-solutions-from-automation-account.md)
* [Incorporación en un runbook de Azure Automation](automation-onboard-solutions.md)

## <a name="track-files"></a>Seguimiento de archivos

### <a name="configure-file-tracking-on-windows"></a>Configuración del seguimiento de archivos en Windows

Use los pasos siguientes para configurar el seguimiento de archivos en equipos Windows:

1. En la cuenta de Automation, seleccione **Change Tracking** en **Administración de configuración**. 
2. Haga clic en **Editar configuración** (el símbolo de engranaje).
3. En la página Configuración del área de trabajo, seleccione **Archivos de Windows** y, a continuación, haga clic en **+ Agregar** para agregar un nuevo archivo para realizar su seguimiento.
4. En el panel Agregar archivo de Windows para el seguimiento de cambios, escriba la información del archivo cuyo seguimiento se va a realizar y haga clic en **Guardar**. En la tabla siguiente se definen las propiedades que puede usar para la información.

    |Propiedad  |Descripción  |
    |---------|---------|
    |habilitado     | True si se aplica la configuración y False en caso contrario.        |
    |Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
    |Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
    |Escriba la ruta de acceso     | Ruta de acceso para buscar el archivo, por ejemplo: **c:\temp\\\*.txt**. También puede usar variables de entorno, como `%winDir%\System32\\\*.*`.       |
    |Tipo de ruta de acceso     | Tipo de ruta de acceso. Los valores son Archivo y Directorio.        |    
    |Recursividad     | True si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar y False en caso contrario.        |    
    |Cargar contenido de archivo | True para cargar el contenido del archivo en los cambios bajo seguimiento y False en caso contrario.|

5. Asegúrese de especificar True para **Cargar contenido de archivo**. Esta opción habilita el seguimiento del contenido del archivo para la ruta de acceso de archivo indicada.

### <a name="configure-file-tracking-on-linux"></a>Configuración del seguimiento de archivos en Linux

Use los pasos siguientes para configurar el seguimiento de archivos en equipos Linux:

1. En la cuenta de Automation, seleccione **Change Tracking** en **Administración de configuración**. 
2. Haga clic en **Editar configuración** (el símbolo de engranaje).
3. En la página Configuración del área de trabajo, seleccione **Archivos de Linux** y, a continuación, haga clic en **+ Agregar** para agregar un nuevo archivo para realizar su seguimiento.
4. En la panel Agregar archivo de Linux para el seguimiento de cambios, escriba la información del archivo o directorio cuyo seguimiento se va a realizar y haga clic en **Guardar**. En la tabla siguiente se definen las propiedades que puede usar para la información.

    |Propiedad  |Descripción  |
    |---------|---------|
    |habilitado     | True si se aplica la configuración y False en caso contrario.        |
    |Nombre del elemento     | Nombre descriptivo del archivo cuyo seguimiento se va a realizar.        |
    |Grupo     | Un nombre de grupo para agrupar lógicamente los archivos.        |
    |Escriba la ruta de acceso     | La ruta de acceso para buscar el archivo, por ejemplo: **/etc/*.conf**.       |
    |Tipo de ruta de acceso     | Tipo de ruta de acceso. Los valores son Archivo y Directorio.        |
    |Recursividad     | True si se usa recursividad al buscar el elemento cuyo seguimiento se va a realizar y False en caso contrario.        |
    |Usar sudo     | True si se usa sudo al buscar el elemento y False en caso contrario.         |
    |Vínculos     | Configuración que determina cómo tratar los vínculos simbólicos cuando se recorren directorios. Los valores posibles son:<br> Omitir: ignora los vínculos simbólicos y no incluye los archivos y directorios de referencia.<br>Seguir: sigue los vínculos simbólicos durante la recursión y también incluye los archivos o directorios de referencia.<br>Administrar: sigue los vínculos simbólicos y permite modificar el contenido devuelto. **Nota**: No se recomienda esta opción, ya que no admite la recuperación del contenido del archivo.    |
    |Cargar contenido de archivo | True para cargar el contenido del archivo en los cambios bajo seguimiento y False en caso contrario. |

5. Asegúrese de especificar True para **Cargar contenido de archivo**. Esta opción habilita el seguimiento del contenido del archivo para la ruta de acceso de archivo indicada.

   ![Agregar archivo de Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Seguimiento del contenido de archivos

El seguimiento del contenido de archivos le permite ver el contenido de un archivo antes y después de que se produzca un cambio del que se realiza el seguimiento. La característica guarda el contenido del archivo en una cuenta de almacenamiento después de que se produzca cada cambio. Estas son algunas reglas que deben seguirse para el seguimiento del contenido de archivos:

* Para almacenar el contenido del archivo se requiere una cuenta de almacenamiento estándar que use el modelo de implementación de Resource Manager. 

* No use cuentas de almacenamiento de modelos de implementación prémium y clásica. Consulte [Acerca de las cuentas de Azure Storage](../storage/common/storage-create-storage-account.md).

* La cuenta de almacenamiento que use solo se puede conectar a una cuenta de Automation.

* [Change Tracking e Inventario](change-tracking.md) se habilita en la cuenta de Automation.

### <a name="enable-tracking-for-file-content-changes"></a>Habilitación del seguimiento de los cambios del contenido de archivos

1. En Azure Portal, abra su cuenta de Automation y, a continuación, seleccione **Change Tracking** en **Administración de configuración**.
2. Haga clic en **Editar configuración** (el símbolo de engranaje).
3. Seleccione **Contenido del archivo** y haga clic en **Vínculo**. Esta acción abre el panel Agregar ubicación de contenido para Change Tracking.

   ![Habilitación de la ubicación del contenido](./media/change-tracking-file-contents/enable.png)

4. Seleccione la suscripción y la cuenta de almacenamiento que se va a usar para almacenar el contenido del archivo. 

5. Si desea habilitar el seguimiento del contenido de los archivos para todos los archivos que se sigan, seleccione **Activar** para **cargar el contenido del archivo para todas las configuraciones**. Puede cambiar esto para la ruta de acceso de cada archivo más adelante.

   ![Establecimiento de la cuenta de almacenamiento](./media/change-tracking-file-contents/storage-account.png)

6. Change Tracking e Inventario muestran los URI de la cuenta de almacenamiento y la firma de acceso compartido (SAS) cuando habilita el seguimiento de cambios del contenido de archivos. Las firmas expiran después de 365 días y puede volver a crearlas haciendo clic en **Regenerar**.

   ![Enumeración de claves de cuenta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visualización del contenido de un archivo del que se realiza el seguimiento

Cuando Change Tracking e Inventario detecta un cambio de un archivo del que se realiza el seguimiento, puede ver el contenido del archivo en el panel Cambiar detalles.  

![Enumeración de cambios](./media/change-tracking-file-contents/change-list.png)

1. En Azure Portal, abra su cuenta de Automation y, a continuación, seleccione **Change Tracking** en **Administración de configuración**.

2. Elija un archivo en la lista de cambios y seleccione **Ver cambios de contenido del archivo** para ver el contenido del archivo. En el panel Cambiar detalles se muestra la información estándar del archivo antes y después.

   ![Cambiar detalles](./media/change-tracking-file-contents/change-details.png)

3. El contenido del archivo se muestra en una vista en paralelo. Puede seleccionar **Alineado** para ver una vista alineada de los cambios.

## <a name="track-registry-keys"></a>Seguimiento de las claves del Registro

Use los pasos siguientes para configurar las claves del registro para realizar un seguimiento en los equipos Windows:

1. En la cuenta de Automation, seleccione **Change Tracking** en **Administración de configuración**. 
2. Haga clic en **Editar configuración** (el símbolo de engranaje).
3. En la página Configuración del área de trabajo, seleccione **Registro de Windows**.
4. Haga clic en **+ Agregar** para agregar una nueva clave del Registro para realizar su seguimiento.
5. En la página Agregar Registro de Windows para el seguimiento de cambios, escriba la información de la clave cuyo seguimiento se va a realizar y haga clic en **Guardar**. En la tabla siguiente se definen las propiedades que puede usar para la información.

    |Propiedad  |Descripción  |
    |---------|---------|
    |habilitado     | True si se aplica la configuración y False en caso contrario.        |
    |Nombre del elemento     | Nombre descriptivo de la clave del Registro cuyo seguimiento se va a realizar.        |
    |Grupo     | Nombre de grupo para agrupar lógicamente las claves del Registro.        |
    |Clave del registro de Windows   | Nombre de clave con la ruta de acceso, como **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**.      |

## <a name="search-logs-for-change-records"></a>Búsqueda de registros de cambios en los registros

Puede realizar varias búsquedas en los registros de Azure Monitor para consultar los registros de cambios. Con la página Change Tracking abierta, haga clic en **Log Analytics** para abrir la página Registros. En la tabla siguiente se proporcionan ejemplos de búsquedas de registros para los registros de cambios.

|Consultar  |Descripción  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "Microsoft services" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Muestra los registros de inventario más recientes para los servicios de Microsoft que se establecieron en Automático, pero se han notificado como Detenidos. Los resultados se limitan al registro más reciente para el equipo y el nombre del software especificados.    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Muestra los registros de cambios del software eliminado.|

## <a name="create-alerts-on-changes"></a>Creación de alertas sobre los cambios

En el ejemplo siguiente se muestra que el archivo **C:\windows\system32\drivers\etc\hosts** se ha modificado en un equipo. Este archivo es importante porque Windows lo usa para resolver los nombres de host en direcciones IP. Esta operación tiene prioridad sobre DNS y puede provocar problemas de conectividad. También puede provocar el redireccionamiento del tráfico a sitios web malintencionados o peligrosos.

![Un gráfico en el que se muestra el cambio del archivo de hosts](./media/change-tracking-file-contents/changes.png)

Vamos a usar este ejemplo para describir los pasos para crear alertas sobre un cambio.

1. En la cuenta de Automation, seleccione **Seguimiento de cambios** en **Administración de configuración** y, a continuación, seleccione **Log Analytics**. 
2. En la búsqueda de registros, busque los cambios de contenido del archivo **hosts** con la consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Esta consulta busca un cambio de contenido para los archivos con una ruta de acceso completa que contenga la palabra "hosts". También puede solicitar un archivo específico si cambia la parte de la ruta de acceso a su forma completa, por ejemplo con `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Una vez que la consulta devuelve los resultados deseados, haga clic en **Nueva regla de alertas** en la búsqueda de registros para abrir la página de creación de alertas. También puede acceder a esta página mediante **Azure Monitor** en Azure Portal. 

4. Vuelva a comprobar la consulta y modifique la lógica de la alerta. En este caso, desea que la alerta se desencadene incluso aunque se detecte un solo cambio en todas las máquinas del entorno.

    ![Cambio en la consulta de seguimiento de cambios en el archivo hosts](./media/change-tracking-file-contents/change-query.png)

5. Después de establecer la lógica de la alerta, asigne grupos de acciones para realizar acciones como respuesta a la alerta que se va a desencadenar. En este caso, vamos a configurar los correos electrónicos que se enviarán y el vale de Administración de servicios de TI (ITSM) que se va a crear. 

    ![Configuración del grupo de acciones para alertar sobre cambios](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre los conceptos básicos de Change Tracking e Inventario, consulte [Introducción a Change Tracking e Inventario](change-tracking.md).
* Para solucionar problemas de cambios en una VM de Azure, consulte [Solución de problemas de Change Tracking e Inventario](troubleshoot/change-tracking.md).
* Consulte [Búsquedas de registros en los registros de Azure Monitor](../log-analytics/log-analytics-log-searches.md) para ver datos detallados sobre el seguimiento de cambios.
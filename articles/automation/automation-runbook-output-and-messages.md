---
title: Salidas de runbook y mensajes en Azure Automation
description: Describe cómo crear y recuperar la salida o los mensajes de error de los runbooks en Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535543"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Salidas de runbook y mensajes en Azure Automation

La mayoría de los runbooks de Azure Automation tienen alguna forma de salida. Esta salida puede ser un mensaje de error para el usuario o un objeto complejo que se va a usar con otro runbook. Windows PowerShell le ofrece [varios flujos](/powershell/module/microsoft.powershell.core/about/about_redirection) para enviar la salida desde un script o flujo de trabajo. Azure Automation funciona de forma diferente con cada uno de estos flujos. Debe seguir los procedimientos recomendados sobre cómo usar los flujos al crear un runbook.

En la tabla siguiente se describe brevemente cada flujo con su comportamiento en Azure Portal en los runbooks publicados y durante las [pruebas de un runbook](automation-testing-runbook.md). El flujo de salida es el flujo principal que se usa para la comunicación entre runbooks. El resto de los flujos se clasifican como flujos de mensajes, pensados para comunicar información al usuario. 

| STREAM | Descripción | Publicado | Prueba |
|:--- |:--- |:--- |:--- |
| Error |Mensaje de error destinado al usuario. A diferencia de lo que sucede con las excepciones, el runbook continúa después de un mensaje de error de forma predeterminada. |Se escribe en el historial de trabajos. |Se muestra en el panel de salida de la prueba. |
| Depurar |Mensajes dirigidos a un usuario interactivo. No debe usarse en runbooks. |No se escribe en el historial de trabajos. |No se muestra en el panel de salida de la prueba. |
| Output |Objetos destinados a ser consumidos por otros runbooks. |Se escribe en el historial de trabajos. |Se muestra en el panel de salida de la prueba. |
| Progreso |Registros que se generan automáticamente antes y después de cada actividad del runbook. El runbook no debe intentar crear sus propios registros de progreso, ya que están dirigidos a un usuario interactivo. |Solo se escribe en el historial de trabajos si el registro del progreso del runbook está activado. |No se muestra en el panel de salida de la prueba. |
| Verbose |Mensajes que proporcionan información general o de depuración. |Solo se escribe en el historial de trabajos si el registro detallado del runbook está activado. |Se muestra en el panel de salida de la prueba solo si la variable `VerbosePreference` está establecida en Continue en el runbook. |
| Advertencia |Mensaje de advertencia destinado al usuario. |Se escribe en el historial de trabajos. |Se muestra en el panel de salida de la prueba. |

>[!NOTE]
>Este artículo se ha actualizado para usar el nuevo módulo Az de Azure PowerShell. Aún puede usar el módulo de AzureRM que continuará recibiendo correcciones de errores hasta diciembre de 2020 como mínimo. Para más información acerca del nuevo módulo Az y la compatibilidad con AzureRM, consulte [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Presentación del nuevo módulo Az de Azure PowerShell). Para obtener instrucciones sobre la instalación del módulo Az en Hybrid Runbook Worker, consulte [Instalación del módulo de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Puede actualizar los módulos de su cuenta de Automation a la versión más reciente mediante [Actualización de módulos de Azure PowerShell en Azure Automation](automation-update-azure-modules.md).

## <a name="output-stream"></a>Flujo de salida

El flujo de salida se usa para la salida de los objetos creados por un script o flujo de trabajo, cuando se ejecuta correctamente. Azure Automation usa este flujo principalmente para objetos destinados a ser consumidos por runbooks primarios que llaman al [runbook actual](automation-child-runbooks.md). Cuando un runbook primario [llama a un runbook insertado](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), el runbook secundario le devuelve los datos del flujo de salida a él. 

El runbook usa el flujo de salida para comunicar información general al cliente solo si nunca se le llama desde otro runbook. Como procedimiento recomendado, sin embargo, le recomendamos que los runbooks usen la opción de [flujo detallado](#verbose-stream) para comunicar información general al usuario.

Para que el runbook escriba datos en el flujo de salida, utilice [Write-Output](https://technet.microsoft.com/library/hh849921.aspx). Como alternativa, puede colocar el objeto en su propia línea en el script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Control de la salida de una función

Cuando una función de runbook escribe en el flujo de salida, la salida se devuelve al runbook. Si el runbook asigna esa salida a una variable, ya no se escribe en el flujo de salida. Asimismo, si se escribe en cualquier otro flujo desde la función, se escribirá en el flujo correspondiente del runbook. Considere el siguiente ejemplo de runbook del flujo de trabajo de PowerShell.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

El flujo de salida del trabajo del runbook es:

```output
Output inside of function
Output outside of function
```

El flujo detallado del trabajo del runbook es:

```output
Verbose outside of function
Verbose inside of function
```

Una vez que haya publicado el runbook y antes de iniciarlo, debe activar también el registro detallado en la configuración del runbook para obtener la salida de flujo detallada.

### <a name="declaring-output-data-type"></a>Declarar los tipos de datos de salida

A continuación se muestran ejemplos de tipos de datos de salida:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Declaración del tipo de datos de salida de un flujo de trabajo

Un flujo de trabajo especifica el tipo de datos de su salida mediante el [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Este atributo no tiene ningún efecto en tiempo de ejecución, pero en tiempo de diseño proporciona una indicación de la salida esperada del runbook. A medida que el conjunto de herramientas de los runbooks evoluciona, aumenta la importancia de la declaración de los tipos de datos de la salida en tiempo de diseño. Por tanto, es recomendable incluir esta declaración en cualquier runbook que se cree.

El siguiente runbook de ejemplo genera un objeto de cadena e incluye una declaración de su tipo de salida. Si su runbook genera una matriz de un tipo determinado, deberá especificar el tipo en lugar de una matriz de ese tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Declaración del tipo de datos de salida en un runbook gráfico

Para declarar un tipo de salida en un runbook gráfico o de flujo de trabajo de PowerShell gráfico, puede seleccionar la opción de menú **Entrada y salida** y especificar el tipo de salida. Se recomienda utilizar el nombre completo de clase .NET para que se pueda identificar fácilmente el tipo cuando se haga referencia a él desde un runbook primario. Al usar el nombre completo, se exponen todas las propiedades de la clase al bus de datos del runbook. Además, se aumenta la flexibilidad cuando se usan las propiedades para la lógica condicional, el registro y la referencia como valores para otras actividades de runbooks.<br> ![Opción de entrada y salida de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Después de escribir un valor en el cuadro **Tipo de salida** del panel Propiedades de entrada y salida, asegúrese de hacer clic fuera del control para que reconozca la entrada.

En el ejemplo siguiente se utilizan dos runbooks gráficos para mostrar la característica de entrada y salida. Si se aplica el modelo de diseño modular de runbook, tenemos un runbook como plantilla de runbook de autenticación que administra la autenticación con Azure mediante la cuenta de ejecución. El segundo runbook, que normalmente realiza la lógica básica para automatizar un determinado escenario, en este caso ejecuta la plantilla de runbook de autenticación. Muestra los resultados en el panel de salida de la prueba. En circunstancias normales, haríamos que este runbook hiciera algo en un recurso que está aprovechando la salida del runbook secundario.

Esta es la lógica básica del runbook **AuthenticateTo-Azure**.<br> ![Autenticar ejemplo de plantilla de Runbook](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

El runbook incluye el tipo de salida `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`, que devuelve las propiedades del perfil de autenticación.<br> ![Ejemplo de tipo de salida de Runbooks](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Aunque este runbook es muy simple, hay un elemento de configuración que se debe destacar. La última actividad ejecuta el cmdlet `Write-Output` para escribir datos de perfil en una variable mediante una expresión de PowerShell para el parámetro `Inputobject`. Este parámetro es obligatorio en el cmdlet `Write-Output`.

El segundo runbook de este ejemplo, denominado **Test-ChildOutputType**, simplemente define dos actividades.<br> ![Runbook de tipo de salida secundario de ejemplo](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

La primera actividad llama al runbook **AuthenticateTo-Azure**. La segunda actividad ejecuta el cmdlet `Write-Verbose` con la opción **Origen de datos** establecida en **Resultado de la actividad**. Además, **Ruta de campo** se establece en **Context.Subscription.SubscriptionName**, la salida de contexto del runbook **AuthenticateTo-Azure**.<br> ![Origen de datos del parámetro del cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

La salida resultante es el nombre de la suscripción.<br> ![Resultados de Runbook de Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Flujos de mensajes

A diferencia del flujo de salida, los flujos de mensajes comunican información al usuario. Hay varios flujos de mensajes para los diferentes tipos de información, y Azure Automation controla cada uno de ellos de forma diferente.

### <a name="warning-and-error-streams"></a>Flujos de error y de advertencia

Los flujos de error y de advertencia registran los problemas que se producen en un runbook. Azure Automation escribe estos flujos en el historial de trabajos cuando se ejecuta un runbook. Cuando se prueba un runbook, Automation incluye los flujos en el panel de salida de la prueba de Azure Portal. 

De forma predeterminada, un runbook continúa ejecutándose después de una advertencia o un error. Puede especificar que el runbook se suspenda en caso de advertencia o error haciendo que el runbook establezca una [variable de preferencia](#preference-variables) antes de crear el mensaje. Por ejemplo, para hacer que el runbook se suspenda en caso de que se produzca un error, al igual que lo hace en caso de una excepción, establezca la variable `ErrorActionPreference` en Stop.

Cree un mensaje de advertencia o de error mediante los cmdlets [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). Las actividades también pueden escribir en los flujos de advertencia y de error.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Flujo de depuración

Azure Automation usa el flujo de mensajes de depuración para usuarios interactivos. No debe usarse en runbooks.

### <a name="verbose-stream"></a>Flujo detallado

El flujo de mensajes detallados ofrece información general acerca del funcionamiento del runbook. Puesto que el flujo de depuración no está disponible para un runbook, este debe usar mensajes detallados para proporcionar la información referente a la depuración. 

De forma predeterminada, el historial de trabajos no almacena mensajes detallados de runbooks publicados, por motivos de rendimiento. Para almacenar mensajes detallados, use la pestaña **Configurar** de Azure Portal con el valor **Registrar registros detallados**; de este modo establece que los runbooks publicados registren mensajes detallados. Active esta opción solo para solucionar problemas o para depurar un runbook. En la mayoría de los casos, deberá mantener la configuración predeterminada y no escribir los registros detallados.

Cuando se [prueba un runbook](automation-testing-runbook.md), los mensajes detallados no se muestran aunque el runbook esté configurado para escribir registros detallados. Para mostrar mensajes detallados al [probar un runbook](automation-testing-runbook.md), debe establecer la variable `VerbosePreference` en Continue. Cuando se establece este valor en la variable, los mensajes detallados se muestran en el panel de salida de la prueba de Azure Portal.

En el código siguiente se crea un mensaje detallado mediante el cmdlet [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx).

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Registros de progreso

Puede usar la pestaña **Configurar** de Azure Portal para establecer que un runbook escriba los registros de progreso. En la configuración predeterminada no se escriben los registros, con el fin de maximizar el rendimiento. En la mayoría de los casos, debe mantener el valor predeterminado. Active esta opción solo para solucionar problemas o para depurar un runbook. 

Si habilita el registro de progreso, el runbook escribe un registro en el historial de trabajos antes y después de que se ejecute cada actividad. Cuando se prueba un runbook, no se muestran los mensajes de progreso, aunque el runbook esté configurado para escribir estos registros.

>[!NOTE]
>El cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) no es válido en un runbook, ya que está pensado para usarse con un usuario interactivo.

## <a name="preference-variables"></a>Variables de preferencia

Puede establecer determinadas [variables de preferencia](https://technet.microsoft.com/library/hh847796.aspx) de Windows PowerShell en los runbooks, para controlar la respuesta a los datos enviados a diferentes flujos de salida. En la siguiente tabla se enumeran las variables de preferencia que pueden usarse en runbooks, junto con sus valores predeterminados y válidos. Hay otros valores disponibles para las variables de preferencia cuando se usan en Windows PowerShell fuera de Azure Automation.

| Variable | Valor predeterminado | Valores válidos |
|:--- |:--- |:--- |
| `WarningPreference` |Continuar |Stop<br>Continuar<br>SilentlyContinue |
| `ErrorActionPreference` |Continuar |Stop<br>Continuar<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continuar<br>SilentlyContinue |

En la siguiente tabla se muestra el comportamiento de los valores de las variables de preferencia que son válidos en los runbooks.

| Value | Comportamiento |
|:--- |:--- |
| Continuar |Registra el mensaje y continúa ejecutando el runbook. |
| SilentlyContinue |Continúa la ejecución del runbook sin registrar el mensaje. Este valor hace que se ignore el mensaje. |
| Stop |Registra el mensaje y suspende el runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperar salidas de runbooks y mensajes

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperación de salidas de runbook y mensajes en Azure Portal

Puede ver los detalles de un trabajo de runbook en Azure Portal, en la pestaña **Trabajos** del runbook. El resumen del trabajo mostrará los parámetros de entrada y el [flujo de salida](#output-stream), además de información general sobre el trabajo y las excepciones que se hayan producido. El historial de trabajos incluye los mensajes del flujo de salida, así como de los [flujos de advertencia y error](#warning-and-error-streams). También incluye los mensajes del [flujo detallado](#verbose-stream) y los [registros de progreso](#progress-records) si el runbook está configurado para escribir registros detallados y de progreso.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperación de salidas de runbook y mensajes en Windows PowerShell

En Windows PowerShell, puede recuperar la salida y los mensajes de un runbook con el cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0). Este cmdlet requiere la identificación del trabajo y tiene un parámetro denominado `Stream` en el cual se especifica qué flujo hay que recuperar. Puede especificar un valor de Any para este parámetro, con el fin de recuperar todos los flujos del trabajo.

En el ejemplo siguiente se inicia un runbook y, a continuación, se espera a que finalice. Una vez que el runbook finaliza la ejecución, el script recopila su flujo de salida del trabajo.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperación de salidas de runbook y mensajes en runbooks gráficos

En los runbooks gráficos, hay disponible un registro adicional de salida y mensajes en forma de seguimiento del nivel de actividad. Hay dos niveles de seguimiento: básico y detallado. El seguimiento básico muestra la hora de inicio y de finalización de cada actividad del runbook, así como información relacionada con los reintentos de actividad, como el número de intentos y la hora de inicio de la actividad. El seguimiento detallado incluye las características del seguimiento básico más el registro de los datos de entrada y salida de cada actividad. 

Actualmente el seguimiento en el nivel de actividad escribe registros mediante el flujo detallado. Por lo tanto, debe habilitar el registro detallado cuando habilite el seguimiento. Para los runbooks gráficos con el seguimiento habilitado, no hay ninguna necesidad de escribir registros de progreso. El seguimiento básico tiene la misma finalidad y es más informativo.

![Vista de secuencias de trabajos de creación de gráficos](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

En la imagen puede ver que, cuando se habilita el registro detallado y el seguimiento para los runbooks gráficos, hay mucha más información disponible en la vista **Transmisiones de trabajo** de producción. Esta información adicional puede resultar esencial para solucionar problemas de producción con un runbook. 

Sin embargo, a menos que necesite esta información para realizar un seguimiento del progreso de un runbook y solucionar problemas, como procedimiento recomendado le conviene mantener el seguimiento desactivado. Los registros de seguimiento pueden ser particularmente numerosos. Con el seguimiento de runbooks gráficos puede obtener de dos a cuatro registros por actividad, en función de la configuración del seguimiento básico o detallado.

**Para habilitar el seguimiento en el nivel de actividad:**

1. En Azure Portal, abra su cuenta de Automation.
2. En **Automatización de procesos**, haga clic en **Runbooks** para abrir la lista de runbooks.
3. En la página Runbooks, seleccione un runbook gráfico de la lista.
4. En **Configuración**, haga clic en **registro y seguimiento**.
5. En la página Registro y seguimiento, en **Registrar registros detallados**, haga clic en **Activar** para habilitar el registro detallado.
6. En **Seguimiento a nivel de actividad**, cambie el nivel de seguimiento a **Básico** o **Detallado**, en función del nivel de seguimiento que necesite.<br>

   ![Página de registro y seguimiento de creación de gráficos](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperación de salida de runbook y mensajes en registros de Microsoft Azure Monitor

Azure Automation puede enviar el estado de un trabajo del runbook y de flujos de trabajos al área de trabajo de Log Analytics. Azure Monitor admite registros que permiten realizar las siguientes actividades:

* Obtener información sobre los trabajos de Automation.
* Desencadenar un correo electrónico o una alerta en función del estado del trabajo del runbook (por ejemplo, Error o Suspendido).
* Escribir consultas avanzadas en los flujos de trabajo.
* Correlacionar trabajos en cuentas de Automation.
* Visualizar el historial de trabajos.

Para más información sobre cómo configurar la integración con los registros de Azure Monitor para recopilar datos de trabajo, correlacionarlos y actuar sobre ellos, consulte [Reenvío del estado de un trabajo y de transmisiones de trabajos de Automation a registros de Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de la ejecución de un runbook, cómo supervisar trabajos del runbook y otros detalles técnicos, consulte [Ejecución de un runbook en Azure Automation](automation-runbook-execution.md).
* Para comprender cómo diseñar y usar runbooks secundarios, consulte [Runbooks secundarios en Azure Automation](automation-child-runbooks.md).
* Para obtener más información sobre PowerShell, incluidos los módulos de referencia de lenguaje y aprendizaje, consulte la [documentación de PowerShell](/powershell/scripting/overview).

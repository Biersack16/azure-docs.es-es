---
title: Enlace de salida de Azure Event Grid para Azure Functions
description: Aprenda a enviar un evento de Event Grid en Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 0237bcbf98578d9f83f3c9652661c786df54e73a
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627694"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Enlace de salida de Azure Event Grid para Azure Functions

Use el enlace de salida de Event Hubs para escribir eventos en un tema personalizado. Debe tener una [clave de acceso válida para el tema personalizado](../event-grid/security-authentication.md#authenticate-publishing-clients-using-sas-or-key).

Para obtener información sobre los detalles de instalación y configuración, consulte la [información general](./functions-bindings-event-grid.md).

> [!NOTE]
> El enlace de salida de Event Grid no es compatible con las firmas de acceso compartido (tokens de SAS). Debe usar la clave de acceso del tema.

> [!IMPORTANT]
> El enlace de salida de Event Grid solo está disponible para Functions 2.x y versiones posteriores.

## <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que escribe un mensaje en un tema personalizado de Event Grid usando el valor devuelto del método como resultado:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

En el siguiente ejemplo se muestra cómo usar la interfaz `IAsyncCollector` para enviar un lote de mensajes.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

En el ejemplo siguiente se muestran los datos del enlace de salida de Event Grid en el archivo *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Este es el código de script de C# que crea un evento:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Este es el código de script de C# que crea varios eventos:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

En el ejemplo siguiente se muestran los datos del enlace de salida de Event Grid en el archivo *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Este es el código de JavaScript que crea un único evento:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Este es el código de JavaScript que crea varios eventos:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

El enlace de salida de Event Grid no está disponible para Python.

# <a name="java"></a>[Java](#tab/java)

El enlace de salida Event Grid no está disponible para Java.

---

## <a name="attributes-and-annotations"></a>Atributos y anotaciones

# <a name="c"></a>[C#](#tab/csharp)

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs).

El constructor del atributo toma el nombre de un valor de la aplicación que contiene el nombre del tema personalizado y el nombre de un valor de la aplicación que contiene la clave del tema. Para obtener más información sobre estas configuraciones, vea [Salida: configuración](#configuration). Este es un ejemplo de atributo `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para ver un ejemplo completo, consulte el [ejemplo](#example).

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

El script de C# no admite atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript no admite atributos.

# <a name="python"></a>[Python](#tab/python)

El enlace de salida de Event Grid no está disponible para Python.

# <a name="java"></a>[Java](#tab/java)

El enlace de salida Event Grid no está disponible para Java.

---

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `EventGrid`.

|Propiedad de function.json | Propiedad de atributo |Descripción|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "eventGrid". |
|**direction** | N/D | Debe establecerse en "out". Este parámetro se establece automáticamente cuando se crea el enlace en Azure Portal. |
|**name** | N/D | Nombre de la variable que se usa en el código de la función que representa el evento. |
|**topicEndpointUri** |**TopicEndpointUri** | Nombre de un valor de aplicación que contiene el URI para el tema personalizado, como `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Nombre de un valor de aplicación que contiene una clave de acceso para el tema personalizado. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Asegúrese de establecer el valor de la propiedad de configuración `TopicEndpointUri` en el nombre de un valor de la aplicación que contiene el URI del tema personalizado. No especifique el URI del tema personalizado directamente en esta propiedad.

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Envíe mensajes mediante un parámetro de método, como `out EventGridEvent paramName`. Para escribir varios mensajes, puede usar `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` en lugar de `out EventGridEvent`.

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

Envíe mensajes mediante un parámetro de método, como `out EventGridEvent paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Para escribir varios mensajes, puede usar `ICollector<EventGridEvent>` o `IAsyncCollector<EventGridEvent>` en lugar de `out EventGridEvent`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acceda al evento de salida mediante `context.bindings.<name>`, donde `<name>` es el valor especificado en la propiedad `name` de *function.json*.

# <a name="python"></a>[Python](#tab/python)

El enlace de salida de Event Grid no está disponible para Python.

# <a name="java"></a>[Java](#tab/java)

El enlace de salida Event Grid no está disponible para Java.

---

## <a name="next-steps"></a>Pasos siguientes

* [Envío de un evento de Event Grid](./functions-bindings-event-grid-trigger.md)

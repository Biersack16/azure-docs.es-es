---
title: Conexión híbrida de Relay como controlador de eventos para eventos de Azure Event Grid
description: Aquí se describe cómo puede usar las conexiones híbridas de Azure Relay como controladores de eventos para eventos de Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596114"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Conexión híbrida de Relay como controlador de eventos para eventos de Azure Event Grid
Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar los eventos y **Azure Relay** es uno de ellos. 

Use las **conexiones híbridas de Azure Relay** para enviar eventos a las aplicaciones que están dentro de una red empresarial y que no tienen un punto de conexión de acceso público.

## <a name="tutorials"></a>Tutoriales
Consulte el siguiente tutorial para obtener un ejemplo del uso de una conexión híbrida de Azure Relay como un controlador de eventos. 

|Título  |Descripción  |
|---------|---------|
| [Tutorial: Envío de eventos a una conexión híbrida](custom-event-to-hybrid-connection.md) | Envía un evento personalizado a una conexión híbrida existente para su procesamiento mediante una aplicación de escucha. |

## <a name="next-steps"></a>Pasos siguientes
Consulte el artículo [Controladores de eventos](event-handlers.md) para obtener una lista de controladores de eventos compatibles. 
---
title: 'Entidad precompilada PersonName: LUIS'
titleSuffix: Azure Cognitive Services
description: Este artículo contiene información sobre la entidad pregenerada personName en Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 768c719211e8a8f2133d3798343d076e795a3da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273432"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entidad precompilada PersonName para una aplicación de LUIS
La entidad pregenerada personName detecta los nombres de personas. Dado que esta entidad ya está entrenada, no es necesario agregar expresiones de ejemplo que contengan la entidad personName para los propósitos de la aplicación. La entidad personName se admite en las [referencias culturales](luis-reference-prebuilt-entities.md) de inglés y chino.

## <a name="resolution-for-personname-entity"></a>Resolución de la entidad personName

La consulta devuelve los siguientes objetos de entidad:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Respuesta de V3](#tab/V3)


El siguiente JSON es con el parámetro `verbose` establecido en `false`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[Respuesta detallada de V3](#tab/V3-verbose)
El siguiente JSON es con el parámetro `verbose` establecido en `true`:

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Respuesta de V2](#tab/V2)

En el siguiente ejemplo, se muestra la resolución de la entidad **builtin.personName**.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Pasos siguientes

Más información acerca del [punto de conexión de predicción de V3](luis-migration-api-v3.md).

Obtenga información acerca de las entidades de [correo electrónico](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md) y [ordinal](luis-reference-prebuilt-ordinal.md).

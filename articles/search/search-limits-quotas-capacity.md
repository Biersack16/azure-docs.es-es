---
title: Límites de servicio para niveles y SKU
titleSuffix: Azure Cognitive Search
description: Límites de servicio usados en el planeamiento de la capacidad y límites máximos sobre solicitudes y respuestas de Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: b54905e201ee7a6dbf4c6837960a6e0b63057ea9
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549051"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Límites de servicio en Azure Cognitive Search

Los límites máximos sobre el almacenamiento, las cargas de trabajo, las cantidades de índices y otros objetos dependen de si se [aprovisiona Azure Cognitive Search](search-create-service-portal.md) conforme a un plan de tarifa **Gratis**, **Básico**, **Estándar** u **Optimizado para almacenamiento**.

+ **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure. Las solicitudes de indexación y consulta se ejecutan en las réplicas y las particiones que usan otros inquilinos.

+ **Básico** proporciona recursos informáticos dedicados para cargas de trabajo de producción en una escala menor, pero comparte algunas infraestructuras de red con otros inquilinos.

+ **Estándar** se ejecuta en máquinas dedicadas, con más almacenamiento y capacidad de procesamiento en cada nivel. Estándar incluye cuatro niveles: S1, S2, S3 y S3 HD.

+ El plan de tarifa **Almacenamiento optimizado** se ejecuta en máquinas dedicadas con más almacenamiento total, ancho de banda de almacenamiento y memoria que el plan **Estándar**. Dicho plan se ofrece en dos niveles: L1 y L2.

> [!NOTE]
> A partir del 1 de julio, todos los niveles tendrán disponibilidad general, incluido el nivel optimizado para almacenamiento. Puede encontrar más información en la [página de precios](https://azure.microsoft.com/pricing/details/search/).

  S3 High Density (S3 HD) está diseñado para cargas de trabajo específicas: [multiinquilino](search-modeling-multitenant-saas-applications.md) y grandes cantidades de índices pequeños (tres mil índices por servicio). Este nivel no proporciona la [característica de indexador](search-indexer-overview.md). En S3 HD, la ingesta de datos debe aprovechar el enfoque de inserción, mediante llamadas API para insertar los datos del origen en el índice. 

> [!NOTE]
> Un servicio se aprovisiona en un nivel específico. Saltar niveles para obtener capacidad implica el aprovisionamiento de un nuevo servicio (no hay ninguna actualización local). Para más información, vea [Selección de SKU o plan de tarifa](search-sku-tier.md). Para información sobre cómo ajustar la capacidad dentro de un servicio que ya ha aprovisionado, vea [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Azure Search](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Límites de suscripción
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Límites de Storage
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Límites de índice

| Resource | Gratuito | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 o 15 |50 |200 |200 |1000 GB por partición o 3000 por servicio |10 |10 |
| Campos simples máximos por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Campos de colección complejos máximos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Número máximo de elementos en todas las colecciones complejas por documento&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidad máxima de campos complejos |10 |10 |10 |10 |10 |10 |10 |10 |
| [Proveedores de sugerencias](https://docs.microsoft.com/rest/api/searchservice/suggesters) máximos por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| [Perfiles de puntuación](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) máximos por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Funciones máximas por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Los servicios básicos creados antes de diciembre de 2017 tienen límites inferiores (5 en lugar de 15) en los índices. El nivel básico es la única SKU con un límite inferior de 100 campos por índice.

<sup>2</sup> Tener muchos elementos en colecciones complejas por documento actualmente provoca un uso elevado del almacenamiento. Este es un problema conocido. Mientras tanto, un límite de 3000 es un límite superior seguro para todos los niveles de servicio. Este límite solo se aplica a las operaciones de indexación que utilizan la versión de API de disponibilidad general más temprana (GA) que admite los campos de tipo complejo (`2019-05-06`) en adelante. Para no interrumpir a los clientes que puedan estar usando versiones preliminares de la API anteriores (que admiten campos de tipo complejo), no aplicaremos este límite para las operaciones de indexación que usan estas versiones preliminares de la API. Tenga en cuenta que no está previsto que las versiones preliminares de la API se usen en escenarios de producción y se recomienda encarecidamente que los clientes pasen a la versión de API de disponibilidad general más reciente.

> [!NOTE]
> Aunque la capacidad máxima de un índice único suele estar limitada por el almacenamiento disponible, hay límites superiores máximos en el número total de documentos que se pueden almacenar en un solo índice. Este límite es de aproximadamente 24 mil millones de documentos por índice para los servicios de búsqueda Básico, S1, S2 y S3, y 2 mil millones documentos por índice para los servicios de búsqueda de S3HD. Cada elemento de una colección compleja cuenta como documentos independientes para los fines de estos límites.

<a name="document-limits"></a>

## <a name="document-limits"></a>Límites de documento 

A partir de octubre de 2018, ya no existen límites de documentos para ningún nuevo servicio creado en ningún nivel facturable (Básico, S1, S2, S3, S3 HD) en ninguna región. Los servicios antiguos creados antes de octubre del 2018 todavía pueden estar sujetos a límites en el número de documentos.

Para determinar si su servicio tiene límites de documento, use la [API de REST de estadísticas del servicio GET](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Los límites de documento se reflejan en la respuesta, con `null` indicando que no hay límites.

### <a name="document-size-limits-per-api-call"></a>Límites de tamaño de documento por llamada API

El tamaño máximo del documento cuando se llama a una API de índice es de aproximadamente 16 megabytes.

El tamaño del documento es de hecho un límite en el tamaño del cuerpo de la solicitud de la API de índice. Dado que se puede pasar un lote de varios documentos a la API de índice de una vez, el límite de tamaño depende en realidad de cuántos documentos estén en el lote. Para un lote con un único documento, el tamaño máximo del documento es de 16 MB de JSON.

Para mantener el tamaño del documento reducido, no olvide excluir los datos no consultables de la solicitud. Las imágenes y otros datos binarios no son consultables directamente y no se deben almacenar en el índice. Para integrar los datos no consultables en los resultados de búsqueda, defina un campo que admita búsqueda que almacene una referencia de dirección URL al recurso.

## <a name="indexer-limits"></a>Límites de indexador

Los tiempos de ejecución máximos existen para proporcionar equilibrio y estabilidad al servicio como un todo, pero es posible que los conjuntos de datos más grandes necesiten más tiempo de indexación que el que permite el máximo. Si un trabajo de indexación no se puede completar en el tiempo máximo permitido, intente ejecutarlo en una programación. El programador realiza un seguimiento del estado de la indexación. Si un trabajo programado de indexación se interrumpe por cualquier motivo, el indizador puede reanudarlo donde se quedó en la siguiente ejecución programada.


| Resource | Gratis&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 o 15|50 |200 |200 |N/D |10 |10 |
| Orígenes de datos máximos |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Conjuntos de habilidades máximos <sup>4</sup> |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Carga máxima de indexación por invocación |10 000 documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |N/D |Sin límite |Sin límite |
| Programación mínima | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tiempo de ejecución máximo <sup>5</sup> | 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tiempo de ejecución máximo para conocimientos adicionales de búsqueda cognitiva o indización de blobs con análisis de imágenes <sup>5</sup> | 3-10 minutos |2 horas |2 horas |2 horas |2 horas |N/D  |2 horas |2 horas |
| Indexador de blobs: tamaño máximo de blob, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador de blobs: número máximo de caracteres del contenido extraído de un blob |32 000 |64 000 |4&nbsp;millones |8&nbsp;millones |16&nbsp;millones |N/D |4&nbsp;millones |4&nbsp;millones |

<sup>1</sup> Los servicios gratuitos tienen un tiempo de ejecución máximo del indexador de 3 minutos para servicios de blob y de 1 minuto para todos los demás orígenes de datos. Para la indexación de IA que llama a Cognitive Services, los servicios gratuitos se limitan a 20 transacciones gratuitas por día, donde una transacción se define como un documento que pasa correctamente a través de la canalización enriquecimiento.

<sup>2</sup> Los servicios básicos creados antes de diciembre de 2017 tienen límites inferiores (5 en lugar de 15) en los indexadores, los orígenes de datos y los conjuntos de aptitudes.

<sup>3</sup> Los servicios S3 HD no incluyen compatibilidad con indexador.

<sup>4</sup> Máximo de 30 habilidades por conjunto de habilidades.

<sup>5</sup> Las cargas de trabajo de búsqueda cognitiva y análisis de imágenes en la indexación de blobs de Azure tienen tiempos de ejecución más cortos que la indexación de texto normal. El análisis de imágenes y el procesamiento de lenguaje natural consumen muchos recursos informáticos y una cantidad desproporcionada de la potencia de procesamiento disponible. Se ha reducido el tiempo de ejecución para que otros trabajos de la cola tengan la oportunidad de ejecutarse.  

> [!NOTE]
> Como se indica en el apartado [Límites de índice](#index-limits), los indexadores también aplicarán el límite superior de 3000 elementos en todas las colecciones complejas por documento a partir de la versión de la API de disponibilidad general más reciente que admita tipos complejos (`2019-05-06`) en adelante, lo que significa que si ha creado un indexador con una versión anterior de la API, no estará sujeto a ese límite. Para conservar la máxima compatibilidad, los indexadores que se crearon con una versión anterior de la API y, después, se actualizaron con una versión de la API `2019-05-06`, o posterior, seguirá estando **excluido** de los límites. Los clientes deben ser conscientes del impacto negativo de tener colecciones muy complejas (como se ha indicado anteriormente) y recomendamos encarecidamente que se creen todos los indexadores con la versión de la API de disponibilidad general más reciente.

## <a name="synonym-limits"></a>Límites de sinónimos

El número máximo de asignaciones de sinónimos permitidas varía según el nivel de precios. Cada regla puede tener hasta 20 expansiones, donde una expansión es un término equivalente. Por ejemplo, dado el término "gato", la asociación con "gatito", "felino" y "felis" (el género de gatos) contaría como 3 expansiones.

| Resource | Gratuito | Básica | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Asignaciones máximas de sinónimos |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de reglas por asignación |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

Cada cliente debe desarrollar las estimaciones de QPS de manera independiente. El tamaño y la complejidad del índice, el tamaño y la complejidad de la consulta, y la cantidad de tráfico son los determinantes principales de las consultas por segundo. No hay manera de ofrecer estimaciones significativas cuando estos factores son desconocidos.

Las estimaciones son más predecibles cuando se calculan en los servicios que se ejecutan en recursos dedicados (niveles Básico y Estándar). Puede calcular el número de QPS con más precisión porque tiene control sobre más parámetros. Para instrucciones sobre cómo realizar una estimación, consulte [Consideraciones sobre el rendimiento y la optimización de Azure Cognitive Search](search-performance-optimization.md).

En el caso de los niveles de Almacenamiento optimizado, debe esperar un rendimiento más bajo de las consultas y una latencia superior que en los niveles Estándar.  La metodología para calcular el rendimiento de las consultas que experimentará es igual que en los niveles Estándar.

## <a name="data-limits-ai-enrichment"></a>Límites de datos (enriquecimiento de inteligencia artificial)

Una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) que realiza llamadas a un recurso de Text Analytics para el [reconocimiento de entidades](cognitive-search-skill-entity-recognition.md), la [extracción de frases clave](cognitive-search-skill-keyphrases.md), el [análisis de sentimiento](cognitive-search-skill-sentiment.md), la [detección de idioma](cognitive-search-skill-language-detection.md) y la [detección de PII](cognitive-search-skill-pii-detection.md) está sujeta a límites de datos. El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos al analizador de opiniones, use la [aptitud División de texto](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limitaciones

Las solicitudes de indexación y de consultas de búsqueda se limitan a medida que el sistema se aproxima a la capacidad máxima. La limitación se comporta de forma diferente para las distintas API. Las API de consulta (Search/Suggest/Autocomplete) y las de indexación se limitan dinámicamente en función de la carga del servicio. Las API de indexación tienen límites estáticos de frecuencia de solicitudes. 

Límites estáticos de frecuencia de solicitudes para las operaciones relacionadas con un índice:

+ Enumerar índices (GET /indexes): 5 por segundo por unidad de búsqueda
+ Obtener índice (GET /indexes/myindex): 10 por segundo por unidad de búsqueda
+ Crear índice (POST /indexes): 12 por minuto por unidad de búsqueda
+ Crear o actualizar índice (PUT /indexes/myindex): 6 por segundo por unidad de búsqueda
+ Eliminar índice (DELETE /indexes/myindex): 12 por minuto por unidad de búsqueda 

## <a name="api-request-limits"></a>Límites de solicitud de API
* Máximo de 16 MB por solicitud <sup>1</sup>
* Longitud máxima de dirección URL de 8 KB
* Máximo de 1000 documentos por lote del índice de cargas de índices, combinaciones o eliminaciones
* Máximo de 32 campos en cláusula $orderby
* El tamaño máximo del término de búsqueda es de 32 766 bytes (32 KB menos 2 bytes) de texto con codificación UTF-8

<sup>1</sup> En Azure Cognitive Search, el cuerpo de una solicitud está sujeto a un límite superior de 16 MB, lo que impone un límite práctico sobre el contenido de campos individuales o colecciones que no están restringidos de algún modo por límites teóricos (consulte [Tipos de datos admitidos](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para más información sobre composición de campos y restricciones).

## <a name="api-response-limits"></a>Límites de respuesta de API
* Máximo de 1000 documentos devueltos por página de resultados de búsqueda
* Máximo de 100 sugerencias devueltas por solicitud de Sugerir API

## <a name="api-key-limits"></a>Límites de clave de API
Las claves de API se usan para la autenticación del servicio. Hay dos tipos. Las claves de administración se especifican en el encabezado de solicitud y conceden acceso completo de lectura y escritura al servicio. Las claves de consulta son de solo lectura, se especifican en la dirección URL y normalmente se distribuyen a las aplicaciones cliente.

* Máximo de 2 claves de administración por servicio
* Máximo de 50 claves de consultas por servicio
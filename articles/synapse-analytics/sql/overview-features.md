---
title: Diferencias entre las características de T-SQL en Azure Synapse SQL
description: Lista de las características de Transact-SQL que están disponibles en Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421289"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Características de Transact-SQL compatibles en Azure Synapse SQL

Azure Synapse SQL es un servicio de análisis de macrodatos que permite consultar y analizar los datos mediante el lenguaje T-SQL. Puede usar el dialecto estándar compatible con ANSI del lenguaje SQL que se usa en SQL Server y Azure SQL Database para el análisis de datos. 

El lenguaje Transact-SQL que se usa en el modelo sin servidor y aprovisionado de Synapse SQL puede hacer referencia a objetos diferentes y tiene algunas diferencias en el conjunto de características admitidas. En esta página, puede encontrar diferencias de alto nivel en el lenguaje Transact-SQL entre los modelos de consumo de Synapse SQL.

## <a name="database-objects"></a>Objetos de base de datos

Los modelos de consumo de Synapse SQL permiten usar diferentes objetos de base de datos. En la siguiente tabla se muestra una comparación de los distintos tipos de objetos admitidos:

|   | aprovisionado | Sin servidor |
| --- | --- | --- |
| Tablas | [Sí](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No, el modelo sin servidor solo puede consultar datos externos colocados en [Azure Storage](#storage-options) |
| Vistas | [Sí](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Las vistas pueden utilizar [elementos del lenguaje de consulta](#query-language) que están disponibles en el modelo aprovisionado. | [Sí](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Las vistas pueden utilizar [elementos del lenguaje de consulta](#query-language) que están disponibles en el modelo sin servidor. |
| Esquemas | [Sí](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sí](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Tablas temporales | [Sí](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | No |
| Procedimientos | [Sí](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| Functions | [Sí](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| Desencadenadores | No | No |
| Tablas externas | [Sí](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Consulte los [formatos de datos](#data-formats) compatibles. | [Sí](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Consulte los [formatos de datos](#data-formats) compatibles. |
| Almacenamiento en caché de consultas | Sí, varios formularios (almacenamiento en caché basado en SSD, en memoria, almacenamiento en caché de un conjunto de resultados). Además, se admite la vista materializada | No |
| Variables de tabla | [No](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), se usan tablas temporales | No |

## <a name="query-language"></a>Lenguaje de consulta

Los lenguajes de consulta que se usan en Synapse SQL pueden tener diferentes características admitidas según el modelo de consumo. En la tabla siguiente se describen las diferencias más importantes del lenguaje de consulta en los dialectos de Transact-SQL:

|   | aprovisionado | Sin servidor |
| --- | --- | --- |
| Instrucción SELECT | Sí. No se admiten las cláusulas de consulta de Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Sí. No se admiten las cláusulas de consulta de Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), ni las sugerencias de consulta. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) y [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) solo se pueden usar para consultar datos en tablas temporales (datos no externos). |
| Instrucción INSERT | Sí | No |
| Instrucción UPDATE | Sí | No |
| Instrucción DELETE | Sí | No |
| Instrucción MERGE | Sí | No |
| Carga de datos | Sí. La utilidad preferida es la instrucción [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), pero el sistema admite tanto la carga masiva (BCP) como el uso de [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para la carga de datos. | No |
| Exportación de datos | Sí. Usar [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Sí. Usar [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Tipos | Sí, todos los tipos de Transact-SQL excepto [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text, and image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí, todos los tipos de Transact-SQL excepto [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text, and image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y Table. |
| Consultas entre bases de datos | No | Sí, incluida la instrucción [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Funciones integradas (análisis) | Sí, todas las funciones [analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), de conversión, [de fecha y hora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), lógicas y [matemáticas](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) de Transact-SQL, excepto [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí, todas las funciones [analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), de conversión, [de fecha y hora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), lógicas y [matemáticas](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) de Transact-SQL. |
| Funciones integradas (texto) | Sí. Todas las funciones de [cadena](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e intercalación de Transact-SQL, excepto [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí. Todas las funciones de [cadena](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y de intercalación de Transact-SQL. |
| Funciones integradas de valores de tabla | Sí, las [funciones de conjunto de filas de Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), excepto [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí, las [funciones de conjunto de filas de Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), excepto [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Agregados |  Agregados integrados de Transact-SQL, excepto [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Agregados integrados de Transact-SQL excepto [STRING\_AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operadores | Sí, todos los [operadores de Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), excepto [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí, todos los [operadores de Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Control de flujo | Sí. Todas las [instrucciones del lenguaje de control de flujo de Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), excepto [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí. Todas las [instrucciones de control de flujo de Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), excepto [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) y SELECT consulta en la condición `WHILE (...)` |
| Instrucciones de DDL (CREATE, ALTER, DROP) | Sí. Todas las instrucciones de DDL de Transact-SQL aplicables a los tipos de objeto admitidos | Sí. Todas las instrucciones de DDL de Transact-SQL aplicables a los tipos de objeto admitidos |

## <a name="security"></a>Seguridad

Synapse SQL permite usar características de seguridad integradas para proteger los datos y controlar el acceso. En la siguiente tabla se comparan las diferencias de alto nivel que hay entre los modelos de consumo de Synapse SQL.

|   | aprovisionado | Sin servidor |
| --- | --- | --- |
| Inicios de sesión | N/D (en las bases de datos solo se admiten los usuarios contenidos) | Sí |
| Usuarios |  N/D (en las bases de datos solo se admiten los usuarios contenidos) | Sí |
| [Usuarios contenidos](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí. **Nota:** No puede haber más de un usuario de AAD que sea administrador sin restricciones | Sí |
| Autenticación de Azure Active Directory (AAD) | Sí, los usuarios de AAD | Sí, los inicios de sesión y usuarios de AAD |
| Autenticación de paso a través de AAD de Storage | Sí | Sí |
| Autenticación de token de SAS de Storage | No | Sí, mediante [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) o [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en el nivel de instancia. |
| Autenticación de la clave de acceso de almacenamiento | Si, mediante [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| Autenticación de identidad administrada de Storage | Sí, mediante la [credencial de la identidad de servicio administrado](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí, mediante la credencial de `Managed Identity`. |
| Autenticación de identidad de la aplicación de almacenamiento | [Sí](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| Permisos: nivel de objeto | Sí, lo que incluye la capacidad para conceder, denegar y revocar permisos a los usuarios mediante GRANT, DENY y REVOKE | Sí, lo que incluye la capacidad para conceder, denegar y revocar permisos a los usuarios o inicios de sesión en los objetos del sistema que se admiten mediante GRANT, DENY y REVOKE |
| Permisos: nivel de esquema | Sí, lo que incluye la capacidad para conceder, denegar y revocar permisos a los usuarios o inicios de sesión en el esquema mediante GRANT, DENY y REVOKE | Sí, lo que incluye la capacidad para conceder, denegar y revocar permisos a los usuarios o inicios de sesión en el esquema mediante GRANT, DENY y REVOKE |
| Permisos: [nivel de base de datos](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sí | Sí |
| Permisos: [nivel de servidor](/sql/relational-databases/security/authentication-access/server-level-roles) | No | Sí, se admiten sysadmin y otros roles de servidor |
| Roles y grupos | Sí (ámbito de base de datos) | Sí (ámbito de servidor y base de datos) |
| Funciones de identidad y seguridad | Algunos operadores y funciones de seguridad de Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Algunos operadores y funciones de seguridad de Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` y `REVERT`. Las funciones de seguridad no se pueden usar para consultar datos externos (almacenamiento del resultado en una variable que se puede usar en la consulta).  |
| DATABASE SCOPED CREDENTIAL | Sí | Sí |

Tanto el grupo de SQL como SQL a petición usan el lenguaje Transact-SQL estándar para realizar consultas en los datos. Para ver las diferencias al detalle, consulte la [referencia del lenguaje Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Herramientas

Puede usar varias herramientas para conectarse a Synapse SQL para consultar datos.

|   | aprovisionado | Sin servidor |
| --- | --- | --- |
| Synapse Studio | Sí, scripts de SQL | Sí, scripts de SQL |
| Power BI | Sí | [Sí](tutorial-connect-power-bi-desktop.md) |
| Azure Analysis Service | Sí | Sí |
| Azure Data Studio | Sí | Sí, la versión 1.14, o cualquier versión superior. Se admiten scripts de SQL y cuadernos de SQL. |
| SQL Server Management Studio | Sí | Sí, la versión 18.4, o cualquier versión superior. |

La mayoría de las aplicaciones que usan el lenguaje Transact-SQL estándar pueden consultar modelos de consumo tanto aprovisionados como sin servidor de Synapse SQL.

## <a name="storage-options"></a>Opciones de almacenamiento

Los datos que se analizan se pueden guardar en varios tipos de almacenamiento. En la siguiente tabla se enumeran todas las opciones de almacenamiento disponibles:

|   | aprovisionado | Sin servidor |
| --- | --- | --- |
| Almacenamiento interno | Sí | No |
| Azure Data Lake v2 | Sí | Sí |
| Azure Blob Storage | Sí | Sí |

## <a name="data-formats"></a>Formatos de datos

Los datos que se analizan se pueden guardar en varios formatos de almacenamiento. En la tabla siguiente se enumeran todos los formatos de datos disponibles que se pueden analizar:

|   | aprovisionado | Sin servidor |
| --- | --- | --- |
| Delimitado | [Sí](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sí](query-single-csv-file.md) |
| CSV | Sí (no se admiten delimitadores con varios caracteres) | [Sí](query-single-csv-file.md) |
| Parquet | [Sí](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sí](query-parquet-files.md), incluidos los archivos con [tipos anidados](query-parquet-nested-types.md) |
| Hive ORC | [Sí](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| RC de Hive | [Sí](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | No |
| JSON | Sí | [Sí](query-json-files.md) |
| [Delta-lake](https://delta.io/) | No | No |
| [CDM](https://docs.microsoft.com/common-data-model/) | No | No |

## <a name="next-steps"></a>Pasos siguientes
En los siguientes artículos puede encontrar más información sobre los procedimientos recomendados para el grupo de SQL y SQL a petición:

- [Procedimientos recomendados para grupos de SQL](best-practices-sql-pool.md)
- [Procedimientos recomendados para SQL a petición](best-practices-sql-on-demand.md)
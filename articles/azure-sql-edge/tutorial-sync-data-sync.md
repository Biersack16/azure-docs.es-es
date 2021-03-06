---
title: Sincronización de datos desde Azure SQL Edge (versión preliminar) mediante SQL Data Sync
description: Información sobre la sincronización de datos desde Azure SQL Edge (versión preliminar) mediante Azure SQL Data Sync
keywords: SQL Edge, sincronización de datos desde SQL Edge, sincronización de datos de SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7971681c3f0c99a11567e6a30e61167c5d42348c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680515"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutorial: Sincronización de datos desde SQL Edge a Azure SQL Database mediante SQL Data Sync

En este tutorial, aprenderá a usar un *grupo de sincronización* de Azure SQL Data Sync para sincronizar incrementalmente los datos de Azure SQL Edge con Azure SQL Database. SQL Data Sync es un servicio basado en Azure SQL Database que permite sincronizar los datos seleccionados de manera bidireccional entre varias bases de datos SQL e instancias de SQL Server. Para más información sobre SQL Data Sync, consulte [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Dado que SQL Edge se basa en las versiones más recientes del [Motor de base de datos de SQL Server](/sql/sql-server/sql-server-technical-documentation/), cualquier mecanismo de sincronización de datos que sea aplicable a una instancia de SQL Server local también se puede usar para sincronizar datos con la instancia de SQL Edge que se ejecute en un dispositivo perimetral.

## <a name="prerequisites"></a>Prerrequisitos

Para este tutorial se requiere un equipo Windows configurado con [Data Sync Agent para Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Antes de empezar

* Cree una base de datos de Azure SQL. Para obtener información sobre cómo crear una base de datos de Azure SQL desde Azure Portal, consulte [Creación de una base de datos única en Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Cree las tablas y otros objetos necesarios en la implementación de Azure SQL Database.

* Cree las tablas y objetos necesarios en la implementación de Azure SQL Edge. Para más información, vea [Uso de paquetes DAC de SQL Database con SQL Edge](deploy-dacpac.md).

* Registre la instancia de Azure SQL Edge con Data Sync Agent para Azure SQL Data Sync. Para más información, consulte [Incorporación de una base de datos local de SQL Server](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Sincronización de datos entre una base de datos de Azure SQL y SQL Edge

La configuración de la sincronización entre una base de datos de Azure SQL y una instancia de SQL Edge mediante SQL Data Sync implica tres pasos clave:  

1. Use Azure Portal para crear un grupo de sincronización. Para más información, consulte [Creación de un área de trabajo](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Puede usar una sola base de datos *Centro* para crear varios grupos de sincronización para sincronizar datos de distintas instancias de SQL Edge con una o varias bases de datos SQL en Azure.

2. Agregue miembros de sincronización al grupo de sincronización. Para más información, consulte [Adición de miembros de sincronización](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configure el grupo de sincronización para seleccionar las tablas que formarán parte de la sincronización. Para más información, consulte [Configuración de un grupo de sincronización](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Después de completar los pasos anteriores, tendrá un grupo de sincronización que incluye una base de datos de Azure SQL y una instancia de SQL Edge.

Para más información sobre SQL Data Sync, consulte estos artículos:

* [Data Sync Agent para Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Procedimientos recomendados](../sql-database/sql-database-best-practices-data-sync.md) y [Solución de problemas de Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Monitor SQL Data Sync with Azure Monitor logs](../sql-database/sql-database-sync-monitor-oms.md) (Supervisión de SQL Data Sync con registros de Azure Monitor)

* [Actualización del esquema de sincronización con Transact-SQL](../sql-database/sql-database-update-sync-schema.md) o [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Pasos siguientes

* [Uso de PowerShell para sincronizar entre Azure SQL Database y Azure SQL Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). En el tutorial, reemplace los detalles de la base de datos `OnPremiseServer` por los detalles de Azure SQL Edge.

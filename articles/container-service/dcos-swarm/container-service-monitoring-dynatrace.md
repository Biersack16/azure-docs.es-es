---
title: '(EN DESUSO) Supervisión de un clúster de Azure DC/OS: Dynatrace'
description: Supervisión de un clúster de Azure Container Service DC/OS con Dynatrace. Implemente Dynatrace OneAgent mediante el panel DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: ab6bb116c93aad8501da21dc5688d7e39f4195fe
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166196"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(EN DESUSO) Supervisión de un clúster DC/OS de Azure Container Service con Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

En este artículo, le mostramos cómo implementar [Dynatrace](https://www.dynatrace.com/) OneAgent para supervisar todos los nodos de agente en el clúster de Azure Container Service. Necesita una cuenta con Dynatrace SaaS/Managed para esta configuración. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace es una solución de supervisión nativa en la nube para entornos de clúster y contenedor muy dinámicos. Permite optimizar mejor las implementaciones de contenedor y las asignaciones de memoria mediante el uso de datos de uso en tiempo real. Permite indicar automáticamente los problemas de infraestructura y aplicación proporcionando una línea de base automatizada, la correlación de problemas y la detección de la causa raíz.

La figura siguiente muestra la interfaz de usuario de Dynatrace:

![Interfaz de usuario de Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Prerrequisitos 
[Implemente](container-service-deployment.md) y [conecte](./../container-service-connect.md) un clúster configurado por Azure Container Service. Explore la [interfaz de usuario de Marathon](container-service-mesos-marathon-ui.md). Vaya a [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) para configurar una cuenta de Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configuración de una implementación de Dynatrace con Marathon
Estos pasos muestran cómo configurar e implementar aplicaciones de Dynatrace en su clúster con Marathon.

1. Acceda a la interfaz de usuario de DC/OS mediante `http://localhost:80/`. Una vez en la interfaz de usuario de DC/OS, navegue hasta la pestaña **Universe** (Universo) y, a continuación, busque **Dynatrace**.

    ![Dynatrace en Universe (Universo) en DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para completar la configuración, necesita una cuenta de Dynatrace SaaS o una cuenta de evaluación gratuita. Una vez que haya iniciado sesión en el panel de Dynatrace, seleccione **Implementar Dynatrace**.

    ![Integración de PaaS de configuración de Dynatrace](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. En la página, seleccione **Configurar integración de PaaS**. 

    ![Token de la API de Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Escriba el token de la API en la configuración de Dynatrace OneAgent dentro de Universe (Universo) en DC/OS. 

    ![Configuración de Dynatrace OneAgent en Universe (Universo) en DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Establezca las instancias en el número de nodos que vaya a ejecutar. Establecer un número mayor también funciona, pero DC/OS seguirá intentando buscar nuevas instancias hasta que realmente se alcance ese número. Si lo prefiere, también puede establecerlo en un valor como 1000000. En este caso, cada vez que se agregue un nuevo nodo al clúster, Dynatrace implementará automáticamente un agente en ese nodo nuevo, a costa de que DC/OS intente constantemente implementar instancias adicionales.

    ![Configuración de Dynatrace en instancias de Universe en DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Pasos siguientes

Una vez haya instalado el paquete, desplácese hacia el panel de Dynatrace. Puede explorar distintas métricas de uso de los contenedores en el clúster. 

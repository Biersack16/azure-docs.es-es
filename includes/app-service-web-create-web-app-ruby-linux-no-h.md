---
title: archivo de inclusión
description: archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/10/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 01c1c7c0bdf8e55b002b73682407a3e03ee30b8f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085794"
---
Cree una [aplicación web](../articles/app-service/containers/app-service-linux-intro.md) en el plan de App Service de `myAppServicePlan`. 

En Cloud Shell, puede usar el comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest). En el siguiente ejemplo, reemplace `<app-name>` por un nombre único global de aplicación (los caracteres válidos son `a-z`, `0-9` y `-`). El tiempo de ejecución se establece en `RUBY|2.3`. Para ver todos los entornos en tiempo de ejecución admitidos, ejecute [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "RUBY|2.6.2" --deployment-local-git
```

Cuando se haya creado la aplicación web, la CLI de Azure mostrará información similar a la del ejemplo siguiente:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Ha creado una nueva aplicación web vacía, con la implementación de Git habilitada.

> [!NOTE]
> La dirección URL del repositorio de Git remoto se muestra en la propiedad `deploymentLocalGitUrl`, con el formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Guarde esta dirección URL, ya que la necesitará más adelante.
>

---
title: Seguimiento de MLflow para experimentos de Machine Learning
titleSuffix: Azure Machine Learning
description: Configure MLflow con Azure Machine Learning para registrar métricas y artefactos de los modelos de Machine Learning creados en clústeres de Databricks, el entorno local o el entorno de máquinas virtuales.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779116"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Seguimiento de métricas de modelos con MLflow y Azure Machine Learning (versión preliminar)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

En este artículo se muestra cómo habilitar el identificador URI de seguimiento y la API de registro de MLflow, que en conjunto se conocen como [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), para conectar experimentos de MLflow y Azure Machine Learning. Esto le permite realizar un seguimiento las métricas y los artefactos de los experimentos, así como registrarlos, en el [área de trabajo de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Si ya usa Seguimiento de MLflow para los experimentos, el área de trabajo proporciona una ubicación centralizada, segura y escalable para almacenar los modelos y las métricas de entrenamiento.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) es una biblioteca de código abierto para administrar el ciclo de vida de los experimentos de aprendizaje automático. MLFlow Tracking es un componente de MLflow que lleva a cabo un registro y un seguimiento de las métricas de ejecución de entrenamiento y de los artefactos del modelo, independientemente del entorno del experimento (localmente en su equipo, en un destino de proceso remoto, en una máquina virtual o en un clúster de Azure Databricks). 

En el siguiente diagrama se ilustra que con Seguimiento de MLflow, se realiza un seguimiento de las métricas de ejecución de un experimento y se almacenan los artefactos del modelo en el área de trabajo de Azure Machine Learning.

![Diagrama de MLflow con Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> La información de este documento va destinada principalmente a aquellos científicos de datos y desarrolladores que deseen supervisar el proceso de entrenamiento del modelo. Los administradores que estén interesados en la supervisión del uso de recursos y eventos desde Azure Machine Learning, como cuotas, ejecuciones de entrenamiento completadas o implementaciones de modelos completadas pueden consultar [Supervisión de Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparación entre los clientes de MLflow y Azure Machine Learning

 En la tabla siguiente se resumen los distintos clientes que pueden usar Azure Machine Learning y sus respectivas funcionalidades.

 El Seguimiento de MLflow ofrece funciones de registro de métricas y almacenamiento de artefactos que de otra manera solo están disponibles a través del [SDK de Python de Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | MLflow&nbsp;Tracking <!--& Deployment--> | SDK de Python de Azure Machine Learning |  CLI de Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Administración del área de trabajo |   | ✓ | ✓ | ✓ |
| Uso de almacenes de datos  |   | ✓ | ✓ | |
| Métricas de registro      | ✓ | ✓ |   | |
| Carga de artefactos | ✓ | ✓ |   | |
| Visualización de métricas     | ✓ | ✓ | ✓ | ✓ |
| Administración de procesos   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Prerequisites

* [Instale MLflow](https://mlflow.org/docs/latest/quickstart.html).
* [Instale el SDK de Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) en el equipo local. El SDK proporciona la conectividad de MLflow para acceder al área de trabajo.
* [Cree un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Seguimiento de ejecuciones locales

El seguimiento de MLflow con Azure Machine Learning le permite almacenar las métricas y los artefactos registrados desde las ejecuciones locales en el área de trabajo de Azure Machine Learning.

Instale el paquete `azureml-mlflow` que va a usar el Seguimiento de MLflow con Azure Machine Learning en los experimentos ejecutados localmente en un editor de código o Jupyter Notebook.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>El espacio de nombres azureml.contrib cambia con frecuencia, ya que estamos trabajando para mejorar el servicio. Por lo tanto, todo el contenido de este espacio de nombres debe considerarse como una versión preliminar, no completamente compatible con Microsoft.

Importe las clases `mlflow` y [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) para acceder al URI de seguimiento de MLflow y configurar el área de trabajo.

En el código siguiente, el método `get_mlflow_tracking_uri()` asigna un dirección URI de seguimiento única al área de trabajo (`ws`) y `set_tracking_uri()` apunta el URI de seguimiento de MLflow a esa dirección.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>El URI de seguimiento es válido hasta una hora como máximo. Si reinicia el script después de un tiempo de inactividad, use la API get_mlflow_tracking_uri para obtener un nuevo URI.

Establezca el nombre del experimento de MLflow con `set_experiment()` y comience la ejecución de entrenamiento con `start_run()`. Después, use `log_metric()` para activar la API de registro de MLflow y empezar a registrar las métricas de la ejecución de entrenamiento.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Seguimiento de ejecuciones remotas

El seguimiento de MLflow con Azure Machine Learning le permite almacenar las métricas y los artefactos registrados desde las ejecuciones remotas en el área de trabajo de Azure Machine Learning.

Las ejecuciones remotas permiten entrenar modelos en procesos más eficaces, como máquinas virtuales habilitadas para GPU o clústeres de Proceso de Machine Learning. Consulte [Configuración de destinos de proceso del entrenamiento del modelo](how-to-set-up-training-targets.md) para obtener información sobre las distintas opciones de proceso.

Configure el proceso y el entorno de ejecución de entrenamiento con la clase [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Incluya paquetes de PIP `mlflow` y `azureml-mlflow` en la sección [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) del entorno. Después, construya [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) con el proceso remoto como destino de proceso.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

En el script de entrenamiento, importe `mlflow` para usar las API de registro de MLflow y empiece a registrar las métricas de ejecución.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Con esta configuración del proceso y de la ejecución de entrenamiento, use el método `Experiment.submit('train.py')` para enviar una ejecución. Este método establece automáticamente el identificador URI de seguimiento de MLflow y dirige el registro de MLflow al área de trabajo.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Seguimiento de las ejecuciones de Azure Databricks

MLflow Tracking con Azure Machine Learning permite almacenar las métricas y los artefactos de las ejecuciones de Azure Databricks en el área de trabajo de Azure Machine Learning.

Para ejecutar los experimentos de Mlflow con Azure Databricks, primero debe crear un [área de trabajo y un clúster de Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). En su clúster, compruebe que instala la biblioteca *azureml-mlflow* desde PyPi para asegurarse de que el clúster tiene acceso a las funciones y clases necesarias.

Una vez hecho esto, importe su cuaderno de experimentos, adjúntelo a su clúster de Azure Databricks y ejecute el experimento. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas en el clúster, vaya a la pestaña **Bibliotecas** y haga clic en **Instalar nuevo**.

 ![Diagrama de MLflow con Azure Machine Learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

En el campo **Paquete**, escriba azureml-mlflow y, a continuación, haga clic en instalar. Repita este paso según sea necesario para instalar otros paquetes adicionales en el clúster para el experimento.

 ![Diagrama de MLflow con Azure Machine Learning](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configuración del cuaderno y del área de trabajo

Una vez haya configurado el clúster, importe el cuaderno del experimento, ábralo y asóciele el clúster.

El código siguiente debe estar en el cuaderno del experimento. Este código obtiene los detalles de la suscripción a Azure para crear una instancia del área de trabajo. En este código se supone que tiene un grupo de recursos y un área de trabajo de Azure Machine Learning. Si no es así, puede [crearlos](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conectar las áreas de trabajo de Azure Databricks y Azure Machine Learning

En [Azure Portal](https://ms.portal.azure.com), puede vincular su área de trabajo de Azure Databricks (ADB) a un área de trabajo de Azure Machine Learning nueva o existente. Para ello, vaya al área de trabajo de ADB y seleccione **Vincular el área de trabajo de Azure Machine Learning**  que está en la parte inferior derecha. Al vincular las áreas de trabajo, podrá realizar un seguimiento de los datos del experimento en el área de trabajo Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Vinculación del seguimiento de MLflow al área de trabajo

Una vez cree la instancia de su área de trabajo, establezca el URI de seguimiento de MLflow. Al hacerlo, vincula el seguimiento de MLflow al área de trabajo de Azure Machine Learning. Después de la vinculación, todos los experimentos se redirigirán al servicio de seguimiento administrado de Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Establecer directamente el seguimiento de MLflow en el cuaderno

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

En su script de entrenamiento, importe mlflow para usar las API de registro de MLflow y empiece a registrar las métricas de ejecución. En el ejemplo siguiente, se registra la métrica de pérdida de tiempo. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizar el seguimiento de la configuración de MLflow

En lugar de configurar manualmente el URI de seguimiento en cada sesión del cuaderno del experimento posterior en los clústeres, hágalo automáticamente mediante este [script de inicio del clúster de seguimiento de Azure Machine Learning ](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Cuando se configura correctamente, puede ver los datos de seguimiento de MLflow en la API de REST de Azure Machine Learning, en todos los clientes y en Azure Databricks a través de la interfaz de usuario de MLflow o mediante el cliente de MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Visualización de las métricas y los artefactos en el área de trabajo

Las métricas y los artefactos procedentes del registro de MLflow se conservan en el área de trabajo. Para verlos en cualquier momento, vaya al área de trabajo y busque el experimento por su nombre en el área de trabajo en [Azure Machine Learning Studio](https://ml.azure.com).  O bien, ejecute el código siguiente: 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Cuadernos de ejemplo

En [MLflow con cuadernos de Azure ML](https://aka.ms/azureml-mlflow-examples) se demuestran y se analizan con mayor profundidad los conceptos presentados en este artículo.

## <a name="next-steps"></a>Pasos siguientes
* [Administra sus modelos](concept-model-management-and-deployment.md).
* Supervise los modelos de producción para el [desfase de datos](how-to-monitor-data-drift.md).

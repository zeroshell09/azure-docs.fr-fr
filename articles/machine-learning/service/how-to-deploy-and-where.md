---
title: Comment et où déployer des modèles ?
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment et où déployer vos modèles de service Azure Machine Learning, notamment : Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge et FPGA (Field-Programmable Gate Arrays).'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 14ced5ed45bcc91e6b6c812f2d1cbb61e139cc4f
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278951"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Déployer des modèles avec le service Azure Machine Learning

Découvrez comment déployer votre modèle Machine Learning en tant que service web dans le cloud Azure ou sur des appareils IoT Edge.

Le workflow est similaire quel que soit l’endroit [où vous déployez](#target) votre modèle :

1. Inscrire le modèle.
1. Préparer le déploiement (spécifier les ressources, l’utilisation et la cible de calcul).
1. Déployer le modèle sur la cible de calcul.
1. Tester le modèle déployé, également appelé service web.

Pour plus d’informations sur les concepts impliqués dans le workflow de déploiement, consultez [Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prérequis

- Un espace de travail de service Microsoft Azure Machine Learning. Pour plus d’informations, consultez [Créer un espace de travail Azure Machine Learning service](how-to-manage-workspace.md).

- Un modèle Si vous n’avez pas de modèle entraîné, vous pouvez utiliser les fichiers de modèle et de dépendance fournis dans [ce tutoriel](https://aka.ms/azml-deploy-cloud).

- L’[extension Azure CLI pour Machine Learning service](reference-azure-machine-learning-cli.md), le [SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou l’[extension Azure Machine Learning pour Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Se connecter à un espace de travail

Le code suivant montre comment se connecter à un espace de travail de service Azure Machine Learning service à l’aide des informations mises en cache dans l’environnement de développement local :

**Avec le kit SDK**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Pour plus d’informations sur l’utilisation du Kit de développement logiciel (SDK) pour se connecter à un espace de travail, consultez le [Kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

**Avec l’interface CLI**

Quand vous utilisez l’interface CLI, utilisez le paramètre `-w` ou `--workspace-name` pour spécifier l’espace de travail de la commande.

**Avec VS Code**

Lorsque vous utilisez VS Code, l’espace de travail est sélectionné à l’aide d’une interface graphique. Pour plus d’informations, consultez [Déployer et gérer des modèles](how-to-vscode-tools.md#deploy-and-manage-models) dans la documentation relative à l’extension VS Code.

## <a id="registermodel"></a> Inscrire votre modèle

Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous disposez d'un modèle stocké dans plusieurs fichiers, vous pouvez enregistrer ces derniers en tant que modèle unique dans l'espace de travail. Après l’inscription, vous pouvez ensuite télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.

> [!TIP]
> Lors de l’inscription d’un modèle, vous fournissez soit un chemin d’accès à un emplacement cloud (à partir d’une exécution de formation), soit un répertoire local. Ce chemin d’accès sert uniquement à localiser les fichiers à charger dans le cadre du processus d’inscription. Il n’est pas nécessaire qu’il corresponde au chemin d’accès utilisé dans le script d’entrée. Pour plus d’informations, consultez [Présentation de get_model_path](#what-is-get_model_path).

Les modèles Machine Learning sont inscrits dans votre espace de travail Azure Machine Learning. Le modèle peut provenir d’Azure Machine Learning ou d’un autre emplacement. Les exemples suivants montrent comment inscrire un modèle :

### <a name="register-a-model-from-an-experiment-run"></a>Inscrire un modèle à partir d’une exécution d’expérience

Les extraits de code de cette section montrent comment inscrire un modèle à partir d’une exécution d’entraînement :

> [!IMPORTANT]
> Ces extraits de code partent du principe que vous avez déjà effectué une exécution d’entraînement et que vous avez accès à l’objet `run` (exemple du kit de développement logiciel) ou à la valeur de l’ID d’exécution (exemple de CLI). Pour plus d’informations sur les modèles d’entraînement, consultez [Créer et utiliser des cibles de calcul pour l’entraînement du modèle](how-to-set-up-training-targets.md).

+ **Avec le kit SDK**

  Lorsque vous utilisez le kit de développement logiciel (SDK) pour effectuer l’apprentissage d’un modèle, vous pouvez recevoir un objet [Run](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) ou [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master), en fonction de la façon dont vous effectuez l’apprentissage. Chaque objet peut être utilisé pour inscrire un modèle créé par un exécution d’expérimentation.

  + Inscrire un modèle à partir d’un objet `azureml.core.Run` :
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Le `model_path` fait référence à l’emplacement cloud du modèle. Dans cet exemple, le chemin d’accès à un fichier unique est utilisé. Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` dans le répertoire contenant les fichiers. Pour plus de détails, voir les informations de référence sur [Run.register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-).

  + Inscrire un modèle à partir d’un objet `azureml.train.automl.run.AutoMLRun` :

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    Dans cet exemple, les paramètres `metric` et `iteration` ne sont pas spécifiés, ce qui a pour effet que l’itération avec la meilleure métrique principale est inscrite. La valeur `model_id` retournée par l’exécution est utilisée à la place d’un nom de modèle.

    Pour plus de détails, voir les informations de référence sur [AutoMLRun.register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-).

+ **Avec l’interface CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  Le `--asset-path` fait référence à l’emplacement cloud du modèle. Dans cet exemple, le chemin d’accès à un fichier unique est utilisé. Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `--asset-path` dans le répertoire contenant les fichiers.

+ **Avec VS Code**

  Utilisez l’extension [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) pour inscrire des modèles à l’aide de fichiers ou dossiers de modèles.

### <a name="register-a-model-from-a-local-file"></a>Inscrire un modèle à partir d’un fichier local

Vous pouvez inscrire un modèle en indiquant le **chemin local** du modèle. Vous pouvez fournir un dossier ou un seul fichier. Vous pouvez utiliser cette méthode pour inscrire les modèles entraînés avec le service Azure Machine Learning service et ceux téléchargés, ou entraînés en dehors d’Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Exemple ONNX avec le SDK Python :**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `model_path` dans le répertoire contenant les fichiers.

+ **Avec l’interface CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Pour inclure plusieurs fichiers dans l’inscription du modèle, définissez `-p` dans le répertoire contenant les fichiers.

**Durée estimée** : Environ 10 secondes.

Pour plus d'informations, consultez la documentation de référence de la classe [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Pour plus d’informations sur l’utilisation de modèles formés en dehors du service Azure Machine Learning, consultez [Déployer un modèle existant](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Choisir une cible de calcul

Les cibles de calcul (ou ressources de calcul) suivantes peuvent héberger votre déploiement de service web. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Préparer au déploiement

Le déploiement du modèle requiert ce qui suit :

* Un __script d'entrée__. Ce script accepte les requêtes, évalue la requête à l’aide du modèle et renvoie les résultats.

    > [!IMPORTANT]
    > Le script d’entrée est spécifique à votre modèle. Il doit comprendre le format des données de la requête entrante, le format des données attendues par votre modèle et le format des données renvoyées aux clients.
    >
    > Si le format des données de la requête n'est pas utilisable par votre modèle, le script peut les convertir à un format acceptable. Il peut également transformer la réponse avant de la renvoyer au client.

    > [!IMPORTANT]
    > Le Kit de développement logiciel (SDK) Azure Machine Learning n’offre aucun moyen pour les déploiements de service Web ou de IoT Edge d’accéder à votre magasin de données ou à vos jeux de données. Si vous avez besoin du modèle déployé pour accéder aux données stockées en dehors du déploiement, comme dans un compte de stockage Azure, vous devez développer une solution de code personnalisée à l’aide du Kit de développement logiciel (SDK) approprié. Exemple : [Kit de développement logiciel (SDK) Stockage Azure pour Python](https://github.com/Azure/azure-storage-python).
    >
    > Une autre solution possible pour votre scénario consiste à utiliser les [prédictions par lots](how-to-run-batch-predictions.md), qui donnent accès aux magasins de travail lors du scoring.

* **Dépendances**, comme les scripts d'assistance ou les packages Python/Conda nécessaires à l'exécution du script d'entrée ou du modèle

* __Configuration de déploiement__ de la cible de calcul qui héberge le modèle déployé. Cette configuration décrit notamment les besoins en mémoire et en ressources CPU pour exécuter le modèle.

Ces entités sont encapsulées dans une __configuration d'inférence__ et une __configuration de déploiement__. La configuration d'inférence référence le script d'entrée et d'autres dépendances. Ces configurations sont définies par programmation lors de l'utilisation du kit de développement logiciel (SDK) et sous forme de fichiers JSON lors de l'utilisation de l'interface CLI pour procéder au déploiement.

### <a id="script"></a> 1. Définir votre script d’entrée et les dépendances

Le script d’entrée reçoit les données envoyées à un service web déployé, puis les passe au modèle. Ensuite, il prend la réponse retournée par le modèle et la retourne au client. **Le script est propre à votre modèle**. Il doit comprendre les données que le modèle attend et retourne.

Le script contient deux fonctions qui chargent et exécutent le modèle :

* `init()`: en général, cette fonction charge le modèle dans un objet global. Cette fonction est exécutée une seule fois lors du démarrage du conteneur Docker de votre service web.

* `run(input_data)`: cette fonction utilise le modèle pour prédire une valeur basée sur les données d'entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation. Vous pouvez également utiliser des données binaires brutes. Vous pouvez transformer les données avant de les envoyer au modèle ou avant de les retourner au client.

#### <a name="what-is-get_model_path"></a>Qu’est-ce que l’API get_model_path ?

Quand vous inscrivez un modèle, vous fournissez un nom de modèle qui sera utilisé pour la gestion du modèle dans le Registre. Vous utilisez ce nom avec [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) pour récupérer le chemin de chaque fichier de modèle présent sur le système de fichiers local. Si vous inscrivez un dossier ou une collection de fichiers, cette API retourne le chemin du répertoire qui contient ces fichiers.

Lorsque vous inscrivez un modèle, vous lui donnez un nom qui reflète l’emplacement du modèle, localement ou durant le déploiement du service.

> [!IMPORTANT]
> Si vous avez effectué l’apprentissage d’un modèle à l’aide d’un Machine Learning automatisé, une valeur `model_id` est utilisée comme nom de modèle. Pour obtenir un exemple d’inscription et de déploiement d’un modèle formé à l’aide d’un Machine Learning automatisé, voir [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment).

L’exemple ci-dessous retourne le chemin d’un seul fichier appelé `sklearn_mnist_model.pkl` (mais qui a été inscrit sous le nom `sklearn_mnist`) :

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>(Facultatif) Génération automatique d'un schéma

Si vous voulez générer automatiquement un schéma pour votre service web, spécifiez un exemple d’entrée et/ou de sortie dans le constructeur pour l’un des objets de type définis. Le type et l’exemple fournis sont alors utilisés automatiquement pour créer le schéma. Azure Machine Learning service crée ensuite une spécification (Swagger) [OpenAPI](https://swagger.io/docs/specification/about/) pour le service web pendant le déploiement.

Les types suivants sont pris en charge :

* `pandas`
* `numpy`
* `pyspark`
* Objet Python standard

Pour utiliser la génération de schéma, incluez le package `inference-schema` dans votre fichier d’environnement Conda.

##### <a name="example-dependencies-file"></a>Exemple de fichier de dépendances

L’extrait YAML suivant est un exemple de fichier de dépendances Conda pour l’inférence :

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Si vous souhaitez utiliser la génération de schéma automatique, votre script d’entrée **doit** importer les packages `inference-schema`.

Définissez les exemples de formats d’entrée et de sortie dans les variables `input_sample` et `output_sample`, qui représentent les formats de requête et de réponse pour le service web. Utilisez ces exemples dans les éléments décoratifs des fonctions d’entrée et de sortie sur la fonction `run()`. L’exemple scikit-learn ci-dessous utilise la génération de schéma.

##### <a name="example-entry-script"></a>Exemple de script d’entrée

L’exemple suivant montre comment accepter et retourner des données JSON :

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

L'exemple suivant montre comment définir les données d'entrée en tant que dictionnaire `<key: value>` à l’aide d'un Dataframe. Cette méthode est prise en charge pour la consommation du service web déployé à partir de Power BI ([En savoir plus sur la consommation du service web à partir de Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)) :

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Pour obtenir d’autres exemples de scripts, consultez ces exemples :

* Pytorch : [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow : [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras : [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX : [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Données binaires

Si votre modèle accepte les données binaires, comme une image, vous devez modifier le fichier `score.py` utilisé pour votre déploiement afin d’accepter les demandes HTTP brutes. Pour accepter des données brutes, utilisez la classe `AMLRequest` dans votre script d’entrée et ajoutez l’ élément décoratif `@rawhttp` à la fonction `run()`.

Voici l’exemple d’un `score.py` qui accepte des données binaires :

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La classe `AMLRequest` se trouve dans l’espace de noms `azureml.contrib`. Les éléments dans l’espace de noms changent fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer ce test sur votre environnement de développement local, vous pouvez installer les composants à l’aide de la commande suivante :
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)

Le partage des ressources Cross-Origin permet de demander des ressources dans une page web à partir d’un autre domaine. Le partage des ressources Cross-Origin fonctionne en fonction des en-têtes HTTP envoyés avec la demande du client et renvoyés avec la réponse du service. Pour plus d’informations sur le partage des ressources Cross-Origin et les en-têtes valides, consultez le [partage des ressources Cross-Origin](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) sur Wikipédia.

Pour configurer que votre modèle de déploiement prend en charge le partage des ressources Cross-Origin, utilisez la classe `AMLResponse` dans votre script d’entrée. Cette classe vous permet de définir les en-têtes de l’objet de réponse.

L’exemple suivant définit l’en-tête `Access-Control-Allow-Origin` de la réponse à partir du script d’entrée :

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> La classe `AMLResponse` se trouve dans l’espace de noms `azureml.contrib`. Les éléments dans l’espace de noms changent fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer ce test sur votre environnement de développement local, vous pouvez installer les composants à l’aide de la commande suivante :
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Définir votre configuration d’inférence

La configuration de l’inférence décrit comment configurer le modèle pour les prédictions. Cette configuration ne fait pas partie de votre script d'entrée. Elle référence votre script d'entrée et sert à localiser toutes les ressources requises par le déploiement. Elle est utilisée plus tard lors du déploiement du modèle.

La configuration de l’inférence peut utiliser des environnements Azure Machine Learning pour définir les dépendances logicielles nécessaires pour votre déploiement. Les environnements vous permettent de créer, de gérer et de réutiliser les dépendances logicielles requises pour l’apprentissage et le déploiement. L’exemple suivant présente le chargement d’un environnement à partir de votre espace de travail, puis son utilisation avec la configuration de l’inférence :

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Pour plus d’informations sur les environnements , consultez [Créer et gérer des environnements pour la formation et le déploiement](how-to-use-environments.md).

Vous pouvez également spécifier directement les dépendances sans utiliser d’environnement. L’exemple suivant montre comment créer une configuration d’inférence qui charge des dépendances logicielles à partir d’un fichier Conda :

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Pour plus d’informations, consultez les informations de référence de classe sur [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

Pour plus d’informations sur l’utilisation d’une image Docker personnalisée avec configuration de l’inférence, consultez [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Exemple CLI InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

La commande suivante montre comment déployer un modèle à l’aide de l’interface CLI :

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Dans cet exemple, la configuration contient les éléments suivants :

* L’information indiquant l’utilisation de Python
* Le [script d’entrée](#script) qui est utilisé pour gérer les requêtes web envoyées au service déployé
* Le fichier Conda qui décrit les packages Python nécessaires à l’inférence

Pour plus d’informations sur l’utilisation d’une image Docker personnalisée avec configuration de l’inférence, consultez [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Définir votre configuration de déploiement

Avant de commencer le déploiement, vous devez définir la configuration de déploiement. __La configuration de déploiement est propre à la cible de calcul qui va héberger le service web__. Par exemple, dans un déploiement local, vous devez spécifier le port sur lequel le service accepte les requêtes. La configuration de déploiement ne fait pas partie de votre script d'entrée. Elle est utilisée pour définir les caractéristiques de la cible de calcul qui hébergera le modèle et le script d'entrée.

Vous pouvez aussi avoir besoin de créer la ressource de calcul. C’est le cas, par exemple, si vous n’avez pas encore associé Azure Kubernetes Service à votre espace de travail.

Le tableau suivant donne un exemple de configuration de déploiement créée pour chaque cible de calcul :

| Cible de calcul | Exemple de configuration de déploiement |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Chacune des classes des services web Local, ACI et AKS peut être importée à partir de `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Profilage

Avant de déployer votre modèle en tant que service, vous pouvez le profiler afin de déterminer les exigences optimales en processeur et en mémoire. Vous pouvez profiler votre modèle à l’aide du kit de développement logiciel (SDK) ou de l'interface CLI. Les exemples suivants montrent comment utiliser un profilage du Kit de développement logiciel (SDK) :

> [!IMPORTANT]
> Lors de l’utilisation d’un profilage, la configuration d’inférence que vous fournissez ne peut pas référencer un environnement Azure Machine Learning. Au lieu de cela, définissez les dépendances logicielles à l’aide du paramètre `conda_file` de l’objet `InferenceConfig`.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(true)
profiling_results = profile.get_results()
print(profiling_results)
```

Ce code affiche un résultat similaire au texte suivant :

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Les résultats du profilage du modèle sont fournis sous la forme d’un objet `Run`.

Pour plus d’informations sur l’utilisation d’un profilage à partir de l’interface de ligne de commande, voir [Profil de modèle Azure Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Pour plus d’informations, voir les documents de référence suivants :

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [profile()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Schéma de fichier de configuration d’inférence](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Déployer sur la cible

Le déploiement utilise la configuration de déploiement de configuration de l’inférence pour déployer les modèles. Le processus de déploiement est similaire, quelle que soit la cible de calcul. Le déploiement sur AKS est légèrement différent, car vous devez fournir une référence au cluster AKS.

### <a id="local"></a> Déploiement local

Pour un déploiement local, Docker doit être installé sur votre machine locale.

#### <a name="using-the-sdk"></a>Utilisation du kit de développement logiciel

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Pour plus d'informations, consultez la documentation de référence de [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Utilisation de l’interface CLI

Pour déployer à l’aide de la CLI, utilisez la commande suivante. Remplacez `mymodel:1` par le nom et la version du modèle inscrit :

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Pour plus d’informations, consultez les informations de référence sur [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

### <a id="notebookvm"></a> Service web NotebookVM (DEVTEST)

Consultez [Déployer un modèle sur des machines virtuelles Notebook](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a> Azure Container Instances (DEVTEST)

Consultez [Procéder à un déploiement sur Azure Container Instances](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST & PRODUCTION)

Consultez [Procéder à un déploiement sur Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Utiliser des services web

Chaque service web déployé fournit une API REST, qui vous permet de créer des applications clientes dans divers langages de programmation. Si vous avez activé l'authentification de clé pour votre service, vous devez fournir une clé de service comme jeton dans l'en-tête de la requête.
Si vous avez activé l'authentification de jeton pour votre service, vous devez fournir un jeton JWT Azure Machine Learning comme jeton du porteur dans l'en-tête de la requête.

> [!TIP]
> Après avoir déployé le service, vous pouvez récupérer le document JSON du schéma. Utilisez la [propriété swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) du service web déployé, par exemple `service.swagger_uri`, pour obtenir l'URI du fichier Swagger du service web local.

### <a name="request-response-consumption"></a>Consommation de requête-réponse

Voici un exemple montrant comment appeler votre service dans Python :
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Pour plus d’informations, consultez [Créer des applications clientes pour utiliser des services web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Schéma de service web (spécification OpenAPI)

Si vous avez utilisé la génération automatique de schéma dans le cadre du déploiement, vous pouvez obtenir l'adresse de la spécification OpenAPI du service à l'aide de la [propriété swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Par exemple : `print(service.swagger_uri)`. Utilisez une requête GET (ou ouvrez l'URI dans un navigateur) pour récupérer la spécification.

Le document JSON suivant est un exemple de schéma (spécification OpenAPI) généré pour un déploiement :

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Pour plus d'informations sur la spécification, consultez [Spécification Open API](https://swagger.io/specification/).

Pour disposer d'un utilitaire permettant de créer des bibliothèques clientes à partir de la spécification, consultez [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a> Inférence par lots
Les cibles de calcul Azure Machine Learning sont créées et managées par Azure Machine Learning service. Elles peuvent être utilisées pour la prédiction par lots à partir d’Azure Machine Learning Pipelines.

Pour obtenir une présentation détaillée de l’inférence par lots avec la capacité de calcul Azure Machine Learning, lisez l’article [Exécuter des prédictions par lots](how-to-run-batch-predictions.md).

### <a id="iotedge"></a> Inférence IoT Edge
La prise en charge du déploiement en périphérie est en préversion. Pour plus d’informations, consultez l’article [Déployer Azure Machine Learning en tant que module IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a> Mettre à jour les services web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Modèle de déploiement en continu 

Vous pouvez déployer des modèles en continu à l’aide de l’extension Machine Learning pour [Azure DevOps](https://azure.microsoft.com/services/devops/). L'extension Machine Learning pour Azure DevOps vous permet de déclencher un pipeline de déploiement lorsqu’un nouveau modèle Machine Learning est inscrit dans l’espace de travail du service Azure Machine Learning. 

1. Inscrivez-vous sur [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), ce qui permet l'intégration et la livraison continues de votre application vers n’importe quelle plateforme/n'importe quel cloud. Azure Pipelines [diffère des pipelines ML](concept-ml-pipelines.md#compare). 

1. [Créez un projet Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Installer l'[extension Machine Learning pour Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Utilisez __Connexions au service__ pour configurer une connexion de principal de service à votre espace de travail de service Azure Machine Learning afin d'accéder à tous vos artefacts. Accédez aux paramètres du projet, cliquez sur Connexions au service, puis sélectionnez Azure Resource Manager.

    [![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png) 

1. Définissez AzureMLWorkspace en tant que __niveau d'étendue__ et renseignez les paramètres suivants.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Ensuite, pour déployer en continu votre modèle Machine Learning à l’aide d'Azure Pipelines, sous Pipelines, sélectionnez __Mise en production__. Ajoutez un nouvel artefact, sélectionnez l’artefact Modèle AzureML et la connexion au service créée à l’étape précédente. Sélectionnez le modèle et la version pour déclencher un déploiement. 

    [![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Activez le déclencheur de modèle sur votre artefact de modèle. En activant le déclencheur, chaque fois que la version spécifiée (version la plus récente) de ce modèle est inscrite dans votre espace de travail, un pipeline de mise en production Azure DevOps est déclenché. 

    [![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Pour obtenir d’autres exemples de projets et des exemples, consultez les exemples de référentiels suivants :

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="package-models"></a>Modèles de package

Dans certains cas, vous pouvez créer une image Docker sans déployer le modèle. Par exemple, lorsque vous planifiez un [déploiement sur Azure App Service](how-to-deploy-app-service.md). Vous pouvez également télécharger l’image et l’exécuter sur une installation locale de Docker. Vous pouvez même télécharger les fichiers utilisés pour générer l’image, les inspecter, les modifier, puis générer l’image manuellement.

L’empaquetage de modèle vous permet d’effectuer les deux. Il empaquette toutes les ressources nécessaires pour héberger un modèle en tant que service web, et vous permet de télécharger une image Docker entièrement générée ou les fichiers nécessaires pour en générer une. Vous pouvez utiliser l’empaquetage de modèle de deux façons :

* __Télécharger un modèle empaqueté__ : Vous téléchargez une image Docker contenant le modèle et les autres fichiers nécessaires pour l’héberger en tant que service web.
* __Générer un fichier Docker__ : Vous téléchargez le fichier Docker, le modèle, le script d’entrée et les autres ressources nécessaires pour générer une image Docker. Vous pouvez ensuite inspecter les fichiers ou apporter des modifications avant de générer l’image localement.

Les deux packages permettent d’obtenir une image Docker locale. 

> [!TIP]
> La création d’un package est similaire au déploiement d’un modèle, car elle utilise un modèle inscrit et une configuration d’inférence.

> [!IMPORTANT]
> Des fonctionnalités telles que le téléchargement d’une image entièrement générée ou la création d’une image localement requièrent l’installation d’un [Docker](https://www.docker.com) opérationnel sur votre environnement de développement.

### <a name="download-a-packaged-model"></a>Télécharger un modèle empaqueté

L’exemple suivant montre comment générer une image qui est inscrite dans le registre de conteneurs Azure pour votre espace de travail :

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Après avoir créé un package, vous pouvez utiliser `package.pull()` pour extraire l’image dans votre environnement Docker local. La sortie de cette commande affiche le nom de l’image. Par exemple : `Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. Après le téléchargement, utilisez la commande `docker images` pour afficher la liste des images locales :

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Pour démarrer un conteneur local à l’aide de cette image, utilisez la commande suivante pour démarrer un conteneur nommé à partir de l’interpréteur de commandes ou de la ligne de commande. Remplacez la valeur `<imageid>` par l’ID d’image retourné par la commande `docker images` :

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Cette commande démarre la version la plus récente de l’image nommée `myimage`. Elle mappe le port local 6789 au port du conteneur sur lequel le service web écoute (5001). Elle affecte également le nom `mycontainer` au conteneur, ce qui en facilite l’arrêt. Après le démarrage, vous pouvez envoyer des demandes à `http://localhost:6789/score`.

### <a name="generate-dockerfile-and-dependencies"></a>Générer un fichier Docker et des dépendances

L’exemple suivant montre comment télécharger le fichier Docker, le modèle et les autres ressources nécessaires pour générer l’image localement. Le paramètre `generate_dockerfile=True` indique que nous souhaitons les fichiers, et non une image entièrement générée :

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package
package.save("./imagefiles")
# Get the Azure Container Registry that the model/dockerfile uses
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Ce code télécharge les fichiers nécessaires à la création de l’image dans le répertoire `imagefiles`. Le fichier Docker inclus dans les fichiers d’enregistrement référence une image de base stockée dans un registre de conteneurs Azure. Lors de la génération de l’image sur votre installation Docker locale, vous devez utiliser l’adresse, le nom d’utilisateur et le mot de passe pour vous authentifier auprès de ce registre. Pour générer l’image à l’aide d’une installation Docker locale, procédez comme suit :

1. À partir d’un interpréteur de commandes ou d’une session de ligne de commande, utilisez la commande suivante pour authentifier Docker auprès du registre de conteneurs Azure. Remplacez `<address>`, `<username>` et `<password>` par les valeurs récupérées à l’aide de `package.get_container_registry()` :

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Pour créer l’image, utilisez la commande suivante. Remplacez `<imagefiles>` par le chemin d’accès au répertoire dans lequel la commande `package.save()` a enregistré les fichiers :

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Cette commande définit le nom de l’ image sur `myimage`.

Pour vérifier que l’image a été générée, utilisez la commande `docker images`. L’image `myimage` doit figurer dans la liste :

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Pour démarrer un conteneur basé sur cette image, utilisez la commande suivante :

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Cette commande démarre la version la plus récente de l’image nommée `myimage`. Elle mappe le port local 6789 au port du conteneur sur lequel le service web écoute (5001). Elle affecte également le nom `mycontainer` au conteneur, ce qui en facilite l’arrêt. Après le démarrage, vous pouvez envoyer des demandes à `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Exemple de client pour tester le conteneur local

Le code suivant est un exemple de client Python utilisable avec le conteneur :

```python
import requests
import json

# URL for the web service
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string
input_data = json.dumps(data)

# Set the content type
headers = {'Content-Type': 'application/json'}

# Make the request and display the response
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Pour d’autres exemples de clients dans d’autres langages de programmation, voir [Utiliser des modèles déployés en tant que services web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Arrêter le conteneur Docker

Pour arrêter le conteneur, utilisez la commande suivante à partir d’un autre interpréteur de commandes ou d’une autre ligne de commande :

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer un service web déployé, utilisez `service.delete()`.
Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation de référence sur [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) et [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Étapes suivantes
* [Guide pratique pour déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md)
* [Résolution des problèmes liés au déploiement](how-to-troubleshoot-deployment.md)
* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)


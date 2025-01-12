---
title: 'Didacticiel : Créer votre première expérience ML Paramétrage'
titleSuffix: Azure Machine Learning service
description: Dans cette série de tutoriels, vous allez effectuer des étapes de bout en bout pour vous familiariser avec le kit SDK Python Azure Machine Learning s’exécutant dans des notebooks Jupyter.  La première partie aborde la création d’un environnement serveur de notebooks cloud, ainsi que la création d’un espace de travail pour gérer vos expériences et vos modèles de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: d968d6e799b75940d1fb73aa31c22eb84068df7d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860428"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Didacticiel : Bien démarrer avec la création de votre première expérience ML avec le SDK Python

Dans ce tutoriel, vous allez effectuer des étapes de bout en bout pour vous familiariser avec le kit de développement logiciel (SDK) Python pour Azure Machine Learning s’exécutant dans des notebooks Jupyter. Ce tutoriel **fait partie d’une série de tutoriels en deux parties** et aborde l’installation et la configuration de l’environnement Python, ainsi que la création d’un espace de travail pour gérer vos expériences et vos modèles Machine Learning. [**La deuxième partie**](tutorial-1st-experiment-sdk-train.md) se poursuit avec l’apprentissage de plusieurs modèles Machine Learning et présente le processus de gestion des modèles avec le portail Azure et le kit de développement logiciel (SDK).

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un [espace de travail Azure Machine Learning](concept-workspace.md) à utiliser dans le tutoriel suivant.
> * Préinstallez et préconfigurez le SDK Python Azure Machine Learning, puis créez une machine virtuelle Notebook Jupyter basée sur le cloud.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="create-a-workspace"></a>Créer un espace de travail

Un espace de travail Azure Machine Learning est une ressource fondamentale du cloud que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le kit de développement logiciel (SDK). Si vous disposez déjà d’un espace de travail Azure Machine Learning service, passez à la [section suivante](#azure). Dans le cas contraire, créez-en un maintenant.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Créer un serveur de notebooks basé sur le cloud

Cet exemple utilise le serveur de notebook cloud dans votre espace de travail pour une expérience préconfigurée sans installation. Utilisez [votre propre environnement](how-to-configure-environment.md#local) si vous préférez contrôler votre environnement, vos packages et vos dépendances.

À partir de votre espace de travail, créez une ressource cloud pour commencer à utiliser des notebooks Jupyter. Cette ressource est une machine virtuelle Linux cloud préconfigurée avec tout ce dont vous avez besoin pour exécuter Azure Machine Learning service.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  Si vous ne savez pas comment localiser votre espace de travail sur le portail, découvrez comment [retrouver votre espace de travail](how-to-manage-workspace.md#view).

1. Dans la page de votre espace de travail sur le portail Azure, sélectionnez **Machines virtuelles Notebook** à gauche.

1. Sélectionnez **+Nouveau** pour créer un machine virtuelle Notebook.

     ![Sélectionner Nouvelle machine virtuelle](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Attribuez un nom à votre machine virtuelle. 
   + Le nom de votre machine virtuelle Notebook doit comprendre entre 2 et 16 caractères. Les caractères valides sont les lettres, les chiffres et le caractère « - ».  
   + Le nom doit aussi être unique dans votre abonnement Azure.

1. Sélectionnez ensuite **Créer**. Un certain temps peut être nécessaire pour configurer votre machine virtuelle.

1. Attendez que l’état du travail passe à **Exécution**.
   Dès lors que votre machine virtuelle s’exécute, lancez l’interface web Jupyter à partir de la section **Machines virtuelles Notebook**.

1. Sélectionnez **Jupyter** dans la colonne **URI** correspondant à votre machine virtuelle.

    ![Démarrer le serveur de notebooks Jupyter](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   Le lien démarre votre serveur de notebooks et ouvre la page web de notebook Jupyter dans un nouvel onglet du navigateur.  Ce lien fonctionne uniquement pour la personne qui crée la machine virtuelle. Chaque utilisateur de l’espace de travail doit créer sa propre machine virtuelle.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez effectué les tâches suivantes :

* Créer un espace de travail Azure Machine Learning service.
* Utiliser et configurer un serveur de notebooks cloud dans votre espace de travail.

Dans la **deuxième partie** du tutoriel, vous exécutez le code dans `tutorial-1st-experiment-sdk-train.ipynb` pour entraîner un modèle Machine Learning. 

> [!div class="nextstepaction"]
> [Tutoriel : effectuer l’apprentissage de votre premier modèle](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Si vous ne prévoyez pas de suivre la deuxième partie de ce tutoriel ou tout autre tutoriel, vous devez [arrêter la machine virtuelle du serveur de notebooks cloud](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quand vous ne l’utilisez pas, afin de réduire les coûts.



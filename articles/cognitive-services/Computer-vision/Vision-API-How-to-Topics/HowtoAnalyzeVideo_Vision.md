---
title: 'Exemple : Analyse de vidéo en temps réel - Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer une analyse en quasi temps réel des images d’un flux vidéo en direct en utilisant l’API Vision par ordinateur.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 3432ea20f9fb59524940258e13c46ee6f4c4e890
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565702"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Comment analyser des vidéos en temps réel

Ce guide montre comment effectuer une analyse en temps quasi réel des images provenant d’un flux vidéo en direct. Les composants de base d’un tel système sont les suivants :

- Acquisition d’images à partir d’une source vidéo
- Sélection des images à analyser
- Envoi de ces images à l’API
- Utilisation de chaque résultat de l’analyse renvoyé par l’appel d’API

Ces exemples sont écrits en C# et le code est accessible à l’emplacement suivant sur GitHub : [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Approche

Il existe de nombreuses manières de résoudre le problème d’exécution d’une analyse en temps quasi réel de flux vidéo. Nous allons commencer par décrire trois approches, par ordre de sophistication croissante.

### <a name="a-simple-approach"></a>Approche simple

La conception la plus simple pour un système d’analyse en temps quasi réel est une boucle infinie, où lors de chaque itération nous capturons une image, nous l’analysons, puis nous utilisons le résultat :

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Si notre analyse était constituée d’un algorithme léger côté client, cette approche conviendrait. Toutefois, quand notre analyse a lieu dans le cloud, la latence inhérente signifie qu’un appel d’API peut prendre quelques secondes, période pendant laquelle nous ne capturons aucune image et notre thread ne fait rien. La fréquence d’images maximale est limitée par la latence des appels d’API.

### <a name="parallelizing-api-calls"></a>Appels d’API parallèles

Une simple boucle monothread convient parfaitement à un algorithme léger côté client, mais elle n’est pas vraiment compatible avec la latence impliquée dans les appels d’API cloud. La solution à ce problème consiste à autoriser l’exécution des longs appels d’API en parallèle avec la capture d’images. En C#, nous pourrions y parvenir grâce au parallélisme basé sur les tâches, par exemple :

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Cette approche permet de lancer chaque analyse dans une tâche distincte, qui peut s’exécuter en arrière-plan pendant que nous continuons de capturer de nouvelles images. Cela évite de bloquer le thread principal pendant que nous attendons le retour d’un appel d’API. En revanche, nous perdons certaines des garanties offertes par la version simplifiée : plusieurs appels d’API peuvent se produire en parallèle, et les résultats peuvent être retournés dans un ordre incorrect. En outre, avec cette approche, plusieurs threads risquent d’entrer dans la fonction ConsumeResult() simultanément, ce qui peut être dangereux si la fonction n’est pas thread-safe. Pour finir, ce code simple n’assurant pas le suivi des tâches créées, les exceptions disparaîtront de manière silencieuse. Nous devons donc ajouter un dernier ingrédient, à savoir un thread « consommateur » qui effectue le suivi des tâches d’analyse, lève des exceptions, arrête les tâches longues et garantit que les résultats sont consommés dans l’ordre correct, un à la fois.

### <a name="a-producer-consumer-design"></a>Modèle producteur-consommateur

Dans notre système « producteur-consommateur » final, nous avons un thread producteur qui ressemble à notre boucle infinie précédente. Toutefois, au lieu d’utiliser les résultats de l’analyse dès qu’ils sont disponibles, le producteur place simplement les tâches dans une file d’attente afin d’effectuer leur suivi.

```csharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```

Nous avons également un thread consommateur qui enlève les tâches de la file d’attente, attend qu’elles se terminent et affiche le résultat ou déclenche l’exception qui a été levée. Grâce à la file d’attente, nous pouvons garantir que les résultats sont utilisés chacun à leur tour, dans le bon ordre, sans limiter la fréquence d’images maximale du système.

```csharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implémentation de la solution

### <a name="getting-started"></a>Mise en route

Pour rendre votre application opérationnelle le plus rapidement possible, nous avons implémenté le système décrit ci-dessus, avec l’intention qu’il soit suffisamment souple pour implémenter de nombreux scénarios tout en étant facile à utiliser. Pour obtenir le code, accédez à [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

La bibliothèque contient la classe FrameGrabber, qui implémente le système producteur-consommateur décrit ci-dessus pour traiter des trames vidéo provenant d’une webcam. L’utilisateur peut spécifier la forme exacte de l’appel d’API, et la classe utilise des événements pour informer le code appelant quand une nouvelle image est acquise ou un nouveau résultat d’analyse est disponible.

Pour illustrer certaines des possibilités, il existe deux exemples d’applications qui utilisent la bibliothèque. La première est une application console simple, dont une version simplifiée est reproduite ci-dessous. Elle capture les images à partir de la webcam par défaut et les soumet à l’API Visage en vue d’effectuer la détection de visage.

```csharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```

Le deuxième exemple d’application est un peu plus intéressant. Il vous permet de choisir l’API à appeler sur les trames vidéo. Sur le côté gauche, l’application affiche un aperçu de la vidéo en direct. Sur le côté droit, elle affiche le résultat d’API le plus récent, superposé sur l’image correspondante.

Dans la plupart des modes, il y aura un délai visible entre la vidéo en direct à gauche et l’analyse visualisée à droite. Ce délai correspond au temps nécessaire pour effectuer l’appel d’API. L’exception à cette règle est le mode « EmotionsWithClientFaceDetect », qui effectue la détection de visage localement sur l’ordinateur client à l’aide d’OpenCV, avant d’envoyer des images à Cognitive Services. Ce faisant, nous pouvons visualiser le visage détecté immédiatement, puis mettre à jour les émotions ultérieurement, après le retour de l’appel d’API. Cela illustre la possibilité d’une approche « hybride », où un traitement simple peut être effectué sur le client, puis les API Cognitive Services peuvent être utilisées pour compléter ce traitement avec une analyse plus approfondie si nécessaire.

![Capture d’écran de l’application LiveCameraSample montrant une image avec balises affichées](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Intégration dans votre base de code

Pour commencer avec cet exemple, effectuez les étapes suivantes :

1. Obtenez des clés API pour les API Vision à partir [d’Abonnements](https://azure.microsoft.com/try/cognitive-services/). Pour l’analyse des trames vidéo, les API applicables sont les suivantes :
    - [AP Vision par ordinateur](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API Émotion](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API Visage](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Clonez le référentiel GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Ouvrez l’exemple dans Visual Studio 2015, générez et exécutez les exemples d’applications :
    - Pour BasicConsoleSample, la clé API Visage est codée en dur directement dans  [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Pour LiveCameraSample, les clés doivent être entrées dans le volet Paramètres de l’application. Elles seront conservées d’une session à l’autre en tant que données utilisateur.

Quand vous êtes prêt à procéder à l’intégration, **référencez simplement la bibliothèque VideoFrameAnalyzer à partir de vos propres projets**.

Les fonctionnalités de compréhension d’image, de voix, de vidéo ou de texte de VideoFrameAnalyzer utilisent Azure Cognitive Services. Microsoft recevra les images, les sons, les vidéos et autres données que vous chargez (par le biais de cette application) et pourra les utiliser à des fins d’amélioration du service. Nous sollicitons votre aide afin de protéger les personnes dont votre application envoie les données à Azure Cognitive Services.

## <a name="summary"></a>Résumé

Dans ce guide, vous avez découvert comment exécuter une analyse en temps quasi réel de flux vidéo en direct à l’aide des API Visage, Vision par ordinateur et Émotion, et comment utiliser notre exemple de code pour bien démarrer. Vous pouvez commencer à créer votre application avec des clés API gratuites en accédant à la [page d’inscription de Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

N’hésitez pas à soumettre vos commentaires et suggestions dans le [dépôt GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) ou, pour envoyer des commentaires plus généraux sur les API, sur notre  [site UserVoice](https://cognitive.uservoice.com/).


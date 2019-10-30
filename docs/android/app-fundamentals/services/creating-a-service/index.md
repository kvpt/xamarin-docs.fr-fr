---
title: Création d'un service
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 658bb65c9f9dea2c68b782736de02d95df368dd3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024855"
---
# <a name="creating-a-service"></a>Création d'un service

Les services Xamarin. Android doivent respecter deux règles inviolables des services Android :

- Ils doivent étendre le [`Android.App.Service`](xref:Android.App.Service).
- Ils doivent être décorés avec l' [`Android.App.ServiceAttribute`](xref:Android.App.ServiceAttribute).

Une autre exigence des services Android est qu’ils doivent être inscrits dans le **fichier AndroidManifest. xml** et donner un nom unique. Xamarin. Android inscrira automatiquement le service dans le manifeste au moment de la génération avec l’attribut XML nécessaire.

Cet extrait de code est l’exemple le plus simple pour créer un service dans Xamarin. Android qui répond à ces deux conditions :  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

Au moment de la compilation, Xamarin. Android inscrira le service en injectant l’élément XML suivant dans **fichier AndroidManifest. xml** (Notez que Xamarin. Android a généré un nom aléatoire pour le service) :

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

Il est possible de partager un service avec d’autres applications Android en l' _exportant_ . Pour ce faire, définissez la propriété `Exported` sur l' `ServiceAttribute`. Lors de l’exportation d’un service, la propriété `ServiceAttribute.Name` doit également être définie pour fournir un nom public significatif pour le service. Cet extrait de code montre comment exporter et nommer un service :

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

L’élément **fichier AndroidManifest. xml** de ce service ressemble à ce qui suit :

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

Les services ont leur propre cycle de vie avec des méthodes de rappel appelées lors de la création du service. Les méthodes qui sont appelées varient en fonction du type de service. Un service démarré doit implémenter des méthodes de cycle de vie différentes par rapport à un service lié, tandis qu’un service hybride doit implémenter les méthodes de rappel pour un service démarré et un service lié. Ces méthodes sont tous des membres de la classe `Service` ; le mode de démarrage du service détermine les méthodes de cycle de vie qui seront appelées. Ces méthodes de cycle de vie seront abordées plus en détail ultérieurement.

Par défaut, un service démarre dans le même processus qu’une application Android. Il est possible de démarrer un service dans son propre processus en affectant à la propriété `ServiceAttribute.IsolatedProcess` la valeur true :

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

L’étape suivante consiste à examiner comment démarrer un service, puis à examiner comment implémenter les trois différents types de services.

> [!NOTE]
> Un service s’exécute sur le thread d’interface utilisateur. ainsi, si un travail est exécuté, ce qui bloque l’interface utilisateur, le service doit utiliser des threads pour effectuer le travail.

## <a name="starting-a-service"></a>Démarrage d’un service

La méthode la plus simple pour démarrer un service dans Android est de distribuer un `Intent` qui contient des métadonnées pour aider à identifier le service qui doit être démarré. Il existe deux styles différents d’intentions qui peuvent être utilisés pour démarrer un service :

- Une **intention explicite &ndash; une** _intention explicite_ identifiera exactement le service qui doit être utilisé pour effectuer une action donnée. Une intention explicite peut être considérée comme une lettre qui a une adresse spécifique ; Android acheminera l’intention vers le service explicitement identifié. Cet extrait de code est un exemple d’utilisation d’une intention explicite pour démarrer un service appelé `DownloadService` :

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

- L' **intention implicite** &ndash; ce type d’intention identifie faiblement l’action que l’utilisateur souhaite effectuer, mais le service exact pour terminer cette action est inconnu. Une intention implicite peut être considérée comme une lettre adressée « à qui elle peut poser problème... ».
    Android examine le contenu de l’intention et détermine s’il existe un service qui correspond à l’intention.

    Un _filtre d’intention_ est utilisé pour aider à faire correspondre l’intention implicite avec un service inscrit. Un filtre d’intention est un élément XML qui est ajouté à **fichier AndroidManifest. xml** , qui contient les métadonnées nécessaires pour aider à faire correspondre un service à un but implicite.

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

Si Android a plusieurs correspondances possibles pour une intention implicite, il peut demander à l’utilisateur de sélectionner le composant pour gérer l’action :

![Capture d’écran d’une boîte de dialogue de désambiguïsation](images/creating-a-service-01.png "Capture d’écran d’une boîte de dialogue de désambiguïsation")

> [!IMPORTANT]
> À partir de Android 5,0 (AP Level 21), un but implicite ne peut pas être utilisé pour démarrer un service.

Dans la mesure du possible, les applications doivent utiliser des intentions explicites pour démarrer un service. Une intention implicite ne demande pas de démarrer un service spécifique &ndash; il s’agit d’une demande pour un service installé sur l’appareil pour traiter la demande. Cette requête ambiguë peut entraîner un mauvais service pour la gestion de la demande ou une autre application qui démarre inutilement (ce qui augmente la pression pour les ressources sur l’appareil).

Le mode de distribution de l’intention dépend du type de service et sera abordé plus en détail ultérieurement dans les guides spécifiques à chaque type de service.

### <a name="creating-an-intent-filter-for-implicit-intents"></a>Création d’un filtre d’intention pour les intentions implicites

Pour associer un service à une intention implicite, une application Android doit fournir des métadonnées pour identifier les fonctionnalités du service. Ces métadonnées sont fournies par des _filtres d’intention_. Les filtres d’intention contiennent des informations, telles qu’une action ou un type de données, qui doivent être présentes dans l’intention de démarrer un service. Dans Xamarin. Android, le filtre d’intention est inscrit dans **fichier AndroidManifest. xml** en décorant un service avec la [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute). Par exemple, le code suivant ajoute un filtre d’intention avec une action associée de `com.xamarin.DemoService` :

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

Cela entraîne l’inclusion d’une entrée dans le fichier **fichier AndroidManifest. xml** &ndash; une entrée empaquetée avec l’application d’une manière analogue à l’exemple suivant :

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Avec les principes fondamentaux d’un service Xamarin. Android, examinons les différents sous-types de services plus en détail.

## <a name="related-links"></a>Liens associés

- [Android. app. service](xref:Android.App.Service)
- [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Android. app. intention](xref:Android.Content.Intent)
- [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)

---
title: UrhoSharp iOS et tvOS prise en charge
description: Ce document aborde iOS et tvOS prise en charge de UrhoSharp. Il décrit comment créer un projet, configurer et lancer Urho et effectuer une incorporation personnalisée de Urho.
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 3efdbfcfdd670525dbf3198deb17c4631a889c56
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832461"
---
# <a name="urhosharp-ios-and-tvos-support"></a>Prise en charge iOS et tvOS UrhoSharp

Bien que Urho est une bibliothèque de classes portable, et permet à la même API pour être utilisé sur la plateforme différents pour votre logique de jeu, vous devez toujours initialiser Urho dans votre pilote de plateforme spécifique et dans certains cas, vous pouvez tirer parti des fonctionnalités spécifiques de plateforme .

Dans les pages ci-dessous, supposons que `MyGame` est un sublcass de la `Application` classe.

## <a name="ios-and-tvos"></a>iOS et tvOS

**Architectures prises en charge :** armv7, arm64, i386

## <a name="creating-a-project"></a>Création d'un projet

Créer un projet iOS, puis ajouter des données dans le répertoire de ressources et assurez-vous que tous les fichiers ont **BundleResource** en tant que le **Action de génération**.

![Le programme d’installation de projet](ios-images/image-4.png "ajouter des données dans le répertoire de ressources")

## <a name="configuring-and-launching-urho"></a>Configurant et en lançant Urho

Ajouter à l’aide des instructions pour la `Urho` et `Urho.iOS` espaces de noms, puis ajoutez ce code pour l’initialisation Urho, ainsi que le lancement de votre application :

```csharp
new MyGame().Run();
```

Notez que dans la mesure où iOS attend `FinishedLaunching` pour terminer, vous devez la file d’attente l’appel à `Run()` pour exécuter une fois la méthode terminée, il s’agit d’un idiome courant :

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

Il est important de désactiver les optimisations PNG, car l’optimiseur PNG iOS par défaut générera des images qui Urho ne peut pas consommer actuellement correctement

## <a name="custom-embedding-of-urho"></a>Incorporation personnalisé de Urho

Vous peut également avoir Urho reprendre l’écran de l’application entière, et pour l’utiliser en tant que composant de votre application, vous pouvez créer un `UrhoSurface` qui est un `UIView` que vous pouvez incorporer dans votre application existante.

C’est ce que vous devez faire :

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

Il hébergera votre classe Urho, par conséquent, puis vous le feriez :

```csharp
new MyGame().Run ();
```

---
title: Inscription et résolution de Xamarin.Forms DependencyService
description: Cet article explique comment utiliser la classe DependencyService de Xamarin.Forms pour appeler des fonctionnalités natives d’une plateforme.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: 6e666c16c9b1afc3478f524cae2f84d6704319c2
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199223"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Inscription et résolution de Xamarin.Forms DependencyService

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Lors de l’utilisation de Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour appeler des fonctionnalités natives d’une plateforme, les implémentations de la plateforme doivent être inscrites auprès de `DependencyService` puis résolues pour que le code partagé puisse les appeler.

## <a name="register-platform-implementations"></a>Inscrire des implémentations d’une plateforme

Les implémentations de la plateforme doivent être inscrites auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour que Xamarin.Forms puisse les localiser lors de l’exécution.

L’inscription peut être effectuée avec [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) ou avec les méthodes [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

> [!IMPORTANT]
> Les versions Release des projets UWP qui utilisent la compilation native de .NET doivent inscrire les implémentations de plateforme avec les méthodes [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

### <a name="registration-by-attribute"></a>Inscription par attribut

Vous pouvez utiliser [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pour inscrire une implémentation de plateforme auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService). L’attribut indique que le type spécifié fournit une implémentation concrète de l’interface.

L’exemple suivant montre l’utilisation de [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pour inscrire l’implémentation iOS de l’interface `IDeviceOrientationService` :

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

Dans cet exemple, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) inscrit `DeviceOrientationService` auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService). Ce qui aboutit à l’inscription du type concret auprès de l’interface qu’il implémente.

De même, les implémentations de l’interface `IDeviceOrientationService` sur d’autres plateformes doivent être inscrites avec [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

> [!NOTE]
> L’inscription avec [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) est effectuée au niveau de l’espace de noms.

### <a name="registration-by-method"></a>Inscription par méthode

Les méthodes [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) peuvent être utilisées pour inscrire une implémentation de plateforme auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService).

L’exemple suivant montre l’utilisation de la méthode [`Register`](xref:Xamarin.Forms.DependencyService.Register*) pour inscrire l’implémentation iOS de l’interface `IDeviceOrientationService` :

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());
        DependencyService.Register<IDeviceOrientationService, DeviceOrientationService>();
        return base.FinishedLaunching(app, options);
    }
}
```

Dans cet exemple, la méthode [`Register`](xref:Xamarin.Forms.DependencyService.Register*) inscrit le type concret `DeviceOrientationService` auprès de l’interface `IDeviceOrientationService`. Vous pouvez aussi utiliser une surcharge de la méthode [`Register`](xref:Xamarin.Forms.DependencyService.Register*) pour inscrire une implémentation de plateforme auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService) :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

Dans cet exemple, la méthode [`Register`](xref:Xamarin.Forms.DependencyService.Register*) inscrit `DeviceOrientationService` auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService). Ce qui aboutit à l’inscription du type concret auprès de l’interface qu’il implémente.

De même, les implémentations de l’interface `IDeviceOrientationService` sur d’autres plateformes peuvent être inscrites avec les méthodes [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

> [!IMPORTANT]
> L’inscription avec les méthodes [`Register`](xref:Xamarin.Forms.DependencyService.Register*) doit être effectuée dans les projets de plateforme, avant que la fonctionnalité fournie par l’implémentation de la plateforme soit appelée depuis du code partagé.

## <a name="resolve-the-platform-implementations"></a>Résoudre les implémentations d’une plateforme

Les implémentations d’une plateforme doivent être résolues avant d’être appelées. Cette opération est généralement effectuée dans du code partagé avec la méthode [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*). Cependant, cette opération peut aussi être effectuée avec la méthode [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*).

Par défaut, [`DependencyService`](xref:Xamarin.Forms.DependencyService) résout uniquement les implémentations de plateforme qui ont des constructeurs sans paramètre. Toutefois, il est possible d’injecter une méthode de résolution de dépendance dans Xamarin.Forms qui utilise un conteneur d’injection de dépendance ou des méthodes de fabrique pour résoudre les implémentations de plateforme. Cette approche permet de résoudre les implémentations de plateforme qui ont des constructeurs avec des paramètres. Pour plus d’informations, consultez [Résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> L’appel d’une implémentation de plateforme qui n’a pas été inscrite auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService) entraîne la levée d’une `NullReferenceException`.

### <a name="resolve-using-the-getlttgt-method"></a>Résoudre avec la méthode Get&lt;T&gt;

La méthode [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) extrait l’implémentation de l’interface `T` de la plateforme à l’exécution et en crée une instance en tant que singleton. Cette instance va perdurer pendant tout la durée de vie de l’application, et tous les appels suivants pour résoudre la même implémentation de la plateforme vont récupérer la même instance.

Le code suivant montre un exemple d’appel de la méthode [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) pour résoudre l’interface `IDeviceOrientationService`, puis d’un appel de sa méthode `GetOrientation` :

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> La méthode [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) crée par défaut une instance de l’implémentation de l’interface `T` de la plateforme à l’exécution en tant que singleton. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [Gérer la durée de vie des objets résolus](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Résoudre avec la méthode Resolve&lt;T&gt;

La méthode [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) extrait l’implémentation de l’interface `T` de la plateforme à l’exécution, en utilisant une méthode de résolution des dépendances qui a été injectée dans Xamarin.Forms avec la classe [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver). Si une méthode de résolution des dépendances n’a pas été injectée dans Xamarin.Forms, la méthode `Resolve<T>` recours alors à un appel de la méthode [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) pour récupérer l’implémentation de la plateforme. Pour plus d’informations sur l’injection d’une méthode de résolution des dépendances dans Xamarin.Forms, consultez [Résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

Le code suivant montre un exemple d’appel de la méthode [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) pour résoudre l’interface `IDeviceOrientationService`, puis d’un appel de sa méthode `GetOrientation` :

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quand la méthode [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) recours à un appel de la méthode [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*), elle crée par défaut une instance de l’implémentation de l’interface `T` de la plateforme en tant que singleton. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [Gérer la durée de vie des objets résolus](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gérer la durée de vie des objets résolus

Le comportement par défaut de la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) est de résoudre les implémentations de plateforme en tant que singletons. Par conséquent, les implémentations de plateforme vont perdurer pendant toute la durée de vie d’une application.

Ce comportement est spécifié avec l’argument facultatif [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) sur les méthodes [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) et [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*). L’énumération [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) définit deux membres :

- `GlobalInstance`, qui retourne l’implémentation de plateforme en tant que singleton.
- `NewInstance`, qui retourne une nouvelle instance de l’implémentation de la plateforme. L’application est alors responsable de la gestion de la durée de vie de l’instance de l’implémentation de la plateforme.

Les méthodes [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) et [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) définissent leurs arguments facultatifs sur [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget), et les implémentations de plateforme sont donc toujours résolues en tant que singletons. Vous pouvez changer ce comportement de façon à créer les nouvelles instances des implémentations de plateforme en spécifiant [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) comme arguments des méthodes `Get<T>` et `Resolve<T>` :

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

Dans cet exemple, [`DependencyService`](xref:Xamarin.Forms.DependencyService) crée une nouvelle instance de l’implémentation de la plateforme pour l’interface `ITextToSpeechService`. Tous les appels suivants pour résoudre `ITextToSpeechService` vont également créer de nouvelles instances.

La conséquence de la création systématique d’une nouvelle instance d’une implémentation de plateforme est que l’application devient responsable de la gestion de la durée de vie des instances. Cela signifie que si vous vous abonnez à un événement défini dans une implémentation de plateforme, vous devez vous désabonner de l’événement quand l’implémentation de plateforme n’est plus nécessaire. Cela signifie aussi que les implémentations de plateforme devront éventuellement implémenter `IDisposable` et nettoyer leurs ressources dans les `Dispose`. L’exemple d’application illustre ce scénario dans ses implémentations de plateforme `TextToSpeechService`.

Quand une application a fini d’utiliser une implémentation de plateforme qui implémente `IDisposable`, elle doit appeler l’implémentation `Dispose` de l’objet. Ceci peut être effectué avec une instruction `using` :

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
using (service as IDisposable)
{
    await service.SpeakAsync("Hello world");
}
```

Dans cet exemple, après l’appel de la méthode `SpeakAsync`, l’instruction `using` supprime automatiquement l’objet d’implémentation de plateforme. Le résultat est que la méthode `Dispose` de l’objet est appelée, qui effectue le nettoyage nécessaire.

Pour plus d’informations sur l’appel de la méthode `Dispose` d’un objet, consultez [Utilisation d’objets qui implémentent IDisposable](/dotnet/standard/garbage-collection/using-objects).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de DependencyService (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)

---
title: Xamarin.Forms Inscription et résolution DependencyService
description: Cet article explique comment utiliser la Xamarin.Forms classe DependencyService pour appeler des fonctionnalités de plateforme natives.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9e8c0eeb70393cd3a5c6db7b99146b69b79ec14d
ms.sourcegitcommit: 01ccefd54c0ced724784dbe1aec9ecfc9b00e633
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630242"
---
# <a name="no-locxamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.Forms Inscription et résolution DependencyService

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Quand vous utilisez Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour appeler la fonctionnalité de plateforme native, les implémentations de plateforme doivent être inscrites auprès du `DependencyService` , puis résolues à partir du code partagé pour les appeler.

## <a name="register-platform-implementations"></a>Inscrire des implémentations d’une plateforme

Les implémentations de plateforme doivent être inscrites auprès du [`DependencyService`](xref:Xamarin.Forms.DependencyService) afin que Xamarin.Forms puisse les localiser au moment de l’exécution.

L’inscription peut être effectuée avec [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) , ou avec les [`Register`](xref:Xamarin.Forms.DependencyService.Register*) `RegisterSingleton` méthodes et.

> [!IMPORTANT]
> Les versions Release des projets UWP qui utilisent la compilation native .NET doivent inscrire des implémentations de plateforme avec les [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthodes.

### <a name="registration-by-attribute"></a>Inscription par attribut

Le [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) peut être utilisé pour inscrire une implémentation de plateforme avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService) . L’attribut indique que le type spécifié fournit une implémentation concrète de l’interface.

L’exemple suivant utilise [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) pour inscrire l’implémentation iOS de l' `IDeviceOrientationService` interface :

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

Dans cet exemple, le [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) s’inscrit `DeviceOrientationService` auprès de [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Ce qui aboutit à l’inscription du type concret auprès de l’interface qu’il implémente.

De même, les implémentations de l' `IDeviceOrientationService` interface sur d’autres plateformes doivent être inscrites auprès du [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) .

> [!NOTE]
> L’inscription auprès du [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) est effectuée au niveau de l’espace de noms.

### <a name="registration-by-method"></a>Inscription par méthode

Les [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) méthodes, et la `RegisterSingleton` méthode, peuvent être utilisées pour inscrire une implémentation de plateforme avec [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

L’exemple suivant utilise la [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthode pour inscrire l’implémentation iOS de l' `IDeviceOrientationService` interface :

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

Dans cet exemple, la [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthode enregistre le type concret, `DeviceOrientationService` , par rapport à l' `IDeviceOrientationService` interface. Une surcharge de la [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthode peut également être utilisée pour inscrire une implémentation de plateforme avec les [`DependencyService`](xref:Xamarin.Forms.DependencyService) éléments suivants :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

Dans cet exemple, la [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthode inscrit le `DeviceOrientationService` avec [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Ce qui aboutit à l’inscription du type concret auprès de l’interface qu’il implémente.

Une instance d’objet existante peut également être inscrite en tant que singleton avec la `RegisterSingleton` méthode :

```csharp
var service = new DeviceOrientationService();
DependencyService.RegisterSingleton<IDeviceOrientationService>(service);
```

Dans cet exemple, la `RegisterSingleton` méthode enregistre l' `DeviceOrientationService` instance d’objet par rapport à l' `IDeviceOrientationService` interface, en tant que singleton.

De même, les implémentations de l' `IDeviceOrientationService` interface sur d’autres plateformes peuvent être inscrites avec les [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthodes, ou la `RegisterSingleton` méthode.

> [!IMPORTANT]
> L’inscription auprès [`Register`](xref:Xamarin.Forms.DependencyService.Register*) des `RegisterSingleton` méthodes et doit être effectuée dans les projets de plateforme, avant que les fonctionnalités fournies par l’implémentation de la plateforme ne soient appelées à partir du code partagé.

## <a name="resolve-the-platform-implementations"></a>Résoudre les implémentations d’une plateforme

Les implémentations d’une plateforme doivent être résolues avant d’être appelées. Cela est généralement effectué en code partagé à l’aide de la [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode. Toutefois, elle peut également être effectuée à l’aide de la [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode.

Par défaut, le [`DependencyService`](xref:Xamarin.Forms.DependencyService) ne résout que les implémentations de plateforme qui ont des constructeurs sans paramètre. Toutefois, une méthode de résolution de dépendance peut être injectée dans Xamarin.Forms qui utilise un conteneur d’injection de dépendances ou des méthodes de fabrique pour résoudre les implémentations de plateforme. Cette approche permet de résoudre les implémentations de plateforme qui ont des constructeurs avec des paramètres. Pour plus d’informations, consultez [résolution des Xamarin.Forms dépendances dans ](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> L’appel d’une implémentation de plateforme qui n’a pas été inscrite auprès de entraîne la [`DependencyService`](xref:Xamarin.Forms.DependencyService) levée d’une `NullReferenceException` exception.

### <a name="resolve-using-the-getlttgt-method"></a>Résoudre avec la méthode Get&lt;T&gt;

La [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode récupère l’implémentation de la plateforme de l’interface `T` au moment de l’exécution et :

- Crée une instance de celle-ci en tant que singleton.
- Retourne une instance existante en tant que singleton, qui a été inscrit avec le `DependencyService` par la `RegisterSingleton` méthode.

Dans les deux cas, l’instance est active pendant la durée de vie de l’application, et tous les appels suivants pour résoudre la même implémentation de la plateforme récupèrent la même instance.

Le code suivant illustre un exemple d’appel de la [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode pour résoudre l’interface, puis l’appel de la `IDeviceOrientationService` `GetOrientation` méthode :

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Par [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) défaut, la méthode retourne une instance de l’implémentation de plateforme de l’interface `T` en tant que singleton. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [Gérer la durée de vie des objets résolus](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Résoudre avec la méthode Resolve&lt;T&gt;

La [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode récupère l’implémentation de plateforme de l’interface `T` au moment de l’exécution, à l’aide d’une méthode de résolution de dépendance qui a été injectée dans Xamarin.Forms avec la [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) classe. Si une méthode de résolution de dépendance n’a pas été injectée dans Xamarin.Forms , la `Resolve<T>` méthode va revenir à l’appel de la [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode pour récupérer l’implémentation de la plateforme. Pour plus d’informations sur l’injection d’une méthode de résolution de dépendance dans Xamarin.Forms , consultez [résolution des dépendances dans Xamarin.Forms ](~/xamarin-forms/internals/dependency-resolution.md).

Le code suivant illustre un exemple d’appel de la [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode pour résoudre l’interface, puis l’appel de la `IDeviceOrientationService` `GetOrientation` méthode :

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Lorsque la [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode revient à appeler la [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode, elle retourne une instance de l’implémentation de plateforme de l’interface `T` en tant que singleton, par défaut. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [Gérer la durée de vie des objets résolus](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gérer la durée de vie des objets résolus

Le comportement par défaut de la [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe consiste à résoudre les implémentations de plateforme en tant que singletons. Par conséquent, les implémentations de plateforme vont perdurer pendant toute la durée de vie d’une application.

Ce comportement est spécifié avec l' [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) argument facultatif sur les [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthodes et. L' [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) énumération définit deux membres :

- `GlobalInstance`, qui retourne l’implémentation de plateforme en tant que singleton.
- `NewInstance`, qui retourne une nouvelle instance de l’implémentation de la plateforme. L’application est alors responsable de la gestion de la durée de vie de l’instance de l’implémentation de la plateforme.

Les [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthodes et définissent leurs arguments facultatifs sur [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget) , et les implémentations de plateforme sont donc toujours résolues en tant que singletons. Ce comportement peut être modifié, afin que de nouvelles instances d’implémentations de plateforme soient créées, en spécifiant [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) comme arguments pour les `Get<T>` `Resolve<T>` méthodes et :

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

Dans cet exemple, [`DependencyService`](xref:Xamarin.Forms.DependencyService) crée une nouvelle instance de l’implémentation de la plateforme pour l' `ITextToSpeechService` interface. Tous les appels suivants pour résoudre `ITextToSpeechService` vont également créer de nouvelles instances.

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

- [Démonstrations de DependencyService (exemple)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)

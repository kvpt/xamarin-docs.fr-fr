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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70199223"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Inscription et résolution de Xamarin.Forms DependencyService

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Lors de l’utilisation [`DependencyService`](xref:Xamarin.Forms.DependencyService) de la Xamarin.Forms pour invoquer `DependencyService`la fonctionnalité de la plate-forme native, les implémentations de la plate-forme doivent être enregistrées avec le , puis résolu à partir de code partagé pour les invoquer.

## <a name="register-platform-implementations"></a>Inscrire des implémentations d’une plateforme

Les implémentations [`DependencyService`](xref:Xamarin.Forms.DependencyService) de la plate-forme doivent être enregistrées auprès de la sorte que Xamarin.Forms peut les localiser au moment de l’exécution.

L’inscription peut [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)être effectuée [`Register`](xref:Xamarin.Forms.DependencyService.Register*) avec le , ou avec les méthodes.

> [!IMPORTANT]
> Les builds de version des projets UWP qui utilisent [`Register`](xref:Xamarin.Forms.DependencyService.Register*) la compilation native .NET devraient enregistrer les implémentations de plate-forme avec les méthodes.

### <a name="registration-by-attribute"></a>Inscription par attribut

Le [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) peut être utilisé pour enregistrer [`DependencyService`](xref:Xamarin.Forms.DependencyService)une mise en œuvre de la plate-forme avec le . L’attribut indique que le type spécifié fournit une implémentation concrète de l’interface.

L’exemple suivant [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) montre l’utilisation de `IDeviceOrientationService` l’enregistrement de la mise en œuvre iOS de l’interface:

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

Dans cet exemple, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) les `DeviceOrientationService` registres avec le [`DependencyService`](xref:Xamarin.Forms.DependencyService). Ce qui aboutit à l’inscription du type concret auprès de l’interface qu’il implémente.

De même, les `IDeviceOrientationService` implémentations de l’interface sur d’autres plates-formes doivent être enregistrées auprès de la [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

> [!NOTE]
> L’inscription [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) avec le est effectuée au niveau de l’espace de nom.

### <a name="registration-by-method"></a>Inscription par méthode

Les [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) méthodes peuvent être utilisées pour [`DependencyService`](xref:Xamarin.Forms.DependencyService)enregistrer une mise en œuvre de la plate-forme avec le .

L’exemple suivant [`Register`](xref:Xamarin.Forms.DependencyService.Register*) montre l’utilisation de la `IDeviceOrientationService` méthode pour enregistrer la mise en œuvre iOS de l’interface :

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

Dans cet exemple, la [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthode enregistre `DeviceOrientationService`le `IDeviceOrientationService` type concret, , contre l’interface. Alternativement, une surcharge [`Register`](xref:Xamarin.Forms.DependencyService.Register*) de la méthode peut être utilisée [`DependencyService`](xref:Xamarin.Forms.DependencyService)pour enregistrer une mise en œuvre de la plate-forme avec le :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

Dans cet exemple, la [`Register`](xref:Xamarin.Forms.DependencyService.Register*) `DeviceOrientationService` méthode [`DependencyService`](xref:Xamarin.Forms.DependencyService)enregistre le . Ce qui aboutit à l’inscription du type concret auprès de l’interface qu’il implémente.

De même, les `IDeviceOrientationService` implémentations de l’interface sur d’autres plates-formes peuvent être enregistrées avec les [`Register`](xref:Xamarin.Forms.DependencyService.Register*) méthodes.

> [!IMPORTANT]
> L’enregistrement [`Register`](xref:Xamarin.Forms.DependencyService.Register*) avec les méthodes doit être effectué dans des projets de plate-forme, avant que la fonctionnalité fournie par la mise en œuvre de la plate-forme soit invoquée à partir du code partagé.

## <a name="resolve-the-platform-implementations"></a>Résoudre les implémentations d’une plateforme

Les implémentations d’une plateforme doivent être résolues avant d’être appelées. Ceci est généralement effectué dans [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) le code partagé en utilisant la méthode. Cependant, il peut également [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) être accompli avec la méthode.

Par défaut, [`DependencyService`](xref:Xamarin.Forms.DependencyService) la volonté ne résoudra que les implémentations de plate-forme qui ont des constructeurs sans paramètres. Toutefois, il est possible d’injecter une méthode de résolution de dépendance dans Xamarin.Forms qui utilise un conteneur d’injection de dépendance ou des méthodes de fabrique pour résoudre les implémentations de plateforme. Cette approche permet de résoudre les implémentations de plateforme qui ont des constructeurs avec des paramètres. Pour plus d’informations, consultez [Résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> Invoquer une mise en œuvre [`DependencyService`](xref:Xamarin.Forms.DependencyService) de plate-forme qui n’a pas été enregistrée avec le se traduira par un `NullReferenceException` être jeté.

### <a name="resolve-using-the-getlttgt-method"></a>Résoudre avec la méthode Get&lt;T&gt;

La [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode récupère la mise `T` en œuvre de la plate-forme de l’interface au moment de l’exécution, et crée une instance de celui-ci comme un singleton. Cette instance va perdurer pendant tout la durée de vie de l’application, et tous les appels suivants pour résoudre la même implémentation de la plateforme vont récupérer la même instance.

Le code suivant montre un [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) exemple d’appel de la méthode pour résoudre l’interface, `IDeviceOrientationService` puis d’invoquer sa `GetOrientation` méthode:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> La [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) méthode crée une instance de `T` la mise en œuvre de la plate-forme de l’interface en tant que singleton, par défaut. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [Gérer la durée de vie des objets résolus](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Résoudre avec la méthode Resolve&lt;T&gt;

La [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode récupère la mise `T` en œuvre de la plate-forme de l’interface au [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) moment de l’exécution, en utilisant une méthode de résolution de dépendance qui a été injecté dans Xamarin.Forms avec la classe. Si une méthode de résolution de dépendance n’a `Resolve<T>` pas été injectée [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) dans Xamarin.Forms, la méthode se repliera sur l’appel de la méthode pour récupérer la mise en œuvre de la plate-forme. Pour plus d’informations sur l’injection d’une méthode de résolution des dépendances dans Xamarin.Forms, consultez [Résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

Le code suivant montre un [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) exemple d’appel de la méthode pour résoudre l’interface, `IDeviceOrientationService` puis d’invoquer sa `GetOrientation` méthode:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Vous pouvez aussi condenser ce code sur une seule ligne :

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Lorsque [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) la méthode revient [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) à appeler la méthode, elle `T` crée une instance de la mise en œuvre de la plate-forme de l’interface comme un singleton, par défaut. Vous pouvez cependant changer ce comportement. Pour plus d’informations, consultez [Gérer la durée de vie des objets résolus](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gérer la durée de vie des objets résolus

Le comportement par [`DependencyService`](xref:Xamarin.Forms.DependencyService) défaut de la classe est de résoudre les implémentations de la plate-forme en tant que singletons. Par conséquent, les implémentations de plateforme vont perdurer pendant toute la durée de vie d’une application.

Ce comportement est [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) spécifié avec [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) l’argument facultatif sur le et [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) les méthodes. L’énumération [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) définit deux membres :

- `GlobalInstance`, qui retourne l’implémentation de plateforme en tant que singleton.
- `NewInstance`, qui retourne une nouvelle instance de l’implémentation de la plateforme. L’application est alors responsable de la gestion de la durée de vie de l’instance de l’implémentation de la plateforme.

Les [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) deux et les méthodes [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget)définir leurs arguments facultatifs à , et donc les implémentations de plate-forme sont toujours résolus en singletons. Ce comportement peut être modifié, de sorte que de nouveaux [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) cas d’implémentations de plate-forme sont créés, en spécifiant comme arguments à la et `Get<T>` `Resolve<T>` les méthodes:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

Dans cet exemple, le [`DependencyService`](xref:Xamarin.Forms.DependencyService) crée un nouvel `ITextToSpeechService` exemple de la mise en œuvre de la plate-forme pour l’interface. Tous les appels suivants pour résoudre `ITextToSpeechService` vont également créer de nouvelles instances.

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

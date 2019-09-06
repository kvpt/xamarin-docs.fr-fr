---
title: Résolution des dépendances dans Xamarin.Forms
description: Cet article explique comment injecter une méthode de résolution de dépendance dans Xamarin.Forms afin que le conteneur d’injection de dépendance d’une application peut contrôler la création et la durée de vie des convertisseurs personnalisés, des effets et des implémentations de DependencyService.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
ms.openlocfilehash: 6df393d59207cea9c316189059f8d0e08a5e5137
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290065"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Résolution des dépendances dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_Cet article explique comment injecter une méthode de résolution de dépendance dans Xamarin.Forms afin que le conteneur d’injection de dépendance d’une application peut contrôler la création et la durée de vie des convertisseurs personnalisés, des effets et des implémentations de DependencyService. Les exemples de code dans cet article sont extraites de la [résolution des dépendances à l’aide de conteneurs](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) exemple._

Dans le contexte d’une application Xamarin.Forms qui utilise le modèle Model-View-ViewModel (MVVM), un conteneur d’injection de dépendance peut être utilisé pour l’inscription et la résolution des modèles de vue et de l’inscription des services et de les injecter dans les modèles de vue. Lors de la création du modèle de vue, le conteneur injecte toutes les dépendances requises. Si ces dépendances n’ont pas été créés, le conteneur crée et résout d’abord les dépendances. Pour plus d’informations sur l’injection de dépendances, ainsi que des exemples d’injection de dépendances dans les modèles de vue, consultez [l’Injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Contrôle sur la création et la durée de vie de types dans les projets de plateforme est généralement effectuée par Xamarin.Forms, qui utilise le `Activator.CreateInstance` méthode pour créer des instances de convertisseurs personnalisés, des effets, et [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implémentations. Malheureusement, cela limite le contrôle du développeur sur la création et la durée de vie de ces types, ainsi que d’injecter des dépendances. Ce comportement peut être modifié en injectant une méthode de résolution de dépendance dans Xamarin.Forms qui contrôle comment les types seront créées : soit en conteneur d’injection de dépendance de l’application, soit par Xamarin.Forms. Toutefois, notez qu’il n’existe aucune exigence pour injecter une méthode de résolution de dépendance dans Xamarin.Forms. Xamarin.Forms continuera à créer et gérer la durée de vie des types dans les projets de plateforme, si une méthode de résolution de dépendance n’est pas injectée.

> [!NOTE]
> Si cet article se concentre sur l’injection d’une méthode de résolution de dépendance dans Xamarin.Forms qui résout les types inscrits à l’aide d’un conteneur d’injection de dépendance, il est également possible d’injecter une méthode de résolution de dépendance qui utilise des méthodes de fabrique pour résoudre types inscrits. Pour plus d’informations, consultez le [résolution des dépendances à l’aide de méthodes de fabrique](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) exemple.

## <a name="injecting-a-dependency-resolution-method"></a>Injection d’une méthode de résolution de dépendance

Le [ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver) classe offre la possibilité d’injecter une méthode de résolution de dépendance dans Xamarin.Forms, à l’aide de la [ `ResolveUsing` ](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) (méthode). Alors, lorsque Xamarin.Forms a besoin d’une instance d’un type particulier, la méthode de résolution de dépendance est la possibilité de fournir l’instance. Si la méthode de résolution de dépendance retourne `null` pour un type demandé, repasse Xamarin.Forms à essayer de créer le type de l’instance elle-même à l’aide de la `Activator.CreateInstance` (méthode).

L’exemple suivant montre comment définir la méthode de résolution de dépendance avec le [ `ResolveUsing` ](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) méthode :

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

Dans cet exemple, la méthode de résolution de dépendance est définie à une expression lambda qui utilise le conteneur d’injection de dépendance Autofac pour résoudre tous les types qui ont été inscrits auprès du conteneur. Sinon, `null` s’affichera, ce qui entraînera Xamarin.Forms tente de résoudre le type.

> [!NOTE]
> L’API utilisée par un conteneur d’injection de dépendance est spécifique au conteneur. Les exemples de code dans cet article utilisent Autofac comme un conteneur d’injection de dépendance, qui fournit le `IContainer` et `ContainerBuilder` types. Conteneurs d’injection de dépendance de remplacement peut également être utilisés, mais seraient qu’ils recourent différentes API sont présentées ici.

Notez qu’il n’existe aucune exigence pour définir la méthode de résolution de dépendance au cours du démarrage de l’application. Elle peut être définie à tout moment. La seule contrainte est que Xamarin.Forms doit savoir sur la méthode de résolution de dépendance au moment où l’application tente d’utiliser des types stockés dans le conteneur d’injection de dépendance. Par conséquent, s’il existe des services dans le conteneur d’injection de dépendance qui nécessite l’application lors du démarrage, la méthode de résolution de dépendance aura à définir au début de cycle de vie de l’application. De même, si le conteneur d’injection de dépendance gère la création et la durée de vie d’un particulier [ `Effect` ](xref:Xamarin.Forms.Effect), Xamarin.Forms devez connaître à propos de la méthode de résolution de dépendance avant d’essayer de créer une vue qui qui utilise `Effect`.

> [!WARNING]
> L’inscription et la résolution des types avec un conteneur d’injection de dépendance a un impact en raison de l’utilisation du conteneur de la réflexion pour la création de chaque type, en particulier si les dépendances sont en cours de reconstruction pour chaque navigation entre les pages dans l’application sur les performances. S’il existe de nombreuses ou approfondis des dépendances, le coût de création peut augmenter considérablement.

## <a name="registering-types"></a>L’inscription des types

Types doivent être inscrits avec le conteneur d’injection de dépendance avant de pouvoir les résoudre par le biais de la méthode de résolution de dépendance. L’exemple de code suivant montre les méthodes d’inscription que l’exemple d’application expose dans le `App` (classe), pour le conteneur Autofac :

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

Lorsqu’une application utilise une méthode de résolution de dépendance pour résoudre des types à partir d’un conteneur, les enregistrements de type sont généralement effectuées à partir de projets de plateforme. Ainsi, les projets de plateforme inscrire des types de convertisseurs personnalisés, des effets, et [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implémentations.

Après l’enregistrement du type à partir d’un projet de plateforme, le `IContainer` objet doit être généré, ce qui s’effectue en appelant le `BuildContainer` (méthode). Cette méthode appelle de Autofac `Build` méthode sur le `ContainerBuilder` instance, ce qui génère un nouveau conteneur d’injection de dépendance qui contient les enregistrements qui ont été apportées.

Dans les sections qui suivent, un `Logger` classe qui implémente le `ILogger` interface est injectée dans les constructeurs de classe. Le `Logger` à l’aide des fonctionnalités de journalisation simple implémente classe le `Debug.WriteLine` (méthode) et est utilisé pour illustrer la façon dont les services peuvent être injectées dans renderers personnalisés, des effets, et [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implémentations.

### <a name="registering-custom-renderers"></a>L’inscription des convertisseurs personnalisés

L’exemple d’application comprend une page qui lit les vidéos web, dont la source XAML est illustrée dans l’exemple suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

Le `VideoPlayer` vue est implémentée sur chaque plateforme par un `VideoPlayerRenderer` (classe), qui fournit les fonctionnalités de la vidéo. Pour plus d’informations sur ces classes de convertisseur personnalisé, consultez [implémentation d’un lecteur vidéo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

Sur iOS et Universal Windows Platform (UWP), le `VideoPlayerRenderer` classes ont le constructeur suivant, ce qui nécessite un `ILogger` argument :

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Sur toutes les plateformes, l’inscription de type avec le conteneur d’injection de dépendance est réalisée par le `RegisterTypes` (méthode), qui est appelé avant la plateforme de chargement de l’application avec le `LoadApplication(new App())` (méthode). L’exemple suivant montre le `RegisterTypes` méthode sur la plateforme iOS :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

Dans cet exemple, le `Logger` type concret est inscrit via un mappage par rapport à son type d’interface et le `VideoPlayerRenderer` type est enregistré directement sans un mappage d’interface. Lorsque l’utilisateur accède à la page contenant le `VideoPlayer` vue, la méthode de résolution de dépendance sera appelée pour résoudre le `VideoPlayerRenderer` type à partir du conteneur d’injection de dépendance, qui sera également résoudre et injecter le `Logger` taper dans le `VideoPlayerRenderer` constructeur.

Le `VideoPlayerRenderer` constructeur sur la plateforme Android est légèrement plus compliqué car il nécessite un `Context` argument outre le `ILogger` argument :

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

L’exemple suivant montre le `RegisterTypes` méthode sur la plateforme Android :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Dans cet exemple, le `App.RegisterTypeWithParameters` méthode inscrit le `VideoPlayerRenderer` avec le conteneur d’injection de dépendance. La méthode d’inscription garantit que le `MainActivity` instance est injectée comme le `Context` argument et que le `Logger` type est injecté comme le `ILogger` argument.

### <a name="registering-effects"></a>L’inscription d’effets

L’exemple d’application comprend une page qui utilise une pression tactile suivi effet à faire glisser [ `BoxView` ](xref:Xamarin.Forms.BoxView) instances autour de la page. Le [ `Effect` ](xref:Xamarin.Forms.Effect) est ajouté à la `BoxView` utilisant le code suivant :

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

Le `TouchEffect` classe est un [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) qui est implémentée sur chaque plateforme par un `TouchEffect` classe qui a un `PlatformEffect`. La plateforme `TouchEffect` classe fournit les fonctionnalités permettant de faire glisser le `BoxView` autour de la page. Pour plus d’informations sur ces classes d’effet, consultez [appel d’événements à partir des effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Sur toutes les plateformes, les `TouchEffect` classe possède le constructeur suivant, ce qui nécessite un `ILogger` argument :

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Sur toutes les plateformes, l’inscription de type avec le conteneur d’injection de dépendance est réalisée par le `RegisterTypes` (méthode), qui est appelé avant la plateforme de chargement de l’application avec le `LoadApplication(new App())` (méthode). L’exemple suivant montre le `RegisterTypes` méthode sur la plateforme Android :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

Dans cet exemple, le `Logger` type concret est inscrit via un mappage par rapport à son type d’interface et le `TouchEffect` type est enregistré directement sans un mappage d’interface. Lorsque l’utilisateur accède à la page contenant un [ `BoxView` ](xref:Xamarin.Forms.BoxView) instance qui a le `TouchEffect` attaché à ce dernier, la méthode de résolution de dépendance sera appelée pour résoudre la plateforme `TouchEffect` type à partir de la dépendance conteneur d’injection, qui sera également résoudre et injecter le `Logger` taper dans le `TouchEffect` constructeur.

### <a name="registering-dependencyservice-implementations"></a>L’inscription d’implémentations de DependencyService

L’exemple d’application comprend une page qui utilise [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implémentations sur chaque plateforme pour autoriser l’utilisateur à choisir une photo à partir de la bibliothèque d’images de l’appareil. Le `IPhotoPicker` interface définit la fonctionnalité qui est implémentée par le `DependencyService` implémentations et est illustré dans l’exemple suivant :

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

Dans chaque projet de plateforme, le `PhotoPicker` la classe implémente le `IPhotoPicker` interface à l’aide des API de la plateforme. Pour plus d’informations sur ces services de dépendance, consultez [sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Sur iOS et UWP, le `PhotoPicker` classes ont le constructeur suivant, ce qui nécessite un `ILogger` argument :

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Sur toutes les plateformes, l’inscription de type avec le conteneur d’injection de dépendance est réalisée par le `RegisterTypes` (méthode), qui est appelé avant la plateforme de chargement de l’application avec le `LoadApplication(new App())` (méthode). L’exemple suivant montre le `RegisterTypes` méthode sur UWP :

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

Dans cet exemple, le `Logger` type concret est inscrit via un mappage par rapport à son type d’interface et le `PhotoPicker` type est également enregistré via un mappage d’interface.

Le `PhotoPicker` constructeur sur la plateforme Android est légèrement plus compliqué car il nécessite un `Context` argument outre le `ILogger` argument :

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

L’exemple suivant montre le `RegisterTypes` méthode sur la plateforme Android :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Dans cet exemple, le `App.RegisterTypeWithParameters` méthode inscrit le `PhotoPicker` avec le conteneur d’injection de dépendance. La méthode d’inscription garantit que le `MainActivity` instance est injectée comme le `Context` argument et que le `Logger` type est injecté comme le `ILogger` argument.

Lorsque l’utilisateur accède à la page de sélection de photo et choisit sélectionner une photo, le `OnSelectPhotoButtonClicked` gestionnaire est exécuté :

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

Lorsque le [ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) méthode est appelée, la méthode de résolution de dépendance sera appelée pour résoudre le `PhotoPicker` type à partir du conteneur d’injection de dépendance, qui sera également résoudre et injecter le `Logger` type dans le `PhotoPicker` constructeur.

> [!NOTE]
> Le [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) méthode doit être utilisée lors de la résolution d’un type à partir du conteneur d’injection de dépendance de l’application via le [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

## <a name="related-links"></a>Liens connexes

- [Résolution des dépendances à l’aide de conteneurs (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [Injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implémentation d’un lecteur vidéo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)

---
titre : « résolution de dépendance dans Xamarin.Forms » Description : cet article explique comment injecter une méthode de résolution de dépendance dans Xamarin.Forms afin que le conteneur d’injection de dépendances d’une application contrôle la création et la durée de vie des convertisseurs, des effets et des implémentations de DependencyService personnalisés.
ms. Prod : ms. AssetID : ms. Technology : Auteur : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="dependency-resolution-in-xamarinforms"></a>Résolution des dépendances dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_Cet article explique comment injecter une méthode de résolution des dépendances dans Xamarin.Forms afin que le conteneur d’injection de dépendances d’une application contrôle la création et la durée de vie des convertisseurs, des effets et des implémentations de DependencyService personnalisés. Les exemples de code de cet article sont tirés de l’exemple [résolution des dépendances à l’aide de conteneurs](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) ._

Dans le contexte d’une Xamarin.Forms application qui utilise le modèle MVVM (Model-View-ViewModel), un conteneur d’injection de dépendances peut être utilisé pour l’inscription et la résolution des modèles de vue, ainsi que pour l’inscription des services et leur injection dans des modèles de vue. Pendant la création du modèle de vue, le conteneur injecte toutes les dépendances requises. Si ces dépendances n’ont pas été créées, le conteneur crée et résout d’abord les dépendances. Pour plus d’informations sur l’injection de dépendances, y compris des exemples d’injection de dépendances dans les modèles de vue, consultez [injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Le contrôle sur la création et la durée de vie des types dans les projets de plateforme est traditionnellement effectué par Xamarin.Forms , qui utilise la `Activator.CreateInstance` méthode pour créer des instances de convertisseurs, d’effets et d' [`DependencyService`](xref:Xamarin.Forms.DependencyService) implémentations personnalisés. Malheureusement, cela limite le contrôle du développeur sur la création et la durée de vie de ces types, ainsi que la possibilité d’injecter des dépendances dans ces types. Ce comportement peut être modifié en injectant une méthode de résolution de dépendance dans Xamarin.Forms qui contrôle la manière dont les types sont créés, soit par le conteneur d’injection de dépendances de l’application, soit par Xamarin.Forms . Toutefois, Notez qu’il n’est pas nécessaire d’injecter une méthode de résolution des dépendances dans Xamarin.Forms . Xamarin.Formsva continuer à créer et à gérer la durée de vie des types dans les projets de plateforme si une méthode de résolution de dépendance n’est pas injectée.

> [!NOTE]
> Bien que cet article se concentre sur l’injection d’une méthode de résolution de dépendance dans Xamarin.Forms qui résout les types inscrits à l’aide d’un conteneur d’injection de dépendance, il est également possible d’injecter une méthode de résolution de dépendance qui utilise des méthodes de fabrique pour résoudre les types inscrits. Pour plus d’informations, consultez l’exemple [résolution des dépendances à l’aide des méthodes de fabrique](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) .

## <a name="injecting-a-dependency-resolution-method"></a>Injection d’une méthode de résolution de dépendance

La [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) classe offre la possibilité d’injecter une méthode de résolution de dépendance dans Xamarin.Forms , à l’aide de la [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) méthode. Ensuite, lorsque Xamarin.Forms requiert une instance d’un type particulier, la méthode de résolution des dépendances a la possibilité de fournir l’instance. Si la méthode de résolution des dépendances retourne `null` pour un type demandé, revient Xamarin.Forms à essayer de créer l’instance de type elle-même à l’aide de la `Activator.CreateInstance` méthode.

L’exemple suivant montre comment définir la méthode de résolution des dépendances à l’aide de la [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) méthode :

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

Dans cet exemple, la méthode de résolution des dépendances est définie sur une expression lambda qui utilise le conteneur d’injection de dépendances Autofac pour résoudre tous les types qui ont été inscrits auprès du conteneur. Sinon, est `null` retourné, ce qui entraîne Xamarin.Forms la tentative de résolution du type.

> [!NOTE]
> L’API utilisée par un conteneur d’injection de dépendances est spécifique au conteneur. Les exemples de code de cet article utilisent Autofac comme conteneur d’injection de dépendances, qui fournit les `IContainer` `ContainerBuilder` types et. Vous pouvez également utiliser des conteneurs d’injection de dépendances alternatifs, mais utilisez des API différentes de celles présentées ici.

Notez qu’il n’est pas nécessaire de définir la méthode de résolution des dépendances au démarrage de l’application. Elle peut être définie à tout moment. La seule contrainte est que Xamarin.Forms doit connaître la méthode de résolution des dépendances au moment où l’application tente d’utiliser les types stockés dans le conteneur d’injection de dépendances. Par conséquent, s’il existe des services dans le conteneur d’injection de dépendances requis par l’application au cours du démarrage, la méthode de résolution des dépendances doit être définie au début du cycle de vie de l’application. De même, si le conteneur d’injection de dépendances gère la création et la durée de vie d’un particulier [`Effect`](xref:Xamarin.Forms.Effect) , doit Xamarin.Forms en savoir plus sur la méthode de résolution des dépendances avant de tenter de créer une vue qui l’utilise `Effect` .

> [!WARNING]
> L’inscription et la résolution des types avec un conteneur d’injection de dépendances ont un coût en matière de performances en raison de l’utilisation par le conteneur de la réflexion pour la création de chaque type, en particulier si les dépendances sont reconstruites pour chaque navigation entre les pages de l’application. S’il existe de nombreuses dépendances ou des dépendances profondes, le coût de création peut augmenter de manière significative.

## <a name="registering-types"></a>Inscrire des types

Les types doivent être inscrits auprès du conteneur d’injection de dépendances avant de pouvoir être résolus par le biais de la méthode de résolution des dépendances. L’exemple de code suivant montre les méthodes d’inscription exposées par l’exemple d’application dans la `App` classe, pour le conteneur Autofac :

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

Quand une application utilise une méthode de résolution des dépendances pour résoudre les types d’un conteneur, les inscriptions de type sont généralement effectuées à partir de projets de plateforme. Cela permet aux projets de plateforme d’inscrire des types pour les convertisseurs, les effets et les [`DependencyService`](xref:Xamarin.Forms.DependencyService) implémentations personnalisés.

Après l’inscription de type à partir d’un projet de plateforme, l' `IContainer` objet doit être généré, ce qui est effectué en appelant la `BuildContainer` méthode. Cette méthode appelle `Build` la méthode de Autofac sur l' `ContainerBuilder` instance, qui génère un nouveau conteneur d’injection de dépendances qui contient les inscriptions effectuées.

Dans les sections qui suivent, une `Logger` classe qui implémente l' `ILogger` interface est injectée dans les constructeurs de classe. La `Logger` classe implémente la fonctionnalité de journalisation simple à l’aide de la `Debug.WriteLine` méthode et est utilisée pour montrer comment les services peuvent être injectés dans des convertisseurs, des effets et des [`DependencyService`](xref:Xamarin.Forms.DependencyService) implémentations personnalisés.

### <a name="registering-custom-renderers"></a>Inscription de convertisseurs personnalisés

L’exemple d’application comprend une page qui lit les vidéos Web, dont la source XAML est présentée dans l’exemple suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

La `VideoPlayer` vue est implémentée sur chaque plateforme par une `VideoPlayerRenderer` classe, qui fournit les fonctionnalités permettant de visionner la vidéo. Pour plus d’informations sur ces classes de convertisseur personnalisées, consultez [implémentation d’un lecteur vidéo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

Sur iOS et le plateforme Windows universelle (UWP), les `VideoPlayerRenderer` classes ont le constructeur suivant, qui requiert un `ILogger` argument :

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Sur toutes les plateformes, l’inscription de type avec le conteneur d’injection de dépendances est effectuée par la `RegisterTypes` méthode, qui est appelée avant la plateforme chargeant l’application avec la `LoadApplication(new App())` méthode. L’exemple suivant illustre la `RegisterTypes` méthode sur la plateforme iOS :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

Dans cet exemple, le `Logger` type concret est inscrit via un mappage par rapport à son type interface et le `VideoPlayerRenderer` type est inscrit directement sans mappage d’interface. Lorsque l’utilisateur accède à la page contenant la `VideoPlayer` vue, la méthode de résolution des dépendances est appelée pour résoudre le `VideoPlayerRenderer` type à partir du conteneur d’injection de dépendances, ce qui permet également de résoudre et d’injecter le `Logger` type dans le `VideoPlayerRenderer` constructeur.

Le `VideoPlayerRenderer` constructeur sur la plateforme Android est légèrement plus compliqué, car il nécessite un `Context` argument en plus de l' `ILogger` argument :

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

L’exemple suivant illustre la `RegisterTypes` méthode sur la plateforme Android :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Dans cet exemple, la `App.RegisterTypeWithParameters` méthode inscrit le `VideoPlayerRenderer` avec le conteneur d’injection de dépendances. La méthode d’inscription garantit que l' `MainActivity` instance sera injectée comme `Context` argument et que le `Logger` type sera injecté comme `ILogger` argument.

### <a name="registering-effects"></a>Enregistrement des effets

L’exemple d’application comprend une page qui utilise un effet tactile pour faire glisser des [`BoxView`](xref:Xamarin.Forms.BoxView) instances dans la page. Le [`Effect`](xref:Xamarin.Forms.Effect) est ajouté au `BoxView` à l’aide du code suivant :

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

La `TouchEffect` classe est une [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) qui est implémentée sur chaque plateforme par une `TouchEffect` classe qui est un `PlatformEffect` . La `TouchEffect` classe Platform fournit les fonctionnalités permettant de faire glisser le `BoxView` autour de la page. Pour plus d’informations sur ces classes d’effet, consultez [appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Sur toutes les plateformes, la `TouchEffect` classe a le constructeur suivant, qui requiert un `ILogger` argument :

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Sur toutes les plateformes, l’inscription de type avec le conteneur d’injection de dépendances est effectuée par la `RegisterTypes` méthode, qui est appelée avant la plateforme chargeant l’application avec la `LoadApplication(new App())` méthode. L’exemple suivant illustre la `RegisterTypes` méthode sur la plateforme Android :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

Dans cet exemple, le `Logger` type concret est inscrit via un mappage par rapport à son type interface et le `TouchEffect` type est inscrit directement sans mappage d’interface. Lorsque l’utilisateur accède à la page contenant une [`BoxView`](xref:Xamarin.Forms.BoxView) instance à laquelle est `TouchEffect` attaché, la méthode de résolution des dépendances est appelée pour résoudre le `TouchEffect` type de plateforme à partir du conteneur d’injection de dépendance, ce qui permet également de résoudre et d’injecter le `Logger` type dans le `TouchEffect` constructeur.

### <a name="registering-dependencyservice-implementations"></a>Inscription des implémentations de DependencyService

L’exemple d’application comprend une page qui utilise [`DependencyService`](xref:Xamarin.Forms.DependencyService) des implémentations sur chaque plateforme pour permettre à l’utilisateur de choisir une photo dans la bibliothèque d’images de l’appareil. L' `IPhotoPicker` interface définit les fonctionnalités implémentées par les `DependencyService` implémentations et est illustrée dans l’exemple suivant :

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

Dans chaque projet de plateforme, la `PhotoPicker` classe implémente l' `IPhotoPicker` interface à l’aide des API de plateforme. Pour plus d’informations sur ces services de dépendance, consultez [sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Sur iOS et UWP, les `PhotoPicker` classes comportent le constructeur suivant, qui requiert un `ILogger` argument :

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

Sur toutes les plateformes, l’inscription de type avec le conteneur d’injection de dépendances est effectuée par la `RegisterTypes` méthode, qui est appelée avant la plateforme chargeant l’application avec la `LoadApplication(new App())` méthode. L’exemple suivant illustre la `RegisterTypes` méthode sur UWP :

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

Dans cet exemple, le `Logger` type concret est inscrit via un mappage par rapport à son type interface, et le `PhotoPicker` type est également inscrit via un mappage d’interface.

Le `PhotoPicker` constructeur sur la plateforme Android est légèrement plus compliqué, car il nécessite un `Context` argument en plus de l' `ILogger` argument :

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

L’exemple suivant illustre la `RegisterTypes` méthode sur la plateforme Android :

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

Dans cet exemple, la `App.RegisterTypeWithParameters` méthode inscrit le `PhotoPicker` avec le conteneur d’injection de dépendances. La méthode d’inscription garantit que l' `MainActivity` instance sera injectée comme `Context` argument et que le `Logger` type sera injecté comme `ILogger` argument.

Lorsque l’utilisateur accède à la page de sélection de la photo et choisit de sélectionner une photo, le `OnSelectPhotoButtonClicked` gestionnaire est exécuté :

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

Lorsque la [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode est appelée, la méthode de résolution des dépendances est appelée pour résoudre le `PhotoPicker` type à partir du conteneur d’injection de dépendances, ce qui permet également de résoudre et d’injecter le `Logger` type dans le `PhotoPicker` constructeur.

> [!NOTE]
> La [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) méthode doit être utilisée lors de la résolution d’un type à partir du conteneur d’injection de dépendances de l’application via le [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

## <a name="related-links"></a>Liens connexes

- [Résolution des dépendances à l’aide de conteneurs (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [Injection de dépendances](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implémentation d’un lecteur vidéo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Appel d’événements à partir d’effets](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)

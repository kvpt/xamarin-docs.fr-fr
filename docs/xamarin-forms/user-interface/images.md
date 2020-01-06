---
title: Images dans Xamarin.Forms
description: Images peuvent être partagées entre les plateformes avec Xamarin.Forms, ils peuvent être chargés spécifiquement pour chaque plateforme, ou ils peuvent être téléchargés pour l’affichage.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 255d3f2f532e4899b1a890405af942a7ca2da8ea
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490149"
---
# <a name="images-in-xamarinforms"></a>Images dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Images peuvent être partagées entre les plateformes avec Xamarin.Forms, ils peuvent être chargés spécifiquement pour chaque plateforme, ou ils peuvent être téléchargés pour l’affichage._

Les images sont une composante essentielle de la navigation dans une application, l’utilisation et de personnalisation. Applications de Xamarin.Forms doivent être en mesure de partager des images sur toutes les plateformes, mais peut également afficher des images différentes sur chaque plateforme.

Spécifique à la plateforme images est également requis pour les icônes et écrans de démarrage ; ceux-ci doivent être configurés sur une base par plateforme.

## <a name="display-images"></a>Afficher les images

Xamarin.Forms utilise le [ `Image` ](xref:Xamarin.Forms.Image) pour afficher les images sur une page. Il possède deux propriétés importantes :

- [`Source`](xref:Xamarin.Forms.Image.Source) -Un [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) instance, fichier, Uri ou une ressource, qui définit l’image à afficher.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect) -Comment dimensionner l’image dans les limites, qu'il est affiché dans (que ce soit pour stretch, rogner ou letterbox).

[`ImageSource`](xref:Xamarin.Forms.ImageSource) instances peuvent être obtenus à l’aide des méthodes statiques pour chaque type de source de l’image :

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) -Nécessite un nom de fichier ou le chemin d’accès qui peut être résolu sur chaque plateforme.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) -Nécessite un objet Uri, par ex.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) -Nécessite un identificateur de ressource dans un fichier d’image incorporé dans l’application ou d’un projet de bibliothèque .NET Standard, avec un **Build Action : EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) -Requiert un flux qui fournit des données d’image.

Le [ `Aspect` ](xref:Xamarin.Forms.Image.Aspect) propriété détermine comment l’image est redimensionnée pour s’ajuster à la zone d’affichage :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -Étire l’image remplisse complètement et exactement la zone d’affichage. Cela peut entraîner l’image est déformée.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Découpe l’image afin qu’il remplisse la zone d’affichage tout en conservant l’aspect (ie. sans distorsion).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -Partout l’image (si nécessaire) afin que l’image entière s’adapte à la zone d’affichage, avec espace ajouté pour le haut/bas ou les côtés selon que l’image est largeur ou hauteur.

Les images peuvent être chargées à partir d’un [fichier local](#local-images), d’une [ressource incorporée](#embedded-images), [téléchargées](#download-images)ou chargées à partir d’un flux. En outre, les icônes de police peuvent être affichées par la vue [`Image`](xref:Xamarin.Forms.Image) en spécifiant les données d’icône de police dans un objet `FontImageSource`. Pour plus d’informations, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dans le guide des [polices](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Images locales

Fichiers image peuvent être ajoutées à chaque projet d’application et référencés à partir de code Xamarin.Forms partagé. Cette méthode de distribution d’images est indispensable dans le cas d’images spécifiques à la plateforme, par exemple si vous utilisez différentes résolutions sur différentes plateformes ou des conceptions qui présentent quelques variations mineures.

Pour utiliser une image unique dans toutes les applications, *le même nom de fichier doit être utilisé sur toutes les plateformes*, et il doit être un nom de ressource Android valide (ie. uniquement des lettres minuscules, des chiffres, le trait de soulignement et la période sont autorisées).

- **iOS** - le moyen de gérer et prendre en charge les images étant iOS 9 à utiliser de préférence **ensembles d’images catalogue Asset**, qui doit contenir toutes les versions d’une image qui sont nécessaires pour prendre en charge de divers périphériques et facteurs de mise à l’échelle un application. Pour plus d’informations, consultez [Ajout d’Images à une ressource catalogue Image défini](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** -placer des images dans le **ressources/drawable** répertoire avec **Action de génération : AndroidResource**. Versions haute et basse résolution d’une image peuvent également être fournies (dans correctement nommé **ressources** sous-répertoires comme **drawable ldpi**, **drawable hdpi**et **drawable xhdpi**).
- **Universal Windows Platform (UWP)** -placer des images dans le répertoire racine de l’application avec **Action de génération : contenu**.

> [!IMPORTANT]
> Avant d’iOS 9, les images ont été généralement placés dans le **ressources** dossier avec **Action de génération : BundleResource**. Toutefois, cette méthode d’utilisation des images dans une application iOS a été déconseillée par Apple. Pour plus d’informations, consultez [tailles d’Image et les noms de fichiers](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Adhérant à ces règles de dénomination des fichiers et le positionnement permet le XAML suivant charger et afficher l’image sur toutes les plateformes :

```xaml
<Image Source="waterfront.jpg" />
```

Le code c# équivalent est comme suit :

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Les captures d’écran suivantes montrent le résultat de l’affichage d’une image locale sur chaque plateforme :

[Exemple d’application ![affichant une image locale](images-images/local-sml.png)](images-images/local.png#lightbox)

Pour plus de souplesse la `Device.RuntimePlatform` propriété peut être utilisée pour sélectionner un autre fichier image ou un chemin d’accès pour certains ou l’ensemble des plateformes, comme illustré dans cet exemple de code :

```csharp
image.Source = Device.RuntimePlatform == Device.Android 
                ? ImageSource.FromFile("waterfront.jpg") 
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Pour utiliser le même nom de fichier image sur toutes les plateformes, le nom doit être valide sur toutes les plateformes. Drawables Android ont de restrictions d’affectation de noms : uniquement des lettres minuscules, des chiffres, un trait de soulignement et période sont autorisés : et pour une compatibilité multiplateforme cela doit être suivi sur toutes les autres plateformes trop. Le nom de fichier d’exemple **waterfront.png** suit les règles, mais les exemples de noms de fichiers non valides incluent « eau front.png », « WaterFront.png », « eau front.png » et « wåterfront.png ».

### <a name="native-resolutions-retina-and-high-dpi"></a>Résolutions natives (retine et haute résolution)

iOS, Android et UWP incluent la prise en charge pour les résolutions différents, où le système d’exploitation choisit l’image appropriée lors de l’exécution en fonction des capacités de l’appareil. Xamarin.Forms utilise les API des plateformes natives pour le chargement des images locales, afin qu’il prend automatiquement en charge les résolutions autre si les fichiers sont correctement nommés et situés dans le projet.

La meilleure façon de gérer les images depuis iOS 9 consiste à faire glisser des images pour chaque résolution requise pour l’ensemble d’images de catalogue asset approprié. Pour plus d’informations, consultez [Ajout d’Images à une ressource catalogue Image défini](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Avant d’iOS 9, versions rétine de l’image peut être placées dans le **ressources** dossier - deux et trois fois la résolution avec un **@2x** ou **@3x** suffixes sur le nom de fichier avant l’extension de fichier (par exemple). **myimage@2x.png** ). Toutefois, cette méthode d’utilisation des images dans une application iOS a été déconseillée par Apple. Pour plus d’informations, consultez [tailles d’Image et les noms de fichiers](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Les images Android autre résolution doivent être placés dans [répertoires spécialement nommée](https://developer.android.com/guide/practices/screens_support.html) dans le projet Android, comme indiqué dans la capture d’écran suivante :

[![l’emplacement des images à résolution multiple Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Noms de fichiers image UWP [peut être suivi du suffixe `.scale-xxx` avant l’extension de fichier](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), où `xxx` est le pourcentage de mise à l’échelle appliquée à la ressource, par exemple, **myimage.scale-200.png**. Images peuvent ensuite faire référence dans le code ou XAML sans le modificateur de mise à l’échelle, par exemple, simplement **myimage.png**. La plateforme sélectionnera le plus proche actifs approprié mise à l’échelle en fonction de la résolution actuelle de l’affichage.

### <a name="additional-controls-that-display-images"></a>Contrôles supplémentaires qui affichent des images

Certains contrôles ont des propriétés qui affichent une image, tels que :

- [`Button`](xref:Xamarin.Forms.Button) a une propriété [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) qui peut être définie sur une image bitmap à afficher sur le `Button`. Pour plus d’informations, consultez [utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button) a une propriété [`Source`](xref:Xamarin.Forms.ImageButton.Source) qui peut être définie sur l’image à afficher dans le `ImageButton`. Pour plus d’informations, consultez [définition de la source de l’image](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) a une propriété [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) qui peut être définie sur une image qui est chargée à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) a une propriété [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) qui peut être définie sur une image récupérée à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.
- [`Page`](xref:Xamarin.Forms.Page). Tout type de page qui dérive de `Page` a des propriétés [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) et [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) , qui peuvent être affectées à un fichier, à une ressource incorporée, à un URI ou à un flux. Dans certaines circonstances, par exemple quand un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) affiche un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), l’icône s’affichera si pris en charge par la plateforme.

  > [!IMPORTANT]
  > Sur iOS, le [ `Page.IconImageSource` ](xref:Xamarin.Forms.Page.IconImageSource) propriété ne peut pas être remplie à partir d’une image dans un ensemble d’images de catalogue actif. Au lieu de cela, chargez les images d’icône pour la propriété `Page.IconImageSource` à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.

## <a name="embedded-images"></a>Images incorporées

Images incorporées sont également inclus dans une application (comme les images locales), mais au lieu d’avoir une copie de l’image dans la structure de fichiers de chaque application l’image fichier est incorporé dans l’assembly en tant que ressource. Cette méthode de distribution d’images est recommandée lorsque les images identiques sont utilisées sur chaque plateforme et est particulièrement adaptée à la création de composants, comme l’image est fourni avec le code.

Pour incorporer une image dans un projet, avec le bouton droit pour ajouter de nouveaux éléments et sélectionnez l’image/s que vous souhaitez ajouter. Par défaut, l’image aura **Action de génération : aucun**; cette opération doit être définie sur **Action de génération : EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![définir l’action de génération sur la ressource incorporée](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

Le **Action de génération** peuvent être affichées et modifiées dans le **propriétés** fenêtre pour un fichier.

Dans cet exemple est l’ID de ressource **WorkingWithImages.beach.jpg**.
L’IDE a généré ce comportement par défaut en concaténant le **par défaut Namespace** pour ce projet avec le nom de fichier, à l’aide d’un point (.) entre chaque valeur.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

**Action de génération** peuvent également être affichées et modifiées dans le **propriétés** remplissage pour un fichier.
Ce panneau affiche les **ID de ressource** qui est utilisé pour référencer la ressource dans le code. Dans la capture d’écran ci-dessous, le **ID de ressource** est **WorkingWithImages.beach.jpg**.
L’IDE a généré ce comportement par défaut en concaténant le **par défaut Namespace** pour ce projet avec le nom de fichier, à l’aide d’un point (.) entre chaque valeur.
Cet ID peut être modifié dans le **propriétés** remplissage, mais pour ces exemples de la valeur **WorkingWithImages.beach.jpg** sera utilisé.

[panneau Propriétés des ressources incorporées ![](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Si vous placez des images incorporées dans des dossiers au sein de votre projet, les noms de dossiers sont également séparés par des points (.) dans l’ID de ressource. Déplacer le **beach.jpg** image dans un dossier nommé **MyImages** entraînerait un ID de ressource de **WorkingWithImages.MyImages.beach.jpg**

Le code pour charger une image incorporée transmet simplement le **ID de ressource** à la [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*) méthode comme indiqué ci-dessous :

```csharp
var embeddedImage = new Image { 
      Source = ImageSource.FromResource(
        "WorkingWithImages.beach.jpg", 
        typeof(EmbeddedImages).GetTypeInfo().Assembly
      ) };
```

> [!NOTE]
> Pour prendre en charge d’afficher des images incorporées en mode de mise en production sur la plateforme Windows universelle, il est nécessaire d’utiliser la surcharge de `ImageSource.FromResource` qui spécifie l’assembly source dans laquelle rechercher l’image.

Actuellement, il n’existe aucune conversion implicite pour les identificateurs de ressource. Au lieu de cela, vous devez utiliser [ `ImageSource.FromResource` ](xref:Xamarin.Forms.ImageSource.FromResource*) ou `new ResourceImageSource()` pour charger des images incorporées.

Les captures d’écran suivantes montrent le résultat de l’affichage d’une image incorporée sur chaque plateforme :

[Exemple d’application ![affichant une image incorporée](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Car il n’existe aucun convertisseur de type intégré de `string` à `ResourceImageSource`, ces types d’images ne peut pas être chargés en mode natif par XAML. Au lieu de cela, une simple extension de balisage XAML personnalisée peut être écrites pour charger des images à l’aide un **ID de ressource** spécifié dans XAML :

```csharp
[ContentProperty (nameof(Source))]
public class ImageResourceExtension : IMarkupExtension
{
 public string Source { get; set; }

 public object ProvideValue (IServiceProvider serviceProvider)
 {
   if (Source == null)
   {
     return null;
   }

   // Do your translation lookup here, using whatever method you require
   var imageSource = ImageSource.FromResource(Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);

   return imageSource;
 }
}
```

> [!NOTE]
> Pour prendre en charge d’afficher des images incorporées en mode de mise en production sur la plateforme Windows universelle, il est nécessaire d’utiliser la surcharge de `ImageSource.FromResource` qui spécifie l’assembly source dans laquelle rechercher l’image.

Pour utiliser cette extension, ajoutez un personnalisé `xmlns` vers le XAML, en utilisant les valeurs correctes de l’espace de noms et d’assembly pour le projet. La source de l’image peut être définie à l’aide de cette syntaxe : `{local:ImageResource WorkingWithImages.beach.jpg}`. Un exemple XAML complet est indiqué ci-dessous :

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
   xmlns="http://xamarin.com/schemas/2014/forms"
   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
   xmlns:local="clr-namespace:WorkingWithImages;assembly=WorkingWithImages"
   x:Class="WorkingWithImages.EmbeddedImagesXaml">
 <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
   <!-- use a custom Markup Extension -->
   <Image Source="{local:ImageResource WorkingWithImages.beach.jpg}" />
 </StackLayout>
</ContentPage>
```

### <a name="troubleshoot-embedded-images"></a>Résoudre les problèmes liés aux images incorporées

#### <a name="debug-code"></a>Déboguer du code

Car il est parfois difficile de comprendre pourquoi une ressource d’image particulier n’est pas en cours de chargement, le débogage code suivant peut être temporairement ajouté à une application pour vous aider à vérifier que les ressources sont correctement configurées. Il génère en sortie connues de toutes les ressources incorporées dans l’assembly donné à la **Console** pour aider à déboguer les problèmes de chargement des ressources.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(EmbeddedImages).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Images incorporées dans d’autres projets

Par défaut, le `ImageSource.FromResource` méthode se présente uniquement pour les images dans le même assembly que le code qui appelle le `ImageSource.FromResource` (méthode). À l’aide de la déboguer du code ci-dessus, vous pouvez déterminer quels assemblys contiennent une ressource spécifique en modifiant le `typeof()` instruction à un `Type` connue dans chaque assembly.

Toutefois, l’assembly source à rechercher une image incorporée peut être spécifié en tant qu’argument à la `ImageSource.FromResource` méthode :

```csharp
var imageSource = ImageSource.FromResource("filename.png", 
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Télécharger des images

Les images peuvent être téléchargés automatiquement pour l’affichage, comme indiqué dans le XAML suivant :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://xamarin.com/content/images/pages/forms/example-app.png" />
    <Label Text="example-app.png gets downloaded from xamarin.com" />
  </StackLayout>
</ContentPage>
```

Le code c# équivalent est comme suit :

```csharp
var webImage = new Image { 
     Source = ImageSource.FromUri(
        new Uri("https://xamarin.com/content/images/pages/forms/example-app.png")
     ) };
```

Le [ `ImageSource.FromUri` ](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) méthode requiert un `Uri` de l’objet et retourne un nouvel [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource) qui lit à partir de la `Uri`.

Il existe également une conversion implicite pour les chaînes d’URI, donc l’exemple suivant fonctionne également :

```csharp
webImage.Source = "https://xamarin.com/content/images/pages/forms/example-app.png";
```

Les captures d’écran suivantes montrent le résultat de l’affichage d’une image à distance sur chaque plateforme :

[Exemple d’application ![affichant une image téléchargée](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Mise en cache des images téléchargées

Un [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource) prend également en charge la mise en cache des images téléchargées, configurés via les propriétés suivantes :

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) : Indique si la mise en cache est activée (`true` par défaut).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) -A `TimeSpan` qui définit la durée pendant laquelle l’image est stockée localement.

La mise en cache est activée par défaut et stocke l’image localement pendant 24 heures. Pour désactiver la mise en cache pour une image particulière, instanciez la source d’image comme suit :

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("http://server.com/image") };
```

Pour définir une période de cache spécifique (par exemple, 5 jours) instancier la source d’image comme suit :

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://xamarin.com/content/images/pages/forms/example-app.png"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

La mise en cache intégrée rend très facile de prendre en charge des scénarios tels que le défilement des listes d’images, dans lequel vous pouvez définir (ou) une image dans chaque cellule et laisser le cache intégré s’occuper de rechargement de l’image lorsque vous faites défiler la cellule dans la vue.

## <a name="animated-gifs"></a>Images GIF animées

Xamarin. Forms comprend la prise en charge de l’affichage de petits GIF animés. Pour ce faire, définissez la propriété [`Image.Source`](xref:Xamarin.Forms.Image.Source) sur un fichier GIF animé :

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Tandis que la prise en charge GIF animée dans Xamarin. Forms comprend la possibilité de télécharger des fichiers, elle ne prend pas en charge la mise en cache ou la diffusion en continu de fichiers GIF animés.

Par défaut, lorsqu’un fichier GIF animé est chargé, il n’est pas lu. Cela est dû au fait que la propriété `IsAnimationPlaying`, qui contrôle si un GIF animé est en train de s’exécuter ou s’arrête, a une valeur par défaut de `false`. Cette propriété, de type `bool`, est stockée par un objet [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’il peut s’agir de la cible d’une liaison de données et d’un style.

Par conséquent, lorsqu’un fichier GIF animé est chargé, il n’est pas lu tant que la valeur de la propriété `IsAnimationPlaying` n’est pas `true`. La lecture peut ensuite être arrêtée en affectant à la propriété `IsAnimationPlaying` la valeur `false`. Notez que cette propriété n’a aucun effet lors de l’affichage d’une source d’image non GIF.

> [!NOTE]
> Sur Android, la prise en charge GIF animée nécessite que votre application utilise des convertisseurs rapides et ne fonctionnera pas si vous avez opté pour l’utilisation des convertisseurs hérités.
> Sur UWP, la prise en charge GIF animée nécessite une version minimale de la mise à jour anniversaire Windows 10 (version 1607).

## <a name="icons-and-splash-screens"></a>Icônes et écrans de démarrage

Bien que non liés à la [ `Image` ](xref:Xamarin.Forms.Image) affichage, les icônes d’application et les écrans de démarrage sont également une utilisation importante des images dans les projets Xamarin.Forms.

Icônes et écrans de démarrage pour les applications Xamarin.Forms se fait dans chacun des projets d’application. Cela signifie que la génération correctement en taille réelle des images pour iOS, Android et UWP. Ces images doivent être nommés et trouve en fonction des besoins de chaque des plateformes.

## <a name="icons"></a>Icônes

Pour plus d’informations sur la création de ces ressources d’application, consultez les instructions [iOS Working with images](~/ios/app-fundamentals/images-icons/index.md), [Google iconographie](https://developer.android.com/design/style/iconography.html)et UWP pour les éléments multimédias de [vignettes et d’icônes](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

En outre, les icônes de police peuvent être affichées par la vue [`Image`](xref:Xamarin.Forms.Image) en spécifiant les données d’icône de police dans un objet `FontImageSource`. Pour plus d’informations, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dans le guide des [polices](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Écrans de démarrage

Uniquement les applications iOS et UWP nécessitent un écran de démarrage (également appelé une image de démarrage écran ou la valeur par défaut).

Reportez-vous à la documentation pour [iOS utilisation des Images](~/ios/app-fundamentals/images-icons/index.md) et [écrans de démarrage](/windows/uwp/launch-resume/splash-screens/) sur le centre de développement Windows.

## <a name="related-links"></a>Liens connexes

- [WorkingWithImages (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS utilisation d’Images](~/ios/app-fundamentals/images-icons/index.md)
- [Iconographie Android](https://developer.android.com/design/style/iconography.html)
- [Instructions pour les ressources en mosaïque et icône](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)

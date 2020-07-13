---
title: Images dansXamarin.Forms
description: Les images peuvent être partagées entre les plateformes avec Xamarin.Forms , elles peuvent être chargées spécifiquement pour chaque plateforme, ou elles peuvent être téléchargées pour l’affichage.
ms.prod: xamarin
ms.assetid: C025AB53-05CC-49BA-9815-75D6DF9E40B7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7ae6e5e764dc066940971dd9b5a8fdc36c7a1970
ms.sourcegitcommit: cd0c0999b53e825b60471bfbfd4144cfcd783587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225492"
---
# <a name="images-in-xamarinforms"></a>Images dansXamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)

_Les images peuvent être partagées entre les plateformes avec Xamarin.Forms , elles peuvent être chargées spécifiquement pour chaque plateforme, ou elles peuvent être téléchargées pour l’affichage._

Les images sont un élément essentiel de la navigation, de la convivialité et de la personnalisation des applications. Xamarin.Formsles applications doivent être en mesure de partager des images sur toutes les plateformes, mais elles peuvent également afficher des images différentes sur chaque plateforme.

Les images spécifiques à la plateforme sont également requises pour les icônes et les écrans de démarrage. celles-ci doivent être configurées sur la base de chaque plateforme.

## <a name="display-images"></a>Afficher les images

Xamarin.Formsutilise la [`Image`](xref:Xamarin.Forms.Image) vue pour afficher des images sur une page. Il comporte plusieurs propriétés importantes :

- [`Source`](xref:Xamarin.Forms.Image.Source)- [`ImageSource`](xref:Xamarin.Forms.ImageSource) Instance, fichier, Uri ou ressource, qui définit l’image à afficher.
- [`Aspect`](xref:Xamarin.Forms.Image.Aspect): Dimensionnement de l’image dans les limites dans lesquelles elle est affichée (étirer, rogner ou bandes).

[`ImageSource`](xref:Xamarin.Forms.ImageSource)les instances peuvent être obtenues à l’aide de méthodes statiques pour chaque type de source d’image :

- [`FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))-Requiert un nom de fichier ou un FilePath qui peut être résolu sur chaque plateforme.
- [`FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))-Nécessite un objet Uri, par exemple.  `new Uri("http://server.com/image.jpg")` .
- [`FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)-Requiert un identificateur de ressource dans un fichier image incorporé dans l’application ou dans le projet de bibliothèque .NET Standard, avec une **action de génération : EmbeddedResource**.
- [`FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))-Requiert un flux qui fournit des données d’image.

La [`Aspect`](xref:Xamarin.Forms.Image.Aspect) propriété détermine la façon dont l’image est mise à l’échelle pour s’ajuster à la zone d’affichage :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)-Étire l’image pour qu’elle remplisse complètement et exactement la zone d’affichage. Cela peut entraîner la déformation de l’image.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-Découpe l’image pour qu’elle remplisse la zone d’affichage tout en préservant l’aspect (c’est-à-dire aucune distorsion).
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-Cadres l’image (si nécessaire) pour que la totalité de l’image s’ajuste à la zone d’affichage, avec un espace ajouté au haut/bas ou sur les côtés, selon que l’image est grande ou en hauteur.

Les images peuvent être chargées à partir d’un [fichier local](#local-images), d’une [ressource incorporée](#embedded-images), [téléchargées](#download-images)ou chargées à partir d’un flux. En outre, les icônes de police peuvent être affichées par la [`Image`](xref:Xamarin.Forms.Image) vue en spécifiant les données d’icône de police dans un `FontImageSource` objet. Pour plus d’informations, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dans le guide des [polices](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="local-images"></a>Images locales

Des fichiers image peuvent être ajoutés à chaque projet d’application et référencés à partir du Xamarin.Forms code partagé. Cette méthode de distribution d’images est indispensable dans le cas d’images spécifiques à la plateforme, par exemple si vous utilisez différentes résolutions sur différentes plateformes ou des conceptions qui présentent quelques variations mineures.

Pour utiliser une seule image pour l’ensemble des applications, *le même nom de fichier doit être utilisé sur chaque plateforme*et doit être un nom de ressource Android valide (c’est-à-dire uniquement des lettres minuscules, des chiffres, le trait de soulignement et le point sont autorisés).

- **iOS** : la meilleure façon de gérer et de prendre en charge les images depuis iOS 9 consiste à utiliser des **ensembles d’images du catalogue de ressources**, qui doivent contenir toutes les versions d’une image qui sont nécessaires pour prendre en charge différents appareils et facteurs d’échelle pour une application. Pour plus d’informations, consultez [Ajout d’images à un ensemble d’images du catalogue de composants](~/ios/app-fundamentals/images-icons/displaying-an-image.md).
- **Android** : Placez les images dans le répertoire **Resources/Drawable** avec l' **action de génération : AndroidResource**. Les versions haute et basse résolution d’une image peuvent également être fournies (dans des sous-répertoires de **ressources** nommées de manière appropriée, tels que les sous-répertoires de **dessinable-LDPI**, les **bacs-HDPI**et les **xhdpi de dessin**).
- **Plateforme Windows universelle (UWP)** : par défaut, les images doivent être placées dans le répertoire racine de l’application à l’aide de l' **action de génération : contenu**. Les images peuvent également être placées dans un répertoire différent qui est ensuite spécifié avec un spécifique à la plateforme. Pour plus d’informations, consultez [répertoire d’images par défaut sur Windows](~/xamarin-forms/platform/windows/default-image-directory.md).

> [!IMPORTANT]
> Avant iOS 9, les images étaient généralement placées dans le dossier **Resources** avec l' **action de génération : BundleResource**. Toutefois, cette méthode d’utilisation des images dans une application iOS a été dépréciée par Apple. Pour plus d’informations, consultez [tailles et noms d’images](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

L’adhésion à ces règles pour la dénomination et l’emplacement des fichiers permet au XAML suivant de charger et d’afficher l’image sur toutes les plateformes :

```xaml
<Image Source="waterfront.jpg" />
```

Le code C# équivalent est le suivant :

```csharp
var image = new Image { Source = "waterfront.jpg" };
```

Les captures d’écran suivantes montrent le résultat de l’affichage d’une image locale sur chaque plateforme :

[![Exemple d’application affichant une image locale](images-images/local-sml.png)](images-images/local.png#lightbox)

Pour plus de flexibilité `Device.RuntimePlatform` , la propriété peut être utilisée pour sélectionner un autre fichier image ou chemin d’accès pour une partie ou l’ensemble des plateformes, comme illustré dans cet exemple de code :

```csharp
image.Source = Device.RuntimePlatform == Device.Android
                ? ImageSource.FromFile("waterfront.jpg")
                : ImageSource.FromFile("Images/waterfront.jpg");
```

> [!IMPORTANT]
> Pour utiliser le même nom de fichier d’image sur toutes les plateformes, le nom doit être valide sur toutes les plateformes. Les drawables Android ont des restrictions de nommage : seules les lettres minuscules, les chiffres, le trait de soulignement et le point sont autorisés, et pour la compatibilité multiplateforme, cette fonction doit également être suivie sur toutes les autres plateformes. L’exemple de nom de fichier **waterfront.png** suit les règles, mais des exemples de noms de fichiers non valides incluent « Water front.png », « WaterFront.png », « water-front.png » et « wåterfront.png ».

### <a name="native-resolutions-retina-and-high-dpi"></a>Résolutions natives (retine et haute résolution)

iOS, Android et UWP incluent la prise en charge de différentes résolutions d’image, où le système d’exploitation choisit l’image appropriée au moment de l’exécution en fonction des capacités de l’appareil. Xamarin.Formsutilise les API des plateformes natives pour le chargement des images locales. il prend donc automatiquement en charge les autres résolutions si les fichiers sont correctement nommés et localisés dans le projet.

La meilleure façon de gérer des images depuis iOS 9 consiste à faire glisser des images pour chaque résolution requise pour l’ensemble d’images du catalogue de ressources approprié. Pour plus d’informations, consultez [Ajout d’images à un ensemble d’images du catalogue de composants](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Avant iOS 9, les versions de retine de l’image pouvaient être placées dans le dossier **Resources** -deux et trois fois la résolution avec un **@2x** **@3x** suffixe ou sur le nom de fichier avant l’extension de fichier (par exemple, **myimage@2x.png**). Toutefois, cette méthode d’utilisation des images dans une application iOS a été dépréciée par Apple. Pour plus d’informations, consultez [tailles et noms d’images](~/ios/app-fundamentals/images-icons/displaying-an-image.md).

Les images de résolution alternative Android doivent être placées dans des [répertoires spécialement nommés](https://developer.android.com/guide/practices/screens_support.html) dans le projet Android, comme illustré dans la capture d’écran suivante :

[![Emplacement d’image à résolution multiple Android](images-images/xs-highdpisolution-sml.png)](images-images/xs-highdpisolution.png#lightbox)

Les noms de fichiers image UWP [peuvent être suivis d’un suffixe `.scale-xxx` avant l’extension de fichier](https://docs.microsoft.com/windows/uwp/app-resources/images-tailored-for-scale-theme-contrast), où `xxx` est le pourcentage de mise à l’échelle appliqué à la ressource, par exemple **myimage.scale-200.png**. Les images peuvent ensuite être référencées dans le code ou XAML sans le modificateur d’échelle, par exemple **myimage.png**. La plateforme sélectionnera l’échelle de ressources appropriée la plus proche en fonction de la résolution actuelle de l’affichage.

### <a name="additional-controls-that-display-images"></a>Contrôles supplémentaires qui affichent des images

Certains contrôles ont des propriétés qui affichent une image, par exemple :

- [`Button`](xref:Xamarin.Forms.Button)a une [`ImageSource`](xref:Xamarin.Forms.Button.ImageSource) propriété qui peut être définie sur une image bitmap à afficher sur le `Button` . Pour plus d’informations, consultez [utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).
- [`ImageButton`](xref:Xamarin.Forms.Button)a une [`Source`](xref:Xamarin.Forms.ImageButton.Source) propriété qui peut être définie sur l’image à afficher dans le `ImageButton` . Pour plus d’informations, consultez [définition de la source de l’image](~/xamarin-forms/user-interface/imagebutton.md#setting-the-image-source).
- [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)a une [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) propriété qui peut être définie sur une image qui est chargée à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.
- [`ImageCell`](xref:Xamarin.Forms.ImageCell)a une [`ImageSource`](xref:Xamarin.Forms.ImageCell.ImageSource) propriété qui peut être définie sur une image récupérée à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.
- [`Page`](xref:Xamarin.Forms.Page). Tout type de page qui dérive de `Page` possède les [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) [`BackgroundImageSource`](xref:Xamarin.Forms.Page.BackgroundImageSource) Propriétés et, qui peuvent être affectées à un fichier, à une ressource incorporée, à un URI ou à un flux. Dans certaines circonstances, par exemple quand un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) affiche un [`ContentPage`](xref:Xamarin.Forms.ContentPage) , l’icône s’affiche si elle est prise en charge par la plateforme.

  > [!IMPORTANT]
  > Sur iOS, la [`Page.IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) propriété ne peut pas être remplie à partir d’une image dans un jeu d’images du catalogue de composants. Au lieu de cela, chargez les images d’icône pour la `Page.IconImageSource` propriété à partir d’un fichier, d’une ressource incorporée, d’un URI ou d’un flux.

## <a name="embedded-images"></a>Images incorporées

Les images incorporées sont également fournies avec une application (comme les images locales), mais au lieu d’avoir une copie de l’image dans la structure de fichiers de chaque application, le fichier image est incorporé dans l’assembly en tant que ressource. Cette méthode de distribution d’images est recommandée lorsque des images identiques sont utilisées sur chaque plateforme, et est particulièrement adaptée à la création de composants, lorsque l’image est regroupée avec le code.

Pour incorporer une image dans un projet, cliquez avec le bouton droit pour ajouter de nouveaux éléments et sélectionnez les images/s que vous souhaitez ajouter. Par défaut, l’image a une **action de génération : aucune**; Cela doit être défini sur **action de génération : EmbeddedResource**.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Définir l’action de génération sur la ressource incorporée](images-images/vs-buildaction-sml.png)](images-images/vs-buildaction.png#lightbox)

L' **action de génération** peut être affichée et modifiée dans la fenêtre **Propriétés** d’un fichier.

Dans cet exemple, l’ID de ressource est **WorkingWithImages.beach.jpg**.
L’IDE a généré cette valeur par défaut en concaténant l' **espace de noms par défaut** pour ce projet avec le nom de fichier, à l’aide d’un point (.) entre chaque valeur.
<!-- https://msdn.microsoft.com/library/ms950960.aspx -->

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![](images-images/xs-buildaction.png "Set Build Action: EmbeddedResource")

L' **action de génération** peut également être affichée et modifiée dans le panneau **Propriétés** d’un fichier.
Ce panneau affiche l' **ID de ressource** utilisé pour référencer la ressource dans le code. Dans la capture d’écran ci-dessous, l' **ID de ressource** est **WorkingWithImages.beach.jpg**.
L’IDE a généré cette valeur par défaut en concaténant l' **espace de noms par défaut** pour ce projet avec le nom de fichier, à l’aide d’un point (.) entre chaque valeur.
Cet ID peut être modifié dans le panneau **Propriétés** , mais pour ces exemples, la valeur **WorkingWithImages.beach.jpg** sera utilisée.

[![Panneau Propriétés des ressources incorporées](images-images/xs-embeddedproperties-sml.png)](images-images/xs-embeddedproperties.png#lightbox)

-----

Si vous placez des images incorporées dans des dossiers de votre projet, les noms de dossiers sont également séparés par des points (.) dans l’ID de ressource. Le déplacement de l’image **beach.jpg** dans un dossier appelé **myImages** entraînerait un ID de ressource de **WorkingWithImages.MyImages.beach.jpg**

Le code permettant de charger une image incorporée transmet simplement l' **ID de ressource** à la [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) méthode comme indiqué ci-dessous :

```csharp
Image embeddedImage = new Image
{
    Source = ImageSource.FromResource("WorkingWithImages.beach.jpg", typeof(MyClass).GetTypeInfo().Assembly)
};
```

> [!NOTE]
> Pour prendre en charge l’affichage d’images incorporées en mode release sur le plateforme Windows universelle, il est nécessaire d’utiliser la surcharge de `ImageSource.FromResource` qui spécifie l’assembly source dans lequel Rechercher l’image.

Actuellement, il n’existe pas de conversion implicite pour les identificateurs de ressource. Au lieu de cela, vous devez utiliser [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) ou `new ResourceImageSource()` pour charger des images incorporées.

Les captures d’écran suivantes montrent le résultat de l’affichage d’une image incorporée sur chaque plateforme :

[![Exemple d’application affichant une image incorporée](images-images/resource-sml.png)](images-images/resource.png#lightbox)

### <a name="xaml"></a>XAML

Étant donné qu’il n’existe aucun convertisseur de type intégré de `string` à `ResourceImageSource` , ces types d’images ne peuvent pas être chargés en mode natif par XAML. Au lieu de cela, il est possible d’écrire une extension de balisage XAML personnalisée simple pour charger des images à l’aide d’un **ID de ressource** spécifié en XAML :

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
> Pour prendre en charge l’affichage d’images incorporées en mode release sur le plateforme Windows universelle, il est nécessaire d’utiliser la surcharge de `ImageSource.FromResource` qui spécifie l’assembly source dans lequel Rechercher l’image.

Pour utiliser cette extension, ajoutez un personnalisé `xmlns` au code XAML, en utilisant l’espace de noms et les valeurs d’assembly corrects pour le projet. La source de l’image peut ensuite être définie à l’aide de la syntaxe suivante : `{local:ImageResource WorkingWithImages.beach.jpg}` . Un exemple de code XAML complet est illustré ci-dessous :

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

Étant donné qu’il est parfois difficile de comprendre pourquoi une ressource d’image particulière n’est pas chargée, le code de débogage suivant peut être ajouté temporairement à une application pour aider à confirmer que les ressources sont correctement configurées. Elle génère toutes les ressources connues incorporées dans l’assembly donné sur la **console** pour aider à déboguer les problèmes de chargement des ressources.

```csharp
using System.Reflection;
// ...
// NOTE: use for debugging, not in released app code!
var assembly = typeof(MyClass).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames())
{
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

#### <a name="images-embedded-in-other-projects"></a>Images incorporées dans d’autres projets

Par défaut, la `ImageSource.FromResource` méthode recherche uniquement les images dans le même assembly que le code appelant la `ImageSource.FromResource` méthode. À l’aide du code de débogage ci-dessus, vous pouvez déterminer les assemblys qui contiennent une ressource spécifique en remplaçant l' `typeof()` instruction par une instruction `Type` connue comme étant dans chaque assembly.

Toutefois, l’assembly source faisant l’objet de la recherche d’une image incorporée peut être spécifié en tant qu’argument de la `ImageSource.FromResource` méthode :

```csharp
var imageSource = ImageSource.FromResource("filename.png",
            typeof(MyClass).GetTypeInfo().Assembly);
```

## <a name="download-images"></a>Télécharger des images

Les images peuvent être téléchargées automatiquement pour l’affichage, comme illustré dans le code XAML suivant :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       x:Class="WorkingWithImages.DownloadImagesXaml">
  <StackLayout VerticalOptions="Center" HorizontalOptions="Center">
    <Label Text="Image UriSource Xaml" />
    <Image Source="https://aka.ms/campus.jpg" />
    <Label Text="campus.jpg gets downloaded from microsoft.com" />
  </StackLayout>
</ContentPage>
```

Le code C# équivalent est le suivant :

```csharp
var webImage = new Image {
     Source = ImageSource.FromUri(
        new Uri("https://aka.ms/campus.jpg")
     ) };
```

La [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) méthode requiert un `Uri` objet et retourne un nouveau [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) qui lit à partir de `Uri` .

Il y a également une conversion implicite pour les chaînes d’URI, l’exemple suivant fonctionne également :

```csharp
webImage.Source = "https://aka.ms/campus.jpg";
```

Les captures d’écran suivantes montrent le résultat de l’affichage d’une image distante sur chaque plateforme :

[![Exemple d’application affichant une image téléchargée](images-images/download-sml.png)](images-images/download.png#lightbox)

### <a name="downloaded-image-caching"></a>Mise en cache des images téléchargées

[`UriImageSource`](xref:Xamarin.Forms.UriImageSource)Prend également en charge la mise en cache des images téléchargées, configurées par le biais des propriétés suivantes :

- [`CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled): Indique si la mise en cache est activée ( `true` par défaut).
- [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity)-Un `TimeSpan` qui définit la durée pendant laquelle l’image sera stockée localement.

La mise en cache est activée par défaut et stocke l’image localement pendant 24 heures. Pour désactiver la mise en cache d’une image particulière, instanciez la source de l’image comme suit :

```csharp
image.Source = new UriImageSource { CachingEnabled = false, Uri = new Uri("https://server.com/image") };
```

Pour définir une période de cache spécifique (par exemple, 5 jours), instanciez la source de l’image comme suit :

```csharp
webImage.Source = new UriImageSource
{
    Uri = new Uri("https://aka.ms/campus.jpg"),
    CachingEnabled = true,
    CacheValidity = new TimeSpan(5,0,0,0)
};
```

Grâce à la mise en cache intégrée, il est très facile de prendre en charge des scénarios tels que les listes de défilement d’images, où vous pouvez définir (ou lier) une image dans chaque cellule et laisser le cache intégré se charger de recharger l’image lorsque la cellule est replacée dans la vue.

## <a name="animated-gifs"></a>Images GIF animées

Xamarin.Formsprend en charge l’affichage de petits GIF animés. Pour ce faire, définissez la [`Image.Source`](xref:Xamarin.Forms.Image.Source) propriété sur un fichier GIF animé :

```xaml
<Image Source="demo.gif" />
```

> [!IMPORTANT]
> Alors que la prise en charge GIF animée dans Xamarin.Forms offre la possibilité de télécharger des fichiers, elle ne prend pas en charge la mise en cache ou la diffusion en continu de fichiers GIF animés.

Par défaut, lorsqu’un fichier GIF animé est chargé, il n’est pas lu. Cela est dû au fait que la `IsAnimationPlaying` propriété, qui contrôle si un gif animé est en train de s’exécuter ou s’arrête, a une valeur par défaut `false` . Cette propriété, de type `bool` , est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible d’une liaison de données et d’un style.

Par conséquent, lorsqu’un GIF animé est chargé, il n’est pas lu tant que la `IsAnimationPlaying` propriété n’a pas la valeur `true` . La lecture peut ensuite être arrêtée en affectant à la propriété la valeur `IsAnimationPlaying` `false` . Notez que cette propriété n’a aucun effet lors de l’affichage d’une source d’image non GIF.

> [!NOTE]
> Sur Android, la prise en charge GIF animée nécessite que votre application utilise des convertisseurs rapides et ne fonctionnera pas si vous avez opté pour l’utilisation des convertisseurs hérités.
> Sur UWP, la prise en charge GIF animée nécessite une version minimale de la mise à jour anniversaire Windows 10 (version 1607).

## <a name="icons-and-splash-screens"></a>Icônes et écrans de démarrage

Bien qu’il ne soit pas lié à la [`Image`](xref:Xamarin.Forms.Image) vue, les icônes d’application et les écrans de démarrage sont également une utilisation importante des images dans les Xamarin.Forms projets.

La définition des icônes et des écrans de démarrage pour les Xamarin.Forms applications s’effectue dans chacun des projets d’application. Cela implique la génération d’images de taille correcte pour iOS, Android et UWP. Ces images doivent être nommées et situées selon les exigences de chaque plateforme.

## <a name="icons"></a>Icônes

Pour plus d’informations sur la création de ces ressources d’application, consultez les instructions [iOS Working with images](~/ios/app-fundamentals/images-icons/index.md), [Google iconographie](https://developer.android.com/design/style/iconography.html)et UWP pour les éléments multimédias de [vignettes et d’icônes](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/) .

En outre, les icônes de police peuvent être affichées par la [`Image`](xref:Xamarin.Forms.Image) vue en spécifiant les données d’icône de police dans un `FontImageSource` objet. Pour plus d’informations, consultez [afficher les icônes de police](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons) dans le guide des [polices](~/xamarin-forms/user-interface/text/fonts.md) .

## <a name="splash-screens"></a>Écrans de démarrage

Seules les applications iOS et UWP requièrent un écran de démarrage (également appelé écran de démarrage ou image par défaut).

Reportez-vous à la documentation pour [iOS utilisation des images](~/ios/app-fundamentals/images-icons/index.md) et des [écrans de démarrage](/windows/uwp/launch-resume/splash-screens/) dans le centre de développement Windows.

## <a name="related-links"></a>Liens connexes

- [WorkingWithImages (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithimages)
- [iOS utilisation d’images](~/ios/app-fundamentals/images-icons/index.md)
- [Iconographie Android](https://developer.android.com/design/style/iconography.html)
- [Recommandations en matière de ressources de vignette et d’icône](/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets/)

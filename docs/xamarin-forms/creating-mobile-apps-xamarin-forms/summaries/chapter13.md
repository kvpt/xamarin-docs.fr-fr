---
title: Résumé du chapitre 13. Images bitmap
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 13. Images bitmap'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292821"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Résumé du chapitre 13. Images bitmap

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

L’élément Xamarin. Forms [`Image`](xref:Xamarin.Forms.Image) affiche une image bitmap. Toutes les plateformes de Xamarin.Forms prend en charge les formats de fichier JPEG, PNG, GIF et BMP.

Bitmaps dans Xamarin.Forms proviennent de quatre endroits :

- Sur le web tel que spécifié par une URL
- Incorporé comme ressource dans la bibliothèque partagée
- Incorporé comme ressource dans les projets d’application de plateforme
- À partir de n’importe quel endroit pouvant être référencé par un objet .NET `Stream`, y compris `MemoryStream`

Ressources bitmap dans la bibliothèque partagée sont indépendant de la plateforme, tandis que les ressources de bitmap dans les projets de plateforme sont spécifiques à la plateforme.

> [!NOTE]
> Le texte du livre fait référence aux bibliothèques de classes portables, qui ont été remplacés par des bibliothèques .NET Standard. Exemples de code à partir de l’ouvrage a été converti pour utiliser les bibliothèques .NET standard.

La bitmap est spécifiée en définissant la propriété [`Source`](xref:Xamarin.Forms.Image.Source) de `Image` sur un objet de type [`ImageSource`](xref:Xamarin.Forms.ImageSource), une classe abstraite avec trois dérivés :

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource) pour accéder à une image bitmap sur le Web en fonction d’un objet `Uri` défini sur sa propriété [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri)
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) pour accéder à une bitmap stockée dans un projet d’application de plateforme basé sur un chemin d’accès de dossier et de fichier défini sur sa propriété [`File`](xref:Xamarin.Forms.FileImageSource.File)
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource) pour le chargement d’une bitmap à l’aide d’un objet `Stream` .net spécifié en retournant un `Stream` à partir d’un `Func` défini sur sa propriété [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream)

Vous pouvez également utiliser les méthodes statiques suivantes de la classe `ImageSource`, qui retournent toutes `ImageSource` objets :

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) pour accéder à une image bitmap sur le Web en fonction d’un objet `Uri`
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*) pour accéder à une image bitmap stockée en tant que ressource incorporée dans l’application PCL ; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) ou [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) pour accéder à une image bitmap dans un autre assembly source
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String)) pour accéder à une image bitmap à partir d’un projet d’application de plateforme
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream})) pour le chargement d’une bitmap basée sur un objet `Stream`

Il n’existe aucun équivalent de classe des méthodes `Image.FromResource`. La classe `UriImageSource` est utile si vous avez besoin de contrôler la mise en cache. La classe `FileImageSource` est utile en XAML. `StreamImageSource` est utile pour le chargement asynchrone d’objets `Stream`, tandis que `ImageSource.FromStream` est synchrone.

## <a name="platform-independent-bitmaps"></a>Bitmaps indépendantes de la plate-forme

Le projet [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) charge une image bitmap sur le Web à l’aide de `ImageSource.FromUri`. L’élément `Image` est défini sur la propriété `Content` de l' `ContentPage`, donc il est restreint à la taille de la page. Quelle que soit la taille de la bitmap, un élément `Image` limité est étiré jusqu’à la taille de son conteneur, et la bitmap est affichée dans sa taille maximale dans l’élément `Image` tout en conservant les proportions de la bitmap. Les zones de la `Image` au-delà de la bitmap peuvent être colorées avec [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor).

L’exemple [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) est similaire, mais définit simplement la propriété `Source` sur l’URL. La conversion est gérée par la classe [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) .

### <a name="fit-and-fill"></a>Ajuster et remplissage

Vous pouvez contrôler la façon dont la bitmap est étirée en affectant à la propriété [`Aspect`](xref:Xamarin.Forms.Image.Aspect) du `Image` l’un des membres suivants de l’énumération [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): proportions (par défaut)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): zone de remplissage, qui ne respecte pas les proportions
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): remplit la zone mais respecte les proportions en rognant une partie de l’image bitmap.

### <a name="embedded-resources"></a>Ressources incorporées

Vous pouvez ajouter un fichier bitmap pour une bibliothèque de classes portable, ou dans un dossier dans la bibliothèque PCL. Donnez-lui une **action de génération** de **EmbeddedResource**. L’exemple [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) montre comment utiliser `ImageSource.FromResource` pour charger le fichier. Le nom de ressource passé à la méthode se compose du nom d’assembly, suivi d’un point, suivie du nom de dossier facultatif et un point, suivi par le nom de fichier.

Le programme définit les propriétés `VerticalOptions` et `HorizontalOptions` de la `Image` sur `LayoutOptions.Center`, ce qui rend l’élément `Image` sans contrainte. La `Image` et la bitmap sont de la même taille :

- Sur iOS et Android, le `Image` est la taille en pixels de la bitmap. Il existe une correspondance biunivoque entre les pixels du bitmap et de pixels de l’écran.
- Sur plateforme Windows universelle, la `Image` correspond à la taille en pixels de l’image bitmap en unités indépendantes du périphérique. Sur la plupart des périphériques, chaque pixel de bitmap occupe plusieurs pixels de l’écran.

L’exemple [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) place une `Image` dans un `StackLayout` vertical en XAML. Une extension de balisage nommée [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) permet de référencer la ressource incorporée en XAML. Cette classe charge uniquement des ressources à partir de l’assembly dans lequel il se trouve, donc il ne peut pas être placé dans une bibliothèque.

### <a name="more-on-sizing"></a>Plus d’informations sur le dimensionnement

Il est souvent souhaitable de bitmaps de taille cohérente parmi toutes les plateformes.
En expérimentant avec **StackedBitmap**, vous pouvez définir un `WidthRequest` sur l’élément `Image` dans un `StackLayout` vertical pour rendre la taille cohérente entre les plateformes, mais vous pouvez uniquement réduire la taille à l’aide de cette technique.

Vous pouvez également définir la `HeightRequest` pour que les tailles d’image soient cohérentes sur les plateformes, mais la largeur limitée de la bitmap limitera la polyvalence de cette technique. Pour une image dans une `StackLayout`verticale, `HeightRequest` doit être évité.

La meilleure approche consiste à commencer par une bitmap plus large que la largeur du téléphone en unités indépendantes du périphérique et à définir `WidthRequest` sur une largeur souhaitée en unités indépendantes du périphérique. Cela est illustré dans l’exemple [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

Le [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) affiche le chapitre 7 de l’aventures d’Alice de Lewis Carroll *dans merveilles* avec les illustrations d’origine de John Tenniel :

[![Capture d’écran triple du tiers de thé Mad](images/ch13fg16-small.png "Texte de l’ouvrage de la partie thé Mad Hatten")](images/ch13fg16-large.png#lightbox "Texte de l’ouvrage de la partie thé Mad Hatten")

### <a name="browsing-and-waiting"></a>Navigation et en attente

L’exemple [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permet à l’utilisateur de parcourir les images stockées sur le site Web Xamarin. Elle utilise la classe de [`WebRequest`](xref:System.Net.WebRequest) .net pour télécharger un fichier JSON avec la liste des bitmaps.

> [!NOTE]
> Les programmes Xamarin. Forms doivent utiliser [`HttpClient`](xref:System.Net.Http.HttpClient) plutôt que des [`WebRequest`](xref:System.Net.WebRequest) pour accéder aux fichiers sur Internet.

Le programme utilise un [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) pour indiquer qu’un événement se passe. À mesure que chaque bitmap est chargée, la propriété en lecture seule [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) de `Image` est `true`. La propriété `IsLoading` est stockée par une propriété pouvant être liée, de sorte qu’un événement `PropertyChanged` est déclenché lorsque cette propriété change. Le programme attache un gestionnaire à cet événement et utilise le paramètre actuel de `IsLoaded` pour définir la propriété [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) de l' `ActivityIndicator`.

## <a name="streaming-bitmaps"></a>Diffusion en continu de bitmaps

La méthode `ImageSource.FromStream` crée une `ImageSource` basée sur un `Stream`.NET. Une `Func` objet qui retourne un objet `Stream` doit être passé à la méthode.

### <a name="accessing-the-streams"></a>L’accès à des flux de données

L’exemple [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) montre comment utiliser la méthode `ImaageSource.FromStream` pour charger une image bitmap stockée en tant que ressource incorporée et charger une image bitmap sur le Web.

### <a name="generating-bitmaps-at-run-time"></a>Générer des images bitmap au moment de l’exécution

Toutes les plateformes Xamarin. Forms prennent en charge le format de fichier BMP non compressé, qui est facile à construire dans le code, puis à stocker dans une `MemoryStream`. Cette technique permet de créer des bitmaps algorithmiques au moment de l’exécution, tels qu’ils sont implémentés dans la classe [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) de la bibliothèque **xamarin. FormsBook. Toolkit** .

L’exemple de [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) « do it vous » illustre l’utilisation de `BmpMaker` pour créer une bitmap avec une image dégradée.

## <a name="platform-specific-bitmaps"></a>Bitmaps spécifiques à la plateforme

Toutes les plateformes de Xamarin.Forms permettent de stocker des images bitmap dans les assemblys d’application de plateforme. Lorsqu’ils sont récupérés par une application Xamarin. Forms, ces bitmaps de plateforme sont de type [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Vous les utilisez pour :

- propriété [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`MenuItem`](xref:Xamarin.Forms.MenuItem)
- propriété [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- propriété [`Image`](xref:Xamarin.Forms.Button) de `Button`

Les assemblys de plateforme contiennent déjà des bitmaps pour les icônes et écrans de démarrage :

- Dans le projet iOS, dans le dossier **ressources**
- Dans le projet Android, dans les sous-dossiers du dossier **ressources**
- Dans les projets Windows, dans le dossier **ressources** (bien que les plateformes Windows ne restreignent pas les bitmaps à ce dossier)

L’exemple [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) utilise du code pour afficher une icône à partir des projets d’application de plateforme.

### <a name="bitmap-resolutions"></a>Résolutions de bitmap

Toutes les plateformes permettent de stocker plusieurs versions des images bitmap pour les résolutions de périphérique différent. Lors de l’exécution, la version correcte est chargée en fonction de la résolution de l’appareil de l’écran.

Sur iOS, ces bitmaps sont différenciées par le suffixe du nom de fichier :

- Pas de suffixe pour les appareils de 160 DPI (1 pixel à l’unité indépendante du périphérique)
- suffixe'@2x'pour les appareils 320 DPI (2 pixels sur le DIU)
- suffixe'@3x'pour les appareils 480 PPP (3 pixels au DIU)

Une image bitmap destinée à être affiché sous forme d’un pouce carré existerait dans trois versions :

- MyImage.jpg à 160 pixels carrés
- MyImage@2x.jpg à 320 pixels carrés
- MyImage@3x.jpg à 480 pixels carrés

Le programme fait référence à cette image bitmap en tant que MyImage.jpg, mais la version correcte est récupérée pendant l’exécution en fonction de la résolution de l’écran. Quand elles sont libres, l’image bitmap restitue toujours à 160 unités indépendantes du périphérique.

Pour Android, les bitmaps sont stockées dans différents sous-dossiers du dossier **ressources** :

- drawable-ldpi (faible PPP) pour les appareils de 120 DPI (0,75 pixels pour le DIU)
- drawable-mdpi (moyenne) pour les appareils de 160 DPI (1 pixel à la DIU)
- drawable-hdpi (élevé) pour les appareils de 240 DPI (1,5 pixels pour le DIU)
- drawable-xhdpi (très élevée) pour les appareils de 320 DPI (2 pixels à le DIU)
- drawable-xxhdpi (très très élevée) pour les appareils de 480 PPP (de 3 pixels pour le DIU)
- drawable-xxxhdpi (trois points forts supplémentaires) pour les appareils de 640 PPP (4 pixels pour le DIU)

Pour une image bitmap destinée à être rendu à un pouce carré, les différentes versions de l’image bitmap auront le même nom mais une taille différente et être stockées dans ces dossiers :

- drawable-ldpi/MyImage.jpg à 120 pixels carrés
- drawable-mdpi/MyImage.jpg à 160 pixels carrés
- drawable-hdpi/MyImage.jpg à 240 pixels carrés
- drawable-xhdpi/MyImage.jpg à 320 pixels carrés
- drawable-xxhdpi/MyImage.jpg à 480 pixels carrés
- drawable-xxxhdpi/MyImage.jpg à 640 pixels carrés

La bitmap est toujours le rendu à 160 unités indépendantes du périphérique. (Le modèle de solution Xamarin.Forms standard inclut uniquement le hdpi, xhdpi et les dossiers xxhdpi.)

Le projet UWP prend en charge un schéma d’affectation de noms bitmap comprenant un facteur d’échelle en pixels indépendants du périphérique unitaire sous forme de pourcentage, par exemple :

- MyImage.scale-200.jpg à 320 pixels carrés

Seuls certains pourcentages sont valides. Les exemples de programmes de ce livre incluent uniquement les images avec les suffixes de **Scale-200** , mais les modèles de solution Xamarin. Forms actuels incluent **Scale-100**, SCAL **-125**, **Scale-150**et **Scale-400**.

Quand vous ajoutez des bitmaps aux projets de plateforme, l' **action de génération** doit être :

- iOS : **BundleResource**
- Android : **AndroidResource**
- UWP : **contenu**

L’exemple [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) crée deux objets de type bouton composés d’éléments `Image` avec une `TapGestureRecognizer` installée. Il est prévu que les objets soient carré d’un pouce. La propriété `Source` de `Image` est définie à l’aide d' `OnPlatform` et `On` objets pour faire référence à des noms de fichiers potentiellement différents sur les plateformes. Les images bitmap incluent les numéros d’indiquant leur taille en pixels, pour voir quelle bitmap de taille est récupéré et affiché.

### <a name="toolbars-and-their-icons"></a>Barres d’outils et leurs icônes

L’une des principales utilisations des bitmaps spécifiques à la plateforme est la barre d’outils Xamarin. Forms, qui est construite en ajoutant des objets [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) à la collection [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) définie par `Page`. `ToobarItem` dérive de [`MenuItem`](xref:Xamarin.Forms.MenuItem) dont il hérite certaines propriétés.

Les propriétés `ToolbarItem` les plus importantes sont les suivantes :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) pour le texte qui peut s’afficher en fonction de la plateforme et `Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) de type `FileImageSource` pour l’image qui peut s’afficher en fonction de la plateforme et `Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) de type [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder), une énumération avec trois membres, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default), [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)et [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary).

Le nombre d’éléments de `Primary` doit être limité à trois ou quatre. Vous devez inclure un paramètre de `Text` pour tous les éléments. Pour la plupart des plateformes, seuls les éléments de `Primary` requièrent un `Icon`, mais Windows 8.1 requiert une `Icon` pour tous les éléments. Les icônes doivent être des unités indépendantes du périphérique 32 carrées. Le type de `FileImageSource` indique qu’ils sont spécifiques à la plateforme.

Le `ToolbarItem` déclenche un événement [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) lorsqu’il est appuyé, comme un `Button`. `ToolbarItem` prend également en charge les propriétés [`Command`](xref:Xamarin.Forms.MenuItem.Command) et [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) souvent utilisées dans le cadre d’une connexion avec MVVM. (Voir le [chapitre 18, MVVM](chapter18.md)).

IOS et Android requièrent qu’une page affichant une barre d’outils soit une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ou une page à laquelle accède une `NavigationPage`. Le programme [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) définit la propriété `MainPage` de sa classe `App` sur le [constructeur`NavigationPage`](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) avec un argument `ContentPage`, et illustre le gestionnaire d’événements et la construction d’une barre d’outils.

### <a name="button-images"></a>Images de bouton

Vous pouvez également utiliser des bitmaps spécifiques à la plateforme pour définir la propriété [`Image`](xref:Xamarin.Forms.Button.Image) de `Button` sur une image bitmap de 32 unités indépendantes du périphérique carré, comme illustré par l’exemple [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> L’utilisation d’images sur les boutons a été améliorée. Consultez [utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Liens connexes

- [Chapitre 13 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Exemples du chapitre 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Utilisation d’images](~/xamarin-forms/user-interface/images.md)
- [Utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)

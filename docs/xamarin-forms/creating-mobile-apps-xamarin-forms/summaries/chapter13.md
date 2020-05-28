---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 13. Bitmaps''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 43caf088ad6cb816f049e7862a287c17839c2170
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136771"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Résumé du chapitre 13. Images bitmap

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

L' Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) élément affiche une image bitmap. Toutes les Xamarin.Forms plateformes prennent en charge les formats de fichier JPEG, png, GIF et BMP.

Les bitmaps de Xamarin.Forms proviennent de quatre emplacements :

- Sur le Web, comme spécifié par une URL
- Incorporé en tant que ressource dans la bibliothèque partagée
- Incorporé en tant que ressource dans les projets d’application de plateforme
- À partir de n’importe quel endroit pouvant être référencé par un `Stream` objet .net, y compris`MemoryStream`

Les ressources bitmap de la bibliothèque partagée sont indépendantes des plateformes, tandis que les ressources bitmap des projets de plateforme sont spécifiques à la plateforme.

> [!NOTE]
> Le texte du livre fait référence à des bibliothèques de classes portables, qui ont été remplacées par .NET Standard bibliothèques. Tout l’exemple de code du livre a été converti pour utiliser des bibliothèques .NET standard.

La bitmap est spécifiée en affectant [`Source`](xref:Xamarin.Forms.Image.Source) à la propriété de la valeur d' `Image` un objet de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , une classe abstraite avec trois dérivées :

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)pour accéder à une image bitmap sur le Web en fonction d’un `Uri` objet défini sur sa [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri) propriété
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)pour accéder à une image bitmap stockée dans un projet d’application de plateforme en fonction d’un chemin d’accès de dossier et de fichier défini sur sa [`File`](xref:Xamarin.Forms.FileImageSource.File) propriété
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)pour charger une bitmap à l’aide d’un `Stream` objet .net spécifié en retournant un `Stream` d’un `Func` défini à sa [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) propriété

Vous pouvez également utiliser les méthodes statiques suivantes de la `ImageSource` classe, qui retournent toutes les `ImageSource` objets :

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))pour accéder à une image bitmap sur le Web en fonction d’un `Uri` objet
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)pour accéder à une image bitmap stockée en tant que ressource incorporée dans l’application PCL ; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type))ou [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) pour accéder à une image bitmap dans un autre assembly source
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))pour accéder à une image bitmap à partir d’un projet d’application de plateforme
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))pour charger une image bitmap basée sur un `Stream` objet

Il n’existe aucun équivalent de classe des `Image.FromResource` méthodes. La `UriImageSource` classe est utile si vous avez besoin de contrôler la mise en cache. La `FileImageSource` classe est utile en XAML. `StreamImageSource`est utile pour le chargement asynchrone d' `Stream` objets, tandis que `ImageSource.FromStream` est synchrone.

## <a name="platform-independent-bitmaps"></a>Bitmaps indépendantes de la plateforme

Le projet [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) charge une image bitmap sur le Web à l’aide de `ImageSource.FromUri` . L' `Image` élément est défini sur la `Content` propriété du `ContentPage` , donc il est restreint à la taille de la page. Quelle que soit la taille de la bitmap, un `Image` élément limité est étiré jusqu’à la taille de son conteneur, et la bitmap est affichée dans sa taille maximale dans l' `Image` élément tout en conservant les proportions de la bitmap. Les zones du au `Image` -delà de la bitmap peuvent être colorées avec [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) .

L’exemple [**WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) est similaire, mais définit simplement la `Source` propriété sur l’URL. La conversion est gérée par la [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) classe.

### <a name="fit-and-fill"></a>Ajuster et remplir

Vous pouvez contrôler la façon dont la bitmap est étirée en affectant [`Aspect`](xref:Xamarin.Forms.Image.Aspect) à la propriété de l' `Image` un des membres suivants de l' [`Aspect`](xref:Xamarin.Forms.Aspect) énumération :

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): respecte les proportions (valeur par défaut)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): zone de remplissage, qui ne respecte pas les proportions
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): remplit la zone mais respecte les proportions en rognant une partie de l’image bitmap.

### <a name="embedded-resources"></a>Ressources incorporées

Vous pouvez ajouter un fichier bitmap à une bibliothèque de classes portable ou à un dossier de la bibliothèque de classes portable. Donnez-lui une **action de génération** de **EmbeddedResource**. L’exemple [**ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) montre comment utiliser `ImageSource.FromResource` pour charger le fichier. Le nom de ressource passé à la méthode se compose du nom de l’assembly, suivi d’un point, suivi du nom de dossier facultatif et d’un point, suivi du nom de fichier.

Le programme définit les `VerticalOptions` `HorizontalOptions` Propriétés et de la propriété `Image` sur `LayoutOptions.Center` , ce qui rend l' `Image` élément non restreint. Les `Image` bitmaps et sont de la même taille :

- Sur iOS et Android, `Image` est la taille en pixels de la bitmap. Il existe un mappage un-à-un entre les pixels de l’image bitmap et les pixels de l’écran.
- Sur plateforme Windows universelle, `Image` correspond à la taille en pixels de l’image bitmap en unités indépendantes du périphérique. Sur la plupart des appareils, chaque pixel de bitmap occupe plusieurs pixels d’écran.

L’exemple [**StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) place un `Image` dans un `StackLayout` XAML vertical. Une extension de balisage nommée [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) vous aide à référencer la ressource incorporée en XAML. Cette classe charge uniquement les ressources de l’assembly dans lequel elle se trouve. elle ne peut donc pas être placée dans une bibliothèque.

### <a name="more-on-sizing"></a>En savoir plus sur le dimensionnement

Il est souvent souhaitable de dimensionner les bitmaps de manière cohérente entre les plateformes.
En expérimentant avec **StackedBitmap**, vous pouvez définir un `WidthRequest` sur l' `Image` élément dans un vertical `StackLayout` pour rendre la taille cohérente entre les plateformes, mais vous pouvez uniquement réduire la taille à l’aide de cette technique.

Vous pouvez également définir le `HeightRequest` pour que les tailles d’image soient cohérentes sur les plateformes, mais la largeur limitée de la bitmap limitera la polyvalence de cette technique. Pour une image verticale `StackLayout` , `HeightRequest` doit être évité.

La meilleure approche consiste à commencer par une bitmap plus large que la largeur du téléphone en unités indépendantes du périphérique et `WidthRequest` à définir sur une largeur souhaitée dans les unités indépendantes du périphérique. Cela est illustré dans l’exemple [**DeviceIndBitmapSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize) .

Le [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) affiche le chapitre 7 de l’aventures d’Alice de Lewis Carroll *dans merveilles* avec les illustrations d’origine de John Tenniel :

[![Capture d’écran triple du tiers de thé Mad](images/ch13fg16-small.png "Texte de l’ouvrage de la partie thé Mad Hatten")](images/ch13fg16-large.png#lightbox "Texte de l’ouvrage de la partie thé Mad Hatten")

### <a name="browsing-and-waiting"></a>Navigation et attente

L’exemple [**ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permet à l’utilisateur de parcourir les images stockées sur le site Web Xamarin. Elle utilise la [`WebRequest`](xref:System.Net.WebRequest) classe .net pour télécharger un fichier JSON avec la liste des bitmaps.

> [!NOTE]
> Xamarin.Formsles programmes doivent utiliser [`HttpClient`](xref:System.Net.Http.HttpClient) plutôt que [`WebRequest`](xref:System.Net.WebRequest) pour accéder aux fichiers sur Internet.

Le programme utilise un [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) pour indiquer qu’un événement se passe. À mesure que chaque bitmap est chargée, la propriété en lecture seule [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) de `Image` est `true` . La `IsLoading` propriété est stockée par une propriété pouvant être liée, de sorte qu’un `PropertyChanged` événement est déclenché lorsque cette propriété change. Le programme attache un gestionnaire à cet événement et utilise le paramètre actuel de `IsLoaded` pour définir la [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) propriété de `ActivityIndicator` .

## <a name="streaming-bitmaps"></a>Bitmaps de streaming

La `ImageSource.FromStream` méthode crée un `ImageSource` basé sur un .NET `Stream` . Un `Func` objet qui retourne un objet doit être passé à la méthode `Stream` .

### <a name="accessing-the-streams"></a>Accès aux flux

L’exemple [**BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) montre comment utiliser la `ImaageSource.FromStream` méthode pour charger une image bitmap stockée en tant que ressource incorporée et charger une image bitmap sur le Web.

### <a name="generating-bitmaps-at-run-time"></a>Génération d’images bitmap au moment de l’exécution

Toutes les Xamarin.Forms plateformes prennent en charge le format de fichier BMP non compressé, qui est facile à construire dans le code, puis à stocker dans un `MemoryStream` . Cette technique permet la création algorithmique d’images bitmap au moment de l’exécution, telles qu’elles sont implémentées dans la [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe de la bibliothèque **Xamarin. FormsBook. Toolkit** .

L’exemple de [**DiyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) « do it vous » illustre l’utilisation de `BmpMaker` pour créer un bitmap avec une image dégradée.

## <a name="platform-specific-bitmaps"></a>Bitmaps spécifiques à la plateforme

Toutes les Xamarin.Forms plateformes permettent de stocker des bitmaps dans les assemblys d’application de plateforme. Lorsqu’elles sont récupérées par une Xamarin.Forms application, ces bitmaps de plateforme sont de type [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) . Vous les utilisez pour :

- la [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriété de[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- la [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriété de[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- la [`Image`](xref:Xamarin.Forms.Button) propriété de`Button`

Les assemblys de plateforme contiennent déjà des bitmaps pour les icônes et les écrans de démarrage :

- Dans le projet iOS, dans le dossier **ressources**
- Dans le projet Android, dans les sous-dossiers du dossier **ressources**
- Dans les projets Windows, dans le dossier **ressources** (bien que les plateformes Windows ne restreignent pas les bitmaps à ce dossier)

L’exemple [**PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) utilise du code pour afficher une icône à partir des projets d’application de plateforme.

### <a name="bitmap-resolutions"></a>Résolutions des bitmaps

Toutes les plateformes permettent de stocker plusieurs versions d’images bitmap pour différentes résolutions d’appareil. Au moment de l’exécution, la version appropriée est chargée en fonction de la résolution de l’appareil de l’écran.

Sur iOS, ces bitmaps sont différenciées par un suffixe sur le nom de fichier :

- Aucun suffixe pour les appareils 160 DPI (1 pixel à l’unité indépendante du périphérique)
- @2xsuffixe «» pour les appareils 320 dpi (2 pixels sur le DIU)
- @3xsuffixe «» pour les appareils 480 dpi (3 pixels au DIU)

Une image bitmap destinée à être affichée sous la forme d’un carré d’un pouce existe dans trois versions :

- MyImage. jpg à 160 pixels carrés
- MyImage@2x.jpgà 320 pixels carrés
- MyImage@3x.jpgà 480 pixels carrés

Le programme ferait référence à cette image bitmap en tant que MyImage. jpg, mais la version appropriée est récupérée au moment de l’exécution en fonction de la résolution de l’écran. En cas de non-contrainte, l’image bitmap s’affiche toujours à 160 unités indépendantes du périphérique.

Pour Android, les bitmaps sont stockées dans différents sous-dossiers du dossier **ressources** :

- dessinable-LDPI (DPI faible) pour les appareils 120 DPI (0,75 pixels au DIU)
- dessinable-MDPI (moyen) pour les appareils 160 DPI (1 pixel DIU)
- dessinable-HDPI (élevé) pour les appareils 240 DPI (1,5 pixels au DIU)
- dessinable-xhdpi (très élevé) pour les appareils 320 DPI (2 pixels au DIU)
- dessinable-xxhdpi (extra très élevé) pour les appareils 480 DPI (3 pixels au DIU)
- dessinable-xxxhdpi (trois plus hauts) pour les appareils 640 DPI (4 pixels au DIU)

Pour une image bitmap destinée à être rendue à un pouce carré, les différentes versions de la bitmap auront le même nom mais une taille différente, et seront stockées dans ces dossiers :

- Drawable-LDPI/MyImage. jpg à 120 pixels carrés
- Drawable-MDPI/MyImage. jpg à 160 pixels carrés
- Drawable-HDPI/MyImage. jpg à 240 pixels carrés
- Drawable-xhdpi/MyImage. jpg à 320 pixels carrés
- Drawable-xxhdpi/MyImage. jpg à 480 pixels carrés
- Drawable-xxxhdpi/MyImage. jpg à 640 pixels carrés

La bitmap s’affiche toujours à 160 unités indépendantes du périphérique. (Le Xamarin.Forms modèle de solution standard comprend uniquement les dossiers HDPI, xhdpi et xxhdpi.)

Le projet UWP prend en charge un modèle d’attribution de nom d’image bitmap qui se compose d’un facteur d’échelle en pixels par unité indépendante du périphérique sous la forme d’un pourcentage, par exemple :

- MyImage. Scale-200. jpg à 320 pixels carrés

Seuls certains pourcentages sont valides. Les exemples de programmes de ce livre incluent uniquement les images avec les suffixes de **Scale-200** , mais les Xamarin.Forms modèles de solution actuels incluent **Scale-100**, **scale-125**, **Scale-150**et **Scale-400**.

Quand vous ajoutez des bitmaps aux projets de plateforme, l' **action de génération** doit être :

- iOS : **BundleResource**
- Android : **AndroidResource**
- UWP : **contenu**

L’exemple [**ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) crée deux objets de type bouton composés d' `Image` éléments avec un `TapGestureRecognizer` installé. Il est prévu que les objets soient au carré d’un pouce. La `Source` propriété de `Image` est définie à l’aide des `OnPlatform` `On` objets et pour référencer des noms de fichiers potentiellement différents sur les plateformes. Les images bitmap comportent des nombres indiquant leur taille en pixels, ce qui vous permet de voir la taille de l’image bitmap récupérée et rendue.

### <a name="toolbars-and-their-icons"></a>Barres d’outils et leurs icônes

L’une des principales utilisations des bitmaps spécifiques à la plateforme est la Xamarin.Forms barre d’outils, qui est construite en ajoutant des [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objets à la [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) collection définie par `Page` . `ToobarItem`dérive de [`MenuItem`](xref:Xamarin.Forms.MenuItem) dont il hérite certaines propriétés.

Les propriétés les plus importantes `ToolbarItem` sont les suivantes :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)pour le texte qui peut s’afficher en fonction de la plateforme et`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)de type `FileImageSource` pour l’image qui peut s’afficher en fonction de la plateforme et`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)de type [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder) , une énumération avec trois membres,, [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default) [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary) et [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary) .

Le nombre d' `Primary` éléments doit être limité à trois ou quatre. Vous devez inclure un `Text` paramètre pour tous les éléments. Pour la plupart des plateformes, seuls les `Primary` éléments requièrent un, `Icon` mais Windows 8.1 nécessite un `Icon` pour tous les éléments. Les icônes doivent être des unités indépendantes du périphérique 32 carré. Le `FileImageSource` type indique qu’ils sont spécifiques à la plateforme.

Le `ToolbarItem` déclenche un [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) événement lorsqu’il est taraudé, à l’instar d’un `Button` . `ToolbarItem`prend également en charge [`Command`](xref:Xamarin.Forms.MenuItem.Command) les [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) Propriétés et souvent utilisées en relation avec MVVM. (Voir le [chapitre 18, MVVM](chapter18.md)).

IOS et Android requièrent qu’une page affichant une barre d’outils soit une [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) page ou une page à laquelle accède un `NavigationPage` . Le programme [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) définit la `MainPage` propriété de sa `App` classe sur le [ `NavigationPage` constructeur] (XREF : Xamarin.Forms . NavigationPage .% 23ctor ( Xamarin.Forms . Page)) avec un `ContentPage` argument, et illustre la construction et le gestionnaire d’événements d’une barre d’outils.

### <a name="button-images"></a>Images de bouton

Vous pouvez également utiliser des bitmaps spécifiques à la plateforme pour définir la [`Image`](xref:Xamarin.Forms.Button.Image) propriété de `Button` sur une image bitmap de 32 unités indépendantes du périphérique, carré, comme illustré par l’exemple [**ButtonImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage) .

> [!NOTE]
> L’utilisation d’images sur les boutons a été améliorée. Consultez [utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Liens connexes

- [Chapitre 13 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Exemples du chapitre 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Utilisation des images](~/xamarin-forms/user-interface/images.md)
- [Utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)

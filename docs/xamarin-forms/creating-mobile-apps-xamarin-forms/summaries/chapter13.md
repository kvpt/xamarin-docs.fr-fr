---
title: Résumé du chapitre 13. Images bitmap
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 13. Images bitmap'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5D153857-B6B7-4A14-8FB9-067DE198C2C7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e4746ed94a008d382ce15bb9cd7c52365d9ba574
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292821"
---
# <a name="summary-of-chapter-13-bitmaps"></a>Résumé du chapitre 13. Images bitmap

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)

> [!NOTE]
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

L’élément Xamarin.Forms [`Image`](xref:Xamarin.Forms.Image) affiche une bitmap. Toutes les plateformes Xamarin.Forms prennent en charge les formats de fichiers JPEG, PNG, GIF et BMP.

Bitmaps dans Xamarin.Forms viennent de quatre endroits:

- Sur le web tel que spécifié par une URL
- Intégré comme ressource dans la bibliothèque partagée
- Intégré comme ressource dans les projets d’applications de plate-forme
- De n’importe où qui `Stream` peut être référencé par un objet .NET, y compris`MemoryStream`

Les ressources Bitmap dans la bibliothèque partagée sont indépendantes de la plate-forme, tandis que les ressources bitmap dans les projets de plate-forme sont spécifiques à la plate-forme.

> [!NOTE]
> Le texte du livre fait référence à portables bibliothèques de classe, qui ont été remplacés par des bibliothèques standard .NET. Tout le code de l’échantillon du livre a été converti pour utiliser des bibliothèques standard .NET.

La bitmap est spécifiée `Image` en définissant [`ImageSource`](xref:Xamarin.Forms.ImageSource)la [`Source`](xref:Xamarin.Forms.Image.Source) propriété d’un objet de type, une classe abstraite avec trois dérivés:

- [`UriImageSource`](xref:Xamarin.Forms.UriImageSource)pour accéder à un bitmap sur `Uri` le web [`Uri`](xref:Xamarin.Forms.UriImageSource.Uri) sur la base d’un objet réglé sur sa propriété
- [`FileImageSource`](xref:Xamarin.Forms.FileImageSource)pour accéder à un bitmap stocké dans un projet d’application [`File`](xref:Xamarin.Forms.FileImageSource.File) de plate-forme basé sur un dossier et un chemin de fichier réglé à sa propriété
- [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource)pour le chargement d’une `Stream` bitmap `Stream` à `Func` l’aide [`Stream`](xref:Xamarin.Forms.StreamImageSource.Stream) d’un objet .NET spécifié en retournant un ensemble à sa propriété

Alternativement (et plus généralement) vous pouvez utiliser `ImageSource` les méthodes statiques suivantes de la classe, qui retournent `ImageSource` tous les objets:

- [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))pour accéder à un bitmap sur `Uri` le web en fonction d’un objet
- [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource*)pour accéder à une bitmap stockée comme une ressource intégrée dans l’application PCL; [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Type)) ou [`ImageSource.FromResource`](xref:Xamarin.Forms.ImageSource.FromResource(System.String,System.Reflection.Assembly)) d’accéder à un bitmap dans un autre assemblage source
- [`ImageSource.FromFile`](xref:Xamarin.Forms.ImageSource.FromFile(System.String))pour accéder à un bitmap à partir d’un projet d’application de plate-forme
- [`ImageSource.FromStream`](xref:Xamarin.Forms.ImageSource.FromStream(System.Func{System.IO.Stream}))pour le chargement d’une bitmap à partir d’un `Stream` objet

Il n’y a `Image.FromResource` pas d’équivalent de classe des méthodes. La `UriImageSource` classe est utile si vous avez besoin de contrôler la mise en cache. La `FileImageSource` classe est utile dans XAML. `StreamImageSource`est utile pour le chargement `Stream` asynchrone des objets, alors qu’il `ImageSource.FromStream` est synchrone.

## <a name="platform-independent-bitmaps"></a>Bitmaps indépendant de la plate-forme

Le projet [**WebBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapCode) charge un bitmap `ImageSource.FromUri`sur le web en utilisant . L’élément `Image` est `Content` réglé à `ContentPage`la propriété de la , de sorte qu’il est limité à la taille de la page. Quelle que soit la taille du bitmap, un élément contraint `Image` est étiré à la taille de son `Image` récipient, et la bitmap est affichée dans sa taille maximale dans l’élément tout en maintenant le rapport d’aspect de la bitmap. Zones de `Image` l’au-delà de [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)la bitmap peut être coloré avec .

[**L’échantillon WebBitmapXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/WebBitmapXaml) est similaire, mais définit simplement la `Source` propriété à l’URL. La conversion est gérée [`ImageSourceConverter`](xref:Xamarin.Forms.ImageSourceConverter) par la classe.

### <a name="fit-and-fill"></a>Ajustement et remplissage

Vous pouvez contrôler la façon dont la [`Aspect`](xref:Xamarin.Forms.Image.Aspect) bitmap est étirée en fixant la propriété de l’un `Image` des membres suivants de l’énumération: [`Aspect`](xref:Xamarin.Forms.Aspect)

- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit): respecte le rapport d’aspect (par défaut)
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill): remplit la zone, ne respecte pas le rapport d’aspect
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill): remplit la zone mais respecte le rapport d’aspect, réalisé par la culture d’une partie de la bitmap

### <a name="embedded-resources"></a>Ressources intégrées

Vous pouvez ajouter un fichier bitmap à un PCL, ou à un dossier dans le PCL. Donnez-lui une **action de construction** de **EmbeddedResource**. [**L’échantillon ResourceBitmapCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ResourceBitmapCode) montre `ImageSource.FromResource` comment utiliser pour charger le fichier. Le nom de la ressource transmis à la méthode se compose du nom de l’assemblage, suivi d’un point, suivi du nom du dossier optionnel et d’un point, suivi du nom de fichier.

Le programme `VerticalOptions` définit `HorizontalOptions` les `Image` propriétés et les propriétés de la , `LayoutOptions.Center`ce qui rend l’élément `Image` sans contrainte. Le `Image` bitmap et le bitmap sont de la même taille:

- Sur iOS et `Image` Android, c’est la taille du pixel de la bitmap. Il y a une cartographie en tête-à-tête entre les pixels bitmap et les pixels d’écran.
- Sur la plate-forme Windows Universelle, c’est `Image` la taille de pixel de la bitmap dans les unités indépendantes de l’appareil. Sur la plupart des appareils, chaque pixel bitmap occupe plusieurs pixels d’écran.

[**L’échantillon StackedBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/StackedBitmap) met un `Image` dans une verticale `StackLayout` dans XAML. Une extension de [`ImageResourceExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter13/StackedBitmap/StackedBitmap/StackedBitmap/ImageResourceExtension.cs) balisage nommée permet de référencer la ressource intégrée dans XAML. Cette classe ne charge que les ressources de l’assemblage dans lequel il est situé, de sorte qu’il ne peut pas être placé dans une bibliothèque.

### <a name="more-on-sizing"></a>En savoir plus sur le dimensionnement

Il est souvent souhaitable de taille bitmaps uniformément parmi toutes les plates-formes.
Expérimenter avec **StackedBitmap**, vous `WidthRequest` pouvez `Image` définir un `StackLayout` sur l’élément dans une verticale pour rendre la taille cohérente entre les plates-formes, mais vous ne pouvez réduire la taille en utilisant cette technique.

Vous pouvez également `HeightRequest` définir le pour rendre les tailles d’image cohérentes sur les plates-formes, mais la largeur limitée de la bitmap limitera la polyvalence de cette technique. Pour une image `StackLayout`dans `HeightRequest` une verticale, doit être évitée.

La meilleure approche est de commencer par un bitmap plus large `WidthRequest` que la largeur du téléphone dans les unités indépendantes de l’appareil et réglé à une largeur désirée dans les unités indépendantes de l’appareil. Ceci est démontré dans l’échantillon [**DeviceIndBitmapSize.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DeviceIndBitmapSize)

The [**MadTeaParty**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/MadTeaParty) présente le chapitre 7 *d’Alice’s Adventures in Wonderland de* Lewis Carroll avec les illustrations originales de John Tenniel :

[![Triple capture d’écran de la partie de thé fou](images/ch13fg16-small.png "Texte de livre de thé de Hatters de mad")](images/ch13fg16-large.png#lightbox "Texte de livre de thé de Hatters de mad")

### <a name="browsing-and-waiting"></a>Navigation et attente

[**L’échantillon ImageBrowser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageBrowser) permet à l’utilisateur de parcourir les images stock stockées sur le site Web de Xamarin. Il utilise la [`WebRequest`](xref:System.Net.WebRequest) classe .NET pour télécharger un fichier JSON avec la liste des bitmaps.

> [!NOTE]
> Les programmes Xamarin.Forms devraient être utilisés [`HttpClient`](xref:System.Net.Http.HttpClient) plutôt que [`WebRequest`](xref:System.Net.WebRequest) pour accéder aux fichiers sur Internet.

Le programme [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) utilise un pour indiquer que quelque chose se passe. Comme chaque bitmap est le [`IsLoading`](xref:Xamarin.Forms.Image.IsLoading) chargement, la propriété de lecture seulement de `Image` est `true`. La `IsLoading` propriété est soutenue par une `PropertyChanged` propriété liant, de sorte qu’un événement est déclenché lorsque cette propriété change. Le programme attache un gestionnaire à cet événement, `IsLoaded` et [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) utilise le `ActivityIndicator`cadre actuel de pour définir la propriété de la .

## <a name="streaming-bitmaps"></a>Bitmaps en streaming

La `ImageSource.FromStream` méthode `ImageSource` crée un basé `Stream`sur un .NET . La méthode doit `Func` être passée `Stream` un objet qui renvoie un objet.

### <a name="accessing-the-streams"></a>Accès aux flux

[**L’échantillon BitmapStreams**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/BitmapStreams) montre comment `ImaageSource.FromStream` utiliser la méthode pour charger une bitmap stockée comme une ressource intégrée, et de charger une bitmap sur le web.

### <a name="generating-bitmaps-at-run-time"></a>Génération de bitmaps à l’heure de course

Toutes les plates-formes Xamarin.Forms prennent en charge le format de fichier BMP `MemoryStream`non compressé, qui est facile à construire en code, puis stocker dans un . Cette technique permet de créer algorithmiquement des bitmaps au moment de l’exécution, tel qu’implémenté dans la [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) classe dans la bibliothèque **Xamrin.FormsBook.Toolkit.**

L’échantillon [**diyGradientBitmap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/DiyGradientBitmap) "Do It Yourself" `BmpMaker` démontre l’utilisation de pour créer une bitmap avec une image de gradient.

## <a name="platform-specific-bitmaps"></a>Bitmaps spécifiques à la plate-forme

Toutes les plates-formes Xamarin.Forms permettent de stocker des bitmaps dans les assemblages d’applications de la plate-forme. Lorsqu’ils sont récupérés par une application Xamarin.Forms, ces bitmaps plate-forme sont de type [`FileImageSource`](xref:Xamarin.Forms.FileImageSource). Vous les utilisez pour :

- la [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriété de[`MenuItem`](xref:Xamarin.Forms.MenuItem)
- la [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) propriété de[`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem)
- la [`Image`](xref:Xamarin.Forms.Button) propriété de`Button`

Les assemblages de plate-forme contiennent déjà des bitmaps pour les icônes et les écrans d’éclaboussures :

- Dans le projet iOS, dans le dossier **Ressources**
- Dans le projet Android, dans les sous-dossiers du dossier **Ressources**
- Dans les projets Windows, dans le dossier **Assets** (bien que les plates-formes Windows ne restreignent pas les bitmaps à ce dossier)

[**L’échantillon PlatformBitmaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/PlatformBitmaps) utilise le code pour afficher une icône des projets d’application de la plate-forme.

### <a name="bitmap-resolutions"></a>Résolutions Bitmap

Toutes les plates-formes permettent de stocker plusieurs versions d’images bitmap pour différentes résolutions d’appareils. Au moment de l’exécution, la version appropriée est chargée en fonction de la résolution de l’appareil de l’écran.

Sur iOS, ces bitmaps sont différenciés par un suffixe sur le nom de fichier :

- Pas de suffixe pour 160 appareils DPI (1 pixel à l’unité indépendante de l’appareil)
- '@2xsuffixe pour 320 appareils DPI (2 pixels au DIU)
- '@3xsuffixe pour 480 appareils DPI (3 pixels au DIU)

Une bitmap destinée à être affichée comme un pouce carré existerait en trois versions:

- MyImage.jpg à 160 pixels carrés
- MyImage@2x.jpgà 320 pixels carrés
- MyImage@3x.jpgà 480 pixels carrés

Le programme se référerait à ce bitmap comme MyImage.jpg, mais la version appropriée est récupérée au moment de l’exécution en fonction de la résolution de l’écran. Lorsqu’il n’est pas mis à rude épreuve, le bitmap sera toujours rendu à 160 unités indépendantes de l’appareil.

Pour Android, les bitmaps sont stockés dans divers sous-dossiers du dossier **Ressources** :

- drawable-ldpi (faible DPI) pour 120 appareils DPI (0,75 pixels au DIU)
- drawable-mdpi (moyen) pour 160 appareils DPI (1 pixel au DIU)
- drawable-hdpi (haut) pour 240 appareils DPI (1,5 pixels au DIU)
- drawable-xhdpi (extra high) pour 320 appareils DPI (2 pixels au DIU)
- drawable-xxhdpi (extra extra high) pour 480 appareils DPI (3 pixels au DIU)
- drawable-xxxhdpi (trois sommets supplémentaires) pour 640 appareils DPI (4 pixels au DIU)

Pour une bitmap destinée à être rendue à un pouce carré, les différentes versions de la bitmap auront le même nom, mais une taille différente, et être stockés dans ces dossiers:

- drawable-ldpi/MyImage.jpg à 120 pixels carrés
- drawable-mdpi/MyImage.jpg à 160 pixels carrés
- drawable-hdpi/MyImage.jpg à 240 pixels carrés
- drawable-xhdpi/MyImage.jpg à 320 pixels carrés
- drawable-xxhdpi/MyImage.jpg à 480 pixels carrés
- drawable-xxxhdpi/MyImage.jpg à 640 pixels carrés

Le bitmap sera toujours rendu à 160 unités indépendantes de l’appareil. (Le modèle standard de solution Xamarin.Forms ne comprend que les dossiers hdpi, xhdpi et xxhdpi.)

Le projet UWP prend en charge un système de nommage de bitmap qui consiste en un facteur de mise à l’échelle des pixels par unité indépendante de l’appareil en pourcentage, par exemple:

- MyImage.scale-200.jpg à 320 pixels carrés

Seuls quelques pourcentages sont valides. Les programmes d’exemple de ce livre ne comprennent que des images avec des suffixes **à l’échelle de 200,** mais les modèles actuels de solution Xamarin.Forms comprennent **l’échelle-100**, **l’échelle-125**, **l’échelle-150**, et **l’échelle-400**.

Lors de l’ajout de bitmaps aux projets de plate-forme, **l’action Build** devrait être :

- iOS: **BundleResource**
- Android: **AndroidResource**
- UWP: **Contenu**

[**L’échantillon ImageTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ImageTap) crée deux objets `Image` bouton-like composés d’éléments avec un `TapGestureRecognizer` installé. Il est prévu que les objets soient carrés d’un pouce. La `Source` propriété `Image` de `OnPlatform` est `On` définie à l’aide et des objets pour référencer des noms de fichiers potentiellement différents sur les plates-formes. Les images de bitmap incluent des nombres indiquant leur taille de pixel, ainsi vous pouvez voir quelle bitmap de taille est récupéré et rendu.

### <a name="toolbars-and-their-icons"></a>Les barres d’outils et leurs icônes

L’une des principales utilisations de bitmaps spécifiques à la plate-forme [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) est la [`ToolbarItems`](xref:Xamarin.Forms.Page.ToolbarItems) barre `Page`d’outils Xamarin.Forms, qui est construit en ajoutant des objets à la collection définie par . `ToobarItem`dérive d’où [`MenuItem`](xref:Xamarin.Forms.MenuItem) il hérite de certaines propriétés.

Les propriétés les plus importantes `ToolbarItem` sont :

- [`Text`](xref:Xamarin.Forms.MenuItem.Text)pour le texte qui peut apparaître en fonction de la plate-forme et`Order`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)de `FileImageSource` type pour l’image qui peut apparaître en fonction de la plate-forme et`Order`
- [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)de [`ToolbarItemOrder`](xref:Xamarin.Forms.ToolbarItemOrder)type , un recensement avec [`Default`](xref:Xamarin.Forms.ToolbarItemOrder.Default) [`Primary`](xref:Xamarin.Forms.ToolbarItemOrder.Primary)trois [`Secondary`](xref:Xamarin.Forms.ToolbarItemOrder.Secondary)membres, , , et .

Le nombre `Primary` d’articles devrait être limité à trois ou quatre. Vous devez `Text` inclure un paramètre pour tous les articles. Pour la plupart `Primary` des plates-formes, seuls les éléments nécessitent un `Icon` mais Windows 8.1 nécessite un `Icon` pour tous les éléments. Les icônes doivent être 32 unités indépendantes de l’appareil carré. Le `FileImageSource` type indique qu’ils sont spécifiques à la plate-forme.

Les `ToolbarItem` feux [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) d’un événement lorsqu’il est exploité, un peu comme un `Button`. `ToolbarItem`également [`Command`](xref:Xamarin.Forms.MenuItem.Command) des [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) supports et des propriétés souvent utilisées en relation avec MVVM. (Voir [chapitre 18, MVVM](chapter18.md)).

IOS et Android exigent qu’une page qui [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) affiche une barre d’outils soit une ou une page naviguée par un `NavigationPage`. Le programme [**ToolbarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ToolbarDemo) `MainPage` fixe `App` la propriété de sa `ContentPage` catégorie au [ `NavigationPage` constructeur](xref:Xamarin.Forms.NavigationPage.%23ctor(Xamarin.Forms.Page)) avec un argument, et démontre la construction et le gestionnaire d’événements d’une barre d’outils.

### <a name="button-images"></a>Images de boutons

Vous pouvez également utiliser des bitmaps [`Image`](xref:Xamarin.Forms.Button.Image) spécifiques `Button` à la plate-forme pour définir la propriété d’un bitmap de 32 unités indépendantes de l’appareil carré, comme démontré par l’échantillon [**ButtonImage.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13/ButtonImage)

> [!NOTE]
> L’utilisation d’images sur les boutons a été améliorée. Voir [Utilisation de bitmaps avec des boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons).

## <a name="related-links"></a>Liens connexes

- [Chapitre 13 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch13-Apr2016.pdf)
- [Échantillons du chapitre 13](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter13)
- [Travailler avec Images](~/xamarin-forms/user-interface/images.md)
- [Utilisation de bitmaps avec boutons](~/xamarin-forms/user-interface/button.md#using-bitmaps-with-buttons)

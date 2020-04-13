---
title: Résumé du chapitre 2. Anatomie d’une application
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 2. Anatomie d’une application'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292285"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Résumé du chapitre 2. Anatomie d’une application

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Dans une application Xamarin.Forms, les objets qui occupent l’espace à l’écran sont connus sous le nom *d’éléments visuels,* encapsulés par la [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe. Les éléments visuels peuvent être divisés en trois catégories correspondant à ces classes :

- [Page](xref:Xamarin.Forms.Page)
- [Mise en page](xref:Xamarin.Forms.Layout)
- [Afficher](xref:Xamarin.Forms.View)

Un `Page` dérivé occupe tout l’écran, ou presque tout l’écran. Souvent, l’enfant d’une page est un `Layout` dérivé pour organiser des éléments visuels pour enfants. Les enfants `Layout` de la `Layout` peut `View` être d’autres classes ou dérivés (souvent appelés *éléments*), qui sont des objets familiers tels que le texte, bitmaps, curseurs, boutons, boîtes de liste, et ainsi de suite.

Ce chapitre montre comment créer une application [`Label`](xref:Xamarin.Forms.Label)en se `View` concentrant sur le , qui est le dérivé qui affiche le texte.

## <a name="say-hello"></a>Dis bonjour

Avec la plate-forme Xamarin installée, vous pouvez créer une nouvelle solution Xamarin.Forms dans Visual Studio ou Visual Studio pour Mac. La solution [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) utilise une bibliothèque de classe portable pour le code commun.

> [!NOTE]
> Les bibliothèques de classe portatives ont été remplacées par des bibliothèques standard .NET. Tout le code de l’échantillon du livre a été converti pour utiliser des bibliothèques standard .NET.

Cet échantillon démontre une solution Xamarin.Forms créée en Visual Studio sans modifications. La solution se compose de quatre projets :

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), une bibliothèque de classe portable (PCL) partagée par les autres projets
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un projet d’application pour Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), un projet d’application pour iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un projet d’application pour la plate-forme Windows universelle (Windows 10 et Windows 10 Mobile)

> [!NOTE]
> Xamarin.Forms ne prend plus en charge Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mais les applications Xamarin.Forms s’exécutent sur le bureau Windows 10.

Vous pouvez faire l’un de ces projets d’application le projet de démarrage, puis construire et exécuter le programme sur un appareil ou un simulateur.

Dans bon nombre de vos programmes Xamarin.Forms, vous ne modifierez pas les projets d’applications. Ceux-ci restent souvent de minuscules talons juste pour démarrer le programme. La plupart de votre objectif sera la bibliothèque commune à toutes les applications.

## <a name="inside-the-files"></a>À l’intérieur des fichiers

Les visuels affichés par le programme **Bonjour** sont [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) définis dans le constructeur de la classe. `App`dérive de la classe [`Application`](xref:Xamarin.Forms.Application)Xamarin.Forms .

> [!NOTE]
> Les modèles de solution Visual Studio pour Xamarin.Forms créent une page avec un fichier XAML. XAML n’est pas couvert dans ce livre [jusqu’au chapitre 7](chapter07.md).

La section **Références** du projet **Hello** PCL comprend les assemblages Xamarin.Forms suivants :

- **Xamarin.Forms.Core Xamarin.Forms.Core Xamarin.Forms.Core Xama**
- **Xamarin.Forms.Xaml Xamarin.Forms.Xaml Xamarin.Forms.Xaml Xama**
- **Xamarin.Forms.Platform Xamarin.Forms.Platform Xamarin.Forms.Platform Xama**

Les **sections Références** des cinq projets d’application comprennent des assemblages supplémentaires qui s’appliquent aux différentes plateformes :

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT Xamarin.Forms.Platform.WinRT Xamarin.Forms.Platform.WinRT Xama**
- **Xamarin.Forms.Platform.WinRT.Tablet Xamarin.Forms.Platform.WinRT.Tablet Xamarin.Forms.Platform.WinRT.Tablet Xama**
- **Xamarin.Forms.Platform.WinRT.Téléphone**

> [!NOTE]
> Les **sections Références** de ces projets n’énumèrent plus les assemblées. Au lieu de cela, le fichier de projet contient un **packageReference** tags faisant référence à l’ensemble Xamarin.Forms NuGet. La section **Références** dans Visual Studio répertorie le package **Xamarin.Forms** plutôt que les assemblages Xamarin.Forms.

Chacun des projets d’application `Forms.Init` contient un `Xamarin.Forms` appel à la méthode statique dans l’espace de nom. Ceci initialise la bibliothèque Xamarin.Forms. Une version `Forms.Init` différente de est définie pour chaque plate-forme. Les appels à cette méthode peuvent être trouvés dans les classes suivantes:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe, `OnLaunched` méthode](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

En outre, chaque plate-forme `App` doit instantané l’emplacement de la classe dans la bibliothèque partagée. Cela se produit `LoadApplication` dans un appel à dans les classes suivantes:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Uwp:[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Sinon, ces projets d’application sont des programmes normaux de « ne rien faire ».

## <a name="pcl-or-sap"></a>PCL ou SAP?

Il est possible de créer une solution Xamarin.Forms avec le code commun dans une bibliothèque de classe portable (PCL) ou un projet d’actif partagé (SAP). Pour créer une solution SAP, sélectionnez l’option Partagée dans Visual Studio. La solution [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) démontre le modèle SAP sans modifications.

> [!NOTE]
> Les bibliothèques de classe portables ont été remplacées par des bibliothèques standard .NET. Tout le code de l’échantillon du livre a été converti pour utiliser des bibliothèques standard .NET. Sinon, les bibliothèques PCL et .NET Standard sont conceptuellement très similaires.

L’approche de la bibliothèque regroupe tout le code commun dans un projet de bibliothèque référencé par les projets d’application de la plate-forme. Avec l’approche SAP, le code commun existe effectivement dans tous les projets d’application de plate-forme et est partagé entre eux.

La plupart des développeurs de Xamarin.Forms préfèrent l’approche de la bibliothèque. Dans ce livre, la plupart des solutions utilisent une bibliothèque. Ceux qui utilisent SAP comprennent un suffixe **Sap** dans le nom du projet.

Avec l’approche SAP, le code du projet partagé peut exécuter différents codes`#if`pour`elif`les `#endif`différentes plates-formes en utilisant les directives préprocessoires de CMD (, et ) avec ces identificateurs prédéfinis :

- Ios:`__IOS__`
- Android : `__ANDROID__`
- Uwp:`WINDOWS_UWP`

Dans une bibliothèque partagée, vous pouvez déterminer la plate-forme sur laquelle vous exécutez au moment de l’exécution, comme vous le verrez plus loin dans ce chapitre.

## <a name="labels-for-text"></a>Étiquettes pour texte

La solution [**Salutations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) montre comment ajouter un nouveau fichier Cmd au projet **Salutations.** Ce fichier définit une `GreetingsPage` classe nommée `ContentPage`qui dérive de . Dans ce livre, la `ContentPage` plupart des projets contiennent un seul `Page` dérivé dont le nom est le nom du projet avec le suffixe joint.

Le `GreetingsPage` constructeur instantané une [`Label`](xref:Xamarin.Forms.Label) vue, qui est le Xamarin.Forms voir qui affiche le texte. La [`Text`](xref:Xamarin.Forms.Label.Text) propriété est réglée sur `Label`le texte affiché par le . Ce programme `Label` définit `Content` la `ContentPage`propriété de . Le constructeur de `App` la classe `GreetingsPage` les instantanéise `MainPage` alors et le place à sa propriété.

Le texte s’affiche dans le coin supérieur gauche de la page. Sur iOS, cela signifie qu’il chevauche la barre d’état de la page. Il existe plusieurs solutions à ce problème :

### <a name="solution-1-include-padding-on-the-page"></a>Solution 1. Inclure le rembourrage sur la page

Réglez une [`Padding`](xref:Xamarin.Forms.Page.Padding) propriété sur la page. `Padding`est de [`Thickness`](xref:Xamarin.Forms.Thickness)type , une structure avec quatre propriétés:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`définit une zone à l’intérieur d’une page où le contenu est exclu. Cela permet `Label` d’éviter de souscrire la barre de statut iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solution 2. Inclure le rembourrage juste pour iOS (SAP seulement)

Définissez une propriété 'Padding' uniquement sur iOS à l’aide d’un SAP avec une directive préprocesseur C. Cela est démontré dans la solution [**GreetingsSap.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solution 3. Inclure le rembourrage juste pour iOS (PCL ou SAP)

Dans la version de Xamarin.Forms utilisé `Padding` pour le livre, une propriété spécifique à iOS [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) dans un PCL ou SAP peut être sélectionnée en utilisant la méthode ou statique. [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) Ces méthodes sont maintenant dépréciées

Les `Device.OnPlatform` méthodes sont utilisées pour exécuter un code spécifique à la plate-forme ou pour sélectionner des valeurs spécifiques à la plate-forme. À l’interne, [`Device.OS`](xref:Xamarin.Forms.Device.OS) ils utilisent la propriété statique de [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) lecture seulement, qui renvoie un membre de l’énumération :

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)pour les appareils UWP.

Les `Device.OnPlatform` méthodes, `Device.OS` la propriété `TargetPlatform` et l’énumération sont toutes maintenant dépréciées. Utilisez plutôt [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) la propriété `string` et comparez la valeur de rendement avec les champs statiques suivants :

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la chaîne "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), la chaîne "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la chaîne "UWP", se référant à la plate-forme Windows universelle

La [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) propriété statique de lecture-seulement est liée. Cela renvoie un [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)membre de la , qui a ces membres:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)n’est pas utilisé

Pour iOS et Android, `Tablet` le `Phone` seuil entre et est une largeur de portrait de 600 unités. Pour la plate-forme Windows, `Desktop` indique une application UWP fonctionnant sous Windows 10, et `Phone` indique une application UWP fonctionnant sous l’application Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solution 3a. Définir la marge sur l’étiquette

La [`Margin`](xref:Xamarin.Forms.View.Margin) propriété a été introduite trop tard pour être incluse `Thickness` dans le livre, `Label` mais elle est également de type et vous pouvez la définir sur le pour définir une zone en dehors de la vue qui est inclus dans le calcul de la disposition de la vue.

La `Padding` propriété n’est disponible que sur [`Layout`](xref:Xamarin.Forms.Layout) et [`Page`](xref:Xamarin.Forms.Page) les dérivés. La `Margin` propriété est [`View`](xref:Xamarin.Forms.View) disponible sur tous les produits dérivés.

## <a name="solution-4-center-the-label-within-the-page"></a>Solution 4. Centrez l’étiquette dans la page

Vous pouvez `Label` centrer `Page` l’intérieur de la (ou le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) mettre [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) dans `Label` l’un des [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)huit autres endroits) en définissant le et les propriétés de la à une valeur de type . La `LayoutOptions` structure définit deux propriétés :

- Une [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) propriété [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)de type , un recensement [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)avec quatre membres: , ce [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) [`End`](xref:Xamarin.Forms.LayoutAlignment.End)qui signifie gauche ou en haut [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)en fonction de l’orientation, , , ce qui signifie à droite ou en bas en fonction de l’orientation, et .

- Une [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) propriété `bool`de type .

En général, ces propriétés ne sont pas utilisées directement. Au lieu de cela, les combinaisons de ces deux `LayoutOptions`propriétés sont fournies par huit propriétés statiques de type :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`et `VerticalOptions` sont les propriétés les plus importantes dans la disposition Xamarin.Forms, et sont discutés plus en détail dans [**le chapitre 4. Faire défiler la pile**](chapter04.md).

Voici le résultat avec `HorizontalOptions` `VerticalOptions` le `Label` et les `LayoutOptions.Center`propriétés des deux ensemble à:

[![Triple capture d’écran du programme de salutations](images/ch02fg05-small.png "Étiquette horizontalement et verticalement centrée")](images/ch02fg05-large.png#lightbox "Étiquette horizontalement et verticalement centrée")

## <a name="solution-5-center-the-text-within-the-label"></a>Solution 5. Centrez le texte dans l’étiquette

Vous pouvez également centrer le texte (ou le placer dans [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) huit `Label` autres endroits sur [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) la page) en définissant le et les propriétés d’un membre de l’énumération:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), c’est-à-dire gauche ou en haut (selon l’orientation)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), c’est-à-dire à droite ou en bas (selon l’orientation)

Ces deux propriétés `Label`ne sont `HorizontalAlignment` `VerticalAlignment` définies que `View` par, `View` tandis que les propriétés et les propriétés sont définies et héritées par tous les dérivés. Les résultats visuels peuvent sembler similaires, mais ils sont très différents comme le montre le chapitre suivant.

## <a name="related-links"></a>Liens connexes

- [Texte intégral du chapitre 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Échantillons du chapitre 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Échantillons du chapitre 2 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Bien démarrer avec Xamarin.Forms](~/get-started/index.yml)

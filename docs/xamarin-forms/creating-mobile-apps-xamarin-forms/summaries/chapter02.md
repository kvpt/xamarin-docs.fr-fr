---
title: Résumé du chapitre 2. Anatomie d’une application
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 2. Anatomie d’une application'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292285"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Résumé du chapitre 2. Anatomie d’une application

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Dans une application Xamarin. Forms, les objets qui occupent de l’espace sur l’écran sont appelés *éléments visuels*, encapsulés par la classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Éléments visuels peuvent être divisées en trois catégories correspondant à ces classes :

- [Page](xref:Xamarin.Forms.Page)
- [Disposition](xref:Xamarin.Forms.Layout)
- [Afficher](xref:Xamarin.Forms.View)

Un `Page` dérivé occupe tout l’écran, ou presque l’écran entier. Souvent, l’enfant d’une page est un `Layout` dérivé pour organiser les éléments visuels enfants. Les enfants du `Layout` peuvent être d’autres `Layout` classes ou `View` dérivés (souvent appelées *éléments*), qui sont des objets familiers tels que du texte, des bitmaps, des curseurs, des boutons, des zones de liste, etc.

Ce chapitre explique comment créer une application en vous concentrant sur le [`Label`](xref:Xamarin.Forms.Label), qui est la `View` dérivée qui affiche du texte.

## <a name="say-hello"></a>Saluez

Sur la plateforme Xamarin est installée, vous pouvez créer une solution Xamarin.Forms dans Visual Studio ou Visual Studio pour Mac. La solution [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) utilise une bibliothèque de classes portable pour le code commun.

> [!NOTE]
> Bibliothèques de classes portables ont été remplacés par des bibliothèques .NET Standard. Exemples de code à partir de l’ouvrage a été converti pour utiliser les bibliothèques .NET standard.

Cet exemple montre une solution Xamarin.Forms créée dans Visual Studio sans aucune modification. La solution se compose de quatre projets :

- [**Bonjour**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), une bibliothèque de classes portable (PCL) partagée par les autres projets
- [**Hello. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un projet d’application pour Android
- [**Hello. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), projet d’application pour iOS
- [**Hello. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un projet d’application pour le plateforme Windows universelle (Windows 10 et Windows 10 mobile)

> [!NOTE]
> Xamarin.Forms ne gère plus de Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mais les applications Xamarin.Forms s’exécutent sur le bureau Windows 10.

Vous pouvez apporter l’une de ces projets d’application du projet de démarrage, puis générer et exécuter le programme sur un appareil ou un simulateur.

Dans la plupart de vos programmes de Xamarin.Forms, vous ne modifient les projets d’application. Souvent, ils restent minuscules stubs simplement pour lancer le programme. La majeure partie de votre objectif sera de la bibliothèque commune à toutes les applications.

## <a name="inside-the-files"></a>Dans les fichiers

Les visuels affichés par le programme **Hello** sont définis dans le constructeur de la classe [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) . `App` dérive de la classe Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application).

> [!NOTE]
> Les modèles de solution Visual Studio pour Xamarin.Forms créent une page avec un fichier XAML. XAML n’est pas couvert dans ce livre avant le [chapitre 7](chapter07.md).

La section **références** du projet PCL **Hello** contient les assemblys Xamarin. Forms suivants :

- **Xamarin. Forms. Core**
- **Xamarin. Forms. Xaml**
- **Xamarin. Forms. Platform**

Les sections **références** des cinq projets d’application incluent des assemblys supplémentaires qui s’appliquent aux plateformes individuelles :

- **Xamarin. Forms. Platform. Android**
- **Xamarin. Forms. Platform. iOS**
- **Xamarin. Forms. Platform. UWP**
- **Xamarin. Forms. Platform. WinRT**
- **Xamarin. Forms. Platform. WinRT. Tablet**
- **Xamarin. Forms. Platform. WinRT. Phone**

> [!NOTE]
> Les sections **références** de ces projets ne répertorient plus les assemblys. Au lieu de cela, le fichier projet contient des balises **PackageReference** qui référencent le package NuGet Xamarin. Forms. La section **références** dans Visual Studio répertorie le package **Xamarin. Forms** plutôt que les assemblys Xamarin. Forms.

Chacun des projets d’application contient un appel à la méthode statique `Forms.Init` dans l’espace de noms `Xamarin.Forms`. Ce code initialise la bibliothèque Xamarin.Forms. Une version différente de `Forms.Init` est définie pour chaque plateforme. Vous trouverez les appels à cette méthode dans les classes suivantes :

- iOS : [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android : [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP : [classe`App`, méthode `OnLaunched`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

En outre, chaque plateforme doit instancier l’emplacement de la classe `App` dans la bibliothèque partagée. Cela se produit dans un appel à `LoadApplication` dans les classes suivantes :

- iOS : [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android : [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP : [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Sinon, ces projets d’application sont des programmes de « ne rien faire » normal.

## <a name="pcl-or-sap"></a>Bibliothèque de classes portable ou SAP ?

Il est possible de créer une solution Xamarin.Forms avec le code commun dans une bibliothèque de classes Portable (PCL) ou un projet de ressource partagé (SAP). Pour créer une solution SAP, sélectionnez l’option partagé dans Visual Studio. La solution [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) illustre le modèle SAP sans aucune modification.

> [!NOTE]
> Bibliothèques de classes portables a été remplacée par des bibliothèques .NET Standard. Exemples de code à partir de l’ouvrage a été converti pour utiliser les bibliothèques .NET standard. Sinon, les bibliothèques .NET Standard et de bibliothèque de classes portable sont conceptuellement très similaires.

Les regroupements d’approche de bibliothèque tous les courantes de code dans un projet de bibliothèque référencé par les projets d’application de plateforme. Avec l’approche SAP, le code commun existe dans tous les projets d’application de plateforme efficacement et est partagé entre eux.

La plupart des développeurs Xamarin.Forms préfère l’approche de la bibliothèque. Dans cet ouvrage, la plupart des solutions utilisent une bibliothèque. Ceux qui utilisent SAP incluent un suffixe **SAP** dans le nom du projet.

Avec l’approche SAP, le code du projet partagé peut exécuter un code différent pour les différentes plateformes C# à l’aide des directives de préprocesseur (`#if`, #`elif`et `#endif`) avec ces identificateurs prédéfinis :

- iOS : `__IOS__`
- Android : `__ANDROID__`
- UWP : `WINDOWS_UWP`

Dans une bibliothèque partagée, vous pouvez déterminer la plateforme que vous exécutez lors de l’exécution, comme vous le verrez plus loin dans ce chapitre.

## <a name="labels-for-text"></a>Étiquettes de texte

La solution [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) montre comment ajouter un nouveau C# fichier au projet **Greetings** . Ce fichier définit une classe nommée `GreetingsPage` qui dérive de `ContentPage`. Dans ce livre, la plupart des projets contiennent une seule `ContentPage` dérivée dont le nom est le nom du projet avec le suffixe `Page` ajouté.

Le constructeur `GreetingsPage` instancie une vue de [`Label`](xref:Xamarin.Forms.Label) , qui est la vue Xamarin. Forms qui affiche le texte. La propriété [`Text`](xref:Xamarin.Forms.Label.Text) est définie sur le texte affiché par le `Label`. Ce programme définit la `Label` sur la propriété `Content` de `ContentPage`. Le constructeur de la classe `App` instancie ensuite `GreetingsPage` et le définit sur sa propriété `MainPage`.

Le texte est affiché dans le coin supérieur gauche de la page. Sur iOS, cela signifie qu’il chevauche barre d’état de la page. Il existe plusieurs solutions à ce problème :

### <a name="solution-1-include-padding-on-the-page"></a>Solution 1. Inclure la marge intérieure dans la page

Définissez une propriété [`Padding`](xref:Xamarin.Forms.Page.Padding) sur la page. `Padding` est de type [`Thickness`](xref:Xamarin.Forms.Thickness), une structure avec quatre propriétés :

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` définit une zone à l’intérieur d’une page où le contenu est exclu. Cela permet à l' `Label` d’éviter le remplacement de la barre d’État iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solution 2. Inclure la marge intérieure simplement pour iOS (SAP uniquement)

Définir une propriété « Marge » uniquement sur iOS à l’aide d’un SAP avec une directive de préprocesseur c#. Cela est illustré dans la solution [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solution 3. Inclure la marge intérieure simplement pour iOS (bibliothèque de classes portable ou SAP)

Dans la version de Xamarin. Forms utilisée pour le livre, une propriété `Padding` spécifique à iOS dans un PCL ou un SAP peut être sélectionnée à l’aide de la méthode statique [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) ou [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) . Ces méthodes sont désormais déconseillés.

Les méthodes de `Device.OnPlatform` sont utilisées pour exécuter du code spécifique à la plateforme ou pour sélectionner des valeurs spécifiques à la plateforme. En interne, ils utilisent la propriété en lecture seule statique [`Device.OS`](xref:Xamarin.Forms.Device.OS) , qui retourne un membre de l’énumération [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) :

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) pour les appareils UWP.

Les méthodes `Device.OnPlatform`, la propriété `Device.OS` et l’énumération `TargetPlatform` sont désormais dépréciées. Utilisez plutôt la propriété [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) et comparez la valeur renvoyée `string` avec les champs statiques suivants :

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la chaîne « iOS »
- [`Android`](xref:Xamarin.Forms.Device.Android), la chaîne « Android »
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la chaîne « UWP », qui fait référence au plateforme Windows universelle

La propriété en lecture seule statique [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) est liée. Cela retourne un membre du [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom), qui contient les membres suivants :

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) n’est pas utilisé

Pour iOS et Android, la coupure entre `Tablet` et `Phone` est une largeur Portrait de 600 unités. Pour la plateforme Windows, `Desktop` indique une application UWP s’exécutant sous Windows 10, et `Phone` indique une application UWP s’exécutant sous l’application Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>3 a de solution. Définir la marge sur l’étiquette

La propriété [`Margin`](xref:Xamarin.Forms.View.Margin) a été introduite trop tard pour être incluse dans le livre, mais elle est également de type `Thickness` et vous pouvez la définir sur le `Label` pour définir une zone en dehors de la vue qui est incluse dans le calcul de la disposition de la vue.

La propriété `Padding` n’est disponible que sur les dérivés de [`Layout`](xref:Xamarin.Forms.Layout) et de [`Page`](xref:Xamarin.Forms.Page) . La propriété `Margin` est disponible sur tous les dérivés de [`View`](xref:Xamarin.Forms.View) .

## <a name="solution-4-center-the-label-within-the-page"></a>Solution 4. Centrer l’étiquette dans la page

Vous pouvez centrer les `Label` au sein du `Page` (ou le placer dans l’un des huit autres emplacements) en définissant les propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) du `Label` sur une valeur de type [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions). La structure `LayoutOptions` définit deux propriétés :

- Une propriété [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) de type [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment), une énumération avec quatre membres : [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), c’est-à-dire à gauche ou en haut en fonction de l’orientation, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End), c’est-à-dire à droite ou en bas, selon l’orientation et [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Propriété [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) de type `bool`.

Généralement ces propriétés ne sont pas utilisées directement. Au lieu de cela, les combinaisons de ces deux propriétés sont fournies par huit propriétés en lecture seule statiques de type `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` et `VerticalOptions` sont les propriétés les plus importantes de la disposition Xamarin. Forms. elles sont abordées plus en détail dans le [**Chapitre 4. Défilement de la pile**](chapter04.md).

Voici le résultat des propriétés `HorizontalOptions` et `VerticalOptions` de `Label` définies sur `LayoutOptions.Center`:

[![Capture d’écran triple du programme Greetings](images/ch02fg05-small.png "Étiquette centrée horizontalement et verticalement")](images/ch02fg05-large.png#lightbox "Étiquette centrée horizontalement et verticalement")

## <a name="solution-5-center-the-text-within-the-label"></a>Solution 5. Centrer le texte dans l’étiquette

Vous pouvez également centrer le texte (ou le placer dans huit autres emplacements sur la page) en définissant les propriétés [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) et [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) de `Label` sur un membre de l’énumération [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) :

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), c’est-à-dire gauche ou haut (selon l’orientation)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), c’est-à-dire à droite ou en bas (selon l’orientation)

Ces deux propriétés sont définies uniquement par `Label`, tandis que les propriétés `HorizontalAlignment` et `VerticalAlignment` sont définies par `View` et héritées par tous les `View` dérivés. Les résultats visuels peuvent sembler similaires, mais ils sont très différents, comme illustré dans le chapitre suivant.

## <a name="related-links"></a>Liens connexes

- [Chapitre 2 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Exemples du chapitre 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Exemples du F# chapitre 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Prise en main avec Xamarin. Forms](~/get-started/index.yml)

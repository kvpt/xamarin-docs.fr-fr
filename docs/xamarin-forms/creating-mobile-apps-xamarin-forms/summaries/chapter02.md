---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 2. Anatomy of an app''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78da3ed91acea0c056074d712d368de70b251392
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136914"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Résumé du chapitre 2. Anatomie d’une application

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

Dans une Xamarin.Forms application, les objets qui occupent de l’espace sur l’écran sont appelés *éléments visuels*, encapsulés par la [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe. Les éléments visuels peuvent être répartis en trois catégories correspondant à ces classes :

- [Page](xref:Xamarin.Forms.Page)
- [Disposition](xref:Xamarin.Forms.Layout)
- [Afficher](xref:Xamarin.Forms.View)

Un `Page` dérivé occupe tout l’écran, ou presque la totalité de l’écran. Souvent, l’enfant d’une page est un `Layout` dérivé permettant d’organiser les éléments visuels enfants. Les enfants de `Layout` peuvent être d’autres `Layout` classes ou `View` dérivées (souvent appelées *éléments*), qui sont des objets familiers tels que du texte, des bitmaps, des curseurs, des boutons, des zones de liste, etc.

Ce chapitre explique comment créer une application en vous concentrant sur [`Label`](xref:Xamarin.Forms.Label) , qui est la `View` dérivée qui affiche du texte.

## <a name="say-hello"></a>Say Hello

Une fois la plateforme Xamarin installée, vous pouvez créer une nouvelle Xamarin.Forms solution dans Visual Studio ou Visual Studio pour Mac. La solution [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) utilise une bibliothèque de classes portable pour le code commun.

> [!NOTE]
> Les bibliothèques de classes portables ont été remplacées par des bibliothèques de .NET Standard. Tout l’exemple de code du livre a été converti pour utiliser des bibliothèques .NET standard.

Cet exemple illustre une Xamarin.Forms solution créée dans Visual Studio sans aucune modification. La solution se compose de quatre projets :

- [**Bonjour**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), une bibliothèque de classes portable (PCL) partagée par les autres projets
- [**Hello. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), un projet d’application pour Android
- [**Hello. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), projet d’application pour iOS
- [**Hello. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), un projet d’application pour le plateforme Windows universelle (Windows 10 et Windows 10 mobile)

> [!NOTE]
> Xamarin.Formsne prend plus en charge Windows 8.1, Windows Phone 8,1 ou Windows 10 mobile, mais Xamarin.Forms les applications s’exécutent sur le bureau Windows 10.

Vous pouvez faire de ces projets d’application le projet de démarrage, puis générer et exécuter le programme sur un appareil ou un simulateur.

Dans la plupart de vos Xamarin.Forms programmes, vous ne modifierez pas les projets d’application. Ces derniers sont souvent des petits stubs pour démarrer le programme. La plus grande partie de votre attention sera la bibliothèque commune à toutes les applications.

## <a name="inside-the-files"></a>À l’intérieur des fichiers

Les visuels affichés par le programme **Hello** sont définis dans le constructeur de la [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App`dérive de la Xamarin.Forms classe [`Application`](xref:Xamarin.Forms.Application) .

> [!NOTE]
> Modèles de solution Visual Studio pour Xamarin.Forms créer une page avec un fichier XAML. XAML n’est pas couvert dans ce livre avant le [chapitre 7](chapter07.md).

La section **références** du projet PCL **Hello** comprend les assemblys suivants Xamarin.Forms :

- **Xamarin.Forms. Ebauche**
- **Xamarin.Forms. Langage**
- **Xamarin.Forms. Multi**

Les sections **références** des cinq projets d’application incluent des assemblys supplémentaires qui s’appliquent aux plateformes individuelles :

- **Xamarin.Forms. Platform. Android**
- **Xamarin.Forms. Platform. iOS**
- **Xamarin.Forms. Platform. UWP**
- **Xamarin.Forms. Platform. WinRT**
- **Xamarin.Forms. Platform. WinRT. Tablet**
- **Xamarin.Forms. Platform. WinRT. Phone**

> [!NOTE]
> Les sections **références** de ces projets ne répertorient plus les assemblys. Au lieu de cela, le fichier projet contient des balises **PackageReference** qui référencent le Xamarin.Forms package NuGet. La section **références** dans Visual Studio répertorie le **Xamarin.Forms** package plutôt que les Xamarin.Forms assemblys.

Chacun des projets d’application contient un appel à la `Forms.Init` méthode statique dans l' `Xamarin.Forms` espace de noms. La bibliothèque est initialisée Xamarin.Forms . Une version différente de `Forms.Init` est définie pour chaque plateforme. Les appels à cette méthode se trouvent dans les classes suivantes :

- Libéré[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Tablette[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP : [ `App` classe, `OnLaunched` méthode](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

En outre, chaque plateforme doit instancier l' `App` emplacement de la classe dans la bibliothèque partagée. Cela se produit dans un appel à `LoadApplication` dans les classes suivantes :

- Libéré[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Tablette[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Dans le cas contraire, ces projets d’application sont des programmes « ne rien faire » normaux.

## <a name="pcl-or-sap"></a>PCL ou SAP ?

Il est possible de créer une Xamarin.Forms solution avec le code commun dans une bibliothèque de classes portable (PCL) ou un projet de ressources partagées (SAP). Pour créer une solution SAP, sélectionnez l’option partagé dans Visual Studio. La solution [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) illustre le modèle SAP sans aucune modification.

> [!NOTE]
> Les bibliothèques de classes portables ont été remplacées par les bibliothèques de .NET Standard. Tout l’exemple de code du livre a été converti pour utiliser des bibliothèques .NET standard. Dans le cas contraire, les bibliothèques PCL et .NET Standard sont conceptuellement très similaires.

L’approche de la bibliothèque regroupe tout le code commun dans un projet de bibliothèque référencé par les projets d’application de plateforme. Avec l’approche SAP, le code commun existe effectivement dans tous les projets d’application de plateforme et est partagé entre eux.

La plupart des Xamarin.Forms développeurs préfèrent l’approche de la bibliothèque. Dans ce livre, la plupart des solutions utilisent une bibliothèque. Ceux qui utilisent SAP incluent un suffixe **SAP** dans le nom du projet.

Avec l’approche SAP, le code du projet partagé peut exécuter un code différent pour les différentes plateformes en utilisant les directives de préprocesseur C# ( `#if` , # `elif` et `#endif` ) avec ces identificateurs prédéfinis :

- Libéré`__IOS__`
- Android : `__ANDROID__`
- UWP`WINDOWS_UWP`

Dans une bibliothèque partagée, vous pouvez déterminer la plateforme que vous utilisez lors de l’exécution, comme vous le verrez plus loin dans ce chapitre.

## <a name="labels-for-text"></a>Étiquettes de texte

La solution [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) montre comment ajouter un nouveau fichier C# au projet **Greetings** . Ce fichier définit une classe nommée `GreetingsPage` qui dérive de `ContentPage` . Dans cet ouvrage, la plupart des projets contiennent une seule `ContentPage` dérivée dont le nom est le nom du projet dont le suffixe est `Page` ajouté.

Le `GreetingsPage` constructeur instancie une [`Label`](xref:Xamarin.Forms.Label) vue, qui est la Xamarin.Forms vue qui affiche du texte. La [`Text`](xref:Xamarin.Forms.Label.Text) propriété est définie sur le texte affiché par le `Label` . Ce programme affecte la `Label` valeur à la `Content` propriété de `ContentPage` . Le constructeur de la `App` classe instancie ensuite `GreetingsPage` et le définit sur sa `MainPage` propriété.

Le texte est affiché dans l’angle supérieur gauche de la page. Sur iOS, cela signifie qu’elle chevauche la barre d’état de la page. Il existe plusieurs solutions à ce problème :

### <a name="solution-1-include-padding-on-the-page"></a>Solution 1. Inclure le remplissage sur la page

Définissez une [`Padding`](xref:Xamarin.Forms.Page.Padding) propriété sur la page. `Padding`est de type [`Thickness`](xref:Xamarin.Forms.Thickness) , une structure avec quatre propriétés :

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`définit une zone à l’intérieur d’une page où le contenu est exclu. Cela permet `Label` à d’éviter de remplacer la barre d’État iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solution 2. Inclure le remplissage uniquement pour iOS (SAP uniquement)

Définissez une propriété « Padding » uniquement sur iOS à l’aide d’une directive de préprocesseur SAP avec une directive de préprocesseur C#. Cela est illustré dans la solution [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solution 3. Inclure le remplissage uniquement pour iOS (PCL ou SAP)

Dans la version de Xamarin.Forms utilisée pour le livre, une `Padding` propriété spécifique à iOS dans un PCL ou un SAP peut être sélectionnée à l’aide de la [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) méthode statique ou. Ces méthodes sont désormais déconseillées

Les `Device.OnPlatform` méthodes sont utilisées pour exécuter du code spécifique à la plateforme ou pour sélectionner des valeurs spécifiques à la plateforme. En interne, ils utilisent la [`Device.OS`](xref:Xamarin.Forms.Device.OS) propriété en lecture seule statique, qui retourne un membre de l' [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) énumération :

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)pour les appareils UWP.

Les `Device.OnPlatform` méthodes, la `Device.OS` propriété et l' `TargetPlatform` énumération sont désormais dépréciées. Utilisez plutôt la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propriété et comparez la `string` valeur de retour avec les champs statiques suivants :

- [`iOS`](xref:Xamarin.Forms.Device.iOS), la chaîne « iOS »
- [`Android`](xref:Xamarin.Forms.Device.Android), la chaîne « Android »
- [`UWP`](xref:Xamarin.Forms.Device.UWP), la chaîne « UWP », qui fait référence au plateforme Windows universelle

La [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) propriété en lecture seule statique est liée. Cela retourne un membre du [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) , qui contient les membres suivants :

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)n’est pas utilisé

Pour iOS et Android, la coupure entre `Tablet` et `Phone` est une largeur portrait de 600 unités. Pour la plateforme Windows, `Desktop` indique une application UWP s’exécutant sous Windows 10 et `Phone` indique une application UWP s’exécutant sous l’application Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solution 3A. Définir la marge sur l’étiquette

La [`Margin`](xref:Xamarin.Forms.View.Margin) propriété a été introduite trop tard pour être incluse dans le livre, mais elle est également de type `Thickness` et vous pouvez la définir sur le `Label` pour définir une zone en dehors de la vue qui est incluse dans le calcul de la disposition de la vue.

La `Padding` propriété est uniquement disponible sur [`Layout`](xref:Xamarin.Forms.Layout) les [`Page`](xref:Xamarin.Forms.Page) dérivés de et. La `Margin` propriété est disponible sur tous les [`View`](xref:Xamarin.Forms.View) dérivés.

## <a name="solution-4-center-the-label-within-the-page"></a>Solution 4. Centrer l’étiquette dans la page

Vous pouvez centrer le `Label` dans le `Page` (ou le placer dans l’un des huit autres emplacements) en affectant [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) à la propriété et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de la `Label` valeur de type [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) . La `LayoutOptions` structure définit deux propriétés :

- Une [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) propriété de type [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) , une énumération avec quatre membres : [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) , qui signifie gauche ou haut en fonction de l’orientation, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) ,, c’est-à-dire [`End`](xref:Xamarin.Forms.LayoutAlignment.End) à droite ou en bas, selon l’orientation, et [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill) .

- [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)Propriété de type `bool` .

En général, ces propriétés ne sont pas utilisées directement. Au lieu de cela, les combinaisons de ces deux propriétés sont fournies par huit propriétés statiques en lecture seule de type `LayoutOptions` :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`et `VerticalOptions` sont les propriétés les plus importantes de la Xamarin.Forms mise en page. elles sont abordées plus en détail dans le [**Chapitre 4. Défilement de la pile**](chapter04.md).

Voici le résultat avec les `HorizontalOptions` Propriétés et `VerticalOptions` des `Label` deux ayant la valeur `LayoutOptions.Center` :

[![Capture d’écran triple du programme Greetings](images/ch02fg05-small.png "Étiquette centrée horizontalement et verticalement")](images/ch02fg05-large.png#lightbox "Étiquette centrée horizontalement et verticalement")

## <a name="solution-5-center-the-text-within-the-label"></a>Solution 5. Centrer le texte dans l’étiquette

Vous pouvez également centrer le texte (ou le placer dans huit autres emplacements sur la page) en affectant [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) à la propriété et de la valeur `Label` un membre de l' [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) énumération :

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), c’est-à-dire gauche ou haut (selon l’orientation)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), c’est-à-dire à droite ou en bas (selon l’orientation)

Ces deux propriétés sont définies uniquement par `Label` , tandis que les `HorizontalAlignment` `VerticalAlignment` Propriétés et sont définies par `View` et héritées par tous les `View` dérivés. Les résultats visuels peuvent sembler similaires, mais ils sont très différents comme le montre le chapitre suivant.

## <a name="related-links"></a>Liens connexes

- [Chapitre 2 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Exemples du chapitre 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Exemples de chapitre 2 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Prise en main avecXamarin.Forms](~/get-started/index.yml)

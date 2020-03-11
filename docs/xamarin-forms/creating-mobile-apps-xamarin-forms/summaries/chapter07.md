---
title: Résumé du chapitre 7. XAML et code
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 7. XAML et code'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334742"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Résumé du chapitre 7. XAML et code

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Xamarin.Forms prend en charge un langage de balisage basé sur XML appelé l’Extensible Application Markup Language ou le XAML (prononcé « zammel »). XAML offre une alternative à c# dans la définition de la disposition de l’interface utilisateur d’une application Xamarin.Forms et dans la définition des liaisons entre les éléments d’interface utilisateur et les données sous-jacentes.

## <a name="properties-and-attributes"></a>Propriétés et les attributs

Structures et classes de Xamarin.Forms deviennent des éléments XML dans XAML, et les propriétés de ces classes et des structures deviennent des attributs XML. Pour être instancié en XAML, une classe doit avoir généralement d’un constructeur sans paramètre public. Toutes les propriétés définies en XAML doivent avoir des accesseurs `set` publics.

Pour les propriétés des types de données de base (`string`, `double`, `bool`, etc.), l’analyseur XAML utilise les méthodes de `TryParse` standard pour convertir les paramètres d’attribut en ces types. L’analyseur XAML peut également gérer facilement les types énumération, et il peut combiner des membres de l’énumération si le type énumération est marqué avec l’attribut `Flags`.

Pour aider l’analyseur XAML, les types plus complexes (ou les propriétés de ces types) peuvent inclure une [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) qui identifie une classe qui dérive de [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) qui prend en charge la conversion de valeurs de chaîne en ces types. Par exemple, le [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) convertit des noms de couleur et des chaînes, tels que « #rrggbb », en valeurs de `Color`.

## <a name="property-element-syntax"></a>Syntaxe d’élément de propriété

Dans XAML, les classes et les objets créés à partir de celles-ci sont exprimées en tant qu’éléments XML. Celles-ci sont appelées *éléments objets*. La plupart des propriétés de ces objets sont exprimées en tant qu’attributs XML. Il s’agit des *attributs de propriété*.

Parfois, une propriété doit être définie à un objet qui ne peuvent pas être exprimé sous forme de chaîne simple. Dans ce cas, le code XAML prend en charge une balise appelée *élément de propriété* qui se compose du nom de la classe et du nom de la propriété séparés par un point. Un élément objet peut ensuite apparaître dans une paire de balises d’élément de propriété.

## <a name="adding-a-xaml-page-to-your-project"></a>Ajout d’une page XAML à votre projet

Une bibliothèque de classes Portable Xamarin.Forms peut contenir une page XAML quand il est tout d’abord créé, ou vous pouvez ajouter une page XAML à un projet existant. Dans la boîte de dialogue permettant d’ajouter un nouvel élément, choisissez l’élément qui fait référence à une page XAML, ou `ContentPage` et XAML. (Pas un `ContentView`.)

> [!NOTE]
> Options de Visual Studio ont été modifiés depuis la rédaction de ce chapitre.

Deux fichiers sont créés : un fichier XAML avec la .xaml d’extension de nom de fichier et un fichier c# avec l’extension. xaml.cs. Le C# fichier est souvent appelé *code-behind* du fichier XAML. Le fichier code-behind est une définition de classe partielle qui dérive de `ContentPage`. Au moment de la génération, le XAML est analysé et une autre définition de classe partielle est générée pour la même classe. Cette classe générée comprend une méthode nommée `InitializeComponent` appelée à partir du constructeur du fichier code-behind.

Pendant l’exécution, à la fin de l’appel de `InitializeComponent`, tous les éléments du fichier XAML ont été instanciés et initialisés exactement comme s’ils avaient été créés C# dans le code.

L’élément racine dans le fichier XAML est `ContentPage`. La balise racine contient au moins deux déclarations d’espaces de noms XML, une pour les éléments Xamarin. Forms et l’autre qui définit un préfixe `x` pour les éléments et les attributs intrinsèques à toutes les implémentations XAML. La balise racine contient également un attribut `x:Class` qui indique l’espace de noms et le nom de la classe qui dérive de `ContentPage`. Cela correspond à l’espace de noms et le nom dans le fichier code-behind.

La combinaison du code XAML et du code est illustrée par l’exemple [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) .

## <a name="the-xaml-compiler"></a>Le compilateur XAML

Xamarin. Forms a un compilateur XAML, mais son utilisation est facultative en fonction de l’utilisation d’un [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Si le XAML n’est pas compilé, le XAML est analysé au moment de la génération et le fichier XAML est incorporé dans la bibliothèque PCL, où il est également analysé lors de l’exécution. Si le XAML est compilé, le processus de génération convertit le XAML dans un format binaire et le traitement de l’exécution est plus efficace.

## <a name="platform-specificity-in-the-xaml-file"></a>Spécificité de plateforme dans le fichier XAML

En XAML, la classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) peut être utilisée pour sélectionner le balisage dépendant de la plateforme. Il s’agit d’une classe générique qui doit être instanciée avec un attribut `x:TypeArguments` qui correspond au type cible. La classe [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) est similaire, mais elle est plus rarement utilisée.

L’utilisation de `OnPlatform` a changé depuis la publication du livre. Il a été utilisé à l’origine avec les propriétés nommées `iOS`, `Android`et `WinPhone`. Il est désormais utilisé avec les objets de [`On`](xref:Xamarin.Forms.On) enfants. Affectez à la propriété [`Platform`](xref:Xamarin.Forms.On.Platform) une chaîne cohérente avec les champs de `const` publics de la classe [`Device`](xref:Xamarin.Forms.Device) . Affectez à la propriété [`Value`](xref:Xamarin.Forms.On.Value) une valeur cohérente avec l’attribut `x:TypeArguments` de la balise `OnPlatform`.

`OnPlatform` est illustré dans l’exemple [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) , appelé, car il contient des blocs de code XAML presque identique. L’existence de ce balisage et répétitive suggère que les techniques doivent être disponibles pour la réduire.

## <a name="the-content-property-attributes"></a>Les attributs de propriété de contenu

Certains éléments de propriété se produisent très fréquemment, tels que la balise `<ContentPage.Content>` sur l’élément racine d’un `ContentPage`ou la balise `<StackLayout.Children>` qui encadre les enfants de `StackLayout`.

Chaque classe est autorisée à identifier une propriété avec un [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) sur la classe. Pour cette propriété, les balises d’élément de propriété ne sont pas nécessaires. `ContentPage` définit sa propriété de contenu comme `Content`et `Layout<T>` (la classe à partir de laquelle `StackLayout` dérive) définit sa propriété de contenu comme `Children`. Ces balises d’élément de propriété ne sont pas nécessaires.

L’élément de propriété de `Label` est `Text`.

## <a name="formatted-text"></a>Texte mis en forme

L’exemple [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contient plusieurs exemples de définition des propriétés `Text` et `FormattedText` de `Label`. En XAML, les objets `Span` apparaissent en tant qu’enfants de l’objet `FormattedString`.

 Quand une chaîne multiligne est définie sur la propriété `Text`, les caractères de fin de ligne sont convertis en espaces, mais les caractères de fin de ligne sont conservés quand une chaîne multiligne apparaît comme contenu des balises `Label` ou `Label.Text` :

 [![Capture d’écran triple du partage des variantes de texte](images/ch07fg03-small.png "Variantes du texte mis en forme")](images/ch07fg03-large.png#lightbox "Variantes du texte mis en forme")

## <a name="related-links"></a>Liens connexes

- [Chapitre 7 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Exemples du chapitre 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Exemple de F# chapitre 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Notions de base XAML](~/xamarin-forms/xaml/xaml-basics/index.md)

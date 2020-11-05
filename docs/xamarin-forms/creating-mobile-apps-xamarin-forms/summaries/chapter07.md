---
title: Résumé du chapitre 7. XAML et code
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 7. XAML et code'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d69c5c96138024f6a45d71013b259b2c47a20da3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370176"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Résumé du chapitre 7. XAML et code

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Xamarin.Forms prend en charge un langage de balisage basé sur XML appelé Extensible Application Markup Language ou XAML (prononcez « zammel »). XAML offre une alternative à C# dans la définition de la disposition de l’interface utilisateur d’une Xamarin.Forms application, ainsi que dans la définition des liaisons entre les éléments d’interface utilisateur et les données sous-jacentes.

## <a name="properties-and-attributes"></a>Propriétés et attributs

Xamarin.Forms les classes et les structures deviennent des éléments XML en XAML, et les propriétés de ces classes et structures deviennent des attributs XML. Pour être instancié en XAML, une classe doit en général avoir un constructeur sans paramètre public. Toutes les propriétés définies en XAML doivent avoir des `set` accesseurs publics.

Pour les propriétés des types de données de base ( `string` , `double` , `bool` , etc.), l’analyseur XAML utilise les `TryParse` méthodes standard pour convertir les paramètres d’attribut en ces types. L’analyseur XAML peut également gérer facilement les types énumération, et il peut combiner des membres de l’énumération si le type énumération est marqué avec l' `Flags` attribut.

Pour aider l’analyseur XAML, les types plus complexes (ou les propriétés de ces types) peuvent inclure un [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) qui identifie une classe qui dérive de [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) qui prend en charge la conversion des valeurs de chaîne vers ces types. Par exemple, le [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) convertit les noms et les chaînes de couleur, tels que « #rrggbb », en `Color` valeurs.

## <a name="property-element-syntax"></a>Syntaxe des éléments de propriété

En XAML, les classes et les objets créés à partir de celles-ci sont exprimés en tant qu’éléments XML. Celles-ci sont appelées *éléments objets*. La plupart des propriétés de ces objets sont exprimées en tant qu’attributs XML. Il s’agit des *attributs de propriété*.

Parfois, une propriété doit être définie sur un objet qui ne peut pas être exprimé sous forme de chaîne simple. Dans ce cas, le code XAML prend en charge une balise appelée *élément de propriété* qui se compose du nom de la classe et du nom de la propriété séparés par un point. Un élément objet peut alors apparaître dans une paire de balises d’éléments de propriété.

## <a name="adding-a-xaml-page-to-your-project"></a>Ajout d’une page XAML à votre projet

Une Xamarin.Forms bibliothèque de classes portable peut contenir une page XAML lorsqu’elle est créée pour la première fois, ou vous pouvez ajouter une page XAML à un projet existant. Dans la boîte de dialogue pour ajouter un nouvel élément, choisissez l’élément qui fait référence à une page XAML, ou `ContentPage` XAML. (Pas `ContentView` .)

> [!NOTE]
> Les options Visual Studio ont été modifiées depuis l’écriture de ce chapitre.

Deux fichiers sont créés : un fichier XAML avec l’extension de nom de fichier. xaml et un fichier C# avec l’extension. xaml.cs. Le fichier C# est souvent appelé *code-behind* du fichier XAML. Le fichier code-behind est une définition de classe partielle qui dérive de `ContentPage` . Au moment de la génération, le code XAML est analysé et une autre définition de classe partielle est générée pour la même classe. Cette classe générée comprend une méthode nommée `InitializeComponent` qui est appelée à partir du constructeur du fichier code-behind.

Pendant l’exécution, à la fin de l' `InitializeComponent` appel, tous les éléments du fichier XAML ont été instanciés et initialisés exactement comme s’ils avaient été créés dans du code C#.

L’élément racine dans le fichier XAML est `ContentPage` . La balise racine contient au moins deux déclarations d’espaces de noms XML, une pour les Xamarin.Forms éléments et l’autre qui définit un `x` préfixe pour les éléments et les attributs intrinsèques à toutes les implémentations XAML. La balise racine contient également un `x:Class` attribut qui indique l’espace de noms et le nom de la classe qui dérive de `ContentPage` . Cela correspond à l’espace de noms et au nom de la classe dans le fichier code-behind.

La combinaison du code XAML et du code est illustrée par l’exemple [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) .

## <a name="the-xaml-compiler"></a>Compilateur XAML

Xamarin.Forms a un compilateur XAML, mais son utilisation est facultative en fonction de l’utilisation d’un [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) . Si le XAML n’est pas compilé, le code XAML est analysé au moment de la génération, et le fichier XAML est incorporé dans la bibliothèque PCL, où il est également analysé au moment de l’exécution. Si le XAML est compilé, le processus de génération convertit le code XAML au format binaire, et le traitement du runtime est plus efficace.

## <a name="platform-specificity-in-the-xaml-file"></a>Spécificité de la plateforme dans le fichier XAML

En XAML, la [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe peut être utilisée pour sélectionner le balisage dépendant de la plateforme. Il s’agit d’une classe générique qui doit être instanciée avec un `x:TypeArguments` attribut qui correspond au type cible. La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe est similaire, mais elle est plus rarement utilisée.

L’utilisation de `OnPlatform` a changé depuis la publication du livre. Il a été utilisé à l’origine avec les propriétés nommées `iOS` , `Android` et `WinPhone` . Il est désormais utilisé avec les [`On`](xref:Xamarin.Forms.On) objets enfants. Affectez [`Platform`](xref:Xamarin.Forms.On.Platform) à la propriété une chaîne cohérente avec les `const` champs publics de la [`Device`](xref:Xamarin.Forms.Device) classe. Affectez [`Value`](xref:Xamarin.Forms.On.Value) à la propriété une valeur cohérente avec l' `x:TypeArguments` attribut de la `OnPlatform` balise.

`OnPlatform` est illustré dans l’exemple [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) , appelé, car il contient des blocs de code XAML presque identique. L’existence de ce balisage répétitive suggère que des techniques doivent être disponibles pour le réduire.

## <a name="the-content-property-attributes"></a>Attributs de la propriété de contenu

Certains éléments de propriété se produisent très fréquemment, tels que la `<ContentPage.Content>` balise de l’élément racine d’un `ContentPage` ou la `<StackLayout.Children>` balise qui encadre les enfants de `StackLayout` .

Chaque classe est autorisée à identifier une propriété avec un [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) sur la classe. Pour cette propriété, les balises d’élément de propriété ne sont pas requises. `ContentPage` définit sa propriété de contenu comme `Content` , et `Layout<T>` (la classe à partir de laquelle `StackLayout` dérive) définit sa propriété de contenu comme `Children` . Ces balises d’élément de propriété ne sont pas requises.

L’élément de propriété de `Label` est `Text` .

## <a name="formatted-text"></a>Texte mis en forme

L’exemple [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contient plusieurs exemples de définition des `Text` `FormattedText` Propriétés et de `Label` . En XAML, `Span` les objets apparaissent en tant qu’enfants de l' `FormattedString` objet.

 Lorsqu’une chaîne multiligne est définie sur la `Text` propriété, les caractères de fin de ligne sont convertis en espaces, mais les caractères de fin de ligne sont conservés quand une chaîne multiligne apparaît en tant que contenu des `Label` `Label.Text` balises ou :

 [![Capture d’écran triple du partage des variantes de texte](images/ch07fg03-small.png "Variantes du texte mis en forme")](images/ch07fg03-large.png#lightbox "Variantes du texte mis en forme")

## <a name="related-links"></a>Liens connexes

- [Chapitre 7 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Exemples du chapitre 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Chapitre 7-exemple F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Notions de base XAML](~/xamarin-forms/xaml/xaml-basics/index.md)

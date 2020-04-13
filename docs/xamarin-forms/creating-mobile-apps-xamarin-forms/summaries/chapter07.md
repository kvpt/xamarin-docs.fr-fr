---
title: Résumé du chapitre 7. XAML et code
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 7. XAML et code'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334742"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Résumé du chapitre 7. XAML et code

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Xamarin.Forms prend en charge un langage de balisage basé sur XML appelé Extensible Application Markup Language ou XAML (prononcé "zammel"). XAML offre une alternative à Cmd dans la définition de la disposition de l’interface utilisateur d’une application Xamarin.Forms, et dans la définition des liaisons entre les éléments d’interface utilisateur et les données sous-jacentes.

## <a name="properties-and-attributes"></a>Propriétés et attributs

Les classes et les structures de Xamarin.Forms deviennent des éléments XML dans XAML, et les propriétés de ces classes et structures deviennent des attributs XML. Pour être instantanée dans XAML, une classe doit généralement avoir un constructeur public sans paramètres. Toutes les propriétés établies `set` dans XAML doivent avoir des accessoirors publics.

Pour les propriétés des`string` `double`types `bool`de données de base ( , , `TryParse` , et ainsi de suite), le parser XAML utilise les méthodes standard pour convertir les paramètres d’attribut à ces types. Le analyseur XAML peut également facilement gérer les types d’énumération, et il peut combiner `Flags` les membres de recensement si le type de recensement est signalé avec l’attribut.

Pour aider le parser XAML, les types plus complexes [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) (ou les propriétés [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) de ces types) peuvent inclure un qui identifie une classe qui dérive à partir de laquelle prend en charge la conversion des valeurs de chaîne à ces types. Par exemple, [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) les convertit les noms de couleurs et `Color` les cordes, tels que « #rrggbb », en valeurs.

## <a name="property-element-syntax"></a>Syntaxe d’élément de propriété

Dans XAML, les classes et les objets créés à partir d’eux sont exprimés comme des éléments XML. Ceux-ci sont connus sous le nom *d’éléments d’objet*. La plupart des propriétés de ces objets sont exprimées comme attributs XML. Ceux-ci sont appelés *attributs de propriété*.

Parfois, une propriété doit être réglée à un objet qui ne peut pas être exprimé comme une chaîne simple. Dans un tel cas, XAML prend en charge une étiquette appelée *élément de propriété* qui se compose du nom de classe et du nom de propriété séparés par une période. Un élément d’objet peut alors apparaître dans une paire de balises d’élément de propriété.

## <a name="adding-a-xaml-page-to-your-project"></a>Ajout d’une page XAML à votre projet

Une bibliothèque de classe portable Xamarin.Forms peut contenir une page XAML lorsqu’elle est créée pour la première fois, ou vous pouvez ajouter une page XAML à un projet existant. Dans le dialogue pour ajouter un nouvel élément, choisissez l’élément qui `ContentPage` se réfère à une page XAML, ou et XAML. (Pas `ContentView`un .)

> [!NOTE]
> Les options Visual Studio ont changé depuis l’écriture de ce chapitre.

Deux fichiers sont créés : un fichier XAML avec l’extension du nom de fichier .xaml, et un fichier C avec l’extension .xaml.cs. Le fichier C est souvent appelé le *code-derrière* du fichier XAML. Le fichier de code est une définition `ContentPage`partielle de classe qui dérive de . Au moment de la construction, le XAML est analysé et une autre définition partielle de classe est générée pour la même classe. Cette classe générée comprend `InitializeComponent` une méthode nommée qui est appelée à partir du constructeur du fichier de code-derrière.

Pendant le temps d’exécution, à la fin de l’appel, `InitializeComponent` tous les éléments du fichier XAML ont été instantanés et paralés comme s’ils avaient été créés dans le code C.

L’élément racine dans le `ContentPage`fichier XAML est . L’étiquette racine contient au moins deux déclarations XML namespace, l’une `x` pour les éléments Xamarin.Forms et l’autre définissant un préfixe pour les éléments et les attributs intrinsèques à toutes les implémentations XAML. L’étiquette racine `x:Class` contient également un attribut qui indique l’espace `ContentPage`de nom et le nom de la classe qui dérive de . Cela correspond à l’espace de nom et le nom de classe dans le fichier de code-derrière.

La combinaison de XAML et de code est démontrée par l’échantillon [**CodePlusXaml.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

## <a name="the-xaml-compiler"></a>Le compilateur XAML

Xamarin.Forms a un compilateur XAML, mais son utilisation [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)est facultative basée sur l’utilisation d’un . Si le XAML n’est pas compilé, le XAML est analysé au moment de la construction, et le fichier XAML est intégré dans le PCL, où il est également analysé au moment de l’exécution. Si le XAML est compilé, le processus de construction convertit le XAML en une forme binaire, et le traitement de durée d’exécution est plus efficace.

## <a name="platform-specificity-in-the-xaml-file"></a>Spécificité de la plate-forme dans le fichier XAML

Dans XAML, [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) la classe peut être utilisée pour sélectionner la plate-forme dépendante du balisage. Il s’agit d’une classe générique `x:TypeArguments` et doit être instantanée avec un attribut qui correspond au type cible. La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe est similaire, mais utilisé beaucoup moins souvent.

L’utilisation `OnPlatform` de a changé depuis la publication du livre. Il a été utilisé à `iOS` `Android`l’origine en conjonction avec des propriétés nommées , , et `WinPhone`. Il est maintenant [`On`](xref:Xamarin.Forms.On) utilisé avec des objets pour enfants. Définissez [`Platform`](xref:Xamarin.Forms.On.Platform) la propriété à une `const` chaîne [`Device`](xref:Xamarin.Forms.Device) compatible avec les champs publics de la classe. Définissez [`Value`](xref:Xamarin.Forms.On.Value) la propriété à `x:TypeArguments` une valeur `OnPlatform` compatible avec l’attribut de l’étiquette.

`OnPlatform`est démontré dans l’échantillon [**ScaryColorList,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) ainsi appelé parce qu’il contient des blocs de presque identique XAML. L’existence de cette majoration répétitive suggère que des techniques devraient être disponibles pour la réduire.

## <a name="the-content-property-attributes"></a>La propriété de contenu attribue

Certains éléments de propriété se `<ContentPage.Content>` produisent assez fréquemment, `ContentPage`comme l’étiquette sur l’élément racine d’un , ou l’étiquette `<StackLayout.Children>` qui entoure les enfants de `StackLayout`.

Chaque classe est autorisée à [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) identifier une propriété avec un sur la classe. Pour cette propriété, les étiquettes d’élément de propriété ne sont pas exigées. `ContentPage`définit sa propriété `Content`de `Layout<T>` contenu comme , `StackLayout` et (la classe à `Children`partir de laquelle dérive) définit sa propriété de contenu comme . Ces étiquettes d’élément de propriété ne sont pas exigées.

L’élément `Label` de `Text`propriété de est .

## <a name="formatted-text"></a>Texte mis en forme

[**L’échantillon textVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contient plusieurs `Text` `FormattedText` exemples `Label`de réglage et propriétés de . Dans XAML, `Span` les objets `FormattedString` apparaissent comme des enfants de l’objet.

 Lorsqu’une chaîne multiline `Text` est réglée sur la propriété, les caractères de fin de ligne sont convertis en personnages spatiaux, `Label` `Label.Text` mais les caractères de fin de ligne sont conservés lorsqu’une chaîne multiline apparaît sous forme de contenu ou d’étiquettes :

 [![Triple capture d’écran du partage des variations de texte](images/ch07fg03-small.png "Variations de texte formatées")](images/ch07fg03-large.png#lightbox "Variations de texte formatées")

## <a name="related-links"></a>Liens connexes

- [Chapitre 7 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Échantillons du chapitre 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Échantillon du chapitre 7 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Notions de base XAML](~/xamarin-forms/xaml/xaml-basics/index.md)

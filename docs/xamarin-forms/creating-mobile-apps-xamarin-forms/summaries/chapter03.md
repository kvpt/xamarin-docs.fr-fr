---
title: Résumé du chapitre 3. Détails du texte
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 3. Détails du texte'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "72980925"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Résumé du chapitre 3. Détails du texte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Ce chapitre explore l’affichage [`Label`](xref:Xamarin.Forms.Label) plus en détail, y compris la couleur, les polices et la mise en forme.

## <a name="wrapping-paragraphs"></a>Habillage des paragraphes

Lorsque la propriété [`Text`](xref:Xamarin.Forms.Label.Text) d' `Label` contient du texte long, `Label` l’encapsule automatiquement sur plusieurs lignes, comme le montre l’exemple [**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . Vous pouvez incorporer des codes Unicode tels que « \u2014 » pour le cadratin, ou C# caractères comme « \r » pour arrêter l’exécution vers une nouvelle ligne.

Lorsque les propriétés [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) et [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) d’un `Label` sont définies sur `LayoutOptions.Fill`, la taille globale du `Label` est régie par l’espace que son conteneur met à disposition. La `Label` est dite *contraction*. La taille de la `Label` correspond à la taille de son conteneur.

Lorsque les propriétés `HorizontalOptions` et `VerticalOptions` sont définies sur des valeurs autres que `LayoutOptions.Fill`, la taille du `Label` est régie par l’espace requis pour le rendu du texte, jusqu’à la taille que son conteneur met à disposition pour le `Label`. La `Label` est dite non *contrainte* et détermine sa propre taille.

(Remarque : les termes *contrainte* et sans *contrainte* peuvent être exintuitifs, car une vue non contrainte est généralement plus petite qu’une vue contrainte. En outre, ces termes ne sont pas utilisés régulièrement dans les premiers chapitres du livre.)

Un affichage tel qu’un `Label` peut être contraint dans une dimension et sans contrainte dans l’autre. Un `Label` renvoie uniquement du texte sur plusieurs lignes s’il est restreint horizontalement.

Si une `Label` est limitée, elle peut occuper beaucoup plus d’espace que nécessaire pour le texte. Le texte peut être positionné à l’intérieur de la zone globale du `Label`. Affectez à la propriété [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) la valeur d’un membre de l’énumération [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [`Center`](xref:Xamarin.Forms.TextAlignment.Center)ou [`End`](xref:Xamarin.Forms.TextAlignment.Center)) pour contrôler l’alignement de toutes les lignes du paragraphe. La valeur par défaut est `Start` et aligne à gauche le texte.

Affectez à la propriété [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) la valeur d’un membre de l’énumération `TextAlignment` pour positionner le texte en haut, au centre ou en bas de la zone occupée par le `Label`.

Affectez à la propriété [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) la valeur d’un membre de l’énumération [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation) [,`MiddleTruncation`ou ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation) [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) pour contrôler la façon dont les lignes multiples dans un saut de paragraphe ou sont tronquées.

## <a name="text-and-background-colors"></a>Couleurs du texte et d’arrière-plan

Définissez les propriétés [`TextColor`](xref:Xamarin.Forms.Label.TextColor) et [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de `Label` sur [`Color`](xref:Xamarin.Forms.Color) valeurs pour contrôler la couleur du texte et de l’arrière-plan.

Le `BackgroundColor` s’applique à l’arrière-plan de la zone entière occupée par le `Label`. Selon les propriétés `HorizontalOptions` et `VerticalOptions`, cette taille peut être considérablement plus grande que la zone requise pour afficher le texte. Vous pouvez utiliser la couleur pour expérimenter différentes valeurs de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`et `VerticalTextAlignment` pour voir comment elles affectent la taille et la position du `Label`, ainsi que la taille et la position du texte dans le `Label`.

## <a name="the-color-structure"></a>La structure de couleur

La structure [`Color`](xref:Xamarin.Forms.Color) vous permet de spécifier des couleurs en tant que valeurs rouge-vert-bleu (RVB) ou valeurs de teinte-saturation-luminosité (TSL), ou avec un nom de couleur. Un canal Alpha est également disponible pour indiquer la transparence.

Utilisez un constructeur `Color` pour spécifier :

- [ombre grise](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [valeur RVB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [valeur RVB avec transparence](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Les arguments sont des valeurs `double` comprises entre 0 et 1.

Vous pouvez également utiliser plusieurs méthodes statiques pour créer `Color` valeurs :

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) pour `double` valeurs RVB comprises entre 0 et 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) pour les valeurs RVB entières comprises entre 0 et 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) pour `double` valeurs RVB avec transparence
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) pour les valeurs RVB entières avec transparence
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) pour `double` valeurs TSL avec transparence
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) pour une valeur `uint` calculée comme (B + 256 \* (G + 256 \* (R + 256 \* a)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) pour un format `string` de chiffres hexadécimaux sous la forme « #AARRGGBB » ou « #RRGGBB » ou « #ARGB » ou « #RGB », où chaque lettre correspond à un chiffre hexadécimal pour les canaux alpha, rouge, vert et bleu. Cette méthode est principalement utilisée pour les conversions de couleurs XAML, comme indiqué dans le [chapitre 7, XAML et code](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Une fois créée, une valeur de `Color` est immuable. Les caractéristiques de la couleur peuvent être obtenus à partir des propriétés suivantes :

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Toutes les valeurs `double` sont comprises entre 0 et 1.

`Color` définit également les champs statiques publics 240 en lecture seule pour les couleurs courantes. Au moment de que l’ouvrage a été écrit, des couleurs courantes uniquement 17 étaient disponibles.

Un autre champ statique public en lecture seule définit une couleur avec tous les canaux de couleur a la valeur zéro :

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Plusieurs méthodes d’instance permettent la modification d’une couleur existante pour créer une nouvelle couleur :

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Enfin, deux propriétés statiques en lecture seule définissent la valeur de couleur spécial :

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), tous les canaux sont définis sur &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` est destiné à appliquer le modèle de couleurs de la plateforme, et a donc une signification différente dans différents contextes sur différentes plateformes. Par défaut, les jeux de couleurs de plateforme sont :

- iOS : texte sombre sur un arrière-plan clair
- Android : texte clair sur un arrière-plan sombre (dans le livre) ou texte foncé sur un arrière-plan clair (pour la conception de matériau via AppCompat dans la branche **principale** de l’exemple de référentiel de code)
- UWP : Texte de foncé sur un arrière-plan clair

La valeur `Color.Accent` entraîne une couleur spécifique à la plateforme (et parfois sélectionnable par l’utilisateur) qui est visible sur un arrière-plan sombre ou clair.

## <a name="changing-the-application-color-scheme"></a>Changer les couleurs de l’application

Les différentes plateformes ont un modèle de couleurs par défaut, comme indiqué dans la liste ci-dessus.

Quand vous ciblez Android, il est possible de basculer vers un modèle sombre en clair en spécifiant un thème clair dans le fichier Android. manifest. xml, ou en [ajoutant des conceptions AppCompat et Material](~/xamarin-forms/platform/android/appcompat-material-design.md).

Pour les plateformes Windows, le thème de couleur est normalement sélectionné par l’utilisateur, mais vous pouvez ajouter un attribut `RequestedTheme` défini sur `Light` ou `Dark` dans le fichier app. XAML de la plateforme. Par défaut, le fichier app. xaml du projet UWP contient un attribut `RequestedTheme` défini sur `Light`.

## <a name="font-sizes-and-attributes"></a>Attributs et les tailles de police

Affectez à la propriété [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) de `Label` une chaîne telle que « Times Roman » pour sélectionner une famille de polices. Toutefois, vous devez spécifier une famille de polices qui est pris en charge sur la plateforme spécifique, et les plateformes ne sont pas cohérents à cet égard.

Affectez à la propriété [`FontSize`](xref:Xamarin.Forms.Label.FontSize) de `Label` la valeur d’un `double` pour spécifier la hauteur approximative de la police. Pour plus d’informations sur le choix intelligent de la taille des polices, consultez le [Chapitre 5 traitant des tailles](chapter05.md).

Vous pouvez également obtenir un plusieurs prédéfini dépend de la plateforme de tailles de police. La méthode [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) statique et [la surcharge](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) retournent toutes deux une valeur de taille de police `double` appropriée à la plateforme en fonction des membres de l’énumération [`NamedSize`](xref:Xamarin.Forms.NamedSize) ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [`Micro`](xref:Xamarin.Forms.NamedSize.Micro) [,`Small`, ](xref:Xamarin.Forms.NamedSize.Small) [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)et [`Large`](xref:Xamarin.Forms.NamedSize.Large)). La valeur retournée par le membre `Medium` n’est pas nécessairement la même que `Default`. L’exemple [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) affiche du texte avec ces tailles nommées.

Affectez à la propriété [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) de `Label` la valeur d’un membre de ces [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) énumération, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold), [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)ou [`None`](xref:Xamarin.Forms.FontAttributes.None). Vous pouvez combiner les membres `Bold` et `Italic` à l' C# aide de l’opérateur or au niveau du bit.

## <a name="formatted-text"></a>Texte mis en forme

Dans tous les exemples jusqu’à présent, le texte entier affiché par le `Label` a été mis en forme uniformément. Pour faire varier la mise en forme dans une chaîne de texte, ne définissez pas la propriété `Text` de `Label`. Au lieu de cela, affectez à la propriété [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) un objet de type [`FormattedString`](xref:Xamarin.Forms.FormattedString).

`FormattedString` a une propriété [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) qui est une collection d’objets [`Span`](xref:Xamarin.Forms.Span) . Chaque objet `Span` possède ses propres propriétés [`Text`](xref:Xamarin.Forms.Span.Text), [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily), [`FontSize`](xref:Xamarin.Forms.Span.FontSize), [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes), [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)et [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) .

L’exemple [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) illustre l’utilisation de la propriété `FormattedText` pour une seule ligne de texte, tandis que [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustre la technique d’un paragraphe entier, comme illustré ici :

[![Capture d’écran triple du paragraphe mis en forme variable](images/ch03fg06-small.png "Texte de l’étiquette mise en forme de la variable")](images/ch03fg06-large.png#lightbox "Texte de l’étiquette mise en forme de la variable")

Le programme [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) utilise un `Label` unique et un objet `FormattedString` pour afficher toutes les tailles de police nommées pour chaque plateforme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 3 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemples du chapitre 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Exemples du F# chapitre 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Étiquette](~/xamarin-forms/user-interface/text/label.md)
- [Utilisation des couleurs](~/xamarin-forms/user-interface/colors.md)

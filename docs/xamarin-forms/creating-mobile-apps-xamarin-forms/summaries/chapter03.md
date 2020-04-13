---
title: Résumé du chapitre 3. Détails du texte
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 3. Détails du texte'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72980925"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Résumé du chapitre 3. Détails du texte

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Ce chapitre explore [`Label`](xref:Xamarin.Forms.Label) la vue plus en profondeur, y compris la couleur, les polices et le formatage.

## <a name="wrapping-paragraphs"></a>Paragraphes d’emballage

Lorsque [`Text`](xref:Xamarin.Forms.Label.Text) la `Label` propriété contient `Label` du texte long, l’enveloppe automatiquement à plusieurs lignes comme le démontre l’échantillon [**baskervilles.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) Vous pouvez intégrer des codes Unicode tels que 'u2014' pour l’em-dash, ou des caractères C’comme 'r' pour se briser vers une nouvelle ligne.

Lorsque [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) les [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriétés `Label` et `LayoutOptions.Fill`les propriétés d’un sont définies à , la taille globale de la `Label` est régie par l’espace que son conteneur met à disposition. On `Label` dit que le sont *limités*. La taille `Label` de la taille est la taille de son conteneur.

Lorsque `HorizontalOptions` les `VerticalOptions` propriétés et les `LayoutOptions.Fill`propriétés sont `Label` définies à des valeurs autres que , la taille de `Label`l’est régi par l’espace nécessaire pour rendre le texte, jusqu’à la taille que son conteneur met à disposition pour le . On `Label` dit que le n’est *pas mis à rude épreuve* et il détermine sa propre taille.

(Remarque : Les termes *limités* et *non tendus* peuvent être contre-intuitifs, car une vue sans contrainte est généralement plus petite qu’une vue limitée. En outre, ces termes ne sont pas utilisés uniformément dans les premiers chapitres du livre.)

Une vue telle `Label` qu’un point de vue peut être limitée dans une dimension et sans contrainte dans l’autre. Un `Label` texte d’enveloppement ne s’enveloppera que sur plusieurs lignes s’il est limité horizontalement.

Si `Label` un est limité, il peut occuper beaucoup plus d’espace que nécessaire pour le texte. Le texte peut être positionné dans `Label`la zone globale de la . Définissez [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) la propriété à [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) un membre[`Start`](xref:Xamarin.Forms.TextAlignment.Start) [`Center`](xref:Xamarin.Forms.TextAlignment.Center)de [`End`](xref:Xamarin.Forms.TextAlignment.Center)l’énumération ( , , ou ) pour contrôler l’alignement de toutes les lignes du paragraphe. La valeur `Start` par défaut est et aligne le texte.

Réglez la [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriété `TextAlignment` à un membre de l’énumération pour positionner le texte `Label`en haut, au centre ou en bas de la zone occupée par le .

Réglez la [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propriété [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) à un[`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap)membre [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap) [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap)de l’énumération ( , , , [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation), , ou [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) pour contrôler la façon dont les lignes multiples dans un paragraphe se cassent ou sont tronquées.

## <a name="text-and-background-colors"></a>Couleurs de texte et de fond

Définissez [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) les `Label` [`Color`](xref:Xamarin.Forms.Color) propriétés et les propriétés des valeurs pour contrôler la couleur du texte et de l’arrière-plan.

Le `BackgroundColor` s’applique à l’arrière-plan de toute la zone occupée par le `Label`. Selon les `HorizontalOptions` `VerticalOptions` propriétés et les propriétés, cette taille peut être considérablement plus grande que la zone requise pour afficher le texte. Vous pouvez utiliser la couleur `HorizontalOptions`pour `VerticalOptions` `HorizontalExeAlignment`expérimenter `VerticalTextAlignment` avec différentes valeurs de , , `Label`, et de voir comment `Label`ils affectent la taille et la position de la , et la taille et la position du texte dans le .

## <a name="the-color-structure"></a>La structure de couleur

La [`Color`](xref:Xamarin.Forms.Color) structure vous permet de spécifier les couleurs comme des valeurs Rouge-Vert-Bleu (RGB) ou des valeurs Hue-Saturation-Luminosity (HSL), ou avec un nom de couleur. Un canal Alpha est également disponible pour indiquer la transparence.

Utilisez `Color` un constructeur pour spécifier :

- une [nuance grise](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- une [valeur RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- une [valeur RGB avec transparence](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Les `double` arguments sont des valeurs allant de 0 à 1.

Vous pouvez également utiliser plusieurs `Color` méthodes statiques pour créer des valeurs :

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))pour `double` les valeurs RGB de 0 à 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))pour les valeurs RGB integer de 0 à 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))pour `double` les valeurs RGB avec transparence
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))pour les valeurs RGB integer avec transparence
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))pour `double` les valeurs HSL avec transparence
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))pour `uint` une valeur calculée comme (B 256 \* \* (G 256 \* (R - 256 A))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))pour `string` un format de chiffres hexadecimal sous la forme "#AARRGGBB" ou "#RRGGBB" ou "#ARGB" ou "#RGB", où chaque lettre correspond à un chiffre hexadecimal pour les canaux alpha, rouge, vert et bleu. Cette méthode est principalement utilisée pour les conversions de couleurs XAML comme discuté dans [le chapitre 7, XAML vs code](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Une fois `Color` créée, une valeur est immuable. Les caractéristiques de la couleur peuvent être obtenues à partir des propriétés suivantes:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Ce sont `double` toutes des valeurs allant de 0 à 1.

`Color`définit également 240 champs de lecture statiques publics uniquement pour des couleurs communes. Au moment de l’écriture du livre, seulement 17 couleurs communes étaient disponibles.

Un autre champ de lecture statique public définit une couleur avec tous les canaux de couleur réglés à zéro :

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Plusieurs méthodes d’instance permettent de modifier une couleur existante pour créer une nouvelle couleur :

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Enfin, deux propriétés statiques de lecture seulement définissent la valeur de couleur spéciale :

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), toutes les &ndash;chaînes réglées à 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default`est destiné à faire respecter le schéma de couleur de la plate-forme, et a par conséquent une signification différente dans différents contextes sur différentes plates-formes. Par défaut, les schémas de couleurs de la plate-forme sont les :

- iOS: Texte sombre sur un fond léger
- Android: Texte léger sur un fond sombre (dans le livre) ou texte sombre sur un fond lumineux (pour la conception de matériel via AppCompat dans la branche **principale** du référentiel de code d’échantillon)
- UWP: Texte sombre sur un fond léger

La `Color.Accent` valeur se traduit par une couleur spécifique à la plate-forme (et parfois sélectionnable par l’utilisateur) qui est visible sur un fond sombre ou léger.

## <a name="changing-the-application-color-scheme"></a>Modification du schéma de couleur d’application

Les différentes plates-formes ont un schéma de couleurs par défaut comme indiqué dans la liste ci-dessus.

Lors du ciblage Android, il est possible de passer à un schéma sombre sur la lumière en spécifiant un thème de lumière dans le fichier Android.Manifest.xml, ou en [ajoutant AppCompat et Material Design](~/xamarin-forms/platform/android/appcompat-material-design.md).

Pour les plates-formes Windows, le thème de couleur est `RequestedTheme` normalement sélectionné `Light` par `Dark` l’utilisateur, mais vous pouvez ajouter un ensemble d’attributs à l’un ou l’autre ou dans le fichier App.xaml de la plate-forme. Par défaut, le fichier App.xaml dans `RequestedTheme` le projet `Light`UWP contient un attribut défini à .

## <a name="font-sizes-and-attributes"></a>Tailles et attributs de police

Définissez [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) la `Label` propriété d’une chaîne telle que "Times Roman" pour sélectionner une famille de polices. Toutefois, vous devez spécifier une famille de polices qui est pris en charge sur la plate-forme particulière, et les plates-formes sont incohérentes à cet égard.

Définissez [`FontSize`](xref:Xamarin.Forms.Label.FontSize) la `Label` propriété `double` de a pour spécifier la hauteur approximative de la police. Voir [le chapitre 5, Traiter avec les tailles](chapter05.md), pour plus de détails sur le choix intelligent des tailles de police.

Vous pouvez également obtenir l’une des nombreuses tailles de police prédéfinies dépendantes de la plate-forme. La [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) méthode statique et [la surcharge retournent](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) à la fois une `double` valeur de [`Micro`](xref:Xamarin.Forms.NamedSize.Micro) [`Small`](xref:Xamarin.Forms.NamedSize.Small)taille de police appropriée à la plate-forme basée sur les membres de l’énumération [`NamedSize`](xref:Xamarin.Forms.NamedSize) ([`Default`](xref:Xamarin.Forms.NamedSize.Default), , , [`Medium`](xref:Xamarin.Forms.NamedSize.Medium), et [`Large`](xref:Xamarin.Forms.NamedSize.Large)). La valeur retournée `Medium` du membre n’est pas nécessairement la même que `Default`. [**L’échantillon NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) affiche du texte avec ces tailles nommées.

Définissez [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) la `Label` propriété d’un membre [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)de [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)ces [`None`](xref:Xamarin.Forms.FontAttributes.None)énumérations, , , ou . Vous pouvez `Bold` combiner `Italic` le et les membres avec l’opérateur DE C bitwise OU.

## <a name="formatted-text"></a>Texte mis en forme

Dans tous les exemples à ce jour, l’ensemble du texte affiché par le `Label` a été formaté uniformément. Pour varier le formatage dans une chaîne `Text` de `Label`texte, ne définissez pas la propriété de . Au lieu [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) de cela, définissez la propriété à un objet de type [`FormattedString`](xref:Xamarin.Forms.FormattedString).

`FormattedString`a [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) une propriété qui [`Span`](xref:Xamarin.Forms.Span) est une collection d’objets. Chaque `Span` objet a [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)son [`FontSize`](xref:Xamarin.Forms.Span.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)propre [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor), [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) , , , et les propriétés.

[**L’échantillon VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) démontre `FormattedText` l’utilisation de la propriété pour une seule ligne de texte, et [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) démontre la technique pour un paragraphe entier, comme indiqué ici:

[![Triple capture d’écran du paragraphe formaté variable](images/ch03fg06-small.png "Texte d’étiquette formaté variable")](images/ch03fg06-large.png#lightbox "Texte d’étiquette formaté variable")

Le programme [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) `Label` utilise `FormattedString` un seul et un objet pour afficher toutes les tailles de police nommées pour chaque plate-forme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 3 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Échantillons du chapitre 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Échantillons du chapitre 3 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Étiquette](~/xamarin-forms/user-interface/text/label.md)
- [Travailler avec Colors](~/xamarin-forms/user-interface/colors.md)

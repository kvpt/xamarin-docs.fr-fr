---
title: Résumé du chapitre 3. Détails du texte
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 3. Détails du texte'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6e53fdc689c15b3495af838f4788c1a50ad7a989
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940341"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Résumé du chapitre 3. Détails du texte

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Ce chapitre explore la [`Label`](xref:Xamarin.Forms.Label) vue plus en détail, y compris la couleur, les polices et la mise en forme.

## <a name="wrapping-paragraphs"></a>Encapsuler des paragraphes

Lorsque la [`Text`](xref:Xamarin.Forms.Label.Text) propriété de `Label` contient du texte long, `Label` l’encapsule automatiquement sur plusieurs lignes, comme le montre l’exemple [**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . Vous pouvez incorporer des codes Unicode tels que « \u2014 » pour le tiret cadratin, ou des caractères C# comme « \r » pour s’arrêter sur une nouvelle ligne.

Lorsque les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriétés et d’un `Label` ont la valeur `LayoutOptions.Fill` , la taille globale du `Label` est régie par l’espace que son conteneur met à disposition. `Label`Est dit qu’il est *imposé*. La taille de `Label` est la taille de son conteneur.

Lorsque les `HorizontalOptions` `VerticalOptions` Propriétés et ont des valeurs autres que `LayoutOptions.Fill` , la taille du `Label` est régie par l’espace requis pour restituer le texte, jusqu’à la taille que son conteneur met à disposition pour le `Label` . `Label`Est dit comme non *contrainte* et détermine sa propre taille.

(Remarque : les termes *contrainte* et sans *contrainte* peuvent être exintuitifs, car une vue non contrainte est généralement plus petite qu’une vue contrainte. En outre, ces termes ne sont pas utilisés de manière cohérente dans les premiers chapitres du livre.)

Un affichage tel qu’un `Label` peut être contraint dans une dimension et sans contrainte dans l’autre. Un `Label` renvoie le texte sur plusieurs lignes uniquement s’il est restreint horizontalement.

Si un `Label` est restreint, il peut occuper beaucoup plus d’espace que nécessaire pour le texte. Le texte peut être positionné à l’intérieur de la zone globale du `Label` . Affectez [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) à la propriété un membre de l' [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) énumération ( [`Start`](xref:Xamarin.Forms.TextAlignment.Start) , [`Center`](xref:Xamarin.Forms.TextAlignment.Center) ou [`End`](xref:Xamarin.Forms.TextAlignment.Center) ) pour contrôler l’alignement de toutes les lignes du paragraphe. La valeur par défaut est `Start` et aligne à gauche le texte.

Affectez [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) à la propriété un membre de l' `TextAlignment` énumération pour positionner le texte en haut, au centre ou en bas de la zone occupée par `Label` .

Affectez [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) à la propriété un membre de l' [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) énumération ( [`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap) ,,,, [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap) [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation) [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation) ou [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation) ) pour contrôler la façon dont les lignes multiples dans un saut de paragraphe ou sont tronquées.

## <a name="text-and-background-colors"></a>Couleurs du texte et de l’arrière-plan

Affectez [`TextColor`](xref:Xamarin.Forms.Label.TextColor) aux [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) Propriétés et de la valeur `Label` [`Color`](xref:Xamarin.Forms.Color) pour contrôler la couleur du texte et de l’arrière-plan.

Le `BackgroundColor` s’applique à l’arrière-plan de la zone entière occupée par `Label` . Selon les `HorizontalOptions` `VerticalOptions` Propriétés et, cette taille peut être beaucoup plus grande que la zone requise pour afficher le texte. Vous pouvez utiliser la couleur pour faire des essais avec différentes valeurs de `HorizontalOptions` , `VerticalOptions` , `HorizontalExeAlignment` et `VerticalTextAlignment` pour voir comment elles affectent la taille et la position du `Label` , ainsi que la taille et la position du texte dans `Label` .

## <a name="the-color-structure"></a>Structure de la couleur

La [`Color`](xref:Xamarin.Forms.Color) structure vous permet de spécifier des couleurs en tant que valeurs rouge-vert-bleu (RVB) ou valeurs de teinte-saturation-luminosité (TSL), ou avec un nom de couleur. Un canal alpha est également disponible pour indiquer la transparence.

Utilisez un `Color` constructeur pour spécifier :

- [ombre grise](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [valeur RVB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [valeur RVB avec transparence](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Les arguments sont des `double` valeurs comprises entre 0 et 1.

Vous pouvez également utiliser plusieurs méthodes statiques pour créer des `Color` valeurs :

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) pour les `double` valeurs RVB comprises entre 0 et 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) pour les valeurs RVB entières comprises entre 0 et 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) pour les `double` valeurs RVB avec transparence
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) pour les valeurs RVB entières avec transparence
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) pour les `double` valeurs TSL avec transparence
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) pour une `uint` valeur calculée comme (B + 256 \* (G + 256 \* (R + 256 \* a)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) pour un `string` format de chiffres hexadécimaux sous la forme « #AARRGGBB » ou « #RRGGBB » ou « #ARGB » ou « #RGB », où chaque lettre correspond à un chiffre hexadécimal pour les canaux alpha, rouge, vert et bleu. Cette méthode est principalement utilisée pour les conversions de couleurs XAML, comme indiqué dans le [chapitre 7, XAML et code](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Une fois créée, une `Color` valeur est immuable. Les caractéristiques de la couleur peuvent être obtenues à partir des propriétés suivantes :

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Il s’agit `double` de toutes les valeurs comprises entre 0 et 1.

`Color` définit également les champs statiques en lecture seule 240 publics pour les couleurs courantes. Au moment de la rédaction du livre, seules 17 couleurs courantes étaient disponibles.

Un autre champ statique public en lecture seule définit une couleur dont tous les canaux de couleur sont définis sur zéro :

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Plusieurs méthodes d’instance autorisent la modification d’une couleur existante pour créer une nouvelle couleur :

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Enfin, deux propriétés statiques en lecture seule définissent une valeur de couleur spéciale :

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), tous les canaux définis sur &ndash; 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` est destiné à appliquer le modèle de couleurs de la plateforme, et par conséquent a une signification différente dans différents contextes sur différentes plateformes. Par défaut, les modèles de couleurs de la plateforme sont les suivants :

- iOS : texte foncé sur un arrière-plan clair
- Android : texte clair sur un arrière-plan sombre (dans le livre) ou texte foncé sur un arrière-plan clair (pour la conception de matériau via AppCompat dans la branche **principale** de l’exemple de référentiel de code)
- UWP : texte foncé sur un arrière-plan clair

La `Color.Accent` valeur entraîne une couleur spécifique à la plateforme (et parfois sélectionnable par l’utilisateur) qui est visible sur un arrière-plan sombre ou clair.

## <a name="changing-the-application-color-scheme"></a>Modification du modèle de couleurs de l’application

Les différentes plateformes ont un modèle de couleurs par défaut, comme indiqué dans la liste ci-dessus.

Quand vous ciblez Android, il est possible de basculer vers un modèle sombre en clair en spécifiant un thème clair dans le fichier Android.Manifest.xml.

Pour les plateformes Windows, le thème de couleur est normalement sélectionné par l’utilisateur, mais vous pouvez ajouter un `RequestedTheme` ensemble d’attributs à `Light` ou `Dark` dans le fichier app. XAML de la plateforme. Par défaut, le fichier app. xaml du projet UWP contient un attribut dont la `RequestedTheme` valeur est `Light` .

## <a name="font-sizes-and-attributes"></a>Tailles et attributs des polices

Affectez à la propriété de la valeur d' [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) `Label` une chaîne telle que « Times Roman » pour sélectionner une famille de polices. Toutefois, vous devez spécifier une famille de polices qui est prise en charge sur la plateforme particulière et les plateformes ne sont pas cohérentes à cet égard.

Affectez [`FontSize`](xref:Xamarin.Forms.Label.FontSize) à la propriété de la valeur `Label` `double` pour spécifier la hauteur approximative de la police. Pour plus d’informations sur le choix intelligent de la taille des polices, consultez le [Chapitre 5 traitant des tailles](chapter05.md).

Vous pouvez également obtenir l’une des nombreuses tailles de police prédéfinies dépendantes de la plateforme. Le [] statique [ `Device.GetNamedSize` ] (XREF : Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . Méthode NamedSize, System. type)) et [surcharge] (XREF : Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . NamedSize, Xamarin.Forms . ) Retournent toutes deux une `double` valeur de taille de police appropriée à la plateforme en fonction des membres de l' [`NamedSize`](xref:Xamarin.Forms.NamedSize)  énumération ( [`Default`](xref:Xamarin.Forms.NamedSize.Default) ,,, [`Micro`](xref:Xamarin.Forms.NamedSize.Micro) [`Small`](xref:Xamarin.Forms.NamedSize.Small) [`Medium`](xref:Xamarin.Forms.NamedSize.Medium) et [`Large`](xref:Xamarin.Forms.NamedSize.Large) ). La valeur retournée par le `Medium` membre n’est pas nécessairement la même que `Default` . L’exemple [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) affiche du texte avec ces tailles nommées.

Affectez [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) à la propriété de la valeur `Label` un membre de l' [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) énumération,, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)  [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic) ou [`None`](xref:Xamarin.Forms.FontAttributes.None) . Vous pouvez combiner les `Bold` `Italic` membres et avec l’opérateur de bits or C#.

## <a name="formatted-text"></a>Texte mis en forme

Dans tous les exemples jusqu’à présent, le texte entier affiché par le `Label` a été mis en forme uniformément. Pour faire varier la mise en forme dans une chaîne de texte, ne définissez pas la `Text` propriété de `Label` . Au lieu de cela, définissez la [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propriété sur un objet de type [`FormattedString`](xref:Xamarin.Forms.FormattedString) .

`FormattedString` a une [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) propriété qui est une collection d' [`Span`](xref:Xamarin.Forms.Span) objets. Chaque `Span` objet possède ses propres propriétés,,,, [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) [`FontSize`](xref:Xamarin.Forms.Span.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) et [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) .

L’exemple [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) illustre l’utilisation `FormattedText` de la propriété pour une seule ligne de texte, tandis que [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustre la technique d’un paragraphe entier, comme illustré ici :

[![Capture d’écran triple du paragraphe mis en forme variable](images/ch03fg06-small.png "Texte de l’étiquette mise en forme de la variable")](images/ch03fg06-large.png#lightbox "Texte de l’étiquette mise en forme de la variable")

Le programme [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) utilise un unique `Label` et un `FormattedString` objet pour afficher toutes les tailles de police nommées pour chaque plateforme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 3 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemples du chapitre 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Exemples de chapitre 3 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Étiquette](~/xamarin-forms/user-interface/text/label.md)
- [Utilisation des couleurs](~/xamarin-forms/user-interface/colors.md)

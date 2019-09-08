---
title: Résumé du chapitre 3. Détails du texte
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 3. Détails du texte'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e37175240825c0fed350589649469c99f1bbf69a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771222"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Résumé du chapitre 3. Détails du texte

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Ce chapitre explore le [ `Label` ](xref:Xamarin.Forms.Label) vue plus en détail, y compris les couleurs, polices et la mise en forme.

## <a name="wrapping-paragraphs"></a>Habillage des paragraphes

Lorsque le [ `Text` ](xref:Xamarin.Forms.Label.Text) propriété du `Label` contient du texte long, `Label` encapsule automatiquement à plusieurs lignes comme illustré par la [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) exemple. Vous pouvez incorporer des codes Unicode tels que « \u2014 » pour le cadratin, ou C# caractères comme « \r » pour arrêter l’exécution vers une nouvelle ligne.

Lorsque le [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) et [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriétés d’un `Label` sont définies sur `LayoutOptions.Fill`, le la taille globale de la `Label` est régie par l’espace qui son conteneur met à disposition. Le `Label` est dite *contraint*. La taille de la `Label` est la taille de son conteneur.

Lorsque le `HorizontalOptions` et `VerticalOptions` propriétés sont définies sur les valeurs autres que `LayoutOptions.Fill`, la taille de la `Label` est régie par l’espace nécessaire pour restituer le texte, jusqu'à la taille de son conteneur rend disponible pour le `Label`. Le `Label` est dite *sans contrainte* et détermine sa taille.

(Remarque : Les termes *contrainte* et sans *contrainte* peuvent être exintuitifs, car une vue non contrainte est généralement plus petite qu’une vue contrainte. En outre, ces termes ne sont pas utilisés régulièrement dans les premiers chapitres du livre.)

Une vue comme un `Label` peut être limité dans une dimension et sans contraintes dans l’autre. Un `Label` sera uniquement habiller le texte sur plusieurs lignes si elle est contrainte horizontalement.

Si un `Label` est contraint, il peut-être occuper beaucoup plus d’espace requis pour le texte. Le texte peut être placé dans la zone globale de la `Label`. Définir le [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) propriété à un membre de la [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) énumération ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center), ou [ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) pour contrôler l’alignement de toutes les lignes du paragraphe. La valeur par défaut est `Start` et aligne le texte à gauche.

Définir le [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriété à un membre de la `TextAlignment` énumération pour positionner le texte en haut, center ou en bas de la zone occupée par le `Label`.

Définir le [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriété à un membre de la [ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode) énumération ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation), ou [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) pour contrôle comment le plusieurs lignes dans un saut de paragraphe ou sont tronqués.

## <a name="text-and-background-colors"></a>Couleurs du texte et d’arrière-plan

Définir le [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) et [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) propriétés de `Label` à [ `Color` ](xref:Xamarin.Forms.Color) valeurs pour contrôler la couleur du texte et d’arrière-plan.

Le `BackgroundColor` s’applique à l’arrière-plan de toute la zone occupée par le `Label`. Selon le `HorizontalOptions` et `VerticalOptions` propriétés, que la taille peut être beaucoup plus volumineuses que la zone requise pour afficher le texte. Vous pouvez utiliser des couleurs pour faire des essais avec différentes valeurs de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, et `VerticalTextAlignment` pour voir comment elles affectent la taille et la position de la `Label`et la taille et la position du texte dans le `Label`.

## <a name="the-color-structure"></a>La structure de couleur

Le [ `Color` ](xref:Xamarin.Forms.Color) structure vous permet de spécifier les couleurs en tant que valeurs de rouge-vert-bleu (RVB) ou des valeurs de Teinte-Saturation-Luminosité (TSL), ou avec un nom de couleur. Un canal Alpha est également disponible pour indiquer la transparence.

Utilisez un `Color` constructeur pour spécifier :

- un [nuance de gris](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- un [valeur RVB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- un [valeur RVB avec transparence](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Les arguments sont `double` valeurs comprises entre 0 et 1.

Vous pouvez également utiliser plusieurs méthodes statiques pour créer `Color` valeurs :

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) pour `double` valeurs RVB à partir de 0 à 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) pour les valeurs RVB de nombre entier compris entre 0 et 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) pour `double` valeurs RVB avec transparence
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) pour les valeurs RVB d’entières avec la transparence
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) pour `double` valeurs TSL avec transparence
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) pour un `uint` valeur calculée en tant que (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) pour un `string` format de chiffres hexadécimaux sous la forme « #AARRGGBB » ou « #RRGGBB » ou « #ARGB » ou « #RGB », où chaque lettre correspond à un chiffre hexadécimal pour la valeur alpha, rouge, verts et bleus de canaux. Cette méthode est principale utilisé pour les conversions de couleur XAML, comme indiqué dans [chapitre 7, XAML et code](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Une fois créé, un `Color` valeur est immuable. Les caractéristiques de la couleur peuvent être obtenus à partir des propriétés suivantes :

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Il s’agit de tous les `double` valeurs comprises entre 0 et 1.

`Color` définit également 240 champs statiques publics en lecture seule des couleurs courantes. Au moment de que l’ouvrage a été écrit, des couleurs courantes uniquement 17 étaient disponibles.

Un autre champ statique public en lecture seule définit une couleur avec tous les canaux de couleur a la valeur zéro :

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Plusieurs méthodes d’instance permettent la modification d’une couleur existante pour créer une nouvelle couleur :

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Enfin, deux propriétés statiques en lecture seule définissent la valeur de couleur spécial :

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), la valeur tous les canaux &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` Permet d’appliquer le modèle de couleurs de la plateforme, et par conséquent a une signification différente dans différents contextes sur différentes plateformes. Par défaut, les jeux de couleurs de plateforme sont :

- Libéré Texte foncé sur un arrière-plan clair
- Tablette Texte clair sur un arrière-plan sombre (dans le livre) ou texte foncé sur un arrière-plan clair (pour la conception de matériau via AppCompat dans la branche **maître** de l’exemple de référentiel de code)
- UWP Texte foncé sur un arrière-plan clair

Le `Color.Accent` valeur les résultats dans une couleur spécifique à la plateforme (et parfois sélectionnables par l’utilisateur) qui est visible sur un arrière-plan sombre ou clair.

## <a name="changing-the-application-color-scheme"></a>Changer les couleurs de l’application

Les différentes plateformes ont un modèle de couleurs par défaut, comme indiqué dans la liste ci-dessus.

Lorsque vous ciblez Android, il est possible de basculer vers un schéma sombre sur clair en spécifiant un thème clair dans le fichier Android.Manifest.xml ou en [AppCompat Ajout et Material Design](~/xamarin-forms/platform/android/appcompat-material-design.md).

Pour les plateformes Windows, le thème de couleur est généralement sélectionné par l’utilisateur, mais vous pouvez ajouter un `RequestedTheme` attribut la valeur `Light` ou `Dark` dans le fichier App.xaml de la plateforme. Par défaut, le fichier App.xaml dans le projet UWP contient un `RequestedTheme` attribut la valeur `Light`.

## <a name="font-sizes-and-attributes"></a>Attributs et les tailles de police

Définir le [ `FontFamily` ](xref:Xamarin.Forms.Label.FontFamily) propriété du `Label` à une chaîne telle que « Times Roman » pour sélectionner une famille de polices. Toutefois, vous devez spécifier une famille de polices qui est pris en charge sur la plateforme spécifique, et les plateformes ne sont pas cohérents à cet égard.

Définir le [ `FontSize` ](xref:Xamarin.Forms.Label.FontSize) propriété du `Label` à un `double` pour spécifier la hauteur approximative de la police. Consultez [chapitre 5, gestion des tailles](chapter05.md), pour plus d’informations sur le choix intelligemment les tailles de police.

Vous pouvez également obtenir un plusieurs prédéfini dépend de la plateforme de tailles de police. Statiques [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) (méthode) et [surcharger](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) retournent un `double` valeur de taille de police appropriée à la plateforme basée sur les membres de la [ `NamedSize` ](xref:Xamarin.Forms.NamedSize)énumération ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro), [ `Small` ](xref:Xamarin.Forms.NamedSize.Small), [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium),  et [ `Large` ](xref:Xamarin.Forms.NamedSize.Large)). La valeur retournée par la `Medium` membre n’est pas nécessairement identique à `Default`. Le [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) exemple affiche le texte avec ces tailles nommées.

Définir le [ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes) propriété du `Label` à un membre de ces [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes) énumération, [ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold), [ `Italic` ](xref:Xamarin.Forms.FontAttributes.Italic), ou [ `None` ](xref:Xamarin.Forms.FontAttributes.None). Vous pouvez combiner la `Bold` et `Italic` membres avec le langage C# opérateur de bits OR.

## <a name="formatted-text"></a>Texte mis en forme

Dans tous les exemples jusqu’ici, l’intégralité du texte affiché par le `Label` a été formaté de manière uniforme. Pour faire varier la mise en forme dans une chaîne de texte, ne définissez pas la `Text` propriété du `Label`. Au lieu de cela, définissez la [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propriété à un objet de type [ `FormattedString` ](xref:Xamarin.Forms.FormattedString).

`FormattedString` a un [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propriété qui est une collection de [ `Span` ](xref:Xamarin.Forms.Span) objets. Chaque `Span` objet possède son propre [ `Text` ](xref:Xamarin.Forms.Span.Text), [ `FontFamily` ](xref:Xamarin.Forms.Span.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Span.FontSize), [ `FontAttributes` ](xref:Xamarin.Forms.Span.FontAttributes), [ `ForegroundColor` ](xref:Xamarin.Forms.Span.ForegroundColor), et [ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor) propriétés.

Le [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) exemple montre comment utiliser le `FormattedText` propriété pour une seule ligne de texte, et [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) illustre cette technique pour tout un paragraphe, comme illustré ici :

[![Capture d’écran triple de variable mise en forme de paragraphe](images/ch03fg06-small.png "Variable au format texte d’étiquette")](images/ch03fg06-large.png#lightbox "Variable au format texte d’étiquette")

Le [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programme utilise un seul `Label` et un `FormattedString` objet pour afficher toutes les tailles de police nommé pour chaque plateforme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 3 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemples de chapitre 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Chapitre 3 F# exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [Utilisation des couleurs](~/xamarin-forms/user-interface/colors.md)

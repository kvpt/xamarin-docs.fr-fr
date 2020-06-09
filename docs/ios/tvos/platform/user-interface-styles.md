---
title: Styles de l’interface utilisateur tvOS dans Xamarin
description: Cet article aborde les thèmes de l’interface utilisateur Light et Dark que Apple a ajoutés à tvOS 10 et comment les implémenter dans une application Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 1d64a212dae055d6a7a5ff1005b25dc48a10d52e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566199"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>Styles de l’interface utilisateur tvOS dans Xamarin

_Cet article aborde les thèmes de l’interface utilisateur Light et Dark que Apple a ajoutés à tvOS 10 et comment les implémenter dans une application Xamarin. tvOS._

tvOS 10 prend désormais en charge à la fois un thème d’interface utilisateur sombre et clair, sur lequel tous les contrôles de UIKit de build s’adapteront automatiquement, en fonction des préférences de l’utilisateur. En outre, le développeur peut ajuster manuellement les éléments d’interface utilisateur en fonction du thème que l’utilisateur a sélectionné et peut remplacer un thème donné.

<a name="About-the-New-User-Interface-Styles"></a>

## <a name="about-the-new-user-interface-styles"></a>À propos des nouveaux styles d’interface utilisateur

Comme indiqué ci-dessus, tvOS 10 prend désormais en charge à la fois un thème d’interface utilisateur sombre et clair, sur lequel tous les contrôles de UIKit de build s’adapteront automatiquement, en fonction des préférences de l’utilisateur.

L’utilisateur peut basculer sur ce thème en accédant à **paramètres**  >  **général**  >  **Appearance** et en basculant entre **clair** et **sombre**:

[![](user-interface-styles-images/theme01.png "The Settings app")](user-interface-styles-images/theme01.png#lightbox)

Lorsque le thème **sombre** est sélectionné, tous les éléments de l’interface utilisateur basculent vers le texte clair sur un arrière-plan foncé :

[![](user-interface-styles-images/theme02.png "The Dark theme")](user-interface-styles-images/theme02.png#lightbox)

L’utilisateur a la possibilité de basculer le thème à tout moment et peut le faire en fonction de l’activité en cours, où se trouve la TV Apple ou l’heure de la journée.

Le thème de l’interface utilisateur légère est le thème par défaut, et toutes les applications tvOS existantes continuent d’utiliser le thème clair, quelles que soient les préférences de l’utilisateur, sauf si elles sont modifiées pour tvOS 10 afin de tirer parti du thème sombre. Une application tvOS 10 a également la possibilité de remplacer le thème actuel et d’utiliser toujours le thème clair ou sombre pour une partie ou la totalité de l’interface utilisateur.

<a name="Adopting-the-Light-and-Dark-Themes"></a>

## <a name="adopting-the-light-and-dark-themes"></a>Adoption des thèmes clairs et foncés

Pour prendre en charge cette fonctionnalité, Apple a ajouté une nouvelle API à la `UITraitCollection` classe et une application tvOS doit accepter la prise en charge de l’apparence sombre (via un paramètre dans son `Info.plist` fichier).

Pour accepter la prise en charge des thèmes clairs et sombres, procédez comme suit :

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier `Info.plist` pour l’ouvrir et le modifier.
2. Sélectionnez la vue **source** (en bas de l’éditeur).
3. Ajoutez une nouvelle clé et appelez-la `UIUserInterfaceStyle` :

    [![](user-interface-styles-images/theme03.png "The UIUserInterfaceStyle key")](user-interface-styles-images/theme03.png#lightbox)
4. Laissez le type défini sur `String` et entrez la valeur `Automatic` suivante :

    [![](user-interface-styles-images/theme04.png "Enter Automatic")](user-interface-styles-images/theme04.png#lightbox)
5. Enregistrez les modifications dans le fichier.

Il existe trois valeurs possibles pour la `UIUserInterfaceStyle` clé :

- **Light** : force l’interface utilisateur de l’application tvOS à toujours utiliser le thème clair.
- **Dark** : force l’interface utilisateur de l’application tvOS à toujours utiliser le thème sombre.
- **Automatique** : bascule entre le thème clair et le thème sombre en fonction des préférences de l’utilisateur dans paramètres. Il s’agit du paramètre par défaut.

<a name="UIKit-Theme-Support"></a>

### <a name="uikit-theme-support"></a>Prise en charge du thème UIKit

Si une application tvOS utilise des contrôles intégrés standard, `UIView` ils répondront automatiquement au thème de l’interface utilisateur sans aucune intervention du développeur.

En outre, `UILabel` et `UITextView` modifie automatiquement leur couleur en fonction du thème de l’interface utilisateur sélectionné :

- Le texte sera noir dans le thème clair.
- Le texte sera blanc dans le thème sombre.

Si le développeur modifie la couleur de texte manuellement (dans le Storyboard ou le code), il est responsable de la gestion des modifications de couleur en fonction du thème de l’interface utilisateur.

<a name="New-Blur-Effects"></a>

### <a name="new-blur-effects"></a>Nouveaux effets de flou

Pour la prise en charge des thèmes clairs et foncés dans une application tvOS 10, Apple a ajouté deux nouveaux effets de flou. Ces nouveaux effets ajustent automatiquement le flou en fonction du thème de l’interface utilisateur que l’utilisateur a sélectionné comme suit :

- `UIBlurEffectStyleRegular`-Utilise un flou clair dans le thème clair et un flou foncé dans le thème sombre.
- `UIBlurEffectStyleProminent`-Utilise un flou extra clair dans le thème clair et un flou extra-sombre dans le thème sombre.

<a name="Working-with-Trait-Collections"></a>

## <a name="working-with-trait-collections"></a>Utilisation des collections de traits

La nouvelle `UserInterfaceStyle` propriété de la `UITraitCollection` classe peut être utilisée pour obtenir le thème de l’interface utilisateur actuellement sélectionné et sera une `UIUserInterfaceStyle` énumération de l’une des valeurs suivantes :

- **Light** : le thème de l’interface utilisateur légère est sélectionné.
- **Dark** -le thème de l’interface utilisateur sombre est sélectionné.
- Non **spécifié** : la vue n’a pas encore été affichée à l’écran, donc le thème de l’interface utilisateur actuel est inconnu.

En outre, les collections de traits présentent les fonctionnalités suivantes dans tvOS 10 :

- Le proxy d’apparence peut être personnalisé en fonction du `UserInterfaceStyle` d’un donné `UITraitCollection` pour modifier des éléments tels que des images ou des couleurs d’élément en fonction du thème.
- Une application tvOS peut gérer les modifications de la collection de traits en substituant la `TraitCollectionDidChange` méthode d’une `UIView` `UIViewController` classe ou.

> [!IMPORTANT]
> La version préliminaire préliminaire de Xamarin. tvOS pour tvOS 10 n’est pas encore prise en charge `UIUserInterfaceStyle` pour le `UITraitCollection` moment. Une prise en charge complète sera ajoutée dans une version ultérieure.

<a name="Customizing-Appearance-Based-on-Theme"></a>

### <a name="customizing-appearance-based-on-theme"></a>Personnalisation de l’apparence en fonction du thème

Pour les éléments d’interface utilisateur qui prennent en charge le proxy d’apparence, leur apparence peut être ajustée en fonction du thème de l’interface utilisateur de leur collection de caractéristiques. Ainsi, pour un élément d’interface utilisateur donné, le développeur peut spécifier une couleur pour le thème clair et une autre couleur pour le thème sombre.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> Malheureusement, la version préliminaire de Xamarin. tvOS pour tvOS 10 n’est pas entièrement prise en charge `UIUserInterfaceStyle` pour `UITraitCollection` . ce type de personnalisation n’est donc pas encore disponible. Une prise en charge complète sera ajoutée dans une version ultérieure.

<a name="Responding-to-Theme-Changes-Directly"></a>

### <a name="responding-to-theme-changes-directly"></a>Réponse directe aux modifications de thème

Dans, le développeur a besoin d’un contrôle approfondi sur l’apparence d’un élément d’interface utilisateur en fonction du thème de l’interface utilisateur sélectionné, il peut substituer la `TraitCollectionDidChange` méthode d’une `UIView` `UIViewController` classe ou.

Par exemple :

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);

    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly"></a>

### <a name="overriding-a-trait-collection"></a>Substitution d’une collection de traits

En fonction de la conception d’une application tvOS, il peut arriver que le développeur doive remplacer la collection de caractéristiques d’un élément d’interface utilisateur donné et qu’il utilise toujours un thème spécifique de l’interface utilisateur.

Cette opération peut être effectuée à l’aide `SetOverrideTraitCollection` de la méthode sur la `UIViewController` classe. Par exemple :

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Pour plus d’informations, consultez les sections [caractéristiques](~/ios/user-interface/storyboards/unified-storyboards.md) et [remplacement des caractéristiques](~/ios/user-interface/storyboards/unified-storyboards.md) de notre introduction à la documentation [sur les storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .

<a name="Trait-Collections-and-Storyboards"></a>

### <a name="trait-collections-and-storyboards"></a>Collections de caractéristiques et storyboards

Dans tvOS 10, le Storyboard d’une application peut être configuré pour répondre aux collections de traits et de nombreux éléments d’interface utilisateur peuvent être pris en charge par les thèmes clairs et foncés. La version préliminaire actuelle de Xamarin. tvOS pour tvOS 10 ne prend pas encore en charge cette fonctionnalité dans le concepteur d’interface. par conséquent, la table de montage séquentiel doit être modifiée dans le Interface Builder de Xcode comme solution de contournement.

Pour activer la prise en charge de la collection de traits, procédez comme suit :

1. Cliquez avec le bouton droit sur le fichier de table de montage séquentiel dans le **Explorateur de solutions** , puis sélectionnez **Ouvrir avec**  >  **Xcode Interface Builder**:

    [![](user-interface-styles-images/theme05.png "Open With Xcode Interface Builder")](user-interface-styles-images/theme05.png#lightbox)
2. Pour activer la prise en charge de la collection de traits, basculez vers l' **inspecteur de fichier** et cochez la propriété utiliser des **variations de trait** dans la section **Interface Builder document** :

    [![](user-interface-styles-images/theme06.png "Enable Trait Collection support")](user-interface-styles-images/theme06.png#lightbox)
3. Confirmez la modification pour utiliser les variations de trait :

    [![](user-interface-styles-images/theme07.png "The use Trait Variations alert")](user-interface-styles-images/theme07.png#lightbox)
4. Enregistrez les modifications apportées au fichier de table de montage séquentiel.

Apple a ajouté les fonctionnalités suivantes lors de la modification des storyboards tvOS dans Interface Builder :

- Le développeur peut spécifier différentes variantes d’éléments d’interface utilisateur en fonction du thème de l’interface utilisateur dans l' **inspecteur d’attribut**:

  - À présent, plusieurs propriétés sont **+** à côté d’elles, sur lesquelles vous pouvez cliquer pour ajouter une version spécifique au thème de l’interface utilisateur :

    [![](user-interface-styles-images/theme08.png "Add a UI theme specific version")](user-interface-styles-images/theme08.png#lightbox)

  - Le développeur peut spécifier une nouvelle propriété ou cliquer sur le bouton **x** pour le supprimer :

    [![](user-interface-styles-images/theme09.png "Specify a new property or click the x button to remove it")](user-interface-styles-images/theme09.png#lightbox)
- Le développeur peut afficher un aperçu de la conception de l’interface utilisateur dans le thème clair ou sombre à partir d’Interface Builder :

  - Le bas de la Aire de conception permet au développeur de changer le thème de l’interface utilisateur actuel :

    [![](user-interface-styles-images/theme10.png "The bottom of the Design Surface")](user-interface-styles-images/theme10.png#lightbox)

  - Le nouveau thème s’affiche dans Interface Builder et les ajustements spécifiques à la collection de traits s’affichent :

    [![](user-interface-styles-images/theme11.png "The theme displayed in Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

En outre, le simulateur tvOS dispose désormais d’un raccourci clavier pour permettre au développeur de basculer rapidement entre les thèmes clairs et foncés lors du débogage d’une application tvOS. Utilisez la séquence de raccourci de **commande-Shift-D** pour basculer entre clair et sombre.

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Cet article a abordé les thèmes de l’interface utilisateur Light et Dark qu’Apple a ajoutés à tvOS 10 et comment les implémenter dans une application Xamarin. tvOS.

## <a name="related-links"></a>Liens connexes

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Nouveautés de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

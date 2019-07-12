---
title: Contraintes de mise en page par programmation dans Xamarin.iOS
description: Ce guide présente travailler avec iOS des contraintes de disposition automatique dans C# code au lieu de les créer dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 089ada051b6780a15acfcdd7f9e32ddda1384d05
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832050"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Contraintes de mise en page par programmation dans Xamarin.iOS

_Ce guide présente travailler avec iOS des contraintes de disposition automatique dans C# code au lieu de les créer dans le concepteur iOS._

Disposition automatique (également appelée « disposition adaptatif ») est une approche de conception réactive. Contrairement au système de disposition transitoire, où les emplacement de chaque élément sont codé en dur à un point sur l’écran, la disposition automatique est sur *relations* -les positions d’éléments par rapport aux autres éléments sur l’aire de conception. Au cœur de la disposition automatique est l’idée des contraintes ou des règles qui définissent le placement d’un élément ou un ensemble d’éléments dans le contexte d’autres éléments à l’écran. Étant donné que les éléments ne sont pas liés à une position particulière sur l’écran, les contraintes permettent de créer une disposition adaptative qui vous semble correcte sur différentes tailles d’écran et les orientations de l’appareil.

En général, lorsque vous travaillez avec une disposition automatique dans iOS, vous allez utiliser le concepteur iOS pour placer graphiquement des contraintes de mise en page sur vos éléments d’interface utilisateur. Toutefois, il peut arriver que vous deviez créer et appliquer des contraintes dans C# code. Par exemple, quand vous utilisez dynamiquement créés les éléments d’interface utilisateur ajoutés à un `UIView`.

Ce guide vous explique comment créer et utiliser des contraintes à l’aide de C# code au lieu de les créer graphiquement dans le concepteur iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Création de contraintes par programmation

Comme indiqué ci-dessus, en général, vous travaillerez avec des contraintes de mise en page automatique dans le concepteur iOS. Dans les cas vous avez à créer vos contraintes par programmation, vous avez trois options au choix :

* [Disposition ancres](#Layout-Anchors) -cette API permet d’accéder aux propriétés d’ancrage (tel que `TopAnchor`, `BottomAnchor` ou `HeightAnchor`) des éléments d’interface utilisateur est contraintes.
* [Contraintes de disposition](#Layout-Constraints) -vous pouvez créer des contraintes directement à l’aide de la `NSLayoutConstraint` classe.
* [Langage de mise en forme Visual](#Visual-Format-Language) -fournit une illustration ASCII comme méthode pour définir vos contraintes.

Les sections suivantes passe en revue chaque option en détail.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Ancres de disposition

À l’aide de la `NSLayoutAnchor` (classe), vous avez une interface fluent permettant de créer des contraintes en fonction des propriétés des éléments d’interface utilisateur est contraintes d’ancrage. Par exemple, un contrôleur d’affichage en haut et bas repères expose le `TopAnchor`, `BottomAnchor` et `HeightAnchor` d’ancrage propriétés pendant une vue expose les propriétés edge, center, taille et de ligne de base.

> [!IMPORTANT]
> En plus de l’ensemble standard de propriétés d’ancrage, les vues iOS aussi incluent la `LayoutMarginsGuides` et `ReadableContentGuide` propriétés. Ces propriétés présentent des `UILayoutGuide` objets permettant de travailler avec des marges de la vue et lisibles respectivement des guides de contenu.

Disposition ancres fournissent plusieurs méthodes pour créer des contraintes dans un format compact facile à lire :

- **ConstraintEqualTo** -définit une relation où `first attribute = second attribute + [constant]` avec éventuellement fourni `constant` valeur de décalage.
- **ConstraintGreaterThanOrEqualTo** -définit une relation où `first attribute >= second attribute + [constant]` avec éventuellement fourni `constant` valeur de décalage.
- **ConstraintLessThanOrEqualTo** -définit une relation où `first attribute <= second attribute + [constant]` avec éventuellement fourni `constant` valeur de décalage.

Par exemple :

```csharp
// Get the parent view's layout
var margins = View.LayoutMarginsGuide;

// Pin the leading edge of the view to the margin
OrangeView.LeadingAnchor.ConstraintEqualTo (margins.LeadingAnchor).Active = true;

// Pin the trailing edge of the view to the margin
OrangeView.TrailingAnchor.ConstraintEqualTo (margins.TrailingAnchor).Active = true;

// Give the view a 1:2 aspect ratio
OrangeView.HeightAnchor.ConstraintEqualTo (OrangeView.WidthAnchor, 2.0f);
```

Une contrainte de mise en page classique peut être exprimée comme une expression linéaire simple. Prenons l’exemple suivant :

[![](programmatic-layout-constraints-images/graph01.png "Une contrainte de mise en page exprimée sous la forme d’une expression linéaire")](programmatic-layout-constraints-images/graph01.png#lightbox)

Qui est converti à la ligne suivante de C# code à l’aide de points d’ancrage disposition :

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Où les parties de la C# code correspondent aux parties de l’équation données comme suit :

|Équation|Code|
|---|---|
|Élément 1|PurpleView|
|Attribut 1|LeadingAnchor|
|Relation|ConstraintEqualTo|
|Multiplicateur|Valeur par défaut est 1.0 donc ne pas spécifié|
|Élément 2|OrangeView|
|Attribut 2|TrailingAnchor|
|Constante|10.0|

En plus de fournir uniquement les paramètres qui sont nécessaires pour résoudre une équation de contrainte de mise en page donnée, chacune des méthodes d’ancrage de disposition appliquer la cohérence des types des paramètres passés à eux. Contrainte donc horizontal ancre comme `LeadingAnchor` ou `TrailingAnchor` ne peut être utilisé avec un autre point d’ancrage horizontal types et les multiplicateurs sont fournies uniquement à des contraintes de taille.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Contraintes de disposition

Vous pouvez ajouter manuellement des contraintes de disposition automatique en créant directement un `NSLayoutConstraint` dans C# code. Contrairement à l’utilisation des ancres de disposition, vous devez spécifier une valeur pour chaque paramètre, même si elle aura aucun effet sur la contrainte qui est définie. Par conséquent, vous obtiendrez produisant une quantité considérable de difficile à lire, un code réutilisable. Par exemple :

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Où les `NSLayoutAttribute` enum définit la valeur pour les marges de la vue et correspondent à la `LayoutMarginsGuide` propriétés, telles que `Left`, `Right`, `Top` et `Bottom` et le `NSLayoutRelation` enum définit la relation qui sera créé entre les attributs donnés en tant que `Equal`, `LessThanOrEqual` ou `GreaterThanOrEqual`.

À la différence avec l’API d’ancrage de la disposition, le `NSLayoutConstraint` méthodes de création n’a pas mettre en surbrillance les aspects importants d’une contrainte particulier et il n’existe aucune compilation ceux effectués sur la contrainte de temps. Par conséquent, il est facile de construire une contrainte non valide qui lève une exception lors de l’exécution.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Langue de Format Visual

La langue de Format Visual vous permet de définir des contraintes à l’aide d’art ASCII telles que des chaînes qui fournissent une représentation visuelle de la contrainte en cours de création. Cela présente les avantages et inconvénients suivants :

- La langue de Format Visual applique la création de contraintes valides uniquement.
- Disposition automatique génère des contraintes sur la console à l’aide de la langue de Format Visual pour les messages de débogage ressemblera le code utilisé pour créer la contrainte.
- La langue de Format Visual vous permet de créer plusieurs contraintes en même temps avec une expression très compacte.
- Dans la mesure où il n’existe aucune validation de compilation côté des chaînes de langue de Format Visual, vous pourrez uniquement détecter les problèmes lors de l’exécution.
- Étant donné que la langue de Format Visual privilégie la visualisation plutôt que certains types de contrainte ne peut pas être créés avec lui (par exemple, les ratios) souci d’exhaustivité.

Vous procédez comme suit lors de l’utilisation de la langue de Format Visual pour créer une contrainte :

1. Créer un `NSDictionary` qui contient les objets de vue et les repères de disposition et une clé de chaîne qui sera utilisée lors de la définition des formats.
2. Vous pouvez également créer un `NSDictionary` qui définit un ensemble de clés et valeurs (`NSNumber`) utilisé en tant que la valeur de constante pour la contrainte.
3. Créer la chaîne de format à disposition une seule colonne ou une ligne d’éléments.
4. Appelez le `FromVisualFormat` méthode de la `NSLayoutConstraint` classe pour générer les contraintes.
5. Appelez le `ActivateConstraints` méthode de la `NSLayoutConstraint` classe à activer et appliquer les contraintes.

Par exemple, pour créer de début et une contrainte de fin dans la langue de Format visuel, vous pouvez utiliser les éléments suivants :

```csharp
// Get views being constrained
var views = new NSMutableDictionary (); 
views.Add (new NSString ("orangeView"), OrangeView);

// Define format and assemble constraints
var format = "|-[orangeView]-|";
var constraints = NSLayoutConstraint.FromVisualFormat (format, NSLayoutFormatOptions.AlignAllTop, null, views);

// Apply constraints
NSLayoutConstraint.ActivateConstraints (constraints);
```

Étant donné que la langue de Format Visual crée toujours zéro attachés pour les marges du mode de parent lors de l’utilisation de l’espacement par défaut des contraintes de point, ce code produit des résultats identiques pour les exemples présentés ci-dessus.

Pour les conceptions d’interface utilisateur plus complexes, tels que plusieurs vues enfants sur une seule ligne, la langue de Format Visual Spécifie l’espacement horizontal et l’alignement vertical. Comme dans l’exemple ci-dessus où il spécifie le `AlignAllTop` `NSLayoutFormatOptions` aligne toutes les vues dans une ligne ou colonne vers leurs tops.

Consultez d’Apple [annexe de langue de Format Visual](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) pour obtenir des exemples de spécifier des contraintes courantes et la syntaxe de chaîne de Format Visual.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Ce guide présentées création et utilisation des contraintes de disposition automatique dans C# plutôt que de les créer graphiquement dans le concepteur iOS. Tout d’abord, il examiné à l’aide de la mise en page ancres (`NSLayoutAnchor`) pour gérer la disposition automatique. Ensuite, il vous a montré comment utiliser des contraintes de disposition (`NSLayoutConstraint`). Enfin, il affiche à l’aide de la langue de Format Visual pour la disposition automatique.

## <a name="related-links"></a>Liens associés

- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
- [procédure pas à pas de contrôles concevable d’iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Disposition automatique avec le Concepteur de Xamarin pour iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple - création par programmation des contraintes](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple - Format visuel langage annexe](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)

---
title: Contraintes de disposition par programmation dans Xamarin. iOS
description: Ce guide présente l’utilisation des contraintes de disposition automatique C# iOS dans le code au lieu de les créer dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 1c8cc9d810a7555626cb00ab0a05dfe03896c94a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292992"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Contraintes de disposition par programmation dans Xamarin. iOS

_Ce guide présente l’utilisation des contraintes de disposition automatique C# iOS dans le code au lieu de les créer dans le concepteur iOS._

La disposition automatique (également appelée « disposition adaptative ») est une approche de conception réactive. Contrairement au système de disposition transitoire, où l’emplacement de chaque élément est codé en dur à un point de l’écran, la disposition automatique concerne les *relations* , c’est-à-dire les positions des éléments par rapport à d’autres éléments sur l’aire de conception. Au cœur de la disposition automatique se trouve l’idée de contraintes ou de règles qui définissent le positionnement d’un élément ou d’un ensemble d’éléments dans le contexte d’autres éléments à l’écran. Étant donné que les éléments ne sont pas liés à une position particulière sur l’écran, les contraintes aident à créer une disposition adaptative qui semble correcte sur différentes tailles d’écran et orientations de périphérique.

En général, lorsque vous travaillez avec la disposition automatique dans iOS, vous utilisez le concepteur iOS pour placer graphiquement les contraintes de disposition sur vos éléments d’interface utilisateur. Toutefois, il peut arriver que vous deviez créer et appliquer des contraintes dans C# le code. Par exemple, lors de l’utilisation d’éléments d’interface utilisateur créés `UIView`dynamiquement ajoutés à un.

Ce guide vous montre comment créer et utiliser des contraintes à l’aide C# de code au lieu de les créer graphiquement dans le concepteur iOS.

<a name="Creating-Constraints-Programmatically" />

## <a name="creating-constraints-programmatically"></a>Création de contraintes par programmation

Comme indiqué ci-dessus, vous utiliserez généralement des contraintes de mise en page automatique dans le concepteur iOS. Dans les cas où vous devez créer vos contraintes par programme, vous avez le choix entre trois options :

- [Ancres de mise en page](#Layout-Anchors) : cette API fournit l’accès aux propriétés d' `TopAnchor`ancrage `BottomAnchor` ( `HeightAnchor`telles que, ou) des éléments d’interface utilisateur qui sont contraints.
- [Contraintes de disposition](#Layout-Constraints) : vous pouvez créer des contraintes directement `NSLayoutConstraint` à l’aide de la classe.
- [Langage de mise en forme visuelle](#Visual-Format-Language) : fournit une méthode de type art ASCII pour définir vos contraintes.

Les sections suivantes décrivent en détail chaque option.

<a name="Layout-Anchors" />

### <a name="layout-anchors"></a>Ancres de disposition

À l’aide `NSLayoutAnchor` de la classe, vous disposez d’une interface Fluent pour créer des contraintes basées sur les propriétés d’ancrage des éléments d’interface utilisateur qui sont contraints. Par exemple, les repères de disposition haut et bas d’un contrôleur `TopAnchor`d' `BottomAnchor` affichage `HeightAnchor` exposent, et les propriétés d’ancrage tandis qu’une vue expose les propriétés Edge, Center, size et Baseline.

> [!IMPORTANT]
> En plus de l’ensemble standard de propriétés d’ancrage, les vues iOS incluent `LayoutMarginsGuides` également `ReadableContentGuide` les propriétés et. Ces propriétés exposent `UILayoutGuide` des objets pour travailler respectivement avec les marges et les guides de contenu lisibles de la vue.

Les ancres de disposition offrent plusieurs méthodes pour créer des contraintes dans un format compact et facile à lire :

- **ConstraintEqualTo** : définit une relation où `first attribute = second attribute + [constant]` avec une valeur de décalage `constant` éventuellement fournie.
- **ConstraintGreaterThanOrEqualTo** : définit une relation où `first attribute >= second attribute + [constant]` avec une valeur de décalage `constant` éventuellement fournie.
- **ConstraintLessThanOrEqualTo** : définit une relation où `first attribute <= second attribute + [constant]` avec une valeur de décalage `constant` éventuellement fournie.

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

Une contrainte de disposition classique peut être exprimée simplement comme une expression linéaire. Prenons l’exemple suivant :

[![](programmatic-layout-constraints-images/graph01.png "Contrainte de disposition exprimée sous la forme d’une expression linéaire")](programmatic-layout-constraints-images/graph01.png#lightbox)

Qui serait converti en la ligne de code suivante C# à l’aide des ancres de disposition :

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Où les parties du C# code correspondent aux parties données de l’équation comme suit :

|Sommaire|Code|
|---|---|
|Élément 1|PurpleView|
|Attribut 1|LeadingAnchor|
|Relation|ConstraintEqualTo|
|Multiple|La valeur par défaut est 1,0, ce qui n’est pas spécifié|
|Élément 2|OrangeView|
|Attribut 2|TrailingAnchor|
|Constante|10.0|

En plus de fournir uniquement les paramètres requis pour résoudre une équation de contrainte de disposition donnée, chacune des méthodes d’ancrage de disposition applique la sécurité de type des paramètres qui lui sont passés. Les ancres de contrainte horizontale, `LeadingAnchor` telles `TrailingAnchor` que ou, ne peuvent être utilisées qu’avec d’autres types d’ancres horizontales et les multiplicateurs sont fournis uniquement aux contraintes de taille.

<a name="Layout-Constraints" />

### <a name="layout-constraints"></a>Contraintes de disposition

Vous pouvez ajouter manuellement des contraintes de disposition automatique en construisant `NSLayoutConstraint` directement C# une dans le code. Contrairement à l’utilisation d’ancres de disposition, vous devez spécifier une valeur pour chaque paramètre, même si elle n’a aucun effet sur la contrainte en cours de définition. Par conséquent, vous obtenez une quantité considérable de code réutilisable et difficile à lire. Par exemple :

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Où l' `NSLayoutAttribute` énumération définit la valeur des marges de la vue et correspond `LayoutMarginsGuide` `Top` aux propriétés `Bottom` telles `Left`que `Right`, et, et `NSLayoutRelation` l’enum définit la relation qui sera créé entre les attributs donnés en tant `Equal`que `LessThanOrEqual` , `GreaterThanOrEqual`ou.

Contrairement à l’API de disposition d’ancrage `NSLayoutConstraint` , les méthodes de création ne mettent pas en évidence les aspects importants d’une contrainte particulière et il n’y a pas de vérifications de l’heure de compilation effectuées sur la contrainte. Par conséquent, il est facile de construire une contrainte non valide qui lèvera une exception au moment de l’exécution.

<a name="Visual-Format-Language" />

### <a name="visual-format-language"></a>Langage de format visuel

Le langage de format visuel vous permet de définir des contraintes à l’aide d’une illustration ASCII, comme des chaînes qui fournissent une représentation visuelle de la contrainte créée. Cela présente les avantages et inconvénients suivants :

- Le langage de format visuel applique uniquement la création de contraintes valides.
- La disposition automatique génère des contraintes sur la console à l’aide du langage de format visuel afin que les messages de débogage ressemblent au code utilisé pour créer la contrainte.
- Le langage de format visuel vous permet de créer plusieurs contraintes en même temps avec une expression très compacte.
- Étant donné qu’il n’y a aucune validation côté compilation des chaînes de langage de format visuel, les problèmes peuvent être découverts uniquement au moment de l’exécution.
- Étant donné que la langue du format visuel met l’accent sur l’exhaustivité, il est impossible de créer des types de contrainte avec celle-ci (par exemple, les ratios).

Lorsque vous utilisez la langue de format visuel pour créer une contrainte, procédez comme suit :

1. Créez un `NSDictionary` qui contient les objets de vue et les repères de mise en page et une clé de chaîne qui sera utilisée lors de la définition des formats.
2. Créez éventuellement un `NSDictionary` qui définit un jeu de clés et de valeurs (`NSNumber`) utilisé comme valeur constante pour la contrainte.
3. Créez la chaîne de format pour mettre en forme une seule colonne ou ligne d’éléments.
4. Appelez la `FromVisualFormat` méthode de la `NSLayoutConstraint` classe pour générer les contraintes.
5. Appelez la `ActivateConstraints` méthode de la `NSLayoutConstraint` classe pour activer et appliquer les contraintes.

Par exemple, pour créer une contrainte de début et de fin dans la langue de format visuel, vous pouvez utiliser ce qui suit :

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

Étant donné que la langue de format visuel crée toujours des contraintes de point zéro attachées aux marges de la vue parent lors de l’utilisation de l’espacement par défaut, ce code produit des résultats identiques aux exemples présentés ci-dessus.

Pour des conceptions d’interface utilisateur plus complexes, telles que plusieurs vues enfants sur une seule ligne, le langage de format visuel spécifie l’espacement horizontal et l’alignement vertical. Comme dans l’exemple ci-dessus où il `AlignAllTop` spécifie `NSLayoutFormatOptions` , aligne toutes les vues d’une ligne ou d’une colonne sur leur sommet.

Pour obtenir des exemples de spécification de contraintes courantes et de la grammaire de chaîne de format visuel, consultez [l’annexe du langage de format visuel](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) d’Apple.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Ce guide a présenté la création et l’utilisation de contraintes C# de mise en page automatique dans, par opposition à leur création graphique dans le concepteur iOS. Tout d’abord, il a vu à l’aide`NSLayoutAnchor`des ancres de disposition () pour gérer la disposition automatique. Ensuite, il a montré comment utiliser les contraintes de disposition`NSLayoutConstraint`(). Enfin, il a présenté l’utilisation de la langue de format visuel pour la disposition automatique.

## <a name="related-links"></a>Liens associés

- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
- [Procédures pas à pas pour les contrôles iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Disposition automatique avec le Xamarin Designer pour iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-création de contraintes par programmation](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-annexe du langage de format visuel](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)

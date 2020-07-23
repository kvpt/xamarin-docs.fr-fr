---
title: Contraintes de disposition par programmation dans Xamarin. iOS
description: Ce guide présente l’utilisation des contraintes de disposition automatique iOS dans le code C# au lieu de les créer dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 119C8365-B470-4CD4-85F7-086F0A46DCBB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: d8dcf64f9a905557bea69402bcaf7fcfb51fb10b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928573"
---
# <a name="programmatic-layout-constraints-in-xamarinios"></a>Contraintes de disposition par programmation dans Xamarin. iOS

_Ce guide présente l’utilisation des contraintes de disposition automatique iOS dans le code C# au lieu de les créer dans le concepteur iOS._

La disposition automatique (également appelée « disposition adaptative ») est une approche de conception réactive. Contrairement au système de disposition transitoire, où l’emplacement de chaque élément est codé en dur à un point de l’écran, la disposition automatique concerne les *relations* , c’est-à-dire les positions des éléments par rapport à d’autres éléments sur l’aire de conception. Au cœur de la disposition automatique se trouve l’idée de contraintes ou de règles qui définissent le positionnement d’un élément ou d’un ensemble d’éléments dans le contexte d’autres éléments à l’écran. Étant donné que les éléments ne sont pas liés à une position particulière sur l’écran, les contraintes aident à créer une disposition adaptative qui semble correcte sur différentes tailles d’écran et orientations de périphérique.

En général, lorsque vous travaillez avec la disposition automatique dans iOS, vous utilisez le concepteur iOS pour placer graphiquement les contraintes de disposition sur vos éléments d’interface utilisateur. Toutefois, il peut arriver que vous deviez créer et appliquer des contraintes dans du code C#. Par exemple, lors de l’utilisation d’éléments d’interface utilisateur créés dynamiquement ajoutés à un `UIView` .

Ce guide vous montre comment créer et utiliser des contraintes à l’aide de code C# au lieu de les créer graphiquement dans le concepteur iOS.

<a name="Creating-Constraints-Programmatically"></a>

## <a name="creating-constraints-programmatically"></a>Création de contraintes par programmation

Comme indiqué ci-dessus, vous utiliserez généralement des contraintes de mise en page automatique dans le concepteur iOS. Dans les cas où vous devez créer vos contraintes par programme, vous avez le choix entre trois options :

- [Ancres de mise en page](#Layout-Anchors) : cette API fournit l’accès aux propriétés d’ancrage (telles que `TopAnchor` , `BottomAnchor` ou `HeightAnchor` ) des éléments d’interface utilisateur qui sont contraints.
- [Contraintes de disposition](#Layout-Constraints) : vous pouvez créer des contraintes directement à l’aide de la `NSLayoutConstraint` classe.
- [Langage de mise en forme visuelle](#Visual-Format-Language) : fournit une méthode de type art ASCII pour définir vos contraintes.

Les sections suivantes décrivent en détail chaque option.

<a name="Layout-Anchors"></a>

### <a name="layout-anchors"></a>Ancres de disposition

À l’aide de la `NSLayoutAnchor` classe, vous disposez d’une interface Fluent pour créer des contraintes basées sur les propriétés d’ancrage des éléments d’interface utilisateur qui sont contraints. Par exemple, les repères de disposition haut et bas d’un contrôleur d’affichage exposent `TopAnchor` , `BottomAnchor` et les propriétés d' `HeightAnchor` ancrage tandis qu’une vue expose les propriétés Edge, Center, size et Baseline.

> [!IMPORTANT]
> En plus de l’ensemble standard de propriétés d’ancrage, les vues iOS incluent également les `LayoutMarginsGuides` `ReadableContentGuide` Propriétés et. Ces propriétés exposent `UILayoutGuide` des objets pour travailler respectivement avec les marges et les guides de contenu lisibles de la vue.

Les ancres de disposition offrent plusieurs méthodes pour créer des contraintes dans un format compact et facile à lire :

- **ConstraintEqualTo** : définit une relation où `first attribute = second attribute + [constant]` avec une valeur de décalage éventuellement fournie `constant` .
- **ConstraintGreaterThanOrEqualTo** : définit une relation où `first attribute >= second attribute + [constant]` avec une valeur de décalage éventuellement fournie `constant` .
- **ConstraintLessThanOrEqualTo** : définit une relation où `first attribute <= second attribute + [constant]` avec une valeur de décalage éventuellement fournie `constant` .

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

[![Contrainte de disposition exprimée sous la forme d’une expression linéaire](programmatic-layout-constraints-images/graph01.png)](programmatic-layout-constraints-images/graph01.png#lightbox)

Qui serait converti en la ligne suivante de code C# à l’aide d’ancres de disposition :

```csharp
PurpleView.LeadingAnchor.ConstraintEqualTo (OrangeView.TrailingAnchor, 10).Active = true; 
```

Où les parties du code C# correspondent aux parties données de l’équation comme suit :

|Sommaire|Code|
|---|---|
|Item 1|PurpleView|
|Attribut 1|LeadingAnchor|
|Relation|ConstraintEqualTo|
|Multiplicateur|La valeur par défaut est 1,0, ce qui n’est pas spécifié|
|Élément 2|OrangeView|
|Attribut 2|TrailingAnchor|
|Constant|10.0|

En plus de fournir uniquement les paramètres requis pour résoudre une équation de contrainte de disposition donnée, chacune des méthodes d’ancrage de disposition applique la sécurité de type des paramètres qui lui sont passés. Les ancres de contrainte horizontale, telles que `LeadingAnchor` ou, `TrailingAnchor` ne peuvent être utilisées qu’avec d’autres types d’ancres horizontales et les multiplicateurs sont fournis uniquement aux contraintes de taille.

<a name="Layout-Constraints"></a>

### <a name="layout-constraints"></a>Contraintes de disposition

Vous pouvez ajouter manuellement des contraintes de disposition automatique en créant directement un `NSLayoutConstraint` en code C#. Contrairement à l’utilisation d’ancres de disposition, vous devez spécifier une valeur pour chaque paramètre, même si elle n’a aucun effet sur la contrainte en cours de définition. Par conséquent, vous obtenez une quantité considérable de code réutilisable et difficile à lire. Par exemple :

```csharp
//// Pin the leading edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Leading, NSLayoutRelation.Equal, View, NSLayoutAttribute.LeadingMargin, 1.0f, 0.0f).Active = true;

//// Pin the trailing edge of the view to the margin
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Trailing, NSLayoutRelation.Equal, View, NSLayoutAttribute.TrailingMargin, 1.0f, 0.0f).Active = true;

//// Give the view a 1:2 aspect ratio
NSLayoutConstraint.Create (OrangeView, NSLayoutAttribute.Height, NSLayoutRelation.Equal, OrangeView, NSLayoutAttribute.Width, 2.0f, 0.0f).Active = true;
```

Où l' `NSLayoutAttribute` enum définit la valeur des marges de la vue et correspondent aux `LayoutMarginsGuide` propriétés telles que `Left` , et, `Right` `Top` `Bottom` et l' `NSLayoutRelation` enum définit la relation qui sera créée entre les attributs donnés comme `Equal` , `LessThanOrEqual` ou `GreaterThanOrEqual` .

Contrairement à l’API de disposition d’ancrage, les `NSLayoutConstraint` méthodes de création ne mettent pas en évidence les aspects importants d’une contrainte particulière et il n’y a pas de vérifications de l’heure de compilation effectuées sur la contrainte. Par conséquent, il est facile de construire une contrainte non valide qui lèvera une exception au moment de l’exécution.

<a name="Visual-Format-Language"></a>

### <a name="visual-format-language"></a>Langage de format visuel

Le langage de format visuel vous permet de définir des contraintes à l’aide d’une illustration ASCII, comme des chaînes qui fournissent une représentation visuelle de la contrainte créée. Cela présente les avantages et inconvénients suivants :

- Le langage de format visuel applique uniquement la création de contraintes valides.
- La disposition automatique génère des contraintes sur la console à l’aide du langage de format visuel afin que les messages de débogage ressemblent au code utilisé pour créer la contrainte.
- Le langage de format visuel vous permet de créer plusieurs contraintes en même temps avec une expression très compacte.
- Étant donné qu’il n’y a aucune validation côté compilation des chaînes de langage de format visuel, les problèmes peuvent être découverts uniquement au moment de l’exécution.
- Étant donné que la langue du format visuel met l’accent sur l’exhaustivité, il est impossible de créer des types de contrainte avec celle-ci (par exemple, les ratios).

Lorsque vous utilisez la langue de format visuel pour créer une contrainte, procédez comme suit :

1. Créez un `NSDictionary` qui contient les objets de vue et les repères de mise en page et une clé de chaîne qui sera utilisée lors de la définition des formats.
2. Créez éventuellement un `NSDictionary` qui définit un jeu de clés et de valeurs ( `NSNumber` ) utilisé comme valeur constante pour la contrainte.
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

Pour des conceptions d’interface utilisateur plus complexes, telles que plusieurs vues enfants sur une seule ligne, le langage de format visuel spécifie l’espacement horizontal et l’alignement vertical. Comme dans l’exemple ci-dessus où il spécifie, `AlignAllTop` `NSLayoutFormatOptions` aligne toutes les vues d’une ligne ou d’une colonne sur leur sommet.

Pour obtenir des exemples de spécification de contraintes courantes et de la grammaire de chaîne de format visuel, consultez [l’annexe du langage de format visuel](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1) d’Apple.

<a name="Summary"></a>

## <a name="summary"></a>Résumé

Ce guide a présenté la création et l’utilisation de contraintes de mise en page automatique en C#, par opposition à leur création graphique dans le concepteur iOS. Tout d’abord, il a vu à l’aide des ancres de disposition ( `NSLayoutAnchor` ) pour gérer la disposition automatique. Ensuite, il a montré comment utiliser les contraintes de disposition ( `NSLayoutConstraint` ). Enfin, il a présenté l’utilisation de la langue de format visuel pour la disposition automatique.

## <a name="related-links"></a>Liens associés

- [Introduction aux storyboards](~/ios/user-interface/storyboards/index.md)
- [Procédures pas à pas pour les contrôles iOS](~/ios/user-interface/designer/ios-designable-controls-walkthrough.md)
- [Disposition automatique avec le Xamarin Designer pour iOS](~/ios/user-interface/designer/designer-auto-layout.md#modifying-in-code)
- [Apple-création de contraintes par programmation](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1)
- [Apple-annexe du langage de format visuel](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH27-SW1)

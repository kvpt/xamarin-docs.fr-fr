---
title: Propriétés, méthodes et événements du contrôle commun Xamarin. Forms
description: Cet article décrit les propriétés, méthodes et événements communs définis sur la classe VisualElement, qui sont couramment utilisés dans les classes dérivées.
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/21/2019
ms.openlocfilehash: 6d10e665c6461655440ddfb2c524cb56a14337f6
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304211"
---
# <a name="xamarinforms-common-control-properties-methods-and-events"></a>Propriétés, méthodes et événements du contrôle commun Xamarin. Forms

La classe Xamarin. Forms `VisualElement` est la classe de base pour la plupart des contrôles utilisés dans une application Xamarin. Forms. La classe `VisualElement` définit de nombreuses [Propriétés](#properties), [méthodes](#methods)et [événements](#events) qui sont utilisés dans les classes dérivées.

## <a name="properties"></a>Propriétés

Les propriétés suivantes sont disponibles sur les instances de `VisualElement`. Pour obtenir une liste complète, consultez les propriétés de l' [API VisualElement](xref:Xamarin.Forms.VisualElement#properties).

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

La propriété `AnchorX` est une valeur `double` qui définit le point central sur l’axe des X pour les transformations telles que l’échelle et la rotation. La valeur par défaut est 0,5.

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

La propriété `AnchorY` est une valeur `double` qui définit le point central sur l’axe des X pour les transformations telles que l’échelle et la rotation. La valeur par défaut est 0,5.

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

La propriété `BackgroundColor` est une `Color` qui détermine la couleur d’arrière-plan du contrôle. Si la valeur est non définie, l’arrière-plan est l’objet `Color` par défaut, qui est rendu transparent.

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

La propriété `Behaviors` est un `List` d’objets `Behavior`. Les comportements vous permettent de joindre des fonctionnalités réutilisables aux éléments en les ajoutant à la liste `Behaviors`. Pour plus d’informations sur la classe `Behavior`, consultez [comportements Xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/index.md).

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

La propriété `Bounds` est un objet `Rectangle` en lecture seule qui représente l’espace occupé par le contrôle. La valeur de la propriété `Bounds` est assignée pendant le cycle de disposition. Le `struct` `Rectangle` contient des propriétés et des méthodes utiles pour tester l’intersection et la relation contenant-contenu des rectangles. Pour plus d’informations, consultez l' [API rectangle Xamarin. Forms](xref:Xamarin.Forms.Rectangle).

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

La propriété `Effects` est une `List` d’objets `Effect`, héritée de la classe `Element`(XREF : Xamarin. Forms. Element). Les effets permettent de personnaliser les contrôles natifs et sont généralement utilisés pour les petites modifications de style. Pour plus d’informations sur la classe `Effect`, consultez [effets Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md).

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

La propriété `FlowDirection` est une valeur d’énumération `FlowDirection`. Le sens du déroulement peut être défini sur `MatchParent`, `LeftToRight`ou `RightToLeft` et détermine l’ordre et la direction de la disposition. La propriété `FlowDirection` est généralement utilisée pour prendre en charge les langues qui se lisent de droite à gauche.

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

La propriété `Height` est une valeur de `double` en lecture seule qui décrit la hauteur rendue du contrôle. La propriété `Height` est calculée pendant le cycle de présentation et ne peut pas être définie directement. La hauteur d’un contrôle peut être demandée à l’aide de la [propriété HeightRequest](#heightrequest).

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

La propriété `HeightRequest` est une valeur `double` qui détermine la hauteur souhaitée du contrôle. La hauteur absolue du contrôle peut ne pas correspondre à la valeur demandée. Pour plus d’informations, consultez Propriétés de la [demande](#request-properties).

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

La propriété `InputTransparent` est une `bool` qui détermine si le contrôle reçoit une entrée d’utilisateur. La valeur par défaut est `false`, garantissant que l’élément reçoit l’entrée. Cette propriété est transférée aux éléments enfants lorsqu’elle est définie. Si vous affectez la valeur `true` à la propriété `InputTransparent` sur une classe Layout, tous les éléments de la disposition ne recevront pas d’entrée.

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

La propriété `IsEnabled` est une valeur `bool` qui détermine si le contrôle réagit à l’entrée utilisateur. La valeur par défaut est `true`. L’affectation de la valeur false à cette propriété empêche le contrôle d’accepter l’entrée d’utilisateur.

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

La propriété `IsFocused` est une valeur `bool` qui indique si le contrôle est actuellement l’objet ayant le focus. L’appel de la méthode [`Focus`](#focus) sur le contrôle entraîne la définition de la valeur `IsFocused` sur true. L’appel de la méthode [`Unfocus`](#unfocus) affecte la valeur false à cette propriété.

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

La propriété `IsTabStop` est une valeur `bool` qui définit si le contrôle reçoit le focus lorsque l’utilisateur avance dans les contrôles à l’aide de la touche Tab. Si cette propriété a la valeur false, la propriété `TabIndex` n’a aucun effet.

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

La propriété `IsVisible` est une valeur `bool` qui détermine si le contrôle est rendu. Les contrôles dont la propriété `IsVisible` a la valeur false ne s’affichent pas, ne sont pas pris en compte pour les calculs d’espace pendant le cycle de présentation et ne peuvent pas accepter d’entrée utilisateur.

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

La propriété `MinimumHeightRequest` est une valeur `double` qui détermine comment le dépassement de capacité est géré lorsque deux éléments sont en concurrence pour l’espace limité. La définition de la propriété `MinimumHeightRequest` permet au processus de disposition de mettre l’élément à l’échelle jusqu’à la dimension minimale demandée. Si aucun `MinimumHeightRequest` n’est spécifié, la valeur par défaut est-1 et le processus de disposition considère le `HeightRequest` comme étant la valeur minimale. Cela signifie que les éléments sans valeur de `MinimumHeightRequest` n’auront pas de hauteur évolutive.

Pour plus d’informations, consultez [propriétés minimales des demandes](#minimum-request-properties).

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

La propriété `MinimumWidthRequest` est une valeur `double` qui détermine comment le dépassement de capacité est géré lorsque deux éléments sont en concurrence pour l’espace limité. La définition de la propriété `MinimumWidthRequest` permet au processus de disposition de mettre l’élément à l’échelle jusqu’à la dimension minimale demandée. Si aucun `MinimumWidthRequest` n’est spécifié, la valeur par défaut est-1 et le processus de disposition considère le `WidthRequest` comme étant la valeur minimale. Cela signifie que les éléments sans `MinimumWidthRequest` valeur n’auront pas de largeur modulable.

Pour plus d’informations, consultez [propriétés minimales des demandes](#minimum-request-properties).

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

La propriété `Opacity` est une valeur `double` comprise entre zéro et un qui détermine l’opacité du contrôle pendant le rendu. La valeur par défaut de cette propriété est 1,0. Les valeurs situées en dehors de la plage comprise entre 0 et 1 seront ancrées. La propriété `Opacity` est appliquée uniquement si la propriété [`IsVisible`](#isvisible) est `true`. L’opacité est appliquée de manière itérative. Par conséquent, si un contrôle parent a une opacité de 0,5 et que son enfant a 0,5 d’opacité, l’enfant s’affiche avec une valeur d’opacité 0,25 effective. L’affectation de la valeur 0 à la propriété `Opacity` d’un contrôle d’entrée a un comportement indéfini.

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

La propriété `Parent` est héritée de la classe `Element`. Cette propriété est un objet `Element` qui est le parent de Control. La propriété `Parent` est généralement définie automatiquement sur un élément lorsqu’elle est ajoutée en tant qu’enfant d’un autre élément.

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

La propriété `Resources` est une instance `ResourceDictionary` qui est remplie avec des paires clé/valeur qui sont généralement remplies au moment de l’exécution à partir de XAML. Ce dictionnaire permet aux développeurs d’applications de réutiliser les objets définis en XAML à la fois au moment de la compilation et au moment de l’exécution. Les clés du dictionnaire sont remplies à partir de l’attribut `x:Key` de la balise XAML. L’objet créé à partir de XAML est inséré dans le `ResourceDictionary` pour la clé spécifiée. une fois qu’elle a été initialisée.

Pour plus d’informations, consultez [dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

La propriété `Rotation` est une valeur de `double` comprise entre zéro et 360 qui définit la rotation autour de l’axe Z, en degrés. La valeur par défaut de cette propriété est 0. La rotation est appliquée par rapport aux valeurs [`AnchorX`](#anchorx) et [`AnchorY`](#anchory) .

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

La propriété `RotationX` est une valeur de `double` comprise entre zéro et 360 qui définit la rotation autour de l’axe X, en degrés. La valeur par défaut de cette propriété est 0. La rotation est appliquée par rapport aux valeurs [`AnchorX`](#anchorx) et [`AnchorY`](#anchory) .

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

La propriété `RotationY` est une valeur de `double` comprise entre zéro et 360 qui définit la rotation autour de l’axe Y, en degrés. La valeur par défaut de cette propriété est 0. La rotation est appliquée par rapport aux valeurs [`AnchorX`](#anchorx) et [`AnchorY`](#anchory) .

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

La propriété `Scale` est une valeur `double` qui définit l’échelle du contrôle. La valeur par défaut de cette propriété est 1,0. L’échelle est appliquée par rapport aux valeurs [`AnchorX`](#anchorx) et [`AnchorY`](#anchory) .

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

La propriété `ScaleX` est une valeur `double` qui définit l’échelle du contrôle le long de l’axe X. La valeur par défaut de cette propriété est 1,0. La propriété `ScaleX` est appliquée par rapport à la valeur [`AnchorX`](#anchorx) .

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

La propriété `ScaleY` est une valeur `double` qui définit l’échelle du contrôle le long de l’axe Y. La valeur par défaut de cette propriété est 1,0. La propriété `ScaleY` est appliquée par rapport à la valeur [`AnchorY`](#anchory) .

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

La propriété `Style` est héritée de la classe `NavigableElement`. Cette propriété est une instance de la classe `Style`. La classe `Style` contient des déclencheurs, des méthodes setter et des comportements qui définissent l’apparence et le comportement des éléments visuels. Pour plus d’informations, consultez [styles XAML Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/index.md).

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

La propriété `StyleClass` est une liste d’objets `string` qui représentent les noms des classes de `Style`. Cette propriété est héritée de la classe `NavigableElement`. La propriété `StyleClass` permet l’application de plusieurs attributs de style à une instance `VisualElement`. Pour plus d’informations, consultez [classes de style Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

La propriété `TabIndex` est une valeur `int` qui définit l’ordre des contrôles lors de l’avancement des contrôles à l’aide de la touche Tab. La propriété `TabIndex` correspond à l’implémentation de la propriété définie sur l’interface `ITabStopElement`, que la classe `VisualElement` implémente.

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

La propriété `TranslationX` est une valeur `double` qui définit la translation Delta à appliquer sur l’axe X. La traduction est appliquée après la disposition et est généralement utilisée pour appliquer des animations. Traduction d’un élément en dehors des limites de son conteneur parent mon empêchent les entrées de fonctionner.

Pour plus d’informations, consultez [animation dans Xamarin. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

La propriété `TranslationY` est une valeur `double` qui définit la translation Delta à appliquer sur l’axe Y. La traduction est appliquée après la disposition et est généralement utilisée pour appliquer des animations. Traduction d’un élément en dehors des limites de son conteneur parent mon empêchent les entrées de fonctionner.

Pour plus d’informations, consultez [animation dans Xamarin. Forms](~/xamarin-forms/user-interface/animation/index.md).

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

La propriété `Triggers` est un `List` en lecture seule des objets `TriggerBase`. Les déclencheurs permettent aux développeurs d’applications d’exprimer des actions en XAML qui modifient l’apparence visuelle des contrôles en réponse à des modifications d’événement ou de propriété. Pour plus d’informations, consultez [déclencheurs Xamarin. Forms](~/xamarin-forms/app-fundamentals/triggers.md).

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

La propriété `Visual` est une instance `IVisual` qui permet de créer des convertisseurs et de les appliquer de manière sélective à des instances de `VisualElement`. La propriété `Visual` est définie pour correspondre à son parent. ainsi, la définition d’un convertisseur sur un composant s’appliquera également à tous les enfants de ce composant. Si aucun convertisseur personnalisé n’est défini sur un contrôle ou ses ancêtres, le convertisseur Xamarin. Forms par défaut sera utilisé. Pour plus d’informations, consultez [Xamarin. Forms Visual](~/xamarin-forms/user-interface/visual/index.md).

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

La propriété `Width` est une valeur de `double` en lecture seule qui décrit la largeur restituée du contrôle. La propriété `Width` est calculée pendant le cycle de présentation et ne peut pas être définie directement. La largeur d’un contrôle peut être demandée à l’aide de la [propriété WidthRequest](#widthrequest).

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

La propriété `WidthRequest` est une valeur `double` qui détermine la largeur souhaitée du contrôle. La largeur absolue du contrôle peut ne pas correspondre à la valeur demandée. Pour plus d’informations, consultez Propriétés de la [demande](#request-properties).

### [`X`](xref:Xamarin.Forms.VisualElement.X)

La propriété `X` est une valeur de `double` en lecture seule qui décrit la position X actuelle du contrôle.

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

La propriété `Y` est une valeur de `double` en lecture seule qui décrit la position Y actuelle du contrôle.

## <a name="methods"></a>Méthodes

Les méthodes suivantes sont disponibles sur la classe `VisualElement`. Pour obtenir une liste complète, consultez Méthodes de l' [API VisualElement](xref:Xamarin.Forms.VisualElement#methods).

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

La méthode `FindByName` est héritée de la classe `Element` et possède la signature suivante :

```csharp
public object FindByName (string name)
```

Cette méthode recherche tous les éléments enfants pour l’argument `name` fourni et retourne l’élément qui porte le nom spécifié. Si aucune correspondance n'est trouvée, retourne la valeur `null`.

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

La méthode `Focus` tente de définir le focus sur l’élément. Cette méthode a la signature suivante :

```csharp
public bool Focus ()
```

La méthode `Focus` retourne `true` si le focus clavier a été correctement défini et `false` si l’appel de méthode n’a pas abouti à un changement de focus. L’élément doit être en mesure de recevoir le focus pour que cette méthode fonctionne. L’appel de la méthode `Focus` sur des éléments hors écran ou non réalisés a un comportement indéfini.

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

La méthode `Unfocus` tente de supprimer le focus sur l’élément. Cette méthode a la signature suivante :

```csharp
public void Unfocus ()
```

L’élément doit déjà avoir le focus pour que cette méthode fonctionne.

## <a name="events"></a>Événements

Les événements suivants sont disponibles sur la classe `VisualElement`. Pour obtenir une liste complète, consultez [événements VisualElement Xamarin. Forms](xref:Xamarin.Forms.VisualElement#events).

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

L’événement `Focused` est déclenché chaque fois que l’instance de `VisualElement` reçoit le focus. Cet événement n’est pas propagé via la pile Xamarin. Forms, il est reçu directement à partir du contrôle natif. Cet événement est émis par l’accesseur Set de propriété [`IsFocused`](#isfocused) .

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

L’événement `SizeChanged` est déclenché chaque fois que l’instance `VisualElement` `Height` ou `Width` propriétés changent. Si les développeurs souhaitent répondre directement à la modification de taille, au lieu de répondre à l’événement après modification, ils doivent implémenter l' [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) méthode virtuelle à la place.

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

L’événement `Unfocused` est déclenché chaque fois que l’instance de `VisualElement` perd le focus. Cet événement n’est pas propagé via la pile Xamarin. Forms, il est reçu directement à partir du contrôle natif. Cet événement est émis par l’accesseur Set de propriété [`IsFocused`](#isfocused) .

## <a name="units-of-measurement"></a>Unités de mesure

Les plateformes Android, iOS et UWP ont toutes des unités de mesure différentes qui peuvent varier d’un appareil à l’autre. Xamarin. Forms utilise une unité de mesure indépendante de la plateforme qui normalise les unités sur les appareils et les plateformes. Il y a 160 unités par pouce, ou 64 unités par centimètre, dans Xamarin. Forms.

## <a name="request-properties"></a>Propriétés de requête

Les propriétés dont le nom contient « Request » définissent une valeur souhaitée, qui peut ne pas correspondre à la valeur affichée réelle. Par exemple, `HeightRequest` peut avoir la valeur 150, mais si la disposition autorise uniquement de l’espace pour 100 unités, le rendu `Height` du contrôle sera uniquement 100. La taille rendue est affectée par l’espace disponible et les composants contenus.

## <a name="minimum-request-properties"></a>Propriétés minimales de la demande

Les propriétés de demande minimales incluent `MinimumHeightRequest` et `MinimumWidthRequest`, et sont destinées à permettre un contrôle plus précis sur la façon dont les éléments gèrent le dépassement de capacité les uns par rapport aux autres. Toutefois, le comportement de disposition lié à ces propriétés présente des considérations importantes.

### <a name="unspecified-minimum-property-values"></a>Valeurs de propriété minimales non spécifiées

Si une valeur minimale n’est pas définie, la propriété minimale prend par défaut la valeur-1. Le processus de disposition ignore cette valeur et considère que la valeur absolue est la valeur minimale. La conséquence pratique de ce comportement est qu’un élément sans valeur minimale spécifiée **ne sera pas** réduit. Un élément avec une valeur minimale spécifiée **est** réduit.

Le code XAML suivant montre deux éléments `BoxView` dans un `StackLayout`horizontal :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

La première `BoxView` instance demande une largeur de 500 et ne spécifie pas de largeur minimale. La deuxième `BoxView` demande une largeur de 500 et une largeur minimale de 250. Si l’élément de `StackLayout` parent n’est pas assez large pour contenir les deux composants à la largeur demandée, la première instance de `BoxView` sera considérée par le processus de disposition comme ayant une largeur minimale de 500, car aucun autre minimum valide n’est spécifié. La deuxième `BoxView` instance est autorisée à réduire le nombre de 250 et elle sera réduite pour s’ajuster jusqu’à ce que sa largeur atteigne 250 unités.

Si le comportement souhaité est que la première instance de `BoxView` est mise à l’échelle sans largeur minimale, le `MinimumWidthRequest` doit être défini sur une valeur valide, telle que 0.

### <a name="minimum-and-absolute-property-values"></a>Valeurs de propriété minimale et absolue

Le comportement n’est pas défini lorsque la valeur minimale est supérieure à la valeur absolue. Par exemple, si `MinimumWidthRequest` est défini sur 100, la propriété `WidthRequest` ne doit jamais dépasser 100. Lorsque vous spécifiez une valeur de propriété minimale, vous devez toujours spécifier une valeur absolue pour garantir que la valeur absolue est supérieure à la valeur minimale.

### <a name="minimum-properties-within-a-grid"></a>Propriétés minimales dans une grille

`Grid` dispositions ont leur propre système de dimensionnement relatif des lignes et des colonnes. L’utilisation de `MinimumWidthRequest` ou `MinimumHeightRequest` dans une disposition de `Grid` n’aura aucun effet. Pour plus d’informations, consultez [Xamarin. Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md).

## <a name="related-links"></a>Liens connexes

* [Documentation de l’API VisualElement](xref:Xamarin.Forms.VisualElement)

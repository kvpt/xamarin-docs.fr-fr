---
title: Xamarin.Forms Propriétés, méthodes et événements du contrôle commun
description: Cet article décrit les propriétés, méthodes et événements communs définis sur la classe VisualElement, qui sont couramment utilisés dans les classes dérivées.
ms.prod: xamarin
ms.assetId: 85A0CCF5-C1D8-40BB-927F-A4D944E5534D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c709331b743d6d6ac1f72fe87dae342b4478a920
ms.sourcegitcommit: 9a83e49eb4d2afb8b3a99ac2347f4b9f8a19e76b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330960"
---
# <a name="no-locxamarinforms-common-control-properties-methods-and-events"></a>Xamarin.Forms Propriétés, méthodes et événements du contrôle commun

La Xamarin.Forms `VisualElement` classe est la classe de base pour la plupart des contrôles utilisés dans une Xamarin.Forms application. La `VisualElement` classe définit de nombreuses [Propriétés](#properties), [méthodes](#methods)et [événements](#events) utilisés dans les classes dérivées.

## <a name="properties"></a>Propriétés

Les propriétés suivantes sont disponibles sur les [`VisualElement`](xref:Xamarin.Forms.VisualElement) objets.

### [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)

La `AnchorX` propriété est une `double` valeur qui définit le point central sur l’axe des X pour les transformations telles que l’échelle et la rotation. La valeur par défaut est 0,5.

### [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

La `AnchorY` propriété est une `double` valeur qui définit le point central sur l’axe des Y pour les transformations telles que l’échelle et la rotation. La valeur par défaut est 0,5.

### `Background`

La `Background` propriété est une `Brush` valeur qui permet d’utiliser des pinceaux comme arrière-plan dans n’importe quel contrôle. La valeur par défaut est `Brush.Default`.

### [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)

La `BackgroundColor` propriété est un `Color` qui détermine la couleur d’arrière-plan du contrôle. Si la valeur est non définie, l’arrière-plan est l’objet par défaut `Color` , qui est rendu transparent.

### [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)

La `Behaviors` propriété est un `List` `Behavior` objet d’objets. Les comportements vous permettent d’attacher des fonctionnalités réutilisables aux éléments en les ajoutant à la `Behaviors` liste. Pour plus d’informations sur la `Behavior` classe, consultez [ Xamarin.Forms comportements](~/xamarin-forms/app-fundamentals/behaviors/index.md).

### [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)

La `Bounds` propriété est un objet en lecture seule `Rectangle` qui représente l’espace occupé par le contrôle. La `Bounds` valeur de la propriété est affectée pendant le cycle de disposition. `Rectangle` `struct` Contient des propriétés et des méthodes utiles pour tester l’intersection et la relation contenant-contenu des rectangles. Pour plus d’informations, consultez l' [ Xamarin.Forms API rectangle](xref:Xamarin.Forms.Rectangle).

### `Clip`

La `Clip` propriété est un `Geometry` objet qui définit le contour du contenu d’un élément. Pour définir un clip, utilisez un `Geometry` objet tel que `EllipseGeometry` pour définir la propriété de l’élément `Clip` . Seule la zone située dans la région de la géométrie sera visible. Pour plus d’informations, consultez [clip avec une géométrie](~/xamarin-forms/user-interface/shapes/geometries.md#clip-with-a-geometry).

### [`Effects`](xref:Xamarin.Forms.Element.Effects)

La `Effects` propriété est un `List` `Effect` objet d’objets, hérité de la [`Element`](xref:Xamarin.Forms.Element) classe. Les effets permettent de personnaliser les contrôles natifs et sont généralement utilisés pour les petites modifications de style. Pour plus d’informations sur la `Effect` classe, consultez [ Xamarin.Forms Effects](~/xamarin-forms/app-fundamentals/effects/index.md).

### [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)

La `FlowDirection` propriété est une `FlowDirection` valeur enum. Le sens du déroulement peut être défini sur `MatchParent` , `LeftToRight` ou `RightToLeft` et détermine l’ordre et la direction de la disposition. La `FlowDirection` propriété est généralement utilisée pour prendre en charge les langues qui se lisent de droite à gauche.

### [`Height`](xref:Xamarin.Forms.VisualElement.Height)

La `Height` propriété est une valeur en lecture seule `double` qui décrit la hauteur rendue du contrôle. La `Height` propriété est calculée pendant le cycle de présentation et ne peut pas être définie directement. La hauteur d’un contrôle peut être demandée à l’aide de la [propriété HeightRequest](#heightrequest).

### [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)

La `HeightRequest` propriété est une `double` valeur qui détermine la hauteur souhaitée du contrôle. La hauteur absolue du contrôle peut ne pas correspondre à la valeur demandée. Pour plus d’informations, consultez Propriétés de la [demande](#request-properties).

### [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent)

La `InputTransparent` propriété est un `bool` qui détermine si le contrôle reçoit l’entrée d’utilisateur. La valeur par défaut est `false` , garantissant que l’élément reçoit l’entrée. Cette propriété est transférée aux éléments enfants lorsqu’elle est définie. Si vous affectez la valeur à la `InputTransparent` propriété `true` sur une classe Layout, tous les éléments de la disposition ne recevront pas d’entrée.

### [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)

La `IsEnabled` propriété est une `bool` valeur qui détermine si le contrôle réagit à l’entrée d’utilisateur. La valeur par défaut est `true`. L’affectation de la valeur false à cette propriété empêche le contrôle d’accepter l’entrée d’utilisateur.

### [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused)

La `IsFocused` propriété est une `bool` valeur qui indique si le contrôle est actuellement l’objet ayant le focus. L’appel de la [`Focus`](#focus) méthode sur le contrôle entraîne la `IsFocused` définition de la valeur sur true. L’appel de la [`Unfocus`](#unfocus) méthode affecte la valeur false à cette propriété.

### [`IsTabStop`](xref:Xamarin.Forms.VisualElement.IsTabStop)

La `IsTabStop` propriété est une `bool` valeur qui définit si le contrôle reçoit le focus lorsque l’utilisateur avance dans les contrôles à l’aide de la touche Tab. Si cette propriété a la valeur false, la `TabIndex` propriété n’a aucun effet.

### [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)

La `IsVisible` propriété est une `bool` valeur qui détermine si le contrôle est rendu. Les contrôles dont la `IsVisible` propriété a la valeur false ne seront pas affichés, ne seront pas pris en compte pour les calculs d’espace pendant le cycle de présentation et ne pourront pas accepter les entrées utilisateur.

### [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)

La `MinimumHeightRequest` propriété est une `double` valeur qui détermine comment le dépassement de capacité est géré lorsque deux éléments sont en concurrence pour l’espace limité. La définition de la `MinimumHeightRequest` propriété permet au processus de disposition de réduire l’élément à la dimension minimale demandée. Si aucun `MinimumHeightRequest` n’est spécifié, la valeur par défaut est-1 et le processus de disposition prend en compte la `HeightRequest` valeur minimale. Cela signifie que les éléments sans `MinimumHeightRequest` valeur n’auront pas de hauteur évolutive.

Pour plus d’informations, consultez [propriétés minimales des demandes](#minimum-request-properties).

### [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)

La `MinimumWidthRequest` propriété est une `double` valeur qui détermine comment le dépassement de capacité est géré lorsque deux éléments sont en concurrence pour l’espace limité. La définition de la `MinimumWidthRequest` propriété permet au processus de disposition de réduire l’élément à la dimension minimale demandée. Si aucun `MinimumWidthRequest` n’est spécifié, la valeur par défaut est-1 et le processus de disposition prend en compte la `WidthRequest` valeur minimale. Cela signifie que les éléments sans `MinimumWidthRequest` valeur n’auront pas de largeur redimensionnable.

Pour plus d’informations, consultez [propriétés minimales des demandes](#minimum-request-properties).

### [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity)

La `Opacity` propriété est une `double` valeur comprise entre zéro et un qui détermine l’opacité du contrôle pendant le rendu. La valeur par défaut de cette propriété est 1,0. Les valeurs situées en dehors de la plage comprise entre 0 et 1 seront ancrées. La `Opacity` propriété est appliquée uniquement si la [`IsVisible`](#isvisible) propriété est `true` . L’opacité est appliquée de manière itérative. Par conséquent, si un contrôle parent a une opacité de 0,5 et que son enfant a 0,5 d’opacité, l’enfant s’affiche avec une valeur d’opacité 0,25 effective. L’affectation `Opacity` de la valeur 0 à la propriété d’un contrôle d’entrée a un comportement indéfini.

### [`Parent`](xref:Xamarin.Forms.Element.Parent)

La propriété `Parent` est héritée de la classe `Element`. Cette propriété est un `Element` objet qui est le parent de Control. La `Parent` propriété est généralement définie automatiquement sur un élément lorsqu’elle est ajoutée en tant qu’enfant d’un autre élément.

### [`Resources`](xref:Xamarin.Forms.VisualElement.Resources)

La `Resources` propriété est une `ResourceDictionary` instance remplie avec des paires clé/valeur qui sont généralement remplies au moment de l’exécution à partir de XAML. Ce dictionnaire permet aux développeurs d’applications de réutiliser les objets définis en XAML à la fois au moment de la compilation et au moment de l’exécution. Les clés dans le dictionnaire sont remplies à partir de l' `x:Key` attribut de la balise XAML. L’objet créé à partir de XAML est inséré dans `ResourceDictionary` pour la clé spécifiée. une fois qu’elle a été initialisée.

Pour plus d’informations, consultez [dictionnaires de ressources](~/xamarin-forms/xaml/resource-dictionaries.md).

### [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)

La `Rotation` propriété est une `double` valeur comprise entre zéro et 360 qui définit la rotation autour de l’axe Z, en degrés. La valeur par défaut de cette propriété est 0. La rotation est appliquée par rapport [`AnchorX`](#anchorx) aux [`AnchorY`](#anchory) valeurs et.

### [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)

La `RotationX` propriété est une `double` valeur comprise entre zéro et 360 qui définit la rotation autour de l’axe X en degrés. La valeur par défaut de cette propriété est 0. La rotation est appliquée par rapport [`AnchorX`](#anchorx) aux [`AnchorY`](#anchory) valeurs et.

### [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

La `RotationY` propriété est une `double` valeur comprise entre zéro et 360 qui définit la rotation autour de l’axe Y en degrés. La valeur par défaut de cette propriété est 0. La rotation est appliquée par rapport [`AnchorX`](#anchorx) aux [`AnchorY`](#anchory) valeurs et.

### [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)

La `Scale` propriété est une `double` valeur qui définit l’échelle du contrôle. La valeur par défaut de cette propriété est 1,0. L’échelle est appliquée par rapport [`AnchorX`](#anchorx) aux [`AnchorY`](#anchory) valeurs et.

### [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX)

La `ScaleX` propriété est une `double` valeur qui définit l’échelle du contrôle le long de l’axe X. La valeur par défaut de cette propriété est 1,0. La `ScaleX` propriété est appliquée par rapport à la [`AnchorX`](#anchorx) valeur.

### [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY)

La `ScaleY` propriété est une `double` valeur qui définit l’échelle du contrôle le long de l’axe Y. La valeur par défaut de cette propriété est 1,0. La `ScaleY` propriété est appliquée par rapport à la [`AnchorY`](#anchory) valeur.

### [`Style`](xref:Xamarin.Forms.NavigableElement.Style)

La propriété `Style` est héritée de la classe `NavigableElement`. Cette propriété est une instance de la `Style` classe. La `Style` classe contient des déclencheurs, des méthodes setter et des comportements qui définissent l’apparence et le comportement des éléments visuels. Pour plus d’informations, consultez [ Xamarin.Forms styles XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)

La `StyleClass` propriété est une liste d' `string` objets qui représentent les noms des `Style` classes. Cette propriété est héritée de la classe `NavigableElement`. La `StyleClass` propriété permet l’application de plusieurs attributs de style à une `VisualElement` instance. Pour plus d’informations, consultez [ Xamarin.Forms classes de style](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

### [`TabIndex`](xref:Xamarin.Forms.VisualElement.TabIndex)

La `TabIndex` propriété est une `int` valeur qui définit l’ordre des contrôles lors de l’avancement des contrôles à l’aide de la touche Tab. La `TabIndex` propriété correspond à l’implémentation de la propriété définie sur l' `ITabStopElement` interface, que la `VisualElement` classe implémente.

### [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)

La `TranslationX` propriété est une `double` valeur qui définit la translation Delta à appliquer sur l’axe X. La traduction est appliquée après la disposition et est généralement utilisée pour appliquer des animations. Traduction d’un élément en dehors des limites de son conteneur parent mon empêchent les entrées de fonctionner.

Pour plus d’informations, consultez [animation Xamarin.Forms dans ](~/xamarin-forms/user-interface/animation/index.md).

### [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)

La `TranslationY` propriété est une `double` valeur qui définit la translation Delta à appliquer sur l’axe Y. La traduction est appliquée après la disposition et est généralement utilisée pour appliquer des animations. Traduction d’un élément en dehors des limites de son conteneur parent mon empêchent les entrées de fonctionner.

Pour plus d’informations, consultez [animation Xamarin.Forms dans ](~/xamarin-forms/user-interface/animation/index.md).

### [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)

La `Triggers` propriété est un en lecture seule `List` d' `TriggerBase` objets. Les déclencheurs permettent aux développeurs d’applications d’exprimer des actions en XAML qui modifient l’apparence visuelle des contrôles en réponse à des modifications d’événement ou de propriété. Pour plus d’informations, consultez [ Xamarin.Forms déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md).

### [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)

La `Visual` propriété est une `IVisual` instance qui permet de créer des convertisseurs et de les appliquer de manière sélective aux `VisualElement` instances. La `Visual` propriété est définie pour correspondre à son parent. ainsi, la définition d’un convertisseur sur un composant s’appliquera également à tous les enfants de ce composant. Si aucun convertisseur personnalisé n’est défini sur un contrôle ou ses ancêtres, le convertisseur par défaut Xamarin.Forms sera utilisé. Pour plus d’informations, consultez [ Xamarin.Forms Visual](~/xamarin-forms/user-interface/visual/index.md).

### [`Width`](xref:Xamarin.Forms.VisualElement.Width)

La `Width` propriété est une valeur en lecture seule `double` qui décrit la largeur rendue du contrôle. La `Width` propriété est calculée pendant le cycle de présentation et ne peut pas être définie directement. La largeur d’un contrôle peut être demandée à l’aide de la [propriété WidthRequest](#widthrequest).

### [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)

La `WidthRequest` propriété est une `double` valeur qui détermine la largeur souhaitée du contrôle. La largeur absolue du contrôle peut ne pas correspondre à la valeur demandée. Pour plus d’informations, consultez Propriétés de la [demande](#request-properties).

### [`X`](xref:Xamarin.Forms.VisualElement.X)

La `X` propriété est une valeur en lecture seule `double` qui décrit la position X actuelle du contrôle.

### [`Y`](xref:Xamarin.Forms.VisualElement.Y)

La `Y` propriété est une valeur en lecture seule `double` qui décrit la position Y actuelle du contrôle.

## <a name="methods"></a>Méthodes

Les méthodes suivantes sont disponibles sur la `VisualElement` classe. Pour obtenir une liste complète, consultez Méthodes de l' [API VisualElement](xref:Xamarin.Forms.VisualElement#methods).

### [`FindByName`](xref:Xamarin.Forms.Element.FindByName*)

La `FindByName` méthode est héritée de la `Element` classe et possède la signature suivante :

```csharp
public object FindByName (string name)
```

Cette méthode recherche tous les éléments enfants pour l' `name` argument fourni et retourne l’élément qui porte le nom spécifié. Si aucune correspondance n'est trouvée, retourne la valeur `null`.

### [`Focus`](xref:Xamarin.Forms.VisualElement.Focus)

La `Focus` méthode tente de définir le focus sur l’élément. Cette méthode a la signature suivante :

```csharp
public bool Focus ()
```

La `Focus` méthode retourne `true` si le focus clavier a été correctement défini et `false` si l’appel de méthode n’a pas abouti à un changement de focus. L’élément doit être en mesure de recevoir le focus pour que cette méthode fonctionne. L’appel `Focus` de la méthode sur des éléments hors écran ou non réalisés a un comportement indéfini.

### [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus)

La `Unfocus` méthode tente de supprimer le focus sur l’élément. Cette méthode a la signature suivante :

```csharp
public void Unfocus ()
```

L’élément doit déjà avoir le focus pour que cette méthode fonctionne.

## <a name="events"></a>Événements

Les événements suivants sont disponibles sur la `VisualElement` classe. Pour obtenir une liste complète, consultez [ Xamarin.Forms événements VisualElement](xref:Xamarin.Forms.VisualElement#events).

### [`Focused`](xref:Xamarin.Forms.VisualElement.Focused)

L' `Focused` événement est déclenché chaque fois que l' `VisualElement` instance reçoit le focus. Cet événement n’est pas propagé via la Xamarin.Forms pile, il est reçu directement à partir du contrôle natif. Cet événement est émis par l' [`IsFocused`](#isfocused) accesseur Set de propriété.

### [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

L' `SizeChanged` événement est déclenché chaque fois que l' `VisualElement` instance `Height` ou les `Width` propriétés changent. Si les développeurs souhaitent répondre directement à la modification de taille, au lieu de répondre à l’événement après modification, ils doivent implémenter la [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated*) méthode virtuelle à la place.

### [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)

L' `Unfocused` événement est déclenché chaque fois que l' `VisualElement` instance perd le focus. Cet événement n’est pas propagé via la Xamarin.Forms pile, il est reçu directement à partir du contrôle natif. Cet événement est émis par l' [`IsFocused`](#isfocused) accesseur Set de propriété.

## <a name="units-of-measurement"></a>Unités de mesure

Les plateformes Android, iOS et UWP ont toutes des unités de mesure différentes qui peuvent varier d’un appareil à l’autre. Xamarin.Forms utilise une unité de mesure indépendante de la plateforme qui normalise les unités sur les appareils et les plateformes. Il y a 160 unités par pouce, ou 64 unités par centimètre, dans Xamarin.Forms .

## <a name="request-properties"></a>Propriétés de requête

Les propriétés dont le nom contient « Request » définissent une valeur souhaitée, qui peut ne pas correspondre à la valeur affichée réelle. Par exemple, `HeightRequest` peut avoir la valeur 150, mais si la disposition autorise uniquement de l’espace pour 100 unités, le rendu `Height` du contrôle sera uniquement 100. La taille rendue est affectée par l’espace disponible et les composants contenus.

## <a name="minimum-request-properties"></a>Propriétés minimales de la demande

Les propriétés de demande minimales incluent `MinimumHeightRequest` et `MinimumWidthRequest` , et sont destinées à permettre un contrôle plus précis sur la façon dont les éléments gèrent le dépassement de capacité les uns par rapport aux autres. Toutefois, le comportement de disposition lié à ces propriétés présente des considérations importantes.

### <a name="unspecified-minimum-property-values"></a>Valeurs de propriété minimales non spécifiées

Si une valeur minimale n’est pas définie, la propriété minimale prend par défaut la valeur-1. Le processus de disposition ignore cette valeur et considère que la valeur absolue est la valeur minimale. La conséquence pratique de ce comportement est qu’un élément sans valeur minimale spécifiée **ne sera pas** réduit. Un élément avec une valeur minimale spécifiée **est** réduit.

Le code XAML suivant montre deux `BoxView` éléments dans un horizontal `StackLayout` :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView HeightRequest="100" BackgroundColor="Purple" WidthRequest="500"></BoxView>
    <BoxView HeightRequest="100" BackgroundColor="Green" WidthRequest="500" MinimumWidthRequest="250"></BoxView>
</StackLayout>
```

La première `BoxView` instance demande une largeur de 500 et ne spécifie pas de largeur minimale. La deuxième `BoxView` instance demande une largeur de 500 et une largeur minimale de 250. Si l' `StackLayout` élément parent n’est pas suffisamment grand pour contenir les deux composants à la largeur demandée, la première `BoxView` instance est considérée par le processus de disposition pour avoir une largeur minimale de 500 car aucun autre minimum valide n’est spécifié. La deuxième `BoxView` instance est autorisée à réduire le nombre de 250 et elle sera réduite pour s’ajuster jusqu’à ce que sa largeur atteigne 250 unités.

Si le comportement souhaité est que la première `BoxView` instance est mise à l’échelle sans largeur minimale, `MinimumWidthRequest` doit être défini sur une valeur valide, telle que 0.

### <a name="minimum-and-absolute-property-values"></a>Valeurs de propriété minimale et absolue

Le comportement n’est pas défini lorsque la valeur minimale est supérieure à la valeur absolue. Par exemple, si `WidthRequest` a la valeur 100, la `MinimumWidthRequest` propriété ne doit jamais dépasser 100. Lorsque vous spécifiez une valeur de propriété minimale, vous devez toujours spécifier une valeur absolue pour garantir que la valeur absolue est supérieure à la valeur minimale.

### <a name="minimum-properties-within-a-grid"></a>Propriétés minimales dans une grille

`Grid` les dispositions disposent de leur propre système pour le dimensionnement relatif des lignes et des colonnes. L’utilisation de `MinimumWidthRequest` ou `MinimumHeightRequest` dans une `Grid` disposition n’aura pas d’effet. Pour plus d’informations, consultez [ Xamarin.Forms Grid](~/xamarin-forms/user-interface/layouts/grid.md).

## <a name="related-links"></a>Liens connexes

- [API VisualElement](xref:Xamarin.Forms.VisualElement)

---
title: Résumé du chapitre 11. Infrastructure Bindable
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 11. Infrastructure Bindable'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334368"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Résumé du chapitre 11. Infrastructure Bindable

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Chaque C# programmeur est familiarisé C# avec les *Propriétés*. Les propriétés contiennent un accesseur *Set* et/ou un accesseur *Get* . Elles sont souvent appelées *Propriétés CLR* pour le Common Language Runtime.

Xamarin. Forms définit une définition de propriété améliorée appelée une *propriété pouvant être liée* , encapsulée par la classe [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) et prise en charge par la classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) . Ces classes sont liées, mais assez distinctes : la `BindableProperty` est utilisée pour définir la propriété elle-même ; `BindableObject` est semblable à `object` dans le fait qu’il s’agit d’une classe de base pour les classes qui définissent des propriétés pouvant être liées.

## <a name="the-xamarinforms-class-hierarchy"></a>La hiérarchie de classes de Xamarin.Forms

L’exemple [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) utilise la réflexion pour afficher une hiérarchie de classes de Xamarin. Forms et illustre le rôle crucial joué par `BindableObject` dans cette hiérarchie. `BindableObject` dérive de `Object` et est la classe parente à [`Element`](xref:Xamarin.Forms.Element) duquel [`VisualElement`](xref:Xamarin.Forms.VisualElement) dérive. Il s’agit de la classe parente à [`Page`](xref:Xamarin.Forms.Page) et [`View`](xref:Xamarin.Forms.View), qui est la classe parente à [`Layout`](xref:Xamarin.Forms.Layout):

[![Capture d’écran triple du partage des hiérarchies de classes](images/ch11fg01-small.png "Partage des hiérarchies de classes")](images/ch11fg01-large.png#lightbox "Partage des hiérarchies de classes")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un coup de œil dans BindableObject et BindableProperty

Dans les classes qui dérivent de `BindableObject` de nombreuses propriétés CLR sont dites « sauvegardées par » des propriétés pouvant être liées. Par exemple, la propriété [`Text`](xref:Xamarin.Forms.Label.Text) de la classe `Label` est une propriété CLR, mais la classe `Label` définit également un champ statique public en lecture seule nommé [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) de type `BindableProperty`.

Une application peut définir ou obtenir la propriété `Text` de `Label` normalement, ou l’application peut définir le `Text` en appelant la méthode [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) définie par `BindableObject` avec un argument `Label.TextProperty`. De même, une application peut obtenir la valeur de la propriété `Text` en appelant la méthode [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) , à nouveau avec un argument `Label.TextProperty`. Cela est illustré par l’exemple [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) .

En effet, la propriété CLR `Text` est entièrement implémentée à l’aide des méthodes `SetValue` et `GetValue` définies par `BindableObject` conjointement avec la propriété statique `Label.TextProperty`.

`BindableObject` et `BindableProperty` prennent en charge :

- Ce qui donne les valeurs de propriétés par défaut
- Stockage de leurs valeurs actuelles
- Offrant des mécanismes de valider les valeurs de propriété
- Maintenir la cohérence entre les propriétés associées dans une classe unique
- Répondre aux modifications de propriétés
- Déclencher des notifications lorsqu’une propriété est sur le point de changer ou a changé
- Prise en charge de la liaison de données
- Prise en charge des styles
- Prise en charge des ressources dynamiques

Chaque fois qu’une propriété qui est stockée par une propriété pouvant être liée est modifiée, `BindableObject` déclenche un événement [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) identifiant la propriété qui a changé. Cet événement n’est pas déclenché lorsque la propriété est définie sur la même valeur.

Certaines propriétés ne sont pas sauvegardées par des propriétés pouvant être liées, et certaines classes Xamarin. Forms &mdash; telles que `Span` &mdash; ne dérivent pas de `BindableObject`. Seule une classe qui dérive de `BindableObject` peut prendre en charge des propriétés pouvant être liées, car `BindableObject` définit les méthodes `SetValue` et `GetValue`.

Étant donné que `Span` ne dérive pas de `BindableObject`, aucune de ses propriétés &mdash; telles que `Text` &mdash; n’est sauvegardée par une propriété pouvant être liée. C’est la raison pour laquelle un paramètre `DynamicResource` sur la propriété `Text` de `Span` lève une exception dans l’exemple [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) dans le chapitre précédent. L’exemple [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) montre comment définir des ressources dynamiques dans le code à l’aide de la méthode [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) définie par `Element`. Le premier argument est un objet de type `BindableProperty`.

De même, la méthode [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) définie par `BindableObject` a un premier argument de type `BindableProperty`.

## <a name="defining-bindable-properties"></a>Définition des propriétés pouvant être liées

Vous pouvez définir vos propres propriétés pouvant être liées à l’aide de la méthode statique [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) pour créer un champ statique en lecture seule de type `BindableProperty`.

Cela est illustré dans la classe [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . La classe dérive de `Label` et vous permet de spécifier une taille de police en points. Il est illustré dans l’exemple [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) .

Quatre arguments de la méthode `BindableProperty.Create` sont requis :

- `propertyName`: nom de texte de la propriété (identique au nom de la propriété CLR)
- `returnType`: type de la propriété CLR
- `declaringType`: type de la classe qui déclare la propriété.
- `defaultValue`: valeur par défaut de la propriété

Étant donné que `defaultValue` est de type `object`, le compilateur doit être en mesure de déterminer le type de la valeur par défaut. Par exemple, si le `returnType` est `double`, la `defaultValue` doit être définie sur une valeur telle que 0,0 plutôt que sur 0, ou l’incompatibilité de type déclenchera une exception au moment de l’exécution.

Il est également très courant pour une propriété pouvant être liée à inclure :

- `propertyChanged`: méthode statique appelée lorsque la propriété change de valeur. Le premier argument est l’instance de la classe dont la propriété a été modifiée.

Les autres arguments de `BindableProperty.Create` ne sont pas aussi courants :

- `defaultBindingMode`: utilisé dans le cadre de la liaison de données (comme indiqué dans le [**chapitre 16. Liaison de données**](chapter16.md))
- `validateValue`: rappel pour vérifier si une valeur est valide.
- `propertyChanging`: rappel indiquant quand la propriété va être modifiée.
- `coerceValue`: rappel permettant de forcer une valeur définie sur une autre valeur
- `defaultValueCreate`: rappel permettant de créer une valeur par défaut qui ne peut pas être partagée entre des instances de la classe (par exemple, une collection)

### <a name="the-read-only-bindable-property"></a>La propriété peut être liée en lecture seule

Une propriété peut être en lecture seule. La création d’une propriété pouvant être liée en lecture seule nécessite l’appel de la méthode statique [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) pour définir un champ statique privé en lecture seule de type [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey).

Ensuite, définissez la propriété CLR `set` accesor comme `private` pour appeler une surcharge [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) avec l’objet `BindablePropertyKey`. Ainsi, la propriété est définie en dehors de la classe.

Cela est illustré dans la classe [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) utilisée dans l’exemple [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 11 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Exemples du chapitre 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md)

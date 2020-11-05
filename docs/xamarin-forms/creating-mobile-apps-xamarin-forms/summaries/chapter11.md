---
title: Résumé du chapitre 11. L’infrastructure pouvant être liée
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 11. L’infrastructure pouvant être liée'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2858d0606cf9c5c97a3457b5b29f620e7da2bad
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375133"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Résumé du chapitre 11. L’infrastructure pouvant être liée

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

Chaque programmeur C# est familiarisé avec les *Propriétés* c#. Les propriétés contiennent un accesseur *Set* et/ou un accesseur *Get* . Elles sont souvent appelées *Propriétés CLR* pour le Common Language Runtime.

Xamarin.Forms définit une définition de propriété améliorée appelée une *propriété pouvant être liée* , encapsulée par la [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) classe et prise en charge par la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Ces classes sont liées, mais assez distinctes : le `BindableProperty` est utilisé pour définir la propriété elle-même ; `BindableObject` est comme s' `object` il s’agissait d’une classe de base pour les classes qui définissent des propriétés pouvant être liées.

## <a name="the-no-locxamarinforms-class-hierarchy"></a>Xamarin.FormsHiérarchie de classes

L’exemple [**ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) utilise la réflexion pour afficher une hiérarchie de classes Xamarin.Forms et présenter le rôle crucial joué par `BindableObject` dans cette hiérarchie. `BindableObject` dérive de `Object` et est la classe parente à [`Element`](xref:Xamarin.Forms.Element) partir de laquelle [`VisualElement`](xref:Xamarin.Forms.VisualElement) dérive. Il s’agit de la classe parente de [`Page`](xref:Xamarin.Forms.Page) et [`View`](xref:Xamarin.Forms.View) , qui est la classe parente pour [`Layout`](xref:Xamarin.Forms.Layout) :

[![Capture d’écran triple du partage des hiérarchies de classes](images/ch11fg01-small.png "Partage des hiérarchies de classes")](images/ch11fg01-large.png#lightbox "Partage des hiérarchies de classes")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Aperçu de BindableObject et BindableProperty

Dans les classes qui dérivent de `BindableObject` nombreuses propriétés CLR, on parle de « stockage des propriétés pouvant être liées ». Par exemple, la [`Text`](xref:Xamarin.Forms.Label.Text) propriété de la `Label` classe est une propriété CLR, mais la `Label` classe définit également un champ statique public en lecture seule nommé [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) de type `BindableProperty` .

Une application peut définir ou recevoir la `Text` propriété de `Label` normalement, ou l’application peut définir le `Text` en appelant la [ `SetValue` ] (XREF : Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . BindableProperty, System. Object)) définie par `BindableObject` avec un `Label.TextProperty` argument. De même, une application peut obtenir la valeur de la `Text` propriété en appelant la [ `GetValue` ] (XREF : Xamarin.Forms . BindableObject. GetValue ( Xamarin.Forms . BindableProperty)), avec un `Label.TextProperty` argument. Cela est illustré par l’exemple [**PropertySettings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings) .

En effet, la `Text` propriété CLR est entièrement implémentée à l’aide des `SetValue` `GetValue` méthodes et définies par `BindableObject` conjointement à la `Label.TextProperty` propriété statique.

`BindableObject` et `BindableProperty` prennent en charge les éléments suivants :

- Attribution de valeurs par défaut aux propriétés
- Stockage de leurs valeurs actuelles
- Fournir des mécanismes pour valider les valeurs de propriété
- Maintien de la cohérence entre les propriétés associées dans une classe unique
- Réponse aux modifications de propriétés
- Déclenchement de notifications lorsqu’une propriété est sur le ou en modification
- Prise en charge de la liaison de données
- Styles de prise en charge
- Prise en charge des ressources dynamiques

Chaque fois qu’une propriété qui est stockée par une propriété pouvant être liée change, `BindableObject` déclenche un [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) événement identifiant la propriété qui a changé. Cet événement n’est pas déclenché lorsque la propriété est définie sur la même valeur.

Certaines propriétés ne sont pas sauvegardées par des propriétés pouvant être liées, et certaines Xamarin.Forms classes &mdash; telles que `Span` &mdash; ne dérivent pas de `BindableObject` . Seule une classe qui dérive de `BindableObject` peut prendre en charge des propriétés pouvant être liées, car `BindableObject` définit les `SetValue` `GetValue` méthodes et.

Étant donné que `Span` ne dérive pas de `BindableObject` , aucune de ses propriétés, &mdash; telles que, n' `Text` &mdash; est stockée par une propriété pouvant être liée. C’est la raison pour laquelle un `DynamicResource` paramètre de la `Text` propriété de `Span` lève une exception dans l’exemple [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) dans le chapitre précédent. L’exemple [**DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) montre comment définir des ressources dynamiques dans le code à l’aide de [ `SetDynamicResource` ] (XREF : Xamarin.Forms . Élément. SetDynamicResource ( Xamarin.Forms . BindableProperty, System. String)) définie par `Element` . Le premier argument est un objet de type `BindableProperty` .

De même, le [ `SetBinding` ] (XREF : Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)). la méthode définie par `BindableObject` a un premier argument de type `BindableProperty` .

## <a name="defining-bindable-properties"></a>Définition des propriétés pouvant être liées

Vous pouvez définir vos propres propriétés pouvant être liées à l’aide de la propriété statique [ `BindableProperty.Create` ] (XREF : Xamarin.Forms . BindableProperty. Create (System. String, System. type, System. type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) pour créer un champ statique en lecture seule de type `BindableProperty` .

Cela est illustré dans la [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) classe de la bibliothèque [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . La classe dérive de `Label` et vous permet de spécifier une taille de police en points. Il est illustré dans l’exemple [**PointSizedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText) .

Quatre arguments de la `BindableProperty.Create` méthode sont requis :

- `propertyName`: nom de texte de la propriété (identique au nom de la propriété CLR)
- `returnType`: le type de la propriété CLR
- `declaringType`: le type de la classe qui déclare la propriété.
- `defaultValue`: la valeur par défaut de la propriété

Étant donné que `defaultValue` est de type `object` , le compilateur doit être en mesure de déterminer le type de la valeur par défaut. Par exemple, si `returnType` est `double` , `defaultValue` doit avoir la valeur 0,0 plutôt que 0, sinon l’incompatibilité de type déclenchera une exception au moment de l’exécution.

Il est également très courant pour une propriété pouvant être liée d’inclure les éléments suivants :

- `propertyChanged`: méthode statique appelée lorsque la propriété change de valeur. Le premier argument est l’instance de la classe dont la propriété a été modifiée.

Les autres arguments de `BindableProperty.Create` ne sont pas aussi courants :

- `defaultBindingMode`: utilisé dans le cadre de la liaison de données (comme indiqué dans le [**chapitre 16. Liaison de données**](chapter16.md))
- `validateValue`: rappel pour vérifier si une valeur est valide.
- `propertyChanging`: rappel permettant d’indiquer le moment où la propriété va être modifiée.
- `coerceValue`: rappel permettant de forcer une valeur définie sur une autre valeur
- `defaultValueCreate`: un rappel pour créer une valeur par défaut qui ne peut pas être partagée entre des instances de la classe (par exemple, une collection)

### <a name="the-read-only-bindable-property"></a>Propriété pouvant être liée en lecture seule

Une propriété pouvant être liée peut être en lecture seule. La création d’une propriété pouvant être liée en lecture seule nécessite l’appel de la méthode statique [ `BindableProperty.CreateReadOnly` ] (XREF : Xamarin.Forms . BindableProperty. CreateReadOnly (System. String, System. type, System. type, System. Object, Xamarin.Forms . BindingMode, Xamarin.Forms . BindableProperty. ValidateValueDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangedDelegate, Xamarin.Forms . BindableProperty. BindingPropertyChangingDelegate, Xamarin.Forms . BindableProperty. CoerceValueDelegate, Xamarin.Forms . BindableProperty. CreateDefaultValueDelegate)) pour définir un champ statique privé en lecture seule de type [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey) .

Ensuite, définissez la propriété CLR `set` accesor comme `private` pour appeler un [ `SetValue` ] (XREF : Xamarin.Forms . BindableObject. SetValue ( Xamarin.Forms . BindablePropertyKey, System. Object)) est surchargé avec l' `BindablePropertyKey` objet. Cela empêche la définition de la propriété en dehors de la classe.

Cela est illustré dans la [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) classe utilisée dans l’exemple  [**BaskervillesCount**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 11 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Exemples du chapitre 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriétés pouvant être liées](~/xamarin-forms/xaml/bindable-properties.md)

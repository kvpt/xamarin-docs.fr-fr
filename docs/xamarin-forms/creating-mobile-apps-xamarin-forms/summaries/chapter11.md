---
title: Résumé du chapitre 11. L’infrastructure bindable
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 11. L’infrastructure bindable'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 34671C48-0ED4-4B76-A33D-D6505390DC5B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: f9e3326c0f55469cfa84a019a674679d82dfc007
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334368"
---
# <a name="summary-of-chapter-11-the-bindable-infrastructure"></a>Résumé du chapitre 11. L’infrastructure bindable

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)

Chaque programmeur CMD est familier avec les *propriétés*de C. Les propriétés contiennent un accesseur *défini* et/ou un accesseur *d’accès.* Ils sont souvent appelés *propriétés CLR* pour le temps de course de langue commune.

Xamarin.Forms définit une définition de propriété améliorée appelée *propriété liant* encapsulée par la [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) classe et soutenue par la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe. Ces classes sont liées mais `BindableProperty` tout à fait distinctes: Le est utilisé pour définir la propriété elle-même; `BindableObject` c’est comme `object` en ce qu’il s’agit d’une classe de base pour les classes qui définissent les propriétés liantes.

## <a name="the-xamarinforms-class-hierarchy"></a>La hiérarchie de classe Xamarin.Forms

[**L’échantillon De ClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/ClassHierarchy) utilise la réflexion pour afficher une hiérarchie de `BindableObject` classe de Xamarin.Forms et démontrer le rôle crucial joué dans cette hiérarchie. `BindableObject`dérive et `Object` est la classe [`Element`](xref:Xamarin.Forms.Element) mère [`VisualElement`](xref:Xamarin.Forms.VisualElement) à laquelle dérive. C’est la [`Page`](xref:Xamarin.Forms.Page) classe [`View`](xref:Xamarin.Forms.View)parent à et [`Layout`](xref:Xamarin.Forms.Layout), qui est la classe parente à:

[![Triple capture d’écran du partage de hiérarchie de classe](images/ch11fg01-small.png "Partage de hiérarchie de classe")](images/ch11fg01-large.png#lightbox "Partage de hiérarchie de classe")

## <a name="a-peek-into-bindableobject-and-bindableproperty"></a>Un coup d’oeil dans BindableObject et BindableProperty

Dans les classes `BindableObject` qui dérivent de nombreuses propriétés CLR sont dit être "soutenu par" propriétés liantes. Par exemple, [`Text`](xref:Xamarin.Forms.Label.Text) la `Label` propriété de la classe est `Label` une propriété CLR, mais la [`TextProperty`](xref:Xamarin.Forms.Label.TextProperty) classe `BindableProperty`définit également un champ de lecture statique publique seulement nommé de type .

Une application peut définir `Text` ou `Label` obtenir la propriété de `Text` normalement, [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) ou l’application peut définir le en appelant la méthode définie par `BindableObject` un `Label.TextProperty` argument. De même, une demande peut `Text` obtenir la [`GetValue`](xref:Xamarin.Forms.BindableObject.GetValue(Xamarin.Forms.BindableProperty)) valeur de `Label.TextProperty` la propriété en appelant la méthode, encore une fois avec un argument. Ceci est démontré par l’échantillon [**de PropertySettings.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PropertySettings)

En `Text` effet, la propriété CLR `SetValue` `GetValue` est entièrement mise en œuvre en utilisant le et les méthodes définies par `BindableObject` en conjonction avec la `Label.TextProperty` propriété statique.

`BindableObject`et `BindableProperty` fournir un soutien pour :

- Donner des valeurs par défaut aux propriétés
- Stockage de leurs valeurs actuelles
- Fournir des mécanismes de validation de la valeur des propriétés
- Maintenir la cohérence entre les propriétés connexes dans une seule catégorie
- Répondre aux changements de propriété
- Déclenchement des notifications lorsqu’une propriété est sur le point de changer ou a changé
- Soutenir la liaison des données
- Styles de soutien
- Soutenir les ressources dynamiques

Chaque fois qu’une propriété qui est `BindableObject` soutenue [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) par une propriété liant change, les incendies d’un événement identifiant la propriété qui a changé. Cet événement n’est pas déclenché lorsque la propriété est réglée à la même valeur.

Certaines propriétés ne sont pas soutenues par des &mdash; propriétés `Span` &mdash; liantes, `BindableObject`et certaines classes Xamarin.Forms telles que ne dérivent pas de . Seule une classe qui `BindableObject` dérive de peut `BindableObject` prendre `SetValue` en `GetValue` charge les propriétés liantes parce que définit le et les méthodes.

Parce `Span` que ne `BindableObject`dérive pas &mdash; de `Text` &mdash; , aucune de ses propriétés telles que sont soutenus par une propriété liant. C’est `DynamicResource` pourquoi un `Text` paramètre sur la propriété de `Span` soulève une exception dans l’échantillon [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) dans le chapitre précédent. [**L’échantillon DynamicVsStaticCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/DynamicVsStaticCode) montre comment définir une ressource [`SetDynamicResource`](xref:Xamarin.Forms.Element.SetDynamicResource(Xamarin.Forms.BindableProperty,System.String)) dynamique `Element`dans le code en utilisant la méthode définie par . Le premier argument est `BindableProperty`un objet de type .

De même, [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) la `BindableObject` méthode définie par `BindableProperty`a un premier argument de type .

## <a name="defining-bindable-properties"></a>Définir les propriétés liantes

Vous pouvez définir vos propres propriétés liantes en utilisant la méthode statique [`BindableProperty.Create`](xref:Xamarin.Forms.BindableProperty.Create(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) pour créer un champ statique de type `BindableProperty`.

Ceci est démontré [`AltLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AltLabel.cs) dans la classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) La classe dérive `Label` et vous permet de spécifier une taille de police en points. Il est démontré dans l’échantillon [**PointSizedText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/PointSizedText)

Quatre arguments `BindableProperty.Create` de la méthode sont nécessaires :

- `propertyName`: le nom de texte de la propriété (le même que le nom de propriété CLR)
- `returnType`: le type de propriété CLR
- `declaringType`: le type de classe déclarant la propriété
- `defaultValue`: valeur par défaut de la propriété

Parce `defaultValue` que `object`c’est du type, le compilateur doit être en mesure de déterminer le type de valeur par défaut. Par exemple, `returnType` si `double`l’est , le `defaultValue` devrait être réglé à quelque chose comme 0.0 plutôt que juste 0, ou l’inadéquation de type déclenchera une exception au moment de l’exécution.

Il est également très fréquent pour une propriété liant d’inclure:

- `propertyChanged`: une méthode statique appelée lorsque la propriété change de valeur. Le premier argument est le cas de la classe dont la propriété a été modifiée.

Les autres `BindableProperty.Create` arguments à ne pas être aussi communs:

- `defaultBindingMode`: utilisé dans le cadre de la liaison des données (tel que discuté au [**chapitre 16. Liaison de données**](chapter16.md))
- `validateValue`: un rappel pour vérifier une valeur valide
- `propertyChanging`: un rappel pour indiquer quand la propriété est sur le point de changer
- `coerceValue`: un rappel pour contraindre une valeur fixe à une autre valeur
- `defaultValueCreate`: un rappel pour créer une valeur par défaut qui ne peut pas être partagée entre les instances de la classe (par exemple, une collection)

### <a name="the-read-only-bindable-property"></a>La propriété liant lisible

Une propriété liant peut être lue uniquement. La création d’une propriété liant [`BindableProperty.CreateReadOnly`](xref:Xamarin.Forms.BindableProperty.CreateReadOnly(System.String,System.Type,System.Type,System.Object,Xamarin.Forms.BindingMode,Xamarin.Forms.BindableProperty.ValidateValueDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangedDelegate,Xamarin.Forms.BindableProperty.BindingPropertyChangingDelegate,Xamarin.Forms.BindableProperty.CoerceValueDelegate,Xamarin.Forms.BindableProperty.CreateDefaultValueDelegate)) lisible uniquement en lecture nécessite [`BindablePropertyKey`](xref:Xamarin.Forms.BindablePropertyKey)d’appeler la méthode statique pour définir un champ de type de type statique et statique.

Ensuite, définissez l’accesor de `set` propriété CLR quant `private` à appeler une [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindablePropertyKey,System.Object)) surcharge avec l’objet. `BindablePropertyKey` Cela empêche la propriété d’être placé en dehors de la classe.

Ceci est démontré [`CountedLabel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CountedLabel.cs) dans la classe utilisée dans l’échantillon [**BaskervillesCount.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11/BaskervillesCount)

## <a name="related-links"></a>Liens connexes

- [Chapitre 11 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch11-Apr2016.pdf)
- [Échantillons du chapitre 11](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter11)
- [Propriétés liants](~/xamarin-forms/xaml/bindable-properties.md)

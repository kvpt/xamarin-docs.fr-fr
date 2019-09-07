---
title: Résumé du chapitre 23. Déclencheurs et comportements
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 23. Déclencheurs et comportements'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760595"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Résumé du chapitre 23. Déclencheurs et comportements

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Déclencheurs et comportements sont similaires, car ils sont tous les deux destinés à être utilisé dans les fichiers XAML pour simplifier les interactions d’élément au-delà de l’utilisation des liaisons de données et pour étendre les fonctionnalités des éléments XAML. Déclencheurs et comportements sont presque toujours utilisés avec les objets d’interface utilisateur visual.

Pour prendre en charge les déclencheurs et les comportements, les deux `VisualElement` et `Style` prennent en charge les deux propriétés de collection :

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) et [ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers) de type `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) et [ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors) de type `IList<Behavior>`

## <a name="triggers"></a>Déclencheurs

Un déclencheur est une condition (une modification de propriété ou le déclenchement d’un événement) qui génère une réponse (un autre changement de propriété ou en cours d’exécution du code). Le `Triggers` propriété du `VisualElement` et `Style` est de type `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) est une classe abstraite dont dérivent les classes sealed quatre :

- [`Trigger`](xref:Xamarin.Forms.Trigger) pour les réponses en fonction des modifications de propriété
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) pour les réponses en fonction de déclenchement d’événement
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) pour les réponses en fonction des liaisons de données
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) pour les réponses selon plusieurs déclencheurs

Le déclencheur est toujours défini sur l’élément dont la propriété est en cours de modification par le déclencheur.

### <a name="the-simplest-trigger"></a>Le déclencheur de la plus simple

Le [ `Trigger` ](xref:Xamarin.Forms.Trigger) recherche un changement d’une valeur de propriété de classe et répond en définissant une autre propriété du même élément.

`Trigger` définit trois propriétés :

- [`Property`](xref:Xamarin.Forms.Trigger.Property) de type `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) de type `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) de type `IList<SetterBase>`, la propriété de contenu de `Trigger`

En outre, `Trigger` nécessite que la propriété suivante héritée `TriggerBase` être définies :

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) pour indiquer le type de l’élément sur lequel le `Trigger` est attaché

Le `Property` et `Value` constituent la condition et le `Setters` collection est la réponse. Lorsque l’indiquée `Property` a la valeur indiquée par `Value`, le `Setter` des objets dans le `Setters` collection sont appliquées. Lorsque le `Property` a une valeur différente, les accesseurs Set sont supprimés. `Setter` définit deux propriétés qui sont les mêmes que les deux premières propriétés de `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) de type `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) de type `Object`

Le [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) exemple montre comment un `Trigger` appliqué à un `Entry` peut augmenter la taille de la `Entry` via la `Scale` propriété lorsque la `IsFocused`propriété de la `Entry` est `true`.

Bien qu’il n’est pas courant, la `Trigger` peut être défini dans le code, comme le [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) montre des exemples.

Le [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) exemple montre comment la `Trigger` peuvent être définies dans un `Style` à appliquer à plusieurs `Entry` éléments.

### <a name="trigger-actions-and-animations"></a>Déclencher des actions et les animations

Il est également possible d’exécuter un peu de code basé sur un déclencheur. Ce code peut être une animation qui cible une propriété. Une façon courante consiste à utiliser un [ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger), qui définit deux propriétés :

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) de type `string`, le nom d’un événement
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) de type `IList<TriggerAction>`, une liste d’actions à exécuter en réponse.

Pour l’utiliser, vous devez écrire une classe qui dérive de [ `TriggerAction<T>` ](xref:Xamarin.Forms.TriggerAction`1), généralement `TriggerAction<VisualElement>`. Vous pouvez définir les propriétés de cette classe. Il s’agit des propriétés CLR normales plutôt que des propriétés pouvant être liées, car `TriggerAction` ne dérive pas de `BindableObject`. Vous devez substituer la [ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*) méthode est appelée lorsque l’action est appelée. L’argument est l’élément cible.

Le [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque est un exemple. Il appelle le `ScaleTo` propriété à animer le `Scale` propriété d’un élément. Étant donné qu’un de ses propriétés est de type `Easing`, le [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe vous permet d’utiliser la norme `Easing` champs statiques dans XAML.

Le [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) exemple montre comment appeler le `ScaleAction` à partir de `EventTrigger` objets qui surveille le `Focused` et `Unfocused` événements.

Le [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) exemple montre comment définir une fonction d’accélération personnalisée pour `ScaleAction` dans un fichier code-behind.

Vous pouvez également appeler des actions à l’aide un `Trigger` (distinguished de `EventTrigger`). Cela nécessite que vous êtes conscient que `TriggerBase` définit deux collections :

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) de type `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) de type `IList<TriggerAction>`

Le [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) exemple montre comment utiliser ces collections.

### <a name="more-event-triggers"></a>Plusieurs déclencheurs d’événements

Le [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) appels à des bibliothèques `ScaleTo` à deux reprises pour descendre en puissance. Le [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) utilise cela dans un style `EventTrigger` pour fournir des commentaires visuels lorsqu’un `Button` est enfoncé. Cette animation double est également possible à l’aide de deux actions dans la collection de type [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

Le [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe dans le **Xamarin.FormsBook.Toolkit** bibliothèque définit une action shiver personnalisable. Le [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) illustre cela.

Le [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe dans le **Xamarin.FormsBook.Toolkit** bibliothèque est limitée à `Entry` éléments et des jeux le `TextColor` propriété if rouge le `Text` propriété n’est pas un `double`. Le [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) illustre cela.

### <a name="data-triggers"></a>Déclencheurs de données

Le [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) est similaire à la `Trigger` , sauf qu’au lieu de la surveillance d’une propriété pour les modifications de valeur, il surveille une liaison de données. Ainsi, une propriété dans un seul élément pour affecter une propriété dans un autre élément.

`DataTrigger` définit trois propriétés :

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) de type `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) de type `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) de type `IList<SetterBase>`

Le [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) exemple requiert le [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) bibliothèque et définit les couleurs des noms des étudiants en bleu ou rose selon le `Sex` propriété :

[![Capture d’écran de triple des couleurs de sexe](images/ch23fg04-small.png "sexe couleurs")](images/ch23fg04-large.png#lightbox "sexe couleurs")

Le [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) exemples de jeux le `IsEnabled` propriété d’un `Entry` à `False` si le `Length` propriété de la `Text` propriété de la `Entry`est égal à 0. Notez que le `Text` propriété est initialisée avec une chaîne vide ; par défaut, il est `null`et le `DataTrigger` ne fonctionnent pas correctement.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinaison de conditions dans le MultiTrigger

Le [ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger) est une collection de conditions. Lorsqu’ils sont tous les `true`, puis les accesseurs Set est appliquées. La classe définit deux propriétés :

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) de type `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) de type `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) est une classe abstraite et dispose de deux classes descendants :

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), qui a [ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property) et [ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value) propriétés telles que `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), qui a [ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding) et [ `Value` ](xref:Xamarin.Forms.BindingCondition.Value) propriétés telles que `DataTrigger`

Dans le [ **conditionsd** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) exemple, un `BoxView` est coloré uniquement lorsqu’il y a quatre `Switch` éléments sont tous activés.

Le [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) exemple montre comment vous pouvez effectuer un `BoxView` une couleur lorsque *n’importe quel* de quatre `Switch` éléments sont sous tension. Cela nécessite une application de la loi de Morgan à l’Allemagne et en inversant toute la logique.

Combinaison de AND et ou logique n’est pas si facile et nécessite généralement invisible `Switch` éléments des résultats intermédiaires. Le [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) exemple montre comment un `Button` peut être activée si une de deux `Entry` éléments ont du texte tapé, mais pas s’ils ont tous deux du texte tapé dans.

## <a name="behaviors"></a>comportements

Tout ce que vous pouvez faire avec un déclencheur, vous pouvez également faire avec un comportement, mais les comportements nécessitent toujours une classe qui dérive de [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) et remplace les deux méthodes suivantes :

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L’argument est l’élément auquel le comportement est associé à. En règle générale, le `OnAttachedTo` méthode attache des gestionnaires d’événements, et `OnDetachingFrom` détache les. Étant donné qu’une telle classe enregistre généralement un état, il généralement ne peut pas être partagé dans un `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) exemple est similaire à **TriggerEntryValidation** sauf qu’elle utilise un comportement &mdash; le [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque.

### <a name="behaviors-with-properties"></a>Comportements avec des propriétés

`Behavior<T>` dérive de `Behavior`, qui dérive à son `BindableObject`, de sorte que les propriétés pouvant être liées peuvent être définies sur un comportement. Ces propriétés peuvent être actives dans les liaisons de données.

Cela est illustré dans le [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) programme et qui utilisent le [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe dans le [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque. `ValidEmailBehavior` a une propriété en lecture seule et sert de source dans les liaisons de données.

Le [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) exemple utilise ce même comportement pour afficher un autre type d’indicateur pour signaler qu’une adresse de messagerie est valide.

Le [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) exemple est une variante de l’exemple précédent. ButtonGlide utilise un `DataTrigger` en association avec ce comportement.

### <a name="toggles-and-check-boxes"></a>Active/désactive et les cases à cocher

Il est possible d’encapsuler le comportement d’un bouton bascule dans une classe comme [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque, puis définissez toutes les les éléments visuels pour le bouton bascule entièrement dans XAML.

Le [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) exemple utilise le `ToggleBehavior` avec un `DataTrigger` à utiliser un `Label` avec deux chaînes de texte pour le bouton bascule.

Le [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) exemple étend ce concept en basculant entre deux `FormattedString` objets.

Le [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe dans le **Xamarin.FormsBook.Toolkit** dérive de la bibliothèque `ContentView`, définit un `IsToggled` propriété et incorpore un `ToggleBehavior` pour le bouton bascule logique. Cela rend plus facile de définir le bouton bascule dans XAML, comme illustré par la [ **TraditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) exemple.

Le [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) inclut un [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe qui dérive de `ToggleBase` et utilise un [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)classe pour construire un bouton bascule qui ressemble à la Xamarin.Forms `Switch`.

Un [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) dans le **Xamarin.FormsBook.Toolkit** fournit une animation utilisée pour effectuer un levier animé dans la [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)exemple.

### <a name="responding-to-taps"></a>Réponse aux clics

L’inconvénient de `EventTrigger` est que vous ne pouvez pas attacher un `TapGestureRecognizer` pour répondre aux clics. Pour contourner ce problème est l’objectif de [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) dans le [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Le [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) exemple utilise `TapBehavior` à utiliser l’ancien `ShiverAction` pour l’appui sur `BoxView` éléments.

Le [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) exemple montre comment réduire le balisage en encapsulant un `ShiverView` classe.

### <a name="radio-buttons"></a>Cases d’option

Le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque possède également un [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe pour effectuer des cases d’option qui sont regroupées par un `string` nom du groupe.

Le [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programme utilise des chaînes de texte de sa case. Le [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) exemple utilise un `Style` pour la différence d’aspect entre les boutons checked et unchecked. Le [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) exemple utilise des images boxed pour ses boutons radio :

[![Capture d’écran de triple des Images de Radio](images/ch23fg17-small.png "Images des boutons Radio")](images/ch23fg17-large.png#lightbox "Images des boutons Radio")

Le [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) exemple dessine traditionnel boutons radio qui apparaît avec un point à l’intérieur d’un cercle.

### <a name="fades-and-orientation"></a>Effectuer des fondus et l’orientation

L’exemple final, [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) vous permet de basculer entre trois modes de sélection de couleur différente à l’aide de cases d’option. Les trois vues de fondu et arrière à l’aide un [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) dans le [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) bibliothèque.

Le programme répond également aux modifications de l’orientation portrait ou paysage à l’aide un [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) dans le **Xamarin.FormsBook.Toolkit** bibliothèque.

## <a name="related-links"></a>Liens connexes

- [Chapitre 23 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Exemples de chapitre 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilisation des déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportements de Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)

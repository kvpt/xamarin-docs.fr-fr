---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 23. Triggers and behaviors''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a0206354254f79756e29f834c85837240736eca
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136654"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Résumé du chapitre 23. Déclencheurs et comportements

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Les déclencheurs et les comportements sont similaires, car ils sont tous les deux destinés à être utilisés dans des fichiers XAML pour simplifier les interactions entre les éléments au-delà de l’utilisation des liaisons de données, et pour étendre les fonctionnalités des éléments XAML. Les déclencheurs et les comportements sont presque toujours utilisés avec les objets d’interface utilisateur visuels.

Pour prendre en charge les déclencheurs et les comportements, `VisualElement` et `Style` prennent en charge deux propriétés de collection :

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)et [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) de type`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)et [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) de type`IList<Behavior>`

## <a name="triggers"></a>Déclencheurs

Un déclencheur est une condition (une modification de propriété ou le déclenchement d’un événement) qui génère une réponse (une autre propriété change ou exécute du code). La `Triggers` propriété de `VisualElement` et `Style` est de type `IList<TriggersBase>` . [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)est une classe abstraite dont dérivent quatre classes sealed :

- [`Trigger`](xref:Xamarin.Forms.Trigger)pour les réponses basées sur les modifications de propriété
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)pour les réponses basées sur les déclenchements d’événements
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)pour les réponses basées sur des liaisons de données
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)pour les réponses basées sur plusieurs déclencheurs

Le déclencheur est toujours défini sur l’élément dont la propriété est modifiée par le déclencheur.

### <a name="the-simplest-trigger"></a>Déclencheur le plus simple

La [`Trigger`](xref:Xamarin.Forms.Trigger) classe recherche une modification d’une valeur de propriété et répond en définissant une autre propriété du même élément.

`Trigger`définit trois propriétés :

- [`Property`](xref:Xamarin.Forms.Trigger.Property)de type`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)de type`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)de type `IList<SetterBase>` , la propriété de contenu de`Trigger`

En outre, `Trigger` exige que la propriété suivante héritée de `TriggerBase` soit définie :

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)pour indiquer le type de l’élément auquel le `Trigger` est attaché

`Property`Et `Value` constituent la condition, et la `Setters` collection est la réponse. Lorsque le indiqué `Property` a la valeur indiquée par `Value` , les `Setter` objets de la `Setters` collection sont appliqués. Lorsque `Property` a une valeur différente, les accesseurs set sont supprimés. `Setter`définit deux propriétés qui sont les mêmes que les deux premières propriétés de `Trigger` :

- [`Property`](xref:Xamarin.Forms.Setter.Property)de type`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)de type`Object`

L’exemple [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) montre comment un `Trigger` appliqué à un `Entry` peut augmenter la taille de `Entry` via la `Scale` propriété lorsque la `IsFocused` propriété du `Entry` est `true` .

Bien qu’il ne soit pas courant, le `Trigger` peut être défini dans le code, comme le montre l’exemple [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) .

L’exemple [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) montre comment le `Trigger` peut être défini dans un `Style` pour s’appliquer à plusieurs `Entry` éléments.

### <a name="trigger-actions-and-animations"></a>Actions et animations du déclencheur

Il est également possible d’exécuter un petit code basé sur un déclencheur. Ce code peut être une animation qui cible une propriété. Une méthode courante consiste à utiliser un [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) , qui définit deux propriétés :

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)de type `string` , le nom d’un événement
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)de type `IList<TriggerAction>` , une liste d’actions à exécuter en réponse.

Pour ce faire, vous devez écrire une classe qui dérive de [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1) , en général `TriggerAction<VisualElement>` . Vous pouvez définir des propriétés dans cette classe. Il s’agit de propriétés CLR ordinaires plutôt que de propriétés pouvant être liées, car `TriggerAction` ne dérive pas de `BindableObject` . Vous devez substituer la [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) méthode qui est appelée lorsque l’action est appelée. L’argument est l’élément cible.

La [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) en est un exemple. Elle appelle la `ScaleTo` propriété pour animer la `Scale` propriété d’un élément. Étant donné que l’une de ses propriétés est de type `Easing` , la [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe vous permet d’utiliser les `Easing` champs statiques standard en XAML.

L’exemple [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) montre comment appeler le `ScaleAction` à partir d' `EventTrigger` objets qui surveillent les `Focused` `Unfocused` événements et.

L’exemple [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) montre comment définir une fonction d’accélération personnalisée pour `ScaleAction` dans un fichier code-behind.

Vous pouvez également appeler des actions à l’aide d’un `Trigger` (comme distingué de `EventTrigger` ). Pour cela, vous devez savoir que `TriggerBase` définit deux collections :

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)de type`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)de type`IList<TriggerAction>`

L’exemple [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) montre comment utiliser ces collections.

### <a name="more-event-triggers"></a>Autres déclencheurs d’événements

La [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe de la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) appelle `ScaleTo` deux fois la mise à l’échelle. L’exemple [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) utilise This dans un style `EventTrigger` pour fournir un retour visuel quand un `Button` est enfoncé. Cette double animation est également possible à l’aide de deux actions dans la collection de type.[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

La [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe de la bibliothèque **Xamarin. FormsBook. Toolkit** définit une action Shiver personnalisable. L’exemple [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) le montre.

La [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe de la bibliothèque **Xamarin. FormsBook. Toolkit** est limitée aux `Entry` éléments et affecte la valeur `TextColor` rouge à la propriété si la `Text` propriété n’est pas un `double` . L’exemple [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) le montre.

### <a name="data-triggers"></a>Déclencheurs de données

[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)Est semblable au, `Trigger` sauf qu’au lieu de surveiller une propriété pour les modifications de valeur, il surveille une liaison de données. Cela permet à une propriété dans un élément d’affecter une propriété dans un autre élément.

`DataTrigger`définit trois propriétés :

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)de type`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)de type`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)de type`IList<SetterBase>`

L’exemple [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) requiert la bibliothèque [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) et définit les couleurs des noms des étudiants sur bleu ou rose en fonction de la `Sex` propriété :

[![Capture d’écran triple des couleurs de sexe](images/ch23fg04-small.png "Couleurs du sexe")](images/ch23fg04-large.png#lightbox "Couleurs du sexe")

L’exemple [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) affecte `IsEnabled` à la propriété d’un la valeur `Entry` `False` si la propriété de la `Length` `Text` propriété de `Entry` est égale à 0. Notez que la `Text` propriété est initialisée à une chaîne vide. par défaut, elle a `null` la valeur, et le `DataTrigger` ne fonctionnera pas correctement.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinaison de conditions dans le multidéclencheur

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)Est une collection de conditions. Lorsqu’ils sont tous `true` , les accesseurs set sont appliqués. La classe définit deux propriétés :

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)de type`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)de type`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)est une classe abstraite et a deux classes descendantes :

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), qui possède [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) des [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) Propriétés et comme`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), qui possède [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) des [`Value`](xref:Xamarin.Forms.BindingCondition.Value) Propriétés et comme`DataTrigger`

Dans l’exemple [**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) , un `BoxView` est uniquement coloré lorsque quatre `Switch` éléments sont activés.

L’exemple [**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) montre comment vous pouvez créer une `BoxView` couleur lorsque *l'* un des quatre `Switch` éléments est activé. Cela nécessite une application de la Loi de de Morganisation et l’inversion de toute la logique.

La combinaison de logiques and et OR n’est pas si simple et requiert généralement des éléments invisibles `Switch` pour les résultats intermédiaires. L’exemple [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) montre comment un `Button` peut être activé si un texte est tapé dans l’un des deux `Entry` éléments, mais pas s’ils ont tous deux du texte tapé.

## <a name="behaviors"></a>Comportements

Tout ce que vous pouvez faire avec un déclencheur, vous pouvez également faire avec un comportement, mais les comportements requièrent toujours une classe qui dérive de [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) et substitue les deux méthodes suivantes :

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L’argument est l’élément auquel le comportement est attaché. En règle générale, la `OnAttachedTo` méthode attache certains gestionnaires d’événements et `OnDetachingFrom` les détache. Comme une telle classe enregistre généralement un certain état, elle ne peut généralement pas être partagée dans un `Style` .

L’exemple [**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) est similaire à **TriggerEntryValidation** , à ceci près qu’il utilise un comportement de &mdash; la [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="behaviors-with-properties"></a>Comportements avec des propriétés

`Behavior<T>`dérive de, `Behavior` qui dérive de `BindableObject` , de sorte que les propriétés pouvant être liées peuvent être définies sur un comportement. Ces propriétés peuvent être actives dans les liaisons de données.

Cela est illustré dans le programme [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) qui utilise la [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . `ValidEmailBehavior`a une propriété pouvant être liée en lecture seule et sert de source dans les liaisons de données.

L’exemple [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) utilise ce même comportement pour afficher un autre type d’indicateur pour signaler qu’une adresse de messagerie est valide.

L’exemple [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) est une variante de l’exemple précédent. ButtonGlide utilise un `DataTrigger` en association avec ce comportement.

### <a name="toggles-and-check-boxes"></a>Active/désactive les cases à cocher

Il est possible d’encapsuler le comportement d’un bouton bascule dans une classe telle que [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , puis de définir tous les éléments visuels pour le bouton bascule entièrement en XAML.

L’exemple [**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) utilise le `ToggleBehavior` avec un `DataTrigger` pour utiliser un `Label` avec deux chaînes de texte pour le bouton bascule.

L’exemple [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) étend ce concept en basculant entre deux `FormattedString` objets.

La [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe de la bibliothèque **Xamarin. FormsBook. Toolkit** dérive de `ContentView` , définit une `IsToggled` propriété et intègre un `ToggleBehavior` pour la logique de basculement. Cela facilite la définition du bouton bascule en XAML, comme le montre l’exemple [**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) .

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) comprend une [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe qui dérive de `ToggleBase` et utilise une [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) classe pour construire un bouton bascule qui ressemble à Xamarin.Forms `Switch` .

Un [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) dans **Xamarin. FormsBook. Toolkit** fournit une animation utilisée pour créer un levier animé dans l’exemple [**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle) .

### <a name="responding-to-taps"></a>Réponse aux clics

L’inconvénient de `EventTrigger` est que vous ne pouvez pas l’associer à un `TapGestureRecognizer` pour répondre aux pressions. L’objectif de [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) dans le [ **Xamarin. FormsBook. Toolkit** est de contourner ce problème.](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

L’exemple [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) utilise `TapBehavior` pour utiliser le précédent `ShiverAction` pour les `BoxView` éléments taraudé.

L’exemple [**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) montre comment réduire le balisage en encapsulant une `ShiverView` classe.

### <a name="radio-buttons"></a>Cases d’option

La bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) a également une [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe pour créer des cases d’option qui sont regroupées par `string` nom de groupe.

Le programme de [**Radioétiquettes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) utilise des chaînes de texte pour sa case d’option. L’exemple de [**radiostyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) utilise un `Style` pour la différence d’apparence entre les boutons activés et non vérifiés. L’exemple de [**radioimage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) utilise des images boxed pour ses cases d’option :

[![Capture d’écran triple des images radio](images/ch23fg17-small.png "Images de cases d’option")](images/ch23fg17-large.png#lightbox "Images de cases d’option")

L’exemple [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) dessine des cases d’option traditionnelles apparaissant avec un point à l’intérieur d’un cercle.

### <a name="fades-and-orientation"></a>Fondu et orientation

L’exemple final, [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) vous permet de basculer entre trois vues de sélection de couleurs différentes à l’aide de cases d’option. Les trois vues sont en fondu et en sortie à l’aide [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) d’un dans la bibliothèque [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Le programme répond également aux changements d’orientation entre le portrait et le paysage à l’aide d’un [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) dans la bibliothèque **Xamarin. FormsBook. Toolkit** .

## <a name="related-links"></a>Liens connexes

- [Chapitre 23 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Chapitre 23 exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilisation des déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsComportements](~/xamarin-forms/app-fundamentals/behaviors/creating.md)

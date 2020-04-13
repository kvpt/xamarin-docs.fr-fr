---
title: Résumé du chapitre 23. Déclencheurs et comportements
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 23. Déclencheurs et comportements'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760595"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Résumé du chapitre 23. Déclencheurs et comportements

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Les déclencheurs et les comportements sont similaires, en ce qu’ils sont tous deux destinés à être utilisés dans les fichiers XAML pour simplifier les interactions d’éléments au-delà de l’utilisation des liaisons de données, et d’étendre la fonctionnalité des éléments XAML. Les déclencheurs et les comportements sont presque toujours utilisés avec des objets d’interface utilisateur visuel.

Pour prendre en charge les `VisualElement` `Style` déclencheurs et les comportements, les deux et les soutenir deux propriétés de collecte:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)et [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) de type`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)et [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) de type`IList<Behavior>`

## <a name="triggers"></a>Déclencheurs

Un déclencheur est une condition (un changement de propriété ou le licenciement d’un événement) qui entraîne une réponse (un autre changement de propriété ou l’exécution d’un code). La `Triggers` propriété `VisualElement` `Style` de et `IList<TriggersBase>`est de type . [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)est une classe abstraite à partir de laquelle quatre classes scellées dérivent :

- [`Trigger`](xref:Xamarin.Forms.Trigger)pour les réponses basées sur les changements de propriété
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)pour les réponses basées sur les tirs d’événements
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)pour les réponses basées sur des liaisons de données
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)pour les réponses basées sur de multiples déclencheurs

Le déclencheur est toujours fixé sur l’élément dont la propriété est modifiée par la gâchette.

### <a name="the-simplest-trigger"></a>La gâchette la plus simple

La [`Trigger`](xref:Xamarin.Forms.Trigger) classe vérifie un changement de valeur de propriété et répond en fixant une autre propriété du même élément.

`Trigger`définit trois propriétés :

- [`Property`](xref:Xamarin.Forms.Trigger.Property)de type`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)de type`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)de `IList<SetterBase>`type, la propriété de contenu de`Trigger`

En outre, `Trigger` exige que les `TriggerBase` biens suivants hérités d’être définis:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)pour indiquer le type de `Trigger` l’élément sur lequel l’élément est attaché

La `Property` `Value` condition et la `Setters` constitution, et la collection est la réponse. Lorsque `Property` l’indication a `Value`la valeur `Setter` indiquée `Setters` par , puis les objets de la collection sont appliqués. Lorsque `Property` le a une valeur différente, les setters sont supprimés. `Setter`définit deux propriétés identiques aux deux `Trigger`premières propriétés de :

- [`Property`](xref:Xamarin.Forms.Setter.Property)de type`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)de type`Object`

L’échantillon [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) montre `Trigger` comment `Entry` une application à `Entry` un `Scale` peut augmenter `IsFocused` la `Entry` taille `true`de la propriété lorsque la propriété de la est .

Bien qu’il ne `Trigger` soit pas commun, le peut être défini dans le code, comme le montre l’échantillon [**EntryPopCode.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)

[**L’échantillon StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) montre `Trigger` comment le `Style` peut être `Entry` réglé dans un pour s’appliquer à plusieurs éléments.

### <a name="trigger-actions-and-animations"></a>Déclencher des actions et des animations

Il est également possible d’exécuter un petit code basé sur un déclencheur. Ce code peut être une animation qui cible une propriété. Une façon courante est [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)d’utiliser un , qui définit deux propriétés:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)de `string`type, le nom d’un événement
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)type `IList<TriggerAction>`, une liste d’actions à exécuter en réponse.

Pour utiliser cela, vous devez écrire une [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)classe `TriggerAction<VisualElement>`qui dérive de , en général . Vous pouvez définir les propriétés de cette classe. Ce sont des propriétés CLR `TriggerAction` plaine plutôt que `BindableObject`des propriétés liantes parce que ne dérive pas de . Vous devez passer [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) outre à la méthode qui s’appelle lorsque l’action est invoquée. L’argument est l’élément cible.

La [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) en est un exemple. Il appelle `ScaleTo` la propriété pour `Scale` animer la propriété d’un élément. Parce que l’une `Easing`de [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) ses propriétés est `Easing` de type, la classe vous permet d’utiliser les champs statiques standard dans XAML.

L’échantillon [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) montre comment `ScaleAction` `EventTrigger` invoquer `Focused` les `Unfocused` objets qui surveillent les événements et les événements.

[**L’échantillon CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) montre comment définir `ScaleAction` une fonction d’assouplissement personnalisé pour dans un fichier de code-derrière.

Vous pouvez également invoquer des actions en utilisant un `Trigger` (comme distingué de `EventTrigger`). Cela exige que vous `TriggerBase` soyez conscient que définit deux collections:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)de type`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)de type`IList<TriggerAction>`

[**L’échantillon EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) montre comment utiliser ces collections.

### <a name="more-event-triggers"></a>Plus de déclencheurs d’événements

La [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe de la bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) appelle `ScaleTo` deux fois à l’échelle de haut en bas. [**L’échantillon ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) l’utilise `EventTrigger` dans un style `Button` pour fournir une rétroaction visuelle lorsqu’un est pressé. Cette double animation est également possible grâce à deux actions de la collection de type[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

La [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe de la bibliothèque **Xamarin.FormsBook.Toolkit** définit une action de frisson personnalisable. [**L’échantillon shiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) le démontre.

La [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe dans la bibliothèque **Xamarin.FormsBook.Toolkit** est `Entry` limitée aux éléments et définit la `TextColor` propriété au rouge si la `Text` propriété n’est pas un `double`. [**L’échantillon TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) le démontre.

### <a name="data-triggers"></a>Déclencheurs de données

Le [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) est similaire `Trigger` à l’exception que, au lieu de surveiller une propriété pour les changements de valeur, il surveille une liaison de données. Cela permet à une propriété dans un élément d’affecter une propriété dans un autre élément.

`DataTrigger`définit trois propriétés :

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)de type`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)de type`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)de type`IList<SetterBase>`

[**L’échantillon GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) nécessite la bibliothèque [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) et définit les couleurs des `Sex` noms des élèves en bleu ou en rose en fonction de la propriété :

[![Triple capture d’écran de Gender Colors](images/ch23fg04-small.png "Couleurs de genre")](images/ch23fg04-large.png#lightbox "Couleurs de genre")

L’échantillon [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) `IsEnabled` définit `Entry` la `False` propriété `Length` d’un à si la propriété de la `Text` propriété de l’égal `Entry` 0. Notez `Text` que la propriété est parascée à une chaîne vide; par défaut, `null`il `DataTrigger` est , et le ne fonctionnerait pas correctement.

### <a name="combining-conditions-in-the-multitrigger"></a>Combiner les conditions dans le MultiTrigger

Il [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) s’agit d’une collection de conditions. Quand ils `true`sont tous, puis les setters sont appliqués. La classe définit deux propriétés :

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)de type`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)de type`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)est une classe abstraite et a deux classes descendantes:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), qui [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) a et les propriétés comme`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), qui [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) [`Value`](xref:Xamarin.Forms.BindingCondition.Value) a et les propriétés comme`DataTrigger`

Dans l’échantillon [**AndConditions,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) a `BoxView` `Switch` n’est coloré que lorsque quatre éléments sont tous allumés.

[**L’échantillon OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) montre comment `BoxView` vous pouvez faire une couleur lorsque *l’un* des quatre `Switch` éléments sont allumés. Cela nécessite une application de la loi de De Morgan et l’inversion de toute la logique.

La combinaison et la logique DE N’est pas si facile et nécessite généralement des éléments invisibles `Switch` pour des résultats intermédiaires. [**L’échantillon XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) montre `Button` comment un peut `Entry` être activé si l’un des deux éléments ont un certain texte tapé, mais pas si elles ont tous deux un certain texte tapé dans.

## <a name="behaviors"></a>Comportements

Tout ce que vous pouvez faire avec un déclencheur, vous pouvez également faire [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) avec un comportement, mais les comportements exigent toujours une classe qui dérive et remplace les deux méthodes suivantes:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L’argument est l’élément à lequel le comportement est attaché. En général, la `OnAttachedTo` méthode attache certains `OnDetachingFrom` gestionnaires d’événements et les détache. Parce qu’une telle classe sauve généralement un `Style`certain état, il ne peut généralement pas être partagé dans un .

[**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) échantillon est similaire à **TriggerEntryValidation,** sauf qu’il utilise un comportement &mdash; de la [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

### <a name="behaviors-with-properties"></a>Comportements avec propriétés

`Behavior<T>`dérive de `Behavior`, qui `BindableObject`dérive de , de sorte que les propriétés li bindables peuvent être définies sur un comportement. Ces propriétés peuvent être actives dans les liaisons de données.

Ceci est démontré dans le programme [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) qui fait usage de la [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) `ValidEmailBehavior`possède une propriété liant non reliée et sert de source dans les liaisons de données.

[**L’échantillon EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) utilise ce même comportement pour afficher un autre type d’indicateur pour signaler qu’une adresse e-mail est valide.

[**L’échantillon EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) est une variation par rapport à l’échantillon précédent. ButtonGlide utilise `DataTrigger` une combinaison avec ce comportement.

### <a name="toggles-and-check-boxes"></a>Bascules et cases à cocher

Il est possible d’encapsuler le comportement d’un [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) bouton de bascule dans une classe comme dans la bibliothèque [**Xamarin.FormsBook.Toolkit,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) puis de définir tous les visuels pour le basculement entièrement dans XAML.

[**L’échantillon ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) utilise l’avec `ToggleBehavior` un `DataTrigger` pour utiliser un `Label` avec deux chaînes de texte pour le basculement.

[**L’échantillon FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) étend ce `FormattedString` concept en passant d’un objet à l’autre.

La [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe de la bibliothèque **Xamarin.FormsBook.Toolkit** dérive de `ContentView`, définit une `IsToggled` propriété, et intègre un `ToggleBehavior` pour la logique de basculement. Il est ainsi plus facile de définir le bouton de bascule dans XAML, comme le démontre l’échantillon [**TraditionalCheckBox.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)

Le [**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) comprend une classe `ToggleBase` qui [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) dérive et utilise une classe pour construire un `Switch`bouton de bascule qui ressemble à la Xamarin.Forms .

A [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) dans le **Xamarin.FormsBook.Toolkit** fournit une animation utilisée pour faire un levier animé dans l’échantillon [**LeverToggle.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)

### <a name="responding-to-taps"></a>Répondre aux robinets

Un inconvénient `EventTrigger` de est que vous ne `TapGestureRecognizer` pouvez pas l’attacher à un pour répondre aux robinets. Contourner ce problème est [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) le but de dans le [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[**L’échantillon BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) utilise `TapBehavior` `ShiverAction` pour utiliser `BoxView` le plus tôt pour les éléments exploités.

[**L’échantillon de ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) montre comment réduire la majoration `ShiverView` en encapsulant une classe.

### <a name="radio-buttons"></a>Cases d’option

La bibliothèque [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) dispose également d’une classe pour `string` la fabrication de boutons radio qui sont regroupés par un nom de groupe.

Le programme [**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) utilise des chaînes de texte pour son bouton radio. [**L’échantillon RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) utilise une pour la différence d’apparence `Style` entre les boutons vérifiés et non contrôlés. [**L’échantillon RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) utilise des images en boîte pour ses boutons radio :

[![Triple capture d’écran de Radio Images](images/ch23fg17-small.png "Images de bouton de radio")](images/ch23fg17-large.png#lightbox "Images de bouton de radio")

[**L’échantillon TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) dessine des boutons radio apparaissant traditionnels avec un point à l’intérieur d’un cercle.

### <a name="fades-and-orientation"></a>Fades et orientation

L’échantillon final, [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) vous permet de basculer entre trois vues différentes de sélection de couleurs à l’aide de boutons radio. Les trois vues s’estompent à l’aide d’un [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) dans la bibliothèque [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Le programme répond également aux changements d’orientation [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) entre le portrait et le paysage à l’aide d’un dans la bibliothèque **Xamarin.FormsBook.Toolkit.**

## <a name="related-links"></a>Liens connexes

- [Chapitre 23 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Échantillons du chapitre 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilisation des déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportements Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)

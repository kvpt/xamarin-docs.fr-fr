---
title: Xamarin. Forms, RadioButton
description: Le RadioButton Xamarin. Forms est un type de bouton qui permet aux utilisateurs de sélectionner une option dans un ensemble. Chaque option est représentée par une case d’option et vous ne pouvez sélectionner qu’une case d’option dans un groupe.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/13/2020
ms.openlocfilehash: 128ab4f6f00adaf86afc08eba37bcb81d3a04a90
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532813"
---
# <a name="xamarinforms-radiobutton"></a>Xamarin. Forms, RadioButton

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Xamarin. Forms `RadioButton` est un type de bouton qui permet aux utilisateurs de sélectionner une option dans un ensemble. Chaque option est représentée par une case d’option et vous ne pouvez sélectionner qu’une case d’option dans un groupe. La `RadioButton` classe hérite de la [`Button`](xref:Xamarin.Forms.Button) classe.

Les captures d’écran `RadioButton` suivantes montrent les objets dont les États ont été effacés et sélectionnés, sur iOS et Android :

![Capture d’écran des cases d’option dans les États sélectionnés et désactivés, sur iOS et Android](radiobutton-images/radiobutton-states.png "Cases d’option sur iOS et Android")

> [!IMPORTANT]
> `RadioButton`est actuellement expérimental et ne peut être utilisé qu’en `RadioButton_Experimental` définissant l’indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).

Le `RadioButton` contrôle définit les propriétés suivantes :

- `IsChecked`, de type `bool`, qui définit si `RadioButton` est sélectionné. Cette propriété utilise une `TwoWay` liaison et a une valeur par défaut de `false`.
- `GroupName`, de type `string`, qui définit le nom qui spécifie `RadioButton` les contrôles qui s’excluent mutuellement. La valeur par défaut de `null`cette propriété est.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Le `RadioButton` contrôle définit un `CheckedChanged` événement qui est déclenché lorsque la `IsChecked` propriété change, par l’intermédiaire de la manipulation de l’utilisateur ou de la programmation. L' `CheckedChangedEventArgs` objet qui accompagne l' `CheckedChanged` événement a une propriété unique nommée `Value`, de type. `bool` Lorsque l’événement est déclenché, la valeur de la `Value` propriété est définie sur la nouvelle valeur de la `IsChecked` propriété.

En outre, la `RadioButton` classe hérite des propriétés suivantes utilisées en général de la [`Button`](xref:Xamarin.Forms.Button) classe :

- [`Command`](xref:Xamarin.Forms.Button.Command), de type `ICommand`, qui est exécuté lorsque la `RadioButton` est sélectionnée.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter), de type `object`, qui est le paramètre passé à l `Command`'.
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes), de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), qui détermine le style de texte.
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), de type `string`, qui définit la famille de polices.
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize), de type `double`, qui définit la taille de la police.
- [`Text`](xref:Xamarin.Forms.Button.Text), de type `string`, qui définit le texte à afficher.
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor), de type [`Color`](xref:Xamarin.Forms.Color), qui définit la couleur du texte affiché.

Pour plus d’informations sur [`Button`](xref:Xamarin.Forms.Button) le contrôle, consultez le [bouton Xamarin. Forms](~/xamarin-forms/user-interface/button.md).

## <a name="create-radiobuttons"></a>Créer des cases d’option

L’exemple suivant montre comment instancier `RadioButton` des objets en XAML :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Text="Cat" />
    <RadioButton Text="Dog" />
    <RadioButton Text="Elephant" />
    <RadioButton Text="Monkey"
                 IsChecked="true" />
</StackLayout>
```

Dans cet exemple, `RadioButton` les objets sont regroupés implicitement dans le même conteneur parent. Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran des cases d’option groupées implicitement, sur iOS et Android](radiobutton-images/radiobuttons.png "Cases d’option groupées implicitement sur iOS et Android")

Vous pouvez également `RadioButton` créer des objets dans le code :

```csharp
StackLayout stackLayout = new StackLayout
{
    Children =
    {
        new Label { Text = "What's your favorite animal?" },
        new RadioButton { Text = "Cat" },
        new RadioButton { Text = "Dog" },
        new RadioButton { Text = "Elephant" },
        new RadioButton { Text = "Monkey", IsChecked = true }
    }
};
```

## <a name="group-radiobuttons"></a>Groupe de cases d’option

Les cases d’option fonctionnent dans des groupes et il existe deux approches de regroupement des cases d’option :

- En les plaçant dans le même conteneur parent. C’est ce que l’on appelle le regroupement implicite.
- En affectant `GroupName` la même valeur à la propriété de chaque case d’option. C’est ce que l’on appelle le regroupement explicite.

L’exemple de code XAML suivant illustre le `RadioButton` regroupement explicite d’objets `GroupName` en définissant leurs propriétés :

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             TextColor="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             TextColor="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

Dans cet exemple, chacun `RadioButton` s’exclut mutuellement, car il partage `GroupName` la même valeur. Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran des cases d’option groupées explicitement, sur iOS et Android](radiobutton-images/grouped-radiobuttons.png "Cases d’option groupées explicitement sur iOS et Android")

## <a name="respond-to-a-radiobutton-state-change"></a>Répondre à un changement d’État RadioButton

Une case d’option a deux états : sélectionnée ou désactivée. Quand une case d’option est sélectionnée, `IsChecked` sa propriété `true`est. Quand une case d’option est désactivée `IsChecked` , `false`sa propriété est. Une case d’option peut être désactivée en cliquant sur une autre case d’option dans le même groupe. Elle ne peut pas être désactivée en cliquant à nouveau dessus. Toutefois, vous pouvez désactiver une case d’option par programmation en affectant `IsChecked` à `false`sa propriété la valeur.

Lorsque la `IsChecked` propriété change, par le biais de la manipulation de l' `CheckedChanged` utilisateur ou de la programmation, l’événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

```xaml
<RadioButton Text="Red"
             TextColor="Red"
             GroupName="colors"
             CheckedChanged="OnColorsRadioButtonCheckedChanged" />
```

Le code-behind contient le gestionnaire de l' `CheckedChanged` événement :

```csharp
void OnColorsRadioButtonCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation
}
```

L' `sender` argument est le `RadioButton` responsable de cet événement. Vous pouvez l’utiliser pour accéder à `RadioButton` l’objet, ou pour faire la `RadioButton` distinction entre plusieurs objets `CheckedChanged` qui partagent le même gestionnaire d’événements.

Un gestionnaire d’événements pour l’événement peut `CheckedChanged` également être enregistré dans le code :

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> Une autre approche pour répondre à un `RadioButton` changement d’état consiste à définir `ICommand` un et à l’assigner à la `RadioButton.Command` propriété. Pour plus d’informations, consultez [Button : à l’aide de l’interface de commande](~/xamarin-forms/user-interface/button.md#using-the-command-interface).

## <a name="radiobutton-visual-states"></a>États visuels de RadioButton

`RadioButton`a un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle lorsqu' `RadioButton` un est sélectionné.

L’exemple de code XAML suivant montre comment définir un état visuel pour `IsChecked` l’État :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="IsChecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Bike"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Train"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
        <RadioButton Text="Walking"
                     CheckedChanged="OnRadioButtonCheckedChanged" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, les objets [`Style`](xref:Xamarin.Forms.Style) cibles `RadioButton` implicites. `IsChecked` `RadioButton` `TextColor` `Opacity` Spécifie que lorsqu’un est sélectionné, sa propriété est définie sur Green avec la valeur 1. [`VisualState`](xref:Xamarin.Forms.VisualState) `Normal` `RadioButton` `TextColor` `Opacity` Spécifie que lorsqu’un est dans un état d’effacement, sa propriété est définie sur rouge avec une valeur de 0,5. `VisualState` Par conséquent, l’effet global est que lorsqu' `RadioButton` un est effacé, il est rouge et partiellement transparent, et il est vert sans transparence lorsqu’il est sélectionné :

![Capture d’écran de l’apparence du RadioButton définie par l’état visuel, sur iOS et Android](radiobutton-images/ischecked-visualstate.png "États visuels de RadioButton sur iOS et Android")

Pour plus d’informations sur les États visuels, consultez [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-radiobutton"></a>Désactiver un RadioButton

Parfois, une application entre dans un état `RadioButton` où une vérification n’est pas une opération valide. Dans ce cas, le `RadioButton` peut être désactivé en affectant `IsEnabled` à `false`sa propriété la valeur.

## <a name="related-links"></a>Liens connexes

- [Démonstrations RadioButton (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Bouton Xamarin. Forms](~/xamarin-forms/user-interface/button.md)
- [Gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)

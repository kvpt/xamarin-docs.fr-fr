---
title: Xamarin.FormsButton
description: Le Xamarin.Forms RadioButton est un type de bouton qui permet aux utilisateurs de sélectionner une option dans un ensemble. Chaque option est représentée par une case d’option et vous ne pouvez sélectionner qu’une case d’option dans un groupe.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f7cbd11f98127cb73514112dae785102ff9c51c0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127621"
---
# <a name="xamarinforms-radiobutton"></a>Xamarin.FormsButton

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Le Xamarin.Forms `RadioButton` est un type de bouton qui permet aux utilisateurs de sélectionner une option dans un ensemble. Chaque option est représentée par une case d’option et vous ne pouvez sélectionner qu’une case d’option dans un groupe. La `RadioButton` classe hérite de la [`Button`](xref:Xamarin.Forms.Button) classe.

Les captures d’écran suivantes montrent les `RadioButton` objets dont les États ont été effacés et sélectionnés, sur iOS et Android :

![Capture d’écran des cases d’option dans les États sélectionnés et désactivés, sur iOS et Android](radiobutton-images/radiobutton-states.png "Cases d’option sur iOS et Android")

> [!IMPORTANT]
> `RadioButton`est actuellement expérimental et ne peut être utilisé qu’en définissant l' `RadioButton_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).

Le `RadioButton` contrôle définit les propriétés suivantes :

- `IsChecked`, de type `bool` , qui définit si `RadioButton` est sélectionné. Cette propriété utilise une `TwoWay` liaison et a une valeur par défaut de `false` .
- `GroupName`, de type `string` , qui définit le nom qui spécifie les contrôles qui s’excluent `RadioButton` mutuellement. La valeur par défaut de cette propriété est `null` .

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Le `RadioButton` contrôle définit un `CheckedChanged` événement qui est déclenché lorsque la `IsChecked` propriété change, par l’intermédiaire de la manipulation de l’utilisateur ou de la programmation. L' `CheckedChangedEventArgs` objet qui accompagne l' `CheckedChanged` événement a une propriété unique nommée `Value` , de type `bool` . Lorsque l’événement est déclenché, la valeur de la `Value` propriété est définie sur la nouvelle valeur de la `IsChecked` propriété.

En outre, la `RadioButton` classe hérite des propriétés suivantes utilisées en général de la [`Button`](xref:Xamarin.Forms.Button) classe :

- [`Command`](xref:Xamarin.Forms.Button.Command), de type `ICommand` , qui est exécuté lorsque la `RadioButton` est sélectionnée.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter), de type `object` , qui est le paramètre passé à l' `Command` .
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes), de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , qui détermine le style de texte.
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), de type `string` , qui définit la famille de polices.
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize), de type `double` , qui définit la taille de la police.
- [`Text`](xref:Xamarin.Forms.Button.Text), de type `string` , qui définit le texte à afficher.
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor), de type [`Color`](xref:Xamarin.Forms.Color) , qui définit la couleur du texte affiché.

Pour plus d’informations sur le [`Button`](xref:Xamarin.Forms.Button) contrôle, consultez [ Xamarin.Forms Button](~/xamarin-forms/user-interface/button.md).

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

L’exemple de code XAML suivant illustre le regroupement explicite `RadioButton` d’objets en définissant leurs `GroupName` Propriétés :

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

Dans cet exemple, chacun `RadioButton` s’exclut mutuellement, car il partage la même `GroupName` valeur. Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran des cases d’option groupées explicitement, sur iOS et Android](radiobutton-images/grouped-radiobuttons.png "Cases d’option groupées explicitement sur iOS et Android")

## <a name="respond-to-a-radiobutton-state-change"></a>Répondre à un changement d’État RadioButton

Une case d’option a deux états : sélectionnée ou désactivée. Quand une case d’option est sélectionnée, sa `IsChecked` propriété est `true` . Quand une case d’option est désactivée, sa `IsChecked` propriété est `false` . Une case d’option peut être désactivée en cliquant sur une autre case d’option dans le même groupe. Elle ne peut pas être désactivée en cliquant à nouveau dessus. Toutefois, vous pouvez désactiver une case d’option par programmation en affectant `IsChecked` à sa propriété la valeur `false` .

Lorsque la `IsChecked` propriété change, par le biais de la manipulation de l’utilisateur ou de la programmation, l' `CheckedChanged` événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

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

L' `sender` argument est le `RadioButton` responsable de cet événement. Vous pouvez l’utiliser pour accéder à l' `RadioButton` objet, ou pour faire la distinction entre plusieurs `RadioButton` objets qui partagent le même `CheckedChanged` Gestionnaire d’événements.

Un gestionnaire d’événements pour l' `CheckedChanged` événement peut également être enregistré dans le code :

```csharp
RadioButton radioButton = new RadioButton { ... };
radioButton.CheckedChanged += (sender, e) =>
{
    // Perform required operation
};
```

> [!NOTE]
> Une autre approche pour répondre à un `RadioButton` changement d’état consiste à définir un `ICommand` et à l’assigner à la `RadioButton.Command` propriété. Pour plus d’informations, consultez [Button : à l’aide de l’interface de commande](~/xamarin-forms/user-interface/button.md#using-the-command-interface).

## <a name="radiobutton-visual-states"></a>États visuels de RadioButton

`RadioButton`a un `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle lorsqu’un `RadioButton` est sélectionné.

L’exemple de code XAML suivant montre comment définir un état visuel pour l' `IsChecked` État :

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

Dans cet exemple, les [`Style`](xref:Xamarin.Forms.Style) objets cibles implicites `RadioButton` . `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) Spécifie que lorsqu’un `RadioButton` est sélectionné, sa `TextColor` propriété est définie sur Green avec la `Opacity` valeur 1. `Normal` `VisualState` Spécifie que lorsqu’un `RadioButton` est dans un état d’effacement, sa `TextColor` propriété est définie sur rouge avec une `Opacity` valeur de 0,5. Par conséquent, l’effet global est que lorsqu’un `RadioButton` est effacé, il est rouge et partiellement transparent, et il est vert sans transparence lorsqu’il est sélectionné :

![Capture d’écran de l’apparence du RadioButton définie par l’état visuel, sur iOS et Android](radiobutton-images/ischecked-visualstate.png "États visuels de RadioButton sur iOS et Android")

Pour plus d’informations sur les États visuels, consultez [ Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-a-radiobutton"></a>Désactiver un RadioButton

Parfois, une application entre dans un État où une `RadioButton` vérification n’est pas une opération valide. Dans ce cas, le `RadioButton` peut être désactivé en affectant `IsEnabled` à sa propriété la valeur `false` .

## <a name="related-links"></a>Liens connexes

- [Démonstrations RadioButton (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Xamarin.FormsBouton](~/xamarin-forms/user-interface/button.md)
- [Xamarin.FormsGestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md)

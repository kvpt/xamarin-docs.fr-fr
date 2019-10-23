---
title: Gestionnaire d’état visuel Xamarin. Forms
description: Utilisez le gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des États visuels définis à partir du code.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656053"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Gestionnaire d’état visuel Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilisez le gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des États visuels définis à partir du code._

Le gestionnaire d’état visuel (VSM) est nouveau dans Xamarin. Forms 3,0. VSM offre un moyen structuré d’apporter des modifications visuelles à l’interface utilisateur à partir du code. Dans la plupart des cas, l’interface utilisateur de l’application est définie en XAML, et ce code XAML comprend un balisage décrivant la manière dont le gestionnaire d’état visuel affecte les visuels de l’interface utilisateur.

L’VSM introduit le concept d' _États visuels_. Une vue Xamarin. Forms, telle qu’une `Button` peut avoir plusieurs apparences visuelles différentes en fonction de son état sous-jacent &mdash; si elle est désactivée ou appuyée ou si elle a le focus d’entrée. Il s’agit des États du bouton.

Les États visuels sont collectés dans les _groupes d’États visuels_. Tous les États visuels dans un groupe d’États visuels s’excluent mutuellement. Les États visuels et les groupes d’États visuels sont identifiés par des chaînes de texte simples.

Le gestionnaire d’état visuel Xamarin. Forms définit un groupe d’États visuels nommé « CommonStates » avec trois états visuels :

- "Normal"
- Activée
- Axée

Ce groupe d’États visuels est pris en charge pour toutes les classes qui dérivent de [`VisualElement`](xref:Xamarin.Forms.VisualElement), qui est la classe de base pour [`View`](xref:Xamarin.Forms.View) et [`Page`](xref:Xamarin.Forms.Page). 

Vous pouvez également définir vos propres groupes d’États visuels et états visuels, comme le montre cet article.

> [!NOTE]
> Les développeurs Xamarin. Forms qui connaissent les [déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md) savent que les déclencheurs peuvent également apporter des modifications aux visuels dans l’interface utilisateur en fonction des modifications apportées aux propriétés d’une vue ou du déclenchement des événements. Toutefois, l’utilisation de déclencheurs pour gérer différentes combinaisons de ces modifications peut devenir assez confuse. Historiquement, le gestionnaire d’état visuel a été introduit dans des environnements Windows XAML pour réduire la confusion résultant de combinaisons d’États visuels. Avec VSM, les États visuels dans un groupe d’États visuels sont toujours mutuellement exclusifs. À tout moment, un seul État dans chaque groupe est l’état actuel.

## <a name="the-common-states"></a>États communs

Le gestionnaire d’état visuel vous permet d’inclure dans votre fichier XAML des sections qui peuvent modifier l’apparence visuelle d’une vue si celle-ci est normale, si elle est désactivée ou si elle a le focus d’entrée. Il s’agit des _États communs_.

Par exemple, supposons que vous avez une vue `Entry` sur votre page et que vous souhaitez que l’apparence visuelle du `Entry` change de la manière suivante :

- Le `Entry` doit avoir un arrière-plan rose lorsque la `Entry` est désactivée.
- Le `Entry` doit normalement avoir un arrière-plan citron normal.
- Le `Entry` doit être étendu à deux fois sa hauteur normale lorsqu’il a le focus d’entrée.

Vous pouvez attacher le balisage VSM à une vue individuelle, ou vous pouvez le définir dans un style s’il s’applique à plusieurs vues. Les deux sections suivantes décrivent ces approches.

### <a name="vsm-markup-on-a-view"></a>Balisage VSM sur une vue

Pour attacher le balisage VSM à une vue `Entry`, commencez par séparer les `Entry`s dans les balises de début et de fin :

```xaml
<Entry FontSize="18">

</Entry>
```

Elle reçoit une taille de police explicite, car l’un des États utilise la propriété `FontSize` pour doubler la taille du texte dans le `Entry`.

Insérez ensuite `VisualStateManager.VisualStateGroups` balises entre ces balises :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) est une propriété pouvant être liée, définie par la classe [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) . (Pour plus d’informations sur les propriétés pouvant être liées, consultez l’article [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).) C’est ainsi que la propriété `VisualStateGroups` est attachée à l’objet `Entry`.

La propriété `VisualStateGroups` est de type [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList), qui est une collection d’objets [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) . Dans les balises `VisualStateManager.VisualStateGroups`, insérez une paire de balises `VisualStateGroup` pour chaque groupe d’États visuels que vous souhaitez inclure :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Notez que la balise `VisualStateGroup` a un attribut `x:Name` indiquant le nom du groupe. La classe `VisualStateGroup` définit une propriété `Name` que vous pouvez utiliser à la place :

```xaml
<VisualStateGroup Name="CommonStates">
```

Vous pouvez utiliser `x:Name` ou `Name`, mais pas les deux dans le même élément.

La classe `VisualStateGroup` définit une propriété nommée [`States`](xref:Xamarin.Forms.VisualStateGroup.States), qui est une collection d’objets [`VisualState`](xref:Xamarin.Forms.VisualState) . `States` est la _propriété de contenu_ de `VisualStateGroups` afin que vous puissiez inclure les balises `VisualState` directement entre les balises `VisualStateGroup`. (Les propriétés de contenu sont décrites dans l’article [syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

L’étape suivante consiste à inclure une paire de balises pour chaque état visuel dans ce groupe. Elles peuvent également être identifiées à l’aide de `x:Name` ou `Name` :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` définit une propriété nommée [`Setters`](xref:Xamarin.Forms.VisualState.Setters), qui est une collection d’objets [`Setter`](xref:Xamarin.Forms.Setter) . Il s’agit des mêmes objets `Setter` que ceux que vous utilisez dans un objet [`Style`](xref:Xamarin.Forms.Style) .

`Setters` n’est _pas_ la propriété de contenu de `VisualState`, il est donc nécessaire d’inclure des balises d’éléments de propriété pour la propriété `Setters` :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Vous pouvez maintenant insérer un ou plusieurs objets `Setter` entre chaque paire de balises `Setters`. Il s’agit des objets `Setter` qui définissent les États visuels décrits précédemment :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Chaque balise `Setter` indique la valeur d’une propriété particulière lorsque cet État est actuel. Toute propriété référencée par un objet `Setter` doit être sauvegardée par une propriété pouvant être liée.

Le balisage similaire à celui-ci est la base de la page de **vue VSM** dans l’exemple de programme **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . La page inclut trois affichages de `Entry`, mais seul le balisage VSM est attaché à la seconde :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Notez que la deuxième `Entry` a également une `DataTrigger` dans le cadre de sa collection de `Trigger`. Cela entraîne la désactivation de la `Entry` jusqu’à ce que le texte soit tapé dans le troisième `Entry`. Voici la page de démarrage s’exécutant sur iOS, Android et le plateforme Windows universelle (UWP) :

[![VSM sur la vue : désactivé](vsm-images/VsmOnViewDisabled.png "VSM sur la vue-désactivé")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

L’état visuel actuel est « Disabled », donc l’arrière-plan de la deuxième `Entry` est rose sur les écrans iOS et Android. L’implémentation UWP de `Entry` ne permet pas de définir la couleur d’arrière-plan lorsque la `Entry` est désactivée. 

Lorsque vous entrez du texte dans la troisième `Entry`, la deuxième `Entry` passe à l’état « normal » et l’arrière-plan est maintenant citron :

[![VSM sur la vue : normal](vsm-images/VsmOnViewNormal.png "VSM sur la vue-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Lorsque vous touchez le deuxième `Entry`, il obtient le focus d’entrée. Il passe à l’État « Focused » et se développe jusqu’à deux fois sa hauteur :

[![VSM sur la vue : Focused](vsm-images/VsmOnViewFocused.png "VSM sur la vue-Focus")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Notez que le `Entry` ne conserve pas l’arrière-plan citron lorsqu’il obtient le focus d’entrée. Étant donné que le gestionnaire d’état visuel bascule entre les États visuels, les propriétés définies par l’état précédent ne sont pas définies. Gardez à l’esprit que les États visuels s’excluent mutuellement. L’état « normal » ne signifie pas uniquement que le `Entry` est activé. Cela signifie que le `Entry` est activé et n’a pas le focus d’entrée. 

Si vous souhaitez que le `Entry` ait un arrière-plan citron à l’État « Focus », ajoutez un autre `Setter` à cet état visuel :

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Pour que ces objets `Setter` fonctionnent correctement, un `VisualStateGroup` doit contenir des objets `VisualState` pour tous les États de ce groupe. S’il existe un état visuel qui n’a pas d’objets `Setter`, incluez-le quand même comme une balise vide :

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Balisage du gestionnaire d’état visuel dans un style

Il est souvent nécessaire de partager le même balisage du gestionnaire d’état visuel entre deux vues ou plus. Dans ce cas, vous souhaiterez placer le balisage dans une définition de `Style`.

Voici le `Style` implicite existant pour les éléments `Entry` dans la page **de la vue VSM** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Ajoutez des balises `Setter` pour la propriété pouvant être liée `VisualStateManager.VisualStateGroups` jointe :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

La propriété de contenu pour `Setter` est `Value`. par conséquent, la valeur de la propriété `Value` peut être spécifiée directement dans ces balises. Cette propriété est de type `VisualStateGroupList` :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

Au sein de ces balises, vous pouvez inclure un ou plusieurs objets `VisualStateGroup` :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

Le reste du balisage VSM est le même que précédemment.

Voici la page **de style VSM dans le style** qui indique le balisage VSM complet :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Désormais, tous les affichages de `Entry` sur cette page répondent de la même façon à leurs États visuels. Notez également que l’État « ciblé » inclut désormais une deuxième `Setter` qui donne à chaque `Entry` un arrière-plan citron vert également lorsqu’il a le focus d’entrée :

[![VSM dans style](vsm-images/VsmInStyle.png "VSM dans style")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Définition de vos propres états visuels

Chaque classe qui dérive de `VisualElement` prend en charge les trois États communs « normal », « focus » et « Disabled ». En interne, la classe [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) détecte le moment où elle est activée ou désactivée, ou qui est ciblée ou inactive, et appelle la méthode statique [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Il s’agit du seul code du gestionnaire d’état visuel que vous trouverez dans la classe `VisualElement`. Étant donné que `GoToState` est appelé pour chaque objet en fonction de chaque classe qui dérive de `VisualElement`, vous pouvez utiliser le gestionnaire d’état visuel avec n’importe quel objet `VisualElement` pour répondre à ces modifications.

Il est intéressant de faire en sorte que le nom du groupe d’États visuels « CommonStates » ne soit pas explicitement référencé dans `VisualElement`. Le nom du groupe ne fait pas partie de l’API du gestionnaire d’état visuel. Dans l’un des deux exemples de programme indiqués jusqu’à présent, vous pouvez remplacer le nom du groupe « CommonStates » par un autre nom et le programme continuera de fonctionner. Le nom du groupe est simplement une description générale des États de ce groupe. Il est implicitement compris que les États visuels d’un groupe s’excluent mutuellement : un État et un seul État est actuel à tout moment.

Si vous souhaitez implémenter vos propres états visuels, vous devez appeler `VisualStateManager.GoToState` à partir du code. Le plus souvent, vous allez effectuer cet appel à partir du fichier code-behind de votre classe page.

La page **validation VSM** de l’exemple **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** montre comment utiliser le gestionnaire d’état visuel dans le cadre de la validation d’entrée. Le fichier XAML se compose de deux éléments `Label`, un `Entry` et `Button` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

Le balisage VSM est attaché au deuxième `Label` (nommé `helpLabel`) et au `Button` (nommé `submitButton`). Il existe deux États mutuellement exclusifs, nommés « valide » et « non valide ». Notez que chacun des deux groupes « ValidationState » contient des balises `VisualState` pour « valide » et « non valide », bien que l’une d’entre elles soit vide dans chaque cas. 

Si le `Entry` ne contient pas de numéro de téléphone valide, l’état actuel est « non valide » et, par conséquent, le deuxième `Label` est visible et le `Button` est désactivé :

[![Validation VSM : état non valide](vsm-images/VsmValidationInvalid.png "Validation VSM-non valide")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Lorsqu’un numéro de téléphone valide est entré, l’état actuel devient « valide ». Le deuxième `Entry` disparaît et le `Button` est maintenant activé :

[![Validation VSM : état valide](vsm-images/VsmValidationValid.png "Validation VSM-valide")](vsm-images/VsmValidationValid-Large.png#lightbox)

Le fichier code-behind est reponsible pour gérer l’événement `TextChanged` à partir du `Entry`. Le gestionnaire utilise une expression régulière pour déterminer si la chaîne d’entrée est valide ou non. La méthode dans le fichier code-behind nommé `GoToState` appelle la méthode statique `VisualStateManager.GoToState` pour `helpLabel` et `submitButton` :

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

Notez également que la méthode `GoToState` est appelée à partir du constructeur pour initialiser l’État. Il doit toujours s’agir d’un état actuel. Toutefois, dans le code, il n’y a aucune référence au nom du groupe d’États visuels, bien qu’il soit référencé dans le XAML en tant que « ValidationStates » à des fins de clarté. 

Notez que le fichier code-behind doit prendre en compte tous les objets de la page qui sont affectés par ces états visuels, et pour appeler `VisualStateManager.GoToState` pour chacun de ces objets. Dans cet exemple, il n’y a que deux objets (le `Label` et le `Button`), mais il peut en être plusieurs.

Vous vous demandez peut-être : si le fichier code-behind doit référencer chaque objet de la page affecté par ces états visuels, pourquoi le fichier code-behind ne peut-il pas accéder aux objets directement ? C’est sûrement possible. Toutefois, l’avantage de l’utilisation de VSM est que vous pouvez contrôler la façon dont les éléments visuels réagissent à différents États en XAML, ce qui permet de conserver toute la conception de l’interface utilisateur dans un seul emplacement. Cela évite de définir l’apparence visuelle en accédant à des éléments visuels directement à partir du code-behind.

Il peut être tentant de dériver une classe de `Entry` et éventuellement de définir une propriété que vous pouvez définir sur une fonction de validation externe. La classe qui dérive de `Entry` peut ensuite appeler la méthode `VisualStateManager.GoToState`. Ce schéma fonctionne correctement, mais uniquement si le `Entry` était le seul objet affecté par les différents États visuels. Dans cet exemple, une `Label` et une `Button` sont également affectées. Il n’existe aucun moyen pour le balisage VSM attaché à un `Entry` de contrôler les autres objets de la page, et le balisage VSM attaché à ces autres objets ne peut pas faire référence à un changement d’état visuel à partir d’un autre objet.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>Utilisation du gestionnaire d’état visuel pour la disposition adaptative

Une application Xamarin. Forms exécutée sur un téléphone peut généralement être affichée dans un rapport d’aspect portrait ou paysage, et un programme Xamarin. Forms en cours d’exécution sur le Bureau peut être redimensionné pour assumer de nombreuses tailles et proportions différentes. Une application bien conçue peut afficher son contenu différemment pour ces différents facteurs de forme de page ou de fenêtre. 

Cette technique est parfois appelée _disposition adaptative_. Étant donné que la disposition adaptative implique uniquement les éléments visuels d’un programme, il s’agit d’une application idéale du gestionnaire d’état visuel.

Un exemple simple est une application qui affiche une petite collection de boutons qui affectent le contenu de l’application. En mode portrait, ces boutons peuvent s’afficher dans une ligne horizontale en haut de la page :

[![Mise en page adaptative VSM : Portrait](vsm-images/VsmAdaptiveLayoutPortrait.png "Mise en page adaptative VSM-portrait")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En mode paysage, le tableau de boutons peut être déplacé d’un côté et affiché dans une colonne :

[![Mise en page adaptative VSM : paysage](vsm-images/VsmAdaptiveLayoutLandscape.png "Mise en page adaptative VSM-paysage")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De haut en bas, le programme s’exécute sur le plateforme Windows universelle, Android et iOS.

La page de **disposition adaptative VSM** de l’exemple [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) définit un groupe nommé « OrientationStates » avec deux États visuels nommés « portrait » et « paysage ». (Une approche plus complexe peut être basée sur plusieurs largeurs de page ou de fenêtre.) 

Le balisage VSM se produit à quatre emplacements dans le fichier XAML. Le `StackLayout` nommé `mainStack` contient à la fois le menu et le contenu, qui est un élément `Image`. Cette `StackLayout` doit avoir une orientation verticale en mode portrait et une orientation horizontale en mode paysage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

Le `ScrollView` interne nommé `menuScroll` et le `StackLayout` nommé `menuStack` implémentent le menu des boutons. L’orientation de ces dispositions est opposée à `mainStack`. Le menu doit être horizontal en mode portrait et vertical en mode paysage.

La quatrième section du balisage VSM est dans un style implicite pour les boutons eux-mêmes. Ce balisage définit `VerticalOptions`, `HorizontalOptions` et `Margin` propriétés spécifiques aux orientations portait et Landscape.

Le fichier code-behind définit la propriété `BindingContext` de `menuStack` pour implémenter `Button` l’exécution de commandes, et attache également un gestionnaire à l’événement `SizeChanged` de la page :

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

Le gestionnaire de `SizeChanged` appelle `VisualStateManager.GoToState` pour les deux éléments `StackLayout` et `ScrollView`, puis effectue une boucle sur les enfants de `menuStack` pour appeler `VisualStateManager.GoToState` pour les éléments `Button`.

Il peut sembler que le fichier code-behind peut traiter les modifications d’orientation plus directement en définissant les propriétés des éléments dans le fichier XAML, mais le gestionnaire d’état visuel est sans aucun doute une approche plus structurée. Tous les éléments visuels sont conservés dans le fichier XAML, où ils deviennent plus faciles à examiner, à gérer et à modifier.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestionnaire d’état visuel avec Xamarin. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vidéo du gestionnaire d’état visuel de Xamarin. Forms 3,0**

## <a name="related-links"></a>Liens connexes

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

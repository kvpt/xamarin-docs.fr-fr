---
title: Gestionnaire d’état visuel Xamarin. Forms
description: Utilisez le Gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des états visuels défini à partir du code.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: 0149806f3ab3772bc206cea9540a989d997c817b
ms.sourcegitcommit: f43d5ecafd19cbc5cce39201916a83927a34617a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78292142"
---
# <a name="xamarinforms-visual-state-manager"></a>Gestionnaire d’état visuel Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilisez le gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des États visuels définis à partir du code._

Le gestionnaire d’état visuel (VSM) offre un moyen structuré d’apporter des modifications visuelles à l’interface utilisateur à partir du code. Dans la plupart des cas, l’interface utilisateur de l’application est défini dans XAML, et ce XAML inclut un balisage qui décrit comment le Gestionnaire d’état visuel affecte les éléments visuels de l’interface utilisateur.

L’VSM introduit le concept d' _États visuels_. Une vue Xamarin. Forms, telle qu’une `Button` peut avoir plusieurs apparences visuelles différentes en fonction de son état sous-jacent &mdash; si elle est désactivée ou appuyée ou si elle a le focus d’entrée. Voici les États du bouton.

Les États visuels sont collectés dans les _groupes d’États visuels_. Tous les états visuels dans un groupe d’états visuels sont mutuellement exclusifs. Les états visuels et les groupes de l’état visuel sont identifiés par des chaînes de texte simple.

Le gestionnaire d’état visuel Xamarin. Forms définit un groupe d’États visuels nommé « CommonStates » avec les États visuels suivants :

- "Normal"
- « Désactivé »
- « Avec focus »
- Sélectionné

Ce groupe d’États visuels est pris en charge pour toutes les classes qui dérivent de [`VisualElement`](xref:Xamarin.Forms.VisualElement), qui est la classe de base pour [`View`](xref:Xamarin.Forms.View) et [`Page`](xref:Xamarin.Forms.Page).

Vous pouvez également définir vos propres groupes d’états visuels et des états visuels, comme cet article va vous montrer.

> [!NOTE]
> Les développeurs Xamarin. Forms qui connaissent les [déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md) savent que les déclencheurs peuvent également apporter des modifications aux visuels dans l’interface utilisateur en fonction des modifications apportées aux propriétés d’une vue ou du déclenchement des événements. Toutefois, à l’aide de déclencheurs pour gérer différentes combinaisons de ces modifications peut devenir très déroutant. Historiquement, le Gestionnaire d’état visuel a été introduit dans les environnements Windows XAML afin d’atténuer la confusion résultant de combinaisons d’états visuels. Avec la migration du stockage, les états visuels dans un groupe d’états visuels sont toujours qui s’excluent mutuellement. À tout moment, qu’un seul état dans chaque groupe est l’état actuel.

## <a name="common-states"></a>États communs

Le gestionnaire d’état visuel vous permet d’inclure le balisage dans votre fichier XAML qui peut modifier l’apparence visuelle d’une vue si celle-ci est normale, désactivée ou a le focus d’entrée. Il s’agit des _États communs_.

Par exemple, supposons que vous avez une vue `Entry` sur votre page et que vous souhaitez que l’apparence visuelle du `Entry` change de la manière suivante :

- Le `Entry` doit avoir un arrière-plan rose lorsque la `Entry` est désactivée.
- Le `Entry` doit normalement avoir un arrière-plan citron normal.
- Le `Entry` doit être étendu à deux fois sa hauteur normale lorsqu’il a le focus d’entrée.

Vous pouvez attacher le balisage VSM à une vue spécifique, ou vous pouvez le définir dans un style si elle s’applique à plusieurs vues. Les deux sections suivantes décrivent ces approches.

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

L’étape suivante consiste à inclure une paire de balises pour chaque état visuel dans ce groupe. Elles peuvent également être identifiées à l’aide de `x:Name` ou `Name`:

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

Notez que la deuxième `Entry` a également une `DataTrigger` dans le cadre de sa collection de `Trigger`. Cela entraîne la désactivation de la `Entry` jusqu’à ce que le texte soit tapé dans le troisième `Entry`. Voici la page au démarrage en cours d’exécution sur iOS, Android et la plateforme universelle Windows (UWP) :

[![VSM sur la vue : désactivé](vsm-images/VsmOnViewDisabled.png "VSM sur la vue-désactivé")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

L’état visuel actuel est « Disabled », donc l’arrière-plan de la deuxième `Entry` est rose sur les écrans iOS et Android. L’implémentation UWP de `Entry` ne permet pas de définir la couleur d’arrière-plan lorsque la `Entry` est désactivée.

Lorsque vous entrez du texte dans la troisième `Entry`, la deuxième `Entry` passe à l’état « normal » et l’arrière-plan est maintenant citron :

[![VSM sur la vue : normal](vsm-images/VsmOnViewNormal.png "VSM sur la vue-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Lorsque vous touchez le deuxième `Entry`, il obtient le focus d’entrée. Il passe à l’état « Actif » et se développe à deux fois sa hauteur :

[![VSM sur la vue : Focused](vsm-images/VsmOnViewFocused.png "VSM sur la vue-Focus")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Notez que le `Entry` ne conserve pas l’arrière-plan citron lorsqu’il obtient le focus d’entrée. Comme le Gestionnaire d’état visuel bascule entre les états visuels, les propriétés définies par l’état précédent ne sont pas définies. N’oubliez pas que les états visuels s’excluent mutuellement. L’état « normal » ne signifie pas uniquement que le `Entry` est activé. Cela signifie que le `Entry` est activé et n’a pas le focus d’entrée.

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

### <a name="visual-state-manager-markup-in-a-style"></a>Balisage de gestionnaire d’état visuel dans un style

Il est souvent nécessaire de partager le même balisage dans le Gestionnaire d’état visuel entre deux ou plusieurs vues. Dans ce cas, vous souhaiterez placer le balisage dans une définition de `Style`.

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

La propriété de contenu pour `Setter` est `Value`. par conséquent, la valeur de la propriété `Value` peut être spécifiée directement dans ces balises. Cette propriété est de type `VisualStateGroupList`:

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

Le reste du balisage VSM est le même qu’avant.

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

## <a name="visual-states-in-xamarinforms"></a>États visuels dans Xamarin. Forms

Le tableau suivant répertorie les États visuels définis dans Xamarin. Forms :

| Classe | États | Informations complémentaires |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [États visuels du bouton](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [CarouselView les États visuels](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [États visuels ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [États communs](#common-states) |

Chacun de ces États est accessible via le groupe d’États visuels nommé `CommonStates`.

En outre, le `CollectionView` implémente l’État `Selected`. Pour plus d’informations, consultez modifier la couleur de l' [élément sélectionné](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Définir l’État sur plusieurs éléments

Dans les exemples précédents, les États visuels étaient attachés et exploités sur des éléments uniques. Toutefois, il est également possible de créer des États visuels attachés à un seul élément, mais qui définissent des propriétés sur d’autres éléments dans la même portée. Cela évite d’avoir à répéter les États visuels sur chaque élément sur lequel les États opèrent.

Le type de [`Setter`](xref:Xamarin.Forms.Setter) a une propriété `TargetName`, de type `string`, qui représente l’élément cible que le `Setter` pour un état visuel sera manipulé. Lorsque la propriété `TargetName` est définie, l' `Setter` définit la `Property` de l’élément défini dans `TargetName` sur `Value`:

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

Dans cet exemple, la propriété `TextColor` d’un `Label` nommé `label` sera définie sur `Red`. Lors de la définition de la propriété `TargetName`, vous devez spécifier le chemin d’accès complet à la propriété dans `Property`. Par conséquent, pour définir la propriété `TextColor` sur un `Label`, `Property` est spécifié en tant que `Label.TextColor`.

> [!NOTE]
> Toute propriété référencée par un objet `Setter` doit être sauvegardée par une propriété pouvant être liée.

La page **VSM with Setter TargetName** de l’exemple **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** montre comment définir l’État sur plusieurs éléments à partir d’un seul groupe d’États visuels. Le fichier XAML se compose d’un `StackLayout` contenant un élément `Label`, un `Entry`et un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

Le balisage VSM est attaché au `StackLayout`. Il existe deux États mutuellement exclusifs, nommés « normal » et « enfoncé », chaque État contenant des balises `VisualState`.

L’état « normal » est actif lorsque l' `Button` n’est pas enfoncé et une réponse à la question peut être entrée :

[![Méthode d’accesseur Set VSM : état normal](vsm-images/VsmSetterTargetNameNormal.png "Méthode d’accesseur Set VSM-normal")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

L’état « enfoncé » devient actif quand vous appuyez sur le `Button` :

[![Méthode TargetName de l’accesseur Set VSM : état enfoncé](vsm-images/VsmSetterTargetNamePressed.png "Méthode d’accesseur Set VSM-appuyée")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

Le `VisualState` « enfoncé » spécifie que lorsque le `Button` est enfoncé, sa propriété `Scale` est remplacée par la valeur par défaut de 1 à 0,8. En outre, la propriété `Text` de la `Entry` nommée `entry` sera définie sur Paris. Par conséquent, le résultat est que lorsque le `Button` est enfoncé, il est mis à l’échelle pour être légèrement plus petit et le `Entry` affiche Paris. Ensuite, lorsque la `Button` est libérée, elle est redimensionnée à sa valeur par défaut de 1, et le `Entry` affiche tout texte précédemment entré.

> [!IMPORTANT]
> Les chemins de propriété ne sont actuellement pas pris en charge dans les éléments `Setter` qui spécifient la propriété `TargetName`.

## <a name="define-your-own-visual-states"></a>Définir vos propres états visuels

Chaque classe qui dérive de `VisualElement` prend en charge les États communs « normal », « Focused » et « Disabled ». En outre, la classe `CollectionView` prend en charge l’état « sélectionné ». En interne, la classe [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) détecte le moment où elle est activée ou désactivée, ou qui est ciblée ou inactive, et appelle la méthode statique [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Il s’agit du seul code du gestionnaire d’état visuel que vous trouverez dans la classe `VisualElement`. Étant donné que `GoToState` est appelé pour chaque objet en fonction de chaque classe qui dérive de `VisualElement`, vous pouvez utiliser le gestionnaire d’état visuel avec n’importe quel objet `VisualElement` pour répondre à ces modifications.

Il est intéressant de faire en sorte que le nom du groupe d’États visuels « CommonStates » ne soit pas explicitement référencé dans `VisualElement`. Le nom du groupe ne fait pas partie de l’API pour le Gestionnaire d’état visuel. Dans un de l’exemple de programme deux montrés jusqu'à présent, vous pouvez modifier le nom du groupe à partir de « CommonStates » pour tout autre élément, et le programme continue de fonctionner. Le nom du groupe est simplement une description générale des États de ce groupe. Il est entendu implicitement que les états visuels dans n’importe quel groupe s’excluent mutuellement : un état et qu’un seul état est en cours à tout moment.

Si vous souhaitez implémenter vos propres états visuels, vous devez appeler `VisualStateManager.GoToState` à partir du code. Plus souvent, vous apporterez cet appel à partir du fichier code-behind de votre classe de page.

La page **validation VSM** de l’exemple **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** montre comment utiliser le gestionnaire d’état visuel dans le cadre de la validation d’entrée. Le fichier XAML se compose d’un `StackLayout` contenant deux éléments `Label`, un `Entry`et un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

Le balisage VSM est attaché au `StackLayout` (nommé `stackLayout`). Il existe deux États mutuellement exclusifs, nommés « valide » et « non valide », chaque État contenant des balises `VisualState`.

Si le `Entry` ne contient pas de numéro de téléphone valide, l’état actuel est « non valide » et, par conséquent, le `Entry` a un arrière-plan rose, le deuxième `Label` est visible et le `Button` est désactivé :

[![Validation VSM : état non valide](vsm-images/VsmValidationInvalid.png "Validation VSM-non valide")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Lorsqu’un numéro de téléphone valide est entré, l’état actuel devient « Valid ». Le `Entry` obtient un arrière-plan citron, le deuxième `Label` disparaît et le `Button` est maintenant activé :

[![Validation VSM : état valide](vsm-images/VsmValidationValid.png "Validation VSM-valide")](vsm-images/VsmValidationValid-Large.png#lightbox)

Le fichier code-behind est chargé de gérer l’événement `TextChanged` à partir du `Entry`. Le Gestionnaire utilise une expression régulière pour déterminer si la chaîne d’entrée est valide ou non. La méthode dans le fichier code-behind nommé `GoToState` appelle la méthode statique `VisualStateManager.GoToState` pour `stackLayout`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

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
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

Notez également que la méthode `GoToState` est appelée à partir du constructeur pour initialiser l’État. Il doit toujours y avoir un état actif. Mais nulle part dans le code est là toute référence au nom de groupe d’états visuels, même si elle est référencée dans le XAML en tant que « ValidationStates » à des fins de clarté.

Notez que le fichier code-behind doit uniquement prendre en compte l’objet sur la page qui définit les États visuels et appeler `VisualStateManager.GoToState` pour cet objet. Cela est dû au fait que les deux États visuels ciblent plusieurs objets sur la page.

Vous vous demandez peut-être : si le fichier code-behind doit faire référence à l’objet sur la page qui définit les États visuels, pourquoi le fichier code-behind ne peut-il pas accéder directement à ce et à d’autres objets ? Il peut certainement. Toutefois, l’avantage d’utiliser la migration du stockage est que vous pouvez contrôler les éléments visuels comment réagir à un état différent entièrement dans XAML, ce qui préserve la confidentialité de la conception de l’interface utilisateur dans un emplacement. Cela évite l’apparence visuelle du paramètre en accédant à des éléments visuels directement à partir de code-behind.

## <a name="visual-state-triggers"></a>Déclencheurs d’état visuel

Les États visuels prennent en charge les déclencheurs d’État, qui sont un groupe spécialisé de déclencheurs qui définissent les conditions dans lesquelles un [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliqué.

Les déclencheurs d’État sont ajoutés à la collection [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) d’un [`VisualState`](xref:Xamarin.Forms.VisualState). Cette collection peut contenir un seul déclencheur d’État ou plusieurs déclencheurs d’État. Une [`VisualState`](xref:Xamarin.Forms.VisualState) est appliquée quand un déclencheur d’état de la collection est actif.

Lorsque vous utilisez des déclencheurs d’État pour contrôler les États visuels, Xamarin. Forms utilise les règles de précédence suivantes pour déterminer le déclencheur (et le [`VisualState`](xref:Xamarin.Forms.VisualState)correspondant) à activer :

1. Tout déclencheur qui dérive de [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase).
1. Une [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activée en raison de la satisfaction de la condition de [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) .
1. Une [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) activée en raison de la satisfaction de la condition de [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) .

Si plusieurs déclencheurs sont actifs simultanément (par exemple, deux déclencheurs personnalisés), le premier déclencheur déclaré dans le balisage est prioritaire.

Pour plus d’informations sur les déclencheurs d’État, consultez [déclencheurs d’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Utiliser le gestionnaire d’état visuel pour la disposition adaptative

Une application qui s’exécute sur un téléphone peut généralement être affichée dans un portrait ou paysage proportions et d’un programme de Xamarin.Forms en cours d’exécution sur le bureau de Xamarin.Forms peut être redimensionné afin de jouer des différentes tailles et proportions peuvent varier. Une application bien conçue peut afficher son contenu différemment pour ces différents facteurs de forme de page ou la fenêtre.

Cette technique est parfois appelée _disposition adaptative_. Étant donné que la disposition ADAPTATIF implique uniquement les éléments visuels d’un programme, c’est une application idéale de gestionnaire d’état visuel.

Un exemple simple est une application qui affiche une petite collection de boutons qui affectent le contenu de l’application. En mode portrait, ces boutons peuvent être affichées dans une ligne horizontale en haut de la page :

[![Mise en page adaptative VSM : Portrait](vsm-images/VsmAdaptiveLayoutPortrait.png "Mise en page adaptative VSM-portrait")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En mode paysage, le tableau des boutons peut être déplacé vers le côté « un » et affiché dans une colonne :

[![Mise en page adaptative VSM : paysage](vsm-images/VsmAdaptiveLayoutLandscape.png "Mise en page adaptative VSM-paysage")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De haut en bas, le programme est en cours d’exécution sur la plateforme Windows universelle, Android et iOS.

La page de **disposition adaptative VSM** de l’exemple [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) définit un groupe nommé « OrientationStates » avec deux États visuels nommés « portrait » et « paysage ». (Une approche plus complexe peut être basée sur plusieurs différentes largeurs de page ou la fenêtre).

Balisage VSM se produit en quatre endroits dans le fichier XAML. Le `StackLayout` nommé `mainStack` contient à la fois le menu et le contenu, qui est un élément `Image`. Cette `StackLayout` doit avoir une orientation verticale en mode portrait et une orientation horizontale en mode paysage :

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

Le `ScrollView` interne nommé `menuScroll` et le `StackLayout` nommé `menuStack` implémentent le menu des boutons. L’orientation de ces dispositions est opposée à `mainStack`. Le menu doit être horizontale en mode portrait et verticale en mode paysage.

La quatrième section de balisage de la migration du stockage est dans un style implicite pour les boutons eux-mêmes. Ce balisage définit `VerticalOptions`, `HorizontalOptions`et `Margin` propriétés spécifiques aux orientations portrait et paysage.

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

Il peut sembler que si le fichier code-behind peut gérer les changements d’orientation plus directement en définissant des propriétés d’éléments dans le fichier XAML, mais que le Gestionnaire d’état visuel est sans aucun doute une approche plus structurée. Tous les éléments visuels sont conservés dans le fichier XAML, où ils deviennent plus faciles à examiner, gérer et modifient.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestionnaire d’état visuel avec Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vidéo du gestionnaire d’état visuel de Xamarin. Forms 3,0**

## <a name="related-links"></a>Liens connexes

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Déclencheurs d’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)

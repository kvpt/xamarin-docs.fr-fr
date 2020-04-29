---
title: Gestionnaire d’état visuel Xamarin. Forms
description: Utilisez le gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des États visuels définis à partir du code.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2020
ms.openlocfilehash: c6930f3361394b04e90083594e2343b50dac64ab
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517513"
---
# <a name="xamarinforms-visual-state-manager"></a>Gestionnaire d’état visuel Xamarin. Forms

[![Télécharger l'](~/media/shared/download.png) exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilisez le gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des États visuels définis à partir du code._

Le gestionnaire d’état visuel (VSM) offre un moyen structuré d’apporter des modifications visuelles à l’interface utilisateur à partir du code. Dans la plupart des cas, l’interface utilisateur de l’application est définie en XAML, et ce code XAML comprend un balisage décrivant la manière dont le gestionnaire d’état visuel affecte les visuels de l’interface utilisateur.

L’VSM introduit le concept d' _États visuels_. Une vue Xamarin. Forms telle que `Button` a peut avoir plusieurs apparences visuelles différentes en fonction de son &mdash; État sous-jacent, qu’elle soit désactivée ou appuyée ou qu’elle ait le focus d’entrée. Il s’agit des États du bouton.

Les États visuels sont collectés dans les _groupes d’États visuels_. Tous les États visuels dans un groupe d’États visuels s’excluent mutuellement. Les États visuels et les groupes d’États visuels sont identifiés par des chaînes de texte simples.

Le gestionnaire d’état visuel Xamarin. Forms définit un groupe d’États visuels nommé « CommonStates » avec les États visuels suivants :

- "Normal"
- Activée
- Axée
- Sélectionné

Ce groupe d’États visuels est pris en charge pour toutes les [`VisualElement`](xref:Xamarin.Forms.VisualElement)classes qui dérivent de, [`View`](xref:Xamarin.Forms.View) qui [`Page`](xref:Xamarin.Forms.Page)est la classe de base pour et.

Vous pouvez également définir vos propres groupes d’États visuels et états visuels, comme le montre cet article.

> [!NOTE]
> Les développeurs Xamarin. Forms qui connaissent les [déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md) savent que les déclencheurs peuvent également apporter des modifications aux visuels dans l’interface utilisateur en fonction des modifications apportées aux propriétés d’une vue ou du déclenchement des événements. Toutefois, l’utilisation de déclencheurs pour gérer différentes combinaisons de ces modifications peut devenir assez confuse. Historiquement, le gestionnaire d’état visuel a été introduit dans des environnements Windows XAML pour réduire la confusion résultant de combinaisons d’États visuels. Avec VSM, les États visuels dans un groupe d’États visuels sont toujours mutuellement exclusifs. À tout moment, un seul État dans chaque groupe est l’état actuel.

## <a name="common-states"></a>États communs

Le gestionnaire d’état visuel vous permet d’inclure le balisage dans votre fichier XAML qui peut modifier l’apparence visuelle d’une vue si celle-ci est normale, désactivée ou a le focus d’entrée. Il s’agit des _États communs_.

Par exemple, supposons que vous `Entry` avez une vue sur votre page et que vous souhaitez que l’apparence `Entry` visuelle du soit modifiée comme suit :

- Le `Entry` doit avoir un arrière-plan rose `Entry` lorsque est désactivé.
- Le `Entry` doit avoir un arrière-plan citron normal.
- Le `Entry` doit se développer jusqu’à deux fois sa hauteur normale lorsqu’il a le focus d’entrée.

Vous pouvez attacher le balisage VSM à une vue individuelle, ou vous pouvez le définir dans un style s’il s’applique à plusieurs vues. Les deux sections suivantes décrivent ces approches.

### <a name="vsm-markup-on-a-view"></a>Balisage VSM sur une vue

Pour attacher le balisage VSM `Entry` à une vue, commencez `Entry` par séparer les balises de début et de fin :

```xaml
<Entry FontSize="18">

</Entry>
```

Elle reçoit une taille de police explicite, car l’un des États utilise la `FontSize` propriété pour doubler la taille du texte dans `Entry`.

Ensuite, insérez `VisualStateManager.VisualStateGroups` des balises entre ces balises :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)est une propriété pouvant être liée attachée définie [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) par la classe. (Pour plus d’informations sur les propriétés pouvant être liées, consultez l’article [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).) C’est ainsi que `VisualStateGroups` la propriété est attachée `Entry` à l’objet.

La `VisualStateGroups` propriété est de type [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList), qui est une collection d' [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) objets. Dans les `VisualStateManager.VisualStateGroups` balises, insérez une paire `VisualStateGroup` de balises pour chaque groupe d’États visuels que vous souhaitez inclure :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Notez que la `VisualStateGroup` balise a `x:Name` un attribut indiquant le nom du groupe. La `VisualStateGroup` classe définit une `Name` propriété que vous pouvez utiliser à la place :

```xaml
<VisualStateGroup Name="CommonStates">
```

Vous pouvez utiliser ou `x:Name` `Name` , mais pas les deux dans le même élément.

La `VisualStateGroup` classe définit une propriété nommée [`States`](xref:Xamarin.Forms.VisualStateGroup.States), qui est une collection d' [`VisualState`](xref:Xamarin.Forms.VisualState) objets. `States`est la _propriété_ de contenu `VisualStateGroups` de afin que vous puissiez `VisualState` inclure les balises `VisualStateGroup` directement entre les balises. (Les propriétés de contenu sont décrites dans l’article [syntaxe XAML essentielle](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

L’étape suivante consiste à inclure une paire de balises pour chaque état visuel dans ce groupe. Elles peuvent également être identifiées `x:Name` à `Name`l’aide de ou :

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

`VisualState`définit une propriété nommée [`Setters`](xref:Xamarin.Forms.VisualState.Setters), qui est une collection d' [`Setter`](xref:Xamarin.Forms.Setter) objets. Ce sont les mêmes `Setter` objets que ceux que vous utilisez [`Style`](xref:Xamarin.Forms.Style) dans un objet.

`Setters`n’est _pas_ la propriété de `VisualState`contenu de, c’est pourquoi il est nécessaire d’inclure des `Setters` balises d’éléments de propriété pour la propriété :

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

Vous pouvez maintenant insérer un ou plusieurs `Setter` objets entre chaque paire de `Setters` balises. Il s’agit `Setter` des objets qui définissent les États visuels décrits précédemment :

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

Chaque `Setter` balise indique la valeur d’une propriété particulière lorsque cet État est actuel. Toute propriété référencée par un `Setter` objet doit être sauvegardée par une propriété pouvant être liée.

Le balisage similaire à celui-ci est la base de la page de **vue VSM** dans l’exemple de programme **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** . La page inclut trois `Entry` vues, mais la balise VSM est attachée uniquement à la seconde :

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

Notez que le deuxième `Entry` a également un `DataTrigger` dans le cadre de `Trigger` sa collection. La valeur est `Entry` alors désactivée jusqu’à ce que le texte `Entry`soit tapé dans la troisième. Voici la page de démarrage s’exécutant sur iOS, Android et le plateforme Windows universelle (UWP) :

[![VSM sur la vue : désactivé](vsm-images/VsmOnViewDisabled.png "VSM sur la vue-désactivé")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

L’état visuel actuel est « Disabled », donc l’arrière `Entry` -plan de la deuxième est rose sur les écrans iOS et Android. L’implémentation UWP de `Entry` n’autorise pas la définition de la couleur d' `Entry` arrière-plan lorsque la est désactivée.

Lorsque vous entrez du texte dans le troisième `Entry`, le deuxième `Entry` passe à l’état « normal » et l’arrière-plan est maintenant citron :

[![VSM sur la vue : normal](vsm-images/VsmOnViewNormal.png "VSM sur la vue-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Lorsque vous touchez la seconde `Entry`, elle obtient le focus d’entrée. Il passe à l’État « Focused » et se développe jusqu’à deux fois sa hauteur :

[![VSM sur la vue : Focused](vsm-images/VsmOnViewFocused.png "VSM sur la vue-Focus")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Notez que le `Entry` ne conserve pas l’arrière-plan citron lorsqu’il obtient le focus d’entrée. Étant donné que le gestionnaire d’état visuel bascule entre les États visuels, les propriétés définies par l’état précédent ne sont pas définies. Gardez à l’esprit que les États visuels s’excluent mutuellement. L’état « normal » ne signifie pas uniquement que `Entry` est activé. Cela signifie que `Entry` est activé et n’a pas le focus d’entrée.

Si vous souhaitez que `Entry` le ait un arrière-plan citron à l’État « Focus », `Setter` ajoutez-en un autre à cet état visuel :

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Pour que ces `Setter` objets fonctionnent correctement, un `VisualStateGroup` doit contenir `VisualState` des objets pour tous les États de ce groupe. S’il existe un état visuel qui n’a pas d' `Setter` objets, incluez-le quand même comme une balise vide :

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Balisage du gestionnaire d’état visuel dans un style

Il est souvent nécessaire de partager le même balisage du gestionnaire d’état visuel entre deux vues ou plus. Dans ce cas, vous souhaiterez placer le balisage dans une `Style` définition.

Voici le implicite `Style` pour les `Entry` éléments de la page **de la vue VSM** :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Ajoutez `Setter` des balises `VisualStateManager.VisualStateGroups` pour la propriété pouvant être liée à un lien :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

La propriété de contenu `Setter` pour `Value`est, de sorte que la `Value` valeur de la propriété peut être spécifiée directement dans ces balises. Cette propriété est de type `VisualStateGroupList`:

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

Au sein de ces balises, vous pouvez `VisualStateGroup` inclure un ou plusieurs objets :

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

Désormais, tous `Entry` les affichages de cette page répondent de la même façon à leurs États visuels. Notez également que l’État « ciblé » inclut maintenant un second `Setter` qui donne à `Entry` chacun un arrière-plan citron également lorsqu’il a le focus d’entrée :

[![VSM dans style](vsm-images/VsmInStyle.png "VSM dans style")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>États visuels dans Xamarin. Forms

Le tableau suivant répertorie les États visuels définis dans Xamarin. Forms :

| Class | États | Informations complémentaires |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [États visuels du bouton](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CheckBox` | `IsChecked` | [États visuels des cases à cocher](~/xamarin-forms/user-interface/checkbox.md#checkbox-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [CarouselView les États visuels](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [États visuels ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `RadioButton` | `IsChecked` | [États visuels de RadioButton](~/xamarin-forms/user-interface/radiobutton.md#radiobutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [États communs](#common-states) |

Chacun de ces États est accessible via le groupe d’États visuels nommé `CommonStates`.

En outre, `CollectionView` implémente l' `Selected` État. Pour plus d’informations, consultez modifier la couleur de l' [élément sélectionné](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color).

## <a name="set-state-on-multiple-elements"></a>Définir l’État sur plusieurs éléments

Dans les exemples précédents, les États visuels étaient attachés et exploités sur des éléments uniques. Toutefois, il est également possible de créer des États visuels attachés à un seul élément, mais qui définissent des propriétés sur d’autres éléments dans la même portée. Cela évite d’avoir à répéter les États visuels sur chaque élément sur lequel les États opèrent.

Le [`Setter`](xref:Xamarin.Forms.Setter) type a une `TargetName` propriété, de type `string`, qui représente l’élément cible que le `Setter` pour un état visuel va manipuler. Lorsque la `TargetName` propriété est définie, le `Setter` définit le `Property` de l’élément défini dans `TargetName` sur `Value`:

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

Dans cet exemple, la `Label` propriété `label` d’un nommé `TextColor` aura la valeur `Red`. Lors de la `TargetName` définition de la propriété, vous devez spécifier le chemin d' `Property`accès complet à la propriété dans. Par conséquent, pour définir `TextColor` la propriété sur `Label`un `Property` , est spécifié `Label.TextColor`comme.

> [!NOTE]
> Toute propriété référencée par un `Setter` objet doit être sauvegardée par une propriété pouvant être liée.

La page **VSM with Setter TargetName** de l’exemple **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** montre comment définir l’État sur plusieurs éléments à partir d’un seul groupe d’États visuels. Le fichier XAML se compose d' `StackLayout` un contenant `Label` un élément, `Entry`un et un `Button`:

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

Le `StackLayout`balisage VSM est attaché à. Il existe deux États mutuellement exclusifs, nommés « normal » et « enfoncé », avec chaque État contenant `VisualState` des balises.

L’état « normal » est actif lorsque le `Button` n’est pas enfoncé et une réponse à la question peut être entrée :

[![Méthode d’accesseur Set VSM : état normal](vsm-images/VsmSetterTargetNameNormal.png "Méthode d’accesseur Set VSM-normal")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

L’état « enfoncé » devient actif quand l' `Button` utilisateur appuie sur le bouton :

[![Méthode TargetName de l’accesseur Set VSM : état enfoncé](vsm-images/VsmSetterTargetNamePressed.png "Méthode d’accesseur Set VSM-appuyée")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

L’option « Pressed » `VisualState` spécifie `Button` que lorsque le est `Scale` enfoncé, sa propriété est modifiée de la valeur par défaut de 1 à 0,8. De plus, la `Entry` `Text` propriété `entry` du nommé aura la valeur Paris. Par conséquent, le résultat est que lorsque `Button` l’utilisateur clique dessus, il est redimensionné pour être légèrement plus `Entry` petit et le affiche Paris. Ensuite, lorsque le `Button` est libéré, il est mis à l’échelle à sa valeur par défaut de 1 `Entry` , et le affiche tout texte précédemment entré.

> [!IMPORTANT]
> Les chemins de propriété ne sont actuellement pas `Setter` pris en charge dans `TargetName` les éléments qui spécifient la propriété.

## <a name="define-your-own-visual-states"></a>Définir vos propres états visuels

Chaque classe qui dérive de `VisualElement` prend en charge les États communs « normal », « Focused » et « Disabled ». En outre, la `CollectionView` classe prend en charge l’état « sélectionné ». En interne, la [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe détecte le moment où elle est activée ou désactivée, ou si elle est ciblée ou inactive [`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) , et appelle la méthode statique :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Il s’agit du seul code du gestionnaire d’état visuel que vous trouverez `VisualElement` dans la classe. Étant `GoToState` donné que est appelé pour chaque objet en fonction de chaque classe qui `VisualElement`dérive de, vous pouvez utiliser le gestionnaire d' `VisualElement` état visuel avec n’importe quel objet pour répondre à ces modifications.

Il est intéressant de faire en sorte que le nom du groupe d’États visuels « CommonStates » ne `VisualElement`soit pas explicitement référencé dans. Le nom du groupe ne fait pas partie de l’API du gestionnaire d’état visuel. Dans l’un des deux exemples de programme indiqués jusqu’à présent, vous pouvez remplacer le nom du groupe « CommonStates » par un autre nom et le programme continuera de fonctionner. Le nom du groupe est simplement une description générale des États de ce groupe. Il est implicitement compris que les États visuels d’un groupe s’excluent mutuellement : un État et un seul État est actuel à tout moment.

Si vous souhaitez implémenter vos propres états visuels, vous devez appeler `VisualStateManager.GoToState` à partir du code. Le plus souvent, vous allez effectuer cet appel à partir du fichier code-behind de votre classe page.

La page **validation VSM** de l’exemple **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** montre comment utiliser le gestionnaire d’état visuel dans le cadre de la validation d’entrée. Le fichier XAML se compose d' `StackLayout` un contenant `Label` deux éléments, `Entry`un et un `Button`:

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

Le `StackLayout` balisage VSM est attaché au ( `stackLayout`nommé). Il existe deux États mutuellement exclusifs, nommés « valide » et « non valide », avec chaque état `VisualState` contenant des balises.

Si le `Entry` ne contient pas de numéro de téléphone valide, l’état actuel est « non valide », et par `Entry` conséquent, a un arrière-plan `Label` rose, le deuxième est `Button` visible et est désactivé :

[![Validation VSM : état non valide](vsm-images/VsmValidationInvalid.png "Validation VSM-non valide")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Lorsqu’un numéro de téléphone valide est entré, l’état actuel devient « valide ». `Entry` Obtient un arrière-plan citron, le `Label` deuxième disparaît et `Button` est maintenant activé :

[![Validation VSM : état valide](vsm-images/VsmValidationValid.png "Validation VSM-valide")](vsm-images/VsmValidationValid-Large.png#lightbox)

Le fichier code-behind est chargé de gérer l' `TextChanged` événement à partir `Entry`du. Le gestionnaire utilise une expression régulière pour déterminer si la chaîne d’entrée est valide ou non. La méthode dans le fichier code-behind nommé `GoToState` appelle la méthode `VisualStateManager.GoToState` statique pour `stackLayout`:

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

Notez également que la `GoToState` méthode est appelée à partir du constructeur pour initialiser l’État. Il doit toujours s’agir d’un état actuel. Toutefois, dans le code, il n’y a aucune référence au nom du groupe d’États visuels, bien qu’il soit référencé dans le XAML en tant que « ValidationStates » à des fins de clarté.

Notez que le fichier code-behind doit uniquement prendre en compte l’objet sur la page qui définit les États visuels et appeler `VisualStateManager.GoToState` pour cet objet. Cela est dû au fait que les deux États visuels ciblent plusieurs objets sur la page.

Vous vous demandez peut-être : si le fichier code-behind doit faire référence à l’objet sur la page qui définit les États visuels, pourquoi le fichier code-behind ne peut-il pas accéder directement à ce et à d’autres objets ? C’est sûrement possible. Toutefois, l’avantage de l’utilisation de VSM est que vous pouvez contrôler la façon dont les éléments visuels réagissent à différents États en XAML, ce qui permet de conserver toute la conception de l’interface utilisateur dans un seul emplacement. Cela évite de définir l’apparence visuelle en accédant à des éléments visuels directement à partir du code-behind.

## <a name="visual-state-triggers"></a>Déclencheurs d’état visuel

Les États visuels prennent en charge les déclencheurs d’État, qui sont un groupe spécialisé de déclencheurs qui définissent les conditions dans lesquelles un [`VisualState`](xref:Xamarin.Forms.VisualState) doit être appliqué.

Les déclencheurs d’État sont ajoutés [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) à la collection [`VisualState`](xref:Xamarin.Forms.VisualState)d’un. Cette collection peut contenir un seul déclencheur d’État ou plusieurs déclencheurs d’État. [`VisualState`](xref:Xamarin.Forms.VisualState) Est appliqué lorsque tous les déclencheurs d’état de la collection sont actifs.

Lorsque vous utilisez des déclencheurs d’État pour contrôler les États visuels, Xamarin. Forms utilise les règles de précédence suivantes pour [`VisualState`](xref:Xamarin.Forms.VisualState)déterminer le déclencheur (et correspondant) à activer :

1. Tout déclencheur qui dérive [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase)de.
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) Activé en raison de la [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) satisfaction de la condition.
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger) Activé en raison de la [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) satisfaction de la condition.

Si plusieurs déclencheurs sont actifs simultanément (par exemple, deux déclencheurs personnalisés), le premier déclencheur déclaré dans le balisage est prioritaire.

Pour plus d’informations sur les déclencheurs d’État, consultez [déclencheurs d’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

<a name="adaptive-layout" />

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>Utiliser le gestionnaire d’état visuel pour la disposition adaptative

Une application Xamarin. Forms exécutée sur un téléphone peut généralement être affichée dans un rapport d’aspect portrait ou paysage, et un programme Xamarin. Forms en cours d’exécution sur le Bureau peut être redimensionné pour assumer de nombreuses tailles et proportions différentes. Une application bien conçue peut afficher son contenu différemment pour ces différents facteurs de forme de page ou de fenêtre.

Cette technique est parfois appelée _disposition adaptative_. Étant donné que la disposition adaptative implique uniquement les éléments visuels d’un programme, il s’agit d’une application idéale du gestionnaire d’état visuel.

Un exemple simple est une application qui affiche une petite collection de boutons qui affectent le contenu de l’application. En mode portrait, ces boutons peuvent s’afficher dans une ligne horizontale en haut de la page :

[![Mise en page adaptative VSM : Portrait](vsm-images/VsmAdaptiveLayoutPortrait.png "Mise en page adaptative VSM-portrait")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En mode paysage, le tableau de boutons peut être déplacé d’un côté et affiché dans une colonne :

[![Mise en page adaptative VSM : paysage](vsm-images/VsmAdaptiveLayoutLandscape.png "Mise en page adaptative VSM-paysage")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De haut en bas, le programme s’exécute sur le plateforme Windows universelle, Android et iOS.

La page de **disposition adaptative VSM** de l’exemple [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) définit un groupe nommé « OrientationStates » avec deux États visuels nommés « portrait » et « paysage ». (Une approche plus complexe peut être basée sur plusieurs largeurs de page ou de fenêtre.)

Le balisage VSM se produit à quatre emplacements dans le fichier XAML. Le `StackLayout` nommé `mainStack` contient à la fois le menu et le contenu, qui `Image` est un élément. Elle `StackLayout` doit avoir une orientation verticale en mode portrait et une orientation horizontale en mode paysage :

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

Le `ScrollView` nommé `menuScroll` et le `StackLayout` nommé `menuStack` implémentent le menu des boutons. L’orientation de ces dispositions est contraire à `mainStack`. Le menu doit être horizontal en mode portrait et vertical en mode paysage.

La quatrième section du balisage VSM est dans un style implicite pour les boutons eux-mêmes. Cet ensemble `VerticalOptions`de balises, `HorizontalOptions`et `Margin` propriétés spécifiques aux orientations portrait et paysage.

Le fichier code-behind définit la `BindingContext` propriété de `menuStack` pour implémenter `Button` les commandes et attache un gestionnaire à l' `SizeChanged` événement de la page :

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

Le `SizeChanged` gestionnaire appelle `VisualStateManager.GoToState` pour les deux `StackLayout` éléments `ScrollView` et, puis effectue une boucle dans les enfants `menuStack` de pour `VisualStateManager.GoToState` appeler pour `Button` les éléments.

Il peut sembler que le fichier code-behind peut traiter les modifications d’orientation plus directement en définissant les propriétés des éléments dans le fichier XAML, mais le gestionnaire d’état visuel est sans aucun doute une approche plus structurée. Tous les éléments visuels sont conservés dans le fichier XAML, où ils deviennent plus faciles à examiner, à gérer et à modifier.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestionnaire d’état visuel avec Xamarin. University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vidéo du gestionnaire d’état visuel de Xamarin. Forms 3,0**

## <a name="related-links"></a>Liens connexes

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [Déclencheurs d’État](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)

---
title: Le Gestionnaire d’état visuel de Xamarin.Forms
description: Utilisez le Gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des états visuels défini à partir du code.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: 11de0ecf20c6748d4958d1f1f1bea80e6a87024e
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490010"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Le Gestionnaire d’état visuel de Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_Utilisez le Gestionnaire d’état visuel pour apporter des modifications aux éléments XAML en fonction des états visuels défini à partir du code._

Le Gestionnaire d’état Visual (VSM) est une nouveauté dans Xamarin.Forms 3.0. Le VSM fournit un moyen structuré d’apporter des modifications visual à l’interface utilisateur à partir du code. Dans la plupart des cas, l’interface utilisateur de l’application est défini dans XAML, et ce XAML inclut un balisage qui décrit comment le Gestionnaire d’état visuel affecte les éléments visuels de l’interface utilisateur.

Le VSM introduit le concept de _états visuels_. Une vue de Xamarin.Forms comme un `Button` peut avoir plusieurs des apparences visuelles différentes en fonction de son état sous-jacent &mdash; si elle est désactivée, enfoncé ou a le focus. Voici les États du bouton.

États visuels sont collectés dans _groupes de l’état visuel_. Tous les états visuels dans un groupe d’états visuels sont mutuellement exclusifs. Les états visuels et les groupes de l’état visuel sont identifiés par des chaînes de texte simple.

Le Gestionnaire d’état visuel Xamarin.Forms définit un groupe d’états visuels nommé « CommonStates » avec trois états visuels :

- "Normal"
- « Désactivé »
- « Avec focus »

Ce groupe d’états visuels est pris en charge pour toutes les classes qui dérivent de [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), qui est la classe de base pour [ `View` ](xref:Xamarin.Forms.View) et [ `Page` ](xref:Xamarin.Forms.Page).

Vous pouvez également définir vos propres groupes d’états visuels et des états visuels, comme cet article va vous montrer.

> [!NOTE]
> Xamarin.Forms les développeurs qui connaissent [déclencheurs](~/xamarin-forms/app-fundamentals/triggers.md) sont conscients que les déclencheurs peuvent également apporter des modifications aux éléments visuels dans l’interface utilisateur en fonction des modifications dans les propriétés de l’affichage ou le déclenchement d’événements. Toutefois, à l’aide de déclencheurs pour gérer différentes combinaisons de ces modifications peut devenir très déroutant. Historiquement, le Gestionnaire d’état visuel a été introduit dans les environnements Windows XAML afin d’atténuer la confusion résultant de combinaisons d’états visuels. Avec la migration du stockage, les états visuels dans un groupe d’états visuels sont toujours qui s’excluent mutuellement. À tout moment, qu’un seul état dans chaque groupe est l’état actuel.

## <a name="the-common-states"></a>Les États courants

Le Gestionnaire d’état visuel vous permet d’inclure des sections dans votre fichier XAML qui peut modifier l’apparence visuelle d’une vue si l’affichage normal, ou est désactivé ou a le focus d’entrée. Ceux-ci sont connus comme les _États courants_.

Par exemple, supposons que vous avez un `Entry` dans votre page, et que vous souhaitez l’apparence visuelle de la `Entry` modifier comme suit :

- Le `Entry` doit avoir un rose en arrière-plan lorsque le `Entry` est désactivé.
- Le `Entry` doit avoir un arrière-plan vert clair normalement.
- Le `Entry` doit se développer à deux fois sa hauteur normal lorsqu’il a le focus.

Vous pouvez attacher le balisage VSM à une vue spécifique, ou vous pouvez le définir dans un style si elle s’applique à plusieurs vues. Les deux sections suivantes décrivent ces approches.

### <a name="vsm-markup-on-a-view"></a>Balisage VSM sur une vue

Pour attacher le balisage VSM à un `Entry` afficher, tout d’abord séparer le `Entry` dans les balises de début et de fin :

```xaml
<Entry FontSize="18">

</Entry>
```

Il a donné une taille de police explicite, car un des États utilisera le `FontSize` propriété doubler la taille du texte dans le `Entry`.

Insérez ensuite `VisualStateManager.VisualStateGroups` balises entre ces balises :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) est une propriété jointe peut être liée, définie par le [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) classe. (Pour plus d’informations sur les propriétés pouvant être liées, consultez l’article [propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).) C’est ainsi que la propriété `VisualStateGroups` est attachée à l’objet `Entry`.

Le `VisualStateGroups` propriété est de type [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), qui est une collection de [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) objets. Dans le `VisualStateManager.VisualStateGroups` balises, insérer une paire de `VisualStateGroup` balises pour chaque groupe d’états visuels que vous souhaitez inclure :

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Notez que le `VisualStateGroup` balise a un `x:Name` attribut indiquant le nom du groupe. Le `VisualStateGroup` classe définit un `Name` propriété que vous pouvez utiliser à la place :

```xaml
<VisualStateGroup Name="CommonStates">
```

Vous pouvez utiliser `x:Name` ou `Name` mais pas les deux dans le même élément.

Le `VisualStateGroup` classe définit une propriété nommée [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), qui est une collection de [ `VisualState` ](xref:Xamarin.Forms.VisualState) objets. `States` est le _propriété de contenu_ de `VisualStateGroups` afin d’inclure le `VisualState` balises directement entre le `VisualStateGroup` balises. (Contenu de propriétés sont décrites dans l’article [essentielles syntaxe XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

L’étape suivante consiste à inclure une paire de balises pour chaque état visuel dans ce groupe. Ils peuvent également être identifiés à l’aide de `x:Name` ou `Name`:

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

`VisualState` définit une propriété nommée [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), qui est une collection de [ `Setter` ](xref:Xamarin.Forms.Setter) objets. Il s’agit de la même `Setter` les objets que vous utilisez dans un [ `Style` ](xref:Xamarin.Forms.Style) objet.

`Setters` est _pas_ la propriété content de `VisualState`, par conséquent, il est nécessaire d’inclure des balises d’élément de propriété pour le `Setters` propriété :

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

Vous pouvez maintenant insérer un ou plusieurs `Setter` objets entre chaque paire de `Setters` balises. Il s’agit de la `Setter` les objets qui définissent les états visuels décrits précédemment :

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

Chaque `Setter` balise indique la valeur d’une propriété particulière lorsque cet état est en cours. N’importe quelle propriété référencée par un `Setter` objet doit reposer sur une propriété pouvant être liée.

Balisage semblable à celle-ci constitue la base de la **VSM sur vue** page dans le **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** exemple de programme. La page inclut trois `Entry` vues, mais seul le deuxième a le balisage VSM attaché à celui-ci :

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

Notez que la seconde `Entry` a également un `DataTrigger` dans le cadre de son `Trigger` collection. Cela entraîne le `Entry` doit être désactivée jusqu'à ce que quelque chose est tapée dans la troisième `Entry`. Voici la page au démarrage en cours d’exécution sur iOS, Android et la plateforme universelle Windows (UWP) :

[![VSM sur la vue : désactivé](vsm-images/VsmOnViewDisabled.png "VSM sur la vue-désactivé")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

L’état visuel actuel est « Disabled » par conséquent, l’arrière-plan de la deuxième `Entry` est rose sur iOS et Android écrans. L’implémentation UWP de `Entry` n’autorise pas la définition de l’arrière-plan de couleur lorsque le `Entry` est désactivé.

Lorsque vous entrez du texte dans la troisième `Entry`, le deuxième `Entry` switches dans l’état « Normal » et l’arrière-plan est désormais citron vert :

[![VSM sur la vue : normal](vsm-images/VsmOnViewNormal.png "VSM sur la vue-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Lorsque vous touchez la seconde `Entry`, il obtient le focus d’entrée. Il passe à l’état « Actif » et se développe à deux fois sa hauteur :

[![VSM sur la vue : Focused](vsm-images/VsmOnViewFocused.png "VSM sur la vue-Focus")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Notez que le `Entry` ne conserve pas l’arrière-plan vert clair lorsqu’il atteint le focus d’entrée. Comme le Gestionnaire d’état visuel bascule entre les états visuels, les propriétés définies par l’état précédent ne sont pas définies. N’oubliez pas que les états visuels s’excluent mutuellement. L’état « Normal » ne signifie pas uniquement que le `Entry` est activé. Cela signifie que le `Entry` est activé et n’a pas le focus d’entrée.

Si vous souhaitez que le `Entry` pour avoir un arrière-plan vert clair dans l’état « Actif », ajoutez un autre `Setter` à cet état visuel :

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

Dans l’ordre pour ces `Setter` objets fonctionne correctement, un `VisualStateGroup` doit contenir `VisualState` objets pour tous les États de ce groupe. S’il existe un état visuel qui ne possède aucun `Setter` objets, inclure quand même comme balise vide :

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>Balisage de gestionnaire d’état visuel dans un style

Il est souvent nécessaire de partager le même balisage dans le Gestionnaire d’état visuel entre deux ou plusieurs vues. Dans ce cas, vous souhaitez placer le balisage dans un `Style` définition.

Voici existant implicite `Style` pour le `Entry` éléments dans le **VSM sur vue** page :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

Ajouter `Setter` balises pour le `VisualStateManager.VisualStateGroups` propriété pouvant être liée jointe :

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

La propriété de contenu pour `Setter` est `Value`, de sorte que la valeur de la `Value` propriété peut être spécifiée directement dans ces balises. Que la propriété est de type `VisualStateGroupList`:

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

Dans ces balises, vous pouvez inclure un ou plusieurs `VisualStateGroup` objets :

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

Voici le **VSM dans Style** page affichant le balisage complet de la migration du stockage :

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

Maintenant tous les `Entry` vues sur cette page répondent de la même façon à leurs états visuels. Notez également que l’état « Focus » inclut maintenant un deuxième `Setter` qui accorde à chacun `Entry` un citron vert en arrière-plan également quand il a le focus d’entrée :

[![VSM dans style](vsm-images/VsmInStyle.png "VSM dans style")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-xamarinforms"></a>États visuels dans Xamarin. Forms

Le tableau suivant répertorie les États visuels définis dans Xamarin. Forms :

| Classe | États | Plus d’informations |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [États visuels du bouton](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CollectionView` | `Selected` | [Modifier la couleur de l’élément sélectionné](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color) |
| `ImageButton` | `Pressed` | [États visuels ImageButton](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused` | [États communs](#the-common-states) |

Chacun de ces États est accessible via le groupe d’États visuels nommé `CommonStates`.

## <a name="defining-your-own-visual-states"></a>Définition de vos propres états visuels

Chaque classe qui dérive de `VisualElement` prend en charge les trois états courants « Normal », « Avec focus » et « Désactivé ». En interne, le [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) classe détecte lorsqu’il devient activé ou désactivé, ou avec ou sans focus et appelle la méthode statique [ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) méthode :

```csharp
VisualStateManager.GoToState(this, "Focused");
```

C’est le seul code de gestionnaire d’état visuel que vous trouverez dans le `VisualElement` classe. Étant donné que `GoToState` est appelée pour chaque objet en fonction de chaque classe qui dérive de `VisualElement`, vous pouvez utiliser le Gestionnaire d’état visuel avec n’importe quel `VisualElement` objet pour répondre à ces modifications.

Curieusement, le nom du groupe d’état visuel « CommonStates » n’est pas référencé explicitement dans `VisualElement`. Le nom du groupe ne fait pas partie de l’API pour le Gestionnaire d’état visuel. Dans un de l’exemple de programme deux montrés jusqu'à présent, vous pouvez modifier le nom du groupe à partir de « CommonStates » pour tout autre élément, et le programme continue de fonctionner. Le nom du groupe est simplement une description générale des États de ce groupe. Il est entendu implicitement que les états visuels dans n’importe quel groupe s’excluent mutuellement : un état et qu’un seul état est en cours à tout moment.

Si vous souhaitez implémenter vos propres états visuels, vous devez appeler `VisualStateManager.GoToState` à partir du code. Plus souvent, vous apporterez cet appel à partir du fichier code-behind de votre classe de page.

Le **VSM Validation** page dans le **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** exemple montre comment utiliser le Gestionnaire d’état visuel dans le cadre de la validation des entrées. Le fichier XAML se compose de deux `Label` éléments, un `Entry`, et `Button`:

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

Balisage VSM est attaché à la seconde `Label` (nommé `helpLabel`) et le `Button` (nommé `submitButton`). Il existe deux États mutuellement exclusives, nommés « Valid » et « Non valide ». Notez que chacun des deux groupes « ValidationState » contient `VisualState` balises pour « Valid » et « Non valide », bien qu’un d’eux est vide dans chaque cas.

Si le `Entry` ne contient pas un numéro de téléphone valide, puis l’état actuel est « Invalid » et donc la seconde `Label` est visible et le `Button` est désactivé :

[![Validation VSM : état non valide](vsm-images/VsmValidationInvalid.png "Validation VSM-non valide")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Lorsqu’un numéro de téléphone valide est entré, l’état actuel devient « Valid ». La seconde `Entry` disparaît et la `Button` est maintenant activé :

[![Validation VSM : état valide](vsm-images/VsmValidationValid.png "Validation VSM-valide")](vsm-images/VsmValidationValid-Large.png#lightbox)

Le fichier code-behind est doit pour la gestion de la `TextChanged` événement à partir de la `Entry`. Le Gestionnaire utilise une expression régulière pour déterminer si la chaîne d’entrée est valide ou non. La méthode dans le fichier code-behind nommé `GoToState` appelle la méthode statique `VisualStateManager.GoToState` méthode pour les deux `helpLabel` et `submitButton`:

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

Notez également que le `GoToState` méthode est appelée à partir du constructeur pour initialiser l’état. Il doit toujours y avoir un état actif. Mais nulle part dans le code est là toute référence au nom de groupe d’états visuels, même si elle est référencée dans le XAML en tant que « ValidationStates » à des fins de clarté.

Notez que le fichier code-behind doit tenir compte de chaque objet dans la page qui est affectée par ces états visuels et d’appeler `VisualStateManager.GoToState` pour chacun de ces objets. Dans cet exemple, il s’agit uniquement de deux objets (la `Label` et `Button`), mais cela peut signifier plusieurs plus.

Vous vous demandez peut-être : si le fichier code-behind doit faire référence à chaque objet dans la page qui est affectée par ces états visuels, pourquoi ne peut pas le fichier code-behind simplement accéder aux objets directement ? Il peut certainement. Toutefois, l’avantage d’utiliser la migration du stockage est que vous pouvez contrôler les éléments visuels comment réagir à un état différent entièrement dans XAML, ce qui préserve la confidentialité de la conception de l’interface utilisateur dans un emplacement. Cela évite l’apparence visuelle du paramètre en accédant à des éléments visuels directement à partir de code-behind.

Il peut être tentant d’envisager de dériver une classe à partir de `Entry` et peut-être définition d’une propriété que vous pouvez définir une fonction de validation externe. La classe qui dérive de `Entry` peut ensuite appeler la `VisualStateManager.GoToState` (méthode). Ce schéma fonctionnerait correctement, mais uniquement si le `Entry` était le seul objet affecté par les différents états visuels. Dans cet exemple, un `Label` et un `Button` sont également affectés. Il n’existe aucun moyen pour le balisage de la migration du stockage attaché à un `Entry` pour contrôler les autres objets sur la page et aucun moyen pour le balisage VSM associés à ces autres objets pour faire référence à une modification dans l’état visuel d’un autre objet.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>À l’aide de gestionnaire d’état visuel pour la disposition ADAPTATIF

Une application qui s’exécute sur un téléphone peut généralement être affichée dans un portrait ou paysage proportions et d’un programme de Xamarin.Forms en cours d’exécution sur le bureau de Xamarin.Forms peut être redimensionné afin de jouer des différentes tailles et proportions peuvent varier. Une application bien conçue peut afficher son contenu différemment pour ces différents facteurs de forme de page ou la fenêtre.

Cette technique est parfois appelée _disposition adaptive_. Étant donné que la disposition ADAPTATIF implique uniquement les éléments visuels d’un programme, c’est une application idéale de gestionnaire d’état visuel.

Un exemple simple est une application qui affiche une petite collection de boutons qui affectent le contenu de l’application. En mode portrait, ces boutons peuvent être affichées dans une ligne horizontale en haut de la page :

[![Mise en page adaptative VSM : Portrait](vsm-images/VsmAdaptiveLayoutPortrait.png "Mise en page adaptative VSM-portrait")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

En mode paysage, le tableau des boutons peut être déplacé vers le côté « un » et affiché dans une colonne :

[![Mise en page adaptative VSM : paysage](vsm-images/VsmAdaptiveLayoutLandscape.png "Mise en page adaptative VSM-paysage")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

De haut en bas, le programme est en cours d’exécution sur la plateforme Windows universelle, Android et iOS.

Le **disposition ADAPTATIF VSM** page dans le [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos) exemple définit un groupe nommé « OrientationStates » avec deux états visuels nommés « Portrait » et « Paysage ». (Une approche plus complexe peut être basée sur plusieurs différentes largeurs de page ou la fenêtre).

Balisage VSM se produit en quatre endroits dans le fichier XAML. Le `StackLayout` nommé `mainStack` contient à la fois le menu et le contenu, qui est un `Image` élément. Cela `StackLayout` doit avoir une orientation verticale en mode portrait et une orientation horizontale en mode paysage :

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

Interne `ScrollView` nommé `menuScroll` et `StackLayout` nommé `menuStack` implémenter le menu de boutons. L’orientation de ces mises en page est l’opposé de `mainStack`. Le menu doit être horizontale en mode portrait et verticale en mode paysage.

La quatrième section de balisage de la migration du stockage est dans un style implicite pour les boutons eux-mêmes. Ce balisage définit `VerticalOptions`, `HorizontalOptions`, et `Margin` propriétés spécifiques pour les orientations portrait et paysage.

Les jeux de fichier code-behind le `BindingContext` propriété du `menuStack` pour implémenter `Button` d’exécution des commandes et également attache un gestionnaire à la `SizeChanged` événements de la page :

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

Le `SizeChanged` appels du gestionnaire `VisualStateManager.GoToState` pour les deux `StackLayout` et `ScrollView` éléments, puis effectue une boucle sur les enfants de `menuStack` pour appeler `VisualStateManager.GoToState` pour le `Button` éléments.

Il peut sembler que si le fichier code-behind peut gérer les changements d’orientation plus directement en définissant des propriétés d’éléments dans le fichier XAML, mais que le Gestionnaire d’état visuel est sans aucun doute une approche plus structurée. Tous les éléments visuels sont conservés dans le fichier XAML, où ils deviennent plus faciles à examiner, gérer et modifient.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Gestionnaire d’état visuel avec Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Vidéo du gestionnaire d’état visuel de Xamarin. Forms 3,0**

## <a name="related-links"></a>Liens connexes

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

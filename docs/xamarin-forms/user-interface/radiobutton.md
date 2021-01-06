---
title: Xamarin.Forms Button
description: Le Xamarin.Forms RadioButton est un type de bouton qui permet aux utilisateurs de sélectionner une option dans un ensemble. Chaque option est représentée par une case d’option et vous ne pouvez sélectionner qu’une case d’option dans un groupe.
ms.prod: xamarin
ms.assetid: E2AA40E0-69A5-41DF-BFC4-C151CA657451
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e57003825e0ea77d54ec528b18328e98f3d1b81e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939853"
---
# <a name="no-locxamarinforms-radiobutton"></a>Xamarin.Forms Button

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)

Le Xamarin.Forms [`RadioButton`](xref:Xamarin.Forms.RadioButton) est un type de bouton qui permet aux utilisateurs de sélectionner une option dans un ensemble. Chaque option est représentée par une case d’option et vous ne pouvez sélectionner qu’une case d’option dans un groupe. Par défaut, chaque [`RadioButton`](xref:Xamarin.Forms.RadioButton) affiche le texte suivant :

![Capture d’écran des cases d’option par défaut](radiobutton-images/radiobuttons-default.png "Cases d’option par défaut")

Toutefois, sur certaines plateformes, un [`RadioButton`](xref:Xamarin.Forms.RadioButton) peut afficher un [`View`](xref:Xamarin.Forms.View) et, sur toutes les plateformes, l’apparence de chaque `RadioButton` peut être redéfinie avec [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) :

![Capture d’écran des cases d’option redéfinies](radiobutton-images/radiobuttons-controltemplate.png "Cases d’option redéfinies")

Le [`RadioButton`](xref:Xamarin.Forms.RadioButton) contrôle définit les propriétés suivantes :

- `Content`, de type `object` , qui définit le `string` ou [`View`](xref:Xamarin.Forms.View) à afficher par le `RadioButton` .
- `IsChecked`, de type `bool` , qui définit si le `RadioButton` est activé. Cette propriété utilise une `TwoWay` liaison et a une valeur par défaut de `false` .
- `GroupName`, de type `string` , qui définit le nom qui spécifie les contrôles qui s’excluent `RadioButton` mutuellement. La valeur par défaut de cette propriété est `null` .
- `Value`, de type `object` , qui définit une valeur unique facultative associée à `RadioButton` .
- `BorderColor`, de type [`Color`](xref:Xamarin.Forms.Color) , qui définit la couleur de contour de la bordure.
- `BorderWidth`, de type `double` , qui définit la largeur de la `RadioButton` bordure.
- `CharacterSpacing`, de type `double` , qui définit l’espacement entre les caractères d’un texte affiché.
- `CornerRadius`, de type `int` , qui définit le rayon de l’angle de `RadioButton` .
- `FontAttributes`, de type [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , qui détermine le style de texte.
- `FontFamily`, de type `string` , qui définit la famille de polices.
- `FontSize`, de type `double` , qui définit la taille de la police.
- `TextColor`, de type [`Color`](xref:Xamarin.Forms.Color) , qui définit la couleur de tout texte affiché.
- `TextTransform`, de type `TextTransform` , qui définit la casse d’un texte affiché.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Le [`RadioButton`](xref:Xamarin.Forms.RadioButton) contrôle définit également un `CheckedChanged` événement qui est déclenché lorsque la `IsChecked` propriété change, par l’intermédiaire de la manipulation de l’utilisateur ou de la programmation. L' `CheckedChangedEventArgs` objet qui accompagne l' `CheckedChanged` événement a une propriété unique nommée `Value` , de type `bool` . Lorsque l’événement est déclenché, la valeur de la `CheckedChangedEventArgs.Value` propriété est définie sur la nouvelle valeur de la `IsChecked` propriété.

[`RadioButton`](xref:Xamarin.Forms.RadioButton) le regroupement peut être géré par la `RadioButtonGroup` classe, qui définit les propriétés jointes suivantes :

-   `GroupName`, de type `string` , qui définit le nom de groupe pour les `RadioButton` objets dans un `Layout<View>` .
- `SelectedValue`, de type `object` , qui représente la valeur de l' `RadioButton` objet vérifié dans un `Layout<View>` groupe. Cette propriété jointe utilise une `TwoWay` liaison par défaut.

Pour plus d’informations sur la `GroupName` propriété jointe, consultez [regrouper des cases d’option](#group-radiobuttons). Pour plus d’informations sur la `SelectedValue` propriété jointe, consultez [répondre aux modifications d’État RadioButton](#respond-to-radiobutton-state-changes).

## <a name="create-radiobuttons"></a>Créer des cases d’option

L’apparence d’un [`RadioButton`](xref:Xamarin.Forms.RadioButton) est définie par le type de données affectées à la `RadioButton.Content` propriété :

- Quand la `RadioButton.Content` propriété est assignée `string` à un, elle est affichée sur chaque plateforme, alignée horizontalement à côté du cercle de la case d’option.
- Lorsque le `RadioButton.Content` se voit attribuer un [`View`](xref:Xamarin.Forms.View) , il s’affiche sur les plateformes prises en charge (iOS, UWP), tandis que les plateformes non prises en charge sont en cours de remplacement par une représentation sous forme de chaîne de l' `View` objet (Android). Dans les deux cas, le contenu est affiché horizontalement à côté du cercle de la case d’option.
- Lorsqu’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est appliqué à un [`RadioButton`](xref:Xamarin.Forms.RadioButton) , un [`View`](xref:Xamarin.Forms.View) peut être assigné à la `RadioButton.Content` propriété sur toutes les plateformes. Pour plus d’informations, consultez [redéfinir l’apparence du RadioButton](#redefine-radiobutton-appearance).

### <a name="display-string-based-content"></a>Afficher le contenu basé sur une chaîne

Un [`RadioButton`](xref:Xamarin.Forms.RadioButton) affiche le texte lorsque la `Content` propriété est assignée à un `string` :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat" />
    <RadioButton Content="Dog" />
    <RadioButton Content="Elephant" />
    <RadioButton Content="Monkey"
                 IsChecked="true" />
</StackLayout>
```

Dans cet exemple, [`RadioButton`](xref:Xamarin.Forms.RadioButton) les objets sont regroupés implicitement dans le même conteneur parent. Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran des cases d’option basées sur du texte](radiobutton-images/radiobuttons-text.png "Cases d’option basées sur du texte")

### <a name="display-arbitrary-content"></a>Afficher du contenu arbitraire

Sur iOS et UWP, un [`RadioButton`](xref:Xamarin.Forms.RadioButton) peut afficher du contenu arbitraire quand la `Content` propriété est assignée à un [`View`](xref:Xamarin.Forms.View) :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton>
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton>
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

Dans cet exemple, [`RadioButton`](xref:Xamarin.Forms.RadioButton) les objets sont regroupés implicitement dans le même conteneur parent. Ce code XAML produit l’apparence indiquée dans les captures d’écran suivantes :

![Capture d’écran des cases d’option basées sur des vues](radiobutton-images/radiobuttons-view.png "Cases d’option basées sur les vues")

Sur Android, [`RadioButton`](xref:Xamarin.Forms.RadioButton) les objets affichent une représentation sous forme de chaîne de l' [`View`](xref:Xamarin.Forms.View) objet qui a été défini en tant que contenu :

![Capture d’écran d’un RadioButton basé sur une vue sur Android](radiobutton-images/radiobutton-view-android.png "RadioButton basé sur les vues sur Android")

> [!NOTE]
> Lorsqu’un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est appliqué à un [`RadioButton`](xref:Xamarin.Forms.RadioButton) , un [`View`](xref:Xamarin.Forms.View) peut être assigné à la `RadioButton.Content` propriété sur toutes les plateformes. Pour plus d’informations, consultez [redéfinir l’apparence du RadioButton](#redefine-radiobutton-appearance).

## <a name="associate-values-with-radiobuttons"></a>Associer des valeurs à des cases d’option

Chaque [`RadioButton`](xref:Xamarin.Forms.RadioButton) objet possède une `Value` propriété, de type `object` , qui définit une valeur unique facultative à associer à la case d’option. Cela permet d’avoir une valeur `RadioButton` différente de son contenu et est particulièrement utile lorsque `RadioButton` des objets affichent des [`View`](xref:Xamarin.Forms.View) objets.

Le code XAML suivant montre comment définir les `Content` `Value` Propriétés et sur chaque [`RadioButton`](xref:Xamarin.Forms.RadioButton) objet :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <RadioButton Value="Cat">
        <RadioButton.Content>
            <Image Source="cat.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Dog">
        <RadioButton.Content>
            <Image Source="dog.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Elephant">
        <RadioButton.Content>
            <Image Source="elephant.png" />
        </RadioButton.Content>
    </RadioButton>
    <RadioButton Value="Monkey">
        <RadioButton.Content>
            <Image Source="monkey.png" />
        </RadioButton.Content>
    </RadioButton>
</StackLayout>
```

Dans cet exemple, chaque [`RadioButton`](xref:Xamarin.Forms.RadioButton) a un [`Image`](xref:Xamarin.Forms.Image) comme son contenu, tout en définissant une valeur basée sur une chaîne. Cela permet d’identifier facilement la valeur de la case d’option cochée.

## <a name="group-radiobuttons"></a>Groupe de cases d’option

Les cases d’option fonctionnent dans des groupes et il existe trois approches de regroupement des cases d’option :

- Placez-les dans le même conteneur parent. C’est ce que l’on appelle le regroupement *implicite* .
- Affectez `GroupName` à la propriété de chaque case d’option du groupe la même valeur. C’est ce que l’on appelle le regroupement *explicite* .
- Définissez la `RadioButtonGroup.GroupName` propriété jointe sur un conteneur parent qui, à son tour, définit la `GroupName` propriété de tous les [`RadioButton`](xref:Xamarin.Forms.RadioButton) objets dans le conteneur. Cette fonction est également appelée regroupement *explicite* .

> [!IMPORTANT]
> [`RadioButton`](xref:Xamarin.Forms.RadioButton) les objets n’ont pas besoin d’appartenir au même parent pour être regroupés. Ils s’excluent mutuellement, à condition qu’ils partagent un nom de groupe.

### <a name="explicit-grouping-with-the-groupname-property"></a>Regroupement explicite avec la propriété GroupName

L’exemple de code XAML suivant illustre le regroupement explicite [`RadioButton`](xref:Xamarin.Forms.RadioButton) d’objets en définissant leurs `GroupName` Propriétés :

```xaml
<Label Text="What's your favorite color?" />
<RadioButton Text="Red"
             GroupName="colors" />
<RadioButton Text="Green"
             GroupName="colors" />
<RadioButton Text="Blue"
             GroupName="colors" />
<RadioButton Text="Other"
             GroupName="colors" />
```

Dans cet exemple, chacun [`RadioButton`](xref:Xamarin.Forms.RadioButton) s’exclut mutuellement, car il partage la même `GroupName` valeur.

### <a name="explicit-grouping-with-the-radiobuttongroupgroupname-attached-property"></a>Regroupement explicite avec la propriété jointe RadioButtonGroup. GroupName

La `RadioButtonGroup` classe définit une `GroupName` propriété jointe, de type `string` , qui peut être définie sur un `Layout<View>` objet. Cela permet de faire en sorte que toute disposition soit transformée en groupe de cases d’option :

```xaml
<StackLayout RadioButtonGroup.GroupName="colors">
    <Label Text="What's your favorite color?" />
    <RadioButton Content="Red" />
    <RadioButton Content="Green" />
    <RadioButton Content="Blue" />
    <RadioButton Content="Other" />
</StackLayout>
```

Dans cet exemple, [`RadioButton`](xref:Xamarin.Forms.RadioButton) la propriété de chaque dans la [`StackLayout`](xref:Xamarin.Forms.StackLayout) est `GroupName` définie sur `fruits` et s’exclut mutuellement.

> [!NOTE]
> Quand un `Layout<View` objet> qui définit la `RadioButtonGroup.GroupName` propriété jointe contient un [`RadioButton`](xref:Xamarin.Forms.RadioButton) qui définit sa `GroupName` propriété, la valeur de la `RadioButton.GroupName` propriété est prioritaire.

## <a name="respond-to-radiobutton-state-changes"></a>Répondre aux changements d’État du RadioButton

Les cases d’option peuvent avoir l’un des deux états suivants : activé ou désactivé. Quand une case d’option est cochée, sa `IsChecked` propriété est `true` . Quand une case d’option est décochée, sa `IsChecked` propriété est `false` . Une case d’option peut être désactivée en appuyant sur une autre case d’option du même groupe, mais elle ne peut pas être désactivée en la retapant. Toutefois, vous pouvez désactiver une case d’option par programmation en affectant la valeur `false` à sa propriété `IsChecked`.

### <a name="respond-to-an-event-firing"></a>Répondre à un déclenchement d’événement

Lorsque la `IsChecked` propriété change, par le biais de la manipulation de l’utilisateur ou de la programmation, l' `CheckedChanged` événement se déclenche. Un gestionnaire d’événements pour cet événement peut être enregistré pour répondre à la modification :

```xaml
<RadioButton Text="Red"
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

L' `sender` argument est le [`RadioButton`](xref:Xamarin.Forms.RadioButton) responsable de cet événement. Vous pouvez l’utiliser pour accéder à l' [`RadioButton`](xref:Xamarin.Forms.RadioButton) objet, ou pour faire la distinction entre plusieurs `RadioButton` objets qui partagent le même `CheckedChanged` Gestionnaire d’événements.

### <a name="respond-to-a-property-change"></a>Répondre à une modification de propriété

La `RadioButtonGroup` classe définit une `SelectedValue` propriété jointe, de type `object` , qui peut être définie sur un `Layout<View>` objet. Cette propriété jointe représente la valeur de vérifiée [`RadioButton`](xref:Xamarin.Forms.RadioButton) dans un groupe défini sur une disposition.

Lorsque la `IsChecked` propriété change, par l’intermédiaire de la manipulation de l’utilisateur ou par programme, la `RadioButtonGroup.SelectedValue` propriété jointe change également. Par conséquent, la `RadioButtonGroup.SelectedValue` propriété jointe peut être liée aux données d’une propriété qui stocke la sélection de l’utilisateur :

```xaml
<StackLayout RadioButtonGroup.GroupName="{Binding GroupName}"
             RadioButtonGroup.SelectedValue="{Binding Selection}">
    <Label Text="What's your favorite animal?" />
    <RadioButton Content="Cat"
                 Value="Cat" />
    <RadioButton Content="Dog"
                 Value="Dog" />
    <RadioButton Content="Elephant"
                 Value="Elephant" />
    <RadioButton Content="Monkey"
                 Value="Monkey"/>
    <Label x:Name="animalLabel">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="You have chosen:" />
                <Span Text="{Binding Selection}" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
</StackLayout>
```

Dans cet exemple, la valeur de la `RadioButtonGroup.GroupName` propriété jointe est définie par la `GroupName` propriété sur le contexte de liaison. De même, la valeur de la `RadioButtonGroup.SelectedValue` propriété jointe est définie par la `Selection` propriété sur le contexte de liaison. En outre, la `Selection` propriété est mise à jour avec la `Value` propriété de la activée [`RadioButton`](xref:Xamarin.Forms.RadioButton) .

## <a name="radiobutton-visual-states"></a>États visuels de RadioButton

[`RadioButton`](xref:Xamarin.Forms.RadioButton) les objets ont des `Checked` `Unchecked` États visuels et qui peuvent être utilisés pour lancer une modification visuelle quand un `RadioButton` est activé ou désactivé.

L’exemple de code XAML suivant montre comment définir un état visuel pour `Checked` les `Unchecked` États et :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="RadioButton">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CheckedStates">
                        <VisualState x:Name="Checked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Green" />
                                <Setter Property="Opacity"
                                        Value="1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="Unchecked">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Red" />
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout>
        <Label Text="What's your favorite mode of transport?" />
        <RadioButton Text="Car" />
        <RadioButton Text="Bike" />
        <RadioButton Text="Train" />
        <RadioButton Text="Walking" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, les [`Style`](xref:Xamarin.Forms.Style) objets cibles implicites [`RadioButton`](xref:Xamarin.Forms.RadioButton) . `Checked` [`VisualState`](xref:Xamarin.Forms.VisualState) Spécifie que lorsqu’un `RadioButton` est activé, sa `TextColor` propriété est définie sur Green avec la `Opacity` valeur 1. `Unchecked` `VisualState` Spécifie que lorsqu’un `RadioButton` est dans un État non vérifié, sa `TextColor` propriété est définie sur rouge avec une `Opacity` valeur de 0,5. Par conséquent, l’effet global est que lorsqu’un `RadioButton` est désactivé, il est rouge et partiellement transparent, et il est vert sans transparence lorsqu’il est activé :

![Capture d’écran des États visuels RadioButton](radiobutton-images/radiobuttons-visualstates.png "États visuels de RadioButton")

Pour plus d’informations sur les États visuels, consultez [ Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="redefine-radiobutton-appearance"></a>Redéfinir l’apparence du RadioButton

Par défaut, les [`RadioButton`](xref:Xamarin.Forms.RadioButton) objets utilisent des convertisseurs de plateforme pour utiliser des contrôles natifs sur les plateformes prises en charge. Toutefois, `RadioButton` la structure visuelle peut être redéfinie avec un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , afin que `RadioButton` les objets aient une apparence identique sur toutes les plateformes. Cela est possible, car la [`RadioButton`](xref:Xamarin.Forms.RadioButton) classe hérite de la [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) classe.

Le code XAML suivant montre un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) qui peut être utilisé pour redéfinir la structure visuelle des [`RadioButton`](xref:Xamarin.Forms.RadioButton) objets :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="RadioButtonTemplate">
            <Frame BorderColor="#F3F2F1"
                   BackgroundColor="#F3F2F1"
                   HasShadow="False"
                   HeightRequest="100"
                   WidthRequest="100"
                   HorizontalOptions="Start"
                   VerticalOptions="Start"
                   Padding="0">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CheckedStates">
                            <VisualState x:Name="Checked">
                                <VisualState.Setters>
                                    <Setter Property="BorderColor"
                                            Value="#FF3300" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="1" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Unchecked">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="#F3F2F1" />
                                    <Setter Property="BorderColor"
                                            Value="#F3F2F1" />
                                    <Setter TargetName="check"
                                            Property="Opacity"
                                            Value="0" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </VisualStateManager.VisualStateGroups>
                <Grid Margin="4"
                      WidthRequest="100">
                    <Grid WidthRequest="18"
                          HeightRequest="18"
                          HorizontalOptions="End"
                          VerticalOptions="Start">
                        <Ellipse Stroke="Blue"
                                 Fill="White"
                                 WidthRequest="16"
                                 HeightRequest="16"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                        <Ellipse x:Name="check"
                                 Fill="Blue"
                                 WidthRequest="8"
                                 HeightRequest="8"
                                 HorizontalOptions="Center"
                                 VerticalOptions="Center" />
                    </Grid>
                    <ContentPresenter />
                </Grid>
            </Frame>
        </ControlTemplate>

        <Style TargetType="RadioButton">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource RadioButtonTemplate}" />
        </Style>
    </ContentPage.Resources>
    <!-- Page content -->
</ContentPage>
```

Dans cet exemple, l’élément racine du [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) est un [`Frame`](xref:Xamarin.Forms.Frame) objet qui définit les `Checked` `Unchecked` États visuels et. L' `Frame` objet utilise une combinaison d' [`Grid`](xref:Xamarin.Forms.Grid) [`Ellipse`](xref:Xamarin.Forms.Shapes.Ellipse) objets, et [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) pour définir la structure visuelle d’un [`RadioButton`](xref:Xamarin.Forms.RadioButton) . L’exemple comprend également un style *implicite* qui assigne `RadioButtonTemplate` à la `ControlTemplate` propriété de tous les `RadioButton` objets de la page.

> [!NOTE]
> L' [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) objet marque l’emplacement dans la structure visuelle où le [`RadioButton`](xref:Xamarin.Forms.RadioButton) contenu sera affiché.

Le code XAML suivant montre les [`RadioButton`](xref:Xamarin.Forms.RadioButton) objets qui consomment [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) via le style *implicite* :

```xaml
<StackLayout>
    <Label Text="What's your favorite animal?" />
    <StackLayout RadioButtonGroup.GroupName="animals"
                 Orientation="Horizontal">
        <RadioButton Value="Cat">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="cat.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Cat"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Dog">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="dog.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Dog"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Elephant">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="elephant.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Elephant"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
        <RadioButton Value="Monkey">
            <RadioButton.Content>
                <StackLayout>
                    <Image Source="monkey.png"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand" />
                    <Label Text="Monkey"
                           HorizontalOptions="Center"
                           VerticalOptions="End" />
                </StackLayout>
            </RadioButton.Content>
        </RadioButton>
    </StackLayout>
</StackLayout>
```

Dans cet exemple, la structure visuelle définie pour chaque [`RadioButton`](xref:Xamarin.Forms.RadioButton) est remplacée par la structure visuelle définie dans le [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , et par conséquent, au moment de l’exécution, les objets dans le `ControlTemplate` deviennent partie intégrante de l’arborescence d’éléments visuels pour chacun `RadioButton` . En outre, le contenu de chaque `RadioButton` est remplacé par le [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) dans le modèle de contrôle. Cela donne l’apparence suivante `RadioButton` :

![Capture d’écran des cases d’option basées sur un modèle](radiobutton-images/radiobuttons-templated.png "Cases d’option basées sur un modèle")

Pour plus d’informations sur les modèles de contrôle, consultez [ Xamarin.Forms modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="disable-a-radiobutton"></a>Désactiver un RadioButton

Parfois, une application entre dans un État où une `RadioButton` vérification n’est pas une opération valide. Dans ce cas, le `RadioButton` peut être désactivé en affectant `IsEnabled` à sa propriété la valeur `false` .

## <a name="related-links"></a>Liens connexes

- [Démonstrations RadioButton (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-radiobuttondemos/)
- [Xamarin.Forms Bouton](~/xamarin-forms/user-interface/button.md)
- [Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md)

---
title: Liaisons relatives Xamarin.Forms
description: Cet article explique comment créer des liaisons relatives en utilisant l’extension de balisage RelativeSource pour définir la source contraignante par rapport à la position de l’objectif contraignant.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 63ce27fc871da12eabb1baad568af167c860926f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75955811"
---
# <a name="xamarinforms-relative-bindings"></a>Liaisons relatives Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Les liaisons relatives offrent la possibilité de définir la source contraignante par rapport à la position de l’objectif contraignant. Ils sont créés avec l’extension `RelativeSource` `Source` de balisage, et définis comme la propriété d’une expression contraignante.

L’extension `RelativeSource` de balisage est prise en charge par la `RelativeSourceExtension` classe, qui définit les propriétés suivantes :

- `Mode`, de `RelativeBindingSourceMode`type , décrit l’emplacement de la source de liaison par rapport à la position de la cible de liaison.
- `AncestorLevel`, de `int`type , un niveau d’ancêtre optionnel à rechercher, lorsque la `Mode` propriété est `FindAncestor`. Un `AncestorLevel` `n` des `n-1` exemples `AncestorType`de skips de la .
- `AncestorType`, de `Type`type , le type d’ancêtre à `Mode` rechercher, lorsque la propriété est `FindAncestor`.

> [!NOTE]
> Le parser XAML `RelativeSourceExtension` permet à la classe `RelativeSource`d’être abrégée comme .

La `Mode` propriété doit être réglée `RelativeBindingSourceMode` à l’un des membres de recensement :

- `TemplatedParent`indique l’élément auquel le modèle, dans lequel l’élément lié existe, est appliqué. Pour plus d’informations, voir [Bind à un parent modélité](#bind-to-a-templated-parent).
- `Self`indique l’élément sur lequel la liaison est définie, vous permettant de lier une propriété de cet élément à une autre propriété sur le même élément. Pour plus d’informations, voir [Bind to self](#bind-to-self).
- `FindAncestor`indique l’ancêtre dans l’arbre visuel de l’élément lié. Ce mode doit être utilisé pour lier à un `AncestorType` contrôle d’ancêtre représenté par la propriété. Pour plus d’informations, voir [Bind to an ancestor](#bind-to-an-ancestor).
- `FindAncestorBindingContext`indique `BindingContext` l’ancêtre dans l’arbre visuel de l’élément lié. Ce mode doit être utilisé `BindingContext` pour lier à l’ancêtre représenté par la `AncestorType` propriété. Pour plus d’informations, voir [Bind to an ancestor](#bind-to-an-ancestor).

La `Mode` propriété est la `RelativeSourceExtension` propriété de contenu de la classe. Par conséquent, pour les expressions de balisage XAML `Mode=` exprimées avec des accolades bouclées, vous pouvez éliminer la partie de l’expression.

Pour plus d’informations sur les extensions de balisage Xamarin.Forms, voir [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Se lier à soi-même

Le `Self` mode de liaison relative est utilisé lier une propriété d’un élément à une autre propriété sur le même élément:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

Dans cet [`BoxView`](xref:Xamarin.Forms.BoxView) exemple, `WidthRequest` la propriété fixe sa `HeightRequest` propriété à une `WidthRequest` taille fixe, et la propriété se lie à la propriété. Par conséquent, les deux propriétés sont égales et donc un carré est dessiné:

[![Capture d’écran d’une liaison relative en mode Self, sur iOS et Android](relative-bindings-images/self-relative-binding.png "Mode de liaison relatif à l’auto-parent")](relative-bindings-images/self-relative-binding-large.png#lightbox "Mode de liaison relatif à l’auto-parent")

> [!IMPORTANT]
> Lorsque vous lierz une propriété d’un élément à une autre propriété sur le même élément, les propriétés doivent être du même type. Alternativement, vous pouvez spécifier un convertisseur sur la liaison pour convertir la valeur.

Une utilisation commune de ce mode de `BindingContext` liaison est définie par un objet à une propriété sur lui-même. Le code suivant montre un exemple de ceci :

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la `BindingContext` page est `DefaultViewModel` définie à la propriété de lui-même. Cette propriété est définie dans le fichier de code-derrière pour la page, et fournit une instance de viewmodel. Les [`ListView`](xref:Xamarin.Forms.ListView) liaisons `Employees` à la propriété du viewmodel.

## <a name="bind-to-an-ancestor"></a>Lier à un ancêtre

Les `FindAncestor` `FindAncestorBindingContext` modes de liaison relatifs sont utilisés pour se lier aux éléments parentaux, d’un certain type, dans l’arbre visuel. Le `FindAncestor` mode est utilisé pour se lier à [`Element`](xref:Xamarin.Forms.Element) un élément parent, qui dérive du type. Le `FindAncestorBindingContext` mode est utilisé `BindingContext` pour se lier à l’élément parent.

> [!WARNING]
> La `AncestorType` propriété doit être `Type` réglée `FindAncestor` à `FindAncestorBindingContext` un lors de `XamlParseException` l’utilisation des modes de liaison relative et relative, sinon un est jeté.

Si `Mode` la propriété n’est pas `AncestorType` explicitement définie, la [`Element`](xref:Xamarin.Forms.Element) mise en place `Mode` de `FindAncestor`la propriété à un type qui dérive de fixer implicitement la propriété à . De même, `AncestorType` la fixation de la `Element` propriété à un `Mode` type `FindAncestorBindingContext`qui ne dérive pas de fixer implicitement la propriété à .

> [!NOTE]
> Les liaisons relatives `FindAncestorBindingContext` qui utilisent le mode `BindingContext` seront réappliquées lorsque les ancêtres changeront.

Le XAML suivant montre `Mode` un exemple où `FindAncestorBindingContext`la propriété sera implicitement définie à:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Label Text="{Binding Fullname}"
                                   VerticalOptions="Center" />
                            <Button Text="Delete"
                                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeleteEmployeeCommand}"
                                    CommandParameter="{Binding}"
                                    HorizontalOptions="EndAndExpand" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Dans cet exemple, la `BindingContext` page est `DefaultViewModel` définie à la propriété de lui-même. Cette propriété est définie dans le fichier de code-derrière pour la page, et fournit une instance de viewmodel. Les [`ListView`](xref:Xamarin.Forms.ListView) liaisons `Employees` à la propriété du viewmodel. Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui définit l’apparence `ListView`de chaque [`Button`](xref:Xamarin.Forms.Button)élément dans le , contient un . La propriété `Command` du bouton est `DeleteEmployeeCommand` liée au modèle de vue de son parent. L’écoute d’un `Button` employé supprime :

[![Capture d’écran d’une liaison relative en mode FindAncestor, sur iOS et Android](relative-bindings-images/findancestor-relative-binding.png "Mode de liaison relative FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Mode de liaison relative FindAncestor")

En outre, `AncestorLevel` la propriété facultative peut aider à désambiguer la recherche ancêtre dans les scénarios où il ya peut-être plus d’un ancêtre de ce type dans l’arbre visuel:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

Dans cet exemple, la `Label.Text` propriété `Text` se lie [`Entry`](xref:Xamarin.Forms.Entry) à la propriété de la seconde qui est rencontrée sur la trajectoire ascendante, à partir de l’élément cible de la liaison.

> [!NOTE]
> La `AncestorLevel` propriété doit être réglée à 1 pour trouver l’ancêtre le plus proche de l’élément cible de liaison.

## <a name="bind-to-a-templated-parent"></a>Lien vers un parent modélé

Le `TemplatedParent` mode de liaison relative est utilisé pour lier à partir d’un modèle de contrôle à l’instance d’objet de temps d’exécution à laquelle le modèle est appliqué (connu sous le nom de parent modéliste). Ce mode ne s’applique que si la liaison relative est `TemplateBinding`dans un modèle de contrôle, et est similaire à la définition d’un .

Le XAML suivant montre `TemplatedParent` un exemple du mode de liaison relative :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                   BackgroundColor="{Binding CardColor}"
                   BorderColor="{Binding BorderColor}"
                   ...>
                <Grid>
                    ...
                    <Label Text="{Binding CardTitle}"
                           ... />
                    <BoxView BackgroundColor="{Binding BorderColor}"
                             ... />
                    <Label Text="{Binding CardDescription}"
                           ... />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout>        
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

Dans cet exemple, le [`Frame`](xref:Xamarin.Forms.Frame), qui `ControlTemplate`est l’élément racine de la , a son `BindingContext` ensemble à l’instance d’objet de temps d’exécution à laquelle le modèle est appliqué. Par conséquent, les `Frame` enfants et ses enfants `CardView` résolvent leurs expressions contraignantes par rapport aux propriétés de chaque objet :

[![Capture d’écran d’une liaison relative relative en mode TemplatedParent, sur iOS et Android](relative-bindings-images/templatedparent-relative-binding.png "Modélementparent mode de liaison relative")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modélementparent mode de liaison relative")

Pour plus d’informations sur les modèles de contrôle, voir [Xamarin.Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-template.md)

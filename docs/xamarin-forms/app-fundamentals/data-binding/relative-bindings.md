---
title: Xamarin.FormsLiaisons relatives
description: Cet article explique comment créer des liaisons relatives à l’aide de l’extension de balisage RelativeSource pour définir la source de liaison par rapport à la position de la cible de liaison.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d4e2696e6027f07b7b8e638cd1e0f1d65a5503d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139709"
---
# <a name="xamarinforms-relative-bindings"></a>Xamarin.FormsLiaisons relatives

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Les liaisons relatives fournissent la possibilité de définir la source de liaison par rapport à la position de la cible de liaison. Elles sont créées avec l' `RelativeSource` extension de balisage et définies en tant que `Source` propriété d’une expression de liaison.

L' `RelativeSource` extension de balisage est prise en charge par la `RelativeSourceExtension` classe, qui définit les propriétés suivantes :

- `Mode`, de type `RelativeBindingSourceMode` , décrit l’emplacement de la source de liaison par rapport à la position de la cible de liaison.
- `AncestorLevel`, de type `int` , un niveau d’ancêtre facultatif à rechercher, lorsque la `Mode` propriété a la valeur `FindAncestor` . Un `AncestorLevel` de `n` ignore `n-1` les instances de `AncestorType` .
- `AncestorType`, de type `Type` , le type d’ancêtre à rechercher, lorsque la `Mode` propriété a la valeur `FindAncestor` .

> [!NOTE]
> L’analyseur XAML permet `RelativeSourceExtension` à la classe d’être abrégée comme `RelativeSource` .

La `Mode` propriété doit être définie sur l’un des `RelativeBindingSourceMode` membres de l’énumération :

- `TemplatedParent`indique l’élément auquel le modèle, dans lequel se trouve l’élément lié, est appliqué. Pour plus d’informations, consultez [lier à un parent basé sur un modèle](#bind-to-a-templated-parent).
- `Self`indique l’élément sur lequel la liaison est définie, ce qui vous permet de lier une propriété de cet élément à une autre propriété sur le même élément. Pour plus d’informations, consultez [lier à soi-même](#bind-to-self).
- `FindAncestor`indique l’ancêtre dans l’arborescence d’éléments visuels de l’élément lié. Ce mode doit être utilisé pour établir une liaison à un contrôle ancêtre représenté par la `AncestorType` propriété. Pour plus d’informations, consultez [lier à un ancêtre](#bind-to-an-ancestor).
- `FindAncestorBindingContext`indique le `BindingContext` de l’ancêtre dans l’arborescence d’éléments visuels de l’élément lié. Ce mode doit être utilisé pour établir une liaison avec `BindingContext` un ancêtre représenté par la `AncestorType` propriété. Pour plus d’informations, consultez [lier à un ancêtre](#bind-to-an-ancestor).

La `Mode` propriété est la propriété de contenu de la `RelativeSourceExtension` classe. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Mode=` partie de l’expression.

Pour plus d’informations sur les Xamarin.Forms extensions de balisage, consultez [extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Lier à soi-même

Le `Self` mode de liaison relatif est utilisé pour lier une propriété d’un élément à une autre propriété sur le même élément :

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

Dans cet exemple, le [`BoxView`](xref:Xamarin.Forms.BoxView) définit sa `WidthRequest` propriété sur une taille fixe, et la `HeightRequest` propriété est liée à la `WidthRequest` propriété. Par conséquent, les deux propriétés sont égales et un carré est dessiné :

[![Capture d’écran d’une liaison relative en mode auto, sur iOS et Android](relative-bindings-images/self-relative-binding.png "Mode de liaison auto-relatif")](relative-bindings-images/self-relative-binding-large.png#lightbox "Mode de liaison auto-relatif")

> [!IMPORTANT]
> Lors de la liaison d’une propriété d’un élément à une autre propriété sur le même élément, les propriétés doivent être du même type. Vous pouvez également spécifier un convertisseur sur la liaison pour convertir la valeur.

Une utilisation courante de ce mode de liaison consiste à définir un objet `BindingContext` sur une propriété sur lui-même. Le code suivant en est un exemple :

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

Dans cet exemple, la `BindingContext` propriété de la page est définie sur la `DefaultViewModel` propriété d’elle-même. Cette propriété est définie dans le fichier code-behind de la page et fournit une instance ViewModel. [`ListView`](xref:Xamarin.Forms.ListView)Crée une liaison avec la `Employees` propriété du ViewModel.

## <a name="bind-to-an-ancestor"></a>Lier à un ancêtre

Les `FindAncestor` `FindAncestorBindingContext` modes de liaison relatifs et sont utilisés pour lier des éléments parents d’un certain type dans l’arborescence d’éléments visuels. Le `FindAncestor` mode est utilisé pour établir une liaison à un élément parent, qui dérive du [`Element`](xref:Xamarin.Forms.Element) type. Le `FindAncestorBindingContext` mode est utilisé pour établir une liaison avec le `BindingContext` d’un élément parent.

> [!WARNING]
> La `AncestorType` propriété doit être définie sur un `Type` lors de l’utilisation des `FindAncestor` `FindAncestorBindingContext` modes de liaison et relatifs, sinon une `XamlParseException` exception est levée.

Si la `Mode` propriété n’est pas définie explicitement, l’affectation à la `AncestorType` propriété d’un type qui dérive de [`Element`](xref:Xamarin.Forms.Element) définit implicitement la `Mode` propriété avec la valeur `FindAncestor` . De même, si vous affectez `AncestorType` à la propriété un type qui ne dérive pas de, `Element` la propriété est implicitement définie `Mode` à `FindAncestorBindingContext` .

> [!NOTE]
> Les liaisons relatives qui utilisent le `FindAncestorBindingContext` mode sont réappliquées lorsque le `BindingContext` de tous les ancêtres change.

Le code XAML suivant montre un exemple dans lequel la `Mode` propriété est implicitement définie sur `FindAncestorBindingContext` :

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

Dans cet exemple, la `BindingContext` propriété de la page est définie sur la `DefaultViewModel` propriété d’elle-même. Cette propriété est définie dans le fichier code-behind de la page et fournit une instance ViewModel. [`ListView`](xref:Xamarin.Forms.ListView)Crée une liaison avec la `Employees` propriété du ViewModel. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), Qui définit l’apparence de chaque élément dans le `ListView` , contient un [`Button`](xref:Xamarin.Forms.Button) . La propriété du bouton `Command` est liée à `DeleteEmployeeCommand` dans le ViewModel de son parent. En appuyant sur, vous `Button` supprimez un employé :

[![Capture d’écran d’une liaison relative en mode FindAncestor, sur iOS et Android](relative-bindings-images/findancestor-relative-binding.png "Mode de liaison relative à FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Mode de liaison relative à FindAncestor")

En outre, la `AncestorLevel` propriété facultative peut aider à lever l’ambiguïté dans les scénarios où il y a peut-être plus d’un ancêtre de ce type dans l’arborescence d’éléments visuels :

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

Dans cet exemple, la `Label.Text` propriété est liée à la `Text` propriété du deuxième [`Entry`](xref:Xamarin.Forms.Entry) élément rencontré sur le chemin d’accès vers le haut, à partir de l’élément cible de la liaison.

> [!NOTE]
> La `AncestorLevel` propriété doit avoir la valeur 1 pour Rechercher l’ancêtre le plus proche de l’élément cible de liaison.

## <a name="bind-to-a-templated-parent"></a>Lier à un parent basé sur un modèle

Le `TemplatedParent` mode de liaison relatif est utilisé pour effectuer une liaison à partir d’un modèle de contrôle à l’instance de l’objet d’exécution à laquelle le modèle est appliqué (connu sous le nom de parent basé sur un modèle). Ce mode est applicable uniquement si la liaison relative est dans un modèle de contrôle et est semblable à la définition d’un `TemplateBinding` .

Le code XAML suivant illustre un exemple du `TemplatedParent` mode de liaison relatif :

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

Dans cet exemple, le [`Frame`](xref:Xamarin.Forms.Frame) , qui est l’élément racine de `ControlTemplate` , a son `BindingContext` défini sur l’instance d’objet runtime à laquelle le modèle est appliqué. Par conséquent, le `Frame` et ses enfants résolvent leurs expressions de liaison par rapport aux propriétés de chaque `CardView` objet :

[![Capture d’écran d’une liaison relative en mode de TemplatedParent, sur iOS et Android](relative-bindings-images/templatedparent-relative-binding.png "Mode de liaison relatif à TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Mode de liaison relatif à TemplatedParent")

Pour plus d’informations sur les modèles de contrôle, consultez [ Xamarin.Forms modèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Liens connexes

- [Démonstrations de liaison de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsModèles de contrôle](~/xamarin-forms/app-fundamentals/templates/control-template.md)

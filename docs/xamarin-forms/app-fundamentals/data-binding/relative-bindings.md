---
title: Liaisons relatives Xamarin. Forms
description: Cet article explique comment créer des liaisons relatives à l’aide de l’extension de balisage RelativeSource pour définir la source de liaison par rapport à la position de la cible de liaison.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: e115014728cce9252a92740b6db5beab582f61ed
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489867"
---
# <a name="xamarinforms-relative-bindings"></a>Liaisons relatives Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Les liaisons relatives fournissent la possibilité de définir la source de liaison par rapport à la position de la cible de liaison. Elles sont créées avec l’extension de balisage `RelativeSource` et définies comme `Source` propriété d’une expression de liaison.

L’extension de balisage `RelativeSource` est prise en charge par la classe `RelativeSourceExtension`, qui définit les propriétés suivantes :

- `Mode`, de type `RelativeBindingSourceMode`, décrit l’emplacement de la source de liaison par rapport à la position de la cible de liaison.
- `AncestorLevel`, de type `int`, un niveau d’ancêtre facultatif à rechercher, lorsque la propriété `Mode` est `FindAncestor`. Une `AncestorLevel` de `n` ignore `n-1` instances du `AncestorType`.
- `AncestorType`, de type `Type`, le type d’ancêtre à rechercher, lorsque la propriété `Mode` est `FindAncestor`.

> [!NOTE]
> L’analyseur XAML permet à la classe `RelativeSourceExtension` d’être abrégée en `RelativeSource`.

La propriété `Mode` doit être définie sur l’un des membres de l’énumération `RelativeBindingSourceMode` :

- `TemplatedParent` indique l’élément auquel le modèle, dans lequel se trouve l’élément lié, est appliqué. Pour plus d’informations, consultez [lier à un parent basé sur un modèle](#bind-to-a-templated-parent).
- `Self` indique l’élément sur lequel la liaison est définie, ce qui vous permet de lier une propriété de cet élément à une autre propriété sur le même élément. Pour plus d’informations, consultez [lier à soi-même](#bind-to-self).
- `FindAncestor` indique l’ancêtre dans l’arborescence d’éléments visuels de l’élément lié. Ce mode doit être utilisé pour établir une liaison à un contrôle ancêtre représenté par la propriété `AncestorType`. Pour plus d’informations, consultez [lier à un ancêtre](#bind-to-an-ancestor).
- `FindAncestorBindingContext` indique la `BindingContext` de l’ancêtre dans l’arborescence d’éléments visuels de l’élément lié. Ce mode doit être utilisé pour établir une liaison avec l' `BindingContext` d’un ancêtre représenté par la propriété `AncestorType`. Pour plus d’informations, consultez [lier à un ancêtre](#bind-to-an-ancestor).

La propriété `Mode` est la propriété de contenu de la classe `RelativeSourceExtension`. Par conséquent, pour les expressions de balisage XAML exprimées à l’aide d’accolades, vous pouvez éliminer la `Mode=` partie de l’expression.

Pour plus d’informations sur les extensions de balisage Xamarin. Forms, consultez [extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Lier à soi-même

Le mode de liaison relative `Self` est utilisé pour lier une propriété d’un élément à une autre propriété sur le même élément :

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

Dans cet exemple, le [`BoxView`](xref:Xamarin.Forms.BoxView) affecte à sa propriété `WidthRequest` une taille fixe, et la propriété `HeightRequest` est liée à la propriété `WidthRequest`. Par conséquent, les deux propriétés sont égales et un carré est dessiné :

[![Capture d’écran d’une liaison relative en mode auto, sur iOS et Android](relative-bindings-images/self-relative-binding.png "Mode de liaison auto-relatif")](relative-bindings-images/self-relative-binding-large.png#lightbox "Mode de liaison auto-relatif")

> [!IMPORTANT]
> Lors de la liaison d’une propriété d’un élément à une autre propriété sur le même élément, les propriétés doivent être du même type. Vous pouvez également spécifier un convertisseur sur la liaison pour convertir la valeur.

Une utilisation courante de ce mode de liaison consiste à définir l' `BindingContext` d’un objet sur une propriété sur lui-même. Le code suivant en est un exemple :

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

Dans cet exemple, le `BindingContext` de la page est défini sur la propriété `DefaultViewModel` de lui-même. Cette propriété est définie dans le fichier code-behind de la page et fournit une instance ViewModel. Le [`ListView`](xref:Xamarin.Forms.ListView) est lié à la propriété `Employees` du ViewModel.

## <a name="bind-to-an-ancestor"></a>Lier à un ancêtre

Les modes de liaison relative `FindAncestor` et `FindAncestorBindingContext` sont utilisés pour lier des éléments parents d’un certain type dans l’arborescence d’éléments visuels. Le mode de `FindAncestor` est utilisé pour établir une liaison à un élément parent, qui dérive du type de [`Element`](xref:Xamarin.Forms.Element) . Le mode de `FindAncestorBindingContext` est utilisé pour effectuer une liaison à la `BindingContext` d’un élément parent.

> [!WARNING]
> La propriété `AncestorType` doit avoir la valeur d’une `Type` lors de l’utilisation de la `FindAncestor` et `FindAncestorBindingContext` modes de liaison relatifs, dans le cas contraire, une `XamlParseException` est levée.

Si la propriété `Mode` n’est pas définie explicitement, la définition de la propriété `AncestorType` sur un type qui dérive de [`Element`](xref:Xamarin.Forms.Element) définira implicitement la propriété `Mode` sur `FindAncestor`. De même, si vous affectez à la propriété `AncestorType` un type qui ne dérive pas de `Element`, la propriété `Mode` est implicitement définie sur `FindAncestorBindingContext`.

> [!NOTE]
> Les liaisons relatives qui utilisent le mode de `FindAncestorBindingContext` sont réappliquées lorsque la `BindingContext` de tous les ancêtres change.

Le code XAML suivant montre un exemple dans lequel la propriété `Mode` est implicitement définie sur `FindAncestorBindingContext`:

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

Dans cet exemple, le `BindingContext` de la page est défini sur la propriété `DefaultViewModel` de lui-même. Cette propriété est définie dans le fichier code-behind de la page et fournit une instance ViewModel. Le [`ListView`](xref:Xamarin.Forms.ListView) est lié à la propriété `Employees` du ViewModel. Le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), qui définit l’apparence de chaque élément dans le `ListView`, contient un [`Button`](xref:Xamarin.Forms.Button). La propriété `Command` du bouton est liée à la `DeleteEmployeeCommand` dans le ViewModel de son parent. En appuyant sur une `Button`, vous supprimez un employé :

[![Capture d’écran d’une liaison relative en mode FindAncestor, sur iOS et Android](relative-bindings-images/findancestor-relative-binding.png "Mode de liaison relative à FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Mode de liaison relative à FindAncestor")

En outre, la propriété facultative `AncestorLevel` peut aider à lever l’ambiguïté dans les scénarios où il y a peut-être plus d’un ancêtre de ce type dans l’arborescence d’éléments visuels :

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

Dans cet exemple, la propriété `Label.Text` est liée à la propriété `Text` du deuxième [`Entry`](xref:Xamarin.Forms.Entry) rencontré sur le chemin vers le haut, à partir de l’élément cible de la liaison.

> [!NOTE]
> La propriété `AncestorLevel` doit avoir la valeur 1 pour Rechercher l’ancêtre le plus proche de l’élément cible de liaison.

## <a name="bind-to-a-templated-parent"></a>Lier à un parent basé sur un modèle

Le mode de liaison relative `TemplatedParent` est utilisé pour effectuer une liaison à partir d’un modèle de contrôle à l’instance de l’objet d’exécution à laquelle le modèle est appliqué (connu sous le nom de parent basé sur un modèle). Ce mode est applicable uniquement si la liaison relative est dans un modèle de contrôle et est semblable à la définition d’un `TemplateBinding`.

Le code XAML suivant montre un exemple de `TemplatedParent` mode de liaison relative :

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

Dans cet exemple, le [`Frame`](xref:Xamarin.Forms.Frame), qui est l’élément racine du `ControlTemplate`, a son `BindingContext` défini sur l’instance de l’objet d’exécution à laquelle le modèle est appliqué. Par conséquent, le `Frame` et ses enfants résolvent leurs expressions de liaison par rapport aux propriétés de chaque objet `CardView` :

[![Capture d’écran d’une liaison relative en mode de TemplatedParent, sur iOS et Android](relative-bindings-images/templatedparent-relative-binding.png "Mode de liaison relatif à TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Mode de liaison relatif à TemplatedParent")

Pour plus d’informations sur les modèles de contrôle, consultez [modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Liens connexes

- [Démos des liaisons de données (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Modèles de contrôle Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)

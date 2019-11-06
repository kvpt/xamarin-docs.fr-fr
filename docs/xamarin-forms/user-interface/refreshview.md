---
title: Xamarin. Forms RefreshView
description: Xamarin. Forms RefreshView est un contrôle conteneur qui fournit des fonctionnalités pull pour actualiser le contenu défilant.
ms.prod: xamarin
ms.assetId: 58DBD23B-ADB9-40DA-B331-4DDB6E698990
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/19/2019
ms.openlocfilehash: e38987006025dad1c2ff49c3ea8916e2075d61d7
ms.sourcegitcommit: d1d4700b3b1b417a9d7b7da85ab5d28f8e8e599d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73649294"
---
# <a name="xamarinforms-refreshview"></a>Xamarin. Forms RefreshView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)

Le `RefreshView` est un contrôle conteneur qui fournit des fonctionnalités pull pour actualiser le contenu défilant. Par conséquent, l’enfant d’un `RefreshView` doit être un contrôle à défilement, tel que [`ScrollView`](xref:Xamarin.Forms.ScrollView), [`CollectionView`](xref:Xamarin.Forms.CollectionView)ou [`ListView`](xref:Xamarin.Forms.ListView).

`RefreshView` définit les propriétés suivantes :

- `Command`, de type `ICommand`, qui est exécuté lorsqu’une actualisation est déclenchée.
- `CommandParameter`, de type `object` : paramètre passé à la commande `Command`.
- `IsRefreshing`, de type `bool`, qui indique l’état actuel du `RefreshView`.
- `RefreshColor`, de type `Color`, la couleur du cercle de progression qui s’affiche pendant l’actualisation.

Ces propriétés sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et un style.

> [!NOTE]
> Sur la plateforme Windows universelle, la direction d’extraction d’une `RefreshView` peut être définie avec un spécifique à la plateforme. Pour plus d’informations, consultez [RefreshView pull direction](~/xamarin-forms/platform/windows/refreshview-pulldirection.md).

## <a name="create-a-refreshview"></a>Créer un RefreshView

L’exemple suivant montre comment instancier un `RefreshView` en XAML :

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <ScrollView>
        <FlexLayout Direction="Row"
                    Wrap="Wrap"
                    AlignItems="Center"
                    AlignContent="Center"
                    BindableLayout.ItemsSource="{Binding Items}"
                    BindableLayout.ItemTemplate="{StaticResource ColorItemTemplate}" />
    </ScrollView>
</RefreshView>
```

Vous pouvez également créer un `RefreshView` dans le code :

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

ScrollView scrollView = new ScrollView();
FlexLayout flexLayout = new FlexLayout { ... };
scrollView.Content = flexLayout;
refreshView.Content = scrollView;
```

Dans cet exemple, le `RefreshView` fournit la fonctionnalité d’actualisation de l’extraction à un [`ScrollView`](xref:Xamarin.Forms.ScrollView) dont l’enfant est un [`FlexLayout`](xref:Xamarin.Forms.FlexLayout). L' `FlexLayout` utilise une disposition pouvant être liée pour générer son contenu en liant à une collection d’éléments et définit l’apparence de chaque élément avec une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Pour plus d’informations sur les dispositions pouvant être liées, consultez [dispositions pouvant être liées dans Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

La valeur de la propriété `RefreshView.IsRefreshing` indique l’état actuel du `RefreshView`. Lorsqu’une actualisation est déclenchée par l’utilisateur, cette propriété effectue automatiquement la transition vers `true`. Une fois l’actualisation terminée, vous devez réinitialiser la propriété à `false`.

Lorsque l’utilisateur lance une actualisation, la `ICommand` définie par la propriété `Command` est exécutée, ce qui doit actualiser les éléments affichés. Une visualisation d’actualisation s’affiche pendant l’actualisation, qui se compose d’un cercle de progression animé :

[![Capture d’écran d’un RefreshView actualisant les données, sur iOS et Android](refreshview-images/default-progress-circle.png "RefreshView actualiser les données")](refreshview-images/default-progress-circle-large.png#lightbox "RefreshView actualiser les données")

> [!NOTE]
> La définition manuelle de la propriété `IsRefreshing` sur `true` déclenchera la visualisation de l’actualisation et exécutera la `ICommand` définie par la propriété `Command`.

## <a name="refreshview-appearance"></a>Apparence RefreshView

En plus des propriétés que `RefreshView` hérite de la classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , `RefreshView` définit également la propriété `RefreshColor`. Cette propriété peut être définie pour définir la couleur du cercle de progression qui s’affiche pendant l’actualisation :

```xaml
<RefreshView RefreshColor="Teal"
             ... />
```

La capture d’écran suivante montre une `RefreshView` avec la propriété `RefreshColor` définie :

[![Capture d’écran d’un RefreshView avec un cercle de progression bleu-vert, sur iOS et Android](refreshview-images/teal-progress-circle.png "RefreshView avec un cercle de progression bleu-vert")](refreshview-images/teal-progress-circle-large.png#lightbox "RefreshView avec un cercle de progression bleu-vert")

En outre, la propriété `BackgroundColor` peut être définie sur une [`Color`](xref:Xamarin.Forms.Color) qui représente la couleur d’arrière-plan du cercle de progression.

> [!NOTE]
> Sur iOS, la propriété `BackgroundColor` définit la couleur d’arrière-plan du `UIView` qui contient le cercle de progression.

## <a name="disable-a-refreshview"></a>Désactiver un RefreshView

Une application peut entrer dans un État dans lequel l’extraction à actualiser n’est pas une opération valide. Dans ce cas, l' `RefreshView` peut être désactivée en affectant à sa propriété `IsEnabled` la valeur `false`. Cela empêchera les utilisateurs de déclencher l’actualisation de l’extraction.

En guise d’alternative, lors de la définition de la propriété `Command`, le `CanExecute` délégué du `ICommand` peut être spécifié pour activer ou désactiver la commande.

## <a name="related-links"></a>Liens connexes

- [RefreshView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-refreshviewdemo/)
- [Dispositions pouvant être liées dans Xamarin. Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
- [Direction d’extraction RefreshView-spécifique à la plateforme](~/xamarin-forms/platform/windows/refreshview-pulldirection.md)

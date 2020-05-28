---
title: Xamarin.FormsNavigation dans le shell
description: Xamarin.FormsLes applications de l’interpréteur de commandes peuvent utiliser une expérience de navigation basée sur un URI qui permet de naviguer vers n’importe quelle page de l’application, sans avoir à suivre une hiérarchie de navigation définie.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e67d49f300a8a98ec5685c33abf98f5b2ded08ed
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132390"
---
# <a name="xamarinforms-shell-navigation"></a>Xamarin.FormsNavigation dans le shell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.FormsL’interpréteur de commandes comprend une expérience de navigation basée sur les URI qui utilise des itinéraires pour accéder à n’importe quelle page de l’application, sans devoir suivre une hiérarchie de navigation définie. De plus, l’utilisateur peut revenir en arrière sans avoir à visiter toutes les pages de la pile de navigation.

`Shell` définit les propriétés de navigation associées suivantes :

- `BackButtonBehavior`, de type `BackButtonBehavior` : une propriété qui définit le comportement du bouton Précédent.
- `CurrentItem`, de type `FlyoutItem` ; l’élément `FlyoutItem` sélectionné.
- `CurrentState`, de type `ShellNavigationState` : l’état actuel de la navigation de l’élément `Shell`.
- `Current`, de type `Shell` : un alias converti pour `Application.Current.MainPage`.

Les `BackButtonBehavior` `CurrentItem` Propriétés, et `CurrentState` sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que ces propriétés peuvent être des cibles de liaisons de données.

La navigation est effectuée en appelant la méthode `GoToAsync` à partir de la classe `Shell`. Juste avant la navigation, un événement `Navigating` est déclenché, puis un événement `Navigated` est déclenché à la fin de la navigation.

> [!NOTE]
> La navigation peut toujours être effectuée dans une Xamarin.Forms application Shell à l’aide de la propriété de [navigation](xref:Xamarin.Forms.NavigableElement.Navigation) . Pour plus d’informations, consultez [Navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="routes"></a>Itinéraires

La navigation est effectuée dans une application Shell en spécifiant un URI vers lequel naviguer. Les URI de navigation peuvent avoir trois composants :

- Un *itinéraire*, qui définit le chemin d’accès au contenu existant dans le cadre de la hiérarchie visuelle Shell.
- Une *page*. Les pages qui n’existent pas dans la hiérarchie visuelle Shell peuvent être transmises en mode push à la pile de navigation depuis n’importe où dans une application Shell. Par exemple, une page de détails d’un élément ne sera pas définie dans la hiérarchie visuelle Shell, mais elle peut être transmise en mode push à la pile de navigation selon les besoins.
- Un ou plusieurs *paramètres de requête*. Les paramètres de requête sont des paramètres qui peuvent être transmis à la page de destination lors de la navigation.

Lorsqu’un URI de navigation comprend les trois composants, la structure est : //route/page?queryParameters

### <a name="register-routes"></a>Inscrire des itinéraires

Les itinéraires peuvent être définis sur des objets `FlyoutItem`, `Tab` et `ShellContent` via leurs propriétés `Route` :

```xaml
<Shell ...>
    <FlyoutItem ...
                Route="animals">
        <Tab ...
             Route="domestic">
            <ShellContent ...
                          Route="cats" />
            <ShellContent ...
                          Route="dogs" />
        </Tab>
        <ShellContent ...
                      Route="monkeys" />
        <ShellContent ...
                      Route="elephants" />  
        <ShellContent ...
                      Route="bears" />
    </FlyoutItem>
    <ShellContent ...
                  Route="about" />                  
    ...
</Shell>
```

> [!NOTE]
> Tous les éléments dans la hiérarchie Shell sont associés à un itinéraire. Si un itinéraire n’est pas défini par le développeur, l’itinéraire est généré lors de l’exécution. Toutefois, la cohérence des itinéraires n’est pas garantie entre les sessions d’application.

Cet exemple, tiré de l’exemple d’application, crée la hiérarchie d’itinéraire suivante, qui peut être utilisée dans la navigation par programmation :

```
animals
  domestic
    cats
    dogs
  monkeys
  elephants
  bears
about
```

Afin d’accéder à l’objet `ShellContent` pour l’itinéraire `dogs`, l’URI de l’itinéraire absolu est `//animals/domestic/dogs`. De même, afin d’accéder à l’objet `ShellContent` pour l’itinéraire `about`, l’URI de l’itinéraire absolu est `//about`.

> [!IMPORTANT]
> Une exception `ArgumentException` sera levée au démarrage de l’application si un itinéraire en double est détecté. Cette exception est également levée si au moins deux routes au même niveau dans la hiérarchie partagent un nom de route.

#### <a name="register-page-routes"></a>Inscrire les itinéraires de pages

Dans le constructeur de la sous-classe Shell, ou tout autre emplacement qui s’exécute avant l’appel d’un itinéraire, des itinéraires supplémentaires peuvent être inscrits explicitement pour toutes les pages qui ne sont pas représentées dans la hiérarchie visuelle Shell :

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

Cet exemple inscrit des pages de détail sur l’élément, qui ne sont pas définies dans la sous-classe Shell, en tant qu’itinéraires. Ces pages sont ensuite accessibles à l’aide d’une navigation basée sur URI, depuis n’importe où dans l’application. Les itinéraires pour ces pages sont appelés *itinéraires globaux*.

> [!NOTE]
> Les pages dont les itinéraires ont été inscrits avec la méthode `Routing.RegisterRoute` peuvent être désinscrites avec la méthode `Routing.UnRegisterRoute`, si nécessaire.

Les pages peuvent également être inscrites sur des hiérarchies d’itinéraires différentes, si nécessaire :

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

Cet exemple active la navigation contextuelle dans les pages, dans laquelle l’accès à l’itinéraire `details` depuis la page pour l’itinéraire `monkeys` affiche l’élément `MonkeyDetailPage`. De même, l’accès à l’itinéraire `details` depuis la page pour l’itinéraire `elephants` affiche l’élément `ElephantDetailPage`.

> [!IMPORTANT]
> Une `ArgumentException` est levée si la méthode `Routing.RegisterRoute` tente d’enregistrer la même route dans au moins deux types différents.

## <a name="perform-navigation"></a>Effectuer la navigation

Pour effectuer la navigation, vous devez d’abord obtenir une référence à la sous-classe `Shell`. Cette référence peut être obtenue en associant la propriété `App.Current.MainPage` à un objet `Shell`, ou via la propriété `Shell.Current`. La navigation peut ensuite être effectuée en appelant la méthode `GoToAsync` sur l’objet `Shell`. Cette méthode permet d’accéder à un élément `ShellNavigationState` puis retourne un élément `Task` qui se termine à la fin de l’animation de navigation. L’objet `ShellNavigationState` est construit par la méthode `GoToAsync`, à partir d’un élément `string` ou `Uri`, et sa propriété `Location` est définie sur l’argument `string` ou `Uri`.

Lorsque vous accédez à un itinéraire depuis la hiérarchie visuelle Shell, aucune pile de navigation n’est créée. Cependant, lorsque vous accédez à une page qui ne figure pas dans la hiérarchie visuelle Shell, une pile de navigation est créée.

> [!NOTE]
> L’état actuel de la navigation de l’élément `Shell` peut être récupéré via la propriété `Shell.Current.CurrentState`, qui inclut l’URI de l’itinéraire affiché dans la propriété `Location`.

### <a name="absolute-routes"></a>Itinéraires absolus

La navigation peut être effectuée en spécifiant un URI absolu valide en tant qu’argument pour la méthode `GoToAsync` :

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

Cet exemple accède à la page pour l’itinéraire `monkeys`, avec l’itinéraire défini sur un objet `ShellContent`. L’objet `ShellContent` qui représente l’itinéraire `monkeys` est un enfant d’un objet `FlyoutItem`, dont l’itinéraire est `animals`.

### <a name="relative-routes"></a>Itinéraires relatifs

La navigation peut également être effectuée en spécifiant un URI relatif valide en tant qu’argument pour la méthode `GoToAsync`. Le système d’acheminement tente de faire correspondre l’URI à un objet `ShellContent`. Par conséquent, si tous les itinéraires d’une application sont uniques, la navigation peut être effectuée en spécifiant uniquement le nom d’itinéraire unique comme un URI relatif :

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

Cet exemple accède à la page pour l’itinéraire `monkeydetails`.

En outre, les formats d’itinéraires relatifs suivants sont pris en charge :

| Format | Description |
| --- | --- |
| //*Itinéraire* | La hiérarchie de l’itinéraire sera recherchée pour l’itinéraire spécifié, en amont de l’itinéraire actuellement affiché. |
| ///*Itinéraire* | La hiérarchie de l’itinéraire sera recherchée pour l’itinéraire spécifié, en aval de l’itinéraire actuellement affiché. |

#### <a name="contextual-navigation"></a>Navigation contextuelle

Les itinéraires relatifs permettent une navigation contextuelle. Par exemple, considérez la hiérarchie d’acheminement suivante :

```
monkeys
  details
bears
  details
```

Lorsque la page inscrite pour l’itinéraire `monkeys` s’affiche, l’accès à l’itinéraire `details` affichera la page inscrite pour l’itinéraire `monkeys/details`. De même, lorsque la page inscrite pour l’itinéraire `bears` s’affiche, l’accès à l’itinéraire `details` affichera la page inscrite pour l’itinéraire `bears/details`. Pour plus d’informations sur la façon d’inscrire les itinéraires dans cet exemple, consultez [Inscrire les itinéraires de pages](#register-page-routes).

### <a name="backwards-navigation"></a>Navigation vers l’arrière

La navigation vers l’arrière peut être effectuée en spécifiant « .. » comme argument de la `GotoAsync` méthode :

```csharp
await Shell.Current.GoToAsync("..");
```

La navigation vers l’arrière avec « .. » peut également être associée à un itinéraire, comme suit :

```csharp
await Shell.Current.GoToAsync("../route");
```

Dans cet exemple, l’effet global consiste à naviguer vers l’arrière, puis à naviguer vers l’itinéraire spécifié.

> [!IMPORTANT]
> Naviguer vers l’arrière et dans un itinéraire spécifié n’est possible que si la navigation vers l’arrière vous place à l’emplacement actuel dans la hiérarchie de routage pour accéder à l’itinéraire spécifié.

De même, il est possible de parcourir plusieurs fois, puis d’accéder à un itinéraire spécifié :

```csharp
await Shell.Current.GoToAsync("../../route");
```

Dans cet exemple, l’effet global consiste à parcourir deux fois en arrière, puis à naviguer vers l’itinéraire spécifié.

> [!NOTE]
> Les données peuvent également être passées lors de la navigation avec « .. ». Pour plus d’informations, consultez [transmettre des données](#pass-data).

### <a name="invalid-routes"></a>Itinéraires non valides

Les formats d’itinéraires suivants ne sont pas valides :

| Mettre en forme | Explication |
| --- | --- |
| *route* ou /*route* | Les itinéraires dans la hiérarchie visuelle ne peuvent pas être transmis en mode push à la pile de navigation. |
| //*page* ou ///*page* | Actuellement, les itinéraires globaux ne peuvent pas représenter la seule page de la pile de navigation. Par conséquent, l’acheminement absolu pour les itinéraires globaux n’est pas pris en charge. |

L’utilisation d’un de ces formats d’acheminement lève une exception `Exception`.

> [!IMPORTANT]
> La tentative d’accéder à un itinéraire inexistant lève une exception `ArgumentException`.

### <a name="debugging-navigation"></a>Débogage de la navigation

Certaines des classes Shell comportent l’élément `DebuggerDisplayAttribute`, qui spécifie comment le débogueur affiche une classe ou un champ. Cela peut faciliter le débogage des demandes de navigation en affichant les données relatives à la demande de navigation. Par exemple, la capture d’écran suivante montre les propriétés `CurrentItem` et `CurrentState` de l’objet `Shell.Current` :

![Capture d’écran du débogueur](navigation-images/debugger.png "Débogueur")

Dans cet exemple, la propriété `CurrentItem` de type `FlyoutItem` affiche le titre et l’itinéraire de l’objet `FlyoutItem`. De même, la propriété `CurrentState` de type `ShellNavigationState` affiche l’URI de l’itinéraire affiché dans l’application Shell.

### <a name="tab-class"></a>Classe Tab

La classe `Tab` définit une propriété `Stack` de type `IReadOnlyList<Page>` qui représente la pile de navigation actuellement transmise en mode push au sein de l’élément `Tab`. La classe fournit également les méthodes de navigation substituables suivantes :

- `GetNavigationStack`, retourne `IReadOnlyList<Page`>, la pile de navigation en cours.
- `OnInsertPageBefore`, appelée lorsque `INavigation.InsertPageBefore` est appelée.
- `OnPopAsync`, retourne `Task<Page>` et est appelée lorsque `INavigation.PopAsync` est appelée.
- `OnPopToRootAsync`, retourne `Task` et est appelée lorsque `INavigation.OnPopToRootAsync` est appelée.
- `OnPushAsync`, retourne `Task` et est appelée lorsque `INavigation.PushAsync` est appelée.
- `OnRemovePage`, appelée lorsque `INavigation.RemovePage` est appelée.

## <a name="navigation-events"></a>Événements de navigation

La classe `Shell` définit un événement `Navigating` déclenché quand la navigation est sur le point d’être effectuée, en raison de la navigation par programmation ou d’une intervention de l’utilisateur. L’objet `ShellNavigatingEventArgs` qui accompagne l’événement `Navigating` fournit les propriétés suivantes :

| Propriété | Type | Description |
|---|---|---|
| `Current` | `ShellNavigationState` | URI de la page actuelle. |
| `Source` | `ShellNavigationSource` | Le type de navigation qui s’est produit. |
| `Target` | `ShellNavigationState`  | URI représentant la destination de la navigation. |
| `CanCancel`  | `bool` | Valeur indiquant s’il est possible d’annuler la navigation. |
| `Cancelled`  | `bool` | Valeur indiquant si la navigation a été annulée. |

En outre, la classe `ShellNavigatingEventArgs` fournit une méthode `Cancel` qui peut être utilisée pour annuler la navigation.

> [!NOTE]
> L’événement `Navigated` est déclenché par la méthode substituable `OnNavigating` dans la classe `Shell`.

La classe `Shell` définit également un événement `Navigated` déclenché quand la navigation est terminée. L’objet `ShellNavigatedEventArgs` qui accompagne l’événement `Navigating` fournit les propriétés suivantes :

| Propriété | Type | Description |
|---|---|---|
| `Current` | `ShellNavigationState` | URI de la page actuelle. |
| `Previous`| `ShellNavigationState` | URI de la page précédente. |
| `Source`  | `ShellNavigationSource` | Le type de navigation qui s’est produit. |

> [!NOTE]
> L’événement `Navigating` est déclenché par la méthode substituable `OnNavigated` dans la classe `Shell`.

Les classes `ShellNavigatedEventArgs` et `ShellNavigatingEventArgs` comportent des propriétés `Source` de type `ShellNavigationSource`. Cette énumération fournit les valeurs suivantes :

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

Par conséquent, dans un gestionnaire pour l’événement `Navigating`, il est possible d’intercepter la navigation et d’effectuer des actions basées sur la source de navigation. Par exemple, le code suivant montre comment annuler la navigation en amont si les données sur la page ne sont pas enregistrées :

```csharp
void OnNavigating(object sender, ShellNavigatingEventArgs e)
{
    // Cancel back navigation if data is unsaved
    if (e.Source == ShellNavigationSource.Pop && !dataSaved)
    {
        e.Cancel();
    }
}
```

## <a name="pass-data"></a>Passer des données

Les données peuvent être passées comme paramètres de requête lorsque vous effectuez une navigation par programmation. Par exemple, le code suivant est exécuté dans l’exemple d’application lorsqu’un utilisateur sélectionne un éléphant sur la page `ElephantsPage` :

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"//animals/elephants/elephantdetails?name={elephantName}");
}
```

Cet exemple de code récupère l’éléphant actuellement sélectionné dans le [`CollectionView`](xref:Xamarin.Forms.CollectionView) et navigue vers l' `elephantdetails` itinéraire, en passant `elephantName` en tant que paramètre de requête. Notez que les paramètres de requête seront encodés au format URL pour la navigation, donc « Indian Elephant » deviendra « Indian%20Elephant ».

Pour recevoir des données, la classe qui représente la page vers laquelle la navigation est effectuée ou la classe de la page [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) doit être décorée avec un `QueryPropertyAttribute` pour chaque paramètre de requête :

```csharp
[QueryProperty("Name", "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            BindingContext = ElephantData.Elephants.FirstOrDefault(m => m.Name == Uri.UnescapeDataString(value));
        }
    }
    ...
}
```

Le premier argument de la `QueryPropertyAttribute` propriété spécifie le nom de la propriété qui recevra les données, avec le deuxième argument qui spécifie l’ID du paramètre de requête. Par conséquent, `QueryPropertyAttribute` dans l’exemple ci-dessus, spécifie que la `Name` propriété recevra les données passées dans le `name` paramètre de requête à partir de l’URI dans l' `GoToAsync` appel de méthode. La `Name` propriété, Then URL, décode la valeur du paramètre de requête et l’utilise pour définir le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page sur l’objet qui sera affiché.

> [!NOTE]
> Une classe peut être configurée avec plusieurs objets `QueryPropertyAttribute`.

## <a name="back-button-behavior"></a>Comportement du bouton Précédent

La classe `BackButtonBehavior` définit les propriétés suivantes, qui contrôlent l’apparence et le comportement du bouton Précédent :

- `Command`, de type `ICommand` : exécuté lorsque l’utilisateur appuie sur le bouton Précédent.
- `CommandParameter`, de type `object` : paramètre passé à la commande `Command`.
- `IconOverride`, de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) , l’icône utilisée pour le bouton précédent.
- `IsEnabled`, de type `boolean` : indique si le bouton Précédent est activé. La valeur par défaut est `true`.
- `TextOverride`, de type `string` : texte utilisé pour le bouton Précédent.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

La classe `BackButtonBehavior` peut être consommée en définissant la propriété `Shell.BackButtonBehavior` jointe à un objet `BackButtonBehavior` :

```xaml
<ContentPage ...>    
    <Shell.BackButtonBehavior>
        <BackButtonBehavior Command="{Binding BackCommand}"
                            IconOverride="back.png" />   
    </Shell.BackButtonBehavior>
    ...
</ContentPage>
```

Le code C# équivalent est :

```csharp
Shell.SetBackButtonBehavior(this, new BackButtonBehavior
{
    Command = new Command(() =>
    {
        ...
    }),
    IconOverride = "back.png"
});
```

La propriété `Command` est définie sur un élément `ICommand` à exécuter lorsque le bouton Précédent est enfoncé, et la propriété `IconOverride` est définie sur l’icône utilisée pour le bouton Précédent :

[![Capture d’écran d’un remplacement d’icône de bouton précédent de Shell sur iOS et Android](navigation-images/back-button.png "Remplacement de l’icône du bouton précédent de l’interpréteur de commandes")](navigation-images/back-button-large.png#lightbox "Remplacement de l’icône du bouton précédent de l’interpréteur de commandes")

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

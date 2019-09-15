---
title: Performances de ListView
description: Bien que ListView est un puissant pour afficher les données, il présente certaines limitations. Cet article explique comment garantir des performances exceptionnelles avec un ListView de Xamarin.Forms dans une application.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998179"
---
# <a name="listview-performance"></a>Performances de ListView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Lorsque vous écrivez des applications mobiles, des performances est importante. Les utilisateurs s’attendent défilement fluide et temps de chargement rapide. Ne pas répondre aux attentes de vos utilisateurs vous coûter des évaluations dans le magasin de l’application ou dans le cas d’une application line of business, coût votre temps à l’entreprise et l’argent.

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) est un affichage puissant pour l’affichage des données, mais il présente certaines limites. Les performances de défilement peuvent pâtir de l’utilisation de cellules personnalisées, en particulier lorsqu’elles contiennent des hiérarchies d’affichage profondément imbriquées ou utilisent certaines dispositions qui nécessitent une mesure complexe. Heureusement, il existe des techniques que vous pouvez utiliser pour éviter des performances médiocres.

## <a name="caching-strategy"></a>Stratégie de mise en cache

Les ListViews sont souvent utilisés pour afficher bien plus de données que s’affichent à l’écran. Par exemple, une application musicale peut avoir une bibliothèque de chansons avec des milliers d’entrées. La création d’un élément pour chaque entrée entraînerait une perte de mémoire précieuse et une mauvaise exécution. La création et la destruction de lignes nécessitent en permanence que l’application instancie et nettoie les objets en permanence, ce qui peut également s’avérer médiocre.

Pour économiser de la mémoire, les [`ListView`](xref:Xamarin.Forms.ListView) équivalents natifs de chaque plateforme ont des fonctionnalités intégrées permettant de réutiliser des lignes. Seules les cellules visibles sur l’écran sont chargées en mémoire et le **contenu** est chargé dans les cellules existantes. Ce modèle empêche l’application d’instancier des milliers d’objets, ce qui permet de gagner du temps et de la mémoire.

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) permet la réutilisation [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) des cellules via l’énumération, qui a les valeurs suivantes :

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> La plateforme universelle Windows (UWP) ignore le [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) mise en cache de la stratégie, car elle utilise toujours la mise en cache pour améliorer les performances. Par conséquent, par défaut il se comporte comme si le [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) mise en cache de la stratégie est appliquée.

### <a name="retainelement"></a>RetainElement

Le [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) mise en cache de stratégie spécifie que le [ `ListView` ](xref:Xamarin.Forms.ListView) générera une cellule pour chaque élément dans la liste, et est la valeur par défaut `ListView` comportement. Elle doit être utilisée dans les circonstances suivantes :

- Chaque cellule a un grand nombre de liaisons (20-30 +).
- Le modèle de cellule change fréquemment.
- Les tests révèlent `RecycleElement` que la stratégie de mise en cache entraîne une réduction de la vitesse d’exécution.

Il est important de reconnaître les conséquences de la [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) stratégie de mise en cache lorsque vous travaillez avec des cellules personnalisés. Code d’initialisation de cellule devez exécuter pour la création de chaque cellule, qui peut être plusieurs fois par seconde. Dans ce cas, les techniques de disposition qui étaient correctes sur une page, telles que [`StackLayout`](xref:Xamarin.Forms.StackLayout) l’utilisation de plusieurs instances imbriquées, deviennent des goulots d’étranglement de performances lorsqu’elles sont configurées et détruites en temps réel à mesure que l’utilisateur fait défiler.

### <a name="recycleelement"></a>RecycleElement

Le [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) mise en cache de stratégie spécifie que le [ `ListView` ](xref:Xamarin.Forms.ListView) va tenter de réduire sa vitesse d’encombrement et l’exécution de mémoire par recyclage des cellules de la liste. Ce mode n’offre pas toujours une amélioration des performances et les tests doivent être effectués pour déterminer les améliorations. Toutefois, il s’agit du choix privilégié et doit être utilisé dans les circonstances suivantes :

- Chaque cellule possède un nombre de liaisons faible à modéré.
- Chaque cellule [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) définit toutes les données de la cellule.
- Chaque cellule est en grande partie similaire, avec le modèle de cellule invariable.

Lors de la virtualisation, la cellule aura son contexte de liaison mis à jour, et par conséquent, si une application utilise ce mode elle doit s’assurer que les mises à jour du contexte de liaison sont gérés de façon appropriée. Toutes les données relatives à la cellule doivent provenir du contexte de liaison ou les erreurs de cohérence peuvent se produire. Ce problème peut être évité à l’aide de la liaison de données pour afficher les données des cellules. Vous pouvez également les données des cellules doivent être définies dans le `OnBindingContextChanged` remplacer, plutôt que dans le constructeur de la cellule personnalisée, comme illustré dans l’exemple de code suivant :

```csharp
public class CustomCell : ViewCell
{
    Image image = null;
    
    public CustomCell ()
    {
        image = new Image();
        View = image;
    }
    
    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();
        
        var item = BindingContext as ImageItem;
        if (item != null) {
            image.Source = item.ImageUrl;
        }
    }
}
```

Pour plus d’informations, consultez [changements de contexte de liaison](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

Sur iOS et Android, si les cellules utilisent des convertisseurs personnalisés, ils doivent vous assurer que notification de modification de propriété est implémentée correctement. Lorsque les cellules sont réutilisées leurs valeurs de propriété change le contexte de liaison est mise à jour à celle d’une cellule disponible, avec `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [personnalisation d’une ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement avec un DataTemplateSelector

Quand un [ `ListView` ](xref:Xamarin.Forms.ListView) utilise un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) pour sélectionner un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), le [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) la mise en cache stratégie de ne pas mettre en cache `DataTemplate`s. Au lieu de cela, un `DataTemplate` est sélectionné pour chaque élément de données dans la liste.

> [!NOTE]
> Le [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) mise en cache de la stratégie a une condition préalable, introduite dans Xamarin.Forms 2.4, qui, lorsqu’un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) est invité à sélectionner un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)que chaque `DataTemplate` doit retourner le même [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) type. Par exemple, étant donné un [ `ListView` ](xref:Xamarin.Forms.ListView) avec un `DataTemplateSelector` qui peut retourner `MyDataTemplateA` (où `MyDataTemplateA` retourne un `ViewCell` de type `MyViewCellA`), ou `MyDataTemplateB` (où `MyDataTemplateB`retourne un `ViewCell` de type `MyViewCellB`), lorsque `MyDataTemplateA` est retourné doit retourner `MyViewCellA` ou une exception sera levée.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

Le [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) mise en cache de la stratégie s’appuie sur le [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) mise en cache de la stratégie en veillant en outre à ce que, lorsqu’un [ `ListView` ](xref:Xamarin.Forms.ListView) utilise un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) pour sélectionner un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s sont mis en cache par le type d’élément dans la liste. Par conséquent, `DataTemplate`s sont sélectionnées en une seule fois par type d’élément, au lieu d’une fois par instance d’élément.

> [!NOTE]
> Le [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) mise en cache de la stratégie a un composant requis qui le `DataTemplate`s retourné par la [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) doit utiliser le [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) constructeur qui accepte un `Type`.

### <a name="set-the-caching-strategy"></a>Définir la stratégie de mise en cache

Le [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) valeur d’énumération est spécifiée avec un [ `ListView` ](xref:Xamarin.Forms.ListView) surcharge de constructeur, comme indiqué dans l’exemple de code suivant :

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

Dans XAML, définissez l' `CachingStrategy` attribut comme indiqué dans le code XAML ci-dessous :

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Cette méthode a le même effet que la définition de l’argument de stratégie de mise C#en cache dans le constructeur dans.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Définir la stratégie de mise en cache dans un ListView sous-classé

La définition `CachingStrategy` de l’attribut à partir de XAML sur [`ListView`](xref:Xamarin.Forms.ListView) une sous-classe ne produira pas le comportement souhaité `CachingStrategy` , car `ListView`il n’y a aucune propriété sur. En outre, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) est activé, le message d’erreur suivant est généré : **Aucune propriété, propriété pouvant être liée ou événement trouvé pour’CachingStrategy'**

La solution à ce problème consiste à spécifier un constructeur sur sous-classé [ `ListView` ](xref:Xamarin.Forms.ListView) qui accepte un [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) paramètre et le transmet à la classe de base :

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Le [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) valeur d’énumération peut être spécifiée à partir de XAML en utilisant le `x:Arguments` syntaxe :

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Suggestions de performances de ListView

Il existe de nombreuses techniques pour améliorer les performances d' `ListView`un. Les suggestions suivantes peuvent améliorer les performances de votre ListView

- Lier le `ItemsSource` propriété à un `IList<T>` collection au lieu d’un `IEnumerable<T>` collection, car `IEnumerable<T>` collections ne prennent pas en charge un accès aléatoire.
- Utilisez les `TextCell` cellules intégrées (comme  /  `SwitchCell` ) au lieu de `ViewCell` chaque fois que vous le pouvez.
- Utiliser moins d’éléments. Par exemple, envisagez d' `FormattedString` utiliser une seule étiquette au lieu de plusieurs étiquettes.
- Remplacez le `ListView` avec un `TableView` lors de l’affichage des données non homogènes : autrement dit, les données de types différents.
- Limiter l’utilisation de la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (méthode). Si l’utilisation excessive, il sera dégrader les performances.
- Sur Android, évitez de définir un `ListView`de visibilité de séparateur de ligne ou de couleur après qu’il a été instancié, comme il en résulte une baisse des performances de grande taille.
- Évitez de modifier la disposition des cellules selon la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). La modification de la disposition entraîne des coûts de mesure et d’initialisation importants.
- Évitez les hiérarchies de dispositions profondément imbriquées. Utilisez `AbsoluteLayout` ou `Grid` afin de réduire l’imbrication.
- Évitez une `LayoutOptions` valeur spécifique `Fill` autre`Fill` que (est le moins coûteux à calculer).
- Éviter de placer un `ListView` à l’intérieur d’un `ScrollView` pour les raisons suivantes :
  - Le `ListView` implémente son propre le défilement.
  - Le `ListView` ne recevra pas les mouvements, car ceux-ci sont gérés par le parent `ScrollView`.
  - Le `ListView` peut présenter un en-tête personnalisé et le pied de page qui fait défiler les éléments de la liste, potentiellement offre les fonctionnalités qui le `ScrollView` a été utilisé pour. Pour plus d’informations, consultez [en-têtes et pieds de page](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Imaginez un convertisseur personnalisé si vous avez besoin d’une conception spécifique et complexe présentée dans vos cellules.

`AbsoluteLayout`permet d’effectuer des mises en page sans appel de mesure unique, ce qui la rend très performante. Si `AbsoluteLayout` ne peut pas être utilisée, envisagez [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Si vous utilisez `RelativeLayout`, en passant directement des contraintes sera beaucoup plus rapide que l’utilisation de l’API de l’expression. Cette méthode est plus rapide, car l’API expression utilise JIT et, sur iOS, l’arborescence doit être interprétée, ce qui est plus lent. L’expression API convient aux mises en page où il requis uniquement sur la disposition initiale et la rotation, mais en `ListView`, où elle est exécutée en permanence pendant le défilement, elle nuit aux performances.

Création d’un convertisseur personnalisé pour un [ `ListView` ](xref:Xamarin.Forms.ListView) ou ses cellules est une approche à réduire l’effet des calculs de disposition sur les performances de défilement. Pour plus d’informations, consultez [personnalisation d’un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) et [personnalisation d’une ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Liens connexes

- [Affichage de convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)

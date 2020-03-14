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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305338"
---
# <a name="listview-performance"></a>Performances de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Lorsque vous écrivez des applications mobiles, des performances est importante. Les utilisateurs s’attendent défilement fluide et temps de chargement rapide. Ne pas répondre aux attentes de vos utilisateurs vous coûter des évaluations dans le magasin de l’application ou dans le cas d’une application line of business, coût votre temps à l’entreprise et l’argent.

Xamarin. Forms [`ListView`](xref:Xamarin.Forms.ListView) est un affichage puissant pour l’affichage des données, mais il présente certaines limites. Les performances de défilement peuvent pâtir de l’utilisation de cellules personnalisées, en particulier lorsqu’elles contiennent des hiérarchies d’affichage profondément imbriquées ou utilisent certaines dispositions qui nécessitent une mesure complexe. Heureusement, il existe des techniques que vous pouvez utiliser pour éviter des performances médiocres.

## <a name="caching-strategy"></a>Stratégie de mise en cache

Les ListViews sont souvent utilisés pour afficher bien plus de données que s’affichent à l’écran. Par exemple, une application musicale peut avoir une bibliothèque de chansons avec des milliers d’entrées. La création d’un élément pour chaque entrée entraînerait une perte de mémoire précieuse et une mauvaise exécution. La création et la destruction de lignes nécessitent en permanence que l’application instancie et nettoie les objets en permanence, ce qui peut également s’avérer médiocre.

Pour économiser de la mémoire, les équivalents [`ListView`](xref:Xamarin.Forms.ListView) natifs pour chaque plateforme ont des fonctionnalités intégrées permettant de réutiliser des lignes. Seules les cellules visibles à l’écran sont chargées en mémoire et le **contenu** est chargé dans les cellules existantes. Ce modèle empêche l’application d’instancier des milliers d’objets, ce qui permet de gagner du temps et de la mémoire.

Xamarin. Forms permet la réutilisation des cellules [`ListView`](xref:Xamarin.Forms.ListView) via l’énumération [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) , qui a les valeurs suivantes :

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> La plateforme Windows universelle (UWP) ignore la stratégie de mise en cache [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , car elle utilise toujours la mise en cache pour améliorer les performances. Par conséquent, il se comporte par défaut comme si la stratégie de mise en cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) est appliquée.

### <a name="retainelement"></a>RetainElement

La stratégie de mise en cache [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) spécifie que le [`ListView`](xref:Xamarin.Forms.ListView) génère une cellule pour chaque élément de la liste, et est le comportement de `ListView` par défaut. Elle doit être utilisée dans les circonstances suivantes :

- Chaque cellule a un grand nombre de liaisons (20-30 +).
- Le modèle de cellule change fréquemment.
- Les tests révèlent que la stratégie de mise en cache `RecycleElement` entraîne une réduction de la vitesse d’exécution.

Il est important de reconnaître les conséquences de la stratégie de mise en cache [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) lors de l’utilisation de cellules personnalisées. Code d’initialisation de cellule devez exécuter pour la création de chaque cellule, qui peut être plusieurs fois par seconde. Dans ce cas, les techniques de disposition qui étaient correctes sur une page, telles que l’utilisation de plusieurs instances de [`StackLayout`](xref:Xamarin.Forms.StackLayout) imbriquées, deviennent des goulots d’étranglement de performances lorsqu’elles sont configurées et détruites en temps réel à mesure que l’utilisateur fait défiler.

### <a name="recycleelement"></a>RecycleElement

La stratégie de mise en cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) spécifie que le [`ListView`](xref:Xamarin.Forms.ListView) tentera de réduire son encombrement mémoire et sa vitesse d’exécution en rerecyclant les cellules de la liste. Ce mode n’offre pas toujours une amélioration des performances et les tests doivent être effectués pour déterminer les améliorations. Toutefois, il s’agit du choix privilégié et doit être utilisé dans les circonstances suivantes :

- Chaque cellule possède un nombre de liaisons faible à modéré.
- Le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de chaque cellule définit toutes les données de la cellule.
- Chaque cellule est en grande partie similaire, avec le modèle de cellule invariable.

Lors de la virtualisation, la cellule aura son contexte de liaison mis à jour, et par conséquent, si une application utilise ce mode elle doit s’assurer que les mises à jour du contexte de liaison sont gérés de façon appropriée. Toutes les données relatives à la cellule doivent provenir du contexte de liaison ou les erreurs de cohérence peuvent se produire. Ce problème peut être évité à l’aide de la liaison de données pour afficher les données des cellules. Vous pouvez également définir les données de cellule dans la `OnBindingContextChanged` substitution, plutôt que dans le constructeur de la cellule personnalisée, comme illustré dans l’exemple de code suivant :

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

Pour plus d’informations, consultez [modifications du contexte de liaison](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

Sur iOS et Android, si les cellules utilisent des convertisseurs personnalisés, ils doivent vous assurer que notification de modification de propriété est implémentée correctement. Lorsque des cellules sont réutilisées, leurs valeurs de propriété changent lorsque le contexte de liaison est mis à jour avec celui d’une cellule disponible, avec `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [Personnalisation d’un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement avec un DataTemplateSelector

Lorsqu’un [`ListView`](xref:Xamarin.Forms.ListView) utilise un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pour sélectionner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la stratégie de mise en cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) ne met pas en cache `DataTemplate`s. Au lieu de cela, une `DataTemplate` est sélectionnée pour chaque élément de données de la liste.

> [!NOTE]
> La stratégie de mise en cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) dispose d’un prérequis, introduit dans Xamarin. forms 2,4, que lorsqu’un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) est invité à sélectionner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que chaque `DataTemplate` doit retourner le même [`ViewCell`](xref:Xamarin.Forms.ViewCell) type. Par exemple, étant donné un [`ListView`](xref:Xamarin.Forms.ListView) avec un `DataTemplateSelector` qui peut retourner `MyDataTemplateA` (où `MyDataTemplateA` retourne un `ViewCell` de type `MyViewCellA`), ou `MyDataTemplateB` (où `MyDataTemplateB` retourne une `ViewCell` de type `MyViewCellB`), lorsque `MyDataTemplateA` est retourné, il doit retourner `MyViewCellA` ou une exception est levée.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

La stratégie de mise en cache [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) s’appuie sur la stratégie de mise en cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) en vérifiant en outre que lorsqu’un [`ListView`](xref:Xamarin.Forms.ListView) utilise une [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pour sélectionner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), `DataTemplate`s sont mis en cache par le type d’élément de la liste. Par conséquent, les `DataTemplate`s sont sélectionnées une fois par type d’élément, et non une fois par instance d’élément.

> [!NOTE]
> La stratégie de mise en cache [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) a un prérequis que les `DataTemplate`s retournés par le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) doivent utiliser le constructeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) qui prend une `Type`.

### <a name="set-the-caching-strategy"></a>Définir la stratégie de mise en cache

La valeur d’énumération [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) est spécifiée avec une surcharge de constructeur [`ListView`](xref:Xamarin.Forms.ListView) , comme indiqué dans l’exemple de code suivant :

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

Dans XAML, définissez l’attribut `CachingStrategy` comme indiqué dans le code XAML ci-dessous :

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

La définition de l’attribut `CachingStrategy` à partir de XAML sur une [`ListView`](xref:Xamarin.Forms.ListView) sous-classée ne produit pas le comportement souhaité, car il n’y a aucune propriété `CachingStrategy` sur `ListView`. En outre, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) est activé, le message d’erreur suivant est généré : **aucune propriété, propriété pouvant être liée ou événement trouvé pour « CachingStrategy »**

La solution à ce problème consiste à spécifier un constructeur sur la [`ListView`](xref:Xamarin.Forms.ListView) sous-classée qui accepte un paramètre [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) et le transmet à la classe de base :

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

Ensuite, la valeur d’énumération [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) peut être spécifiée à partir de XAML à l’aide de la syntaxe `x:Arguments` :

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Suggestions de performances de ListView

Il existe de nombreuses techniques pour améliorer les performances d’un `ListView`. Les suggestions suivantes peuvent améliorer les performances de votre ListView

- Liez la propriété `ItemsSource` à une collection `IList<T>` au lieu d’une collection `IEnumerable<T>`, car `IEnumerable<T>` collections ne prennent pas en charge l’accès aléatoire.
- Utilisez les cellules intégrées (comme `TextCell` / `SwitchCell`) au lieu de `ViewCell` chaque fois que vous le pouvez.
- Utiliser moins d’éléments. Par exemple, envisagez d’utiliser une seule étiquette de `FormattedString` au lieu de plusieurs étiquettes.
- Remplacez le `ListView` par un `TableView` lors de l’affichage de données non homogènes, c’est-à-dire des données de types différents.
- Limitez l’utilisation de la méthode [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) . Si l’utilisation excessive, il sera dégrader les performances.
- Sur Android, évitez de définir la couleur ou la visibilité du séparateur de lignes d’un `ListView`une fois qu’il a été instancié, car cela entraîne une baisse importante des performances.
- Évitez de modifier la disposition des cellules en fonction de la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). La modification de la disposition entraîne des coûts de mesure et d’initialisation importants.
- Évitez les hiérarchies de dispositions profondément imbriquées. Utilisez `AbsoluteLayout` ou `Grid` pour réduire l’imbrication.
- Évitez les `LayoutOptions` spécifiques autres que `Fill` (`Fill` est le moins coûteux à calculer).
- Évitez de placer un `ListView` à l’intérieur d’une `ScrollView` pour les raisons suivantes :
  - Le `ListView` implémente son propre défilement.
  - La `ListView` ne recevra aucun geste, car elle sera gérée par le `ScrollView`parent.
  - La `ListView` peut présenter un en-tête et un pied de page personnalisés qui défilent les éléments de la liste, offrant potentiellement la fonctionnalité pour laquelle la `ScrollView` a été utilisée. Pour plus d’informations, consultez [en-têtes et pieds de page](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Imaginez un convertisseur personnalisé si vous avez besoin d’une conception spécifique et complexe présentée dans vos cellules.

`AbsoluteLayout` a la possibilité d’effectuer des dispositions sans appel de mesure unique, ce qui en fait un très performant. Si `AbsoluteLayout` ne peut pas être utilisé, envisagez [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout). Si vous utilisez `RelativeLayout`, le passage direct de contraintes est beaucoup plus rapide que l’utilisation de l’API expression. Cette méthode est plus rapide, car l’API expression utilise JIT et, sur iOS, l’arborescence doit être interprétée, ce qui est plus lent. L’API expression convient pour les mises en page où elle est requise uniquement lors de la mise en page et de la rotation initiales, mais dans `ListView`, où elle est exécutée en permanence pendant le défilement, elle nuit aux performances.

La création d’un convertisseur personnalisé pour un [`ListView`](xref:Xamarin.Forms.ListView) ou ses cellules est une approche permettant de réduire l’effet des calculs de disposition sur les performances de défilement. Pour plus d’informations, consultez [Personnalisation d’un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) et [Personnalisation d’un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Liens connexes

- [Vue du convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de rendu personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)

---
title: Performances de ListView
description: Bien que ListView soit un affichage puissant pour l’affichage des données, il présente certaines limitations. Cet article explique comment garantir des performances exceptionnelles avec un Xamarin.Forms ListView dans une application.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98f91ec614df08997e7cbf23530086f541500b5d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375471"
---
# <a name="listview-performance"></a>Performances de ListView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Lors de l’écriture d’applications mobiles, les performances sont importantes. Les utilisateurs s’attendent à attendre un défilement régulier et des temps de chargement rapides. Si vous ne répondez pas aux attentes de vos utilisateurs, vous coûterez les évaluations dans le magasin d’applications, ou dans le cas d’une application métier, vous coûterez le temps et l’argent de votre organisation.

Le Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) est un affichage puissant pour l’affichage des données, mais il présente certaines limites. Les performances de défilement peuvent pâtir de l’utilisation de cellules personnalisées, en particulier lorsqu’elles contiennent des hiérarchies d’affichage profondément imbriquées ou utilisent certaines dispositions qui nécessitent une mesure complexe. Heureusement, il existe des techniques que vous pouvez utiliser pour éviter de mauvaises performances.

## <a name="caching-strategy"></a>Stratégie de mise en cache

Les ListViews sont souvent utilisés pour afficher bien plus de données que s’affichent à l’écran. Par exemple, une application musicale peut avoir une bibliothèque de chansons avec des milliers d’entrées. La création d’un élément pour chaque entrée entraînerait une perte de mémoire précieuse et une mauvaise exécution. La création et la destruction de lignes nécessitent en permanence que l’application instancie et nettoie les objets en permanence, ce qui peut également s’avérer médiocre.

Pour économiser de la mémoire, les [`ListView`](xref:Xamarin.Forms.ListView) équivalents natifs de chaque plateforme ont des fonctionnalités intégrées permettant de réutiliser des lignes. Seules les cellules visibles à l’écran sont chargées en mémoire et le **contenu** est chargé dans les cellules existantes. Ce modèle empêche l’application d’instancier des milliers d’objets, ce qui permet de gagner du temps et de la mémoire.

Xamarin.Forms autorise la [`ListView`](xref:Xamarin.Forms.ListView) réutilisation des cellules via l' [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) énumération, qui a les valeurs suivantes :

```csharp
public enum ListViewCachingStrategy
{
    RetainElement,   // the default value
    RecycleElement,
    RecycleElementAndDataTemplate
}
```

> [!NOTE]
> La plateforme Windows universelle (UWP) ignore la [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) stratégie de mise en cache, car elle utilise toujours la mise en cache pour améliorer les performances. Par conséquent, il se comporte par défaut comme si la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache était appliquée.

### <a name="retainelement"></a>RetainElement

La [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) stratégie de mise en cache spécifie que [`ListView`](xref:Xamarin.Forms.ListView) génère une cellule pour chaque élément de la liste, et est le comportement par défaut `ListView` . Elle doit être utilisée dans les circonstances suivantes :

- Chaque cellule a un grand nombre de liaisons (20-30 +).
- Le modèle de cellule change fréquemment.
- Les tests révèlent que la `RecycleElement` stratégie de mise en cache entraîne une réduction de la vitesse d’exécution.

Il est important de reconnaître les conséquences de la [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) stratégie de mise en cache lors de l’utilisation de cellules personnalisées. Tout code d’initialisation de cellule doit s’exécuter pour chaque création de cellule, qui peut être plusieurs fois par seconde. Dans ce cas, les techniques de disposition qui étaient correctes sur une page, telles que l’utilisation de plusieurs instances imbriquées [`StackLayout`](xref:Xamarin.Forms.StackLayout) , deviennent des goulots d’étranglement de performances lorsqu’elles sont configurées et détruites en temps réel à mesure que l’utilisateur fait défiler.

### <a name="recycleelement"></a>RecycleElement

La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache spécifie que le [`ListView`](xref:Xamarin.Forms.ListView) tentera de réduire son encombrement mémoire et sa vitesse d’exécution par recyclage des cellules de la liste. Ce mode n’offre pas toujours une amélioration des performances et les tests doivent être effectués pour déterminer les améliorations. Toutefois, il s’agit du choix privilégié et doit être utilisé dans les circonstances suivantes :

- Chaque cellule possède un nombre de liaisons faible à modéré.
- Chaque cellule [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) définit toutes les données de la cellule.
- Chaque cellule est en grande partie similaire, avec le modèle de cellule invariable.

Pendant la virtualisation, le contexte de liaison de la cellule est mis à jour. par conséquent, si une application utilise ce mode, elle doit s’assurer que les mises à jour du contexte de liaison sont gérées de manière appropriée. Toutes les données relatives à la cellule doivent provenir du contexte de liaison ou des erreurs de cohérence peuvent se produire. Ce problème peut être évité à l’aide de la liaison de données pour afficher les données des cellules. Vous pouvez également définir des données de cellule dans la `OnBindingContextChanged` substitution, plutôt que dans le constructeur de la cellule personnalisée, comme illustré dans l’exemple de code suivant :

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

Sur iOS et Android, si des cellules utilisent des convertisseurs personnalisés, ils doivent s’assurer que la notification de modification de propriété est correctement implémentée. Lorsque des cellules sont réutilisées, leurs valeurs de propriété changent lorsque le contexte de liaison est mis à jour avec celui d’une cellule disponible, avec des `PropertyChanged` événements déclenchés. Pour plus d’informations, consultez [Personnalisation d’un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement avec DataTemplateSelector

Quand un [`ListView`](xref:Xamarin.Forms.ListView) utilise un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pour sélectionner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache ne met pas en cache les `DataTemplate` savesets. Au lieu de cela, un `DataTemplate` est sélectionné pour chaque élément de données de la liste.

> [!NOTE]
> La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) stratégie de mise en cache a une condition préalable, introduite dans Xamarin.Forms 2,4, que lorsqu’un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) est invité à sélectionner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui `DataTemplate` doit retourner le même [`ViewCell`](xref:Xamarin.Forms.ViewCell) type. Par exemple, [`ListView`](xref:Xamarin.Forms.ListView) avec un `DataTemplateSelector` qui peut retourner soit `MyDataTemplateA` (où `MyDataTemplateA` retourne un `ViewCell` de type `MyViewCellA` ), soit `MyDataTemplateB` (où `MyDataTemplateB` retourne un `ViewCell` de type `MyViewCellB` ), lorsque `MyDataTemplateA` est retourné, il doit être retourné `MyViewCellA` ou une exception est levée.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

La [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) stratégie de mise en cache s’appuie sur la stratégie de mise en cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) en vérifiant également que quand un [`ListView`](xref:Xamarin.Forms.ListView) utilise un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pour sélectionner un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , `DataTemplate` les s sont mises en cache par le type d’élément de la liste. Par conséquent, `DataTemplate` les s sont sélectionnées une fois par type d’élément, et non une fois par instance d’élément.

> [!NOTE]
> La [`RecycleElementAndDataTemplate`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) stratégie de mise en cache a une condition préalable que les `DataTemplate` s retournés par le [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) doivent utiliser le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type)) constructeur qui prend un `Type` .

### <a name="set-the-caching-strategy"></a>Définir la stratégie de mise en cache

La [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valeur d’énumération est spécifiée avec une [`ListView`](xref:Xamarin.Forms.ListView) surcharge de constructeur, comme indiqué dans l’exemple de code suivant :

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

Cette méthode a le même effet que la définition de l’argument de stratégie de mise en cache dans le constructeur en C#.

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>Définir la stratégie de mise en cache dans un ListView sous-classé

La définition `CachingStrategy` de l’attribut à partir de XAML sur une sous-classe [`ListView`](xref:Xamarin.Forms.ListView) ne produira pas le comportement souhaité, car il n’y a aucune `CachingStrategy` propriété sur `ListView` . En outre, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) est activé, le message d’erreur suivant est généré : **aucune propriété, propriété pouvant être liée ou événement trouvé pour « CachingStrategy »**

La solution à ce problème consiste à spécifier un constructeur sur la sous-classe [`ListView`](xref:Xamarin.Forms.ListView) qui accepte un [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) paramètre et le transmet à la classe de base :

```csharp
public class CustomListView : ListView
{
    public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
    {
    }
    ...
}
```

La [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy) valeur d’énumération peut ensuite être spécifiée à partir de XAML à l’aide de la `x:Arguments` syntaxe suivante :

```xaml
<local:CustomListView>
    <x:Arguments>
        <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
    </x:Arguments>
</local:CustomListView>
```

## <a name="listview-performance-suggestions"></a>Suggestions de performances de ListView

Il existe de nombreuses techniques pour améliorer les performances d’un `ListView` . Les suggestions suivantes peuvent améliorer les performances de votre ListView

- Liez la `ItemsSource` propriété à une `IList<T>` collection au lieu d’une `IEnumerable<T>` collection, parce que les `IEnumerable<T>` collections ne prennent pas en charge l’accès aléatoire.
- Utilisez les cellules intégrées (comme `TextCell`  /  `SwitchCell` ) au lieu de `ViewCell` chaque fois que vous le pouvez.
- Utilisez moins d’éléments. Par exemple, envisagez d’utiliser une seule `FormattedString` étiquette au lieu de plusieurs étiquettes.
- Remplacez `ListView` par un `TableView` lors de l’affichage de données non homogènes, c’est-à-dire des données de types différents.
- Limitez l’utilisation de la [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) méthode. En cas de surutilisation, les performances seront dégradées.
- Sur Android, évitez de définir la `ListView` couleur ou la visibilité du séparateur de lignes d’une après son instanciation, car cela entraîne une baisse importante des performances.
- Évitez de modifier la disposition des cellules en fonction de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . La modification de la disposition entraîne des coûts de mesure et d’initialisation importants.
- Évitez les hiérarchies de disposition profondément imbriquées. Utilisez  `AbsoluteLayout` ou  `Grid` pour réduire l’imbrication.
- Évitez une `LayoutOptions` valeur spécifique autre que  `Fill` ( `Fill` est le moins coûteux à calculer).
- Évitez de placer un `ListView` à l’intérieur `ScrollView` d’un pour les raisons suivantes :
  - `ListView`Implémente son propre défilement.
  - Le `ListView` ne recevra aucun geste, car il sera géré par le parent `ScrollView` .
  - Le `ListView` peut présenter un en-tête et un pied de page personnalisés qui défilent les éléments de la liste, offrant potentiellement les fonctionnalités pour lesquelles le `ScrollView` a été utilisé. Pour plus d’informations, consultez [en-têtes et pieds de page](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers).
- Imaginez un convertisseur personnalisé si vous avez besoin d’une conception spécifique et complexe présentée dans vos cellules.

`AbsoluteLayout` permet d’effectuer des mises en page sans appel de mesure unique, ce qui la rend très performante. Si `AbsoluteLayout` ne peut pas être utilisé, envisagez [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Si `RelativeLayout` vous utilisez, le passage direct de contraintes est beaucoup plus rapide que l’utilisation de l’API expression. Cette méthode est plus rapide, car l’API expression utilise JIT et, sur iOS, l’arborescence doit être interprétée, ce qui est plus lent. L’API expression convient pour les mises en page où elle est requise uniquement lors de la mise en page et de la rotation initiales, mais dans `ListView` , où elle est exécutée constamment pendant le défilement, elle nuit aux performances.

La création d’un convertisseur personnalisé pour un [`ListView`](xref:Xamarin.Forms.ListView) ou ses cellules est une approche permettant de réduire l’effet des calculs de disposition sur les performances de défilement. Pour plus d’informations, consultez [Personnalisation d’un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) et [Personnalisation d’un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

## <a name="related-links"></a>Liens connexes

- [Vue du convertisseur personnalisé (exemple)](/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell de rendu personnalisé (exemple)](/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
---
title: Performances de ListView
description: Bien que ListView est un puissant pour afficher les données, il présente certaines limitations. Cet article explique comment garantir des performances exceptionnelles avec un ListView de Xamarin.Forms dans une application.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: dabbd21a2ad2ef3c77017ea92704ccdf69f7b36c
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228202"
---
# <a name="listview-performance"></a>Performances de ListView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

Lorsque vous écrivez des applications mobiles, des performances est importante. Les utilisateurs s’attendent défilement fluide et temps de chargement rapide. Ne pas répondre aux attentes de vos utilisateurs vous coûter des évaluations dans le magasin de l’application ou dans le cas d’une application line of business, coût votre temps à l’entreprise et l’argent.

Bien que [ `ListView` ](xref:Xamarin.Forms.ListView) est une vue puissante pour afficher les données, il présente certaines limitations. Performances de défilement peuvent se dégrader lors de l’utilisation de cellules personnalisés, en particulier lorsqu’ils contiennent des hiérarchies d’affichage profondément imbriquées ou utilisent certaines dispositions qui nécessitent beaucoup de mesure. Heureusement, il existe des techniques que vous pouvez utiliser pour éviter des performances médiocres.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Stratégie de mise en cache

ListView est souvent utilisés pour afficher des données beaucoup plus que vous pouvez ajuster à l’écran. Considérez une application de la musique, par exemple. Une bibliothèque de chansons peut avoir des milliers d’entrées. L’approche simple, qui serait de créer une ligne pour chaque chanson, aurait des performances médiocres. Cette approche gaspille de la mémoire précieuse et peut ralentir le défilement pour une analyse. Une autre approche consiste à créer et détruire des lignes comme données défile dans la vue. Cela nécessite l’instanciation constante et nettoyage d’objets de vue, qui peut être très lent.

Pour économiser la mémoire, natif [ `ListView` ](xref:Xamarin.Forms.ListView) équivalents pour chaque plateforme ont des fonctionnalités intégrées pour réutiliser des lignes. Seules les cellules visibles sur l’écran sont chargées en mémoire et le **contenu** est chargé dans les cellules existantes. Cela empêche l’application d’avoir à instancier des milliers d’objets, en économisant du temps et mémoire.

Permet de Xamarin.Forms [ `ListView` ](xref:Xamarin.Forms.ListView) cellule réutilisation via la [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) énumération, qui a les valeurs suivantes :

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

Le [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) mise en cache de stratégie spécifie que le [ `ListView` ](xref:Xamarin.Forms.ListView) générera une cellule pour chaque élément dans la liste, et est la valeur par défaut `ListView` comportement. Il doit généralement être utilisée dans les circonstances suivantes :

- Lorsque chaque cellule comporte un grand nombre de liaisons (20-30 +).
- Lorsque le modèle de cellule change fréquemment.
- Lorsque test révèle que le `RecycleElement` la mise en cache des résultats de stratégie dans une vitesse d’exécution réduites.

Il est important de reconnaître les conséquences de la [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) stratégie de mise en cache lorsque vous travaillez avec des cellules personnalisés. Code d’initialisation de cellule devez exécuter pour la création de chaque cellule, qui peut être plusieurs fois par seconde. Dans ce cas, des techniques de mise en page ont été correctement sur une page, telles que l’utilisation de plusieurs imbriqués [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) instances, deviennent des goulots d’étranglement de performances lorsqu’ils sont le programme d’installation et destruction en temps réel en tant que l’utilisateur fait défiler.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

Le [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) mise en cache de stratégie spécifie que le [ `ListView` ](xref:Xamarin.Forms.ListView) va tenter de réduire sa vitesse d’encombrement et l’exécution de mémoire par recyclage des cellules de la liste. Ce mode n’offre pas toujours une amélioration des performances et de test doit être effectué pour déterminer les améliorations. Toutefois, il est généralement le choix privilégié et doit être utilisé dans les circonstances suivantes :

- Lorsque chaque cellule comporte un petit nombre modéré de liaisons.
- Lors de chaque cellule [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) définit toutes les données de cellule.
- Lorsque chaque cellule est en grande partie similaire, avec le modèle de cellule qui ne changent pas.

Lors de la virtualisation, la cellule aura son contexte de liaison mis à jour, et par conséquent, si une application utilise ce mode elle doit s’assurer que les mises à jour du contexte de liaison sont gérés de façon appropriée. Toutes les données relatives à la cellule doivent provenir du contexte de liaison ou les erreurs de cohérence peuvent se produire. Pour cela, à l’aide de la liaison de données pour afficher des données de cellule. Vous pouvez également les données des cellules doivent être définies dans le `OnBindingContextChanged` remplacer, plutôt que dans le constructeur de la cellule personnalisée, comme illustré dans l’exemple de code suivant :

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

### <a name="setting-the-caching-strategy"></a>Définir la stratégie de mise en cache

Le [ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) valeur d’énumération est spécifiée avec un [ `ListView` ](xref:Xamarin.Forms.ListView) surcharge de constructeur, comme indiqué dans l’exemple de code suivant :

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

Dans XAML, définissez la `CachingStrategy` attribut comme indiqué dans le code ci-dessous :

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

Cela a le même effet que la définition de l’argument de stratégie de mise en cache dans le constructeur en c# ; Notez qu’il existe aucune `CachingStrategy` propriété sur `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Définir la stratégie de mise en cache dans un ListView sous-classé

Définissant le `CachingStrategy` attribut à partir de XAML sur un sous-classé [ `ListView` ](xref:Xamarin.Forms.ListView) ne produira pas le comportement souhaité, car il n’est pas `CachingStrategy` propriété sur `ListView`. En outre, si [XAMLC](~/xamarin-forms/xaml/xamlc.md) est activé, le message d’erreur suivant est généré: **Aucune propriété, propriété pouvant être liée ou événement trouvé pour’CachingStrategy'**

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

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Amélioration des performances de ListView

Il existe plusieurs techniques permettant d’améliorer les performances d’un `ListView`:

- Lier le `ItemsSource` propriété à un `IList<T>` collection au lieu d’un `IEnumerable<T>` collection, car `IEnumerable<T>` collections ne prennent pas en charge un accès aléatoire.
- Utilisez les cellules intégrés (tels que `TextCell`  /  `SwitchCell` ) au lieu de `ViewCell` chaque fois que vous pouvez.
- Utiliser moins d’éléments. Par exemple envisagez d’utiliser un seul `FormattedString` étiquette au lieu de plusieurs étiquettes.
- Remplacez le `ListView` avec un `TableView` lors de l’affichage des données non homogènes : autrement dit, les données de types différents.
- Limiter l’utilisation de la [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize) (méthode). Si l’utilisation excessive, il sera dégrader les performances.
- Sur Android, évitez de définir un `ListView`de visibilité de séparateur de ligne ou de couleur après qu’il a été instancié, comme il en résulte une baisse des performances de grande taille.
- Évitez de modifier la disposition des cellules selon la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Cela entraîne des coûts importants de mise en page et de l’initialisation.
- Évitez les hiérarchies de dispositions profondément imbriquées. Utilisez `AbsoluteLayout` ou `Grid` afin de réduire l’imbrication.
- Éviter spécifique `LayoutOptions` autre que `Fill` (remplissage est le cher pour calculer).
- Éviter de placer un `ListView` à l’intérieur d’un `ScrollView` pour les raisons suivantes :
  - Le `ListView` implémente son propre le défilement.
  - Le `ListView` ne recevra pas les mouvements, car ceux-ci sont gérés par le parent `ScrollView`.
  - Le `ListView` peut présenter un en-tête personnalisé et le pied de page qui fait défiler les éléments de la liste, potentiellement offre les fonctionnalités qui le `ScrollView` a été utilisé pour. Pour plus d’informations, consultez [en-têtes et pieds de page](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
- Envisagez un convertisseur personnalisé si vous avez besoin d’une conception très spécifique, complexe présentée dans vos cellules.

`AbsoluteLayout` a la possibilité d’effectuer des mises en page sans un appel de la mesure unique. Cela rend très puissante pour les performances. Si `AbsoluteLayout` ne peut pas être utilisée, envisagez [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout). Si vous utilisez `RelativeLayout`, en passant directement des contraintes sera beaucoup plus rapide que l’utilisation de l’API de l’expression. C’est parce que l’API de l’expression utilise JIT et sur iOS l’arborescence doit être interprétée, qui est plus lente. L’expression API convient aux mises en page où il requis uniquement sur la disposition initiale et la rotation, mais en `ListView`, où elle est exécutée en permanence pendant le défilement, elle nuit aux performances.

Création d’un convertisseur personnalisé pour un [ `ListView` ](xref:Xamarin.Forms.ListView) ou ses cellules est une approche à réduire l’effet des calculs de disposition sur les performances de défilement. Pour plus d’informations, consultez [personnalisation d’un ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) et [personnalisation d’une ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Liens associés

- [Affichage de convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [ViewCell convertisseur personnalisé (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)

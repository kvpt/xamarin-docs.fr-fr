---
title: Créer une disposition personnalisée dansXamarin.Forms
description: Cet article explique comment écrire une classe de disposition personnalisée et illustre une classe WrapLayout sensible à l’orientation qui réorganise ses enfants horizontalement sur la page, puis encapsule l’affichage des enfants suivants sur des lignes supplémentaires.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2c1a958bd4cb56096f554acf26756019eeb0693c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572232"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Créer une disposition personnalisée dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin. Forms définit cinq classes de disposition (StackLayout, AbsoluteLayout, RelativeLayout, Grid et FlexLayout) et chacune réorganise ses enfants d’une façon différente. Toutefois, il est parfois nécessaire d’organiser le contenu de la page à l’aide d’une disposition non fournie par Xamarin.Forms . Cet article explique comment écrire une classe de disposition personnalisée et illustre une classe WrapLayout sensible à l’orientation qui réorganise ses enfants horizontalement sur la page, puis encapsule l’affichage des enfants suivants sur des lignes supplémentaires._

Dans Xamarin.Forms , toutes les classes de disposition dérivent de la [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classe et contraignent le type générique à [`View`](xref:Xamarin.Forms.View) et à ses types dérivés. À son tour, la `Layout<T>` classe dérive de la [`Layout`](xref:Xamarin.Forms.Layout) classe, qui fournit le mécanisme permettant de positionner et de redimensionner des éléments enfants.

Chaque élément visuel est responsable de la détermination de sa propre taille par défaut, appelée taille *demandée* . [`Page`](xref:Xamarin.Forms.Page)[`Layout`](xref:Xamarin.Forms.Layout) [`Layout<View>`](xref:Xamarin.Forms.Layout`1) les types dérivés, et sont responsables de la détermination de l’emplacement et de la taille de leurs enfants, ou enfants, par rapport à eux-mêmes. Par conséquent, la disposition implique une relation parent-enfant, où le parent détermine la taille de ses enfants, mais tente de s’adapter à la taille demandée de l’enfant.

Une compréhension approfondie de la Xamarin.Forms disposition et des cycles d’invalidation est nécessaire pour créer une disposition personnalisée. Ces cycles seront à présent abordés.

## <a name="layout"></a>Layout

La disposition commence en haut de l’arborescence d’éléments visuels par une page et se poursuit dans toutes les branches de l’arborescence d’éléments visuels pour englober chaque élément visuel d’une page. Les éléments qui sont parents d’autres éléments sont responsables du dimensionnement et du positionnement de leurs enfants par rapport à eux-mêmes.

La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe définit un [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) qui mesure un élément pour les opérations de disposition et un [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) qui spécifie la zone rectangulaire dans laquelle l’élément sera rendu. Lorsqu’une application démarre et que la première page est affichée, un *cycle de disposition* qui comprend les premiers `Measure` appels, puis `Layout` appelle, démarre sur l' [`Page`](xref:Xamarin.Forms.Page) objet :

1. Pendant le cycle de disposition, chaque élément parent est chargé d’appeler la `Measure` méthode sur ses enfants.
1. Une fois que les enfants ont été mesurés, chaque élément parent est chargé d’appeler la `Layout` méthode sur ses enfants.

Ce cycle garantit que chaque élément visuel sur la page reçoit des appels aux `Measure` `Layout` méthodes et. Le processus est illustré dans le diagramme suivant :

![](custom-images/layout-cycle.png "Xamarin.Forms Layout Cycle")

> [!NOTE]
> Notez que les cycles de disposition peuvent également se produire sur un sous-ensemble de l’arborescence d’éléments visuels si une modification affecte la disposition. Cela comprend les éléments ajoutés ou supprimés d’une collection, tels que dans [`StackLayout`](xref:Xamarin.Forms.StackLayout) , une modification de la [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriété d’un élément ou une modification de la taille d’un élément.

Chaque Xamarin.Forms classe qui a une `Content` propriété ou `Children` a une méthode substituable [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) . Les classes de disposition personnalisées qui dérivent de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) doivent substituer cette méthode et garantir que [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) et [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) sont appelées sur tous les enfants de l’élément, pour fournir la disposition personnalisée souhaitée.

En outre, chaque classe qui dérive de [`Layout`](xref:Xamarin.Forms.Layout) ou [`Layout<View>`](xref:Xamarin.Forms.Layout`1) doit substituer la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthode, qui est l’endroit où une classe de disposition détermine la taille nécessaire en effectuant des appels au [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) de ses enfants.

> [!NOTE]
> Les éléments déterminent leur taille en fonction des *contraintes*, qui indiquent la quantité d’espace disponible pour un élément dans le parent de l’élément. Contraintes passées à [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) et les [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthodes peuvent être comprises entre 0 et `Double.PositiveInfinity` . Un élément est *restreint*, ou *complètement restreint*, lorsqu’il reçoit un appel à son [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) avec des arguments non infinis-l’élément est soumis à une taille particulière. Un élément est sans *contrainte*, ou *partiellement contraint*, lorsqu’il reçoit un appel à sa `Measure` méthode avec au moins un argument égal à `Double.PositiveInfinity` : la contrainte infinie peut être considérée comme indiquant le redimensionnement automatique.

## <a name="invalidation"></a>Invalidation

L’invalidation est le processus par lequel une modification dans un élément d’une page déclenche un nouveau cycle de disposition. Les éléments sont considérés comme non valides lorsqu’ils n’ont plus la taille ou la position correcte. Par exemple, si la [`FontSize`](xref:Xamarin.Forms.Button.FontSize) propriété d’un [`Button`](xref:Xamarin.Forms.Button) change, `Button` est dite non valide, car elle n’aura plus la taille correcte. Le redimensionnement `Button` peut alors avoir un effet ondulation des modifications apportées à la disposition dans le reste d’une page.

Les éléments sont invalidés en appelant la [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) méthode, en général lorsqu’une propriété de l’élément change et peut entraîner une nouvelle taille de l’élément. Cette méthode déclenche l' [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) événement, qui est géré par le parent de l’élément pour déclencher un nouveau cycle de disposition.

La [`Layout`](xref:Xamarin.Forms.Layout) classe définit un gestionnaire pour l' [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) événement sur chaque enfant ajouté à sa `Content` propriété ou `Children` collection, et détache le gestionnaire lorsque l’enfant est supprimé. Par conséquent, chaque élément de l’arborescence d’éléments visuels qui a des enfants est alerté chaque fois que l’un de ses enfants change de taille. Le diagramme suivant illustre la façon dont une modification de la taille d’un élément dans l’arborescence d’éléments visuels peut entraîner des modifications qui réagitent dans l’arborescence :

![](custom-images/invalidation.png "Invalidation in the Visual Tree")

Toutefois, la `Layout` classe tente de limiter l’impact d’une modification de la taille d’un enfant sur la disposition d’une page. Si la disposition est restreinte, une modification de la taille de l’enfant n’affecte pas ce qui est plus élevé que la disposition parente dans l’arborescence d’éléments visuels. Toutefois, une modification de la taille d’une disposition affecte généralement la manière dont la disposition réorganise ses enfants. Par conséquent, toute modification apportée à la taille d’une disposition démarre un cycle de disposition pour la disposition, et la disposition reçoit des appels à ses [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) méthodes et.

La [`Layout`](xref:Xamarin.Forms.Layout) classe définit également une [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) méthode qui a un objectif semblable à la [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) méthode. La `InvalidateLayout` méthode doit être appelée chaque fois qu’une modification est apportée qui affecte la façon dont la disposition place et dimensionne ses enfants. Par exemple, la `Layout` classe appelle la `InvalidateLayout` méthode chaque fois qu’un enfant est ajouté ou supprimé dans une disposition.

La [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) valeur peut être substituée pour implémenter un cache afin de réduire les appels répétitifs de [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) des enfants de la disposition. La substitution de la `InvalidateLayout` méthode fournit une notification indiquant quand des enfants sont ajoutés ou supprimés de la disposition. De même, la [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) méthode peut être substituée pour fournir une notification lorsque l’un des enfants de la disposition change de taille. Pour les deux remplacements de méthode, une disposition personnalisée doit répondre en effaçant le cache. Pour plus d’informations, consultez [calculer et mettre en cache les données de disposition](#calculate-and-cache-layout-data).

## <a name="create-a-custom-layout"></a>Créer une disposition personnalisée

Le processus de création d’une disposition personnalisée est le suivant :

1. Créez une classe qui dérive de la classe `Layout<View>`. Pour plus d’informations, consultez [créer un WrapLayout](#create-a-wraplayout).
1. [*facultatif*] Ajoutez des propriétés, sauvegardées par des propriétés pouvant être liées, pour tous les paramètres qui doivent être définis sur la classe Layout. Pour plus d’informations, consultez [Ajouter des propriétés sauvegardées par des propriétés pouvant être liées](#add-properties-backed-by-bindable-properties).
1. Substituez la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthode pour appeler [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) sur tous les enfants de la disposition, et retourner une taille demandée pour la disposition. Pour plus d’informations, consultez [substitution de la méthode OnMeasure](#override-the-onmeasure-method).
1. Substituez la [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) méthode pour appeler [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)) sur tous les enfants de la disposition. Échec de l’appel de [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle))) : la méthode sur chaque enfant dans une mise en page entraîne la non-réception de la taille ou de la position correcte de l’enfant, et par conséquent, l’enfant ne devient pas visible sur la page. Pour plus d’informations, consultez [substitution de la méthode LayoutChildren](#override-the-layoutchildren-method).

    > [!NOTE]
    > Lors de l’énumération des enfants dans [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) et des [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) substitutions, ignorez tout enfant dont la [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriété a la valeur `false` . Cela permet de s’assurer que la disposition personnalisée ne laisse pas d’espace pour les enfants invisibles.

1. [*facultatif*] Substituez la [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) méthode pour être averti lorsque des enfants sont ajoutés ou supprimés de la disposition. Pour plus d’informations, consultez [substitution de la méthode InvalidateLayout](#override-the-invalidatelayout-method).
1. [*facultatif*] Substituez la [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) méthode pour être averti lorsque l’un des enfants de la disposition change de taille. Pour plus d’informations, consultez [substitution de la méthode OnChildMeasureInvalidated](#override-the-onchildmeasureinvalidated-method).

> [!NOTE]
> Notez que le [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) remplacement ne sera pas appelé si la taille de la disposition est régie par son parent, plutôt que par ses enfants. Toutefois, la substitution sera appelée si une ou les deux contraintes sont infinies, ou si la classe Layout a des valeurs de propriété ou non par défaut [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) . Pour cette raison, le [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) remplacement ne peut pas reposer sur les tailles enfants obtenues pendant l’appel de la [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthode. Au lieu de cela, vous `LayoutChildren` devez appeler [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) sur les enfants de la disposition, avant d’appeler la méthode [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)). En guise d’alternative, la taille des enfants obtenus dans la `OnMeasure` substitution peut être mise en cache pour éviter les appels ultérieurs `Measure` dans la `LayoutChildren` substitution, mais la classe Layout devra savoir quand les tailles doivent être retirées. Pour plus d’informations, consultez [calculer et mettre en cache les données de disposition](#calculate-and-cache-layout-data).

La classe Layout peut ensuite être consommée en l’ajoutant à un [`Page`](xref:Xamarin.Forms.Page) , et en ajoutant des enfants à la disposition. Pour plus d’informations, consultez [utilisation du WrapLayout](#consume-the-wraplayout).

### <a name="create-a-wraplayout"></a>Créer un WrapLayout

L’exemple d’application illustre une classe sensible à l’orientation `WrapLayout` qui réorganise ses enfants horizontalement sur la page, puis encapsule l’affichage des enfants suivants sur des lignes supplémentaires.

La `WrapLayout` classe alloue la même quantité d’espace pour chaque enfant, connue sous le nom de *taille de cellule*, en fonction de la taille maximale des enfants. Les enfants inférieurs à la taille de la cellule peuvent être positionnés dans la cellule en fonction de leurs [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valeurs de propriété et.

La `WrapLayout` définition de classe est présentée dans l’exemple de code suivant :

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

#### <a name="calculate-and-cache-layout-data"></a>Calculer et mettre en cache les données de disposition

La `LayoutData` structure stocke les données relatives à une collection d’enfants dans plusieurs propriétés :

- `VisibleChildCount`: nombre d’enfants visibles dans la disposition.
- `CellSize`: taille maximale de tous les enfants, ajustée à la taille de la disposition.
- `Rows`: nombre de lignes.
- `Columns`: nombre de colonnes.

Le `layoutDataCache` champ est utilisé pour stocker plusieurs `LayoutData` valeurs. Lorsque l’application démarre, deux `LayoutData` objets sont mis en cache dans le `layoutDataCache` dictionnaire pour l’orientation actuelle : un pour les arguments de contrainte à la `OnMeasure` substitution et un pour les `width` arguments et `height` à la `LayoutChildren` substitution. Lors de la rotation de l’appareil en orientation paysage, le `OnMeasure` remplacement et le `LayoutChildren` remplacement seront de nouveau appelés, ce qui entraînera la mise en cache d’un autre `LayoutData` objet dans le dictionnaire. Toutefois, lors du retour de l’appareil à l’orientation portrait, aucun autre calcul n’est nécessaire, car le `layoutDataCache` contient déjà les données requises.

L’exemple de code suivant montre la `GetLayoutData` méthode, qui calcule les propriétés de la `LayoutData` structure structurée en fonction d’une taille particulière :

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

La `GetLayoutData` méthode effectue les opérations suivantes :

- Elle détermine si une `LayoutData` valeur calculée est déjà dans le cache et la retourne si elle est disponible.
- Dans le cas contraire, il énumère tous les enfants, en appelant la `Measure` méthode sur chaque enfant avec une largeur et une hauteur infinies, et détermine la taille maximale de l’enfant.
- À condition qu’il y ait au moins un enfant visible, il calcule le nombre de lignes et de colonnes requises, puis calcule une taille de cellule pour les enfants en fonction des dimensions de `WrapLayout` . Notez que la taille de la cellule est généralement légèrement plus grande que la taille maximale de l’enfant, mais qu’elle peut également être plus petite si le `WrapLayout` n’est pas suffisamment grand pour l’enfant le plus grand, ou suffisamment grand pour l’enfant le plus élevé.
- Elle stocke la nouvelle `LayoutData` valeur dans le cache.

#### <a name="add-properties-backed-by-bindable-properties"></a>Ajouter des propriétés sauvegardées par des propriétés pouvant être liées

La `WrapLayout` classe définit `ColumnSpacing` les `RowSpacing` Propriétés et, dont les valeurs sont utilisées pour séparer les lignes et les colonnes dans la mise en page et qui sont sauvegardées par des propriétés pouvant être liées. Les propriétés pouvant être liées sont indiquées dans l’exemple de code suivant :

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

Le gestionnaire de modification de propriété de chaque propriété pouvant être liée appelle la `InvalidateLayout` substitution de méthode pour déclencher une nouvelle passe de disposition sur `WrapLayout` . Pour plus d’informations, consultez [substitution de la méthode InvalidateLayout](#override-the-invalidatelayout-method) et [substitution de la méthode OnChildMeasureInvalidated](#override-the-onchildmeasureinvalidated-method).

#### <a name="override-the-onmeasure-method"></a>Remplacer la méthode OnMeasure

Le `OnMeasure` remplacement est illustré dans l’exemple de code suivant :

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

La substitution appelle la `GetLayoutData` méthode et construit un `SizeRequest` objet à partir des données retournées, tout en tenant compte également des `RowSpacing` `ColumnSpacing` valeurs de propriété et. Pour plus d’informations sur la `GetLayoutData` méthode, consultez [calculer et mettre en cache les données de disposition](#calculate-and-cache-layout-data).

> [!IMPORTANT]
> [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)) et les [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthodes ne doivent jamais demander une dimension infinie en retournant une [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) valeur avec une propriété dont la valeur est `Double.PositiveInfinity` . Toutefois, au moins l’un des arguments de contrainte de `OnMeasure` peut avoir la valeur `Double.PositiveInfinity` .

#### <a name="override-the-layoutchildren-method"></a>Remplacer la méthode LayoutChildren

Le `LayoutChildren` remplacement est illustré dans l’exemple de code suivant :

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

La substitution commence par un appel à la `GetLayoutData` méthode, puis énumère tous les enfants pour les dimensionner et les positionner dans chaque cellule de l’enfant. Pour cela, vous devez appeler [ `LayoutChildIntoBoundingRegion` ] (XREF : Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle)), qui est utilisée pour positionner un enfant dans un rectangle en fonction de ses [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valeurs de propriété et. Cela équivaut à effectuer un appel à la [ `Layout` ] (XREF : Xamarin.Forms . VisualElement. Layout ( Xamarin.Forms . Rectangle)).

> [!NOTE]
> Notez que le rectangle passé à la `LayoutChildIntoBoundingRegion` méthode comprend l’ensemble de la zone dans laquelle l’enfant peut résider.

Pour plus d’informations sur la `GetLayoutData` méthode, consultez [calculer et mettre en cache les données de disposition](#calculate-and-cache-layout-data).

#### <a name="override-the-invalidatelayout-method"></a>Remplacer la méthode InvalidateLayout

La [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) substitution est appelée lorsque des enfants sont ajoutés ou supprimés de la disposition, ou lorsque l’une des `WrapLayout` Propriétés change de valeur, comme indiqué dans l’exemple de code suivant :

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

Le remplacement invalide la disposition et ignore toutes les informations de mise en page mises en cache.

> [!NOTE]
> Pour arrêter la [`Layout`](xref:Xamarin.Forms.Layout) classe appelant la [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) méthode chaque fois qu’un enfant est ajouté ou supprimé dans une disposition, substituez le [ `ShouldInvalidateOnChildAdded` ] (XREF : Xamarin.Forms . Layout. ShouldInvalidateOnChildAdded ( Xamarin.Forms . View)) et [ `ShouldInvalidateOnChildRemoved` ] (XREF : Xamarin.Forms . Layout. ShouldInvalidateOnChildRemoved ( Xamarin.Forms . View)), et retournent `false` . La classe Layout peut ensuite implémenter un processus personnalisé lorsque des enfants sont ajoutés ou supprimés.

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Remplacer la méthode OnChildMeasureInvalidated

La [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) substitution est appelée quand l’un des enfants de la disposition change de taille et est illustré dans l’exemple de code suivant :

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

Le remplacement invalide la disposition enfant et ignore toutes les informations de mise en page mises en cache.

### <a name="consume-the-wraplayout"></a>Utilisation du WrapLayout

La `WrapLayout` classe peut être consommée en la plaçant sur un [`Page`](xref:Xamarin.Forms.Page) type dérivé, comme illustré dans l’exemple de code XAML suivant :

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

Le code C# équivalent est illustré ci-dessous :

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

Les enfants peuvent ensuite être ajoutés au `WrapLayout` en fonction des besoins. L’exemple de code suivant montre des [`Image`](xref:Xamarin.Forms.Image) éléments ajoutés à `WrapLayout` :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

Lorsque la page contenant le `WrapLayout` s’affiche, l’exemple d’application accède de façon asynchrone à un fichier JSON distant contenant une liste de photos, crée un [`Image`](xref:Xamarin.Forms.Image) élément pour chaque photo et l’ajoute à `WrapLayout` . Cela donne l’affichage illustré dans les captures d’écran suivantes :

![](custom-images/portait-screenshots.png "Sample Application Portrait Screenshots")

Les captures d’écran suivantes montrent le `WrapLayout` après sa rotation en orientation paysage :

![](custom-images/landscape-ios.png "Sample iOS Application Landscape Screenshot")
![](custom-images/landscape-android.png "Sample Android Application Landscape Screenshot")
![](custom-images/landscape-uwp.png "Sample UWP Application Landscape Screenshot")

Le nombre de colonnes dans chaque ligne dépend de la taille de la photo, de la largeur de l’écran et du nombre de pixels par unité indépendante du périphérique. Les [`Image`](xref:Xamarin.Forms.Image) éléments chargent les photos de manière asynchrone et, par conséquent, la `WrapLayout` classe recevra des appels fréquents à sa [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) méthode, car chaque `Image` élément reçoit une nouvelle taille en fonction de la photo chargée.

## <a name="related-links"></a>Liens connexes

- [WrapLayout (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Dispositions personnalisées](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Création de dispositions personnalisées dans Xamarin.Forms (vidéo)](https://www.youtube.com/watch?v=sxjOqNZFhKU)
- [Layout\<T>](xref:Xamarin.Forms.Layout`1)
- [Disposition](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)

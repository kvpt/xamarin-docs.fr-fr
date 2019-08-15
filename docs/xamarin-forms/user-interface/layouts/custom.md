---
title: Créer une disposition personnalisée dans Xamarin. Forms
description: Cet article explique comment écrire une classe de disposition personnalisée et illustre une classe de WrapLayout orientation accents qui réorganise ses enfants horizontalement sur la page et encapsule ensuite afficher les enfants suivants à des lignes supplémentaires.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 0f2136aa4a07d289e1e8aecc6cb37460fdc5727c
ms.sourcegitcommit: 157da886e1f304c6b482aa3f265ef7d78b696ab7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69024532"
---
# <a name="create-a-custom-layout-in-xamarinforms"></a>Créer une disposition personnalisée dans Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Forms définit quatre classes de mise en page – StackLayout, DispositionAbsolue, RelativeLayout et grille, et chacune dispose ses enfants d’une manière différente. Toutefois, il est parfois nécessaire organiser le contenu de la page à l’aide d’une disposition non fournie par Xamarin.Forms. Cet article explique comment écrire une classe de disposition personnalisée et illustre une classe de WrapLayout orientation accents qui réorganise ses enfants horizontalement sur la page et encapsule ensuite afficher les enfants suivants à des lignes supplémentaires._

Dans Xamarin.Forms, dont dérivent toutes les classes de mise en page le [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe et de contraindre le type générique à [ `View` ](xref:Xamarin.Forms.View) et ses types dérivés. À son tour, le `Layout<T>` classe dérive de la [ `Layout` ](xref:Xamarin.Forms.Layout) (classe), qui fournit le mécanisme pour les enfants de dimensionnement et de positionnement des éléments.

Chaque élément visuel est chargé de déterminer sa propre taille par défaut, ce qui est appelé le *demandé* taille. [`Page`](xref:Xamarin.Forms.Page), [ `Layout` ](xref:Xamarin.Forms.Layout), et [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) types dérivés sont chargés de déterminer l’emplacement et la taille de leurs enfants, ou enfants, par rapport à eux-mêmes. Par conséquent, la mise en page implique une relation parent-enfant, où le parent détermine ce qui doit être la taille de ses enfants, mais tente de prendre en compte la taille demandée de l’enfant.

Une bonne compréhension des cycles de mise en page et invalidation Xamarin.Forms est nécessaire pour créer une disposition personnalisée. Ces cycles aborderons maintenant.

## <a name="layout"></a>Mise en page

Disposition commence en haut de l’arborescence visuelle avec une page, et elle se poursuit dans toutes les branches de l’arborescence visuelle pour englober tous les éléments visuels sur une page. Les éléments qui sont des parents à d’autres éléments sont responsables de dimensionnement et de positionnement de leurs enfants par rapport à eux-mêmes.

Le [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe définit un [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) méthode qui mesure un élément pour les opérations de mise en page, et un [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) méthode spécifie la zone rectangulaire de. que l' élément sera rendu. Lorsque l’application démarre et la première page s’affiche, un *cycle de disposition* comprenant le premier des `Measure` appels, puis `Layout` appelle démarre sur le [ `Page` ](xref:Xamarin.Forms.Page) objet :

1. Au cours du cycle de mise en page, chaque élément parent est chargée d’appeler le `Measure` (méthode) sur ses enfants.
1. Après que les enfants ont été mesurés, chaque élément parent est chargée d’appeler le `Layout` (méthode) sur ses enfants.

Ce cycle garantit que chaque élément visuel sur la page reçoit des appels à la `Measure` et `Layout` méthodes. Le processus est illustré dans le diagramme suivant :

![](custom-images/layout-cycle.png "Cycle de disposition de Xamarin.Forms")

> [!NOTE]
> Notez que les cycles de mise en page peuvent également se produire sur un sous-ensemble de l’arborescence visuelle si quelque chose change pour affecter la disposition. Cela inclut les éléments soient ajoutés ou supprimés à partir d’une collection, comme dans un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), une modification dans le [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) propriété d’un élément ou une modification de la taille d’un élément.

Chaque classe de Xamarin.Forms a une `Content` ou un `Children` propriété a un substituable [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) (méthode). Les classes de disposition personnalisée qui dérivent [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) doit substituer cette méthode et vérifiez que le [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) et [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) sont des méthodes appelée sur tous les enfants de l’élément, pour fournir la disposition personnalisée souhaitée.

En outre, chaque classe qui dérive de [ `Layout` ](xref:Xamarin.Forms.Layout) ou [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) doit remplacer le [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) (méthode), c'est-à-dire où une classe de mise en page Détermine la taille qu’il doit être en effectuant des appels à la [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) méthodes de ses enfants.

> [!NOTE]
> Éléments déterminent leur taille selon *contraintes*, qui indiquent la quantité d’espace est disponible pour un élément dans le parent de l’élément. Contraintes passé à la [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) et [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthodes peuvent aller de 0 à `Double.PositiveInfinity`. Un élément est *contraint*, ou *complètement contraint*, lorsqu’il reçoit un appel à son [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) méthode avec des arguments n’est pas infini - l’élément est limitée pour une taille particulière. Un élément est *sans contrainte*, ou *partiellement contraint*, lorsqu’il reçoit un appel à son `Measure` méthode avec au moins un argument égal à `Double.PositiveInfinity` – la contrainte infinie peut être considéré comme indiquant le redimensionnement automatique.

## <a name="invalidation"></a>Invalidation

Invalidation est le processus par lequel une modification dans un élément sur une page déclenche un nouveau cycle de disposition. Éléments sont considérés comme non valides quand ils n’ont plus la bonne taille ou la position. Par exemple, si le [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize) propriété d’un [ `Button` ](xref:Xamarin.Forms.Button) modifications, le `Button` est dite non valide, car il n’a plus la taille correcte. Redimensionner le `Button` peut ensuite posséder les répercussions des modifications de disposition via le reste d’une page.

Éléments invalident eux-mêmes en appelant le [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) (méthode), généralement quand une propriété de l’élément change peuvent aboutir à une nouvelle taille de l’élément. Cette méthode déclenche la [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) événement, qui gère les parent de l’élément pour déclencher un nouveau cycle de disposition.

Le [ `Layout` ](xref:Xamarin.Forms.Layout) classe définit un gestionnaire pour le [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) événement sur chaque enfant ajouté à son `Content` propriété ou `Children` collection et détache le gestionnaire lorsque le enfant est supprimé. Par conséquent, chaque élément dans l’arborescence visuelle qui a des enfants est averti chaque fois qu’un de ses enfants change de taille. Le diagramme suivant illustre comment une modification de la taille d’un élément dans l’arborescence visuelle peut entraîner des modifications ripple l’arborescence :

![](custom-images/invalidation.png "Invalidation de l’arborescence visuelle")

Toutefois, la `Layout` classe tente de limiter l’impact d’un changement de taille d’un enfant sur la disposition d’une page. Si la disposition est de taille limitée, puis un changement de taille enfant n’affecte pas quoi que ce soit supérieure à la disposition du parent dans l’arborescence visuelle. Toutefois, une modification de la taille d’une disposition affecte d’habitude comment la mise en page dispose ses enfants. Par conséquent, toute modification de la taille d’une disposition démarrera un cycle de disposition pour la disposition et la mise en page recevront des appels à ses [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) et [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) méthodes.

Le [ `Layout` ](xref:Xamarin.Forms.Layout) classe définit également un [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) méthode qui a un objectif similaire à la [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) (méthode). Le `InvalidateLayout` méthode doit être appelée chaque fois qu’une modification est apportée qui affecte la façon dont la disposition positionne et redimensionne des ses enfants. Par exemple, le `Layout` classe appelle le `InvalidateLayout` méthode chaque fois qu’un enfant est ajouté ou supprimé à partir d’une mise en page.

Le [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) peut être substituée pour implémenter un cache pour réduire les appels successifs de la [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) méthodes les enfants de la disposition. Substitution de la `InvalidateLayout` méthode fournira une notification de lorsque les enfants sont ajoutés ou supprimés à partir de la mise en page. De même, le [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) méthode peut être substituée pour fournir une notification lorsqu’un des enfants de la disposition change de taille. Pour les deux substitutions de méthode, une disposition personnalisée doit répondre en effaçant le cache. Pour plus d’informations, consultez [calcul et la mise en cache des données](#caching).

## <a name="create-a-custom-layout"></a>Créer une disposition personnalisée

Le processus de création d’une disposition personnalisée est la suivante :

1. Créez une classe qui dérive de la classe `Layout<View>`. Pour plus d’informations, consultez [création d’un WrapLayout](#creating).
1. [*facultatif*] ajouter des propriétés, assorties de propriétés pouvant être liées, pour tous les paramètres qui doivent être définies sur la classe de mise en page. Pour plus d’informations, consultez [Ajout de propriétés assorties de propriétés pouvant être liées](#adding_properties).
1. Remplacer le [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthode à appeler le [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) méthode sur tous les la disposition, les enfants retour une taille demandée pour la mise en page. Pour plus d’informations, consultez [substitution de la méthode OnMeasure](#onmeasure).
1. Remplacer le [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) méthode à appeler le [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (méthode) sur les enfants de tous les la disposition. Échec d’appel la [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) méthode sur chaque enfant dans une disposition entraînera l’enfant reçoit jamais une taille correcte ou une position et par conséquent, l’enfant ne seront pas visible dans la page. Pour plus d’informations, consultez [substitution de la méthode LayoutChildren](#layoutchildren).

  > [!NOTE]
>  Lors de l’énumération des enfants dans le [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) et [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) remplacements, ignorer tout enfant dont [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) propriété est définie sur `false`. Cela garantit que la disposition personnalisée ne laissez d’espace pour les enfants invisibles.

1. [*facultatif*] remplacer le [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) méthode pour être averti lorsque des enfants sont ajoutés ou supprimés à partir de la mise en page. Pour plus d’informations, consultez [substitution de la méthode InvalidateLayout](#invalidatelayout).
1. [*facultatif*] remplacer le [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) méthode d’être averti lorsque l’une des enfants de la disposition change de taille. Pour plus d’informations, consultez [substitution de la méthode OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Notez que le [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) override ne sont pas être appelée si la taille de la disposition est régie par son parent, plutôt que de ses enfants. Toutefois, le remplacement sera appelé si une ou des contraintes sont infinies, ou si la classe de disposition a non-valeur par défaut [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) ou [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) les valeurs de propriété. Pour cette raison, le [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) override ne peut pas s’appuient sur les tailles enfant obtenus au cours de la [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) appel de méthode. Au lieu de cela, `LayoutChildren` doit appeler le [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) (méthode) sur les enfants de la disposition, avant d’appeler le [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (méthode). Également, la taille des enfants obtenue dans le `OnMeasure` remplacement peut être mis en cache pour éviter les ultérieurement `Measure` en appels de la `LayoutChildren` override, mais la classe de mise en page devez connaître lorsque les tailles doivent encore être obtenues. Pour plus d’informations, consultez [calcul et la mise en cache les données de disposition](#caching).

La classe de disposition peut ensuite être consommée en l’ajoutant à un [ `Page` ](xref:Xamarin.Forms.Page)et en ajoutant des enfants à la disposition. Pour plus d’informations, consultez [consomme le WrapLayout](#consuming).

<a name="creating" />

### <a name="create-a-wraplayout"></a>Créer un WrapLayout

L’exemple d’application montre l’orientation accents `WrapLayout` classe qui réorganise ses enfants horizontalement sur la page et encapsule ensuite afficher les enfants suivants à des lignes supplémentaires.

Le `WrapLayout` classe alloue la même quantité d’espace pour chaque enfant, appelé le *taille de cellule*, en fonction de la taille maximale des enfants. Enfants plus petits que la taille de la cellule peut être positionnée dans la cellule en fonction de leur [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) et [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) les valeurs de propriété.

Le `WrapLayout` définition de classe est indiquée dans l’exemple de code suivant :

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculate-and-cache-layout-data"></a>Calculer et mettre en cache les données de disposition

Le `LayoutData` structure stocke les données sur une collection d’enfants dans un nombre de propriétés :

- `VisibleChildCount` – le nombre d’enfants qui sont visibles dans la disposition.
- `CellSize` – la taille maximale de tous les enfants, ajustée à la taille de la disposition.
- `Rows` – le nombre de lignes.
- `Columns` – le nombre de colonnes.

Le `layoutDataCache` champ est utilisé pour stocker plusieurs `LayoutData` valeurs. Lorsque l’application démarre, deux `LayoutData` objets seront mis en cache dans le `layoutDataCache` dictionnaire pour l’orientation actuelle : un pour les arguments de contrainte à la `OnMeasure` override et l’autre pour le `width` et `height` arguments pour le `LayoutChildren` remplacer. Lors de la rotation de l’appareil dans l’orientation paysage, le `OnMeasure` substituer et la `LayoutChildren` remplacement sera à nouveau appelé, ce qui entraînera un autre deux `LayoutData` objets mis en cache dans le dictionnaire. Toutefois, lors du retour de l’appareil à l’orientation portrait, aucuns plus de calculs ne sont requis car le `layoutDataCache` possède déjà les données requises.

Le code suivant montre l’exemple le `GetLayoutData` (méthode), qui calcule les propriétés de la `LayoutData` structuré selon une taille particulière :

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

Le `GetLayoutData` méthode effectue les opérations suivantes :

- Il détermine si un calculé `LayoutData` valeur se trouve déjà dans le cache et retourne, s’il est disponible.
- Sinon, il énumère tous les enfants, en appelant le `Measure` méthode sur chaque enfant avec une largeur infinie et la hauteur et détermine la taille maximale d’enfant.
- Sous réserve qu’il y ait au moins un enfant visible, il calcule le nombre de lignes et colonnes nécessaires, puis calcule une taille de cellule pour les enfants selon les dimensions de la `WrapLayout`. Notez que la taille de la cellule est généralement légèrement plus large que la taille maximale d’enfants, mais qu’elle peut également être inférieure si le `WrapLayout` n’est pas assez large pour le plus large enfant ou suffisamment grande pour l’enfant plus haut.
- Il stocke le nouveau `LayoutData` valeur dans le cache.

<a name="adding_properties" />

#### <a name="add-properties-backed-by-bindable-properties"></a>Ajouter des propriétés sauvegardées par des propriétés pouvant être liées

Le `WrapLayout` classe définit `ColumnSpacing` et `RowSpacing` propriétés, dont les valeurs sont utilisées pour séparer les lignes et colonnes dans la disposition, et qui sont assortie de propriétés pouvant être liées. Les propriétés pouvant être liées sont affichées dans l’exemple de code suivant :

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

Appelle le Gestionnaire de modification de propriété de chaque propriété à lier le `InvalidateLayout` substitution de méthode pour déclencher une nouvelle disposition passer le `WrapLayout`. Pour plus d’informations, consultez [substitution de la méthode InvalidateLayout](#invalidatelayout) et [substitution de la méthode OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="override-the-onmeasure-method"></a>Remplacer la méthode OnMeasure

Le `OnMeasure` override est illustré dans l’exemple de code suivant :

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

Appelle la substitution de la `GetLayoutData` (méthode) et les constructions un `SizeRequest` objet des données retournées, tout en prenant en compte la `RowSpacing` et `ColumnSpacing` les valeurs de propriété. Pour plus d’informations sur la `GetLayoutData` (méthode), consultez [calcul et la mise en cache des données](#caching).

> [!IMPORTANT]
> Le [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) et [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) méthodes ne doivent jamais demander une dimension infinie en retournant un [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest) valeur avec une propriété définie sur `Double.PositiveInfinity`. Toutefois, au moins un des arguments de contrainte à `OnMeasure` peut être `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="override-the-layoutchildren-method"></a>Remplacer la méthode LayoutChildren

Le `LayoutChildren` override est illustré dans l’exemple de code suivant :

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

Le remplacement commence par un appel à la `GetLayoutData` (méthode), puis énumère tous les enfants pour dimensionner et positionner les au sein de la cellule de chaque enfant. Cela est obtenue en appelant le [ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)) (méthode), qui est utilisé pour positionner un enfant dans un rectangle en fonction de son [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) et [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)les valeurs de propriété. Cela équivaut à un appel à l’enfant [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) (méthode).

> [!NOTE]
> Notez que le rectangle passé à la `LayoutChildIntoBoundingRegion` méthode inclut l’ensemble du domaine dans lequel l’enfant peut résider.

Pour plus d’informations sur la `GetLayoutData` (méthode), consultez [calcul et la mise en cache des données](#caching).

<a name="invalidatelayout" />

#### <a name="overridethe-invalidatelayout-method"></a>Méthode Overridethe InvalidateLayout

Le [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) override est appelée lorsque les enfants sont ajoutés ou supprimés à partir de la mise en page, ou lorsque l’une de le `WrapLayout` propriétés change de valeur, comme indiqué dans l’exemple de code suivant :

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

Le remplacement invalide la disposition et ignore toutes les informations de présentation mis en cache.

> [!NOTE]
> Pour arrêter la [ `Layout` ](xref:Xamarin.Forms.Layout) appel de la classe la [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) méthode chaque fois qu’un enfant est ajouté ou supprimé d’une disposition, remplacer le [ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View)) et [ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View)) méthodes et retour `false`. La classe de disposition peut ensuite implémenter un processus personnalisé lorsque les enfants sont ajoutés ou supprimés.

<a name="onchildmeasureinvalidated" />

#### <a name="override-the-onchildmeasureinvalidated-method"></a>Remplacer la méthode OnChildMeasureInvalidated

Le [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) override est appelé lorsqu’un des enfants de la disposition change de taille et est illustré dans l’exemple de code suivant :

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

Le remplacement invalide la disposition de l’enfant et ignore toutes les informations de présentation mis en cache.

<a name="consuming" />

### <a name="consume-the-wraplayout"></a>Utilisation du WrapLayout

Le `WrapLayout` classe peut être consommée en le plaçant sur un [ `Page` ](xref:Xamarin.Forms.Page) type dérivé, comme illustré dans l’exemple de code XAML suivant :

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

Le code c# équivalent est indiqué ci-dessous :

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

Enfants peuvent ensuite être ajoutés à la `WrapLayout` en fonction des besoins. Le code suivant montre l’exemple [ `Image` ](xref:Xamarin.Forms.Image) éléments ajoutés à la `WrapLayout`:

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

Lorsque la page contenant le `WrapLayout` s’affiche, de façon asynchrone, l’exemple d’application accède à un fichier JSON à distance contenant une liste de photos, crée un [ `Image` ](xref:Xamarin.Forms.Image) élément pour chaque photo et l’ajoute à la `WrapLayout`. Cela provoque l’affichage indiqué dans les captures d’écran suivante :

![](custom-images/portait-screenshots.png "Captures d’écran Portrait d’Application exemple")

L’émission des captures d’écran suivante le `WrapLayout` après tourne orientation paysage :

![](custom-images/landscape-ios.png "Exemples iOS Application capture d’écran paysage")
![](custom-images/landscape-android.png "exemple Android Application paysage capture d’écran de")
![](custom-images/landscape-uwp.png " Capture d’écran du paysage Application exemple UWP")

Le nombre de colonnes dans chaque ligne dépend de la taille de la photo, la largeur d’écran et le nombre de pixels par unité indépendante du périphérique. Le [ `Image` ](xref:Xamarin.Forms.Image) éléments chargement de façon asynchrone les photos et par conséquent le `WrapLayout` classe recevront des appels fréquents à son [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) chacun la méthode `Image` élément reçoit une nouvelle taille en fonction de la photo chargée.

## <a name="related-links"></a>Liens associés

- [WrapLayout (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [Dispositions personnalisées](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Création de dispositions personnalisées dans Xamarin.Forms (vidéo)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Disposition<T>](xref:Xamarin.Forms.Layout`1)
- [Disposition](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)

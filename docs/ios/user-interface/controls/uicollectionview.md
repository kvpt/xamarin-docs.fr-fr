---
title: Vues de collection dans Xamarin. iOS
description: Les vues de collection permettent d’afficher le contenu à l’aide de dispositions arbitraires. Ils permettent de créer facilement des dispositions de type grille prêtes à l’emploi, tout en prenant en charge les dispositions personnalisées.
ms.prod: xamarin
ms.assetid: F4B85F25-0CB5-4FEA-A3B5-D22FCDC81AE4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 29483ca184786f8f0d2a7d0d9345608542d30e61
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768853"
---
# <a name="collection-views-in-xamarinios"></a>Vues de collection dans Xamarin. iOS

_Les vues de collection permettent d’afficher le contenu à l’aide de dispositions arbitraires. Ils permettent de créer facilement des dispositions de type grille prêtes à l’emploi, tout en prenant en charge les dispositions personnalisées._

Les vues de collection, disponibles `UICollectionView` dans la classe, sont un nouveau concept dans iOS 6 qui introduit la présentation de plusieurs éléments à l’écran à l’aide de dispositions. Les modèles permettant de fournir des données `UICollectionView` à un pour créer des éléments et interagir avec ces éléments suivent les mêmes modèles de délégation et de source de données que ceux utilisés couramment dans le développement iOS.

Toutefois, les `UICollectionView` vues de collection fonctionnent avec un sous-système de disposition qui est indépendant du lui-même. Par conséquent, le simple fait de fournir une disposition différente peut facilement modifier la présentation d’une vue de collection.

iOS fournit une classe de disposition `UICollectionViewFlowLayout` appelée qui permet de créer des dispositions basées sur les lignes, telles qu’une grille, sans travail supplémentaire. En outre, il est également possible de créer des dispositions personnalisées qui autorisent toute présentation que vous pouvez imaginer.

## <a name="uicollectionview-basics"></a>Notions de base de UICollectionView

La `UICollectionView` classe est composée de trois éléments différents :

- **Cellules** -vues pilotées par les données pour chaque élément
- **Vues supplémentaires** : vues pilotées par les données associées à une section.
- **Vues de décoration** -vues non pilotées par les données créées par une disposition

## <a name="cells"></a>Cellules

Les cellules sont des objets qui représentent un seul élément dans le jeu de données qui est présenté par la vue de collection. Chaque cellule est une instance de la `UICollectionViewCell` classe, qui est composée de trois vues différentes, comme le montre la figure ci-dessous :

 [![](uicollectionview-images/01-uicollectionviewcell.png "Chaque cellule est composée de trois vues différentes, comme illustré ici")](uicollectionview-images/01-uicollectionviewcell.png#lightbox)

La `UICollectionViewCell` classe a les propriétés suivantes pour chacune de ces vues :

- `ContentView`: Cette vue contient le contenu que la cellule présente. Il est affiché dans l’ordre de plan le plus haut sur l’écran.
- `SelectedBackgroundView`: Les cellules prennent en charge la sélection. Cette vue est utilisée pour indiquer visuellement qu’une cellule est sélectionnée. Il s’affiche juste en dessous `ContentView` du lorsqu’une cellule est sélectionnée.
- `BackgroundView`: Les cellules peuvent également afficher un arrière-plan, qui est `BackgroundView` présenté par le. Cette vue est affichée sous le `SelectedBackgroundView` .

En définissant `ContentView` le tel qu’il est plus petit `BackgroundView` que `SelectedBackgroundView`et, `BackgroundView` le peut `SelectedBackgroundView` être utilisé pour imager visuellement le contenu, tandis que est affiché quand une cellule est sélectionnée, comme indiqué ci-dessous :

 [![](uicollectionview-images/02-cells.png "Les différents éléments de cellule")](uicollectionview-images/02-cells.png#lightbox)

Les cellules de la capture d’écran ci-dessus sont créées `UICollectionViewCell` en héritant `ContentView`de et `BackgroundView` en définissant les propriétés, `SelectedBackgroundView` et, respectivement, comme indiqué dans le code suivant :

```csharp
public class AnimalCell : UICollectionViewCell
{
        UIImageView imageView;

        [Export ("initWithFrame:")]
        public AnimalCell (CGRect frame) : base (frame)
        {
            BackgroundView = new UIView{BackgroundColor = UIColor.Orange};

            SelectedBackgroundView = new UIView{BackgroundColor = UIColor.Green};

            ContentView.Layer.BorderColor = UIColor.LightGray.CGColor;
            ContentView.Layer.BorderWidth = 2.0f;
            ContentView.BackgroundColor = UIColor.White;
            ContentView.Transform = CGAffineTransform.MakeScale (0.8f, 0.8f);

            imageView = new UIImageView (UIImage.FromBundle ("placeholder.png"));
            imageView.Center = ContentView.Center;
            imageView.Transform = CGAffineTransform.MakeScale (0.7f, 0.7f);

            ContentView.AddSubview (imageView);
        }

        public UIImage Image {
            set {
                imageView.Image = value;
            }
        }
}
```

 <a name="Supplementary_Views" />

## <a name="supplementary-views"></a>Vues supplémentaires

Les vues supplémentaires sont des vues qui présentent les informations associées à chaque `UICollectionView`section d’un. À l’instar des cellules, les vues supplémentaires sont pilotées par les données. Lorsque des cellules présentent les données d’un élément à partir d’une source de données, les vues supplémentaires présentent les données de la section, telles que les catégories de livres dans une bibliothèque virtuelle ou le genre de musique dans une bibliothèque musicale.

Par exemple, une vue supplémentaire peut être utilisée pour présenter un en-tête pour une section particulière, comme illustré dans la figure ci-dessous :

 [![](uicollectionview-images/02a-supplementary-view.png "Vue supplémentaire utilisée pour présenter un en-tête pour une section particulière, comme indiqué ici")](uicollectionview-images/02a-supplementary-view.png#lightbox)

Pour utiliser une vue supplémentaire, elle doit d’abord être inscrite dans `ViewDidLoad` la méthode :

```csharp
CollectionView.RegisterClassForSupplementaryView (typeof(Header), UICollectionElementKindSection.Header, headerId);
```

Ensuite, la vue doit être retournée à l' `GetViewForSupplementaryElement`aide de, créé `DequeueReusableSupplementaryView`à l’aide de, `UICollectionReusableView`et hérite de. L’extrait de code suivant produira le SupplementaryView illustré dans la capture d’écran ci-dessus :

```csharp
public override UICollectionReusableView GetViewForSupplementaryElement (UICollectionView collectionView, NSString elementKind, NSIndexPath indexPath)
        {
            var headerView = (Header)collectionView.DequeueReusableSupplementaryView (elementKind, headerId, indexPath);
            headerView.Text = "Supplementary View";
            return headerView;
        }

```

Les vues supplémentaires sont plus génériques que des en-têtes et des pieds de page simples.
Elles peuvent être placées n’importe où dans la vue de collection et peuvent être composées de n’importe quelle vue, ce qui rend leur apparence entièrement personnalisable.

 <a name="Decoration_Views" />

## <a name="decoration-views"></a>Vues de décoration

Les vues de décoration sont purement visuelles qui peuvent être affichées `UICollectionView`dans un. Contrairement aux cellules et aux vues supplémentaires, elles ne sont pas pilotées par les données. Elles sont toujours créées dans la sous-classe d’une disposition et peuvent ensuite changer comme disposition du contenu. Par exemple, une vue de décoration peut être utilisée pour présenter une vue en arrière-plan qui défile avec `UICollectionView`le contenu de la, comme indiqué ci-dessous :

 [![](uicollectionview-images/02c-decoration-view.png "Vue de décoration avec un arrière-plan rouge")](uicollectionview-images/02c-decoration-view.png#lightbox)

 L’extrait de code ci-dessous remplace l’arrière-plan `CircleLayout` par Red dans la classe Samples :

 ```csharp
 public class MyDecorationView : UICollectionReusableView
  {
    [Export ("initWithFrame:")]
    public MyDecorationView (CGRect frame) : base (frame)
    {
      BackgroundColor = UIColor.Red;
    }
  }
 ```

## <a name="data-source"></a>source de données

Comme avec d’autres parties d’iOS, telles `UITableView` que `MKMapView`et `UICollectionView` , obtient ses données à partir d’une *source de données*, qui est exposée dans **`UICollectionViewDataSource`** Xamarin. iOS via la classe. Cette classe est chargée de fournir du contenu à `UICollectionView` l’exemple suivant :

- **Cells** : retourné `GetCell` à partir de la méthode.
- **Vues supplémentaires** : renvoyées `GetViewForSupplementaryElement` par la méthode.
- **Nombre de sections** – retourné à `NumberOfSections` partir de la méthode. La valeur par défaut est 1 si elle n’est pas implémentée.
- **Nombre d’éléments par section** – retourné à `GetItemsCount` partir de la méthode.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController
Pour plus de commodité `UICollectionViewController` , la classe est disponible. Il est automatiquement configuré pour être le délégué, qui est abordé dans la section suivante, et la source de données pour `UICollectionView` sa vue.

Comme avec `UITableView`, la `UICollectionView` classe appelle uniquement sa source de données pour obtenir des cellules pour les éléments qui se trouvent à l’écran.
Les cellules qui défilent hors de l’écran sont placées dans une file d’attente pour être réutilisées, comme illustré dans l’illustration suivante :

 [![](uicollectionview-images/03-cell-reuse.png "Les cellules qui défilent hors de l’écran sont placées dans une file d’attente pour être réutilisées comme indiqué ici")](uicollectionview-images/03-cell-reuse.png#lightbox)

La réutilisation des cellules a `UICollectionView` été `UITableView`simplifiée avec et. Vous n’avez plus besoin de créer une cellule directement dans la source de données si celle-ci n’est pas disponible dans la file d’attente de réutilisation, car les cellules sont inscrites auprès du système. Si une cellule n’est pas disponible lors de l’appel à la mise en file d’attente de la cellule à partir de la file d’attente de réutilisation, iOS la crée automatiquement en fonction du type ou du bec enregistré.
La même technique est également disponible pour les vues supplémentaires.

Par exemple, considérez le code suivant qui inscrit la `AnimalCell` classe :

```csharp
static NSString animalCellId = new NSString ("AnimalCell");
CollectionView.RegisterClassForCell (typeof(AnimalCell), animalCellId);
```

Quand un `UICollectionView` a besoin d’une cellule parce que son élément est sur l' `UICollectionView` écran, le appelle la `GetCell` méthode de sa source de données. À l’instar de la manière dont cela fonctionne avec UITableView, cette méthode est chargée de configurer une cellule à partir des données de sauvegarde `AnimalCell` , qui serait une classe dans ce cas.

Le code suivant illustre une implémentation de `GetCell` qui retourne une `AnimalCell` instance de :

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, Foundation.NSIndexPath indexPath)
{
        var animalCell = (AnimalCell)collectionView.DequeueReusableCell (animalCellId, indexPath);

        var animal = animals [indexPath.Row];

        animalCell.Image = animal.Image;

        return animalCell;
}
```

L’appel à `DequeReusableCell` est l’emplacement où la cellule sera déplacée de la file d’attente de réutilisation ou, si une cellule n’est pas disponible dans la file d’attente, créée en fonction du `CollectionView.RegisterClassForCell`type inscrit dans l’appel à.

Dans ce cas, en inscrivant `AnimalCell` la classe, iOS crée un nouveau `AnimalCell` en interne et le retourne lorsqu’un appel à la fonction de suppression de la file d’attente d’une cellule est effectué, après quoi il est configuré avec l’image contenue dans la classe animal et retourné pour l’affichage à la `UICollectionView`.

 <a name="Delegate" />

### <a name="delegate"></a>délégué

La `UICollectionView` classe utilise un délégué de type `UICollectionViewDelegate` pour prendre en charge l’interaction avec `UICollectionView`le contenu dans le. Cela permet de contrôler :

- **Sélection de cellules** : détermine si une cellule est sélectionnée.
- **Mise en surbrillance des cellules** : détermine si une cellule est actuellement touchée.
- **Menus de cellule** : menu affiché pour une cellule en réponse à un mouvement de longue durée.

Comme avec la source de données, `UICollectionViewController` est configuré par défaut pour être le délégué `UICollectionView`de.

 <a name="Cell_HighLighting" />

#### <a name="cell-highlighting"></a>Mise en surbrillance des cellules

Quand une cellule est enfoncée, la cellule passe dans un État mis en surbrillance et elle n’est pas sélectionnée tant que l’utilisateur n’a pas levé son doigt dans la cellule. Cela permet une modification temporaire de l’apparence de la cellule avant qu’elle ne soit réellement sélectionnée. Lors de `SelectedBackgroundView` la sélection, la cellule est affichée. La figure ci-dessous montre l’État mis en surbrillance juste avant que la sélection se produise :

 [![](uicollectionview-images/04-cell-highlight.png "Cette illustration montre l’État mis en surbrillance juste avant que la sélection se produise")](uicollectionview-images/04-cell-highlight.png#lightbox)

Pour implémenter la mise en `ItemHighlighted` surbrillance, `UICollectionViewDelegate` vous pouvez utiliser les méthodes et `ItemUnhighlighted` du. Par exemple, le code suivant applique un arrière-plan jaune du `ContentView` lorsque la cellule est mise en surbrillance, ainsi qu’un arrière-plan blanc en cas d’annulation de la mise en surbrillance, comme illustré dans l’image ci-dessus :

```csharp
public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.Yellow;
}

public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
{
        var cell = collectionView.CellForItem(indexPath);
        cell.ContentView.BackgroundColor = UIColor.White;
}
```

 <a name="Disabling_Selection" />

#### <a name="disabling-selection"></a>La désactivation de la sélection

La sélection est activée par défaut `UICollectionView`dans. Pour désactiver la sélection, substituez `ShouldHighlightItem` et retournez la valeur false comme indiqué ci-dessous :

```csharp
public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath)
{
        return false;
}
```

Lorsque la mise en surbrillance est désactivée, le processus de sélection d’une cellule est également désactivé. En outre, il existe également une `ShouldSelectItem` méthode qui contrôle directement la sélection, bien `ShouldHighlightItem` que si est implémenté et retourne `ShouldSelectItem` false, n’est pas appelé.

 `ShouldSelectItem`permet l’activation ou la désactivation de la sélection élément par élément, lorsque `ShouldHighlightItem` n’est pas implémenté. Elle permet également la mise en surbrillance `ShouldHighlightItem` sans sélection, si est implémenté et `ShouldSelectItem` retourne la valeur true, tandis que retourne false.

 <a name="Cell_Menus" />

#### <a name="cell-menus"></a>Menus de cellule

Chaque cellule d’un `UICollectionView` est capable d’illustrer un menu qui permet, éventuellement, de prendre en charge les opérations couper, copier et coller. Pour créer un menu Edition sur une cellule :

1. Substitue `ShouldShowMenu` et retourne la valeur true si l’élément doit afficher un menu.
1. Substituez `CanPerformAction` et retournez la valeur true pour chaque action que l’élément peut effectuer, qui correspond à l’une des opérations couper, copier ou coller.
1. Remplacement `PerformAction` pour effectuer la modification, copie de l’opération de collage.

La capture d’écran suivante montre le menu quand une cellule est appuyée sur une longue durée :

 [![](uicollectionview-images/04a-menu.png "Cette capture d’écran montre le menu quand une cellule est longuement enfoncée")](uicollectionview-images/04a-menu.png#lightbox)

 <a name="Layout" />

## <a name="layout"></a>Mise en page

`UICollectionView`prend en charge un système de disposition qui permet la gestion de tous ses éléments, cellules, vues supplémentaires et vues de décoration, indépendamment de `UICollectionView` lui-même.
À l’aide du système de disposition, une application peut prendre en charge des dispositions telles que la grille, que nous avons vu dans cet article, ainsi que fournir des dispositions personnalisées.

 <a name="Layout_Basics" />

### <a name="layout-basics"></a>Notions de base de la disposition

Les dispositions dans un `UICollectionView` sont définies dans une classe qui hérite de `UICollectionViewLayout`. L’implémentation de la disposition est chargée de créer les attributs de disposition pour chaque `UICollectionView`élément de. Il existe deux façons de créer une disposition :

- Utilisez le intégré `UICollectionViewFlowLayout` .
- Fournissez une disposition personnalisée en héritant `UICollectionViewLayout` de.

 <a name="Flow_Layout" />

### <a name="flow-layout"></a>Mise en page fluide

La `UICollectionViewFlowLayout` classe fournit une disposition linéaire qui convient pour réorganiser le contenu dans une grille de cellules, comme nous l’avons vu.

Pour utiliser une mise en page fluide :

- Créer une instance de `UICollectionViewFlowLayout` :

```csharp
var layout = new UICollectionViewFlowLayout ();
```

- Transmettez l’instance au constructeur du `UICollectionView` :

```csharp
simpleCollectionViewController = new SimpleCollectionViewController (layout);
```

C’est tout ce qui est nécessaire pour mettre en forme le contenu dans une grille. En outre, lorsque l’orientation change, `UICollectionViewFlowLayout` les poignées réorganisent le contenu de manière appropriée, comme indiqué ci-dessous :

 [![](uicollectionview-images/05-layout-orientation.png "Exemple de modification de l’orientation")](uicollectionview-images/05-layout-orientation.png#lightbox)

 <a name="Section_Inset" />

#### <a name="section-inset"></a>Incrustation de section

Pour fournir de l’espace autour `UIContentView`du, les dispositions ont `SectionInset` une propriété de `UIEdgeInsets`type. Par exemple, le code suivant fournit une mémoire tampon de 50 pixels autour de chaque section `UIContentView` du lorsqu’elle est disposée par un : `UICollectionViewFlowLayout`

```csharp
var layout = new UICollectionViewFlowLayout ();
layout.SectionInset = new UIEdgeInsets (50,50,50,50);
```

Cela entraîne un espacement autour de la section, comme indiqué ci-dessous :

 [![](uicollectionview-images/06-sectioninset.png "Espacement autour de la section, comme indiqué ici")](uicollectionview-images/06-sectioninset.png#lightbox)

 <a name="Subclassing_UICollectionViewFlowLayout" />

#### <a name="subclassing-uicollectionviewflowlayout"></a>Sous-classe UICollectionViewFlowLayout

Dans Edition, pour `UICollectionViewFlowLayout` utiliser directement, il peut également être sous-classé pour personnaliser davantage la disposition du contenu sur une ligne. Par exemple, il peut être utilisé pour créer une disposition qui n’encapsule pas les cellules dans une grille, mais crée à la place une seule ligne avec un effet de défilement horizontal, comme indiqué ci-dessous :

 [![](uicollectionview-images/07-line-layout.png "Une seule ligne avec un effet de défilement horizontal")](uicollectionview-images/07-line-layout.png#lightbox)

Pour implémenter cela par le biais `UICollectionViewFlowLayout` de sous-classes, requiert :

- Initialisation des propriétés de disposition qui s’appliquent à la disposition elle-même ou à tous les éléments de la disposition dans le constructeur.
- En remplaçant `ShouldInvalidateLayoutForBoundsChange` , en retournant true afin que, lorsque les `UICollectionView` limites des modifications sont modifiées, la disposition des cellules soit recalculée. Utilisé dans ce cas, assurez-vous que le code de la transformation appliquée à la cellule centermost sera appliqué lors du défilement.
- Substitution `TargetContentOffset` pour que la cellule centermost soit alignée au centre `UICollectionView` du au fur et à mesure que le défilement s’arrête.
- Substitution `LayoutAttributesForElementsInRect` pour retourner un tableau de `UICollectionViewLayoutAttributes` . Chaque `UICollectionViewLayoutAttribute` contient des informations sur la façon de mettre en page l’élément particulier, y `Center` compris des propriétés `Transform3D` telles que, `Size` `ZIndex` et.

Le code suivant illustre une telle implémentation :

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;
using CoreGraphics;
using CoreAnimation;

namespace SimpleCollectionView
{
  public class LineLayout : UICollectionViewFlowLayout
  {
    public const float ITEM_SIZE = 200.0f;
    public const int ACTIVE_DISTANCE = 200;
    public const float ZOOM_FACTOR = 0.3f;

    public LineLayout ()
    {
      ItemSize = new CGSize (ITEM_SIZE, ITEM_SIZE);
      ScrollDirection = UICollectionViewScrollDirection.Horizontal;
            SectionInset = new UIEdgeInsets (400,0,400,0);
      MinimumLineSpacing = 50.0f;
    }

    public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
    {
      return true;
    }

    public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
    {
      var array = base.LayoutAttributesForElementsInRect (rect);
            var visibleRect = new CGRect (CollectionView.ContentOffset, CollectionView.Bounds.Size);

      foreach (var attributes in array) {
        if (attributes.Frame.IntersectsWith (rect)) {
          float distance = (float)(visibleRect.GetMidX () - attributes.Center.X);
          float normalizedDistance = distance / ACTIVE_DISTANCE;
          if (Math.Abs (distance) < ACTIVE_DISTANCE) {
            float zoom = 1 + ZOOM_FACTOR * (1 - Math.Abs (normalizedDistance));
            attributes.Transform3D = CATransform3D.MakeScale (zoom, zoom, 1.0f);
            attributes.ZIndex = 1;
          }
        }
      }
      return array;
    }

    public override CGPoint TargetContentOffset (CGPoint proposedContentOffset, CGPoint scrollingVelocity)
    {
      float offSetAdjustment = float.MaxValue;
      float horizontalCenter = (float)(proposedContentOffset.X + (this.CollectionView.Bounds.Size.Width / 2.0));
      CGRect targetRect = new CGRect (proposedContentOffset.X, 0.0f, this.CollectionView.Bounds.Size.Width, this.CollectionView.Bounds.Size.Height);
      var array = base.LayoutAttributesForElementsInRect (targetRect);
      foreach (var layoutAttributes in array) {
        float itemHorizontalCenter = (float)layoutAttributes.Center.X;
        if (Math.Abs (itemHorizontalCenter - horizontalCenter) < Math.Abs (offSetAdjustment)) {
          offSetAdjustment = itemHorizontalCenter - horizontalCenter;
        }
      }
            return new CGPoint (proposedContentOffset.X + offSetAdjustment, proposedContentOffset.Y);
    }

  }
}
```

 <a name="Custom_Layout" />

### <a name="custom-layout"></a>Disposition personnalisée

En plus d’utiliser `UICollectionViewFlowLayout`, les dispositions peuvent également être entièrement personnalisées en héritant directement `UICollectionViewLayout`de.

Les principales méthodes de remplacement sont les suivantes :

- `PrepareLayout`: Utilisé pour effectuer des calculs géométriques initiaux qui seront utilisés tout au long du processus de disposition.
- `CollectionViewContentSize`: Retourne la taille de la zone utilisée pour afficher le contenu.
- `LayoutAttributesForElementsInRect`– Comme avec l’exemple UICollectionViewFlowLayout présenté précédemment, cette méthode est utilisée pour fournir des informations sur `UICollectionView` la façon de mettre en page chaque élément. Toutefois, contrairement à `UICollectionViewFlowLayout` , lors de la création d’une disposition personnalisée, vous pouvez positionner des éléments comme vous le souhaitez.

Par exemple, le même contenu peut être présenté dans une disposition circulaire comme indiqué ci-dessous :

 [![](uicollectionview-images/08-circle-layout.png "Disposition personnalisée circulaire comme indiqué ici")](uicollectionview-images/08-circle-layout.png#lightbox)

Le puissant aspect des dispositions est que pour passer de la disposition de type grille, à une disposition de défilement horizontal, et par la suite, à cette disposition circulaire, vous ne devez modifier que `UICollectionView` la classe de disposition fournie. Rien dans le `UICollectionView`, son délégué ou code de source de données ne change du tout.

## <a name="changes-in-ios-9"></a>Modifications apportées à iOS 9

Dans iOS 9, la vue de collection`UICollectionView`() prend désormais en charge la réorganisation des éléments par glisser-déplacer en ajoutant un nouveau module de reconnaissance de mouvement par défaut et plusieurs nouvelles méthodes de prise en charge.

À l’aide de ces nouvelles méthodes, vous pouvez facilement implémenter l’opération glisser-déplacer pour réorganiser dans votre vue de collection et avoir la possibilité de personnaliser l’apparence des éléments au cours de n’importe quelle étape du processus de réorganisation.

[![](uicollectionview-images/intro01.png "Exemple de processus de réorganisation")](uicollectionview-images/intro01.png#lightbox)

Dans cet article, nous allons examiner l’implémentation de la fonction glisser-déplacer dans une application Xamarin. iOS, ainsi que d’autres modifications apportées par iOS 9 au contrôle d’affichage de collection :

- [Réorganisation simple des éléments](#Easy-Reordering-of-Items)
  - [Exemple de réorganisation simple](#Simple-Reordering-Example)
  - [Utilisation d’un module de reconnaissance de mouvement personnalisé](#Using-a-Custom-Gesture-Recognizer)
  - [Dispositions personnalisées et réorganisation](#Custom-Layouts-and-Reording)
- [Modifications de la vue de collection](#collection-view-changes)

<a name="Easy-Reordering-of-Items" />

## <a name="reordering-of-items"></a>Réorganisation des éléments

Comme indiqué ci-dessus, l’une des modifications les plus significatives apportées à la vue de collection dans iOS 9 était l’ajout d’une fonctionnalité de glisser-déplacer facile à l’emploi.

Dans iOS 9, la méthode la plus rapide pour ajouter la réorganisation à une vue de collection consiste à `UICollectionViewController`utiliser un.
Le contrôleur d’affichage de collection a `InstallsStandardGestureForInteractiveMovement` maintenant une propriété, qui ajoute un module de *reconnaissance de mouvement* standard qui prend en charge le glissement pour réorganiser les éléments de la collection.
Étant donné que la valeur `true`par défaut est, il vous suffit `MoveItem` d’implémenter `UICollectionViewDataSource` la méthode de la classe pour prendre en charge le glisser-déplacer. Par exemple :

```csharp
public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
  // Reorder our list of items
  ...
}
```

<a name="Simple-Reordering-Example" />

### <a name="simple-reordering-example"></a>Exemple de réorganisation simple

En guise d’exemple rapide, démarrez un nouveau projet Xamarin. iOS, puis modifiez le fichier **main. Storyboard** . Faites glisser `UICollectionViewController` un sur l’aire de conception :

[![](uicollectionview-images/quick01.png "Ajout d’un UICollectionViewController")](uicollectionview-images/quick01.png#lightbox)

Sélectionnez la vue de collection (il est peut-être plus facile d’effectuer cette opération à partir de la structure du document). Dans l’onglet disposition de la Panneau Propriétés, définissez les tailles suivantes, comme illustré dans la capture d’écran ci-dessous :

- **Taille**de la cellule : Width – 60 | Hauteur – 60
- **Taille de l’en-tête**: Largeur – 0 | Hauteur – 0
- **Taille du pied de page**: Largeur – 0 | Hauteur – 0
- **Espacement minimal**: Pour les cellules – 8 | Pour les lignes – 8
- **Indéfinis de section**: Haut – 16 | Bas – 16 | Gauche – 16 | À droite – 16

[![](uicollectionview-images/quick04.png "Définir les tailles de vue de collection")](uicollectionview-images/quick04.png#lightbox)

Modifiez ensuite la cellule par défaut :
- Changer sa couleur d’arrière-plan en bleu
- Ajouter une étiquette pour agir en tant que titre de la cellule
- Définir la **cellule** identificateur de réutilisation sur

[![](uicollectionview-images/quick02.png "Modifier la cellule par défaut")](uicollectionview-images/quick02.png#lightbox)

Ajoutez des contraintes pour garder l’étiquette centrée à l’intérieur de la cellule au fur et à mesure qu’elle change de taille :

Dans le panneau des **Propriétés** de _CollectionViewCell_ et définissez la **classe** sur `TextCollectionViewCell`:

[![](uicollectionview-images/quick05.png "Définir la classe sur TextCollectionViewCell")](uicollectionview-images/quick05.png#lightbox)

Définissez la **vue réutilisable** de `Cell`la collection sur :

[![](uicollectionview-images/quick06.png "Définir la vue réutilisable de la collection sur la cellule")](uicollectionview-images/quick06.png#lightbox)

Enfin, sélectionnez l’étiquette et nommez `TextLabel`-la :

[![](uicollectionview-images/quick07.png "étiquette de nom TextLabel")](uicollectionview-images/quick07.png#lightbox)

Modifiez la `TextCollectionViewCell` classe et ajoutez les propriétés suivantes :

```csharp
using System;
using Foundation;
using UIKit;

namespace CollectionView
{
  public partial class TextCollectionViewCell : UICollectionViewCell
  {
    #region Computed Properties
    public string Title {
      get { return TextLabel.Text; }
      set { TextLabel.Text = value; }
    }
    #endregion

    #region Constructors
    public TextCollectionViewCell (IntPtr handle) : base (handle)
    {
    }
    #endregion
  }
}
```

Ici, `Text` la propriété de l’étiquette est exposée comme titre de la cellule, de sorte qu’elle peut être définie à partir du code.

Ajoutez une nouvelle C# classe au projet et appelez- `WaterfallCollectionSource`la. Modifiez le fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
  public class WaterfallCollectionSource : UICollectionViewDataSource
  {
    #region Computed Properties
    public WaterfallCollectionView CollectionView { get; set;}
    public List<int> Numbers { get; set; } = new List<int> ();
    #endregion

    #region Constructors
    public WaterfallCollectionSource (WaterfallCollectionView collectionView)
    {
      // Initialize
      CollectionView = collectionView;

      // Init numbers collection
      for (int n = 0; n < 100; ++n) {
        Numbers.Add (n);
      }
    }
    #endregion

    #region Override Methods
    public override nint NumberOfSections (UICollectionView collectionView) {
      // We only have one section
      return 1;
    }

    public override nint GetItemsCount (UICollectionView collectionView, nint section) {
      // Return the number of items
      return Numbers.Count;
    }

    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
      // Get a reusable cell and set {~~it's~>its~~} title from the item
      var cell = collectionView.DequeueReusableCell ("Cell", indexPath) as TextCollectionViewCell;
      cell.Title = Numbers [(int)indexPath.Item].ToString();

      return cell;
    }

    public override bool CanMoveItem (UICollectionView collectionView, NSIndexPath indexPath) {
      // We can always move items
      return true;
    }

    public override void MoveItem (UICollectionView collectionView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
    {
      // Reorder our list of items
      var item = Numbers [(int)sourceIndexPath.Item];
      Numbers.RemoveAt ((int)sourceIndexPath.Item);
      Numbers.Insert ((int)destinationIndexPath.Item, item);
    }
    #endregion
  }
}
```

Cette classe sera la source de données de notre vue de collection et fournira les informations pour chaque cellule de la collection.
Notez que la `MoveItem` méthode est implémentée pour permettre la réorganisation des éléments de la collection.

Ajoutez une autre C# nouvelle classe au projet et appelez- `WaterfallCollectionDelegate`la. Modifiez ce fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;

namespace CollectionView
{
  public class WaterfallCollectionDelegate : UICollectionViewDelegate
  {
    #region Computed Properties
    public WaterfallCollectionView CollectionView { get; set;}
    #endregion

    #region Constructors
    public WaterfallCollectionDelegate (WaterfallCollectionView collectionView)
    {

      // Initialize
      CollectionView = collectionView;

    }
    #endregion

    #region Overrides Methods
    public override bool ShouldHighlightItem (UICollectionView collectionView, NSIndexPath indexPath) {
      // Always allow for highlighting
      return true;
    }

    public override void ItemHighlighted (UICollectionView collectionView, NSIndexPath indexPath)
    {
      // Get cell and change to green background
      var cell = collectionView.CellForItem(indexPath);
      cell.ContentView.BackgroundColor = UIColor.FromRGB(183,208,57);
    }

    public override void ItemUnhighlighted (UICollectionView collectionView, NSIndexPath indexPath)
    {
      // Get cell and return to blue background
      var cell = collectionView.CellForItem(indexPath);
      cell.ContentView.BackgroundColor = UIColor.FromRGB(164,205,255);
    }
    #endregion
  }
}
```

Cela servira de délégué pour notre vue de collection. Les méthodes ont été remplacées pour mettre en surbrillance une cellule au fur et à mesure que l’utilisateur interagit avec elle dans la vue de collection.

Ajoutez une dernière C# classe au projet et appelez- `WaterfallCollectionView`la. Modifiez ce fichier et faites en sorte qu’il ressemble à ce qui suit :

```csharp
using System;
using UIKit;
using System.Collections.Generic;
using Foundation;

namespace CollectionView
{
  [Register("WaterfallCollectionView")]
  public class WaterfallCollectionView : UICollectionView
  {

    #region Constructors
    public WaterfallCollectionView (IntPtr handle) : base (handle)
    {
    }
    #endregion

    #region Override Methods
    public override void AwakeFromNib ()
    {
      base.AwakeFromNib ();

      // Initialize
      DataSource = new WaterfallCollectionSource(this);
      Delegate = new WaterfallCollectionDelegate(this);

    }
    #endregion
  }
}
```

Notez que `DataSource` et `Delegate` que nous avons créé ci-dessus sont définis lorsque la vue de collection est construite à partir de son Storyboard (ou fichier **. XIB** ).

Modifiez de nouveau le fichier **main. Storyboard** et sélectionnez la vue de collection et basculez vers les **Propriétés**. Définissez la **classe** sur la classe `WaterfallCollectionView` personnalisée que nous avons définie ci-dessus :

Enregistrez les modifications apportées à l’interface utilisateur et exécutez l’application.
Si l’utilisateur sélectionne un élément dans la liste et le fait glisser vers un nouvel emplacement, les autres éléments s’animent automatiquement au fur et à mesure qu’ils sortent de l’élément.
Lorsque l’utilisateur dépose l’élément à un nouvel emplacement, il s’arrête sur cet emplacement. Par exemple :

[![](uicollectionview-images/intro01.png "Exemple de glissement d’un élément vers un nouvel emplacement")](uicollectionview-images/intro01.png#lightbox)

<a name="Using-a-Custom-Gesture-Recognizer" />

### <a name="using-a-custom-gesture-recognizer"></a>Utilisation d’un module de reconnaissance de mouvement personnalisé

Dans les cas où vous ne pouvez `UICollectionViewController` pas utiliser un et que `UIViewController`vous devez utiliser un régulier, ou si vous souhaitez mieux contrôler le geste de glisser-déplacer, vous pouvez créer votre propre module de reconnaissance de mouvement personnalisé et l’ajouter à la vue de collection lorsque la vue est chargée. Par exemple :

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();

  // Create a custom gesture recognizer
  var longPressGesture = new UILongPressGestureRecognizer ((gesture) => {

    // Take action based on state
    switch(gesture.State) {
    case UIGestureRecognizerState.Began:
      var selectedIndexPath = CollectionView.IndexPathForItemAtPoint(gesture.LocationInView(View));
      if (selectedIndexPath !=null) {
        CollectionView.BeginInteractiveMovementForItem(selectedIndexPath);
      }
      break;
    case UIGestureRecognizerState.Changed:
      CollectionView.UpdateInteractiveMovementTargetPosition(gesture.LocationInView(View));
      break;
    case UIGestureRecognizerState.Ended:
      CollectionView.EndInteractiveMovement();
      break;
    default:
      CollectionView.CancelInteractiveMovement();
      break;
    }

  });

  // Add the custom recognizer to the collection view
  CollectionView.AddGestureRecognizer(longPressGesture);
}
```

Ici, nous utilisons plusieurs nouvelles méthodes ajoutées à la vue de collection pour implémenter et contrôler l’opération glisser :

- `BeginInteractiveMovementForItem`-Marque le début d’une opération de déplacement.
- `UpdateInteractiveMovementTargetPosition`-Est envoyé lorsque l’emplacement de l’élément est mis à jour.
- `EndInteractiveMovement`: Marque la fin du déplacement d’un élément.
- `CancelInteractiveMovement`-Marque l’utilisateur qui annule l’opération de déplacement.

Lorsque l’application est exécutée, l’opération glisser fonctionne exactement comme le module de reconnaissance de mouvement de glissement par défaut qui est fourni avec la vue de collection.

<a name="Custom-Layouts-and-Reording" />

### <a name="custom-layouts-and-reordering"></a>Dispositions personnalisées et réorganisation

Dans iOS 9, plusieurs nouvelles méthodes ont été ajoutées pour travailler avec les dispositions glisser-à-réorganiser et personnalisées dans une vue de collection. Pour explorer cette fonctionnalité, nous allons ajouter une disposition personnalisée à la collection.

Tout d’abord, ajoutez C# une nouvelle `WaterfallCollectionLayout` classe appelée au projet. Modifiez-le et faites-le ressembler à ce qui suit :

```csharp
using System;
using Foundation;
using UIKit;
using System.Collections.Generic;
using CoreGraphics;

namespace CollectionView
{
  [Register("WaterfallCollectionLayout")]
  public class WaterfallCollectionLayout : UICollectionViewLayout
  {
    #region Private Variables
    private int columnCount = 2;
    private nfloat minimumColumnSpacing = 10;
    private nfloat minimumInterItemSpacing = 10;
    private nfloat headerHeight = 0.0f;
    private nfloat footerHeight = 0.0f;
    private UIEdgeInsets sectionInset = new UIEdgeInsets(0, 0, 0, 0);
    private WaterfallCollectionRenderDirection itemRenderDirection = WaterfallCollectionRenderDirection.ShortestFirst;
    private Dictionary<nint,UICollectionViewLayoutAttributes> headersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
    private Dictionary<nint,UICollectionViewLayoutAttributes> footersAttributes = new Dictionary<nint, UICollectionViewLayoutAttributes>();
    private List<CGRect> unionRects = new List<CGRect>();
    private List<nfloat> columnHeights = new List<nfloat>();
    private List<UICollectionViewLayoutAttributes> allItemAttributes = new List<UICollectionViewLayoutAttributes>();
    private List<List<UICollectionViewLayoutAttributes>> sectionItemAttributes = new List<List<UICollectionViewLayoutAttributes>>();
    private nfloat unionSize = 20;
    #endregion

    #region Computed Properties
    [Export("ColumnCount")]
    public int ColumnCount {
      get { return columnCount; }
      set {
        WillChangeValue ("ColumnCount");
        columnCount = value;
        DidChangeValue ("ColumnCount");

        InvalidateLayout ();
      }
    }

    [Export("MinimumColumnSpacing")]
    public nfloat MinimumColumnSpacing {
      get { return minimumColumnSpacing; }
      set {
        WillChangeValue ("MinimumColumnSpacing");
        minimumColumnSpacing = value;
        DidChangeValue ("MinimumColumnSpacing");

        InvalidateLayout ();
      }
    }

    [Export("MinimumInterItemSpacing")]
    public nfloat MinimumInterItemSpacing {
      get { return minimumInterItemSpacing; }
      set {
        WillChangeValue ("MinimumInterItemSpacing");
        minimumInterItemSpacing = value;
        DidChangeValue ("MinimumInterItemSpacing");

        InvalidateLayout ();
      }
    }

    [Export("HeaderHeight")]
    public nfloat HeaderHeight {
      get { return headerHeight; }
      set {
        WillChangeValue ("HeaderHeight");
        headerHeight = value;
        DidChangeValue ("HeaderHeight");

        InvalidateLayout ();
      }
    }

    [Export("FooterHeight")]
    public nfloat FooterHeight {
      get { return footerHeight; }
      set {
        WillChangeValue ("FooterHeight");
        footerHeight = value;
        DidChangeValue ("FooterHeight");

        InvalidateLayout ();
      }
    }

    [Export("SectionInset")]
    public UIEdgeInsets SectionInset {
      get { return sectionInset; }
      set {
        WillChangeValue ("SectionInset");
        sectionInset = value;
        DidChangeValue ("SectionInset");

        InvalidateLayout ();
      }
    }

    [Export("ItemRenderDirection")]
    public WaterfallCollectionRenderDirection ItemRenderDirection {
      get { return itemRenderDirection; }
      set {
        WillChangeValue ("ItemRenderDirection");
        itemRenderDirection = value;
        DidChangeValue ("ItemRenderDirection");

        InvalidateLayout ();
      }
    }
    #endregion

    #region Constructors
    public WaterfallCollectionLayout ()
    {
    }

    public WaterfallCollectionLayout(NSCoder coder) : base(coder) {

    }
    #endregion

    #region Public Methods
    public nfloat ItemWidthInSectionAtIndex(int section) {

      var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
      return (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);
    }
    #endregion

    #region Override Methods
    public override void PrepareLayout ()
    {
      base.PrepareLayout ();

      // Get the number of sections
      var numberofSections = CollectionView.NumberOfSections();
      if (numberofSections == 0)
        return;

      // Reset collections
      headersAttributes.Clear ();
      footersAttributes.Clear ();
      unionRects.Clear ();
      columnHeights.Clear ();
      allItemAttributes.Clear ();
      sectionItemAttributes.Clear ();

      // Initialize column heights
      for (int n = 0; n < ColumnCount; n++) {
        columnHeights.Add ((nfloat)0);
      }

      // Process all sections
      nfloat top = 0.0f;
      var attributes = new UICollectionViewLayoutAttributes ();
      var columnIndex = 0;
      for (nint section = 0; section < numberofSections; ++section) {
        // Calculate section specific metrics
        var minimumInterItemSpacing = (MinimumInterItemSpacingForSection == null) ? MinimumColumnSpacing :
          MinimumInterItemSpacingForSection (CollectionView, this, section);

        // Calculate widths
        var width = CollectionView.Bounds.Width - SectionInset.Left - SectionInset.Right;
        var itemWidth = (nfloat)Math.Floor ((width - ((ColumnCount - 1) * MinimumColumnSpacing)) / ColumnCount);

        // Calculate section header
        var heightHeader = (HeightForHeader == null) ? HeaderHeight :
          HeightForHeader (CollectionView, this, section);

        if (heightHeader > 0) {
          attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Header, NSIndexPath.FromRowSection (0, section));
          attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, heightHeader);
          headersAttributes.Add (section, attributes);
          allItemAttributes.Add (attributes);

          top = attributes.Frame.GetMaxY ();
        }

        top += SectionInset.Top;
        for (int n = 0; n < ColumnCount; n++) {
          columnHeights [n] = top;
        }

        // Calculate Section Items
        var itemCount = CollectionView.NumberOfItemsInSection(section);
        List<UICollectionViewLayoutAttributes> itemAttributes = new List<UICollectionViewLayoutAttributes> ();

        for (nint n = 0; n < itemCount; n++) {
          var indexPath = NSIndexPath.FromRowSection (n, section);
          columnIndex = NextColumnIndexForItem (n);
          var xOffset = SectionInset.Left + (itemWidth + MinimumColumnSpacing) * (nfloat)columnIndex;
          var yOffset = columnHeights [columnIndex];
          var itemSize = (SizeForItem == null) ? new CGSize (0, 0) : SizeForItem (CollectionView, this, indexPath);
          nfloat itemHeight = 0.0f;

          if (itemSize.Height > 0.0f && itemSize.Width > 0.0f) {
            itemHeight = (nfloat)Math.Floor (itemSize.Height * itemWidth / itemSize.Width);
          }

          attributes = UICollectionViewLayoutAttributes.CreateForCell (indexPath);
          attributes.Frame = new CGRect (xOffset, yOffset, itemWidth, itemHeight);
          itemAttributes.Add (attributes);
          allItemAttributes.Add (attributes);
          columnHeights [columnIndex] = attributes.Frame.GetMaxY () + MinimumInterItemSpacing;
        }
        sectionItemAttributes.Add (itemAttributes);

        // Calculate Section Footer
        nfloat footerHeight = 0.0f;
        columnIndex = LongestColumnIndex();
        top = columnHeights [columnIndex] - MinimumInterItemSpacing + SectionInset.Bottom;
        footerHeight = (HeightForFooter == null) ? FooterHeight : HeightForFooter(CollectionView, this, section);

        if (footerHeight > 0) {
          attributes = UICollectionViewLayoutAttributes.CreateForSupplementaryView (UICollectionElementKindSection.Footer, NSIndexPath.FromRowSection (0, section));
          attributes.Frame = new CGRect (0, top, CollectionView.Bounds.Width, footerHeight);
          footersAttributes.Add (section, attributes);
          allItemAttributes.Add (attributes);
          top = attributes.Frame.GetMaxY ();
        }

        for (int n = 0; n < ColumnCount; n++) {
          columnHeights [n] = top;
        }
      }

      var i =0;
      var attrs = allItemAttributes.Count;
      while(i < attrs) {
        var rect1 = allItemAttributes [i].Frame;
        i = (int)Math.Min (i + unionSize, attrs) - 1;
        var rect2 = allItemAttributes [i].Frame;
        unionRects.Add (CGRect.Union (rect1, rect2));
        i++;
      }

    }

    public override CGSize CollectionViewContentSize {
      get {
        if (CollectionView.NumberOfSections () == 0) {
          return new CGSize (0, 0);
        }

        var contentSize = CollectionView.Bounds.Size;
        contentSize.Height = columnHeights [0];
        return contentSize;
      }
    }

    public override UICollectionViewLayoutAttributes LayoutAttributesForItem (NSIndexPath indexPath)
    {
      if (indexPath.Section >= sectionItemAttributes.Count) {
        return null;
      }

      if (indexPath.Item >= sectionItemAttributes [indexPath.Section].Count) {
        return null;
      }

      var list = sectionItemAttributes [indexPath.Section];
      return list [(int)indexPath.Item];
    }

    public override UICollectionViewLayoutAttributes LayoutAttributesForSupplementaryView (NSString kind, NSIndexPath indexPath)
    {
      var attributes = new UICollectionViewLayoutAttributes ();

      switch (kind) {
      case "header":
        attributes = headersAttributes [indexPath.Section];
        break;
      case "footer":
        attributes = footersAttributes [indexPath.Section];
        break;
      }

      return attributes;
    }

    public override UICollectionViewLayoutAttributes[] LayoutAttributesForElementsInRect (CGRect rect)
    {
      var begin = 0;
      var end = unionRects.Count;
      List<UICollectionViewLayoutAttributes> attrs = new List<UICollectionViewLayoutAttributes> ();

      for (int i = 0; i < end; i++) {
        if (rect.IntersectsWith(unionRects[i])) {
          begin = i * (int)unionSize;
        }
      }

      for (int i = end - 1; i >= 0; i--) {
        if (rect.IntersectsWith (unionRects [i])) {
          end = (int)Math.Min ((i + 1) * (int)unionSize, allItemAttributes.Count);
          break;
        }
      }

      for (int i = begin; i < end; i++) {
        var attr = allItemAttributes [i];
        if (rect.IntersectsWith (attr.Frame)) {
          attrs.Add (attr);
        }
      }

      return attrs.ToArray();
    }

    public override bool ShouldInvalidateLayoutForBoundsChange (CGRect newBounds)
    {
      var oldBounds = CollectionView.Bounds;
      return (newBounds.Width != oldBounds.Width);
    }
    #endregion

    #region Private Methods
    private int ShortestColumnIndex() {
      var index = 0;
      var shortestHeight = nfloat.MaxValue;
      var n = 0;

      // Scan each column for the shortest height
      foreach (nfloat height in columnHeights) {
        if (height < shortestHeight) {
          shortestHeight = height;
          index = n;
        }
        ++n;
      }

      return index;
    }

    private int LongestColumnIndex() {
      var index = 0;
      var longestHeight = nfloat.MinValue;
      var n = 0;

      // Scan each column for the shortest height
      foreach (nfloat height in columnHeights) {
        if (height > longestHeight) {
          longestHeight = height;
          index = n;
        }
        ++n;
      }

      return index;
    }

    private int NextColumnIndexForItem(nint item) {
      var index = 0;

      switch (ItemRenderDirection) {
      case WaterfallCollectionRenderDirection.ShortestFirst:
        index = ShortestColumnIndex ();
        break;
      case WaterfallCollectionRenderDirection.LeftToRight:
        index = ColumnCount;
        break;
      case WaterfallCollectionRenderDirection.RightToLeft:
        index = (ColumnCount - 1) - ((int)item / ColumnCount);
        break;
      }

      return index;
    }
    #endregion

    #region Events
    public delegate CGSize WaterfallCollectionSizeDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, NSIndexPath indexPath);
    public delegate nfloat WaterfallCollectionFloatDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);
    public delegate UIEdgeInsets WaterfallCollectionEdgeInsetsDelegate(UICollectionView collectionView, WaterfallCollectionLayout layout, nint section);

    public event WaterfallCollectionSizeDelegate SizeForItem;
    public event WaterfallCollectionFloatDelegate HeightForHeader;
    public event WaterfallCollectionFloatDelegate HeightForFooter;
    public event WaterfallCollectionEdgeInsetsDelegate InsetForSection;
    public event WaterfallCollectionFloatDelegate MinimumInterItemSpacingForSection;
    #endregion
  }
}
```

Cette classe peut être utilisée pour fournir une mise en page personnalisée de type cascade à la vue de collection.
Le code utilise le codage de clé-valeur ( `WillChangeValue` via `DidChangeValue` les méthodes et) pour fournir la liaison de données pour nos propriétés calculées dans cette classe.

Modifiez ensuite le `WaterfallCollectionSource` et apportez les modifications et ajouts suivants :

```csharp
private Random rnd = new Random();
...

public List<nfloat> Heights { get; set; } = new List<nfloat> ();
...

public WaterfallCollectionSource (WaterfallCollectionView collectionView)
{
  // Initialize
  CollectionView = collectionView;

  // Init numbers collection
  for (int n = 0; n < 100; ++n) {
    Numbers.Add (n);
    Heights.Add (rnd.Next (0, 100) + 40.0f);
  }
}
```

Cela permet de créer une hauteur aléatoire pour chacun des éléments qui seront affichés dans la liste.

Modifiez ensuite la `WaterfallCollectionView` classe et ajoutez la propriété d’assistance suivante :

```csharp
public WaterfallCollectionSource Source {
  get { return (WaterfallCollectionSource)DataSource; }
}
```

Cela facilitera l’accès à notre source de données (et à la hauteur des éléments) à partir de la disposition personnalisée.

Enfin, modifiez le contrôleur d’affichage et ajoutez le code suivant :

```csharp
public override void AwakeFromNib ()
{
  base.AwakeFromNib ();

  var waterfallLayout = new WaterfallCollectionLayout ();

  // Wireup events
  waterfallLayout.SizeForItem += (collectionView, layout, indexPath) => {
    var collection = collectionView as WaterfallCollectionView;
    return new CGSize((View.Bounds.Width-40)/3,collection.Source.Heights[(int)indexPath.Item]);
  };

  // Attach the custom layout to the collection
  CollectionView.SetCollectionViewLayout(waterfallLayout, false);
}
```

Cela crée une instance de notre disposition personnalisée, définit l’événement pour qu’il fournisse la taille de chaque élément et attache la nouvelle disposition à notre vue de collection.

Si nous exécutons à nouveau l’application Xamarin. iOS, la vue de la collection ressemble à ce qui suit :

[![](uicollectionview-images/custom01.png "La vue de collection ressemble maintenant à ceci")](uicollectionview-images/custom01.png#lightbox)

Nous pouvons toujours faire glisser pour réorganiser les éléments comme avant, mais les éléments vont maintenant changer la taille en fonction de leur nouvel emplacement lorsqu’ils sont supprimés.

## <a name="collection-view-changes"></a>Modifications de la vue de collection

Dans les sections suivantes, nous examinerons en détail les modifications apportées à chaque classe dans la vue de collection par iOS 9.

### <a name="uicollectionview"></a>UICollectionView

Les modifications ou ajouts suivants ont été apportés à la `UICollectionView` classe pour iOS 9 :

- `BeginInteractiveMovementForItem`: Marque le début d’une opération glisser.
- `CancelInteractiveMovement`: Informe la vue de collection que l’utilisateur a annulé une opération glisser.
- `EndInteractiveMovement`: Informe la vue de collection que l’utilisateur a terminé une opération glisser.
- `GetIndexPathsForVisibleSupplementaryElements`: Retourne le `indexPath` d’un en-tête ou d’un pied de page dans une section de vue de collection.
- `GetSupplementaryView`: Retourne l’en-tête ou le pied de page donné.
- `GetVisibleSupplementaryViews`: Retourne une liste de tous les en-têtes et pieds de page visibles.
- `UpdateInteractiveMovementTargetPosition`: Indique à la vue de collection que l’utilisateur a déplacé, ou se déplace, un élément pendant une opération glisser.

### <a name="uicollectionviewcontroller"></a>UICollectionViewController

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewController` classe dans iOS 9 :

- `InstallsStandardGestureForInteractiveMovement`: Si `true` le nouveau module de reconnaissance de mouvement qui prend automatiquement en charge la fonction glisser-déplacer est utilisé.
- `CanMoveItem`: Informe la vue de collection si un élément donné peut faire l’objet d’une réorganisation.
- `GetTargetContentOffset`: Utilisé pour obtenir le décalage d’un élément de vue de collection donné.
- `GetTargetIndexPathForMove`: Obtient le `indexPath` d’un élément donné pour une opération glisser.
- `MoveItem`: Déplace l’ordre d’un élément donné dans la liste.

### <a name="uicollectionviewdatasource"></a>UICollectionViewDataSource

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewDataSource` classe dans iOS 9 :

- `CanMoveItem`: Informe la vue de collection si un élément donné peut faire l’objet d’une réorganisation.
- `MoveItem`: Déplace l’ordre d’un élément donné dans la liste.

### <a name="uicollectionviewdelegate"></a>UICollectionViewDelegate

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewDelegate` classe dans iOS 9 :

- `GetTargetContentOffset`: Utilisé pour obtenir le décalage d’un élément de vue de collection donné.
- `GetTargetIndexPathForMove`: Obtient le `indexPath` d’un élément donné pour une opération glisser.

### <a name="uicollectionviewflowlayout"></a>UICollectionViewFlowLayout

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewFlowLayout` classe dans iOS 9 :

- `SectionFootersPinToVisibleBounds`: Colle les pieds de page des sections sur les limites visibles de la vue de collection.
- `SectionHeadersPinToVisibleBounds`: Colle les en-têtes de section aux limites visibles de la vue de collection.

### <a name="uicollectionviewlayout"></a>UICollectionViewLayout

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewLayout` classe dans iOS 9 :

- `GetInvalidationContextForEndingInteractiveMovementOfItems`: Retourne le contexte d’invalidation à la fin d’une opération glisser lorsque l’utilisateur termine le glissement ou l’annule.
- `GetInvalidationContextForInteractivelyMovingItems`: Retourne le contexte d’invalidation au début d’une opération glisser.
- `GetLayoutAttributesForInteractivelyMovingItem`: Obtient les attributs de disposition pour un élément donné lors du glissement d’un élément.
- `GetTargetIndexPathForInteractivelyMovingItem`: Retourne le `indexPath` de l’élément qui se trouve à l’instant donné lors du glissement d’un élément.

### <a name="uicollectionviewlayoutattributes"></a>UICollectionViewLayoutAttributes

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewLayoutAttributes` classe dans iOS 9 :

- `CollisionBoundingPath`: Retourne le chemin d’accès de collision de deux éléments pendant une opération glisser.
- `CollisionBoundsType`: Retourne le type de collision (en tant `UIDynamicItemCollisionBoundsType`que) qui s’est produit pendant une opération glisser.

### <a name="uicollectionviewlayoutinvalidationcontext"></a>UICollectionViewLayoutInvalidationContext

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewLayoutInvalidationContext` classe dans iOS 9 :

- `InteractiveMovementTarget`: Retourne l’élément cible d’une opération glisser.
- `PreviousIndexPathsForInteractivelyMovingItems`: Retourne le `indexPaths` d’autres éléments impliqués dans une opération de glisser-déplacer pour réorganiser.
- `TargetIndexPathsForInteractivelyMovingItems`: Retourne le `indexPaths` des éléments qui seront réorganisés à la suite d’une opération de glisser-déplacer.

### <a name="uicollectionviewsource"></a>UICollectionViewSource

Les modifications ou ajouts suivants ont été apportés à la `UICollectionViewSource` classe dans iOS 9 :

- `CanMoveItem`: Informe la vue de collection si un élément donné peut faire l’objet d’une réorganisation.
- `GetTargetContentOffset`: Retourne les décalages des éléments qui seront déplacés via une opération de glisser-déplacer.
- `GetTargetIndexPathForMove`: Retourne le `indexPath` d’un élément qui sera déplacé pendant une opération de glisser-déplacer.
- `MoveItem`: Déplace l’ordre d’un élément donné dans la liste.

## <a name="summary"></a>Récapitulatif

Cet article a abordé les modifications apportées aux affichages de collection dans iOS 9 et a décrit comment les implémenter dans Xamarin. iOS.
Il a abordé l’implémentation d’une simple opération glisser-déplacer dans une vue de collection. utilisation d’un module de reconnaissance de mouvement personnalisé avec la fonction de glisser-déplacer ; et comment la fonction de glisser-déplacer affecte une disposition de vue de collection personnalisée.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Exemple d’affichage de collection](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-collectionview)
- [SimpleCollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplecollectionview)
- [Événements, protocoles et délégués](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Utilisation des tableaux et des cellules](~/ios/user-interface/controls/tables/index.md)

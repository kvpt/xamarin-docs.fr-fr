---
title: Performances des applications Xamarin.iOS
description: Ce document décrit les techniques permettant d’améliorer les performances et l’utilisation de la mémoire dans les applications Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/29/2016
ms.openlocfilehash: bfa8c2cdcdcd6305618c0cd8e9cb69bde59b4f0b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73030202"
---
# <a name="xamarinios-performance"></a>Performances des applications Xamarin.iOS

Le mauvais niveau de performance d’une application se présente de plusieurs façons. L’application semble ne pas répondre, le défilement de l’affichage est ralenti et la durée de vie de la batterie de l’appareil réduite. Toutefois, l’optimisation des performances implique davantage de choses que l’implémentation d’un code efficace. L’expérience utilisateur liée au niveau de performance de l’application doit également être prise en compte. Par exemple, pour contribuer à améliorer l’expérience utilisateur, vous devez vérifier que les opérations s’exécutent sans empêcher l’utilisateur d’effectuer d’autres activités.

Ce document décrit les techniques permettant d’améliorer les performances et l’utilisation de la mémoire dans les applications Xamarin.iOS.

> [!NOTE]
>  Avant de lire cet article, vous devez lire [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md), qui décrit des techniques non spécifiques aux plateformes pour améliorer l’utilisation de la mémoire et les performances des applications générées à l’aide de la plateforme Xamarin.

## <a name="avoid-strong-circular-references"></a>Éviter les références circulaires fortes

Dans certaines situations, vous pouvez créer des cycles de références fortes qui peuvent empêcher le récupérateur de mémoire de récupérer la mémoire des objets. Par exemple, considérez [`NSObject`](xref:Foundation.NSObject)le cas où une sous-classe [`UIView`](xref:UIKit.UIView)dérivée, comme `NSObject`une classe qui hérite de , est ajoutée à un conteneur dérivé et est fortement référencée à partir d’Objectif-C, comme le montre l’exemple de code suivant :

```csharp
class Container : UIView
{
    public void Poke ()
    {
    // Call this method to poke this object
    }
}

class MyView : UIView
{
    Container parent;
    public MyView (Container parent)
    {
        this.parent = parent;
    }

    void PokeParent ()
    {
        parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Quand ce code crée l’instance de `Container`, l’objet C# a une référence forte à un objet Objective-C. De même, l’instance de `MyView` a également une référence forte à un objet Objective-C.

De plus, l’appel à `container.AddSubview` augmente le nombre de références sur l’instance non managée de `MyView`. Quand cela se produit, le runtime Xamarin.iOS crée une instance de `GCHandle` pour garder l’objet `MyView` actif dans le code managé, car il n’existe aucune garantie que les objets managés vont continuer à le référencer. Du point de vue `MyView` du code géré, [`AddSubview`](xref:UIKit.UIView.AddSubview(UIKit.UIView)) l’objet serait `GCHandle`récupéré après l’appel s’il n’était pas pour le .

L’objet `MyView` non managé a un `GCHandle` qui pointe vers l’objet managé. Cela s’appelle un *lien fort*. L’objet managé contient une référence à l’instance de `Container`. À son tour, l’instance de `Container` a une référence managée à l’objet `MyView`.

Dans les cas où un objet contenu conserve un lien vers son conteneur, plusieurs options sont disponibles pour traiter la référence circulaire :

- Rompez manuellement le cycle en affectant au lien vers le conteneur la valeur `null`.
- Supprimez manuellement l’objet contenu du conteneur.
- Appelez `Dispose` sur les objets.
- Évitez la référence circulaire en gardant une référence faible au conteneur. Pour plus d’informations sur les références faibles.

### <a name="using-weakreferences"></a>Utilisation de WeakReferences

Pour empêcher un cycle, vous pouvez, entre autres, utiliser une référence faible de l’enfant au parent. Par exemple, le code ci-dessus peut s’écrire de la façon suivante :

```csharp
class Container : UIView
{
    public void Poke ()
    {
        // Call this method to poke this object
    }
}

class MyView : UIView
{
    WeakReference<Container> weakParent;
    public MyView (Container parent)
    {
        this.weakParent = new WeakReference<Container> (parent);
    }

    void PokeParent ()
    {
        if (weakParent.TryGetTarget (out var parent))
            parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Ici, l’objet contenu ne maintient pas le parent actif. Toutefois, le parent maintient l’enfant actif via l’appel effectué à `container.AddSubView`.

Cela se produit également dans les API iOS qui utilisent le modèle de délégué ou de source de données, où une classe de pairs contient la mise en œuvre; par exemple, lors de la mise en place de la[`Delegate`](xref:UIKit.UITableView.Delegate*)
propriété ou le[`DataSource`](xref:UIKit.UITableView.DataSource*)
dans [`UITableView`](xref:UIKit.UITableView) la classe.

Dans le cas des classes qui sont créées uniquement pour le [`IUITableViewDataSource`](xref:UIKit.IUITableViewDataSource)plaisir de mettre en œuvre un protocole, par exemple le , ce que vous `DataSource` pouvez `this`faire est au lieu de créer une sous-classe, vous pouvez simplement implémenter l’interface dans la classe et remplacer la méthode, et attribuer la propriété à .

#### <a name="weak-attribute"></a>Attribut faible

[Xamarin.iOS 11.10](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_11/xamarin.ios_11.10.md#WeakAttribute) a introduit l’attribut `[Weak]`. Comme `WeakReference <T>`, `[Weak]` peut être utilisé pour rompre les [références circulaires fortes](https://docs.microsoft.com/xamarin/ios/deploy-test/performance#avoid-strong-circular-references), mais avec encore moins de code.

Prenez le code suivant, qui utilise `WeakReference <T>` :

```csharp
public class MyFooDelegate : FooDelegate {
    WeakReference<MyViewController> controller;
    public MyFooDelegate (MyViewController ctrl) => controller = new WeakReference<MyViewController> (ctrl);
    public void CallDoSomething ()
    {
        MyViewController ctrl;
        if (controller.TryGetTarget (out ctrl)) {
            ctrl.DoSomething ();
        }
    }
}
```

Le code équivalent qui utilise `[Weak]` est beaucoup plus concis :

```csharp
public class MyFooDelegate : FooDelegate {
    [Weak] MyViewController controller;
    public MyFooDelegate (MyViewController ctrl) => controller = ctrl;
    public void CallDoSomething () => controller.DoSomething ();
}
```

Voici un autre exemple d’utilisation de `[Weak]` dans le contexte du modèle de [délégation](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html) :

```csharp
public class MyViewController : UIViewController
{
    WKWebView webView;

    protected MyViewController (IntPtr handle) : base (handle) { }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        webView = new WKWebView (View.Bounds, new WKWebViewConfiguration ());
        webView.UIDelegate = new UIDelegate (this);
        View.AddSubview (webView);
    }
}

public class UIDelegate : WKUIDelegate
{
    [Weak] MyViewController controller;

    public UIDelegate (MyViewController ctrl) => controller = ctrl;

    public override void RunJavaScriptAlertPanel (WKWebView webView, string message, WKFrameInfo frame, Action completionHandler)
    {
        var msg = $"Hello from: {controller.Title}";
        var alertController = UIAlertController.Create (null, msg, UIAlertControllerStyle.Alert);
        alertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
        controller.PresentViewController (alertController, true, null);
        completionHandler ();
    }
}
```

### <a name="disposing-of-objects-with-strong-references"></a>Suppression d’objets avec des références fortes

Si une référence forte existe et qu’il est difficile de supprimer la dépendance, utilisez une méthode `Dispose` pour effacer le pointeur parent.

Pour les conteneurs, remplacez la méthode `Dispose` afin de supprimer les objets contenus, comme indiqué dans l’exemple de code suivant :

```csharp
class MyContainer : UIView
{
    public override void Dispose ()
    {
        // Brute force, remove everything
        foreach (var view in Subviews)
        {
              view.RemoveFromSuperview ();
        }
        base.Dispose ();
    }
}
```

Pour un objet enfant qui conserve une référence forte à son parent, effacez la référence au parent dans l’implémentation de `Dispose` :

```csharp
class MyChild : UIView
{
    MyContainer container;
    public MyChild (MyContainer container)
    {
        this.container = container;
    }
    public override void Dispose ()
    {
        container = null;
    }
}
```

Pour plus d’informations sur la libération de références fortes, consultez [Libérer des ressources IDisposable](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable).
Il existe également des informations intéressantes dans ce billet de blog : [Xamarin.iOS, the garbage collector and me](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me).

### <a name="more-information"></a>Informations complémentaires

Pour plus d’informations, consultez [Rules to Avoid Retain Cycles](https://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html) sur Cocoa With Love, [Is this a bug in MonoTouch GC](https://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc) sur StackOverflow, et [Why can’t MonoTouch GC kill managed objects with refcount > 1?](https://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1) sur StackOverflow.

## <a name="optimize-table-views"></a>Optimiser les vues de tableaux

Les utilisateurs s’attendent à [`UITableView`](xref:UIKit.UITableView) un défilement en douceur et des temps de chargement rapides pour les instances. Toutefois, le niveau de performance du défilement peut se dégrader quand les cellules contiennent des hiérarchies d’affichages profondément imbriquées, ou quand elles contiennent des dispositions complexes. Toutefois, différentes techniques permettent d’éviter la dégradation des performances de `UITableView` :

- Réutilisez les cellules. Pour plus d’informations, consultez [Réutiliser les cellules](#reuse-cells).
- Réduisez le nombre de sous-affichages.
- Mettez en cache le contenu de cellule récupéré à partir d’un service web.
- Mettez en cache la hauteur des lignes, si elles ne sont pas identiques.
- Rendez la cellule et les autres affichages opaques.
- Évitez la mise à l’échelle et les dégradés d’images.

Collectivement, ces techniques peuvent [`UITableView`](xref:UIKit.UITableView) aider à garder les instances défiler en douceur.

### <a name="reuse-cells"></a>Réutiliser les cellules

Lors de l’affichage [`UITableView`](xref:UIKit.UITableView)des centaines de lignes dans un [`UITableViewCell`](xref:UIKit.UITableViewCell) , il serait un gaspillage de mémoire pour créer des centaines d’objets quand seulement un petit nombre d’entre eux sont affichés à l’écran à la fois. À la place, seules les cellules visibles à l’écran peuvent être chargées en mémoire, le **contenu** étant chargé dans les cellules réutilisées. Cela évite l’instanciation de centaines d’objets supplémentaires et permet d’économiser à la fois du temps et de la mémoire.

Ainsi, quand une cellule disparaît de l’écran, son affichage peut être placé en file d’attente de réutilisation, comme indiqué dans l’exemple de code suivant :

```csharp
class MyTableSource : UITableViewSource
{
    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        // iOS will create a cell automatically if one isn't available in the reuse pool
        var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

        // Perform required cell actions
        return cell;
    }
}
```

Pendant que l’utilisateur [`UITableView`](xref:UIKit.UITableView) fait `GetCell` défiler, les appels remplacent pour demander de nouvelles vues à afficher. Ce remplacement appelle [`DequeueReusableCell`](xref:UIKit.UITableView.DequeueReusableCell(Foundation.NSString)) alors la méthode et si une cellule est disponible pour la réutilisation, elle sera retournée.

Pour plus d’informations, consultez [Réutilisation des cellules](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) dans [Remplissage d’un tableau avec des données](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

## <a name="use-opaque-views"></a>Utiliser les vues opaques

Assurez-vous que toutes les vues [`Opaque`](xref:UIKit.UIView.Opaque) qui n’ont pas de transparence définie ont leur propriété définie. Cela permet de garantir le rendu optimal des affichages par le système de dessin. Ceci est particulièrement important lorsqu’une vue est intégrée dans un [`UIScrollView`](xref:UIKit.UIScrollView), ou fait partie d’une animation complexe. Sinon, le système de dessin effectue une composition des affichages avec d’autres contenus, ce qui peut impacter le niveau de performance de manière importante.

## <a name="avoid-fat-xibs"></a>Éviter les XIB volumineux

Bien que les XIB aient été largement remplacés par les storyboards, il existe certaines situations où vous pouvez encore utiliser des XIB. Quand un XIB est chargé en mémoire, tout son contenu est chargé en mémoire, notamment les images. Si le XIB contient un affichage qui n’est pas immédiatement utilisé, la mémoire est gaspillée. Ainsi, quand vous utilisez un XIB, vérifiez qu’il n’existe qu’un seul XIB par contrôleur d’affichage et, si possible, séparez la hiérarchie d’affichages du contrôleur d’affichage dans des XIB distincts.

## <a name="optimize-image-resources"></a>Optimiser les ressources d’images

Les images font partie des ressources les plus coûteuses qui sont utilisées par les applications et sont souvent capturées en haute résolution. Par conséquent, lors de l’affichage d’une image à partir du faisceau de l’application dans un [`UIImageView`](xref:UIKit.UIImageView), assurez-vous que l’image et `UIImageView` sont de taille identique. L’échelle des images au moment de l’exécution peut être une opération coûteuse, en particulier si le `UIImageView` est intégré dans un [`UIScrollView`](xref:UIKit.UIScrollView).

Pour plus d’informations, consultez [Optimiser les ressources d’images](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) dans le guide [Performances entre plateformes](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="test-on-devices"></a>Tester sur des appareils

Commencez à déployer et à tester votre application sur un appareil physique le plus tôt possible. Les simulateurs ne reproduisent pas parfaitement les comportements et les limitations des appareils. Il est donc important d’effectuer des tests sur des appareils réels, dès que possible.

En particulier, le simulateur ne peut pas simuler les restrictions de mémoire ou les limitations de l’UC d’un appareil physique.

## <a name="synchronize-animations-with-the-display-refresh"></a>Synchroniser les animations avec l’actualisation de l’affichage

Les jeux ont tendance à avoir des boucles étroites pour l’exécution de la logique du jeu et l’actualisation de l’écran. Les fréquences d’images classiques vont de trente à soixante images par seconde. Certains développeurs estiment qu’ils doivent mettre à jour l’écran autant de fois que possible par seconde, en combinant leur simulation de jeu avec des mises à jour de l’écran. Ils sont donc parfois tentés d’aller au-delà de soixante images par seconde.

Toutefois, le serveur d’affichage effectue des mises à jour de l’écran soixante fois par seconde, ce qui correspond à la limite maximale. Ainsi, toute tentative d’actualisation de l’écran qui dépasse cette limite peut entraîner des interruptions et des micro-saccades. Il est préférable de structurer le code pour que les actualisations de l’écran soient synchronisées avec l’actualisation de l’affichage. Ceci peut être réalisé [`CoreAnimation.CADisplayLink`](xref:CoreAnimation.CADisplayLink) en utilisant la classe, qui est une minuterie adaptée à la visualisation et les jeux qui s’exécute à soixante images par seconde.

## <a name="avoid-core-animation-transparency"></a>Évitez la transparence Core Animation

Le fait d’éviter la transparence Core Animation permet d’améliorer le niveau de performance de la composition bitmap. En règle générale, évitez les calques transparents et les bordures floues, si possible.

## <a name="avoid-code-generation"></a>Éviter la génération de code

Vous devez éviter de générer du code de manière dynamique avec `System.Reflection.Emit` ou à l’aide du *Dynamic Language Runtime*, car le noyau iOS empêche l’exécution du code dynamique.

## <a name="summary"></a>Récapitulatif

Cet article a décrit et expliqué les techniques permettant d’accroître le niveau de performance des applications générées avec Xamarin.iOS. Utilisées conjointement, ces techniques peuvent considérablement réduire la charge de travail d’une UC, ainsi que la quantité de mémoire consommée par une application.

## <a name="related-links"></a>Liens connexes

- [Niveau de performance multiplateforme](~/cross-platform/deploy-test/memory-perf-best-practices.md)

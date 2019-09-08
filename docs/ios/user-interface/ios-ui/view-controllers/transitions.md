---
title: Afficher les transitions du contrôleur dans Xamarin. iOS
description: Ce document explique comment personnaliser des transitions animées entre les contrôleurs d’affichage dans les applications Xamarin. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: bbba1cbb907e3744677ff6bbe39b97fb62616e33
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768165"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Afficher les transitions du contrôleur dans Xamarin. iOS

UIKit ajoute la prise en charge de la personnalisation de la transition animée qui se produit lors de la présentation des contrôleurs d’affichage. Cette prise en charge est incluse avec les contrôleurs intégrés, ainsi que pour tous les contrôleurs personnalisés `UIViewController`qui héritent directement de. En outre, `UICollectionViewController` tire parti de la personnalisation de transition de contrôleur pour tirer parti des transitions animées dans les dispositions de vue de collection.

## <a name="custom-transitions"></a>Transitions personnalisées

La transition animée entre les contrôleurs d’affichage dans iOS 7 est entièrement personnalisable. `UIViewController`inclut désormais une `TransitioningDelegate` propriété qui fournit une classe d’animation personnalisée au système lorsqu’une transition se produit.

Pour utiliser une transition personnalisée avec `PresentViewController`:

1. Affectezlavaleuràsurlecontrôleuràprésenter.`ModalPresentationStyle` `UIModalPresentationStyle.Custom`
2. Implémentez `UIViewControllerTransitioningDelegate` pour créer une classe d’animation, qui est une `UIViewControllerAnimatedTransitioning` instance de.
3. Affectez `TransitioningDelegate` à la propriété une instance `UIViewControllerTransitioningDelegate` de, également sur le contrôleur à présenter.
4. Présentez le contrôleur d’affichage.

Par exemple, le code suivant présente un contrôleur d’affichage de `ControllerTwo` type- `UIViewController` une sous-classe :

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

L’exécution de l’application et le fait de cliquer sur le bouton entraîne l’animation par défaut de la vue du deuxième contrôleur à partir du bas, comme indiqué ci-dessous :

 ![](transitions-images/no-custom-transition.png "L’exécution de l’application et le fait de cliquer sur le bouton entraîne l’animation par défaut de la deuxième vue contrôleurs à partir du bas")

Toutefois, la `ModalPresentationStyle` définition de `TransitioningDelegate` et entraîne une animation personnalisée pour la transition :

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

Est chargé de créer une instance de la `UIViewControllerAnimatedTransitioning` sous-classe, appelée `CustomAnimator` dans l’exemple ci-dessous : `TransitioningDelegate`

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Lorsque la transition a lieu, le système crée une instance de `IUIViewControllerContextTransitioning`, qu’elle a transmise aux méthodes de l’animateur. `IUIViewControllerContextTransitioning`contient le `ContainerView` où l’animation se produit, ainsi que le contrôleur d’affichage qui lance la transition et le contrôleur d’affichage vers lequel effectuer la transition.

La `UIViewControllerAnimatedTransitioning` classe gère l’animation réelle. Deux méthodes doivent être implémentées :

1. `TransitionDuration`: retourne la durée de l’animation, en secondes.
1. `AnimateTransition`: effectue l’animation réelle.

Par exemple, la classe suivante implémente `UIViewControllerAnimatedTransitioning` pour animer le frame de la vue du contrôleur :

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
    {
        var inView = transitionContext.ContainerView;
        var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
        var toView = toVC.View;

        inView.AddSubview (toView);

        var frame = toView.Frame;
        toView.Frame = CGRect.Empty;

        UIView.Animate (TransitionDuration (transitionContext), () => {
            toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
        }, () => {
            transitionContext.CompleteTransition (true);
        });
    }
}
```

Désormais, lorsque le bouton est frappé, l’animation implémentée dans `UIViewControllerAnimatedTransitioning` la classe est utilisée :

 ![](transitions-images/custom-transition.png "Exemple de zoom en cours d’exécution")

## <a name="collection-view-transitions"></a>Transitions de la vue de collection

Les vues de collection offrent une prise en charge intégrée pour la création de transitions animées :

- **Contrôleurs de navigation** : la transition animée `UICollectionViewController` entre deux instances peut éventuellement être gérée automatiquement `UINavigationController` lorsqu’un les gère.
- **Transition layout** : une nouvelle `UICollectionViewTransitionLayout` classe permet la transition interactive entre les dispositions.

### <a name="navigation-controller-transitions"></a>Transitions du contrôleur de navigation

Lorsqu’il est utilisé dans un contrôleur de `UICollectionViewController` navigation, un comprend la prise en charge des transitions animées entre les contrôleurs. Cette prise en charge est intégrée et ne nécessite que quelques étapes simples pour implémenter :

1. `UseLayoutToLayoutNavigationTransitions` Affectez `false` la`UICollectionViewController` valeur à sur.
1. Ajoutez une instance de `UICollectionViewController` à la racine de la pile du contrôleur de navigation.
1. Créez une seconde `UICollectionViewController` et affectez `UseLayoutToLayoutNavigtionTransitions` à `true` sa propriété la valeur.
1. Poussez le deuxième `UICollectionViewController` sur la pile du contrôleur de navigation.

Le code suivant ajoute une `UICollectionViewController` sous-classe `ImagesCollectionViewController` nommée à la racine de la pile d’un contrôleur de navigation `UseLayoutToLayoutNavigationTransitions` , avec la `false`propriété définie sur :

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false
        };

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();

    return true;
}
```

Lorsqu’un élément est sélectionné, une deuxième instance du `ImagesController` est créée, uniquement cette fois à l’aide d’une classe de disposition différente. Pour ce contrôleur, `UseLayoutToLayoutNavigtionTransitions` a la `true`valeur, comme indiqué ci-dessous :

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
    circleLayout = new CircleLayout (Monkeys.Instance.Count){
        ItemSize = new CGSize (100, 100)
    };

    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true
        };

    NavigationController.PushViewController (controller2, true);
}
```

La `UseLayoutToLayoutNavigationTransitions` propriété doit être définie avant d’ajouter le contrôleur à la pile de navigation. Avec cette propriété définie, la transition à glissement horizontale normale est remplacée par une transition animée entre les dispositions des deux contrôleurs, comme illustré ci-dessous :

![](transitions-images/nav2.png "Transition animée entre les dispositions des deux contrôleurs")

### <a name="transition-layout"></a>Disposition de transition

En plus de la prise en charge de la transition de disposition dans les `UICollectionViewTransitionLayout` contrôleurs de navigation, une nouvelle mise en page appelée est désormais disponible. Cette classe de disposition autorise le contrôle interactif pendant le processus de transition de la `TransitionProgress` disposition, en permettant à de définir à partir du code. `UICollectionViewTransitionLayout`est différent de et ne remplace pas pour-la `SetCollectionViewLayout` méthode d’iOS 6 qui a provoqué une transition de disposition animée. Cette méthode n’a pas fourni de prise en charge intégrée pour le contrôle de la progression de la transition animée.

 `UICollectionViewTransitionLayout`permet, par exemple, qu’un module de reconnaissance de mouvement soit configuré pour contrôler la transition entre les dispositions en réponse à l’interaction de l’utilisateur, en gérant la disposition d’origine, ainsi que la disposition prévue pour la transition.

Les étapes permettant d’implémenter une transition interactive au sein d’un `UICollectionViewTransitionLayout` module de reconnaissance de mouvement à l’aide de sont les suivantes :

1. Créer un module de reconnaissance de mouvement.
1. Appelez la `StartInteractiveTransition` méthode `UICollectionView` du, en lui passant la disposition cible et un gestionnaire d’achèvement.
1. Définissez la `TransitionProgress` propriété de l' `UICollectionViewTransitionLayout` instance retournée à `StartInteractiveTransition` partir de la méthode.
1. Invalidez la disposition.
1. Appelez la `FinishInteractiveTransition` méthode `UICollectionView` du pour effectuer la transition ou la `CancelInteractiveTransition` méthode pour l’annuler.  `FinishInteractiveTransition`provoque la transition de l’animation à la disposition cible, tandis `CancelInteractiveTransition` que les résultats de l’animation retournent à la disposition d’origine.
1. Gérez l’achèvement de la transition dans le gestionnaire d' `StartInteractiveTransition` achèvement de la méthode.
1. Ajoutez le module de reconnaissance de mouvement à la vue de collection.

Le code suivant implémente une transition de disposition interactive dans un module de reconnaissance de mouvement de pincement :

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

À mesure que l’utilisateur pince la vue de collection `TransitionProgress` , le est défini par rapport à l’échelle du pincement. Dans cette implémentation, si l’utilisateur met fin au pincement avant la fin de la transition de 50%, la transition est annulée. Dans le cas contraire, la transition est terminée.

## <a name="related-links"></a>Liens associés

- [Introduction à iOS 7 (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Vue d'ensemble de l'interface utilisateur iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)

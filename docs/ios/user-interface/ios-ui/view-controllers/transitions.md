---
title: Afficher les transitions du contrôleur dans Xamarin. iOS
description: Ce document explique comment personnaliser des transitions animées entre les contrôleurs d’affichage dans les applications Xamarin. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: e973221e013132c8172a4b1de0250a085f0ea1df
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436927"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Afficher les transitions du contrôleur dans Xamarin. iOS

UIKit ajoute la prise en charge de la personnalisation de la transition animée qui se produit lors de la présentation des contrôleurs d’affichage. Cette prise en charge est incluse avec les contrôleurs intégrés, ainsi que pour tous les contrôleurs personnalisés qui héritent directement de `UIViewController` . En outre, `UICollectionViewController` tire parti de la personnalisation de transition de contrôleur pour tirer parti des transitions animées dans les dispositions de vue de collection.

## <a name="custom-transitions"></a>Transitions personnalisées

La transition animée entre les contrôleurs d’affichage dans iOS 7 est entièrement personnalisable. `UIViewController` inclut désormais une `TransitioningDelegate` propriété qui fournit une classe d’animation personnalisée au système lorsqu’une transition se produit.

Pour utiliser une transition personnalisée avec `PresentViewController` :

1. Affectez la valeur à  `ModalPresentationStyle`  `UIModalPresentationStyle.Custom` sur le contrôleur à présenter.
2. Implémentez  `UIViewControllerTransitioningDelegate` pour créer une classe d’animation, qui est une instance de  `UIViewControllerAnimatedTransitioning` .
3. Affectez  `TransitioningDelegate` à la propriété une instance de  `UIViewControllerTransitioningDelegate` , également sur le contrôleur à présenter.
4. Présentez le contrôleur d’affichage.

Par exemple, le code suivant présente un contrôleur d’affichage de type `ControllerTwo` -une `UIViewController` sous-classe :

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

L’exécution de l’application et le fait de cliquer sur le bouton entraîne l’animation par défaut de la vue du deuxième contrôleur à partir du bas, comme indiqué ci-dessous :

 ![L’exécution de l’application et le fait de cliquer sur le bouton entraîne l’animation par défaut de la deuxième vue contrôleurs à partir du bas](transitions-images/no-custom-transition.png)

Toutefois, la définition de `ModalPresentationStyle` et `TransitioningDelegate` entraîne une animation personnalisée pour la transition :

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

`TransitioningDelegate`Est chargé de créer une instance de la sous `UIViewControllerAnimatedTransitioning` -classe, appelée `CustomAnimator` dans l’exemple ci-dessous :

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

Lorsque la transition a lieu, le système crée une instance de `IUIViewControllerContextTransitioning` , qu’elle a transmise aux méthodes de l’animateur. `IUIViewControllerContextTransitioning` contient le `ContainerView` où l’animation se produit, ainsi que le contrôleur d’affichage qui lance la transition et le contrôleur d’affichage vers lequel effectuer la transition.

La `UIViewControllerAnimatedTransitioning` classe gère l’animation réelle. Deux méthodes doivent être implémentées :

1. `TransitionDuration` : retourne la durée de l’animation, en secondes.
1. `AnimateTransition` : effectue l’animation réelle.

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

Désormais, lorsque le bouton est frappé, l’animation implémentée dans la `UIViewControllerAnimatedTransitioning` classe est utilisée :

 ![Exemple de zoom en cours d’exécution](transitions-images/custom-transition.png)

## <a name="collection-view-transitions"></a>Transitions de la vue de collection

Les vues de collection offrent une prise en charge intégrée pour la création de transitions animées :

- **Contrôleurs de navigation** : la transition animée entre deux  `UICollectionViewController` instances peut éventuellement être gérée automatiquement lorsqu’un  `UINavigationController` les gère.
- **Transition layout** : une nouvelle  `UICollectionViewTransitionLayout` classe permet la transition interactive entre les dispositions.

### <a name="navigation-controller-transitions"></a>Transitions du contrôleur de navigation

Lorsqu’il est utilisé dans un contrôleur de navigation, un `UICollectionViewController` comprend la prise en charge des transitions animées entre les contrôleurs. Cette prise en charge est intégrée et ne nécessite que quelques étapes simples pour implémenter :

1. Affectez  `UseLayoutToLayoutNavigationTransitions` la valeur à  `false` sur  `UICollectionViewController` .
1. Ajoutez une instance de  `UICollectionViewController` à la racine de la pile du contrôleur de navigation.
1. Créez une seconde  `UICollectionViewController` et affectez  `UseLayoutToLayoutNavigtionTransitions` à sa propriété la valeur  `true` .
1. Poussez le deuxième  `UICollectionViewController` sur la pile du contrôleur de navigation.

Le code suivant ajoute une `UICollectionViewController` sous-classe nommée `ImagesCollectionViewController` à la racine de la pile d’un contrôleur de navigation, avec la `UseLayoutToLayoutNavigationTransitions` propriété définie sur `false` :

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

Lorsqu’un élément est sélectionné, une deuxième instance du `ImagesController` est créée, uniquement cette fois à l’aide d’une classe de disposition différente. Pour ce contrôleur, `UseLayoutToLayoutNavigtionTransitions` a la valeur `true` , comme indiqué ci-dessous :

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

![Transition animée entre les dispositions des deux contrôleurs](transitions-images/nav2.png)

### <a name="transition-layout"></a>Disposition de transition

En plus de la prise en charge de la transition de disposition dans les contrôleurs de navigation, une nouvelle mise en page appelée `UICollectionViewTransitionLayout` est désormais disponible. Cette classe de disposition autorise le contrôle interactif pendant le processus de transition de la disposition, en permettant à `TransitionProgress` de définir à partir du code. `UICollectionViewTransitionLayout` est différent de et ne remplace pas pour-la `SetCollectionViewLayout` méthode d’iOS 6 qui a provoqué une transition de disposition animée. Cette méthode n’a pas fourni de prise en charge intégrée pour le contrôle de la progression de la transition animée.

 `UICollectionViewTransitionLayout` permet, par exemple, qu’un module de reconnaissance de mouvement soit configuré pour contrôler la transition entre les dispositions en réponse à l’interaction de l’utilisateur, en gérant la disposition d’origine, ainsi que la disposition prévue pour la transition.

Les étapes permettant d’implémenter une transition interactive au sein d’un module de reconnaissance de mouvement à l’aide de sont les suivantes `UICollectionViewTransitionLayout` :

1. Créer un module de reconnaissance de mouvement.
1. Appelez la  `StartInteractiveTransition` méthode du  `UICollectionView` , en lui passant la disposition cible et un gestionnaire d’achèvement.
1. Définissez la `TransitionProgress` propriété de l'  `UICollectionViewTransitionLayout` instance retournée à partir de la  `StartInteractiveTransition` méthode.
1. Invalidez la disposition.
1. Appelez la `FinishInteractiveTransition` méthode du  `UICollectionView` pour effectuer la transition ou la  `CancelInteractiveTransition` méthode pour l’annuler.  `FinishInteractiveTransition` provoque la transition de l’animation à la disposition cible, tandis que les `CancelInteractiveTransition` résultats de l’animation retournent à la disposition d’origine.
1. Gérez l’achèvement de la transition dans le gestionnaire d’achèvement de la  `StartInteractiveTransition` méthode.
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

À mesure que l’utilisateur pince la vue de collection, le `TransitionProgress` est défini par rapport à l’échelle du pincement. Dans cette implémentation, si l’utilisateur met fin au pincement avant la fin de la transition de 50%, la transition est annulée. Dans le cas contraire, la transition est terminée.

## <a name="related-links"></a>Liens associés

- [Introduction à iOS 7 (exemple)](/samples/xamarin/ios-samples/introtoios7)
- [Vue d’ensemble de l’interface utilisateur iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
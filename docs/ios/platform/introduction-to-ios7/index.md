---
title: Introduction à iOS 7
description: Cet article traite des nouvelles API majeures introduites dans iOS 7, notamment les transitions de contrôleur d’affichage, les améliorations apportées aux animations UIView, UIKit Dynamics et le kit de texte. Il aborde également certaines des modifications apportées à l’interface utilisateur, ainsi que les nouvelles fonctionnalités de multitâches.
ms.prod: xamarin
ms.assetid: 2C33018F-D64A-4BAA-A34E-082EF311D162
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9f27fa550bb21f4261288e8a9a3d809a0ccc7de2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436499"
---
# <a name="introduction-to-ios-7"></a>Introduction à iOS 7

_Cet article traite des nouvelles API majeures introduites dans iOS 7, notamment les transitions de contrôleur d’affichage, les améliorations apportées aux animations UIView, UIKit Dynamics et le kit de texte. Il aborde également certaines des modifications apportées à l’interface utilisateur, ainsi que les nouvelles fonctionnalités de multitâches._

iOS 7 est une mise à jour majeure de iOS. Il introduit une conception d’interface utilisateur entièrement nouvelle qui met l’accent sur le contenu plutôt que sur le chrome d’application. Outre les modifications visuelles, iOS 7 ajoute une multitude de nouvelles API pour créer des interactions plus riches et des expériences. Ce document explore les nouvelles technologies introduites avec iOS 7 et sert de point de départ pour une exploration plus poussée.

## <a name="uiview-animation-enhancements"></a>Améliorations apportées à l’animation UIView

iOS 7 augmente la prise en charge de l’animation dans UIKit, ce qui permet aux applications d’effectuer des opérations qui nécessitaient auparavant la suppression directe de l’infrastructure d’animation principale. Par exemple, `UIView` peut à présent effectuer des animations de ressort et des animations d’image clé, précédemment `CAKeyframeAnimation` appliquées à un `CALayer` .

### <a name="spring-animations"></a>Animations Spring

 `UIView` prend désormais en charge l’animation des modifications de propriété avec un effet de ressort. Pour ajouter cela, appelez la `AnimateNotify` méthode ou `AnimateNotifyAsync` , en transmettant des valeurs pour le taux d’amortissement des ressorts et la vélocité initiale du ressort, comme décrit ci-dessous :

- `springWithDampingRatio` : Valeur comprise entre 0 et 1, où l’oscillation augmente pour une valeur plus petite.
- `initialSpringVelocity` : Vitesse du ressort initial sous la forme d’un pourcentage de la distance totale de l’animation par seconde.

Le code suivant produit un effet de ressort lorsque le centre de la vue d’image change :

```csharp
void AnimateWithSpring ()
{
    float springDampingRatio = 0.25f;
    float initialSpringVelocity = 1.0f;

    UIView.AnimateNotify (3.0, 0.0, springDampingRatio, initialSpringVelocity, 0, () => {

        imageView.Center = new CGPoint (imageView.Center.X, 400);

    }, null);
}
```

Cet effet de ressort fait que la vue d’image semble rebondir lorsqu’elle termine son animation à un nouvel emplacement central, comme illustré ci-dessous :

 ![Cet effet de ressort amène l’affichage de l’image à rebondir lorsqu’il effectue son animation vers un nouvel emplacement central.](images/spring-animation.png)

### <a name="keyframe-animations"></a>Animations d’images clés

La `UIView` classe comprend maintenant la `AnimateWithKeyframes` méthode permettant de créer des animations d’image clé sur un `UIView` . Cette méthode est similaire à d’autres `UIView` méthodes d’animation, sauf qu’une valeur supplémentaire `NSAction` est passée en tant que paramètre pour inclure les images clés. Dans `NSAction` , les images clés sont ajoutées en appelant `UIView.AddKeyframeWithRelativeStartTime` .

Par exemple, l’extrait de code suivant crée une animation d’image clé pour animer le centre d’une vue, ainsi que pour faire pivoter l’affichage :

```csharp
void AnimateViewWithKeyframes ()
{
    var initialTransform = imageView.Transform;
    var initialCeneter = imageView.Center;

    // can now use keyframes directly on UIView without needing to drop directly into Core Animation

    UIView.AnimateKeyframes (2.0, 0, UIViewKeyframeAnimationOptions.Autoreverse, () => {
        UIView.AddKeyframeWithRelativeStartTime (0.0, 0.5, () => {
            imageView.Center = new CGPoint (200, 200);
        });

        UIView.AddKeyframeWithRelativeStartTime (0.5, 0.5, () => {
            imageView.Transform = CGAffineTransform.MakeRotation ((float)Math.PI / 2);
        });
    }, (finished) => {
        imageView.Center = initialCeneter;
        imageView.Transform = initialTransform;

        AnimateWithSpring ();
    });
}
```

Les deux premiers paramètres de la `AddKeyframeWithRelativeStartTime` méthode spécifient respectivement l’heure de début et la durée de l’image clé, sous la forme d’un pourcentage de la longueur totale de l’animation. L’exemple ci-dessus entraîne l’animation de la vue d’image vers son nouveau centre au cours de la première seconde, puis la rotation de 90 degrés sur la seconde suivante. Étant donné que l’animation spécifie `UIViewKeyframeAnimationOptions.Autoreverse` comme option, les deux images clés sont également animées en arrière. Enfin, les valeurs finales sont définies sur l’état initial dans le gestionnaire d’achèvement.

Les captures d’écran ci-dessous illustrent l’animation combinée via les images clés :

 ![Cette capture d’écran illustre l’animation combinée via les images clés.](images/keyframes.png)

## <a name="uikit-dynamics"></a>UIKit Dynamics

UIKit Dynamics est un nouvel ensemble d’API dans UIKit qui permet aux applications de créer des interactions animées basées sur la physique. UIKit Dynamics encapsule un moteur physique 2D pour rendre cela possible.

L’API est déclarative par nature. Vous pouvez déclarer comment les interactions physiques se comportent en créant des objets (appelés *comportements* ) pour exprimer des concepts physiques tels que la gravité, les collisions, les ressorts, etc. Vous attachez ensuite le ou les comportements à un autre objet, appelé *animation dynamique*, qui encapsule une vue. L’animateur dynamique s’attache à appliquer les comportements physiques déclarés aux éléments *dynamiques* -éléments qui implémentent `IUIDynamicItem` , tels qu’un `UIView` .

Plusieurs comportements primitifs différents sont disponibles pour déclencher des interactions complexes, notamment :

- `UIAttachmentBehavior` : Attache deux éléments dynamiques de sorte qu’ils se déplacent ensemble ou attache un élément dynamique à un point d’attache.
- `UICollisionBehavior` : Permet aux éléments dynamiques de participer aux collisions.
- `UIDynamicItemBehavior` : Spécifie un ensemble général de propriétés à appliquer aux éléments dynamiques, tels que l’élasticité, la densité et le frottement.
- `UIGravityBehavior` : Applique la gravité à un élément dynamique, ce qui entraîne l’accélération des éléments dans la direction de la gravité.
- `UIPushBehavior` : Applique la force à un élément dynamique.
- `UISnapBehavior` : Permet à un élément dynamique de s’aligner sur une position avec un effet de ressort.

Bien qu’il existe de nombreuses primitives, le processus général d’ajout d’interactions physiques à une vue à l’aide de UIKit Dynamics est cohérent entre les comportements :

1. Créer un animateur dynamique.
1. Créer un ou plusieurs comportements.
1. Ajoutez des comportements à l’animateur dynamique.

### <a name="dynamics-example"></a>Exemple Dynamics

Examinons un exemple qui ajoute la gravité et une limite de collision à un `UIView` .

#### <a name="uigravitybehavior"></a>UIGravityBehavior

L’ajout de la gravité à une vue d’image suit les 3 étapes décrites ci-dessus.

Nous allons utiliser la `ViewDidLoad` méthode pour cet exemple. Tout d’abord, ajoutez une instance de la façon suivante `UIImageView` :

```csharp
image = UIImage.FromFile ("monkeys.jpg");

imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
                    Image =  image
                }

View.AddSubview (imageView);
```

Cela crée une vue d’image centrée sur le bord supérieur de l’écran. Pour que l’image « chute » avec la gravité, créez une instance de `UIDynamicAnimator` :

```csharp
dynAnimator = new UIDynamicAnimator (this.View);
```

`UIDynamicAnimator`Prend une instance d’une référence `UIView` ou un `UICollectionViewLayout` , qui contient les éléments qui seront animés par le ou les comportements attachés.

Ensuite, créez une `UIGravityBehavior` instance. Vous pouvez passer un ou plusieurs objets implémentant le `IUIDynamicItem` , comme un `UIView` :

```csharp
var gravity = new UIGravityBehavior (dynItems);
```

Le comportement reçoit un tableau de `IUIDynamicItem` qui, dans ce cas, contient l' `UIImageView` instance unique que nous animons.

Enfin, ajoutez le comportement à l’animation dynamique :

```csharp
dynAnimator.AddBehavior (gravity);
```

Cela a pour effet d’animer l’image vers le bas avec la gravité, comme illustré ci-dessous :

![Emplacement de l’image de départ ](images/gravity2.png)
 ![ de l’emplacement de fin de l’image](images/gravity3.png)

Étant donné qu’il n’y a rien de contraindre les limites de l’écran, la vue d’image se trouve simplement en bas. Pour contraindre la vue afin que l’image soit en conflit avec les bords de l’écran, nous pouvons ajouter un `UICollisionBehavior` . Nous aborderons cela dans la section suivante.

#### <a name="uicollisionbehavior"></a>UICollisionBehavior

Nous allons commencer par créer un `UICollisionBehavior` et l’ajouter à l’animateur dynamique, comme nous l’avons fait pour le `UIGravityBehavior` .

Modifiez le code de façon à inclure les `UICollisionBehavior` éléments suivants :

```csharp
using (image = UIImage.FromFile ("monkeys.jpg")) {

    imageView = new UIImageView (new CGRect (new CGPoint (View.Center.X - image.Size.Width / 2, 0), image.Size)) {
        Image =  image
    };

    View.AddSubview (imageView);

    // 1. create the dynamic animator
    dynAnimator = new UIDynamicAnimator (this.View);

    // 2. create behavior(s)
    var gravity = new UIGravityBehavior (imageView);
    var collision = new UICollisionBehavior (imageView) {
        TranslatesReferenceBoundsIntoBoundary = true
    };

    // 3. add behaviors(s) to the dynamic animator
    dynAnimator.AddBehaviors (gravity, collision);
}
```

`UICollisionBehavior`A une propriété appelée `TranslatesReferenceBoundsIntoBoundry` . L’affectation de la valeur à cette option `true` entraîne l’utilisation des limites de la vue de référence comme limite de collision.

Désormais, lorsque l’image s’anime vers le bas avec la gravité, elle rebondit légèrement en bas de l’écran avant de se régler pour y reposer.

<!--, as shown below:

 ![Now, when the image animates downward with gravity, it bounces slightly off the bottom of the screen before settling to rest there](images/bounce.png)-->

#### <a name="uidynamicitembehavior"></a>UIDynamicItemBehavior

Nous pouvons mieux contrôler le comportement de la vue d’image en baisse avec des comportements supplémentaires. Par exemple, nous pourrions ajouter un `UIDynamicItemBehavior` pour augmenter l’élasticité, provoquant la rebond de la vue d’image quand elle entre en conflit avec le bas de l’écran.

L’ajout d’un `UIDynamicItemBehavior` suit la même procédure que pour les autres comportements. Commencez par créer le comportement :

```csharp
var dynBehavior = new UIDynamicItemBehavior (dynItems) {
    Elasticity = 0.7f
};
```

Ensuite, ajoutez le comportement à l’animation dynamique :

 `dynAnimator.AddBehavior (dynBehavior);`

Avec ce comportement en place, la vue d’image rebondit plus lorsqu’elle entre en conflit avec la limite.

## <a name="general-user-interface-changes"></a>Modifications générales de l’interface utilisateur

Outre les nouvelles API UIKit telles que UIKit Dynamics, les transitions de contrôleur et les animations UIView améliorées décrites ci-dessus, iOS 7 introduit diverses modifications visuelles à l’interface utilisateur, ainsi que les modifications apportées aux API pour différents affichages et contrôles. Pour plus d’informations, consultez la [vue d’ensemble de l’interface utilisateur iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md).

## <a name="text-kit"></a>Kit de texte

Le kit de texte est une nouvelle API qui offre de puissantes fonctionnalités de rendu et de disposition du texte. Il s’appuie sur l’infrastructure de texte de base de bas niveau, mais il est beaucoup plus facile à utiliser que le texte de base.

Pour plus d’informations, consultez notre [TextKit](~/ios/platform/textkit.md)

## <a name="multitasking"></a>Multitâche

iOS 7 change quand et comment le travail en arrière-plan est effectué. L’achèvement des tâches dans iOS 7 ne maintient plus les applications éveillées lorsque les tâches s’exécutent en arrière-plan, et les applications sont réveillées pour le traitement en arrière-plan de manière non contiguë. iOS 7 ajoute également trois nouvelles API pour mettre à jour les applications avec le nouveau contenu en arrière-plan :

- Récupération en arrière-plan : permet aux applications de mettre à jour le contenu en arrière-plan à intervalles réguliers.
- Notifications distantes : permet aux applications de mettre à jour le contenu lors de la réception d’une notification push. Les notifications peuvent être en mode silencieux ou afficher une bannière sur l’écran de verrouillage.
- Service de transfert en arrière-plan : permet le chargement et le téléchargement de données, telles que des fichiers volumineux, sans limite de temps fixe.

Pour plus d’informations sur les nouvelles fonctionnalités multitâches, consultez les sections iOS du [Guide d’arrière-plan](~/ios/app-fundamentals/backgrounding/index.md)Xamarin.

## <a name="summary"></a>Récapitulatif

Cet article présente plusieurs nouveaux ajouts importants à iOS. Tout d’abord, il montre comment ajouter des transitions personnalisées aux contrôleurs d’affichage. Il montre ensuite comment utiliser des transitions dans les vues de collection, à la fois à partir d’un contrôleur de navigation, et de manière interactive entre les vues de collection. Ensuite, il présente plusieurs améliorations apportées aux animations UIView, en expliquant comment les applications utilisent UIKit pour les choses qui nécessitaient auparavant une programmation directement par rapport à l’animation principale. Enfin, l’API Dynamics UIKit, qui apporte un moteur physique à UIKit, est introduite parallèlement à la prise en charge du texte enrichi disponible dans l’infrastructure du kit de texte.

## <a name="related-links"></a>Liens associés

- [Introduction à iOS 7 (exemple)](/samples/xamarin/ios-samples/introtoios7)
- [Vue d’ensemble de l’interface utilisateur iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
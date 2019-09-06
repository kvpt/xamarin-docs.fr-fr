---
title: Utilisation de la navigation tvOS et focus dans Xamarin
description: Cet article traite du concept de focus et de la façon dont il est utilisé pour présenter et gérer la navigation à l’intérieur d’une application Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: fdf675e37bec07ef03576313befbaa9309ab9349
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291572"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Utilisation de la navigation tvOS et focus dans Xamarin

_Cet article traite du concept de focus et de la façon dont il est utilisé pour présenter et gérer la navigation à l’intérieur d’une application Xamarin. tvOS._


Cet article traite du concept de [focus](#Focus-and-Selection) et de la façon dont il est utilisé pour gérer la [navigation](#Navigation) dans une interface utilisateur de l’application Xamarin. tvOS. Nous allons examiner comment les contrôles de navigation tvOS intégrés utilisent le focus, la mise en surbrillance et la sélection pour fournir la navigation de l’interface utilisateur de votre application Xamarin. tvOS.

[![](navigation-focus-images/intro01.png "Navigation dans l’interface utilisateur des applications tvOS")](navigation-focus-images/intro01.png#lightbox)

Ensuite, nous allons examiner comment le focus peut être utilisé avec la [parallaxe](#Focus-and-Parallax) et les *images superposées* pour fournir des indices visuels pour l’état de navigation actuel à l’utilisateur final.

Enfin, nous étudierons l’utilisation du [focus](#Working-with-Focus), le [focus des mises à jour](#Working-with-Focus-Updates), les [guides de focalisation](#Working-with-Focus-Guides), [le focus dans les collections](#Working-with-Focus-in-Collections) et [l’activation de parallaxe](#enabling-parallax) sur les vues d’images dans vos applications Xamarin. tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navigation

Les utilisateurs de votre application Xamarin. tvOS n’interagissent pas directement avec son interface, comme avec iOS, où ils appuient sur des images sur l’écran de l’appareil, mais indirectement à travers la salle à l’aide de la [télécommande Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). Vous devez garder cela à l’esprit lors de la conception de l’interface utilisateur de votre application de sorte qu’elle soit naturelle, tout en gardant l’utilisateur plongé dans l’expérience TV Apple.

Une application tvOS réussie implémente la navigation d’une manière qui prend en charge en douceur l’objectif de l’application et la structure des données qu’elle présente sans attirer l’attention sur la navigation elle-même. Concevez votre navigation de manière à ce qu’elle soit naturelle et familière sans qui prennent l’interface utilisateur ou le focus de dessin à l’extérieur du contenu et de l’expérience utilisateur des applications.

[![](navigation-focus-images/nav01.png "Application de paramètres tvOS")](navigation-focus-images/nav01.png#lightbox)

Quand vous utilisez une TV Apple, l’utilisateur parcourt généralement un ensemble d’écrans empilés, chacun présentant un ensemble de contenu donné. À son tour, chaque nouvel écran peut aboutir à un ou plusieurs sous-écrans de contenu à l’aide de contrôles d’interface utilisateur standard tels que des [boutons](~/ios/tvos/user-interface/buttons.md), des [barres d’onglets](~/ios/tvos/user-interface/tab-bars.md), des tables, des [vues de collection](~/ios/tvos/user-interface/collection-views.md) ou des [affichages fractionnés](~/ios/tvos/user-interface/split-views.md).

Avec chaque nouvel écran de données, l’utilisateur accède plus profondément à cette pile d’écrans. En utilisant le bouton de **menu** de la Siri distante, ils peuvent naviguer vers l’arrière dans la pile pour revenir à un écran précédent ou à un menu principal.

Apple suggère de garder à l’esprit les éléments suivants lors de la conception de la navigation pour votre application tvOS :

- **Mettez en page votre navigation pour** que les utilisateurs puissent accéder rapidement et facilement au contenu de votre application dans le plus petit nombre de clics, de clics et de glissement. Simplifiez votre navigation et organisez le contenu avec le nombre minimal d’écrans.
- **Créez une interface fluidique à l’aide de Touch** -Assurez-vous qu’un utilisateur peut passer d’un élément pouvant être _actif_ à un minimum de frottement en utilisant le plus petit nombre de gestes possible.
- **Conception à l’esprit** : étant donné que l’utilisateur interagit avec le contenu de la pièce, il doit déplacer le focus sur un élément d’interface utilisateur avant d’interagir avec lui à l’aide de la Siri distante. Les utilisateurs seront frustrés de votre application s’ils ont besoin d’un trop grand nombre de gestes pour atteindre leurs objectifs.
- **Fournir une navigation vers l’arrière via le bouton de menu** : pour créer une expérience simple et familière, permettre aux utilisateurs de naviguer vers l’arrière à l’aide du bouton de **menu** distant Siri. Le fait d’appuyer sur le bouton de **menu** doit toujours revenir à l’écran précédent ou revenir au menu principal de l’application. Au niveau supérieur de l’application, appuyez sur le bouton de **menu** pour revenir à l’écran d’accueil Apple TV.
- **Évitez généralement d’afficher un bouton précédent** -parce que le fait d’appuyer sur le bouton **menu** de la Siri distante parcourt vers l’arrière dans la pile d’écran, évitez d’afficher un contrôle supplémentaire qui duplique ce comportement. Une exception à cette règle concerne l’achat d’écrans ou d’écrans avec des actions destructrices (telles que la suppression de contenu) où un bouton **Annuler** doit également être affiché.
- **Affichez les grandes collections sur un seul écran, à la place de plusieurs** -la Siri distante a été conçue pour faire passer rapidement et facilement une grande collection de contenu à l’aide de gestes. Si votre application fonctionne avec un grand nombre d’éléments pouvant être activés, envisagez de les conserver sur un seul écran au lieu de les décomposer en plusieurs écrans qui nécessitent davantage de navigation dans la part de l’utilisateur.
- **Utilisez des contrôles standard pour la navigation** -encore, pour créer une expérience utilisateur simple et familière, dans la mesure du possible `UIKit` , utilisez des contrôles intégrés tels que les contrôles de page, les barres d’onglets, les contrôles segmentés, les vues de table, les vues de collection et les vues fractionnées pour navigation de votre application. Étant donné que les utilisateurs sont déjà familiarisés avec ces éléments, ils pourront parcourir votre application de manière intuitive.
- **Privilégier la navigation dans le contenu horizontal** -en raison de la nature du téléviseur Apple, le balayage de gauche à droite sur le Siri à distance est plus naturel qu’en haut et en baisse. Envisagez cette option lors de la conception de mises en page de contenu pour votre application.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Focus et sélection

Sur la TV Apple, une image, un bouton ou un autre élément d’interface utilisateur est considéré comme étant _actif_ lorsqu’il est la cible de la navigation actuelle.

[![](navigation-focus-images/focus01.png "Exemple de focus et de sélection")](navigation-focus-images/focus01.png#lightbox)

Contrairement aux appareils iOS dans lesquels l’utilisateur interagit directement avec des éléments sur l’écran tactile de l’appareil, les utilisateurs interagissent avec les éléments tvOS à partir de la salle à l’aide de la télécommande Siri. Pour présenter et gérer cette interaction avec l’utilisateur, Apple TV utilise un modèle de _focus_ .

À l’aide des gestes et des enfoncements de bouton sur le [Siri à distance](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), l’utilisateur déplace le focus d’un élément d’interface utilisateur à l’autre. Une fois le focus déplacé vers l’élément souhaité, l’utilisateur clique pour sélectionner le contenu ou activer l’action représentée par cet élément.

À mesure que le focus change, des animations et des effets subtils (tels que l’effet parallaxe sur les images) sont utilisés pour identifier clairement l’élément d’interface utilisateur qui a actuellement le focus.

Apple propose les suggestions suivantes pour l’utilisation du focus et de la sélection :

- **Utilisez des contrôles d’interface utilisateur intégrés pour les effets** de mouvement `UIKit` : en utilisant et l’API de focus dans votre interface utilisateur, le modèle de focus appliquera automatiquement les effets visuels et animés par défaut à vos éléments d’interface utilisateur. Cela rend votre application plus native et familière aux utilisateurs de la plateforme Apple TV et permet un mouvement fluide et intuitif entre les éléments pouvant faire l’objet du focus.
- **Déplacez le focus dans le sens attendu** -sur Apple TV, presque tous les éléments utilisent la manipulation indirecte. Par exemple, l’utilisateur utilise le Siri distant pour déplacer le focus et le système fait défiler automatiquement l’interface pour que l’élément ayant actuellement le focus reste visible. Si votre application implémente ce type d’interaction, assurez-vous que le focus se déplace dans la direction du mouvement de l’utilisateur. Par conséquent, si l’utilisateur fait défiler vers la droite sur le focus à distance Siri, il doit se déplacer vers la droite (ce qui peut amener l’écran à faire défiler vers la gauche). La seule exception à cette règle est les éléments en plein écran qui utilisent la manipulation directe (où le déplacement vers le haut déplace l’élément vers le haut).
- **Assurez-vous que l’élément ayant le focus est évident** : étant donné que vos utilisateurs interagissent avec vos éléments d’interface utilisateur à partir de Afar, il est essentiel que l’élément ayant actuellement le focus soit présent. En général, cette opération est gérée automatiquement par `UIKit` les éléments intégrés. Pour les contrôles personnalisés, utilisez des fonctionnalités telles que la taille de l’élément ou l’ombre pour afficher le focus.
- **Utilisez la fonction parallaxe pour rendre les éléments ciblés réactifs** : les gestes circulaires sur le Siri distant entraînent un déplacement doux et en temps réel de l’élément ayant le focus. Cet [effet de parallaxe](#Focus-and-Parallax) est intégré `UIKit` à des _images en couches_ pour permettre à l’utilisateur de se connecter à l’élément ayant le focus.
- **Créer des éléments pouvant se concentrer sur la taille appropriée-les** éléments volumineux avec un espacement important sont plus faciles à sélectionner et à parcourir que les éléments plus petits.
- **Concevez l’élément d’interface utilisateur de façon à ce qu’il soit bien ciblé ou inactif** -généralement, Apple TV représente l’élément ayant le focus en augmentant sa taille. Assurez-vous que les éléments d’interface utilisateur de votre application s’affichent parfaitement à toute taille de présentation et, si nécessaire, fournissez des ressources pour les éléments de plus grande taille.
- **Représentent les modifications de focus** de manière fluide : utilisez l’animation pour effectuer un fondu fluide entre un état **actif et sans** **focus** pour empêcher les transitions d’être transférerez.
- **N’affiche pas de curseur** : les utilisateurs s’attendent à parcourir l’interface utilisateur de votre application en utilisant le focus et non en déplaçant un curseur à l’écran. Votre interface utilisateur doit toujours utiliser le modèle de focus pour présenter une expérience utilisateur cohérente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Utilisation du focus

Il peut arriver que vous souhaitiez créer un contrôle personnalisé qui peut devenir un élément pouvant être actif. Si c’est le cas `CanBecomeFocused` , remplacez la `true`propriété et retournez `false`, sinon revenez à. Par exemple :

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

À tout moment, vous pouvez utiliser `Focused` la propriété d' `UIKit` un contrôle pour voir s’il s’agit de l’élément actuel. Si `true` l’élément d’interface utilisateur a actuellement le focus, sinon, ce n’est pas le cas. Par exemple :

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Bien que vous ne puissiez pas déplacer directement le focus vers un autre élément d’interface utilisateur à l’aide du code, vous pouvez spécifier l’élément d’interface `PreferredFocusedView` utilisateur qui `true`reçoit d’abord le focus quand un écran est chargé en affectant à sa propriété la valeur. Par exemple :

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Utilisation des mises à jour de focus 

Lorsque l’utilisateur fait passer le focus d’un élément d’interface utilisateur à un autre (par exemple, en réponse à un mouvement sur le Siri distant), un _événement de mise à jour de focus_ est envoyé à l’élément qui perd le focus et l’élément obtient le focus.

Pour les éléments personnalisés qui héritent `UIViewController`de `UIView` ou, vous pouvez substituer plusieurs méthodes pour utiliser l’événement de mise à jour du Focus :

- **DidUpdateFocus** : cette méthode est appelée chaque fois que la vue gagne ou perd le focus.
- **ShouldUpdateFocus** : utilisez cette méthode pour définir l’emplacement où le focus est autorisé à se déplacer.

Pour demander que le moteur de focus repasse le focus `PreferredFocusedView` à l’élément d’interface `SetNeedsUpdateFocus` utilisateur, appelez la méthode du contrôleur d’affichage.

> [!IMPORTANT]
> L' `SetNeedsUpdateFocus` appel à n’a d’effet que si le contrôleur d’affichage sur lequel il est appelé contient la vue qui a actuellement le focus.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Utilisation des repères de focus

Le moteur de focalisation intégré à tvOS est parfait pour la gestion des mouvements entre les éléments qui se trouvent sur une grille horizontale et verticale. En règle générale, vous n’avez rien de plus à ajouter les éléments d’interface utilisateur à votre conception d’interface et le moteur de Focus gère automatiquement le déplacement entre ces éléments sans intervention du développeur.

Toutefois, il peut y avoir des cas, en raison de la nécessité de la conception de votre interface utilisateur, lorsque les éléments d’interface utilisateur ne se trouvent pas sur une grille horizontale et verticale et peuvent être inaccessibles, car ils sont en diagonale les uns par les autres. Cela se produit parce que le moteur de focus a été conçu pour gérer des mouvements simples, descendants, gauches et droits entre les éléments d’interface utilisateur uniquement.

Pour obtenir un exemple, prenez la disposition d’interface utilisateur suivante :

 [![](navigation-focus-images/guide01.png "Exemple d’utilisation de repères de focus")](navigation-focus-images/guide01.png#lightbox)
 
Étant donné que le bouton **plus d’informations** ne se trouve pas sur une grille horizontale et verticale avec le bouton **acheter** , il n’est pas accessible à l’utilisateur. Toutefois, cela peut être facilement corrigé à l’aide d’un _repère de focalisation_ pour fournir des indications de déplacement au moteur de focalisation. 

Un guide de focus`UIFocusGuide`() expose une zone non visible de la vue comme pouvant se concentrer sur le moteur de focus, ce qui permet de rediriger le focus vers une autre vue.

Ainsi, pour résoudre l’exemple ci-dessus, le code suivant peut être ajouté au contrôleur d’affichage pour créer un repère de focus entre les boutons **plus d’informations** et d' **achat** :

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

Tout d’abord, `UIFocusGuide` un nouveau est créé et ajouté à la collection de guides de disposition `AddLayoutGuide` de la vue à l’aide de la méthode.

Ensuite, les ancres supérieure, gauche, largeur et hauteur du repère de focus sont ajustées par rapport aux boutons **plus d’informations** et d' **achat** pour les placer entre eux. Consultez :

[![](navigation-focus-images/guide02.png "Guide de focus de l’exemple")](navigation-focus-images/guide02.png#lightbox)

Il est également important de noter que les nouvelles contraintes sont activées au fur et à mesure de leur `Active` création en `true`affectant à leur propriété la valeur :

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Une fois le nouveau guide de focus créé et ajouté à la vue, la méthode `DidUpdateFocus` du contrôleur d’affichage peut être substituée et le code suivant est ajouté pour se déplacer entre les boutons **plus d’informations** et d' **achat** :

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

Tout d’abord, ce code obtient `NextFocusedView` le de `UIFocusUpdateContext` la qui a été passée (`context`). Si cette vue est `null`, aucun traitement n’est nécessaire et la méthode s’est arrêtée.

Ensuite, `nextFocusableItem` est évalué. S’il correspond aux boutons **plus d’infos** ou **acheter** , le focus est envoyé au bouton opposé à l’aide de la `PreferredFocusedView` propriété du repère de focus. Par exemple :

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

Dans le cas où aucun bouton n’est la source du décalage de focus, `PreferredFocusedView` la propriété est désactivée :

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Utilisation du focus dans les collections

Lorsque vous décidez si un élément individuel peut ou non être actif dans un `UICollectionView` ou un `UITableView`, vous devez substituer les `UICollectionViewDelegate` méthodes de ou `UITableViewDelegate` , respectivement. Par exemple :

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

La `CanFocusItem` méthode retourne `true` si l’élément actuel peut être actif, sinon il retourne `false`.

Si vous souhaitez qu' `UICollectionView` un ou `UITableView` un se rappelle et restaure le focus sur le dernier élément lorsqu’il perd et acquiert le focus `RemembersLastFocusedIndexPath` , affectez à `true`la propriété la valeur.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Focus et parallaxe

Comme indiqué ci-dessus, un élément d’interface utilisateur est considéré comme étant _actif_ lorsqu’il s’agit de l’élément actuel pendant un événement de navigation. En règle générale, quand un élément est activé, sa taille est légèrement augmentée et est visuellement élevée à l’aide d’une ombre. 

Si l’utilisateur effectue un mouvement circulaire lent sur le Siri à distance, l’élément ayant le focus est Sway en temps réel en réponse à ce mouvement. Au fur et à mesure que le Sway se produit, un reflet éclairé est appliqué à son image, ce qui rend l’aire sembler éclatée. Après une quantité donnée d’inactivité, tout contenu hors focus est estompé et l’élément ayant le focus augmente encore plus. 

Ces effets fonctionnent ensemble pour fournir une connexion mentale entre le contenu de l’écran TV et l’utilisateur qui interagit avec la TV Apple à partir du canapé.

Sur Apple TV, cet effet de parallaxe est utilisé dans tout le système pour donner une idée de la profondeur 3D et de la dynamique aux éléments actifs. tvOS utilise une série d' [images multicouches](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) transparentes qu’il déplace et met à l’échelle dynamiquement pour créer cet effet.

Les images superposées sont requises pour l’icône de votre application tvOS et sont prises en charge pour le contenu dynamique supérieur. Bien que cela ne soit pas obligatoire, Apple suggère vivement d’implémenter des images en couches dans tout autre élément pouvant être actif dans l’interface utilisateur de votre application.

### <a name="enabling-parallax"></a>Activation de la parallaxe

Le `UIImageView` contrôle (ou tout contrôle qui hérite de `UIImageView`) prend automatiquement en charge l’effet parallaxe. Par défaut, cette prise en charge est désactivée. pour l’activer, utilisez le code suivant :

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Si cette propriété a la `true`valeur, la vue d’image obtiendra automatiquement l’effet de parallaxe lorsqu’elle sera sélectionnée par l’utilisateur et dans le focus.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé le concept de focus et la façon dont il est utilisé pour gérer la navigation dans une interface utilisateur de l’application Xamarin. tvOS. Il examine comment les contrôles de navigation tvOS intégrés utilisent le focus, la mise en surbrillance et la sélection pour assurer la navigation. Ensuite, il a vu comment le focus peut être utilisé avec la parallaxe et les images superposées pour fournir des indices visuels pour l’état de navigation actuel à l’utilisateur final. Enfin, il a examiné l’utilisation du focus, le focus des mises à jour, le focus dans les collections et l’activation de la parallaxe.




## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)

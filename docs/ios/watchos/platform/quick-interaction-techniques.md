---
title: Techniques d’interaction rapide pour Watchos 3 dans Xamarin
description: Cet article aborde les techniques d’interaction rapide qu’Apple a ajoutées à Watchos 3 et comment les implémenter dans Xamarin. iOS pour Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: a0719dce3b7f5484168dc5679237b01286f5de78
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286755"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Techniques d’interaction rapide pour Watchos 3 dans Xamarin

_Cet article aborde les techniques d’interaction rapide qu’Apple a ajoutées à Watchos 3 et comment les implémenter dans Xamarin. iOS pour Apple Watch._

Il est essentiel de fournir des interactions utilisateur rapides pour créer des applications de Apple Watch attrayantes et des complications. Nouveauté de Watchos 3, Apple a ajouté la prise en charge des détecteurs de mouvement, l’accès aux Digital Crown et aux nouvelles techniques de notification et de navigation utilisateur. Cela, ainsi que la prise en charge supplémentaire pour SceneKit et SpriteKit, permettent au développeur de créer facilement des interfaces riches et faciles à utiliser, à la fois rapides et réactives.

## <a name="what-are-quick-interactions"></a>Que sont les interactions rapides ?

Pour un développeur qui est utilisé pour créer des applications pour iOS ou macOS (où la durée pendant laquelle un utilisateur entre en interaction avec l’application est mesurée en minutes ou en heures), concevoir une application réussie pour le Apple Watch peut être un défi et nécessite une autre méthode.

En regardantos, l’utilisateur souhaite généralement déclencher son poignet, interagir rapidement avec une application (en général, pendant quelques secondes), supprimer son poignet et continuer tout ce qu’il était en cours.

Voici quelques exemples d’interactions rapides typiques sur le Apple Watch :

- Démarrage d’un minuteur.
- Vérification de la météo.
- Marquage d’un élément dans une liste de tâches.

Pour atteindre ces objectifs, une application sur la Apple Watch doit être :

- **Aperçu** : cela signifie qu’avec un coup d’œil rapide, l’utilisateur doit pouvoir obtenir les informations dont il a besoin. 
- **Actionable** : les utilisateurs doivent être en mesure de prendre des décisions rapides et bien informées.
- **Réactif** : cela signifie que l’utilisateur ne doit jamais attendre pour recevoir les informations dont il a besoin ou pour accomplir l’action qu’il souhaite.

### <a name="quick-interactions-length"></a>Longueur des interactions rapides

En raison de la nature plus facile des applications Apple Watch, Apple suggère que la longueur idéale d’une interaction rapide doit être inférieure ou égale à deux secondes. En raison de cette limite de deux secondes, le développeur devra consacrer beaucoup de temps à la conception et à l’implémentation d’une application Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nouvelles fonctionnalités et API Watchos 3

Apple a ajouté plusieurs nouvelles fonctionnalités et API à WatchKit pour aider le développeur à ajouter des interactions rapides à leurs applications Apple Watch :

- Watchos 3 donne accès à de nouveaux types d’entrées utilisateur, telles que :
  - Détecteurs de mouvement
  - Rotation Digital Crown 
- Watchos 3 offre de nouvelles façons d’afficher et de mettre à jour des informations, telles que :
  - Navigation dans les tables améliorée
  - Nouvelle prise en charge de l’infrastructure de notification utilisateur
  - Intégration de SpriteKit et de SceneKit

En implémentant ces nouvelles fonctionnalités, le développeur peut s’assurer que leur application Watchos 3 est plus rapide, actionnable et réactive.

### <a name="gesture-recognizer-support"></a>Prise en charge du module de reconnaissance de mouvement

Si le développeur a implémenté des détecteurs de mouvement dans iOS, il doit être très familiarisé avec le fonctionnement des rechargements de mouvement dans Watchos 3. Pour actualiser, les détecteurs de mouvement sont des objets qui analysent les événements tactiles de bas niveau en gestes prédéfinis et reconnaissables.

Watchos 3 prend en charge les quatre détecteurs de mouvements suivants :

- Types de mouvements discrets :
  - Mouvement de balayage (`WKSwipeGestureRecognizer`).
  - Mouvement TAP (`WKTapGestureRecognizer`).
- Types de mouvements continus :
  - Mouvement de panoramique (`WKPanGestureRecognizer`).
  - Mouvement de longue durée (`WKLongPressGestureRecognizer`).

Pour implémenter l’un des nouveaux détecteurs de mouvement, faites-le simplement glisser sur une aire de conception du concepteur iOS dans Visual Studio pour Mac et configurez ses propriétés.

Dans le code, répondez à l’action du module de reconnaissance pour gérer le mouvement déclenché par l’utilisateur. Là encore, cette opération s’effectue de la même façon que dans iOS.

#### <a name="discrete-gesture-states"></a>États de mouvement discret

Pour les gestes discrets, l’action est appelée lorsque le mouvement est reconnu et un État (`WKGestureRecognizerState`) est affecté en tant que :

[![](quick-interaction-techniques-images/quick01.png "États de mouvement discret")](quick-interaction-techniques-images/quick01.png#lightbox)

Tous les mouvements discrets commencent à l' `Possible` État et passent à l' `Failed` État ou `Recognized` . Lorsque vous utilisez des mouvements discrets, le développeur ne traite généralement pas directement l’État. Au lieu de cela, elles s’appuient sur l’action qui est appelée lorsque le mouvement est reconnu uniquement.

#### <a name="continuous-gesture-states"></a>États des mouvements continus

Les mouvements continus diffèrent légèrement des mouvements discrets, où l’action est appelée plusieurs fois lorsque le mouvement est reconnu :

[![](quick-interaction-techniques-images/quick02.png "États des mouvements continus")](quick-interaction-techniques-images/quick02.png#lightbox)

Là encore, les mouvements continus commencent à `Possible` l’État, mais ils progressent sur plusieurs mises à jour. Ici, le développeur doit prendre en compte l’état du module de reconnaissance et mettre à jour l' `Changed` interface utilisateur de l’application pendant `Recognized` la `Canceled`phase jusqu’à ce que le mouvement soit final ou.

#### <a name="gesture-recognizer-usage-tips"></a>Conseils d’utilisation du module de reconnaissance de mouvement

Apple suggère les éléments suivants lorsque vous utilisez des détecteurs de mouvement dans Watchos 3 :

- Ajoutez les modules de reconnaissance de mouvement pour regrouper des éléments plutôt que des contrôles individuels. Étant donné que la Apple Watch a une taille d’écran physique plus petite, les éléments de groupe ont tendance à être plus importants et plus faciles à atteindre pour l’utilisateur. En outre, les détecteurs de mouvement peuvent entrer en conflit avec les gestes intégrés déjà présents dans les contrôles d’interface utilisateur natifs.
- Définissez les relations de dépendance dans le Storyboard de l’application Watch.
- Certains mouvements sont prioritaires sur les autres types de mouvements, par exemple :
  - Défilement
  - Force Touch
 
### <a name="digital-crown-rotation"></a>Rotation Digital Crown

En implémentant la prise en charge Digital Crown dans les applications Watchos 3, un développeur peut fournir une vitesse de navigation accrue et des interactions de précision pour ses utilisateurs.

Étant donné que Watchos 2, Apple Watch application peut utiliser `WKInterfacePicker` l’objet pour accéder à la Digital Crown en fournissant une `WKPickerItems` liste de et un style de sélecteur (liste, pile ou séquence d’image). Watchos permettait ensuite à l’utilisateur d’utiliser le Digital Crown pour sélectionner un élément dans la liste.

Lorsque vous utilisez `WKInterfacePicker`un, WatchKit gère la majeure partie du travail de la façon suivante :

- Dessin de la liste et des éléments d’interface individuels.
- Traitement des événements de Digital Crown.
- Appel d’une action lorsqu’un élément est sélectionné.

Nouveauté de Watchos 3, le développeur a désormais un accès direct aux événements de rotation Digital Crown qui leur permet de créer leurs propres éléments d’interface utilisateur qui répondent aux valeurs de rotation.

L’accès Digital Crown est fourni par les éléments suivants :

- `WKCrownSequencer`-Permet d’accéder aux rotations par seconde.
- `WKCrownDelegate`-Fournit l’accès aux événements Delta rotatifs.

#### <a name="rotations-per-second"></a>Rotations par seconde

L’accès aux rotations par seconde à partir de la Digital Crown est utile lors de l’utilisation d’animations basées sur la physique. Pour accéder aux rotations par seconde, utilisez la `CrownSequencer` propriété `WKInterfaceController` de l’extension Watch. Par exemple :

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Deltas de rotation

Utilisez les deltas de rotation de la Digital Crown pour compter le nombre de rotations. Utilisez la `CrownDidRotate` méthode override `WKCrownDelegate` du pour accéder aux deltas de rotation. Par exemple :

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class CrownDelegate : WKCrownDelegate
  {
    #region Computed Properties
    public double AccumulatedRotations { get; set;}
    #endregion

    #region Constructors
    public CrownDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
    {
      base.CrownDidRotate (crownSequencer, rotationalDelta);

      // Accumulate rotations
      AccumulatedRotations += rotationalDelta;
    }
    #endregion
  }
}
```

Ici, l’application maintient un accumulateur`AccumulatedRotations`() pour déterminer le nombre de rotations. Une rotation complète de la Digital Crown est égale à un Delta cumulé de `1.0` et une demi-rotation `0.5`serait.

Apple l’a laissé au développeur pour déterminer comment le nombre de rotations correspond à la sensibilité des modifications apportées à l’élément d’interface utilisateur en cours de mise à jour.

Le signe (`+/-`) du delta de rotation indique la direction dans laquelle l’utilisateur active l’Digital Crown :

[![](quick-interaction-techniques-images/quick03.png "Le signe du delta de rotation indique la direction dans laquelle l’utilisateur active l’Digital Crown")](quick-interaction-techniques-images/quick03.png#lightbox)


Si l’utilisateur fait défiler vers le haut, WatchKit retourne des deltas positifs et, si vous faites défiler vers le haut, les deltas négatifs sont retournés, quelle que soit l’orientation dans laquelle l’utilisateur porte la montre.

#### <a name="digital-crown-focus"></a>Digital Crown Focus

Comme tout autre élément d’interface, le Digital Crown a le concept de focus. Ce Focus peut être décalé du Digital Crown vers d’autres éléments d’interface en fonction de la façon dont l’utilisateur interagit avec la montre. 

Par exemple, l’un des contrôles suivants peut voler le focus de l’Digital Crown :

- Sélecteur
- Curseur
- Contrôleur de défilement

Il revient au développeur de déterminer quand son élément d’interface personnalisé doit être le focus du Digital Crown. Apple suggère d’utiliser les nouveaux module de reconnaissance de mouvement pour obtenir le focus dans l’élément d’interface utilisateur personnalisé.

### <a name="vertical-paging"></a>Pagination verticale

La méthode standard utilisée par un utilisateur pour naviguer dans une vue de table dans une application Watchos consiste à faire défiler jusqu’à l’élément de données souhaité, à appuyer sur une ligne spécifique pour afficher la vue détaillée, à appuyer sur le bouton précédent lorsque vous avez terminé d’afficher les détails et à répéter le processus pour toute autre information que le y sont intéressés par l’intérieur de la table :

[![](quick-interaction-techniques-images/quick04.png "Déplacement entre une table et la vue détaillée")](quick-interaction-techniques-images/quick04.png#lightbox)

Nouveauté de Watchos 3, le développeur peut activer la pagination verticale sur les contrôles d’affichage de table. Lorsque cette fonctionnalité est activée, l’utilisateur peut faire défiler pour rechercher une ligne de vue table et appuyer sur la ligne pour afficher ses détails comme avant. Toutefois, ils peuvent maintenant balayer pour sélectionner la ligne suivante dans le tableau ou vers le haut pour sélectionner la ligne précédente (ou utiliser le Digital Crown), sans avoir à revenir à la vue table en premier :

[![](quick-interaction-techniques-images/quick05.png "Déplacement entre une table et la vue détaillée et balayage vers le haut et vers le haut pour passer d’une ligne à l’autre")](quick-interaction-techniques-images/quick05.png#lightbox)

Pour activer ce mode, ouvrez le Storyboard de l’application Watchos dans Xcode à des fins de modification, sélectionnez la vue de table et cochez la case de **pagination des détails verticales** :

[![](quick-interaction-techniques-images/quick06.png "Cochez la case de pagination des détails verticaux")](quick-interaction-techniques-images/quick06.png#lightbox)

Assurez-vous que la table utilise SEGUES pour afficher la vue détaillée et enregistrer les modifications apportées à la table de montage séquentiel et revenir à Visual Studio pour Mac à synchroniser.

Le développeur peut, par programmation, effectuer une pagination verticale sur une ligne spécifique en utilisant le code suivant sur une vue de table :

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Lorsque vous utilisez la pagination verticale, le développeur doit être conscient que WatchKit gère automatiquement le préchargement des contrôleurs et, par conséquent, certaines méthodes de cycle de vie du contrôleur peuvent être appelées avant que l’interface utilisateur soit visible.

### <a name="notification-enhancements"></a>Améliorations des notifications

La notification est la forme principale d’interaction rapide qu’un utilisateur rencontre généralement sur Watchos et qui sont disponibles depuis la première Apple Watch et Watchos 1.

Une interaction rapide de notification classique est la suivante :

1. L’utilisateur estime la notification haptique lorsqu’une nouvelle notification est reçue.
2. Ils soulèvent leur poignet pour voir l’interface de recherche rapide pour la notification.
3. S’ils continuent de faire apparaître leur poignet, Watchos passe automatiquement à l’interface de notification de longue durée.

Un utilisateur peut répondre à la notification de plusieurs façons :

- Pour une notification bien définie et présentée, l’utilisateur ne fera rien et ignorera simplement la notification.
- Ils peuvent également s’appuyer sur la notification pour lancer l’application Watchos.
- Pour une notification qui prend en charge les actions personnalisées, l’utilisateur peut sélectionner l’une des actions personnalisées. Il peut s’agir des éléments suivants :
  - **Actions de premier plan** : elles lancent l’application pour exécuter l’action.
  - **Actions en arrière-plan** : ont toujours été routés vers l’iPhone dans Watchos 2, mais peuvent être routés vers watchApp dans Watchos 3.

Nouveauté pour Watchos 3 :

- La notification utilise une API similaire sur toutes les plateformes (iOS, Watchos, tvOS et macOS).
- La notification locale peut être planifiée sur la Apple Watch.
- La notification d’arrière-plan sera routée vers l’extension de l’application si elle a été planifiée sur le Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Planification et remise des notifications

La notification de l’iPhone de l’utilisateur est transférée vers le Apple Watch lorsque les éléments suivants se produisent :

- L’écran de l’iPhone est désactivé.
- La Apple Watch est en cours d’usure et a été déverrouillée.

Dans Watchos 3, les notifications locales peuvent être planifiées sur la Apple Watch et ne sont fournies qu’à la montre. Il revient au développeur de planifier une notification iPhone correspondante si elle est requise par l’application.

En incluant le même identificateur de notification sur les versions Apple Watch et iPhone des notifications, vous empêchez l’affichage des notifications dupliquées sur la montre. La version Apple Watch de la notification est prioritaire sur la version de l’iPhone.

Étant donné que Watchos 3 utilise `UINotification` la même infrastructure d’API que IOS 10, consultez notre documentation sur l' [infrastructure de notification utilisateur](~/ios/platform/user-notifications/index.md) iOS 10 pour plus d’informations.

### <a name="using-spritekit-and-scenekit"></a>Utilisation de SpriteKit et SceneKit

Nouveauté de Watchos 3, le développeur peut maintenant utiliser les objets SpritKit et SceneKit dans la conception de l’interface utilisateur de l’application pour présenter des graphiques 2D et 3D.

Deux nouvelles classes d’interface ont été ajoutées pour prendre en charge cette fonctionnalité :

- `WKInterfaceSKScene`-Pour l’utilisation de graphiques 2D SpriteKit.
- `WKInterfaceSCNScene`-Pour travailler avec des graphiques 3D SceneKit.

Pour utiliser ces objets, faites-les simplement glisser sur l’aire de conception à l’intérieur du storyboard de l’application Watch dans le Interface Builder de Xcode et utilisez l' **inspecteur d’attributs** pour les configurer.

À partir de ce point, l’utilisation des scènes SpriteKit ou SceneKit fonctionne de la même façon que dans une application iOS. L’application Watch présente un `WKInterfaceSKScene` en appelant l’une `Present` des méthodes. Pour SceneKit, il vous suffit `Scene` de définir la `WKInterfaceSCNScene` propriété de l’objet.

## <a name="actionable-complications"></a>Complications actionnables

Dans Watchos 2, Apple a introduit des complications pour les applications tierces. Dans Watchos 3, Apple a étendu les capacités qu’un développeur peut inclure dans une complication WatchKit. 

En outre, de plus en plus des visages de Watch intégrés peuvent désormais inclure des complications et des visages de espions existants qui, déjà pris en charge, peuvent maintenant inclure encore plus de complications.

La nouvelle est également la possibilité pour un utilisateur de balayer rapidement vers la gauche ou vers la droite pour passer en revue toutes les visages de la montre qu’il a installé sur leur Apple Watch. À l’aide de la nouvelle galerie sur l’application iPhone associée à Apple Watch, l’utilisateur peut ajouter et personnaliser de nouvelles visages de Watch et les complications qu’ils peuvent inclure.

En raison de ces nouvelles fonctionnalités, Apple suggère que chaque application sur Apple Watch doit également inclure au moins une complication et, par conséquent, toute l’application Apple Watch Native présente désormais des complications.

Les complications offrent les fonctionnalités suivantes à une application :

- Ils sont très rapides, car ils sont toujours présents sur le visage de la montre.
- Les complications sont fréquemment mises à jour par Watchos. Toutes les applications qui incluent une complication de la face de montre actuellement affichée de l’utilisateur sont mises à jour au moins deux fois par heure.
- Toute application avec une complication de la face de la montre actuellement affichée est conservée en mémoire, ce qui permet de lancer rapidement l’application et d’améliorer la vitesse des réponses de l’application.
- Les complications permettent à l’utilisateur de lancer facilement des fonctionnalités spécifiques dans une application Watchos.

## <a name="glanceable-notification"></a>Notification plus rapide

La notification sur Apple Watch offre un moyen personnalisable et personnalisable d’informer rapidement l’utilisateur des événements ou de nouvelles informations, comme les messages entrants ou l’obtention d’un objectif dans une application de type entraînement.

À l’aide d’une notification, des informations précieuses peuvent être rapidement présentées à l’utilisateur. Dans de nombreux cas, une notification bien conçue peut supprimer la nécessité pour l’utilisateur de lancer réellement l’application.

Nouveauté de Watchos 3, toutes les notifications prennent désormais en charge :

- SpriteKit
- SceneKit
- Vidéo incluse

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interface utilisateur améliorée avec SpriteKit et SceneKit

En règle générale, un développeur peut considérer l’interface utilisateur du jeu quand SpriteKit et SceneKit sont mentionnés. Toutefois, SpriteKit et SceneKit peuvent être utiles pour créer des interfaces utilisateur sans jeu qui incluent des dispositions personnalisées, du contenu et des animations qui ne sont autrement pas possibles dans WatchKit seul.

Par exemple, une notification utilisateur d’une application de partage de photos peut utiliser SpriteKit pour fournir une expérience utilisateur enrichie en incluant l’utilisateur qui a publié l’image, ainsi qu’une image réelle et d’autres informations personnalisées qui enrichissent l’expérience utilisateur.

En outre, SpriteKit et SceneKit peuvent être mélangés avec des éléments d’interface utilisateur WatchKit standard dans la conception de l’interface utilisateur de l’application.

## <a name="simple-navigation"></a>Navigation simple

Watchos 3 présente plusieurs méthodes permettant à un développeur de simplifier la navigation au sein de leurs applications Watchos, telles que la nouvelle [pagination verticale](#vertical-paging), la [prise en charge](#gesture-recognizer-support) de la reconnaissance de mouvement et les fonctionnalités de [rotation de Digital Crown](#digital-crown-rotation) présentées ci-dessus.

Le Digital Crown est propre à la Apple Watch et peut être utilisé de nombreuses façons différentes pour simplifier la navigation. Par exemple, une application de minuteur peut utiliser le Digital Crown pour nettoyer les longueurs de minuteur disponibles.

Les gestes personnalisés peuvent présenter de nouvelles méthodes et uniques permettant à l’utilisateur d’interagir avec une application Watch et peuvent également être utilisées pour simplifier la navigation dans les applications.

Apple suggérera des moyens de combiner toutes les nouvelles fonctionnalités d’interaction rapide ajoutées à Watchos 3 pour présenter des interfaces d’application Watchos riches, faciles et rapides à utiliser.

## <a name="finishing-the-quick-interaction"></a>Terminer l’interaction rapide

Une expérience d’interaction rapide bien conçue donne à l’utilisateur la certitude de supprimer son poignet (et de se détourner avec l’application) lorsqu’il a terminé l’interaction actuelle.

C’est là que se passe spécifiquement un problème lorsque l’application Watch fait un type de connexion réseau ou partage des informations avec son application iPhone associée. Cela peut souvent entraîner l’absence d’un indicateur d’attente pendant que la transaction est en cours, ce qui n’est pas souhaitable pendant une interaction rapide. Prenons l’exemple suivant :

[![](quick-interaction-techniques-images/quick07.png "Diagramme de l’application Watch procédant à une connexion réseau et au partage d’informations avec son application iPhone associée")](quick-interaction-techniques-images/quick07.png#lightbox)

1. L’utilisateur choisit un élément à acheter sur la montre.
2. Ils appuient sur le bouton acheter.
3. L’application démarre la transaction réseau et affiche un indicateur de chargement.
4. Plus tard, la transaction se termine et l’application affiche une conformité d’achat.
5. L’utilisateur abandonne son poignet et se déconnecte de l’application.

À partir du moment où l’utilisateur clique sur le bouton Acheter jusqu’à ce que la transaction soit terminée, le poignet est déclenché en examinant un indicateur de chargement. Pour résoudre ce problème, Apple suggère que le développeur doit présenter des commentaires instantanés à l’utilisateur au lieu d’afficher un indicateur de chargement. 

À l’aide du modèle proposé par Apple, jetez un coup d’œil à la même interaction rapide :

[![](quick-interaction-techniques-images/quick08.png "Diagramme de modèle proposé par apples")](quick-interaction-techniques-images/quick08.png#lightbox)

1. L’utilisateur choisit un élément à acheter sur la montre.
2. Ils appuient sur le bouton acheter.
3. L’application démarre la transaction réseau et affiche un message indiquant que l’achat a démarré avec succès.
4. L’utilisateur abandonne son poignet et se déconnecte de l’application.
5. Lorsque la transaction se termine avec succès plus tard, l’application affiche une notification locale pour informer l’utilisateur qu’un achat a réussi.

Cette fois-ci, dès que l’utilisateur clique sur le bouton acheter, un message s’affiche indiquant que l’achat a commencé, de sorte qu’il peut supprimer en toute confiance son poignet et mettre fin à l’interaction rapide à ce stade. Plus tard, ils sont informés de la réussite ou de l’échec de la transaction dans une notification utilisateur. De cette façon, l’utilisateur interagit uniquement avec l’application pendant les phases « actives » du processus.

Pour les applications qui utilisent la mise en réseau, ils peuvent `NSURLSession` utiliser un arrière-plan pour gérer la communication réseau avec une tâche de téléchargement. Cela permettra à l’application d’être réveillée en arrière-plan pour traiter les informations téléchargées. Pour les applications qui nécessitent un traitement en arrière-plan, utilisez une assertion de tâche en arrière-plan pour gérer le traitement requis.

## <a name="quick-interaction-design-tips"></a>Conseils de conception d’interaction rapide

Étant donné que la longueur souhaitée d’une interaction rapide est de deux secondes ou moins, le développeur doit se concentrer sur la conception des interactions de l’application dès le début du processus de conception. Recherchez les domaines dans lesquels ces interactions peuvent être simplifiées (à l’aide de la technique présentée ci-dessus) et utilisez les nouvelles fonctionnalités de Watchos 3 pour accélérer et réactiver l’application.

Apple suggère les éléments suivants :

- Concentrez-vous sur les interactions rapides en mettant en avant les fonctionnalités les plus utilisées de l’application.
- Utilisez les complications et les notifications utilisateur pour exposer les fonctions et fonctions courantes.
- Créez une interface utilisateur riche et rapide avec SceneKit et SpriteKit.
- Dans la mesure du possible, simplifiez la navigation au sein de l’application.
- Ne jamais faire attendre l’utilisateur, les autoriser à supprimer leur poignet et à le dégager avec l’application dès que possible.

## <a name="summary"></a>Récapitulatif

Cet article a abordé les techniques d’interaction rapide qu’Apple a ajoutées à Watchos 3 et comment les implémenter dans Xamarin. iOS pour Apple Watch.

## <a name="related-links"></a>Liens associés

- [Exemples watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)

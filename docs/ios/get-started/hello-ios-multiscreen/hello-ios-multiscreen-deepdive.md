---
title: Hello, iOS multi-écran - Immersion
description: Ce document examine de manière détaillée l’application Phoneword développée, en particulier model-view-controller, la navigation iOS et d’autres concepts du développement iOS.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: c866e5f4-8154-4342-876e-efa0693d66f5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 9c7572c3d3a785264e9f26c17e74c41ee28e8af6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526524"
---
# <a name="hello-ios-multiscreen--deep-dive"></a>Hello, iOS multiécran - Immersion

Au cours de la procédure pas à pas de démarrage rapide, nous avons généré et exécuté notre première application Xamarin.iOS multi-écran. Il est maintenant temps d’approfondir les notions de navigation et d’architecture iOS.

Dans ce guide, nous présentons le modèle *MVC (Model-View-Controller)* et son rôle dans l’architecture et la navigation iOS.
Ensuite, nous nous intéressons au contrôleur de navigation pour apprendre à l’utiliser afin d’offrir une expérience de navigation naturelle dans iOS.

## <a name="model-view-controller-mvc"></a>Modèle MVC (Model-View-Controller)

Dans le tutoriel [Hello, iOS](~/ios/get-started/hello-ios/index.md), nous avons appris que les applications iOS ont une seule *fenêtre* et que les contrôleurs de vues sont responsables du chargement de leurs *hiérarchies de vues de contenu* dans cette fenêtre. Dans la deuxième procédure pas à pas Phoneword, nous avons ajouté un deuxième écran à notre application et passé certaines données (une liste de numéros de téléphone) entre les deux écrans, comme l’illustre le diagramme ci-dessous :

 [![](hello-ios-multiscreen-deepdive-images/08.png "Ce diagramme illustre le passage de données entre deux écrans")](hello-ios-multiscreen-deepdive-images/08.png#lightbox)

Dans notre exemple, les données étaient collectées dans le premier écran, passées du premier contrôleur de vue au second, puis affichées par le second écran. Cette séparation des écrans, contrôleurs de vues et données suit le modèle *MVC (modèle-vue-contrôleur)* . Dans les sections suivantes, nous présentons les avantages de ce modèle, ses composants et la manière dont nous l’utilisons dans notre application Phoneword.

### <a name="benefits-of-the-mvc-pattern"></a>Avantages du modèle MVC

Le modèle MVC (Model-View-Controller) est un *modèle de conception*, autrement dit une solution d’architecture réutilisable à un problème courant ou cas d’usage dans le code. Le modèle MVC est une architecture destinée aux applications dotées d’une *interface graphique utilisateur (GUI)* . Il attribue aux objets dans l’application un des trois rôles suivants : *modèle* (Model) (données ou logique d’application), *affichage* (View) (interface utilisateur) et *contrôleur* (Controller) (code-behind). Le diagramme ci-dessous illustre les relations entre les trois éléments du modèle MVC et l’utilisateur :

 [![](hello-ios-multiscreen-deepdive-images/00.png "Ce diagramme illustre les relations entre les trois éléments du modèle MVC et l’utilisateur")](hello-ios-multiscreen-deepdive-images/00.png#lightbox)

Le modèle MVC s’avère utile car il fournit une séparation logique entre les différents composants d’une application GUI et facilite la réutilisation de code et d’affichages. Intéressons-nous maintenant de plus près à chacun des trois rôles.

> [!NOTE]
> Le modèle MVC est vaguement analogue à la structure des pages ASP.NET ou des applications WPF. Dans ces exemples, l’affichage (View) est le composant réellement chargé de décrire l’interface utilisateur ; il correspond à la page ASPX (HTML) dans ASP.NET ou au XAML dans une application WPF. Le contrôleur (Controller) est le composant chargé de gérer l’affichage (View), qui correspond au code-behind dans ASP.NET ou WPF.

### <a name="model"></a>Modèle

L’objet Model est généralement une représentation propre à l’application des données à afficher ou à entrer dans l’affichage (View). Il est souvent peu défini : par exemple, dans notre application **Phoneword_iOS**, la liste des numéros de téléphone (représentés sous la forme d’une liste de chaînes) est le modèle. Si nous voulions générer une application multiplateforme, nous pourrions choisir de partager le code **PhonewordTranslator** entre nos applications iOS et Android. Nous pourrions considérer ce code partagé comme le modèle également.

Le modèle MVC ne tient absolument pas compte de la *persistance des données* ni de l’*accès* de l’objet Model. En d’autres termes, le modèle MVC n’a que faire de ce à quoi ressemblent nos données ni de la manière dont elles sont stockées. Il s’intéresse uniquement à la manière dont elles sont *représentées*. Par exemple, nous pourrions choisir de stocker nos données dans une base de données SQL ou de les rendre persistantes dans un mécanisme de stockage cloud ou encore d’utiliser simplement `List<string>`. Pour MVC, seule la représentation des données est incluse dans le modèle.

Dans certains cas, la partie Model du modèle MVC peut être vide. Par exemple, nous pourrions choisir d’ajouter certaines pages statiques à notre application pour expliquer le fonctionnement du convertisseur de numéros de téléphone, les raisons de sa création et la manière de nous contacter pour signaler des bogues. Ces écrans d’application seraient quand même créés à l’aide d’affichages et de contrôleurs, mais ils n’auraient pas de données Model réelles.

> [!NOTE]
> Dans certains documents, la partie Modèle du modèle MVC peut faire référence au backend de toute l’application, pas seulement aux données qui s’affichent dans l’interface utilisateur. Dans ce guide, nous utilisons une interprétation moderne de la partie Model, mais cette distinction n’est pas particulièrement importante.

### <a name="view"></a>Vue

Un affichage (View) est un composant chargé du rendu de l’interface utilisateur. Dans presque toutes les plateformes qui utilisent le modèle MVC, l’interface utilisateur se compose d’une hiérarchie d’affichages. Un affichage dans le modèle MVC peut être perçu comme une hiérarchie d’affichage avec un affichage unique (appelé affichage racine) en haut de la hiérarchie et autant d’affichages enfants (appelés sous-affichages) en dessous. Dans iOS, la hiérarchie de vues de contenu d’un écran correspond au composant Vue du modèle MVC.

### <a name="controller"></a>Contrôleur

L’objet Controller est le composant qui relie tous les éléments ensemble et qui est représenté dans iOS par `UIViewController`. Nous pouvons le considérer comme le code de stockage d’un écran ou d’un ensemble d’affichages. L’objet Controller (ou contrôleur) est chargé d’écouter les demandes émises par l’utilisateur et de retourner la hiérarchie d’affichage appropriée. Il écoute les demandes émises à partir de l’affichage (clics de bouton, entrée de texte, etc.) et effectue le traitement, la modification de l’affichage et le rechargement de l’affichage appropriés. Le contrôleur est également responsable de la création ou de la récupération de l’objet Model à partir du magasin de données existant dans l’application et du remplissage de l’affichage avec ses données.

Les contrôleurs peuvent également gérer d’autres contrôleurs. Par exemple, un contrôleur peut charger un autre contrôleur s’il a besoin d’afficher un autre écran ou il peut gérer une pile de contrôleurs pour surveiller leur ordre et les transitions entre eux. Dans la section suivante, nous allons voir un exemple de contrôleur qui gère d’autres contrôleurs au fur et à mesure que nous présenterons un type spécial de contrôleur de vue iOS appelé *contrôleur de navigation*.

## <a name="navigation-controller"></a>Contrôleur de navigation

Dans l’application Phoneword, nous avons utilisé un contrôleur de navigation pour mieux gérer la navigation entre plusieurs écrans. Le contrôleur de navigation est un `UIViewController` spécialisé représenté par la classe `UINavigationController`. Au lieu de gérer une seule hiérarchie de vues de contenu, le contrôleur de navigation gère d’autres contrôleurs de vues, ainsi que sa propre hiérarchie de vues de contenu spéciale sous la forme d’une barre d’outils de navigation qui inclut un titre, un bouton Précédent et d’autres fonctionnalités facultatives.

Le contrôleur de navigation est courant dans les applications iOS. Il permet de naviguer dans les applications iOS de base telles que l’application **Settings**, comme l’illustre la capture d’écran ci-dessous :

 [![](hello-ios-multiscreen-deepdive-images/01.png "Le contrôleur de navigation permet de naviguer dans les applications iOS telles que l’application Settings illustrée ici")](hello-ios-multiscreen-deepdive-images/01.png#lightbox)

Le contrôleur de navigation remplit trois fonctions principales :

- **Il fournit des crochets pour la navigation vers l’avant** : le contrôleur de navigation utilise une métaphore de navigation hiérarchique où les hiérarchies de vues de contenu sont *envoyées (push)* dans une *pile de navigation*. Vous pouvez considérer qu’une pile de navigation est comme une pile de cartes à jouer, dans laquelle seule la carte du dessus est visible, comme l’illustre le diagramme ci-dessous :  

    [![](hello-ios-multiscreen-deepdive-images/02.png "Ce diagramme illustre la navigation sous forme de pile de cartes")](hello-ios-multiscreen-deepdive-images/02.png#lightbox)


- **Il fournit éventuellement un bouton Précédent** : Quand nous envoyons un nouvel élément dans la pile de navigation, la barre de titre peut afficher automatiquement un *bouton Précédent* qui permet à l’utilisateur de revenir en arrière. En appuyant sur le bouton Précédent, le contrôleur de vue actif est *retiré* de la pile de navigation, et la précédente hiérarchie de vues de contenu se charge dans la fenêtre :  

    [![](hello-ios-multiscreen-deepdive-images/03.png "Ce diagramme illustre le retrait d’une carte de la pile")](hello-ios-multiscreen-deepdive-images/03.png#lightbox)


- **Il fournit une barre de titre** : la partie supérieure du contrôleur de navigation est appelée *barre de titre*. Elle est chargée d’afficher le titre du contrôleur de vue, comme l’illustre le diagramme ci-dessous :  

    [![](hello-ios-multiscreen-deepdive-images/04.png "La barre de titre est chargée d’afficher le titre du contrôleur de vue")](hello-ios-multiscreen-deepdive-images/04.png#lightbox)

### <a name="root-view-controller"></a>Contrôleur de vue racine

Un contrôleur de navigation ne gère pas une hiérarchie de vues de contenu, il n’a donc rien à afficher de lui-même.
Un contrôleur de navigation est plutôt couplé à un *contrôleur de vue racine* :

 [![](hello-ios-multiscreen-deepdive-images/05.png "Un contrôleur de navigation est couplé à un contrôleur de vue racine")](hello-ios-multiscreen-deepdive-images/05.png#lightbox)

Le contrôleur de vue racine représente le premier contrôleur de vue dans la pile du contrôleur de navigation. La hiérarchie de vues de contenu du contrôleur de vue racine est la première hiérarchie de vues de contenu à être chargée dans la fenêtre. Si nous souhaitons mettre toute notre application dans la pile du contrôleur de navigation, nous pouvons déplacer le Segue sans source dans le contrôleur de navigation, et définir le contrôleur de vue de notre premier écran en tant que contrôleur de vue racine, comme nous l’avons fait dans l’application Phoneword :

 [![](hello-ios-multiscreen-deepdive-images/06.png "Le Segue sans source définit le contrôleur de vue des premiers écrans en tant que contrôleur de vue racine")](hello-ios-multiscreen-deepdive-images/06.png#lightbox)

### <a name="additional-navigation-options"></a>Options de navigation supplémentaires

Le contrôleur de navigation est couramment utilisé pour prendre en charge la navigation dans iOS. Toutefois, il ne représente pas la seule option. Un [contrôleur de barre d’onglets](~/ios/user-interface/controls/creating-tabbed-applications.md) peut scinder une application en différentes zones opérationnelles. Un [contrôleur de vue fractionné](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/split_view/use_split_view_to_show_two_controllers) permet de créer des vues maître/détail. La combinaison des contrôleurs de navigation à ces autres paradigmes de navigation fournit de nombreuses manières flexibles de présenter du contenu iOS, et de naviguer dans celui-ci.

## <a name="handling-transitions"></a>Gestion des transitions

Dans la procédure pas à pas Phoneword, nous avons traité la transition entre les deux contrôleurs de vues de deux manières différentes : d’abord avec un Segue de Storyboard, puis par programmation. Examinons ces deux options de plus près.

### <a name="prepareforsegue"></a>PrepareForSegue

Quand nous ajoutons un Segue avec une action **Afficher** au Storyboard, nous demandons à iOS d’envoyer (push) le deuxième contrôleur de vue dans la pile du contrôleur de navigation :

 [![](hello-ios-multiscreen-deepdive-images/09.png "Définition du type de segue à partir d’une liste déroulante")](hello-ios-multiscreen-deepdive-images/09.png#lightbox)

L’ajout d’un Segue au Storyboard suffit à créer une transition simple entre les écrans. Si nous souhaitons passer des données entre les contrôleurs de vues, nous devons remplacer la méthode `PrepareForSegue` et gérer les données nous-mêmes :

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);
    ...
}
```

iOS appelle `PrepareForSegue` juste avant que la transition ne se produise et passe le Segue que nous avons créé dans le Storyboard à la méthode.
À ce stade, nous devons définir manuellement le contrôleur de vue de destination du Segue. Le code suivant obtient un handle vers le contrôleur de vue de destination et le caste dans la classe appropriée, CallHistoryController, dans le cas présent :

```csharp
CallHistoryController callHistoryController = segue.DestinationViewController as CallHistoryController;
```

Enfin, nous passons la liste de numéros de téléphone (la partie Model) depuis `ViewController` vers `CallHistoryController` en définissant la propriété `PhoneHistory` du `CallHistoryController` sur la liste des numéros de téléphone composés :

```csharp
callHistoryController.PhoneNumbers = PhoneNumbers;
```

Le code complet permettant de passer des données à l’aide d’un Segue est le suivant :

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController as CallHistoryController;

    if (callHistoryController != null) {
         callHistoryController.PhoneNumbers = PhoneNumbers;
    }
 }
```

### <a name="navigation-without-segues"></a>Navigation sans Segues

Le processus de transition entre le premier contrôleur de vue et le deuxième dans le code est le même qu’avec un Segue, mais plusieurs étapes doivent être effectuées manuellement.
Tout d’abord, nous utilisons `this.NavigationController` pour obtenir une référence au contrôleur de navigation dont la pile est celle où nous nous trouvons. Nous utilisons ensuite la méthode `PushViewController` du contrôleur de navigation pour envoyer manuellement le contrôleur de vue suivant dans la pile, en passant le contrôleur de vue et une option d’animation de la transition (nous affectons la valeur `true`).

Le code suivant gère la transition entre l’écran Phoneword et l’écran de l’historique des appels :

```csharp
this.NavigationController.PushViewController (callHistory, true);
```

Pour pouvoir effectuer la transition vers le contrôleur de vue suivant, nous devons d’abord l’instancier manuellement à partir du Storyboard en appelant `this.Storyboard.InstantiateViewController` et en passant l’ID de Storyboard de `CallHistoryController` :

```csharp
CallHistoryController callHistory =
this.Storyboard.InstantiateViewController
("CallHistoryController") as CallHistoryController;
```

Enfin, nous passons la liste de numéros de téléphone (la partie Model) depuis `ViewController` vers `CallHistoryController` en définissant la propriété `PhoneHistory` du `CallHistoryController` sur la liste des numéros de téléphone composés, tout comme nous l’avons fait quand nous avons géré la transition avec un Segue :

```csharp
callHistory.PhoneNumbers = PhoneNumbers;
```

Le code complet pour la transition par programmation est le suivant :

```csharp
CallHistoryButton.TouchUpInside += (object sender, EventArgs e) => {
    // Launches a new instance of CallHistoryController
    CallHistoryController callHistory = this.Storyboard.InstantiateViewController ("CallHistoryController") as CallHistoryController;
    if (callHistory != null) {
     callHistory.PhoneNumbers = PhoneNumbers;
     this.NavigationController.PushViewController (callHistory, true);
    }
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Autres concepts introduits dans Phoneword

L’application Phoneword a introduit plusieurs concepts qui ne sont pas traités dans ce guide. Ces concepts sont notamment :

- **Création automatique de contrôleurs de vues** : quand nous entrons un nom de classe pour le contrôleur de vue dans le **Panneau Propriétés**, le concepteur iOS vérifie si cette classe existe, puis génère la classe de support du contrôleur de vue pour nous. Pour plus d’informations sur ce concept et d’autres fonctionnalités du concepteur iOS, reportez-vous au guide de [présentation du concepteur iOS](~/ios/user-interface/designer/introduction.md).
- **Contrôleur de vue de tableau** : `CallHistoryController` est un contrôleur de vue de tableau. Un contrôleur de vue de tableau contient une vue de tableau, l’outil de disposition et d’affichage de données le plus courant dans iOS. Les tables dépassent le cadre du présent guide. Pour plus d’informations sur les contrôleurs de vues de tableau, consultez le guide d’[utilisation des tableaux et des cellules](~/ios/user-interface/controls/tables/index.md).
- **ID de Storyboard** : la définition de l’ID de Storyboard crée une classe de contrôleur de vue en Objective-C, qui contient le code-behind du contrôleur de vue dans le Storyboard. Nous utilisons l’ID de Storyboard pour trouver la classe Objective-C et instancier le contrôleur de vue dans le Storyboard. Pour plus d’informations sur les ID de Storyboard, reportez-vous au guide de [présentation des Storyboards](~/ios/user-interface/storyboards/index.md).

## <a name="summary"></a>Récapitulatif

Félicitations ! Vous avez terminé votre première application iOS multi-écran.

Dans ce guide, nous avons présenté le modèle MVC et nous l’avons utilisé pour créer une application multi-écran. Nous avons également examiné les contrôleurs de navigation et leur rôle dans la navigation iOS. Vous avez maintenant acquis les bases solides dont vous avez besoin pour commencer à développer vos propres applications Xamarin.iOS.

Maintenant, apprenons à générer des applications multiplateforme avec Xamarin à l’aide des guides de [présentation du développement mobile](~/cross-platform/get-started/introduction-to-mobile-development.md) et de [génération d’applications multiplateforme](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md).

## <a name="related-links"></a>Liens connexes

- [Hello, iOS (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)
- [Lignes directrices de l’interface utilisateur iOS](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [Portail de provisionnement iOS](https://developer.apple.com/ios/manage/overview/index.action)

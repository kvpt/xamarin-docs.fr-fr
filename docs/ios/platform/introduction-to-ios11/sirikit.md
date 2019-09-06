---
title: Mises à jour de SiriKit dans iOS 11
description: Ce document décrit comment utiliser SiriKit dans iOS 11. En particulier, il examine comment utiliser des tâches et des notes et comment fournir des noms alternatifs pour une application.
ms.prod: xamarin
ms.assetid: 8F75300B-B591-42ED-9D17-001992A5C381
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/07/2017
ms.openlocfilehash: d4fab992121ad6a2b272012f7249df5ed8427513
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286738"
---
# <a name="sirikit-updates-in-ios-11"></a>Mises à jour de SiriKit dans iOS 11

SiriKit a été introduit dans iOS 10, avec un certain nombre de domaines de service (y compris les entraînements, la réservation des modifications et les appels). Reportez-vous à la [section SiriKit](~/ios/platform/sirikit/index.md) pour les concepts SiriKit et à la manière d’implémenter SiriKit dans votre application.

![Démonstration de la liste des tâches Siri](sirikit-images/sirikit.png)

SiriKit dans iOS 11 ajoute ces nouveaux domaines d’intention nouveaux et mis à jour :

- [**Listes et notes**](#listsnotes) – nouveautés ! Fournit une API pour que les applications traitent les tâches et les notes.
- **Codes visuels** – nouveau ! Siri peut afficher des codes QR pour partager des informations de contact ou participer à des transactions de paiement.
- **Paiements** : ajout d’intentions de recherche et de transfert pour les interactions de paiement.
- **Réservation de réservation** : ajout de la définition d’annulation et des intentions de commentaires.

Quelques autres nouvelles fonctions :

- [**Autres noms d’applications**](#alternativenames) : fournit des alias qui aident les clients à indiquer à Siri de cibler votre application en proposant d’autres noms/prononciations.
- **Démarrage des entraînements** : offre la possibilité de démarrer une entraînement en arrière-plan.

Certaines de ces fonctionnalités sont expliquées ci-dessous. Pour plus d’informations sur les autres, reportez-vous à la [documentation SiriKit d’Apple](https://developer.apple.com/documentation/sirikit).

<a name="listsnotes" />

## <a name="lists-and-notes"></a>Listes et notes

Les nouvelles listes et le domaine notes fournissent une API pour que les applications traitent les tâches et les notes via des demandes vocales Siri.

**Tâches**

- Avoir un titre et un état d’achèvement.
- Si vous le souhaitez, vous pouvez inclure une échéance et un emplacement.

**Notes**

- Avoir un titre et un champ de contenu.

Les tâches et les notes peuvent être organisées en groupes. Le reste de cette section décrit comment implémenter ce nouveau domaine avec SiriKit, à l’aide de l' [exemple TasksNotes SiriKit](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample).

### <a name="how-to-process-a-sirikit-request"></a>Comment traiter une requête SiriKit

Traitez une demande SiriKit en procédant comme suit :

1. **Résoudre** : valider les paramètres et demander des informations supplémentaires à l’utilisateur (le cas échéant).
2. **Confirmer** : validation finale et vérification que la demande peut être traitée.
3. **Handle** : effectue l’opération (mise à jour des données ou exécution d’opérations réseau).

Les deux premières étapes sont facultatives (bien qu’encouragées) et la dernière étape est requise.
La [section SiriKit](~/ios/platform/sirikit/index.md)contient des instructions plus détaillées.

### <a name="resolve-and-confirm-methods"></a>Résoudre et confirmer les méthodes

Ces méthodes facultatives permettent à votre code d’effectuer la validation, de sélectionner les valeurs par défaut ou de demander des informations supplémentaires à l’utilisateur.

Par exemple, pour l' `IINCreateTaskListIntent` interface, la méthode requise est. `HandleCreateTaskList` Quatre méthodes facultatives permettent de mieux contrôler l’interaction Siri :

- `ResolveTitle`: Valide le titre, définit un titre par défaut (le cas échéant) ou signale que les données ne sont pas requises.
- `ResolveTaskTitles`: Valide la liste des tâches parlées par l’utilisateur.
- `ResolveGroupName`: Valide le nom de groupe, choisit un groupe par défaut ou signale que les données ne sont pas requises.
- `ConfirmCreateTaskList`: Valide le fait que votre code puisse exécuter l’opération demandée, mais ne l’exécute pas (seules `Handle*` les méthodes doivent modifier les données).

### <a name="handle-the-intent"></a>Gérer l’intention

Il existe six intentions dans le domaine des listes et des notes, trois pour les tâches et trois pour les notes.
Les méthodes que vous devez implémenter pour gérer ces intentions sont les suivantes :

- Pour les tâches :
  - `HandleAddTasks`
  - `HandleCreateTaskList`
  - `HandleSetTaskAttribute`
- Pour les remarques :
  - `HandleCreateNote`
  - `HandleAppendToNote`
  - `HandleSearchForNotebookItems`

Chaque méthode a un type d’intention spécifique qui lui est transmis, qui contient toutes les informations que Siri a analysées à partir de la demande de l’utilisateur `Resolve*` ( `Confirm*` et éventuellement mise à jour dans les méthodes et).
Votre application doit analyser les données fournies, puis effectuer des actions pour stocker ou traiter des données, et retourner un résultat que Siri parle et montre à l’utilisateur.

### <a name="response-codes"></a>Codes de réponse

Les méthodes `Handle*` obligatoires et `Confirm*` facultatives indiquent un code de réponse en définissant une valeur sur l’objet qu’elles passent à leur gestionnaire d’achèvement. Les réponses proviennent de `INCreateTaskListIntentResponseCode` l’énumération :

- `Ready`: Retourne pendant la phase de confirmation (c’est-à-dire à partir `Confirm*` d’une méthode, mais pas `Handle*` d’une méthode).
- `InProgress`: Utilisé pour les tâches de longue durée (par exemple, une opération de réseau/serveur).
- `Success`: Répond avec les détails de l’opération réussie (uniquement à partir `Handle*` d’une méthode).
- `Failure`: Indique qu’une erreur s’est produite et que l’opération n’a pas pu être effectuée.
- `RequiringAppLaunch`: Ne peut pas être traité par l’intention, mais l’opération est possible dans l’application.
- `Unspecified`– Ne pas utiliser : un message d’erreur s’affiche pour l’utilisateur.

En savoir plus sur ces méthodes et les réponses dans les [listes SiriKit et la documentation des notes](https://developer.apple.com/documentation/sirikit/lists_and_notes)d’Apple.

### <a name="implementing-lists-and-notes"></a>Implémentation de listes et de notes

L' [exemple TasksNotes SiriKit](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample) a été créé à l’aide de la procédure suivante pour ajouter la prise en charge de SiriKit à une application iOS vide.

Tout d’abord, pour ajouter la prise en charge de SiriKit, procédez comme suit pour votre application iOS :

1. Tick **SiriKit** dans **Entitlements. plist**.
2. Ajoutez la clé de description de l’utilisation de la **confidentialité – Siri** à **info. plist**, ainsi qu’un message pour vos clients.
3. Appelez la `INPreferences.RequestSiriAuthorization` méthode dans l’application pour inviter l’utilisateur à autoriser les interactions Siri.
4. Ajoutez SiriKit à votre ID d’application sur le portail des développeurs, puis recréez vos profils de provisionnement pour inclure le nouveau droit.

Ajoutez ensuite un nouveau projet d’extension à votre application pour gérer les demandes Siri :

1. Cliquez avec le bouton droit sur votre solution et choisissez **ajouter > ajouter un nouveau projet...** .
2. Choisissez l' **extension de > iOS > modèle d’extension intentions** .
3. Deux nouveaux projets seront ajoutés : Intention et IntentUI. La personnalisation de l’interface utilisateur est facultative. par conséquent, l’exemple comprend uniquement du code dans le projet d' **intention** .

Le projet d’extension est l’emplacement où toutes les demandes SiriKit seront traitées. En tant qu’extension distincte, elle n’a pas automatiquement de moyen de communiquer avec votre application principale, ce qui est généralement résolu en implémentant le stockage de fichiers partagés à l’aide de groupes d’applications.

#### <a name="configure-the-intenthandler"></a>Configurer IntentHandler

La `IntentHandler` classe est le point d’entrée pour les demandes Siri : chaque intention est passée à `GetHandler` la méthode, qui retourne un objet capable de gérer la requête.

Le code ci-dessous illustre une implémentation simple :

```csharp
[Register("IntentHandler")]
public partial class IntentHandler : INExtension, IINNotebookDomainHandling
{
  protected IntentHandler(IntPtr handle) : base(handle)
  {}
  public override NSObject GetHandler(INIntent intent)
  {
    // This is the default implementation.  If you want different objects to handle different intents,
    // you can override this and return the handler you want for that particular intent.
    return this;
  }
  // add intent handlers here!
}
```

La classe doit hériter `INExtension`de, et comme l’exemple va gérer des listes et des intentions de notes, elle `IINNotebookDomainHandling`implémente également.

> [!NOTE]
> - Il existe une Convention dans .net pour les interfaces qui doivent être précédées `I`d’un capital, auquel Xamarin adhère lors de la liaison de protocoles à partir du kit de développement logiciel (SDK) iOS.
> - Xamarin conserve également les noms de types d’iOS et Apple utilise les deux premiers caractères dans les noms de types pour refléter l’infrastructure à laquelle un type appartient.
> - Pour le `Intents` Framework, les types ont pour préfixe (par `IN*` exemple, `INExtension`), mais ce ne sont _pas_ des interfaces.
> - Il suit également que les protocoles (qui deviennent des C#interfaces) finissent par deux `I`, comme. `IINAddTasksIntentHandling`

#### <a name="handling-intents"></a>Gestion des intentions

Chaque intention (ajouter une tâche, définir l’attribut de tâche, etc.) est implémentée dans une méthode unique semblable à celle illustrée ci-dessous. La méthode doit effectuer trois fonctions principales :

1. **Traiter l’intention** : les données analysées par Siri sont rendues disponibles dans `intent` un objet spécifique au type d’intention. Votre application a peut-être validé les données `Resolve*` à l’aide de méthodes facultatives.
2. **Valider et mettre à jour le magasin de données** : enregistrer les données dans le système de fichiers (à l’aide de groupes d’applications afin que l’application iOS principale puisse également y accéder) ou via une demande réseau.
3. **Fournir une réponse** : utilisez `completion` le gestionnaire pour renvoyer une réponse à Siri à l’utilisateur :

```csharp
public void HandleCreateTaskList(INCreateTaskListIntent intent, Action<INCreateTaskListIntentResponse> completion)
{
  var list = TaskList.FromIntent(intent);
  // TODO: have to create the list and tasks... in your app data store
  var response = new INCreateTaskListIntentResponse(INCreateTaskListIntentResponseCode.Success, null)
  {
    CreatedTaskList = list
  };
  completion(response);
}
```

Notez que `null` est passé comme deuxième paramètre à la réponse : il s’agit du paramètre d’activité utilisateur et, lorsqu’il n’est pas fourni, une valeur par défaut est utilisée.
Vous pouvez définir un type d’activité personnalisée tant que votre application iOS la prend en charge `NSUserActivityTypes` via la clé dans **info. plist**. Vous pouvez ensuite gérer ce cas lorsque votre application est ouverte et effectuer des opérations spécifiques (telles que l’ouverture d’un contrôleur d’affichage pertinent et le chargement des données à partir de l’opération Siri).

L’exemple hardcodes également le `Success` résultat, mais dans les scénarios réels, des rapports d’erreurs corrects doivent être ajoutés.

### <a name="test-phrases"></a>Expressions de test

Les phrases de test suivantes doivent fonctionner dans l’exemple d’application :

- « Créer une liste d’épiceries avec des pommes, des bananes et des poires en TasksNotes »
- « Ajouter une tâche WWDC dans TasksNotes »
- « Ajouter la tâche WWDC à la liste de formation dans TasksNotes »
- « Mark attend WWDC As completed in TasksNotes »
- « Dans TasksNotes, me le rappeler d’acheter un iPhone quand je me trouve »
- « Mark acheter iPhone comme terminé dans TasksNotes »
- « Me rappeler de sortir chez 8 h. TasksNotes »

![Créer un exemple de liste](sirikit-images/createtasklist-sml.png) ![Exemple définir la tâche en tant que complet](sirikit-images/settaskattribute-sml.png)

> [!NOTE]
> Le simulateur iOS 11 prend en charge les tests avec Siri (contrairement aux versions antérieures).
>
> Si vous testez sur des appareils réels, n’oubliez pas de configurer votre ID d’application et les profils de provisionnement pour la prise en charge de SiriKit.

<a name="alternativenames" />

## <a name="alternative-names"></a>Autres noms

Cette nouvelle fonctionnalité iOS 11 signifie que vous pouvez configurer d’autres noms pour votre application afin d’aider les utilisateurs à les déclencher correctement avec Siri. Ajoutez les clés suivantes au fichier **info. plist** du projet d’application iOS :

![Info. plist, avec des clés et des valeurs de nom d’application de remplacement](sirikit-images/alternative-names.png)

Avec les autres noms d’application définis, les expressions suivantes fonctionnent également pour l’exemple d’application (qui est en fait nommé **TasksNotes**) :

- « Créer une liste d’épiceries avec des pommes, des bananes et des poires en _MonkeyNotes_»
- « Ajouter une tâche WWDC dans _MonkeyTodo_»


## <a name="troubleshooting"></a>Résolution des problèmes

Certaines erreurs que vous pouvez rencontrer lors de l’exécution de l’exemple ou de l’ajout de SiriKit à vos propres applications :

### <a name="nsinternalinconsistencyexception"></a>NSInternalInconsistencyException

_Exception objective-C levée.  Nom : Raison NSInternalInconsistencyException : Utilisation de la classe < inpréférences : 0x60400082ff00 > à partir d’une application requiert le droit com. Apple. Developer. Siri. Avez-vous activé la fonctionnalité Siri dans votre projet XCode ?_

- SiriKit est cochée dans les **droits. plist**.
- **Habilitations. plist** est configuré dans les **Options de projet > générer > signature d’offre groupée iOS**.

  [![Options de projet avec les habilitations correctement définies](sirikit-images/set-entitlements-sml.png)](sirikit-images/set-entitlements.png#lightbox)

- (pour le déploiement d’appareils) L’ID d’application a SiriKit activé et le profil de provisionnement a été téléchargé.


## <a name="related-links"></a>Liens associés

- [SiriKit (Apple)](https://developer.apple.com/documentation/sirikit)
- [Exemple TasksNotes SiriKit](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-sirikitsample)
- [Nouveautés de SiriKit (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/214/)

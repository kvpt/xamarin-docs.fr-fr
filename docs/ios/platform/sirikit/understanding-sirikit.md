---
title: Présentation des concepts de SiriKit
description: Ce document décrit les concepts clés nécessaires à l’utilisation de SiriKit dans une application Xamarin. iOS. Par exemple, il aborde les intentions et les extensions d’interface utilisateur, les autorisations SiriKit, la conception d’une expérience remarquable, et bien plus encore.
ms.prod: xamarin
ms.assetid: 99EC5C1E-484F-4371-8555-58C9F60DE37F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: b9e8ad848204f7db785327093cd4b7ed9aa6de81
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654073"
---
# <a name="understanding-sirikit-concepts"></a>Présentation des concepts de SiriKit

_Cet article aborde les concepts clés qui seront nécessaires à l’utilisation de SiriKit dans une application Xamarin. iOS._


Nouveauté d’iOS 10, SiriKit permet à une application Xamarin. iOS de fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS. Cette fonctionnalité est fournie dans une ou plusieurs extensions d’application à l’aide des nouvelles infrastructures d' **interface utilisateur** **intentions** et intentions.

SiriKit permet à une application iOS de fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS à l’aide d’extensions d’application et des nouvelles infrastructures d' **interface utilisateur** **intentions** et intentions.

Siri fonctionne avec le concept de **domaines**, de groupes d’actions connues pour les tâches associées. Chaque interaction avec Siri doit être comprise dans l’un de ses domaines de service connus, comme suit:

- Appel audio ou vidéo.
- Réservation d’une définition.
- Gestion des entraînements.
- Messag.
- Recherche de photos.
- Envoi ou réception de paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant l’un des services de l’extension d’application, SiriKit envoie à l’extension un objet d' **intention** qui décrit la demande de l’utilisateur ainsi que toutes les données de prise en charge. L’extension d’application génère ensuite l’objet de **réponse** approprié pour l' **intention**donnée, en détaillant la manière dont l’extension peut traiter la demande.

## <a name="the-intents-and-intents-ui-extensions"></a>Les intentions et les extensions d’interface utilisateur des intentions

Siri et l’application Maps interagissent avec les services de l’application par le biais de deux types d’extensions d’application différents:

- **Extension intentions** : fournit Siri et mappe avec le contenu de l’application et effectue les tâches requises pour exécuter les intentions prises en charge.
- **Extension d’interface utilisateur** Intents: fournit une interface utilisateur personnalisée qui sera affichée pour le contenu de l’application dans Siri ou Maps.

L’application doit fournir une extension intentions pour prendre en charge SiriKit et il est chargé de fournir des informations que Siri et Maps peuvent présenter à l’utilisateur et pour gérer les intentions.

La création d’une extension d’interface utilisateur intentions est facultative, car Siri gère généralement toutes les interactions de l’utilisateur et dispose d’une interface utilisateur standard intégrée pour la présentation des informations dans chacun des domaines pris en charge. En fournissant une extension d’interface utilisateur Intent, l’application peut utiliser l’infrastructure d' **interface utilisateur intentionnelle** pour présenter une interface utilisateur riche et personnalisée avec la personnalisation de l’application et des informations supplémentaires.

## <a name="siri-and-the-maps-app-role"></a>Siri et le rôle d’application Maps

Les requêtes orales de l’utilisateur sont traitées en langage et analysées sémantiquement par Siri, ce qui transforme ces demandes en intentions actionnables que les extensions d’intention peuvent gérer.

Maps utilise les extensions d’intention de l’application pour afficher des informations dans l’interface Map en réponse aux actions de l’utilisateur. Par exemple, demander des restaurants à proximité ou obtenir les revues du restaurant de l’application.

Siri et Maps gèrent toutes les interactions de l’utilisateur et affichent les résultats à l’aide de l’interface système standard. Le rôle extensions d’application sert principalement à fournir les données qui sont affichées. Si vous le souhaitez, l’application peut fournir une extension d’interface utilisateur intentions et présenter une interface utilisateur personnalisée pour améliorer l’interface système par défaut.

## <a name="interacting-with-siri-via-sirikit"></a>Interaction avec Siri via SiriKit

Cette section présente une vue d’ensemble de la façon dont SiriKit permet à l’utilisateur d’interagir avec l’application à l’aide de Siri. Pour les besoins de cet exemple, nous allons utiliser l’application factice MonkeyChat:

[![](understanding-sirikit-images/monkeychat01.png "Icône MonkeyChat")](understanding-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat conserve son propre livre de contacts des amis de l’utilisateur, chacun associé à un nom d’écran (comme Bobo, par exemple) et permet à l’utilisateur d’envoyer des conversations textuelles à chaque ami en fonction de leur nom d’écran.

Il existe de nombreuses façons pour l’utilisateur de lancer une interaction avec l’application, car différentes personnes peuvent effectuer la même requête dans de nombreuses formes différentes.

Par exemple, si l’utilisateur souhaite envoyer un message à son Bobo Friend, il peut avoir la conversation suivante avec Siri:

_Utilisateur Bonjour Siri, envoyez un message MonkeyChat._<br />
_Siri À qui?_<br />
_Utilisateur Bobo._<br />
_Siri Que voulez-vous bobo?_<br />
_Utilisateur Veuillez envoyer plus de bananes._<br />

Une autre personne peut effectuer la même demande avec une conversation différente:

_Utilisateur Envoyez un message à bobo sur MonkeyChat._<br />
_Siri Que voulez-vous bobo?_<br />
_Utilisateur Veuillez envoyer plus de bananes._<br />

Et un autre utilisateur peut faire une demande encore plus rapide:

_Utilisateur MonkeyChat Bobo veuillez envoyer plus de bananes._<br />
_Siri OK, envoi d’un message Veuillez envoyer plus de bananes à bobo sur Monkeychat._<br />

Ou même effectuer la même requête dans une autre langue:

_Utilisateur MonkeyChat Bobo S’il. plaît d’envoi plus de bananes._<br />
_Siri Oui, envoi message S’il, plaît envoyer plus de bananes à bobo sur Monkeychat._<br />

Toutefois, un autre utilisateur peut être très détaillé dans sa conversation:

_Utilisateur Bonjour Siri, pouvez-vous m’en faire un plaisir et lancer l’application MonkeyChat pour envoyer un texte avec le message Veuillez envoyer des bananes supplémentaires._<br />
_Siri À qui?_<br />
_Utilisateur Ma meilleure Bobo PAL._<br />

En outre, il existe de nombreuses façons pour Siri de répondre à une demande, en fonction de la façon dont la demande a été effectuée:

- **En maintenant le bouton de démarrage** , Siri fournit plus de réponses visuelles avec un feedback verbal limité.
- **Par «Hey Siri»** -Siri sera plus verbal et fournira moins de réponses visuelles.

Siri est également réglé pour répondre aux besoins d’accessibilité de l’utilisateur et interagit et répond en fonction de ces besoins.

Quelle que soit la façon dont une requête est formulée ou la manière dont Siri répond à la demande, Siri gère la conversation avec l’utilisateur et l’application (via ses extensions) fournit les fonctionnalités.

Lorsque l’utilisateur effectue une requête verbale de Siri, il s’agit des étapes que Siri suivra:

[![](understanding-sirikit-images/monkeychat02.png "Les étapes que Siri suivra")](understanding-sirikit-images/monkeychat02.png#lightbox)

1. Tout d’abord, Siri prend le son de la **parole** de l’utilisateur et le convertit en texte.
2. Ensuite, le texte est converti en une **intention**, une représentation structurée de la demande de l’utilisateur.
3. En fonction de l’intention, Siri effectue une **action** pour effectuer la demande de l’utilisateur.
4. Enfin, Siri présente des **réponses** (visuelles et orales) à l’utilisateur en fonction de l’action effectuée.

L’application peut participer à la conversation de l’utilisateur avec Siri de trois manières principales:

[![](understanding-sirikit-images/monkeychat03.png "Les trois principales façons dont l’application peut participer à la conversation des utilisateurs avec Siri")](understanding-sirikit-images/monkeychat03.png#lightbox)

1. **Vocabulaire** : il s’agit de la façon dont l’application indique à Siri les mots qu’elle doit savoir pour interagir avec elle.
2. **Logique d’application** : il s’agit des actions et des réponses que l’application prendra en fonction des intentions fournies.
3. **Interface utilisateur** : il s’agit de l’interface utilisateur personnalisée facultative dans laquelle l’application peut fournir ses réponses.

### <a name="example"></a>Exemple

Compte tenu des informations ci-dessus, examinez comment la conversation suivante interagira avec l’application MonkeyChat:

_Utilisateur Bonjour Siri, envoyez un message à bobo sur MonkeyChat._<br />
_Siri Que voulez-vous bobo?_<br />
_Utilisateur Veuillez envoyer plus de bananes._<br />

Le premier rôle que l’application prend dans la conversation est d’aider Siri à comprendre la parole de l’utilisateur:

[![](understanding-sirikit-images/monkeychat04.png "Aider Siri à comprendre la parole des utilisateurs")](understanding-sirikit-images/monkeychat04.png#lightbox)

Siri n’a pas le nom «Bobo» dans sa base de données, mais l’application a partagé ces informations avec Siri via son vocabulaire. L’application aide également Siri à reconnaître que Bobo est un destinataire, puisqu’il l’a spécifié à Siri en tant que *contact*.

Siri sait qu’un plus grand nombre d’éléments est nécessaire pour envoyer un message qu’un simple destinataire. il vérifie donc rapidement l’extension de l’application pour voir si un message nécessite du contenu. Comme MonkeyChat le fait, Siri répondra à l’utilisateur avec la question: *«Que voulez-vous bobo?»*

Dans l’exemple ci-dessus, l’utilisateur a répondu *«veuillez envoyer plus de bananes»* , que Siri regroupe dans un **but**structuré:

[![](understanding-sirikit-images/monkeychat05.png "Siri regroupe la réponse de l’utilisateur dans un but structuré")](understanding-sirikit-images/monkeychat05.png#lightbox)

L’intention structurée contiendra les informations suivantes:

- **Domain** Messages
- **Intention:** SendMessage
- **Recev** Bobo
- **Contenu :** Veuillez envoyer plus de bananes

Chaque domaine a un ensemble d' *actions* qui peuvent être effectuées au sein de celui-ci et sur la base du domaine et de l’action, zéro à plusieurs paramètres peuvent être inclus dans l’intention envoyée à l’application.

L’intention est ensuite envoyée à l’extension d’application pour traitement. En raison du traitement de l’intention, l’application génère un **IntentResponse** qui est regroupé avec l’intention et inclut des paramètres décrivant ce que l’application a fait avec l’intention.

Chaque IntentResponse inclut également un **Code de réponse** qui indique à Siri si l’application a pu terminer la demande ou non. Certains domaines ont des codes de réponse d’erreur très spécifiques qui peuvent également être envoyés.

Enfin, le IntentResponse inclut un ( `NSUserActivity` comme ceux utilisés pour prendre en charge la main). Le `NSUserActivity` sera utilisé pour lancer l’application si la réponse en oblige à sortir l’environnement Siri et à entrer l’application pour la terminer. 

Siri crée automatiquement un approprié `NSUserActivity` pour démarrer l’application et récupérer l’emplacement où l’utilisateur s’est arrêté dans l’environnement Siri. Toutefois, l’application peut fournir ses propres `NSUserActivity` informations personnalisées, si nécessaire.

Une fois que l’application a traité l’intention et retourné une réponse à Siri, elle présente ensuite les résultats à l’utilisateur (à la fois oralement et visuellement):

[![](understanding-sirikit-images/monkeychat06.png "Les résultats présentés à l’utilisateur à la fois oralement et visuellement")](understanding-sirikit-images/monkeychat06.png#lightbox)

Siri dispose de plusieurs interfaces utilisateur de réponse intégrées pour chacun des domaines disponibles pour l’application. Toutefois, étant donné que MonkeyChat a fourni une extension d’interface utilisateur Intent facultative, il est utilisé pour présenter les résultats de la conversation à l’utilisateur dans l’exemple ci-dessus.

## <a name="the-intent-lifecycle"></a>Cycle de vie de l’intention

Il existe trois tâches principales que l’extension d’application devra effectuer lors du traitement des intentions:

[![](understanding-sirikit-images/monkeychat07.png "Cycle de vie de l’intention")](understanding-sirikit-images/monkeychat07.png#lightbox)

1. L’application doit **résoudre** chaque paramètre sur un événement. Par conséquent, l’application appellera plusieurs fois la résolution (une fois par paramètre) et parfois plusieurs fois sur le même paramètre jusqu’à ce que l’application et l’utilisateur s’accordent sur ce qui est demandé.
2. L’application doit **confirmer** qu’elle peut gérer l’intention demandée et indiquer à Siri le résultat attendu.
3. Enfin, l’application doit **gérer** l’intention et effectuer les étapes pour obtenir le résultat demandé.

### <a name="the-resolve-stage"></a>Étape de résolution

L’étape de résolution permet à Siri de comprendre les valeurs fournies par l’utilisateur et de s’assurer que l’utilisateur signifiait vraiment ce qui se passerait lorsque l’intention est traitée par l’application. 

Cette étape permet également à l’application d’influencer le comportement de Siri pendant la conversation avec l’utilisateur. Pour ce faire, l’application fournit une **réponse de résolution**. Il existe un certain nombre de réponses prédéfinies pour les différents types de données que Siri comprend.

La réponse de résolution la plus courante de l’application est **réussite**, ce qui signifie que l’application a mis en correspondance les données spécifiques d’un paramètre (par exemple, le nom de l’écran de l’utilisateur) avec une information connue.

Il peut arriver que l’application doive confirmer qu’une demande donnée correspond à la bonne information connue. Dans ce cas, il envoie une réponse **ConfirmationRequired** pour demander à l’utilisateur de répondre à une question de type oui ou non, par exemple *«Envoyer un message à Bobo?»* .

Il peut y avoir d’autres cas où l’application demandera à l’utilisateur de choisir parmi une brève liste d’options. Dans ce cas, l’application fournit une réponse de **désambiguïsation** avec une liste de deux à dix options pour l’utilisateur, par exemple: 

```csharp
Who do you want to message?

* Bobo the Great
* Bobo Jr.
* Little Bobo
```

Siri gère l’utilisateur qui effectue la sélection, oralement ou en interagissant avec l’interface utilisateur Siri, et le résultat est renvoyé à l’application.

Dans d’autres cas, il se peut que l’application ne dispose pas de suffisamment d’informations pour résoudre le paramètre ou qu’il peut y avoir trop de correspondances à résoudre à l’aide de la désambiguïsation (par exemple, 80 utilisateurs avec Bobo dans leur nom). Dans ce cas, l’application enverra une réponse **NeedsMoreDetails** et Siri invitera l’utilisateur à être plus spécifique.

Si l’utilisateur n’a pas fourni de valeur requise pour traiter l’intention, il peut envoyer une réponse **NeedsValue** pour que Siri invite l’utilisateur à entrer la valeur.

Si l’application ne prend pas en charge une valeur fournie par l’utilisateur pour un paramètre spécifique, elle peut envoyer la réponse **UnsupportedWithReason** pour fournir une raison pour laquelle la valeur n’était pas prise en charge. Siri invite ensuite l’utilisateur à entrer une valeur complètement nouvelle et lui a donné la raison pour laquelle il est requis.

Enfin, utilisez la réponse **NotRequired** pour indiquer à Siri que l’application n’a pas besoin d’une valeur pour un paramètre donné. Si l’utilisateur en fournit un tout de même, il sera simplement ignoré par Siri.

### <a name="the-confirm-stage"></a>Étape de confirmation

L’étape de confirmation a deux objectifs:

- Pour indiquer à Siri le résultat attendu de la gestion d’une intention afin que Siri puisse indiquer à l’utilisateur ce qui va se produire.
- Fournit une opportunité pour vérifier tous les États requis dont l’application peut avoir besoin pour terminer la demande présentée par l’utilisateur, par exemple avoir suffisamment d’argent dans la Banque pour effectuer le paiement demandé.

L’application fournit une **réponse intentionnelle** de l’étape de confirmation, qui doit être remplie avec autant d’informations que l’application a disponible pour que Siri puisse la communiquer efficacement avec l’utilisateur.

Selon le type de domaine et d’action, Siri peut demander confirmation à l’utilisateur, par exemple, avant d’envoyer un paiement ou de réserver un appel.

### <a name="the-handle-stage"></a>Étape du handle

L’étape du handle est la partie la plus importante de l’utilisation d’une intention, car il s’agit du point où l’application répond à la demande de l’utilisateur en effectuant la tâche demandée.

Comme dans l’étape de confirmation, l’application doit fournir autant d’informations que possible sur le résultat, de sorte que Siri puisse la mettre en relation avec l’utilisateur. Parfois, ces informations sont présentées visuellement ou d’autres fois, Siri les rappelle à l’utilisateur. 

Dans certains cas, l’application peut nécessiter plus de temps pour traiter une demande donnée, par exemple des retards d’appels réseau ou si une personne en ligne doit répondre à la demande (comme la finalisation et l’expédition d’une commande ou la conduite d’une voiture à l’emplacement de l’utilisateur). Quand Siri attend une réponse de l’application, il affiche une interface utilisateur en attente à l’utilisateur lui indiquant que l’application est en cours de traitement de la requête.

Dans l’idéal, l’application doit fournir une réponse à Siri dans un délai de deux à trois secondes au maximum. Si l’application sait qu’une réponse donnée va prendre plus de temps à traiter, elle doit envoyer un code de réponse en **cours** à Siri. Siri informe ensuite l’utilisateur que l’application est en cours de traitement de la demande en arrière-plan et continue à le faire même s’il sort l’environnement Siri.

## <a name="adding-sirikit-to-the-app"></a>Ajout de SiriKit à l’application

Avec SiriKit dans iOS 10, Apple a créé deux nouveaux points d’extension:

- **Extension intentions** : fournit Siri avec le contenu de l’application et effectue les tâches requises pour exécuter les intentions prises en charge.
- **Extension d’IU** Intents: fournit une interface utilisateur personnalisée qui sera affichée pour le contenu des applications à l’intérieur de Siri. 

Il existe également une API pour fournir des mots et des expressions à Siri pour faciliter la reconnaissance sous la forme de:

- **Vocabulaire d’application** : mots et expressions qui sont communs à tous les utilisateurs de l’application.
- **Vocabulaire utilisateur** : mots et expressions propres à un utilisateur d’application donné.

## <a name="the-intents-extension"></a>Extension intentions

L’extension intentions est chargée de gérer les interactions principales entre l’application et Siri, comme suit:

[![](understanding-sirikit-images/intents01.png "Extension intentions")](understanding-sirikit-images/intents01.png#lightbox)

L’extension d’intention peut prendre en charge une ou plusieurs intentions, il revient au développeur de décider de la façon dont il souhaite implémenter SiriKit dans l’application. Le développeur peut également ajouter une extension d’intention distincte pour chaque intention devant être gérée.  Cela dit, Apple demande au développeur de limiter le nombre d’extensions d’intention afin que Siri n’ait pas plusieurs processus ouverts sur l’application, ce qui nécessite plus de mémoire et de temps pour la gestion.

Le développeur doit également savoir que l’extension d’intention s’exécutera en arrière-plan pendant que Siri est actif. Cela permet à Siri de discuter activement avec l’utilisateur tout en continuant à communiquer avec l’extension pour traiter les informations sur la demande.

## <a name="privacy-and-security-considerations"></a>Considérations sur la confidentialité et la sécurité

Apple a pris de bonnes mesures pour garantir la sécurité des informations privées de l’utilisateur lors de l’utilisation de Siri et, par conséquent, il existe plusieurs interactions qui nécessitent que l’utilisateur soit connecté à l’appareil iOS. Par exemple, lors de la demande d’une requête ou d’un paiement.

En outre, il existe des comportements spécifiques que l’application peut vouloir limiter à l’utilisateur qui est connecté à l’appareil. Dans ce cas, l’application peut demander le comportement **restrict while Locked** . Cette opération s’effectue via un paramètre dans `Info.plist` le fichier.

L’infrastructure d’authentification locale est disponible pour l’extension d’intention afin que l’application puisse demander à l’utilisateur des informations d’authentification supplémentaires, même si l’appareil est déjà déverrouillé.

Enfin, Apple Pay est disponible pour l’extension intention afin que l’application puisse effectuer une transaction à l’aide de Apple Pay et que la feuille de Apple Pay intégrée s’affiche au-dessus de l’interface Siri.

En outre, Apple souhaite s’assurer que les utilisateurs savent quand ils envoient des informations à une application tierce et, par conséquent, l’utilisateur **doit** indiquer le nom spécifique de l’application (tel que spécifié dans le nom d’affichage de l’offre groupée de l’application) lors de l’exécution d’une demande.

Apple a conçu Siri pour effectuer des conversations naturelles et fluides avec l’utilisateur. pour cette raison, le nom de l’offre groupée de l’application peut être utilisé dans de nombreuses parties de la parole, où qu’il s’adapte naturellement à la demande de l’utilisateur.

L’un des éléments les plus courants que les utilisateurs vont faire est de «verbify» le nom de l’application, en d’autres termes, en prenant le nom de l’application et en l’utilisant comme verbe dans une requête. Par exemple, *«MonkeyChat Bobo ce sont de superbes bananes».*

## <a name="the-intents-ui-extension"></a>Extension d’interface utilisateur Intents

L’extension d’interface utilisateur intentions vous donne la possibilité d’apporter l’interface utilisateur et la marque de l’application à l’expérience Siri et de faire en sorte que les utilisateurs se sentent connectés à l’application. Avec cette extension, l’application peut mettre la personnalisation, ainsi que les informations visuelles et autres, dans la transcription.

[![](understanding-sirikit-images/intents02.png "Exemple de sortie d’une extension d’interface utilisateur Intent")](understanding-sirikit-images/intents02.png#lightbox)

L’extension d’interface utilisateur Intents retourne toujours `UIViewController` un et l’application peut ajouter tout ce qu’elle aime dans le contrôleur d’affichage, par exemple afficher des informations supplémentaires qui vont au-delà de la réponse initiale. L’interface utilisateur Intents peut également mettre à jour l’utilisateur avec l’état d’un événement à long terme, par exemple la durée pendant laquelle une voiture de partage est plus longue pour atteindre son emplacement.

L’extension d’interface utilisateur intentions sera toujours affichée avec d’autres contenus Siri tels que l’icône et le nom de l’application, en haut de l’interface utilisateur ou, en fonction de l’intention, les boutons (comme envoyer ou annuler) peuvent s’afficher en bas.

Il existe quelques cas où l’application peut remplacer les informations que Siri affiche par défaut à l’utilisateur, par exemple la messagerie ou les cartes, où l’application peut remplacer l’expérience par défaut par une option adaptée à l’application.

> [!IMPORTANT]
> Bien qu’il soit possible d’ajouter des éléments interactifs tels que `UIButtons` ou `UITextFields` à l' `UIViewController`extension d’interface utilisateur intentionnelle, ceux-ci sont strictement interdits, car l’interface utilisateur de l’intention n’est pas interactive et l’utilisateur ne peut pas interagir avec eux.

Il est totalement facultatif pour que l’application fournisse une extension d’interface utilisateur intentionnelle, car Siri contient un ensemble d’interfaces utilisateur par défaut pour chaque type d’intention. En outre, les interfaces d’interface utilisateur intentions sont uniquement disponibles pour certaines intentions qu’Apple a jugées utiles à l’utilisateur.

## <a name="adding-sirikit-vocabulary"></a>Ajout du vocabulaire SiriKit

L’élément final de l’implémentation de SiriKit se trouve dans l’application en fournissant le vocabulaire requis. De nombreuses applications ont des moyens uniques de décrire les informations à l’utilisateur et de fournir des informations uniques à l’application.

Pour cette raison, Siri nécessite l’assistance de l’application pour comprendre les mots et les expressions propres à l’application. Certaines de ces expressions feront partie de l’application afin que chaque utilisateur les sache et les comprenne. Pourtant, d’autres sont propres à un utilisateur donné de l’application.

### <a name="app-specific-vocabulary"></a>Vocabulaire spécifique à l’application

Le vocabulaire spécifique à l’application définit les mots et expressions spécifiques qui seront connus de tous les utilisateurs de l’application, tels que les types de véhicules ou les noms d’entraînements. Dans la mesure où ils font partie de l’application, ils sont `AppIntentVocabulary.plist` définis dans un fichier dans le cadre de l’offre groupée d’applications principale. En outre, ces mots et ces expressions doivent être localisés.

Un fichier de vocabulaire `AppIntentVocabulary.plist` comporte plusieurs parties:

- **Exemples d’applications** : celles-ci fournissent un ensemble de cas d’usage courants pour les demandes que l’utilisateur peut effectuer. Par exemple :  *«Démarrer un entraînement avec MonkeyFit.»*
- **Paramètres** : ils fournissent un ensemble de types de paramètres non standard spécifiques à l’application. Par exemple, les noms des entraînements pour l’application MonkeyFit. Il s’agit des éléments suivants:
    - **Expression** : permet à l’application de définir des termes uniques pour l’application. Par exemple: type d’entraînement «Bananarific» pour l’application MonkeyFit. 
    - **Prononciation** : donne des indications de prononciation à Siri comme une simple orthographe phonétique pour une expression donnée. Par exemple, «BA Nana RI FIC».
    - **Exemple** : fournit un exemple d’utilisation de l’expression donnée dans l’application. Par exemple, *«Démarrer un Bananarific dans MonkeyFit»* .

Pour plus d’informations, consultez Référence du [format de fichier du vocabulaire d’application](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)Apple.

### <a name="user-specific-vocabulary"></a>Vocabulaire spécifique à l’utilisateur

Le vocabulaire spécifique de l’utilisateur est destiné à fournir des mots ou expressions propres aux utilisateurs individuels de l’application. Celles-ci seront fournies au moment de l’exécution à partir de l’application principale (pas les extensions de l’application) sous la forme d’un ensemble ordonné de termes classés dans une priorité d’utilisation plus importante pour les utilisateurs, avec les termes les plus importants au début de la liste.

Jetez un coup d’œil à l’exemple de l’application MonkeyChat présentée ci-dessus. MonkeyChat conserve une liste de tous les contacts de l’utilisateur, qu’il enverra à Siri via le vocabulaire spécifique de l’utilisateur. Elle conserve également une liste des 10 contacts les plus récents que l’utilisateur a message et un ensemble de contacts favoris pour chaque utilisateur. Pour cet exemple, les contacts favoris doivent se trouver au début de notre vocabulaire spécifique à l’utilisateur, suivi des contacts récents, puis du reste des contacts de l’utilisateur.

Les types d’informations suivants sont pris en charge par le vocabulaire spécifique à l’utilisateur:

- Noms des contacts.
- Noms des exercices.
- Noms des albums photo.
- Mots-clés de photos.

Si l’application s’appuie sur le carnet d’adresses iOS, il n’est pas nécessaire que l’application prenne des mesures, car ces informations sont déjà disponibles pour Siri. L’application doit uniquement fournir les noms des contacts si l’application possède sa propre base de données de contacts unique.

Lors de la conception du vocabulaire, fournissez uniquement les valeurs nécessaires dont les utilisateurs ont connaissance. Évitez de fournir des informations telles que des numéros de téléphone ou des adresses de messagerie.

L’application doit également mettre à jour Siri rapidement lorsque le vocabulaire spécifique à l’utilisateur change. Les utilisateurs sont habitués à demander des informations à Siri le moment où elles ont été ajoutées à leur appareil iOS. Par exemple, si l’utilisateur ajoute un nouveau contact dans l’application, envoyez ces informations à Siri dès que l’utilisateur l’enregistre.

Plus important encore, l’application _doit_ supprimer rapidement les informations du vocabulaire Siri dans la mesure où un utilisateur peut devenir perturber s’il a supprimé un élément d’information, mais que Siri était toujours en mesure de reconnaître des heures ou des jours plus tard.

> [!IMPORTANT]
> L’application doit supprimer tout le vocabulaire spécifique à l’utilisateur de Siri si l’utilisateur choisit de réinitialiser l’application ou si elle se déconnectera.

## <a name="sirikit-permissions"></a>Autorisations SiriKit

La dernière partie de SiriKit est centrée autour des autorisations. Tout comme l’utilisation d’autres fonctionnalités d’iOS (telles que des photos, des appareils photo ou des contacts), les utilisateurs doivent accorder une autorisation explicite pour que l’application communique avec Siri.

L’application peut fournir une chaîne définissant les informations qu’elle fournira à Siri et indiquer la raison pour laquelle l’utilisateur doit accorder cet accès. 

Apple suggère que l’application doit demander l’autorisation à l’utilisateur d’utiliser Siri la première fois que l’utilisateur ouvre l’application une fois qu’elle a été mise à niveau vers iOS 10. C’est pour cela que les utilisateurs connaissent l’intégration de Siri et qu’ils peuvent préapprouver l’utilisation avant d’effectuer leur première demande.

## <a name="sirikit-and-maps"></a>SiriKit et Maps

SiriKit fait partie intégrante d’iOS et utilise l’infrastructure d’intentions plus grande ajoutée à iOS 10. L’infrastructure intentions a été conçue pour partager des actions et des intentions communes et partagées avec d’autres parties du système.

L’infrastructure des intentions va au-delà de l’intégration de Siri et fournit d’autres fonctionnalités telles que l’intégration des contacts, où l’application peut devenir l’application de téléphonie ou de messagerie par défaut pour des contacts spécifiques. Les intentions offrent également une intégration profonde avec CallKit pour fournir aux utilisateurs la meilleure expérience VOIP possible.

L’application Maps dans iOS 10 a ajouté des fonctionnalités telles que le partage de correspondances, où l’utilisateur peut réserver une position directement à l’intérieur de l’interface utilisateur Maps. SiriKit fournit un point d’extension commun avec Maps, de sorte que les intentions de partage (et autres) peuvent être partagées entre les Siri et les mappages. 

Cela signifie que si l’application a adopté les extensions SiriKit, elle obtiendra également l’intégration de Maps gratuitement. 

## <a name="designing-a-great-siri-experience"></a>Conception d’une expérience Siri exceptionnelle

La conception d’une expérience utilisateur idéale lors de l’intégration d’une application dans Siri est différente de la conception d’une interface utilisateur d’application exceptionnelle. Contrairement aux situations normales où l’utilisateur interagit directement avec l’application à l’écran, lors de l’utilisation de Siri, il existe de nombreuses fois quand aucune interface visuelle n’est visible. Par exemple, lorsque l’utilisateur a commencé la conversation avec *«Hey Siri»* .

### <a name="how-siri-helps-the-developer"></a>Comment Siri aide le développeur

Lors de la conception des interactions d’une application avec Siri, l’application crée une *interface de conversation*, ce qui signifie que le contexte est dérivé de la conversation que Siri a avec l’utilisateur au nom de l’application.

En l’absence d’une référence visuelle, l’utilisateur doit effectuer le suivi des informations présentées en tête. Pour cette raison, Siri présente le minimum d’informations nécessaires à la réalisation de la tâche que l’utilisateur souhaite accomplir.

L’interface conversationnel est mise en forme par les questions et les réponses de l’utilisateur et de la Siri au cours de la conversation. Il est donc important de réfléchir à la façon dont Siri pose des questions et répond lors de la conception de cette interface.

Prenons l’exemple suivant de l’utilisateur qui crée un message, Siri peut répondre avec la question *«prêt à l’envoyer?»* . L’utilisateur peut répondre de nombreuses façons différentes, telles que *«l’envoyer»* , *«Annuler»* ou même un lien totalement non lié à cette question. Quelle que soit la façon dont la conversation est jouée, Siri le gère pour l’application et lui envoie uniquement les informations pertinentes lorsqu’elle est disponible.

Un utilisateur peut lancer une conversation avec Siri de plusieurs façons différentes:

- En sélectionnant l’appareil, en appuyant sur le bouton démarrage. Dans ce cas, Siri présente davantage d’interfaces visuelles et moins de réponses verbales.
- En disant *«Bonjour Siri»* et en lançant une conversation en mains libres. Dans cette situation, Siri sera moins visuel et plus oral.
- À l’aide de fonctionnalités d’accessibilité telles que Bluetooth, les aides à l’audition sont adaptées à un utilisateur ayant des besoins particuliers.
- À l’aide de la lecture de voitures, l’utilisateur doit veiller à ce qu’il se focalise sur la conduite en empêchant un minimum de distraction.

### <a name="how-the-developer-helps-siri"></a>Comment le développeur aide Siri

Lorsque vous intégrez une application à Siri, le développeur doit tester cette intégration souvent et s’assurer qu’elle effectue de nombreuses demandes différentes en demandant la même information ou la même tâche dans autant de méthodes que possible.

Dans la mesure où deux personnes ne pensent pas, il est essentiel que le développeur ait autant de testeurs de version bêta que possible pour affiner l’intégration de Siri. Les utilisateurs peuvent demander des informations ou effectuer des demandes de la manière dont le développeur n’a jamais effectué et ce réglage précis peut aider à s’assurer que le groupe d’utilisateurs le plus étendu a une expérience remarquable en utilisant l’application avec Siri.

Testez dans différentes situations et environnements. Initiez les conversations avec Siri dans toutes les méthodes possibles pour garantir que ces conversations restent fluides et naturelles. Testez les emplacements où l’utilisateur aurait probablement utilisé l’application, par exemple dans un sport surchargé.

Assurez-vous que l’application fournit toutes les informations dont Siri a besoin pour représenter correctement la demande et le résultat à l’utilisateur. Cela est particulièrement vrai lors de l’utilisation de Siri dans une situation mains libres.

### <a name="siri-design-guidelines"></a>Instructions de conception de Siri

Souvenez-vous toujours que Siri entretient une conversation avec l’utilisateur pour le compte de l’application. Le développeur souhaite s’assurer que cette conversation reste aussi fluide et naturelle que possible.

Comme c’est le cas pour toute conversation importante, le développeur doit s’assurer de ce qui suit:

- Que l’application est préparée pour la conversation.
- Que l’application écoute exactement ce que l’utilisateur tente d’accomplir.
- Que l’application pose les questions appropriées aux moments appropriés.
- Que l’application répond à la demande avec les informations que l’utilisateur recherche.

#### <a name="preparing-for-the-conversation"></a>Préparation de la conversation

La première chose à garder à l’esprit est que les utilisateurs de l’application ne vont pas être exactement comme le développeur. Ils peuvent provenir de différents arrière-plans, parler de langues différentes ou avoir des besoins particuliers lors de l’utilisation de l’application.

En outre, étant donné que le développeur a conçu et créé l’application, il dispose d’une connaissance approfondie de l’application et de ses travaux internes et fonctions qu’un utilisateur standard n’aura pas. Le développeur peut donc demander une demande de Siri différente de celle d’un utilisateur normal.

C’est pourquoi il est essentiel d’avoir autant de personnes différentes que possible d’interagir avec l’application via Siri. Les utilisateurs peuvent effectuer des requêtes de l’application par le biais de Siri que le développeur n’a jamais pensé ou que le développeur n’a pas pris en compte.

#### <a name="ensure-the-app-is-a-good-listener"></a>Vérifier que l’application est un bon écouteur

Le développeur doit s’assurer que l’application est un bon écouteur et qu’elle obtient les détails de la conversation qui répondent aux attentes de l’utilisateur. Mais il est également possible qu’ils n’aient pas fourni toutes les informations requises par l’application pour accomplir la tâche demandée.

L’application peut gérer cette situation de plusieurs façons:

- **Choisir une bonne valeur par défaut pour la valeur manquante** : par exemple, une application de partage de remplacement peut être définie par défaut à l’emplacement actuel de l’utilisateur si elle n’a pas spécifié l’emplacement à partir duquel elle voulait être récupérée.
- **Effectuer une estimation** en toute connaissance: en utilisant des informations spécifiques que l’application a recueillies sur l’utilisateur, l’application peut être en mesure de prendre des informations manquantes et de les prendre en compte, telles que le remplissage d’un numéro mobile manquant à partir des informations de contact de l’utilisateur. Toutefois, vous devez veiller à éviter les mauvaises surprises, telles que la sélection de l’option la plus coûteuse, etc.
- **Demander plus d’informations** : l’application peut demander à Siri d’inviter l’utilisateur à entrer la valeur manquante. Toutefois, la clé ici est de garder les conversations simples et jusqu’à présent. Les utilisateurs deviendront rapidement mécontents s’ils doivent répondre à plusieurs questions pour obtenir leur demande.
- **Gérez correctement les informations** incorrectes-l’utilisateur peut fournir une valeur que l’application n’attendait pas ou qu’il ne peut pas gérer dans le contexte donné. Assurez-vous que l’application met en relation cette situation à l’utilisateur de manière à ce qu’elle soit claire et facile à corriger.

Lorsque l’application est présentée avec une seule valeur en question, la meilleure façon de gérer cela consiste à demander à l’utilisateur de confirmer l’Siri. Par exemple, *«vouliez-vous Bobo le parfait?»* , auquel il peut répondre avec une simple réponse oui ou non.

Lorsqu’il existe une situation dans laquelle plusieurs choix possibles peuvent être corrects pour une valeur unique, la méthode de gestion par défaut est la méthode de gestion préférée. Dans ce cas, Siri peut inviter l’utilisateur à sélectionner jusqu’à dix options possibles. Par exemple :

```csharp
Who do you want to send the message to?

* Bobo the Great!
* Bobo Jr.
* Little Bobo
```

En cas de doute, Siri invite l’utilisateur à fournir une réponse entièrement nouvelle et spécifique pour une valeur donnée.

#### <a name="request-final-confirmation"></a>Demander la confirmation finale

Avant que l’application effectue réellement la tâche pour traiter la demande de l’utilisateur, Siri vérifie avec l’extension d’application pour s’assurer que tout est en place. Par exemple, l’utilisateur a-t-il suffisamment d’argent dans son compte pour effectuer le paiement demandé?

En outre, l’application doit s’assurer qu’elle fournit toutes les informations possibles à Siri, de sorte qu’elle puisse la présenter à l’utilisateur et confirmer que la tâche sur le lieu d’être effectuée répond à ses attentes.

Une fois que l’utilisateur a confirmé la demande et que l’application l’a exécutée, l’application doit à nouveau s’assurer qu’elle a fourni tous les résultats à Siri pour qu’elle puisse les associer à l’utilisateur.

#### <a name="responding-to-the-request"></a>Réponse à la demande

Siri dispose de plusieurs interfaces utilisateur intégrées pour chacun des domaines et actions qu’il connaît. Toutefois, le cas échéant, l’application peut fournir une extension d’interface utilisateur personnalisée pour enrichir l’expérience utilisateur en présentant la personnalisation et l’interface utilisateur de l’application, ou plus d’informations que celles présentes dans la demande.

Cela dit, l’utilisation de la contrainte doit être utilisée lors de la conception d’interfaces personnalisées pour Siri. En règle générale, l’utilisateur souhaite obtenir une tâche spécifique aussi rapidement que possible et ne souhaite pas être surchargée avec des informations inutiles.

Vous devez également veiller à ce que l’interface utilisateur personnalisée se présente et réponde correctement dans les différents appareils et orientations iOS que l’utilisateur peut avoir ou utiliser l’appareil.

Le cas échéant, utilisez l’API SiriKit pour masquer toutes les informations redondantes déjà présentes dans l’interface utilisateur Siri par défaut. Assurez-vous cependant que l’application fournit toujours les informations à Siri pour qu’elle puisse la présenter verbalement dans des situations mains libres.

Il peut y avoir des situations où Siri lance l’application pour exécuter la demande de l’utilisateur, comme la présentation des photos demandées par l’utilisateur. Dans ces situations, ne surprenez pas l’utilisateur. Affichez les informations attendues sans aucune étape intermédiaire ni interaction supplémentaire n’est requise. N’affichez jamais d’informations ou effectuez une tâche que l’utilisateur n’attend pas.

### <a name="polishing-the-design"></a>Peaufinage de la conception

Apple propose plusieurs étapes pour peaufiner la conception des interfaces de conversation. Tout d’abord, est de fournir un vocabulaire clair, concis et des exemples de cas d’usage à Siri.

L’une des façons dont un utilisateur Découvre l’application consiste à lancer une conversation avec Siri et à demander *«que pouvez-vous faire?»* . Siri affiche plusieurs opérations qu’il peut effectuer, y compris l’application du développeur et les exemples de cas d’utilisation de héros qu’il a `plist` fournis par le biais de son fichier.

Comment écrire de bons exemples d’utilisation:

- Assurez-vous que les exemples incluent le nom de l’application.
- Conservez l’exemple bref et à point.
- Fournissez plusieurs exemples pour chacune des intentions prises en charge par l’application.
- Hiérarchisez les intentions et les exemples qu’ils contiennent en fonction des cas d’utilisation les plus courants pour l’application.
- Assurez-vous que l’application fournit des exemples localisés.
- Vérifiez que chaque exemple donné fonctionne comme prévu dans l’application.
- Évitez d’adresser des Siri dans les exemples, donc n’incluez pas de texte comme *«Hey Siri...»*
- Évitez les pleasantries inutiles, par exemple *«veuillez»* ou *«Merci»* .

Prenez le temps nécessaire pour explorer et expérimenter la manière dont l’application peut mettre en forme la conversation dont Siri a besoin avec l’utilisateur en son nom. Veillez à communiquer avec les utilisateurs standard tout au long du processus, car leurs interactions avec les attentes de l’application peuvent changer au fil du temps.

N’oubliez pas de tester l’application dans différentes situations et toutes les différentes méthodes pour appeler une conversation avec Siri. Tester dans des emplacements réels l’utilisateur peut utiliser l’application, loin du bureau et du bureau.

Efforcez-vous que les conversations avec Siri (pour le compte de l’application) soient fluides, naturelles et «semblent juste». 

## <a name="summary"></a>Récapitulatif

Cet article a abordé les concepts clés nécessaires à l’utilisation de SiriKit et montre qu’il peut interagir avec les applications Xamarin. iOS pour fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS.




## <a name="related-links"></a>Liens associés

- [Exemple ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guide de programmation SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informations de référence sur l’infrastructure Intent](https://developer.apple.com/reference/intents)
- [Informations de référence sur l’infrastructure d’interface utilisateur](https://developer.apple.com/reference/intentsui)

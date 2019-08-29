---
title: Touch ID dans Xamarin. iOS
description: Ce document explique comment utiliser Touch ID, la technologie d’authentification par empreinte digitale biométrique d’Apple, dans les applications Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 0f92dca71f74266e1408cd65c842f729a9a648ce
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065663"
---
# <a name="touch-id-in-xamarinios"></a>Touch ID dans Xamarin. iOS

Touch ID a été introduit dans iOS 7 comme un moyen d’authentifier l’utilisateur, similaire à un code secret. Toutefois, il était limité au déverrouillage de l’appareil à l’aide de l’App Store, en utilisant iTunes et en authentifiant uniquement le trousseau iCloud.

Il existe deux façons d’utiliser Touch ID comme mécanisme d’authentification dans une application iOS 8 à l’aide de l’API d’authentification locale. Il n’est actuellement pas possible d’utiliser l’authentification locale pour l’authentification à distance.

Pour comprendre parfaitement l’ID tactile et sa valeur, nous devons explorer les services de trousseau et ce que ces nouvelles modifications signifient pour les données de vos utilisateurs. L’accès au trousseau a également été développé sur iOS 8 via l’utilisation de la nouvelle fonctionnalité listes de Access Control (ACL).

## <a name="keychain--secure-enclave"></a>Trousseau & l’enclave sécurisée

Le trousseau est une base de données volumineuse fournissant un stockage sécurisé pour les mots de passe, les clés, les certificats et les notes pour un ID Apple individuel. Dans iOS 8, une application a toujours accès à ses propres éléments de trousseau uniques et ne peut pas accéder aux éléments de Trousseau d’autres applications. Cela diffère du système d’exploitation X où le trousseau est déverrouillé avec un mot de passe unique, ce qui permet à toute application prenant en charge le trousseau d’utiliser le trousseau. Dans cet article, nous allons nous concentrer sur le fonctionnement du trousseau dans iOS 8.

Le trousseau est une base de données spécialisée, où chaque ligne est appelée « _élément de trousseau_». Chaque élément est décrit par des attributs de trousseau et est composé de valeurs chiffrées. Pour permettre une utilisation efficace du trousseau, il est optimisé pour les petits éléments ou les _secrets_.
Chaque élément de trousseau est protégé par le code secret de l’utilisateur et par un secret d’appareil unique. Les éléments de trousseau doivent être protégés même si les utilisateurs n’utilisent pas leurs appareils. Elle est implémentée dans iOS en autorisant uniquement les éléments à être disponibles lorsque l’appareil est déverrouillé, lorsque l’appareil est verrouillé, ils deviennent indisponibles. Elles peuvent également être stockées dans une sauvegarde chiffrée. L’une des principales fonctionnalités du trousseau consiste à appliquer le contrôle d’accès. une application a accès à sa partie du trousseau, et toutes les autres applications sont bloquées. Le diagramme ci-dessous illustre comment une application interagit avec le trousseau:

[![](touchid-images/image1.png "Ce diagramme illustre comment une application interagit avec le trousseau")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave sécurisée

Le trousseau ne peut pas déchiffrer l’élément de trousseau par lui-même; au lieu de cela, elle est effectuée dans l' *enclave sécurisée*. L’enclave sécurisée est un coprocesseur au sein de la puce a7 qui est responsable de la détermination d’une correspondance réussie entre les données d’empreinte digitale du capteur d’ID tactile et d’une impression enregistrée. Il déchiffre ensuite l’élément de trousseau et retourne le secret déchiffré au trousseau.

### <a name="working-with-keychain"></a>Utilisation du trousseau

Tout d’abord, votre application doit interroger le trousseau pour voir s’il existe un mot de passe. S’il n’existe pas, vous devrez peut-être demander un mot de passe pour que l’utilisateur ne soit pas continuellement invité. Si le mot de passe doit être mis à jour, invitez l’utilisateur à entrer un nouveau mot de passe et transmettez la valeur mise à jour au trousseau.

> [!NOTE]
> Après l’utilisation d’un secret récupéré dans le trousseau, toutes les références aux données doivent être purgées de la mémoire. Ne l’attribuez jamais à une variable globale.

## <a name="keychain-acl-and-touch-id"></a>ACL de trousseau et ID tactile

Access Control liste est un nouvel attribut d’élément de trousseau dans iOS 8 qui décrit les informations relatives à ce qui doit se produire pour permettre l’exécution d’une opération particulière. Cela peut se présenter sous la forme d’une boîte de dialogue d’alerte ou d’une demande de code d’accès. La liste de contrôle d’accès vous permet de définir l’accessibilité et l’authentification d’un élément de trousseau. Le diagramme ci-dessous montre comment ce nouvel attribut se lie au reste de l’élément de trousseau:

[![](touchid-images/image2.png "Ce diagramme montre comment ce nouvel attribut se lie au reste de l’élément de trousseau")](touchid-images/image2.png#lightbox)

Depuis iOS 8, il existe désormais une nouvelle stratégie de présence d’utilisateur `SecAccessControl`,, qui est appliquée par l’enclave sécurisée sur un iPhone 5 s et versions ultérieures. Nous pouvons voir dans le tableau ci-dessous comment la configuration de l’appareil peut influencer l’évaluation de la stratégie:

|Configuration de l’appareil|Évaluation de la stratégie|Mécanisme de sauvegarde|
|--- |--- |--- |
|Appareil sans code secret|Aucun accès|Aucun|
|Appareil avec code secret|Requiert un code secret|Aucun|
|Appareil avec Touch ID|Préfère l’ID tactile|Autorise le code secret|

Toutes les opérations au sein de l’enclave sécurisée peuvent se faire confiance les unes aux autres. Cela signifie que nous pouvons utiliser le résultat de l’authentification Touch ID pour autoriser le déchiffrement de l’élément de trousseau. L’enclave sécurisée conserve également un compteur des correspondances d’ID tactiles ayant échoué. dans ce cas, un utilisateur devra revenir à l’utilisation du code secret.
Une nouvelle infrastructure dans iOS 8, appelée _authentification locale_, prend en charge ce processus d’authentification au sein de l’appareil. Nous allons l’Explorer dans la section suivante.

## <a name="local-authentication"></a>Authentification locale

Comme nous l’avons établi dans la section précédente, les applications peuvent utiliser l’authentification locale pour authentifier l’utilisateur en respectant la stratégie de sécurité qui a été configurée sur l’appareil.

Actuellement, l’API fournit uniquement deux fonctionnalités: Tout d’abord, il aide les services de trousseau existants grâce à l’utilisation de nouvelles listes de Access Control de trousseau (ACL). Les données de trousseau peuvent être déverrouillées avec l’authentification réussie de l’empreinte digitale d’un utilisateur.

Deuxièmement, LocalAuthentication fournit deux méthodes pour authentifier votre application localement. Les développeurs doivent `CanEvaluatePolicy` utiliser pour déterminer si l’appareil peut accepter l’id tactile, `EvaluatePolicy` puis démarrer l’opération d’authentification.

Bien que les deux fonctionnalités offrent une authentification locale, elles ne fournissent pas de mécanisme permettant à l’application ou à l’utilisateur de s’authentifier auprès d’un serveur distant.
L’authentification locale fournit une nouvelle interface utilisateur standard pour l’authentification. Dans le cas de Touch ID, il s’agit d’un affichage des alertes avec deux boutons, comme illustré ci-dessous. Un bouton pour annuler et un pour utiliser le moyen de secours de l’authentification: le code secret. Il y a également un message personnalisé qui doit être défini. Il est conseillé de l’utiliser pour expliquer à l’utilisateur pourquoi l’authentification Touch ID est requise.

[![](touchid-images/image12.png "Alerte d’authentification Touch ID")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Avec les services de trousseau

Nous avons abordé un peu plus tôt dans la manière dont un élément de trousseau est déchiffré, à l’aide de l’enclave sécurisée pour vérifier notre code secret. Dans iOS 8, nous pouvons utiliser l’authentification locale pour demander une vérification de l’ID tactile conjointement avec la fonctionnalité de listes de Access Control, qui fournit l’implémentation du mécanisme de secours ou le mot de passe.
Pour utiliser la liste de contrôle d’accès `SecAccessControl` , nous devons utiliser la stratégie, puis vérifier l’état `SecAccessible.WhenPasscodeSetThisDeviceOnly` de `SecAccessible.WhenUnlocked`l’appareil à l’aide de ou de.

#### <a name="considerations-with-acl"></a>Considérations relatives à la liste de contrôle d’accès

Il y a de nombreuses choses à garder à l’esprit lorsque vous utilisez une liste de contrôle d’accès avec le trousseau et que certaines d’entre elles sont répertoriées ci-dessous:

- Utiliser uniquement avec l’application de premier plan: Si vous appelez une opération de trousseau sur un thread d’arrière-plan, l’appel échoue.
- L’ajout et la mise à jour des éléments de trousseau peuvent nécessiter une authentification.
- Si une requête retourne plusieurs éléments correspondants dans le trousseau, l’authentification peut être requise.
- Les éléments protégés par la liste de contrôle d’accès sont uniquement des appareils et ne sont donc pas synchronisés ou sauvegardés.

### <a name="using-local-authentication-without-keychain-services"></a>Utilisation de l’authentification locale sans les services trousseau

L’authentification locale a été créée pour collecter des informations d’identification, telles que code secret ou Touch ID, et pour utiliser l’enclave sécurisée pour terminer l’authentification de l’utilisateur. Considérez-le comme un pont entre votre application et l’enclave sécurisée, qui ne peut jamais communiquer directement entre elles. Il peut également être utilisé pour l’évaluation de la stratégie de votre application.

Pour ce faire, une application appelle l’évaluation de la stratégie à l’intérieur de l’authentification locale, qui démarre l’opération dans l’enclave sécurisée. Vous pouvez l’utiliser pour fournir une authentification à votre application, sans interroger directement l’enclave sécurisée et y accéder.

[![](touchid-images/image13a.png "Utilisation de l’authentification locale sans les services trousseau")](touchid-images/image13a.png#lightbox)

L’utilisation de l’authentification locale dans votre application offre un moyen simple d’implémenter la vérification de l’utilisateur, par exemple pour déverrouiller une fonctionnalité uniquement pour les yeux du propriétaire de l’appareil, tels que les applications bancaires, ou pour faciliter le contrôle parental pour chaque oeuvre. Vous pouvez également l’utiliser comme méthode d’extension de l’authentification existante: les utilisateurs comme leurs informations doivent être sécurisés, mais ils souhaitent également avoir des options.

La sécurité de l’authentification locale diffère de celle du trousseau. Par exemple, lors de l’utilisation du trousseau, l’approbation se fait entre le système d’exploitation et l’enclave sécurisée. Avec l’authentification locale, elle se trouve entre l’application et le système d’exploitation, ce qui signifie que vous n’avez accès qu’aux résultats de l’enclave sécurisée, et non à l’enclave sécurisée proprement dite.

Dans le domaine de la sécurité, il est également très important de savoir qu’il n’y a **pas d’accès** aux doigts enregistrés ou aux images d’empreinte digitale. L’enclave sécurisée est le propriétaire de ces informations, ce qui signifie qu’aucun autre composant système n’y a accès.

Pour utiliser Touch ID sans trousseau en tirant parti de l’API d’authentification locale, il existe quelques fonctions que nous pouvons utiliser. Celles-ci sont détaillées ci-dessous:

* `CanEvaluatePolicy`: Cela permet simplement de vérifier si l’appareil est en capacité d’accepter Touch ID.
* `EvaluatePolicy`: Démarre l’opération d’authentification et affiche l’interface utilisateur, et retourne `true` une `false` réponse ou.
* `DeviceOwnerAuthenticationWithBiometrics`: Il s’agit de la stratégie qui peut être utilisée pour afficher l’écran touch ID. Il est important de noter qu’il n’existe aucun mécanisme de secours du code secret ici. vous devez donc implémenter ce secours dans votre application pour permettre aux utilisateurs d’ignorer l’authentification Touch ID.

Il existe quelques inconvénients à l’utilisation de l’authentification locale, qui sont répertoriées ci-dessous:

* Comme pour le trousseau, il ne peut être exécuté qu’au premier plan. Son appel sur un thread d’arrière-plan entraînera l’échec de l’opération.
* Gardez à l’esprit que l’évaluation de la stratégie peut échouer. Un bouton de code secret doit être implémenté en tant que chute.
* Vous devez fournir un `localizedReason` pour expliquer la raison pour laquelle l’authentification est nécessaire. Cela permet de créer une relation de confiance avec l’utilisateur.

Ensuite, dans la section ci-dessous, nous verrons comment implémenter l’API en tenant compte de ces avertissements.

## <a name="adding-touch-id-to-your-application"></a>Ajout de Touch ID à votre application

Dans les sections précédentes, nous avons examiné la théorie qui sous-tend l’accès et l’authentification à l’aide du trousseau et de l’authentification locale. Nous allons maintenant voir comment vous pouvez intégrer Touch ID à votre application.

### <a name="walkthrough"></a>Procédure pas à pas

Examinons donc l’ajout d’une authentification Touch ID à notre application. Dans cette procédure pas à pas, nous allons mettre à jour l’exemple de [table de Storyboard](https://docs.microsoft.com/samples/xamarin/ios-samples/data/storyboardtable/) , en ajoutant l’authentification locale afin qu’elle fonctionne comme l’exemple [table de montage séquentiel-authentification locale](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication) , qui permet uniquement aux utilisateurs authentifiés d’ajouter des tâches à la liste.

1. Téléchargez l’exemple et exécutez-le dans Visual Studio pour Mac.
2. Double-cliquez `MainStoryboard.Storyboard` sur on pour ouvrir l’exemple dans le concepteur iOS. Dans cet exemple, nous souhaitons ajouter un nouvel écran à notre application, qui contrôle l’authentification. Cela va avant le actuel `MasterViewController`.
3. Faites glisser un nouveau **contrôleur d’affichage** de la boîte à **outils** vers le **aire de conception**. Définissez-le en tant que **contrôleur d’affichage racine** en appuyant sur **CTRL + déplacer** à partir du **contrôleur de navigation**:

    [![](touchid-images/image4.png "Définir le contrôleur d’affichage racine")](touchid-images/image4.png#lightbox)
4. Nommez le nouveau contrôleur `AuthenticationViewController`d’affichage.
5. Ensuite, faites glisser un bouton et placez-le `AuthenticationViewController`sur le. Appelez cette `AuthenticateButton`et donnez-lui le texte `Add a Chore`.
6. Créez un événement sur le `AuthenticateButton` appelé `AuthenticateMe`.
7. Créez un segue manuel à `AuthenticationViewController` partir de en cliquant sur la barre noire en bas et en appuyant sur **CTRL + faire glisser** de `MasterViewController` la barre vers et en choisissant **Envoyer** (ou **Afficher** si vous utilisez des classes de taille):

    [![](touchid-images/image5.png "Faites glisser la barre vers le MasterViewController et choisissez push ou afficher")](touchid-images/image6.png#lightbox)
8. Cliquez sur le segue nouvellement créé et donnez-lui l’identificateur `AuthenticationSegue`, comme illustré ci-dessous:

    [![](touchid-images/image7.png "Définissez l’identificateur segue sur AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. Ajoutez le code suivant à `AuthenticationViewController` :

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

Il s’agit de tout le code dont vous avez besoin pour implémenter l’authentification Touch ID à l’aide de l’authentification locale. Les lignes en surbrillance dans l’image ci-dessous illustrent l’utilisation de l’authentification locale:

[![](touchid-images/image8.png "Les lignes en surbrillance montrent l’utilisation de l’authentification locale")](touchid-images/image8.png#lightbox)

Tout d’abord, nous devons établir si l’appareil est en capacité d’accepter l’entrée Touch ID, `CanEvaluatePolicy` en utilisant le et en `DeviceOwnerAuthenticationWithBiometrics`passant la stratégie. Si la valeur est true, vous pouvez afficher l’interface tactile de l' `EvaluatePolicy`ID à l’aide de. Nous devons transférer `EvaluatePolicy` trois éléments d’information: la stratégie elle-même, une chaîne expliquant la raison pour laquelle l’authentification est nécessaire et un gestionnaire de réponse. Le gestionnaire de réponse indique à l’application ce qu’elle doit faire dans le cas d’une authentification réussie ou non réussie. Observons plus en détail le gestionnaire de réponses:

[![](touchid-images/image9.png "Gestionnaire de réponse")](touchid-images/image9.png#lightbox)

Le gestionnaire de réponses est spécifié de `LAContextReplyHandler`type, ce qui prend les paramètres Success `bool` : une valeur et `NSError` un `error`appelé. Si elle réussit, c’est là que nous allons exécuter tout ce que nous voulons authentifier. dans ce cas, nous affichons l’écran qui nous permettra d’ajouter une nouvelle corvée. N’oubliez pas que l’authentification locale doit être exécutée au premier plan. Veillez donc à utiliser `InvokeOnMainThread`:

[![](touchid-images/image10.png "Utiliser InvokeOnMainThread pour l’authentification locale")](touchid-images/image10.png#lightbox)

Enfin, lorsque l’authentification est réussie, nous voulons passer au `MasterViewController`. La `PerformSegue` méthode peut être utilisée pour effectuer cette opération:

[![](touchid-images/image11.png "Appeler la méthode PerformSegue pour passer au MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Récapitulatif

Dans ce guide, nous avons examiné le trousseau et le fonctionnement dans iOS. Nous avons également exploré la liste de contrôle d’accès de trousseau et les modifications apportées à ce dans iOS. Ensuite, nous avons examiné l’infrastructure d’authentification locale, qui est nouvelle dans iOS 8, puis j’ai examiné l’implémentation de l’authentification Touch ID dans notre application.

## <a name="related-links"></a>Liens associés

- [Table de montage séquentiel – authentification locale](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)
- [Exemple de trousseau WWDC](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-keychaintouchid/)
- [Trousseau (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/keychain/)

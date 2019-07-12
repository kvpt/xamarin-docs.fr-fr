---
title: Touch ID dans Xamarin.iOS
description: Ce document décrit comment utiliser Touch ID, la technologie d’authentification par empreinte digitale biométrique d’Apple, dans les applications Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 25ace6d7febe495164378b3633f06371806e2f82
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832304"
---
# <a name="touch-id-in-xamarinios"></a>Touch ID dans Xamarin.iOS

Touch ID a été introduit dans iOS 7 en tant que méthode d’authentification de l’utilisateur - semblable à un code secret. Toutefois, il était limité à déverrouiller l’appareil, à l’aide de l’App Store, à l’aide d’iTunes et l’authentification de la chaîne de clé iCloud uniquement.

Il existe désormais deux façons d’utiliser Touch ID comme mécanisme d’authentification dans une application iOS 8 à l’aide de l’API d’authentification Local. Actuellement, il n’est pas possible d’utiliser l’authentification locale pour l’authentification à distance.

Pour bien comprendre Touch ID et sa valeur, nous devons examiner trousseau Services et la signifient de ces nouvelles modifications pour vos données de l’utilisateur. L’accès au trousseau a également été développée lors dans iOS 8 via l’utilisation de la nouvelle fonctionnalité de listes de contrôle d’accès (ACL).

## <a name="keychain--secure-enclave"></a>Trousseau & Enclave sécurisée

Chaîne de clé est une base de données volumineux en fournissant un stockage sécurisé pour les mots de passe, les clés, les certificats et les notes de publication pour un ID Apple individuels. Dans iOS 8 une application always a accès à ses propres éléments de trousseau unique et ne peut pas accéder à tous les éléments trousseau d’autres applications. Cela diffère d’OS X, où le trousseau est déverrouillé avec un seul mot de passe, permettant à n’importe quelle application Keychain compatible avec les Services d’utiliser le trousseau. Dans cet article, nous nous concentrerons sur le fonctionne de la chaîne de clé dans iOS 8.

Chaîne de clé est une base de données spécialisé, où chaque ligne est appelé un _trousseau élément_. Chaque élément est décrit par les attributs de trousseau et est composé de valeurs chiffrées. Pour permettre une utilisation efficace de trousseau, il est optimisé pour les éléments de petite taille, ou _secrets_.
Chaque élément de trousseau est protégé par le mot de passe des utilisateurs et un secret d’appareil unique. Éléments de trousseau doivent être protégées, même lorsque les utilisateurs n’utilisent pas leurs appareils. Ceci est implémenté dans iOS en autorisant uniquement les éléments soient disponibles quand l’appareil est déverrouillé, quand l’appareil est verrouillé, ils deviennent indisponibles. Ils peuvent également être stockées dans une sauvegarde chiffrée. Une des principales fonctionnalités de porte-clés consiste à appliquer le contrôle d’accès ; une application a accès à sa partie de la chaîne de clé et ne pourra pas toutes les autres applications. Le diagramme ci-dessous illustre la façon dont une application interagit avec la chaîne de clé :

[![](touchid-images/image1.png "Ce diagramme illustre la façon dont une application interagit avec le trousseau")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave sécurisée

La chaîne de clé ne peut pas déchiffrer l’élément trousseau par lui-même ; au lieu de cela, elle est effectuée le *Enclave sécurisée*. L’enclave sécurisée est un coprocesseur dans la puce A7 qui est chargé de déterminer une correspondance à partir de données d’empreintes digitales à partir du capteur de Touch ID contre une impression inscrit. Il sera ensuite déchiffrer l’élément trousseau et retourner la clé secrète déchiffrée au trousseau.

### <a name="working-with-keychain"></a>Utilisation de trousseau

Tout d’abord votre application doit interroger dans le trousseau pour voir s’il existe un mot de passe. S’il n’existe pas, vous devrez peut-être demander un mot de passe afin de l’utilisateur n’est pas invité en permanence. Si le mot de passe doit être mis à jour, inviter l’utilisateur à un nouveau mot de passe et passez la valeur mise à jour dans le trousseau.

> [!NOTE]
> Une fois à l’aide d’une clé secrète récupérées à partir de la chaîne de clé, toutes les références aux données doivent être supprimés de la mémoire. Jamais l’affecter à une variable globale.

## <a name="keychain-acl-and-touch-id"></a>ID de trousseau ACL et l’interaction tactile

Liste de contrôle d’accès est un nouvel attribut d’élément de trousseau dans iOS 8 qui décrit les informations concernant ce qui doit se produire pour autoriser une opération particulière se produise. Cela peut être sous la forme d’affichage d’une boîte de dialogue alerte ou la demande d’un code secret. ACL vous permet de définir l’accessibilité et l’authentification pour un élément de trousseau. Le diagramme ci-dessous illustre la façon dont ce nouvel attribut lie avec le reste de l’élément de trousseau :

[![](touchid-images/image2.png "Ce diagramme illustre comment ce nouvel attribut lie avec le reste de l’élément de trousseau")](touchid-images/image2.png#lightbox)

À compter d’iOS 8, il existe désormais une nouvelle stratégie de présence de l’utilisateur, `SecAccessControl`, qui est appliqué par l’enclave sécurisée sur un iPhone 5 s et versions ultérieures. Nous pouvons voir dans le tableau ci-dessous, simplement comment la configuration de l’appareil peut influencer l’évaluation de stratégie :

|Configuration de l’appareil|Évaluation de la stratégie|Mécanisme de sauvegarde|
|--- |--- |--- |
|Appareil sans code secret|Aucun accès|Aucun|
|Appareil avec le code secret|Nécessite du code secret|Aucun|
|Appareil avec Touch ID|Préfère Touch ID|Autorise le code secret|

Toutes les opérations à l’intérieur de l’Enclave sécurisée peuvent se font confiance. Cela signifie que nous pouvons utiliser le résultat de l’authentification de Touch ID pour autoriser le déchiffrement d’élément de trousseau. L’Enclave sécurisée conserve également un compteur de correspondances infructueuses de Touch ID, auquel cas un utilisateur a revenir à l’utilisation du code secret.
Une nouvelle infrastructure dans iOS 8, appelée _l’authentification locale_, prend en charge ce processus d’authentification au sein de l’appareil. Nous allons examiner cela dans la section suivante.

## <a name="local-authentication"></a>Authentification locale

Comme nous avons créée dans la section précédente, les applications peuvent utiliser l’authentification locale pour authentifier l’utilisateur dans le respect de la stratégie de sécurité qui a été configurée sur l’appareil.

Actuellement, l’API fournit uniquement deux fonctionnalités : Tout d’abord, il aide les services de trousseau existants via l’utilisation de la nouvelle Keychain Access Control Lists (ACL). Données de chaîne de clé peuvent être déverrouillées avec l’authentification réussie d’une empreinte digitale d’utilisateurs.

Deuxièmement, LocalAuthentication fournit deux méthodes pour authentifier votre application localement. Les développeurs doivent utiliser `CanEvaluatePolicy` pour déterminer si l’appareil est capable d’accepter de Touch ID, puis `EvaluatePolicy` pour démarrer l’opération d’authentification.

Bien que les deux fonctions offrent l’authentification locale, ils ne fournissent pas un mécanisme pour l’application ou l’utilisateur à s’authentifier auprès d’un serveur distant.
L’authentification locale fournit une nouvelle interface utilisateur standard pour l’authentification. Dans le cas de Touch ID, il s’agit d’un affichage des alertes avec deux boutons, comme illustré ci-dessous. Un bouton Annuler, l’application à utiliser les moyens de secours d’authentification : le code secret. Il existe également un message personnalisé qui doit être défini. Il est recommandé d’utiliser cela pour expliquer à l’utilisateur pourquoi l’authentification de Touch ID est nécessaire.

[![](touchid-images/image12.png "L’alerte de l’authentification de Touch ID")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Avec les Services de trousseau

Nous avons étudié un peu plus tôt comment un élément de trousseau est déchiffré, à l’aide de l’enclave sécurisée pour vérifier notre code secret. Dans iOS 8, nous pouvons utiliser l’authentification locale pour demander la vérification de Touch ID conjointement avec la fonctionnalité de listes de contrôle d’accès, qui fournit l’implémentation du mécanisme de secours, ou le mot de passe.
Pour utiliser les ACL que nous devrions être à l’aide de la `SecAccessControl` stratégie, puis en vérifiant l’état de l’appareil en utilisant `SecAccessible.WhenPasscodeSetThisDeviceOnly` ou `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Considérations relatives à la liste ACL

Nous devons Gardez à l’esprit lorsque vous utilisez des ACL avec le trousseau de nombreuses choses et certaines d'entre elles sont répertoriées ci-dessous :

- Utiliser uniquement avec l’Application de premier plan, si vous appelez une opération keychain sur un thread d’arrière-plan que l’appel échoue.
- Ajout et la mise à jour des éléments de trousseau peuvent nécessiter l’authentification.
- Si une requête retourne plusieurs éléments correspondants dans le trousseau, l’authentification peut être nécessaire.
- ACL des éléments protégés sont appareil uniquement et par conséquent pas synchronisé ou sauvegardée.

### <a name="using-local-authentication-without-keychain-services"></a>À l’aide de l’authentification locale sans les Services de trousseau

L’authentification locale a été créée comme un moyen de collecter des informations d’identification, telles que le code secret ou Touch ID et fonctionne avec l’Enclave sécurisée pour terminer l’authentification de l’utilisateur. Considérez-le comme un pont entre votre application et de l’Enclave sécurisée, qui ne peuvent jamais directement communiquer entre eux. Il peut également servir pour l’évaluation de stratégie pour votre application.

Pour ce faire une application appelle l’évaluation de stratégie à l’intérieur de l’authentification locale, qui démarre l’opération à l’intérieur d’Enclave sécurisée. Vous pouvez tirer parti pour fournir l’authentification à votre application, sans interrogation et y accéder directement l’Enclave sécurisée.

[![](touchid-images/image13a.png "À l’aide de l’authentification locale sans les Services de trousseau")](touchid-images/image13a.png#lightbox)

À l’aide de l’authentification locale dans votre application fournit un moyen simple de l’implémentation de vérification de l’utilisateur, par exemple pour déverrouiller une fonctionnalité uniquement pour les yeux du propriétaire de l’appareil, telles qu’applications bancaires ou aux contrôles parentaux aide pour la personne application. Vous pouvez également l’utiliser comme un moyen d’étendre l’authentification qui existe déjà : utilisateurs comme leurs informations pour être sécurisé, mais ils le souhaitent également avoir le choix.

La sécurité de l’authentification locale est différent de celui de la chaîne de clé. Par exemple, lorsque vous utilisez le trousseau de clés, l’approbation est entre le système d’exploitation et de l’Enclave sécurisée. Avec l’authentification locale, il est entre l’application et le système d’exploitation, ce qui signifie que vous avez uniquement accès aux résultats de l’Enclave sécurisée, pas le sécuriser Enclave lui-même.

Sur le sujet de la sécurité, il est également extrêmement important de savoir qu’il y a **aucun accès** à doigts inscrits ou des images d’empreintes digitales. L’Enclave sécurisée est le propriétaire de ces informations, et par conséquent, aucun autre composant système n’y a accès.

Pour utiliser Touch ID sans trousseau en tirant parti de l’API d’authentification Local, il existe quelques fonctions que nous pouvons utiliser. Ceux-ci sont décrits ci-dessous :

*   `CanEvaluatePolicy` : Il vérifie simplement si l’appareil est capable d’accepter un ID tactile.
*   `EvaluatePolicy` : Il démarre l’opération d’authentification et affiche l’interface utilisateur et retourne un `true` ou `false` réponse.
*   `DeviceOwnerAuthenticationWithBiometrics` : Il s’agit de la stratégie qui peut être utilisée pour afficher l’écran de Touch ID. Il est important de noter qu’il n’existe ici aucun mécanisme de secours du code secret, au lieu de cela, vous devez implémenter ce secours dans votre application pour permettre aux utilisateurs d’ignorer l’authentification ID tactile.

Il existe quelques inconvénients à l’utilisation de l’authentification locale, qui sont répertoriées ci-dessous :

*   À l’instar de trousseau d’accès, il peut uniquement être exécuté au premier plan. Appeler sur un thread d’arrière-plan entraîne l’échec.
*   N’oubliez pas que l’évaluation de stratégie peut échouer. Un bouton de code secret doit être implémenté comme un secours.
*   Vous devez fournir un `localizedReason` pour expliquer pourquoi l’authentification est nécessaire. Cela permet de consolider la confiance avec l’utilisateur.

Ensuite, dans la section ci-dessous, nous allons examiner comment implémenter l’API de ces mises en garde en tenant compte.

## <a name="adding-touch-id-to-your-application"></a>Ajout de Touch ID à votre application

Dans les sections précédentes, nous avons vu la théorie des accès et l’authentification à l’aide de la chaîne de clé et l’authentification locale. Nous allons maintenant examiner comment vous pouvez intégrer des ID tactile dans à votre application.

### <a name="walkthrough"></a>Procédure pas à pas

Nous allons donc étudier d’ajouter l’authentification ID tactile à notre application. Dans cette procédure pas à pas, nous allons maintenant mettre à jour le [Table de la table de montage séquentiel](https://developer.xamarin.com/samples/StoryboardTable/) exemple, ajout de l’authentification locale afin qu’il fonctionne comme le [Table Storyboard – authentification locale](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) exemple, ce qui permet uniquement utilisateurs authentifiés pour ajouter des tâches à la liste.

1. Télécharger l’exemple et l’exécuter dans Visual Studio pour Mac.
2. Double-cliquez sur `MainStoryboard.Storyboard` pour ouvrir l’exemple dans le concepteur iOS. Avec cet exemple, nous souhaitons ajouter un nouvel écran à notre application, qui contrôlera l’authentification. Celui-ci s’insère avant actuel `MasterViewController`.
3. Faites glisser une nouvelle **contrôleur d’affichage** à partir de la **boîte à outils** à la **aire de conception**. Définir en tant que le **contrôleur d’affichage racine** par **Ctrl + glisser** à partir de la **contrôleur de Navigation**:

    [![](touchid-images/image4.png "Définissez le contrôleur d’affichage racine")](touchid-images/image4.png#lightbox)
4.  Nommez le contrôleur d’affichage `AuthenticationViewController`.
5. Ensuite, faites glisser un bouton et placez-le sur le `AuthenticationViewController`. Appelez cela `AuthenticateButton`et attribuez-lui le texte `Add a Chore`.
6. Créer un événement sur le `AuthenticateButton` appelée `AuthenticateMe`.
7. Créer un manuel segue à partir de `AuthenticationViewController` en cliquant sur la barre noire située au bas et **Ctrl + glisser** à partir de la barre pour la `MasterViewController` et en choisissant **push** (ou **afficher** Si vous utilisez des classes de taille) :

    [![](touchid-images/image5.png "Faites glisser la barre vers MasterViewController et en choisissant push ou afficher")](touchid-images/image6.png#lightbox)
8. Cliquez sur nouvellement créé segue et lui donner l’identificateur `AuthenticationSegue`, comme illustré ci-dessous :

    [![](touchid-images/image7.png "La valeur de l’identificateur de segue AuthenticationSegue")](touchid-images/image7.png#lightbox)
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

Il s’agit de tout le code que vous avez besoin pour implémenter l’authentification de Touch ID à l’aide de l’authentification locale. Les lignes en surbrillance dans l’image ci-dessous illustrent l’utilisation de l’authentification locale :

[![](touchid-images/image8.png "Les lignes en surbrillance illustrent l’utilisation de l’authentification locale")](touchid-images/image8.png#lightbox)

Tout d’abord, nous avons besoin établir si l’appareil est capable d’accepter de Touch ID d’entrée, à l’aide de la `CanEvaluatePolicy` et en passant dans la stratégie `DeviceOwnerAuthenticationWithBiometrics`. Si cela est vrai, nous pouvons afficher l’interface utilisateur de ID tactile à l’aide de `EvaluatePolicy`. Il existe trois éléments d’information, nous devons passer dans `EvaluatePolicy` – la stratégie proprement dite, une chaîne expliquant pourquoi l’authentification est nécessaire et un gestionnaire de réponse. Le Gestionnaire de réponse indique à l’application de ce qu’il doit faire dans le cas d’une authentification réussie ou non. Nous allons nous intéresser davantage au Gestionnaire de la réponse :

[![](touchid-images/image9.png "Le Gestionnaire de réponse")](touchid-images/image9.png#lightbox)

Le Gestionnaire de la réponse est spécifié de type `LAContextReplyHandler`, ce qui amène la réussite des paramètres – un `bool` valeur et un `NSError` appelée `error`. Si elle réussit, il s’agit où nous allons effectuer réellement tout ce qui doivent impérativement que nous voulons authentifier – dans ce cas afficher l’écran qui nous ajoute une corvée de nouveau. N’oubliez pas parmi les mises en garde de l’authentification locale est qu’il doit être exécuté sur le premier plan, veillez à utiliser `InvokeOnMainThread`:

[![](touchid-images/image10.png "Utiliser InvokeOnMainThread pour l’authentification locale")](touchid-images/image10.png#lightbox)

Enfin, lorsque l’authentification s’est déroulée correctement, nous voulons transition vers le `MasterViewController`. Le `PerformSegue` méthode peut être utilisée pour ce faire :

[![](touchid-images/image11.png "Appelez la méthode PerformSegue pour effectuer la transition vers MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Récapitulatif
Dans ce guide, nous avons vu trousseau et comment cela fonctionne dans iOS. Nous avons également examiné le trousseau d’accès ACL et les modifications apportées à ce dans iOS. Ensuite, nous avons un coup de œil à l’infrastructure de l’authentification locale, qui est une nouveauté dans iOS 8 et ensuite examiné d’implémentation de l’authentification de Touch ID dans notre application.

## <a name="related-links"></a>Liens associés

- [Table de montage séquentiel Table : l’authentification locale](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) 
- [Exemple de trousseau WWDC](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Trousseau (exemple)](https://developer.xamarin.com/samples/Keychain/)

---
title: Implémentation de SiriKit dans Xamarin.iOS
description: Ce document décrit les étapes requises pour implémenter la prise en charge de SiriKit dans les applications Xamarin.iOS. Il aborde les extensions de Intents et Intents UI.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 7fc675b69132ac41ffa9d87f4b3264de431b11bd
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865677"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implémentation de SiriKit dans Xamarin.iOS

_Cet article décrit les étapes requises pour implémenter la prise en charge de SiriKit dans les applications Xamarin.iOS._

Nouveau à iOS 10, SiriKit permet à une application Xamarin.iOS fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et l’application Maps sur un appareil iOS. Cet article décrit les étapes requises pour implémenter la prise en charge de SiriKit dans les applications Xamarin.iOS en ajoutant les Extensions d’intentions requises, les Extensions de l’interface utilisateur Intents et le vocabulaire.

Siri fonctionne avec le concept de **domaines**, groupes de connaître les actions pour les tâches associées. Chaque interaction de l’application avec Siri doit appartenir à l’un de ses domaines de service connues comme suit :

- Audio ou vidéo.
- Un tour de réservation.
- La gestion de sommeil.
- La messagerie.
- Recherche des photos.
- Envoyer ou recevoir des paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant un des services de l’Extension application, SiriKit envoie l’extension un **intention** objet qui décrit la demande de l’utilisateur, ainsi que les données pertinentes. l’Extension d’application génère ensuite approprié **réponse** de l’objet pour la donnée **intention**, détaillant la façon dont l’extension puisse traiter la demande.

Ce guide présente un exemple rapide de, notamment la prise en charge de SiriKit dans une application existante. Cet exemple, nous allons utiliser l’application MonkeyChat fausse :

[![](implementing-sirikit-images/monkeychat01.png "L’icône MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat conserve son propre contact livre d’amis de l’utilisateur, chacun étant associé à un nom de l’écran (par exemple, Bobo par exemple) et permet à l’utilisateur d’envoyer des conversations de texte à chaque ami par leur nom de l’écran.

## <a name="extending-the-app-with-sirikit"></a>Extension de l’application avec SiriKit

Comme indiqué dans le [présentation des Concepts de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) guide, il existe trois parties principales relatives à l’extension d’une application avec SiriKit :

[![](implementing-sirikit-images/elements01.png "Extension de l’application avec un diagramme de SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Elles incluent notamment :

1. **Extension Intents** -vérifie les réponses des utilisateurs, confirme que l’application puisse traiter la demande et effectue la tâche pour répondre à la demande de l’utilisateur.
2. **Extension d’IU Intents** - *facultatif*, fournit une interface utilisateur personnalisée pour les réponses dans l’environnement de Siri et faire passer l’interface utilisateur des applications et de personnalisation dans Siri d’enrichir l’expérience utilisateur.
3. **Application** -fournit l’application avec des vocabulaires spécifiques d’utilisateur pour faciliter son utilisation Siri. 

Tous ces éléments et les étapes pour les inclure dans l’application seront abordée en détail dans les sections ci-dessous.


## <a name="preparing-the-app"></a>Préparation de l’application

SiriKit repose sur les Extensions, toutefois, avant d’ajouter des Extensions à l’application, il existe quelques points que le développeur doit faire pour vous aider à l’adoption de SiriKit.

### <a name="moving-common-shared-code"></a>Déplacement de Code commun partagé 

Tout d’abord, le développeur peut incorpore le code commun qui sera partagé entre l’application et les extensions dans soit _projets partagés_, _des bibliothèques de classes portables (PCL)_ ou _natif Bibliothèques_.

Les Extensions doit être en mesure d’effectuer tous les aspects qui effectue l’application. Dans les termes du contrat de l’exemple d’application MonkeyChat, éléments tels que la recherche de contacts, ajout de nouveaux contacts, envoyer des messages et récupérer l’historique des messages.

En déplaçant ce code commun dans un projet partagé, la bibliothèque de classes portable ou la bibliothèque Native, il rend facile à gérer ce code au même endroit commun et garantit que l’Extension et l’application parente fournissent des expériences uniformes et fonctionnalités pour l’utilisateur.

Dans le cas de l’exemple d’application MonkeyChat, les modèles de données et le code de traitement, comme l’accès réseau et de la base de données doit être déplacés dans une bibliothèque Native.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac et ouvrez l’application MonkeyChat.
2. Avec le bouton droit sur le nom de la Solution dans le **panneau solutions** et sélectionnez **ajouter** > **nouveau projet...** : 

    [![](implementing-sirikit-images/prep01.png "Ajouter un nouveau projet")](implementing-sirikit-images/prep01.png#lightbox)
3. Sélectionnez **iOS** > **bibliothèque** > **bibliothèque de classes** et cliquez sur le **suivant** bouton : 

    [![](implementing-sirikit-images/prep02.png "Sélectionnez la bibliothèque de classes")](implementing-sirikit-images/prep02.png#lightbox)
4. Entrez `MonkeyChatCommon` pour le **nom** et cliquez sur le **créer** bouton : 

    [![](implementing-sirikit-images/prep03.png "Entrez MonkeyChatCommon pour le nom")](implementing-sirikit-images/prep03.png#lightbox)
5. Avec le bouton droit sur le **références** dossier de l’application principale dans le **l’Explorateur de solutions** et sélectionnez **modifier les références...** . Vérifier le **MonkeyChatCommon** projet puis cliquez sur le **OK** bouton : 

    [![](implementing-sirikit-images/prep05.png "Enregistrez le projet MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. Dans le **l’Explorateur de solutions**, faites glisser le code commun partagé à partir de l’application principale pour la bibliothèque Native.
7. Dans le cas MonkeyChat, faites glisser le **DataModels** et **processeurs** dossiers à partir de l’application principale dans la bibliothèque Native : 

    [![](implementing-sirikit-images/prep06.png "Les dossiers DataModels et de processeurs dans l’Explorateur de solutions")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez Visual Studio et ouvrez l’application MonkeyChat.
2. Avec le bouton droit sur le nom de la Solution dans le **l’Explorateur de solutions** et sélectionnez **ajouter** > **nouveau projet...** .
3. Sélectionnez **Visual C#**   >  **projet partagé** et cliquez sur le **suivant** bouton : 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Sélectionnez la bibliothèque de classes")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Entrez `MonkeyChatCommon` pour le **nom** et cliquez sur le **créer** bouton.
5. Avec le bouton droit sur le **références** dossier de l’application principale dans le **l’Explorateur de solutions** et sélectionnez **modifier les références...** . Vérifier le **MonkeyChatCommon** projet puis cliquez sur le **OK** bouton : 

    [![](implementing-sirikit-images/prep05w.png "Enregistrez le projet MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. Dans le **l’Explorateur de solutions**, faites glisser le code commun partagé à partir de l’application principale pour le projet partagé.
7. Dans le cas MonkeyChat, faites glisser le **DataModels** et **processeurs** dossiers à partir de l’application principale dans la bibliothèque Native.

-----

Modifiez les fichiers qui ont été déplacées vers la bibliothèque Native et l’espace de noms pour correspondre à celui de la bibliothèque. Par exemple, la modification `MonkeyChat` à `MonkeyChatCommon`:

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

Ensuite, revenez à l’application principale et ajoutez un `using` instruction pour l’espace de noms de la bibliothèque Native n’importe où l’application utilise l’une des classes qui ont été déplacés :

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>Architecture de l’application pour les Extensions

En règle générale, une application sera s’inscrire à plusieurs intentions et le développeur doit s’assurer que l’application est conçue pour le nombre approprié d’Extensions de l’intention.

Dans le cas où une application nécessite plus d’un seul objectif, le développeur a la possibilité de placement de toutes sa gestion intention dans une intention de l’Extension ou la création d’une Extension d’intention distinct pour chaque objectif.

Si vous choisissez de créer une Extension d’intention distincte pour chaque objectif, le développeur peut finissez par dupliquer une grande quantité de code réutilisable dans chaque extension et créer une grande quantité de processeur et de la surcharge de mémoire.

Pour aider à choisir entre les deux options, voir si un des intentions naturellement vont ensemble. Par exemple, une application qui a effectué des appels audio et vidéo souhaiterez peut-être inclure deux qui leur sont consacrés dans une même Extension intention comme ils sont de gestion des tâches similaires et peut fournir la réutilisation de code de la plupart des.

Pour toute intention ou le groupe d’intentions qui ne tiennent pas dans un groupe existant, créer une nouvelle Extension intention dans la solution de l’application pour les contenir.


### <a name="setting-the-required-entitlements"></a>Définir les droits requis

N’importe quelle application Xamarin.iOS qui inclut l’intégration de SiriKit, doivent avoir les droits corrects définis. Si le développeur ne correctement ces droits requis, ils ne seront pas en mesure d’installer ou de tester l’application sur le matériel iOS réels de 10 (ou version ultérieure), ce qui est également nécessaire depuis le 10 iOS Simulator ne prend pas en charge SiriKit.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Entitlements.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Basculez vers le **Source** onglet.
3. Ajouter le `com.apple.developer.siri` **propriété**, définissez le **Type** à `Boolean` et **valeur** à `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Ajoutez la propriété com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Enregistrez les modifications dans le fichier.
5. Double-cliquez sur le **fichier projet** dans le **l’Explorateur de solutions** à ouvrir pour modification.
6. Sélectionnez **signature du Bundle iOS** et vérifiez que le `Entitlements.plist` fichier est sélectionné dans le **droits personnalisés** champ : 

    [![](implementing-sirikit-images/setup02.png "Sélectionnez le fichier Entitlements.plist dans le champ droits personnalisés")](implementing-sirikit-images/setup02.png#lightbox)
7. Cliquez sur le bouton **OK** pour enregistrer les changements.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Entitlements.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Ajouter le `com.apple.developer.siri` **propriété**, définissez le **Type** à `Boolean` et **valeur** à `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Ajoutez la propriété com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
3. Enregistrez les modifications dans le fichier.
4. Double-cliquez sur le **fichier projet** dans le **l’Explorateur de solutions** à ouvrir pour modification.
5. Sélectionnez **signature du Bundle iOS** et vérifiez que le `Entitlements.plist` fichier est sélectionné dans le **droits personnalisés** champ.

-----

Lorsque vous avez terminé, l’application `Entitlements.plist` fichier doit se présenter comme suit (dans ouvert dans un éditeur externe) :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>Approvisionnement correctement l’application

En raison de la sécurité stricte qu’Apple a placé autour de l’infrastructure de SiriKit, n’importe quelle application Xamarin.iOS qui implémente SiriKit _doit_ ont l’ID d’application et les droits (voir la section ci-dessus) corrects et doivent être signées avec un approprié Profil de provisionnement.

Les opérations suivantes sur votre Mac :

1. Dans un navigateur web, accédez à [ https://developer.apple.com ](https://developer.apple.com) et connectez-vous à votre compte.
2. Cliquez sur **certificats**, **identificateurs** et **profils**.
3. Sélectionnez **profils de provisionnement** et sélectionnez **ID d’application**, puis cliquez sur le **+** bouton.
4. Entrez un **nom** pour le nouveau profil.
5. Entrez un **ID d’offre groupée** suivant Apple d’attribution de noms recommandation.
6. Faites défiler jusqu'à la **App Services** section, sélectionnez **SiriKit** et cliquez sur le **continuer** bouton : 

    [![](implementing-sirikit-images/setup03.png "Sélectionnez SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Vérifiez que tous les paramètres, puis **envoyer** l’application ID.
8. Sélectionnez **profils de provisionnement** > **développement**, cliquez sur le **+** bouton, sélectionnez le **ID Apple**, puis cliquez sur **continuer**.
9. Cliquez sur Sélectionner **tous les**, puis cliquez sur **continuer**.
10. Cliquez sur **sélectionner tout** , puis cliquez sur **continuer**.
11. Entrez un **nom du profil** à l’aide d’Apple d’attribution de noms des suggestions, puis cliquez sur **continuer**.
12. Démarrez Xcode.
13. Dans le menu Xcode sélectionnez **préférences...**
14. Sélectionnez **comptes**, puis cliquez sur le **afficher les détails...** Bouton : 

    [![](implementing-sirikit-images/setup04.png "Sélectionnez les comptes")](implementing-sirikit-images/setup04.png#lightbox)
15. Cliquez sur le **télécharger tous les profils** bouton dans le coin inférieur gauche : 

    [![](implementing-sirikit-images/setup05.png "Télécharger tous les profils")](implementing-sirikit-images/setup05.png#lightbox)
16. Vérifiez que le **profil de provisionnement** créé ci-dessus a été installé dans Xcode.
17. Ouvrez le projet pour ajouter la prise en charge de SiriKit à dans Visual Studio pour Mac.
18. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions**.
19. Vérifiez que le **identificateur de Bundle** correspond à celui créé dans le portail des développeurs d’Apple ci-dessus : 

    [![](implementing-sirikit-images/setup06.png "L’identificateur de Bundle")](implementing-sirikit-images/setup06.png#lightbox)
20. Dans le **l’Explorateur de solutions**, sélectionnez le **projet**.
21. Cliquez sur le projet et sélectionnez **Options**.
22. Sélectionnez **signature du Bundle iOS**, sélectionnez le **identité de signature** et **profil de provisionnement** créé ci-dessus : 

    [![](implementing-sirikit-images/setup07.png "Sélectionnez l’identité de signature et profil de provisionnement")](implementing-sirikit-images/setup07.png#lightbox)
23. Cliquez sur le bouton **OK** pour enregistrer les changements.

> [!IMPORTANT]
> Test SiriKit ne fonctionne que sur un véritable iOS 10 périphérique matériel et non dans les 10 iOS Simulator. Si l’application Xamarin.iOS sur du vrai matériel est activée pour avoir des problèmes pour installer un SiriKit, assurez-vous que les droits requis, ID d’application, identificateur de signature et de profil de provisionnement ont été correctement configurés dans du Apple portail des développeurs et dans Visual Studio pour Mac.

### <a name="requesting-siri-authorization"></a>Demandez une autorisation de Siri

Avant de l’application ajoute un vocabulaire spécifique d’utilisateur ou les Extensions d’intentions se connecte à Siri, il doit demande une autorisation de l’utilisateur pour accéder à Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Modifier l’application `Info.plist` de fichiers, basculez vers le **Source** afficher et ajouter la `NSSiriUsageDescription` clé avec une valeur de chaîne qui décrit l’utilisation de l’application Siri et ce qu’il recevront des types de données. Par exemple, l’application MonkeyChat peut indiquer « MonkeyChat contacts seront envoyés à Siri » :

[![](implementing-sirikit-images/request01.png "La NSSiriUsageDescription dans l’éditeur Info.plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Modifier l’application `Info.plist` fichier, puis ajoutez le `NSSiriUsageDescription` clé avec une valeur de chaîne qui décrit l’utilisation de l’application Siri et ce que les types de données seront envoyés. Par exemple, l’application MonkeyChat peut indiquer « MonkeyChat contacts seront envoyés à Siri » :

[![](implementing-sirikit-images/request01w.png "La NSSiriUsageDescription dans l’éditeur Info.plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Appelez le `RequestSiriAuthorization` méthode de la `INPreferences` classe au premier démarrage de l’application. Modifier le `AppDelegate.cs` classe et de rendre le `FinishedLaunching` méthode ressemble ce qui suit :


```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });


    return true;
}
```

La première fois que cette méthode est appelée, une alerte s’affiche invitant l’utilisateur à autoriser l’application à accéder à Siri. Le message que le développeur ajouté à la `NSSiriUsageDescription` ci-dessus s’affichera dans cette alerte. Si l’utilisateur refuse l’accès au départ, ils peuvent utiliser le **paramètres** application pour accorder l’accès à l’application.

À tout moment, l’application peut vérifier la configuration de l’application à accéder à Siri en appelant le `SiriAuthorizationStatus` méthode de la `INPreferences` classe.

### <a name="localization-and-siri"></a>Localisation et Siri

Sur un appareil iOS, l’utilisateur est en mesure de sélectionner une langue pour Siri est différent, puis la valeur par défaut du système. Lorsque vous travaillez avec des données localisées, l’application doit utiliser le `SiriLanguageCode` méthode de la `INPreferences` classe pour obtenir le code de langue à partir de Siri. Par exemple :

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Ajout de vocabulaire spécifique d’utilisateur

Le vocabulaire spécifique utilisateur va fournir des mots ou expressions qui sont uniques à des utilisateurs individuels de l’application. Il seront fournis lors de l’exécution à partir de l’application principale (pas les Extensions d’application) comme un ensemble ordonné de termes, classés dans une priorité d’utilisation plus significative pour les utilisateurs, les termes les plus importantes au début de la liste.

Vocabulaire spécifique d’utilisateur doit appartenir à une des catégories suivantes :

- Noms de contact (qui ne sont pas gérés par l’infrastructure de Contacts).
- Balises de la photo.
- Noms d’Album photo.
- Noms d’entraînement.

Lorsque vous sélectionnez la terminologie à inscrire en tant que de vocabulaire personnalisée, uniquement choisir des termes du contrat qui peut être mal comprise par une personne ne connaît pas l’application. Jamais register termes courants tels que « Mes séances d’entraînement » ou « Mon Album ». Par exemple, l’application MonkeyChat inscrira les surnoms associés à chaque contact dans le carnet d’adresses de l’utilisateur.

L’application fournit le vocabulaire spécifique d’utilisateur en appelant le `SetVocabularyStrings` méthode de la `INVocabulary` classe et en passant un `NSOrderedSet` collection à partir de l’application principale. L’application doit toujours appeler la `RemoveAllVocabularyStrings` méthode tout d’abord, pour supprimer les termes du contrat existant avant d’ajouter de nouveaux. Par exemple :

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

Avec ce code en place, il peut être appelé comme suit :

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri traite de vocabulaire personnalisée en tant qu’indicateurs et intègrera autant de caractères de la terminologie que possible. Toutefois, l’espace de vocabulaire personnalisée est limitée, ce qui la rend important d’inscrire _uniquement_ la terminologie qui peut être déroutant, par conséquent limiter le nombre total de termes inscrits au minimum.

Pour plus d’informations, consultez notre [référence vocabulaire spécifique de l’utilisateur](~/ios/platform/sirikit/understanding-sirikit.md) et d’Apple [spécifiant une référence de vocabulaire personnalisée](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Ajout de vocabulaire de spécifiques de l’application

Le vocabulaire spécifique d’application définit les mots et expressions spécifiques qui seront connues à tous les utilisateurs de l’application, telles que les types de véhicules ou noms d’entraînement. Ils font partie de l’application, elles sont définies dans un `AppIntentVocabulary.plist` fichier en tant que bundle de l’application principale. En outre, ces mots et des expressions doivent être localisées.

Termes spécifiques de l’application doivent appartenir à une des catégories suivantes :

- Puisse surfer sur Options.
- Noms d’entraînement.

Le fichier de vocabulaire spécifique d’application contient deux clés de niveau racine :

- `ParameterVocabularies` **Requis** -définit les termes du contrat personnalisé et des paramètres d’intention elles s’appliquent à l’application.
- `IntentPhrases` **Facultatif** -contient des expressions à l’aide des termes du contrat personnalisé défini dans le `ParameterVocabularies`.

Chaque entrée dans le `ParameterVocabularies` doit spécifier une chaîne d’ID, terme et l’intention du terme s’applique à. En outre, un terme unique peut-être s’appliquer à plusieurs modes.

Pour obtenir une liste complète des valeurs acceptables et de la structure des fichiers requis, consultez d’Apple [référence de Format de fichier de vocabulaire application](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Pour ajouter un `AppIntentVocabulary.plist` fichier au projet d’application, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez sur le nom de projet dans le **l’Explorateur de solutions** et sélectionnez **ajouter** > **nouveau fichier...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Ajouter une liste de propriétés")](implementing-sirikit-images/plist01.png#lightbox)
2. Double-cliquez sur le `AppIntentVocabulary.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
3. Cliquez sur le **+** pour ajouter une clé, définissez le **nom** à `ParameterVocabularies` et **Type** à `Array`:

    [![](implementing-sirikit-images/plist02.png "La valeur est le nom ParameterVocabularies et le Type de tableau")](implementing-sirikit-images/plist02.png#lightbox)
4. Développez `ParameterVocabularies` et cliquez sur le **+** bouton et définissez le **Type** à `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Définissez le Type de dictionnaire")](implementing-sirikit-images/plist03.png#lightbox)
5. Cliquez sur le **+** pour ajouter une nouvelle clé, définissez le **nom** à `ParameterNames` et **Type** à `Array`:

    [![](implementing-sirikit-images/plist04.png "La valeur est le nom ParameterNames et le Type de tableau")](implementing-sirikit-images/plist04.png#lightbox)
6. Cliquez sur le **+** pour ajouter une nouvelle clé avec la **Type** de `String` et la valeur en tant qu’un des noms de paramètres disponibles. Par exemple, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05.png#lightbox)
7. Ajouter le `ParameterVocabulary` clés pour le `ParameterVocabularies` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist06.png "Ajouter la clé de ParameterVocabulary à la clé ParameterVocabularies avec le Type de tableau")](implementing-sirikit-images/plist06.png#lightbox)
8. Ajoutez une nouvelle clé avec la **Type** de `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Ajoutez une nouvelle clé avec le Type de dictionnaire")](implementing-sirikit-images/plist07.png#lightbox)
9. Ajouter le `VocabularyItemIdentifier` clé avec la **Type** de `String` et spécifiez un ID unique pour le terme :

    [![](implementing-sirikit-images/plist08.png "Ajouter la clé VocabularyItemIdentifier avec le Type de chaîne et spécifiez un ID unique")](implementing-sirikit-images/plist08.png#lightbox)
10. Ajouter le `VocabularyItemSynonyms` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist09.png "Ajouter la clé VocabularyItemSynonyms avec le Type de tableau")](implementing-sirikit-images/plist09.png#lightbox)
11. Ajoutez une nouvelle clé avec la **Type** de `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Ajoutez une nouvelle clé avec le Type de dictionnaire")](implementing-sirikit-images/plist10.png#lightbox)
12. Ajouter le `VocabularyItemPhrase` clé avec la **Type** de `String` et le terme définissez l’application :

    [![](implementing-sirikit-images/plist11.png "Ajouter la clé VocabularyItemPhrase avec le Type de chaîne et le terme sont de définition de l’application")](implementing-sirikit-images/plist11.png#lightbox)
13. Ajouter le `VocabularyItemPronunciation` clé avec la **Type** de `String` et la prononciation phonétique du terme :

    [![](implementing-sirikit-images/plist12.png "Ajouter la clé VocabularyItemPronunciation avec le Type de chaîne et la prononciation phonétique du terme")](implementing-sirikit-images/plist12.png#lightbox)
14. Ajouter le `VocabularyItemExamples` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist13.png "Ajouter la clé VocabularyItemExamples avec le Type de tableau")](implementing-sirikit-images/plist13.png#lightbox)
15. Ajouter quelques `String` clés avec des exemples qui utilisent le terme :

    [![](implementing-sirikit-images/plist14.png "Ajouter quelques clés de chaîne avec des exemples qui utilisent le terme")](implementing-sirikit-images/plist14.png#lightbox)
16. Répétez les étapes ci-dessus pour les autres conditions personnalisées de l’application a besoin de définir.
17. Réduire le `ParameterVocabularies` clé.
18. Ajouter le `IntentPhrases` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist15.png "Ajouter la clé IntentPhrases avec le Type de tableau")](implementing-sirikit-images/plist15.png#lightbox)
19. Ajoutez une nouvelle clé avec la **Type** de `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Ajoutez une nouvelle clé avec le Type de dictionnaire")](implementing-sirikit-images/plist16.png#lightbox)
20. Ajouter le `IntentName` clé avec la **Type** de `String` et l’intention de l’exemple :

    [![](implementing-sirikit-images/plist17.png "Ajoutez la clé de IntentName avec le Type de chaîne et l’intention de l’exemple")](implementing-sirikit-images/plist17.png#lightbox)
21. Ajouter le `IntentExamples` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist18.png "Ajouter la clé IntentExamples avec le Type de tableau")](implementing-sirikit-images/plist18.png#lightbox)
22. Ajouter quelques `String` clés avec des exemples qui utilisent le terme :

    [![](implementing-sirikit-images/plist19.png "Ajouter quelques clés de chaîne avec des exemples qui utilisent le terme")](implementing-sirikit-images/plist19.png#lightbox)
23. Répétez les étapes ci-dessus pour les intentions de l’application a besoin pour fournir l’exemple d’utilisation de.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cliquez sur le nom de projet dans le **l’Explorateur de solutions** et sélectionnez **Ajouter > nouvel élément... > Apple > liste de propriétés > Info.plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Ajouter un nouveau Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Double-cliquez sur le `AppIntentVocabulary.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
3. Cliquez sur le **+** pour ajouter une clé, définissez le **nom** à `ParameterVocabularies` et **Type** à `Array`:

    [![](implementing-sirikit-images/plist02w.png "La valeur est le nom ParameterVocabularies et le Type de tableau")](implementing-sirikit-images/plist02w.png#lightbox)
4. Développez `ParameterVocabularies` et cliquez sur le **+** bouton et définissez le **Type** à `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Définissez le Type de dictionnaire")](implementing-sirikit-images/plist03w.png#lightbox)
5. Cliquez sur le **+** pour ajouter une nouvelle clé, définissez le **nom** à `ParameterNames` et **Type** à `Array`:

    [![](implementing-sirikit-images/plist04w.png "La valeur est le nom ParameterNames et le Type de tableau")](implementing-sirikit-images/plist04w.png#lightbox)
6. Cliquez sur le **+** pour ajouter une nouvelle clé avec la **Type** de `String` et la valeur en tant qu’un des noms de paramètres disponibles. Par exemple, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05w.png#lightbox)
7. Ajouter le `ParameterVocabulary` clés pour le `ParameterVocabularies` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist06w.png "Ajouter la clé de ParameterVocabulary à la clé ParameterVocabularies avec le Type de tableau")](implementing-sirikit-images/plist06w.png#lightbox)
8. Ajoutez une nouvelle clé avec la **Type** de `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Ajoutez une nouvelle clé avec le Type de dictionnaire")](implementing-sirikit-images/plist07w.png#lightbox)
9. Ajouter le `VocabularyItemIdentifier` clé avec la **Type** de `String` et spécifiez un ID unique pour le terme :

    [![](implementing-sirikit-images/plist08w.png "Ajouter la clé VocabularyItemIdentifier avec le Type de chaîne et spécifiez un ID unique pour le terme")](implementing-sirikit-images/plist08w.png#lightbox)
10. Ajouter le `VocabularyItemSynonyms` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist09w.png "Ajouter la clé VocabularyItemSynonyms avec le Type de tableau")](implementing-sirikit-images/plist09w.png#lightbox)
11. Ajoutez une nouvelle clé avec la **Type** de `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Ajoutez une nouvelle clé avec le Type de dictionnaire")](implementing-sirikit-images/plist10w.png#lightbox)
12. Ajouter le `VocabularyItemPhrase` clé avec la **Type** de `String` et le terme définissez l’application :

    [![](implementing-sirikit-images/plist11w.png "Ajouter la clé VocabularyItemPhrase avec le Type de chaîne et le terme sont de définition de l’application")](implementing-sirikit-images/plist11w.png#lightbox)
13. Ajouter le `VocabularyItemPronunciation` clé avec la **Type** de `String` et la prononciation phonétique du terme :

    [![](implementing-sirikit-images/plist12w.png "Ajouter la clé VocabularyItemPronunciation avec le Type de chaîne et la prononciation phonétique du terme")](implementing-sirikit-images/plist12w.png#lightbox)
14. Ajouter le `VocabularyItemExamples` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist13w.png "Ajouter la clé VocabularyItemExamples avec le Type de tableau")](implementing-sirikit-images/plist13w.png#lightbox)
15. Ajouter quelques `String` clés avec des exemples qui utilisent le terme :

    [![](implementing-sirikit-images/plist14w.png "Ajouter quelques clés de chaîne avec des exemples qui utilisent le terme")](implementing-sirikit-images/plist14w.png#lightbox)
16. Répétez les étapes ci-dessus pour les autres conditions personnalisées de l’application a besoin de définir.
17. Réduire le `ParameterVocabularies` clé.
18. Ajouter le `IntentPhrases` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist15w.png "Ajouter la clé IntentPhrases avec le Type de tableau")](implementing-sirikit-images/plist15w.png#lightbox)
19. Ajoutez une nouvelle clé avec la **Type** de `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Ajoutez une nouvelle clé avec le Type de dictionnaire")](implementing-sirikit-images/plist16w.png#lightbox)
20. Ajouter le `IntentName` clé avec la **Type** de `String` et l’intention de l’exemple :

    [![](implementing-sirikit-images/plist17w.png "Ajoutez la clé de IntentName avec le Type de chaîne et l’intention de l’exemple")](implementing-sirikit-images/plist17w.png#lightbox)
21. Ajouter le `IntentExamples` clé avec la **Type** de `Array`:

    [![](implementing-sirikit-images/plist18w.png "Ajouter la clé IntentExamples avec le Type de tableau")](implementing-sirikit-images/plist18w.png#lightbox)
22. Ajouter quelques `String` clés avec des exemples qui utilisent le terme :

    [![](implementing-sirikit-images/plist19w.png "Ajouter quelques clés de chaîne avec des exemples qui utilisent le terme")](implementing-sirikit-images/plist19w.png#lightbox)
23. Répétez les étapes ci-dessus pour les intentions de l’application a besoin pour fournir l’exemple d’utilisation de.

-----

> [!IMPORTANT]
> Le `AppIntentVocabulary.plist` sera enregistré avec Siri sur le test appareils pendant le développement et il peuvent prendre un certain temps pour Siri d’incorporer le vocabulaire personnalisée. Par conséquent, le testeur devrez patienter quelques minutes avant de tester le vocabulaire spécifique d’application quand il a été mis à jour.

Pour plus d’informations, consultez notre [référence vocabulaire spécifique d’application](~/ios/platform/sirikit/understanding-sirikit.md) et d’Apple [spécifiant une référence de vocabulaire personnalisée](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Ajout d’une Extension Intents

Maintenant que l’application a été préparée à adopter SiriKit, le développeur devra ajouter des Extensions d’intentions une (ou plus) à la solution pour gérer les intentions requises pour l’intégration de Siri.

Pour chaque Extension Intents requise, procédez comme suit :

- Ajouter un projet d’Extension Intents à la solution d’application Xamarin.iOS.
- Configurer l’Extension Intents `Info.plist` fichier.
- Modifier la classe principale d’Extension Intents.

Pour plus d’informations, consultez notre [la référence d’Extension Intents](~/ios/platform/sirikit/understanding-sirikit.md) et d’Apple [création de la référence d’Extension Intents](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Création de l’Extension

Pour ajouter une Extension Intents à la solution, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Avec le bouton droit sur le **nom de la Solution** dans le **panneau solutions** et sélectionnez **ajouter** > **ajouter un nouveau projet...** .
2. Dans la boîte de dialogue Sélectionnez **iOS** > **Extensions** > **intention Extension** et cliquez sur le **suivant** bouton : 

    [![](implementing-sirikit-images/intents05.png "Sélectionnez l’Extension Intent")](implementing-sirikit-images/intents05.png#lightbox)
3. Entrez ensuite une **nom** pour l’Extension de l’intention et cliquez sur le **suivant** bouton : 

    [![](implementing-sirikit-images/intents06.png "Entrez un nom pour l’Extension Intent")](implementing-sirikit-images/intents06.png#lightbox)
4. Enfin, cliquez sur le **créer** pour ajouter l’Extension de l’intention à la solution d’applications : 

    [![](implementing-sirikit-images/intents07.png "Ajouter l’Extension de l’intention à la solution d’applications")](implementing-sirikit-images/intents07.png#lightbox)
5. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le **références** dossier de l’Extension d’intention nouvellement créé. Vérifiez le nom du projet de bibliothèque code partagé commun (que l’application créée ci-dessus) et cliquez sur le **OK** bouton : 

    [![](implementing-sirikit-images/intents08.png "Sélectionnez le nom du projet de bibliothèque de code partagé commun")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avec le bouton droit sur le **nom de la Solution** dans le **l’Explorateur de solutions** et sélectionnez **ajouter** > **ajouter un nouveau projet...** .
2. Dans la boîte de dialogue Sélectionnez **Visual C# > Extensions iOS > intention Extension** et cliquez sur le **suivant** bouton :

    [![](implementing-sirikit-images/intents05.w157-sml.png "Sélectionnez l’Extension Intent")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Entrez ensuite une **nom** pour l’Extension de l’intention et cliquez sur le **OK** bouton.
4. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le **références** dossier de l’Extension Intents qui vient d’être créé et choisissez **Ajouter > référence**. Vérifiez le nom du projet de bibliothèque code partagé commun (que l’application créée ci-dessus) et cliquez sur le **OK** bouton :

    [![](implementing-sirikit-images/intents08w.png "Sélectionnez le nom du projet de bibliothèque de code partagé commun")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Répétez ces étapes pour le nombre d’Extensions de l’intention (selon [architecture de l’application pour les Extensions](#architecting-the-app-for-extensions) section ci-dessus) qui nécessite l’application.

### <a name="configuring-the-infoplist"></a>Configurer le fichier Info.plist

Pour chacune des Extensions Intents qui ont ajoutés à la solution de l’application, doivent être configurés dans le `Info.plist` fichiers fonctionne avec l’application.

Tout comme n’importe quelle Extension d’application standard, l’application aura les clés existantes de `NSExtension` et `NSExtensionAttributes`. Pour une Extension Intents, il existe deux nouveaux attributs qui doivent être configurés :

[![](implementing-sirikit-images/intents01.png "Les deux nouveaux attributs qui doivent être configurés")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** - est obligatoire et se compose d’un tableau de noms de classe de l’intention que l’application souhaite prendre en charge à partir de l’Extension de l’intention.
- **IntentsRestrictedWhileLocked** -est une clé facultative pour l’application de spécifier le comportement d’écran de verrouillage de l’extension. Il se compose d’un tableau à l’intention de noms de classe que l’application veut l’utilisateur doit être connecté à utiliser à partir de l’Extension de l’intention.

Pour configurer l’Extension intention `Info.plist` , double-cliquez dessus dans le **l’Explorateur de solutions** à ouvrir pour modification. Ensuite, basculez vers le **Source** afficher, puis développez le `NSExtension` et `NSExtensionAttributes` clés dans l’éditeur :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "Les clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "Les clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Développez le `IntentsSupported` de clé et ajoutez le nom de n’importe quelle classe d’intention de cette extension prend en charge. Pour l’exemple d’application MonkeyChat, il prend en charge le `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "La clé INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "La clé INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Si l’application nécessite éventuellement que l’utilisateur soit connecté à l’appareil pour utiliser une intention donnée, développez le `IntentRestrictedWhileLocked` de clé et ajoutez les noms de classe des intentions qui ont un accès limité. Pour l’exemple d’application MonkeyChat, l’utilisateur doit être connecté pour envoyer un message de conversation : nous avons ajouté `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "La clé INSendMessageIntent ajoutée")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "La clé INSendMessageIntent ajoutée")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Pour obtenir une liste complète des domaines d’intention disponibles, consultez le site d’Apple [intention domaines référence](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configuration de la classe principale

Ensuite, le développeur devra configurer la classe principale qui agit en tant que point d’entrée principal pour l’Extension de l’intention dans Siri. Il doit être une sous-classe de `INExtension` qui est conforme à la `IINIntentHandler` déléguer. Par exemple :

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

Il existe une méthode Solitaire que l’application doit implémenter sur la classe principale d’intention de l’Extension, le `GetHandler` (méthode). Cette méthode est passée une intention par SiriKit et l’application doit retourner un **intention gestionnaire** correspondant au type de l’objectif de donnée.

Étant donné que l’exemple d’application MonkeyChat gère uniquement un seul objectif, il retourne lui-même dans le `GetHandler` (méthode). Si l’extension gérée plusieurs intention, le développeur aurait ajouter une classe pour chaque type d’intention et retournez une instance ici selon le `Intent` transmis à la méthode.

### <a name="handling-the-resolve-stage"></a>Gestion de la phase de résolution

L’étape de résoudre est où l’Extension intention sera clarifier et valider les paramètres transmis à partir de Siri et ont été définies par le biais de conversation de l’utilisateur.

Pour chaque paramètre envoyé à partir de Siri, il existe un `Resolve` (méthode). L’application doit implémenter cette méthode pour chaque paramètre que l’application peut avoir besoin d’aide pour la réponse correcte à partir de l’utilisateur de Siri.

Dans le cas de l’exemple d’application MonkeyChat, l’Extension d’intention nécessitera une un ou plusieurs destinataires envoyer le message. Pour chaque destinataire dans la liste, l’extension devrez effectuer une recherche de contact qui peut avoir le résultat suivant :

- Un seul contact correspondant est trouvé.
- Deux ou plusieurs contacts correspondants sont trouvés.
- Aucun contact correspondant n’est trouvé.

En outre, MonkeyChat nécessite le contenu pour le corps du message. Si l’utilisateur a fourni pas cela, Siri doit inviter l’utilisateur pour le contenu.

Vous devez l’Extension de l’intention de façon à gérer chacun de ces cas.


```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

Pour plus d’informations, consultez notre [The Reference de phase résoudre](~/ios/platform/sirikit/understanding-sirikit.md) et d’Apple [Resolving et référence de modes de gestion des](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Gestion de l’étape de confirmation

L’étape de confirmation est où l’Extension intention vérifie qu’il a toutes les informations pour répondre à la demande de l’utilisateur. L’application souhaite envoyer sera de confirmation le long de tous les détails de prise en charge de ce qui est sur le se produisent à Siri afin qu’il peut être confirmée par l’utilisateur que c’est l’action prévue.

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

Pour plus d’informations, consultez notre [The Reference de phase confirmer](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>L’objectif de traitement

C’est le point où l’Extension intention effectue la tâche pour répondre à la demande de l’utilisateur, passer les résultats à Siri afin de l’utilisateur soit informé.


```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

Pour plus d’informations, consultez notre [The Reference de phase gérer](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Ajout d’une Extension d’IU Intents

L’Extension d’IU Intents facultatif offre la possibilité d’afficher l’interface utilisateur de l’application et de personnalisation dans l’expérience de Siri et rendre les utilisateurs se sentent connectés à l’application. Cette extension de l’application peut d’intégrer la marque, ainsi que les visuels et d’autres informations dans la transcription.

[![](implementing-sirikit-images/intentsui01.png "Un exemple de sortie d’Extension d’IU Intents")](implementing-sirikit-images/intentsui01.png#lightbox)

Tout comme l’Extension Intents, le développeur effectuera l’étape suivante pour l’Extension d’IU Intents :

- Ajouter un projet d’Extension d’IU Intents à la solution d’application Xamarin.iOS.
- Configurer l’Extension d’IU Intents `Info.plist` fichier.
- Modifier la classe principale d’Extension d’IU Intents.

Pour plus d’informations, consultez notre [référence de l’Extension d’interface utilisateur Intents](~/ios/platform/sirikit/understanding-sirikit.md) et d’Apple [en fournissant une référence de l’Interface personnalisée](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Création de l’Extension

Pour ajouter une Extension d’IU Intents à la solution, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Avec le bouton droit sur le **nom de la Solution** dans le **panneau solutions** et sélectionnez **ajouter** > **ajouter un nouveau projet...** .
2. Dans la boîte de dialogue Sélectionnez **iOS** > **Extensions** > **Extension d’IU intention** et cliquez sur le **suivant** bouton : 

    [![](implementing-sirikit-images/intents11.png "Sélectionnez l’Extension de l’intention de l’interface utilisateur")](implementing-sirikit-images/intents11.png#lightbox)
3. Entrez ensuite une **nom** pour l’Extension de l’intention et cliquez sur le **suivant** bouton : 

    [![](implementing-sirikit-images/intents12.png "Entrez un nom pour l’Extension Intent")](implementing-sirikit-images/intents12.png#lightbox)
4. Enfin, cliquez sur le **créer** pour ajouter l’Extension de l’intention à la solution d’applications : 

    [![](implementing-sirikit-images/intents13.png "Ajouter l’Extension de l’intention à la solution d’applications")](implementing-sirikit-images/intents13.png#lightbox)
5. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le **références** dossier de l’Extension d’intention nouvellement créé. Vérifiez le nom du projet de bibliothèque code partagé commun (que l’application créée ci-dessus) et cliquez sur le **OK** bouton : 

    [![](implementing-sirikit-images/intents14.png "Sélectionnez le nom du projet de bibliothèque de code partagé commun")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avec le bouton droit sur le **nom de la Solution** dans le **l’Explorateur de solutions** et sélectionnez **ajouter** > **ajouter un nouveau projet...**
2. Dans la boîte de dialogue Sélectionnez **iOS** > **Extensions** > **Extension d’IU intention** et cliquez sur le **suivant** bouton.
3. Entrez ensuite une **nom** pour l’Extension de l’intention et cliquez sur le **OK** bouton.
4. Dans le **l’Explorateur de solutions**, avec le bouton droit sur le **références** dossier de l’Extension d’intention nouvellement créé. Vérifiez le nom du projet de bibliothèque code partagé commun (que l’application créée ci-dessus) et cliquez sur le **OK** bouton.
    
-----

### <a name="configuring-the-infoplist"></a>Configurer le fichier Info.plist

Configurer l’Extension de l’interface utilisateur Intents `Info.plist` fichier fonctionne avec l’application.

Tout comme n’importe quelle Extension d’application standard, l’application aura les clés existantes de `NSExtension` et `NSExtensionAttributes`. Pour une Extension Intents, il existe un nouvel attribut qui doit être configuré :

[![](implementing-sirikit-images/intents03.png "L’un nouvel attribut qui doit être configuré")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** est obligatoire et se compose d’un tableau de noms de classe de l’intention de choix de l’application pour prendre en charge à partir de l’Extension de l’intention.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour configurer l’Extension de l’interface utilisateur intention `Info.plist` , double-cliquez dessus dans le **l’Explorateur de solutions** à ouvrir pour modification. Ensuite, basculez vers le **Source** afficher, puis développez le `NSExtension` et `NSExtensionAttributes` clés dans l’éditeur :

[![](implementing-sirikit-images/intents04.png "Les clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour configurer l’Extension de l’interface utilisateur intention `Info.plist` , double-cliquez dessus dans le **l’Explorateur de solutions** à ouvrir pour modification. Développez le `NSExtension` et `NSExtensionAttributes` clés dans l’éditeur :

[![](implementing-sirikit-images/intents04w.png "Tthe NSExtension et NSExtensionAttributes clés dans l’éditeur")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Développez le `IntentsSupported` de clé et ajoutez le nom de n’importe quelle classe d’intention de cette extension prend en charge. Pour l’exemple d’application MonkeyChat, il prend en charge le `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "La clé INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "La clé INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Pour obtenir une liste complète des domaines d’intention disponibles, consultez le site d’Apple [intention domaines référence](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configuration de la classe principale

Configurer la classe principale qui agit en tant que point d’entrée principal pour l’Extension de l’interface utilisateur d’intention dans Siri. Il doit être une sous-classe de `UIViewController` qui est conforme à la `IINUIHostedViewController` interface. Par exemple :

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri transmettra un `INInteraction` instance de classe vers le `Configure` méthode de la `UIViewController` instance à l’intérieur de l’Extension de l’interface utilisateur d’intention.

Le `INInteraction` objet fournit trois éléments clés d’information à l’extension :

1. L’objet d’intention en cours de traitement.
2. L’objet de réponse de l’intention du `Confirm` et `Handle` méthodes de l’Extension de l’intention.
3. L’état d’Interaction qui définit l’état de l’interaction entre l’application et le Siri.

Le `UIViewController` instance est la classe de principe pour l’interaction avec Siri et, car il hérite de `UIViewController`, il a accès à toutes les fonctionnalités de UIKit.

Lorsque Siri appelle le `Configure` méthode de la `UIViewController` il passe dans un contexte d’affichage indiquant que le contrôleur d’affichage sera soit hébergé dans une Siri Snippit ou de cartes.

Siri sera également transmettre un gestionnaire d’achèvement nécessitant l’application pour retourner la taille souhaitée de l’affichage une fois que l’application a terminé sa configuration.

### <a name="design-the-ui-in-ios-designer"></a>Concevoir l’interface utilisateur dans le concepteur iOS

Disposition de l’interface utilisateur de l’Extension l’interface utilisateur Intents dans le concepteur iOS. Double-cliquez sur l’extension `MainInterface.storyboard` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification. Faites glisser dans tous les éléments d’interface utilisateur requis pour générer l’interface utilisateur et enregistrer les modifications.

> [!IMPORTANT]
> Bien qu’il soit possible d’ajouter des éléments interactifs tels que `UIButtons` ou `UITextFields` à l’Extension de l’interface utilisateur intention `UIViewController`, ils sont strictement interdite en tant que l’interface utilisateur intention dans non interactive et l’utilisateur ne sera pas en mesure d’interagir avec eux.

### <a name="wire-up-the-user-interface"></a>AutoEventWireup l’Interface utilisateur

Avec l’Interface d’utilisateur de l’Extension d’IU Intents créé dans le concepteur iOS, vous devez modifier le `UIViewController` sous-classe et que vous remplacez le `Configure` méthode comme suit :

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>Substitution de l’interface utilisateur de Siri par défaut

L’Extension d’IU Intents seront toujours affichée, ainsi que d’autres contenus Siri telles que l’icône de l’application et le nom en haut de l’interface utilisateur ou, selon l’intention, des boutons (par exemple, envoyer ou annuler) peut être affiché en bas.

Il existe quelques cas où l’application peut remplacer les informations que Siri affiche à l’utilisateur par défaut, comme la messagerie ou les mappages où l’application peut remplacer l’expérience par défaut par un adaptées à l’application.

Si l’Extension d’IU Intents doit remplacer les éléments de la valeur par défaut Siri UI, le `UIViewController` sous-classe devra implémenter le `IINUIHostedViewSiriProviding` interface et participer à l’affichage d’un élément d’interface particulier.

Ajoutez le code suivant à la `UIViewController` sous-classe pour indiquer à Siri que l’Extension de l’interface utilisateur d’intention est déjà affiché le contenu du message :

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considérations

Apple suggère que le développeur prendre en compte lorsque vous concevez et les Extensions de l’interface utilisateur d’intention de mise en œuvre les éléments suivants :

- **Être conscients de l’utilisation de mémoire** - Extensions de l’interface utilisateur d’intention, car sont temporaires et seule affichée pendant une courte période, le système impose des contraintes de mémoire plus étroite que sont utilisées avec une application complète.
- **Envisagez au Minimum et Maximum tailles d’affichage** -Assurez-vous que les Extensions de l’interface utilisateur d’intention extrêmement séduisant sur chaque type d’appareil iOS, la taille et l’orientation. En outre, la taille souhaitée renvoie de l’application à Siri peut-être pas en mesure d’être accordé.
- **Utilisez Flexible et des modèles de disposition Adaptive** - là encore, pour vous assurer que l’interface utilisateur va à merveille chaque appareil.

## <a name="summary"></a>Récapitulatif

Cet article a couvert SiriKit et indiqué comment il peut être ajoutée pour les applications Xamarin.iOS pour fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et l’application Maps sur un appareil iOS.




## <a name="related-links"></a>Liens associés

- [Exemple de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guide de programmation de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Référence de Framework intentions](https://developer.apple.com/reference/intents)
- [Référence de Framework d’interface utilisateur Intents](https://developer.apple.com/reference/intentsui)
- [Référence de l’intention de domaines](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)

---
title: Implémentation de SiriKit dans Xamarin. iOS
description: Ce document décrit les étapes requises pour implémenter la prise en charge de SiriKit dans une application Xamarin. iOS. Il aborde les extensions intentions et les extensions d’interface utilisateur des intentions.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 5c891943d0d23c24169a6d226a10f83964c9257a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290637"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implémentation de SiriKit dans Xamarin. iOS

_Cet article décrit les étapes requises pour implémenter la prise en charge de SiriKit dans une application Xamarin. iOS._

Nouveauté d’iOS 10, SiriKit permet à une application Xamarin. iOS de fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS. Cet article décrit les étapes requises pour implémenter la prise en charge SiriKit dans les applications Xamarin. iOS en ajoutant les extensions requises, les extensions d’interface utilisateur et le vocabulaire.

Siri fonctionne avec le concept de **domaines**, de groupes d’actions connues pour les tâches associées. Chaque interaction avec Siri doit être comprise dans l’un de ses domaines de service connus, comme suit :

- Appel audio ou vidéo.
- Réservation d’une définition.
- Gestion des entraînements.
- Messag.
- Recherche de photos.
- Envoi ou réception de paiements.

Lorsque l’utilisateur effectue une requête de Siri impliquant l’un des services de l’extension d’application, SiriKit envoie à l’extension un objet d' **intention** qui décrit la demande de l’utilisateur ainsi que toutes les données de prise en charge. l’extension d’application génère ensuite l’objet de **réponse** approprié pour l' **intention**donnée, en détaillant la manière dont l’extension peut traiter la demande.

Ce guide présente un exemple rapide d’intégration de la prise en charge de SiriKit dans une application existante. Pour les besoins de cet exemple, nous allons utiliser l’application factice MonkeyChat :

[![](implementing-sirikit-images/monkeychat01.png "Icône MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat conserve son propre livre de contacts des amis de l’utilisateur, chacun associé à un nom d’écran (comme Bobo, par exemple) et permet à l’utilisateur d’envoyer des conversations textuelles à chaque ami en fonction de leur nom d’écran.

## <a name="extending-the-app-with-sirikit"></a>Extension de l’application avec SiriKit

Comme indiqué dans le Guide de [Présentation des concepts de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) , il existe trois parties principales impliquées dans l’extension d’une application avec SiriKit :

[![](implementing-sirikit-images/elements01.png "Extension de l’application avec le diagramme SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Elles incluent notamment :

1. **Extension des intentions** -vérifie les réponses des utilisateurs, confirme que l’application peut traiter la demande et effectue en fait la tâche pour répondre à la demande de l’utilisateur.
2. **L’extension** - d’interface utilisateur intentes*facultative*, fournit une interface utilisateur personnalisée aux réponses dans l’environnement Siri et peut amener l’interface utilisateur de l’application et la personnaliser à Siri pour enrichir l’expérience de l’utilisateur.
3. **Application** : fournit l’application avec des vocabulaires spécifiques à l’utilisateur pour aider Siri à l’utiliser. 

Tous ces éléments et les étapes permettant de les inclure dans l’application sont traités en détail dans les sections ci-dessous.


## <a name="preparing-the-app"></a>Préparation de l’application

SiriKit repose sur les extensions. Toutefois, avant d’ajouter des extensions à l’application, le développeur doit effectuer quelques opérations pour faciliter l’adoption de SiriKit.

### <a name="moving-common-shared-code"></a>Déplacement du code partagé commun 

Tout d’abord, le développeur peut déplacer une partie du code commun qui sera partagé entre l’application et les extensions dans des _projets partagés_, des _bibliothèques de classes portables (classes portables)_ ou des _bibliothèques natives_.

Les extensions doivent être en mesure d’effectuer toutes les opérations de l’application. Dans le cadre de l’exemple d’application MonkeyChat, vous trouvez des contacts, ajoutez de nouveaux contacts, envoyez des messages et récupérez l’historique des messages.

En déplaçant ce code commun dans un projet partagé, PCL ou une bibliothèque native, il est facile de conserver ce code dans un emplacement courant et de s’assurer que l’extension et l’application parente fournissent des expériences et des fonctionnalités uniformes pour l’utilisateur.

Dans le cas de l’exemple d’application MonkeyChat, les modèles de données et le code de traitement, tels que l’accès au réseau et à la base de données, sont déplacés dans une bibliothèque native.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac et ouvrez l’application MonkeyChat.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **panneau solutions** puis sélectionnez **Ajouter** > **un nouveau projet...** : 

    [![](implementing-sirikit-images/prep01.png "Ajouter un nouveau projet")](implementing-sirikit-images/prep01.png#lightbox)
3. Sélectionnez**bibliothèque de classes** **iOS** > **Library** > , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/prep02.png "Sélectionner une bibliothèque de classes")](implementing-sirikit-images/prep02.png#lightbox)
4. Entrez `MonkeyChatCommon` pour le **nom** et cliquez sur le bouton **créer** : 

    [![](implementing-sirikit-images/prep03.png "Entrez MonkeyChatCommon pour le nom")](implementing-sirikit-images/prep03.png#lightbox)
5. Cliquez avec le bouton droit sur le dossier **références** de l’application principale dans le **Explorateur de solutions** puis sélectionnez **modifier les références...** . Vérifiez le projet **MonkeyChatCommon** et cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/prep05.png "Vérifier le projet MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. Dans le **Explorateur de solutions**, faites glisser le code partagé commun de l’application principale vers la bibliothèque native.
7. Dans le cas de MonkeyChat, faites glisser les dossiers **datamodels** et **Processors** de l’application principale vers la bibliothèque Native : 

    [![](implementing-sirikit-images/prep06.png "Les dossiers DataModels et Processors dans le Explorateur de solutions")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez Visual Studio et ouvrez l’application MonkeyChat.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **un nouveau projet...** .
3.  > Sélectionnez **projet partagé** **visuel C#** , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Sélectionner une bibliothèque de classes")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Entrez `MonkeyChatCommon` pour le **nom** et cliquez sur le bouton **créer** .
5. Cliquez avec le bouton droit sur le dossier **références** de l’application principale dans le **Explorateur de solutions** puis sélectionnez **modifier les références...** . Vérifiez le projet **MonkeyChatCommon** et cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/prep05w.png "Vérifier le projet MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. Dans le **Explorateur de solutions**, faites glisser le code partagé commun de l’application principale vers le projet partagé.
7. Dans le cas de MonkeyChat, faites glisser les dossiers **datamodels** et **Processors** de l’application principale vers la bibliothèque native.

-----

Modifiez l’un des fichiers qui ont été déplacés vers la bibliothèque native et modifiez l’espace de noms pour qu’il corresponde à celui de la bibliothèque. Par exemple, en `MonkeyChat` `MonkeyChatCommon`remplaçant par :

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

Ensuite, revenez à l’application principale et ajoutez une `using` instruction pour l’espace de noms de la bibliothèque Native partout où l’application utilise l’une des classes qui ont été déplacées :

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

### <a name="architecting-the-app-for-extensions"></a>Architecture de l’application pour les extensions

En règle générale, une application s’inscrit à plusieurs intentions et le développeur doit s’assurer que l’application est conçue pour le nombre approprié d’extensions d’intention.

Dans le cas où une application nécessite plusieurs intentions, le développeur a la possibilité de placer tout son traitement d’intention dans une extension d’intention ou de créer une extension d’intention distincte pour chaque intention.

Si vous choisissez de créer une extension d’intention distincte pour chaque intention, le développeur peut finir par dupliquer une grande quantité de code réutilisable dans chaque extension et créer un grand nombre de processeurs et de surcharge de mémoire.

Pour choisir entre les deux options, regardez si l’une des intentions appartient naturellement. Par exemple, une application qui a effectué des appels audio et vidéo peut souhaiter inclure ces deux intentions dans une extension d’intention unique, car elles gèrent des tâches similaires et peuvent fournir la plus grande réutilisation de code.

Pour toute intention ou groupe d’intentions qui ne rentre pas dans un groupe existant, créez une nouvelle extension d’intention dans la solution de l’application pour les contenir.


### <a name="setting-the-required-entitlements"></a>Définition des droits requis

Toute application Xamarin. iOS qui comprend l’intégration de SiriKit doit avoir les droits appropriés définis. Si le développeur ne définit pas correctement ces droits requis, il ne pourra pas installer ou tester l’application sur du matériel iOS 10 (ou supérieur) réel, ce qui est également requis puisque le simulateur iOS 10 ne prend pas en charge SiriKit.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur `Entitlements.plist` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Basculez vers l’onglet **source** .
3. Ajoutez la `com.apple.developer.siri` `Yes` **propriété**, `Boolean` affectez au type la valeur et à la **valeur** : 

    [![](implementing-sirikit-images/setup01.png "Ajouter la propriété com. Apple. Developer. Siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Enregistrez les modifications dans le fichier.
5. Double-cliquez sur le **fichier projet** dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
6. Sélectionnez **signature du bundle iOS** et assurez `Entitlements.plist` -vous que le fichier est sélectionné dans le champ **droits personnalisés** : 

    [![](implementing-sirikit-images/setup02.png "Sélectionnez le fichier habilitations. plist dans le champ droits personnalisés.")](implementing-sirikit-images/setup02.png#lightbox)
7. Cliquez sur le bouton **OK** pour enregistrer les changements.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur `Entitlements.plist` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Ajoutez la `com.apple.developer.siri` `Yes` **propriété**, `Boolean` affectez au type la valeur et à la **valeur** : 

    [![](implementing-sirikit-images/setup01w.png "Ajouter la propriété com. Apple. Developer. Siri")](implementing-sirikit-images/setup01w.png#lightbox)
3. Enregistrez les modifications dans le fichier.
4. Double-cliquez sur le **fichier projet** dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
5. Sélectionnez **signature du bundle iOS** et assurez `Entitlements.plist` -vous que le fichier est sélectionné dans le champ **droits personnalisés** .

-----

Lorsque vous avez terminé, le `Entitlements.plist` fichier de l’application doit ressembler à ce qui suit (ouvert dans un éditeur externe) :

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

### <a name="correctly-provisioning-the-app"></a>Configuration correcte de l’application

En raison de la sécurité stricte qu’Apple a placée autour de l’infrastructure SiriKit, toute application Xamarin. iOS qui implémente SiriKit _doit_ avoir l’ID d’application et les droits appropriés (voir la section ci-dessus) et doit être signée avec un profil de provisionnement approprié.

Procédez comme suit sur votre Mac :

1. Dans un navigateur Web, accédez à [https://developer.apple.com](https://developer.apple.com) votre compte et connectez-vous à celui-ci.
2. Cliquez sur **certificats**, **identificateurs** et **profils**.
3. Sélectionnez **Configuration des profils** et sélectionnez **ID d’application**, puis cliquez sur **+** le bouton.
4. Entrez un **nom** pour le nouveau profil.
5. Entrez un **ID de Bundle** suivant la recommandation d’attribution de noms d’Apple.
6. Faites défiler jusqu’à la section **app services** , sélectionnez **SiriKit** , puis cliquez sur le bouton **continue** : 

    [![](implementing-sirikit-images/setup03.png "Sélectionner SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Vérifiez tous les paramètres, puis **soumettez** l’ID de l’application.
8. Sélectionnez **provisionnement des profils** > **développement**, cliquez **+** sur le bouton, sélectionnez l' **ID Apple**, puis cliquez sur **Continuer**.
9. Cliquez sur sélectionner **tout**, puis sur **Continuer**.
10. Cliquez à nouveau sur **Sélectionner** , puis sur **Continuer**.
11. Entrez un **nom de profil** à l’aide des suggestions d’affectation de noms d’Apple, puis cliquez sur **Continuer**.
12. Démarrez Xcode.
13. Dans le menu Xcode **, sélectionnez Préférences...**
14. Sélectionnez **comptes**, puis cliquez sur **afficher les détails...** bouton 

    [![](implementing-sirikit-images/setup04.png "Sélectionner des comptes")](implementing-sirikit-images/setup04.png#lightbox)
15. Cliquez sur le bouton **Télécharger tous les profils** dans le coin inférieur gauche : 

    [![](implementing-sirikit-images/setup05.png "Télécharger tous les profils")](implementing-sirikit-images/setup05.png#lightbox)
16. Assurez-vous que le **profil de provisionnement** créé ci-dessus a été installé dans Xcode.
17. Ouvrez le projet auquel ajouter la prise en charge SiriKit dans Visual Studio pour Mac.
18. Double-cliquez sur `Info.plist` le fichier dans le **Explorateur de solutions**.
19. Assurez-vous que l' **identificateur de Bundle** correspond à celui créé dans le portail des développeurs d’Apple ci-dessus : 

    [![](implementing-sirikit-images/setup06.png "Identificateur du bundle")](implementing-sirikit-images/setup06.png#lightbox)
20. Dans le **Explorateur de solutions**, sélectionnez le **projet**.
21. Cliquez avec le bouton droit sur le projet et sélectionnez **options**.
22. Sélectionnez **signature du bundle iOS**, sélectionnez l' **identité de signature** et le profil de **provisionnement** créés ci-dessus : 

    [![](implementing-sirikit-images/setup07.png "Sélectionner l’identité de signature et le profil de provisionnement")](implementing-sirikit-images/setup07.png#lightbox)
23. Cliquez sur le bouton **OK** pour enregistrer les changements.

> [!IMPORTANT]
> Le test de SiriKit fonctionne uniquement sur un périphérique matériel iOS 10 réel et non dans le simulateur iOS 10. Si vous rencontrez des problèmes lors de l’installation d’une application Xamarin. iOS activée pour SiriKit sur du matériel réel, assurez-vous que les droits, l’ID d’application, l’identificateur de signature et le profil de provisionnement requis ont été correctement configurés dans le portail des développeurs d’Apple et dans Visual Studio pour Mac.

### <a name="requesting-siri-authorization"></a>Demande d’autorisation Siri

Avant que l’application n’ajoute de vocabulaire spécifique à l’utilisateur ou que les extensions intentions se connectent à Siri, elle doit demander l’autorisation à l’utilisateur d’accéder à Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Modifiez le fichier de `Info.plist` l’application, basculez vers la vue **source** et `NSSiriUsageDescription` ajoutez la clé avec une valeur de chaîne décrivant la manière dont l’application utilise Siri et les types de données qui seront envoyés. Par exemple, l’application MonkeyChat peut indiquer « les contacts MonkeyChat sont envoyés à Siri » :

[![](implementing-sirikit-images/request01.png "NSSiriUsageDescription dans l’éditeur info. plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Modifiez le fichier de `Info.plist` l’application et ajoutez `NSSiriUsageDescription` la clé avec une valeur de chaîne décrivant la manière dont l’application utilisera Siri et les types de données qui seront envoyés. Par exemple, l’application MonkeyChat peut indiquer « les contacts MonkeyChat sont envoyés à Siri » :

[![](implementing-sirikit-images/request01w.png "NSSiriUsageDescription dans l’éditeur info. plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Appelez la `RequestSiriAuthorization` méthode de la `INPreferences` classe lors du premier démarrage de l’application. Modifiez la `AppDelegate.cs` classe et faites en `FinishedLaunching` sorte que la méthode ressemble à ce qui suit :


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

La première fois que cette méthode est appelée, une alerte s’affiche pour inviter l’utilisateur à autoriser l’application à accéder à Siri. Le message que le développeur a ajouté à `NSSiriUsageDescription` la version ci-dessus s’affiche dans cette alerte. Si l’utilisateur refuse l’accès initialement, il peut utiliser l’application **paramètres** pour accorder l’accès à l’application.

À tout moment, l’application peut vérifier la capacité de l’application à accéder à Siri en `SiriAuthorizationStatus` appelant la méthode `INPreferences` de la classe.

### <a name="localization-and-siri"></a>Localisation et Siri

Sur un appareil iOS, l’utilisateur est en mesure de sélectionner une langue pour Siri différente de la valeur système par défaut. Lorsque vous utilisez des données localisées, l’application doit utiliser la `SiriLanguageCode` méthode de la `INPreferences` classe pour récupérer le code de langue à partir de Siri. Par exemple :

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Ajout d’un vocabulaire spécifique à l’utilisateur

Le vocabulaire spécifique de l’utilisateur est destiné à fournir des mots ou expressions propres aux utilisateurs individuels de l’application. Celles-ci seront fournies au moment de l’exécution à partir de l’application principale (pas les extensions de l’application) sous la forme d’un ensemble ordonné de termes, classés selon une priorité d’utilisation plus importante pour les utilisateurs, avec les termes les plus importants au début de la liste.

Le vocabulaire spécifique à l’utilisateur doit appartenir à l’une des catégories suivantes :

- Noms des contacts (qui ne sont pas gérés par l’infrastructure de contacts).
- Étiquettes photo.
- Noms des albums photo.
- Noms des exercices.

Lorsque vous sélectionnez la terminologie à inscrire en tant que vocabulaire personnalisé, choisissez uniquement les termes qui peuvent être mal compris par une personne qui ne connaît pas l’application. N’inscrivez jamais des termes courants tels que « mon entraînement » ou « mon album ». Par exemple, l’application MonkeyChat enregistre les surnoms associés à chaque contact dans le carnet d’adresses de l’utilisateur.

L’application fournit le vocabulaire spécifique à l’utilisateur en `SetVocabularyStrings` appelant la méthode `INVocabulary` de la classe et en `NSOrderedSet` passant une collection à partir de l’application principale. L’application doit toujours appeler la `RemoveAllVocabularyStrings` méthode en premier, pour supprimer les termes existants avant d’en ajouter d’autres. Par exemple :

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
> Siri traite le vocabulaire personnalisé comme des indicateurs et incorpore la plus grande partie de la terminologie possible. Toutefois, l’espace pour le vocabulaire personnalisé est limité. il est donc important d’enregistrer _uniquement_ la terminologie qui peut prêter à confusion, ce qui rend le nombre total de termes inscrits au minimum.

Pour plus d’informations, consultez notre [référence de vocabulaire spécifique](~/ios/platform/sirikit/understanding-sirikit.md) à l’utilisateur et la [référence de vocabulaire personnalisé](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)d’Apple.

### <a name="adding-app-specific-vocabulary"></a>Ajout d’un vocabulaire spécifique à l’application

Le vocabulaire spécifique à l’application définit les mots et expressions spécifiques qui seront connus de tous les utilisateurs de l’application, tels que les types de véhicules ou les noms d’entraînements. Dans la mesure où ils font partie de l’application, ils sont `AppIntentVocabulary.plist` définis dans un fichier dans le cadre de l’offre groupée d’applications principale. En outre, ces mots et ces expressions doivent être localisés.

Les termes du vocabulaire spécifique à l’application doivent appartenir à l’une des catégories suivantes :

- Options d’annulation.
- Noms des exercices.

Le fichier vocabulaire propre à l’application contient deux clés de niveau racine :

- `ParameterVocabularies`**Obligatoire** -définit les conditions personnalisées de l’application et les paramètres d’intention auxquels elles s’appliquent.
- `IntentPhrases`**Facultatif** : contient des exemples d’expressions utilisant les termes personnalisés définis `ParameterVocabularies`dans le.

Chaque entrée dans `ParameterVocabularies` doit spécifier une chaîne d’ID, un terme et l’intention à laquelle le terme s’applique. En outre, un terme unique peut s’appliquer à plusieurs intentions.

Pour obtenir la liste complète des valeurs acceptables et la structure des fichiers requise, consultez [Référence du format de fichier du vocabulaire d’application](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)Apple.

Pour ajouter un `AppIntentVocabulary.plist` fichier au projet d’application, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans le **Explorateur de solutions** , cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter** > **un nouveau fichier...**  >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Ajouter une liste de propriétés")](implementing-sirikit-images/plist01.png#lightbox)
2. Double-cliquez sur `AppIntentVocabulary.plist` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
3. Cliquez sur **+** le pour ajouter une clé, définissez le nom `ParameterVocabularies` sur et le type `Array`sur :

    [![](implementing-sirikit-images/plist02.png "Définissez le nom sur ParameterVocabularies et le type sur Array.")](implementing-sirikit-images/plist02.png#lightbox)
4. Développez `ParameterVocabularies` et cliquez **+** sur le bouton et définissez le `Dictionary` **type** sur :

    [![](implementing-sirikit-images/plist03.png "Définir le type sur dictionary")](implementing-sirikit-images/plist03.png#lightbox)
5. Cliquez sur **+** le pour ajouter une nouvelle clé, définissez le nom `ParameterNames` sur et le type `Array`sur :

    [![](implementing-sirikit-images/plist04.png "Définissez le nom sur ParameterNames et le type sur Array.")](implementing-sirikit-images/plist04.png#lightbox)
6. Cliquez sur **+** le pour ajouter une nouvelle clé avec le type `String` de et la valeur comme l’un des noms de paramètres disponibles. Par exemple `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "Clé INStartWorkoutIntent. workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Ajoutez la `ParameterVocabulary` clé à la `ParameterVocabularies` `Array`clé avec le **type** :

    [![](implementing-sirikit-images/plist06.png "Ajoutez la clé ParameterVocabulary à la clé ParameterVocabularies avec le type de tableau")](implementing-sirikit-images/plist06.png#lightbox)
8. Ajoutez une nouvelle clé avec le **type** `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Ajouter une nouvelle clé avec le type de dictionnaire")](implementing-sirikit-images/plist07.png#lightbox)
9. Ajoutez la `VocabularyItemIdentifier` clé avec le **type** `String` et spécifiez un ID unique pour le terme :

    [![](implementing-sirikit-images/plist08.png "Ajoutez la clé VocabularyItemIdentifier avec le type de chaîne et spécifiez un ID unique")](implementing-sirikit-images/plist08.png#lightbox)
10. Ajoutez la `VocabularyItemSynonyms` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist09.png "Ajoutez la clé VocabularyItemSynonyms avec le type de tableau")](implementing-sirikit-images/plist09.png#lightbox)
11. Ajoutez une nouvelle clé avec le **type** `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Ajouter une nouvelle clé avec le type de dictionnaire")](implementing-sirikit-images/plist10.png#lightbox)
12. Ajoutez la `VocabularyItemPhrase` clé avec le **type** `String` et le terme définis par l’application :

    [![](implementing-sirikit-images/plist11.png "Ajouter la clé VocabularyItemPhrase avec le type de chaîne et le terme définis par l’application")](implementing-sirikit-images/plist11.png#lightbox)
13. Ajoutez la `VocabularyItemPronunciation` clé avec le **type** `String` et la prononciation phonétique du terme :

    [![](implementing-sirikit-images/plist12.png "Ajoutez la clé VocabularyItemPronunciation avec le type de chaîne et la prononciation phonétique du terme")](implementing-sirikit-images/plist12.png#lightbox)
14. Ajoutez la `VocabularyItemExamples` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist13.png "Ajoutez la clé VocabularyItemExamples avec le type de tableau")](implementing-sirikit-images/plist13.png#lightbox)
15. Ajoutez quelques `String` clés avec les exemples d’utilisation du terme :

    [![](implementing-sirikit-images/plist14.png "Ajoutez quelques clés de chaîne avec des exemples d’utilisations du terme")](implementing-sirikit-images/plist14.png#lightbox)
16. Répétez les étapes ci-dessus pour tout autre terme personnalisé que l’application doit définir.
17. Réduisez `ParameterVocabularies` la clé.
18. Ajoutez la `IntentPhrases` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist15.png "Ajoutez la clé IntentPhrases avec le type de tableau")](implementing-sirikit-images/plist15.png#lightbox)
19. Ajoutez une nouvelle clé avec le **type** `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Ajouter une nouvelle clé avec le type de dictionnaire")](implementing-sirikit-images/plist16.png#lightbox)
20. Ajoutez la `IntentName` clé avec le **type** `String` et l’intention de l’exemple :

    [![](implementing-sirikit-images/plist17.png "Ajouter la clé IntentName avec le type de chaîne et l’intention de l’exemple")](implementing-sirikit-images/plist17.png#lightbox)
21. Ajoutez la `IntentExamples` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist18.png "Ajoutez la clé IntentExamples avec le type de tableau")](implementing-sirikit-images/plist18.png#lightbox)
22. Ajoutez quelques `String` clés avec les exemples d’utilisation du terme :

    [![](implementing-sirikit-images/plist19.png "Ajoutez quelques clés de chaîne avec des exemples d’utilisations du terme")](implementing-sirikit-images/plist19.png#lightbox)
23. Répétez les étapes ci-dessus pour les intentions dont l’application a besoin pour fournir un exemple d’utilisation de.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans le **Explorateur de solutions** , cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter > nouvel élément... > Liste des propriétés Apple > > info. plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Ajouter un nouveau fichier info. plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Double-cliquez sur `AppIntentVocabulary.plist` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
3. Cliquez sur **+** le pour ajouter une clé, définissez le nom `ParameterVocabularies` sur et le type `Array`sur :

    [![](implementing-sirikit-images/plist02w.png "Définissez le nom sur ParameterVocabularies et le type sur Array.")](implementing-sirikit-images/plist02w.png#lightbox)
4. Développez `ParameterVocabularies` et cliquez **+** sur le bouton et définissez le `Dictionary` **type** sur :

    [![](implementing-sirikit-images/plist03w.png "Définir le type sur dictionary")](implementing-sirikit-images/plist03w.png#lightbox)
5. Cliquez sur **+** le pour ajouter une nouvelle clé, définissez le nom `ParameterNames` sur et le type `Array`sur :

    [![](implementing-sirikit-images/plist04w.png "Définissez le nom sur ParameterNames et le type sur Array.")](implementing-sirikit-images/plist04w.png#lightbox)
6. Cliquez sur **+** le pour ajouter une nouvelle clé avec le type `String` de et la valeur comme l’un des noms de paramètres disponibles. Par exemple `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "Clé INStartWorkoutIntent. workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Ajoutez la `ParameterVocabulary` clé à la `ParameterVocabularies` `Array`clé avec le **type** :

    [![](implementing-sirikit-images/plist06w.png "Ajoutez la clé ParameterVocabulary à la clé ParameterVocabularies avec le type de tableau")](implementing-sirikit-images/plist06w.png#lightbox)
8. Ajoutez une nouvelle clé avec le **type** `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Ajouter une nouvelle clé avec le type de dictionnaire")](implementing-sirikit-images/plist07w.png#lightbox)
9. Ajoutez la `VocabularyItemIdentifier` clé avec le **type** `String` et spécifiez un ID unique pour le terme :

    [![](implementing-sirikit-images/plist08w.png "Ajoutez la clé VocabularyItemIdentifier avec le type de chaîne et spécifiez un ID unique pour le terme")](implementing-sirikit-images/plist08w.png#lightbox)
10. Ajoutez la `VocabularyItemSynonyms` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist09w.png "Ajoutez la clé VocabularyItemSynonyms avec le type de tableau")](implementing-sirikit-images/plist09w.png#lightbox)
11. Ajoutez une nouvelle clé avec le **type** `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Ajouter une nouvelle clé avec le type de dictionnaire")](implementing-sirikit-images/plist10w.png#lightbox)
12. Ajoutez la `VocabularyItemPhrase` clé avec le **type** `String` et le terme définis par l’application :

    [![](implementing-sirikit-images/plist11w.png "Ajouter la clé VocabularyItemPhrase avec le type de chaîne et le terme définis par l’application")](implementing-sirikit-images/plist11w.png#lightbox)
13. Ajoutez la `VocabularyItemPronunciation` clé avec le **type** `String` et la prononciation phonétique du terme :

    [![](implementing-sirikit-images/plist12w.png "Ajoutez la clé VocabularyItemPronunciation avec le type de chaîne et la prononciation phonétique du terme")](implementing-sirikit-images/plist12w.png#lightbox)
14. Ajoutez la `VocabularyItemExamples` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist13w.png "Ajoutez la clé VocabularyItemExamples avec le type de tableau")](implementing-sirikit-images/plist13w.png#lightbox)
15. Ajoutez quelques `String` clés avec les exemples d’utilisation du terme :

    [![](implementing-sirikit-images/plist14w.png "Ajoutez quelques clés de chaîne avec des exemples d’utilisations du terme")](implementing-sirikit-images/plist14w.png#lightbox)
16. Répétez les étapes ci-dessus pour tout autre terme personnalisé que l’application doit définir.
17. Réduisez `ParameterVocabularies` la clé.
18. Ajoutez la `IntentPhrases` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist15w.png "Ajoutez la clé IntentPhrases avec le type de tableau")](implementing-sirikit-images/plist15w.png#lightbox)
19. Ajoutez une nouvelle clé avec le **type** `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Ajouter une nouvelle clé avec le type de dictionnaire")](implementing-sirikit-images/plist16w.png#lightbox)
20. Ajoutez la `IntentName` clé avec le **type** `String` et l’intention de l’exemple :

    [![](implementing-sirikit-images/plist17w.png "Ajouter la clé IntentName avec le type de chaîne et l’intention de l’exemple")](implementing-sirikit-images/plist17w.png#lightbox)
21. Ajoutez la `IntentExamples` clé avec le **type** `Array`:

    [![](implementing-sirikit-images/plist18w.png "Ajoutez la clé IntentExamples avec le type de tableau")](implementing-sirikit-images/plist18w.png#lightbox)
22. Ajoutez quelques `String` clés avec les exemples d’utilisation du terme :

    [![](implementing-sirikit-images/plist19w.png "Ajoutez quelques clés de chaîne avec des exemples d’utilisations du terme")](implementing-sirikit-images/plist19w.png#lightbox)
23. Répétez les étapes ci-dessus pour les intentions dont l’application a besoin pour fournir un exemple d’utilisation de.

-----

> [!IMPORTANT]
> Le `AppIntentVocabulary.plist` sera inscrit avec Siri sur les appareils de test pendant le développement et l’intégration du vocabulaire personnalisé peut prendre un certain temps. Par conséquent, le testeur doit patienter quelques minutes avant de tenter de tester le vocabulaire spécifique à l’application lorsqu’il a été mis à jour.

Pour plus d’informations, consultez notre [référence de vocabulaire spécifique](~/ios/platform/sirikit/understanding-sirikit.md) à l’application et la [référence de vocabulaire personnalisé](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)d’Apple.

## <a name="adding-an-intents-extension"></a>Ajout d’une extension intentions

Maintenant que l’application a été préparée à adopter SiriKit, le développeur doit ajouter une ou plusieurs extensions intentions à la solution pour gérer les intentions requises pour l’intégration de Siri.

Pour chaque extension astentes requise, procédez comme suit :

- Ajoutez un projet d’extension intentions à la solution d’application Xamarin. iOS.
- Configurez le fichier d' `Info.plist` extension intentions.
- Modifiez la classe principale de l’extension intentions.

Pour plus d’informations, consultez notre [référence d’extension intentions](~/ios/platform/sirikit/understanding-sirikit.md) et la [référence d’extension création](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)d’apples.

### <a name="creating-the-extension"></a>Création de l’extension

Pour ajouter une extension intentions à la solution, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **panneau solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...** .
2. Dans la boîte de dialogue, sélectionnez**extension d’intention** des**Extensions** >  **iOS** > et cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents05.png "Sélectionner une extension d’intention")](implementing-sirikit-images/intents05.png#lightbox)
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents06.png "Entrer un nom pour l’extension d’intention")](implementing-sirikit-images/intents06.png#lightbox)
4. Enfin, cliquez sur le bouton **créer** pour ajouter l’extension d’intention à la solution applications : 

    [![](implementing-sirikit-images/intents07.png "Ajouter l’extension d’intention à la solution apps")](implementing-sirikit-images/intents07.png#lightbox)
5. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension intention nouvellement créée. Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/intents08.png "Sélectionner le nom du projet de bibliothèque de code partagé commun")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...** .
2. Dans la boîte de dialogue, sélectionnez  **C# Visual > extensions iOS > extension intention** , puis cliquez sur le bouton **suivant** :

    [![](implementing-sirikit-images/intents05.w157-sml.png "Sélectionner une extension d’intention")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **OK** .
4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension Intents nouvellement créée, puis choisissez **Ajouter une référence de >** . Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** :

    [![](implementing-sirikit-images/intents08w.png "Sélectionner le nom du projet de bibliothèque de code partagé commun")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Répétez ces étapes pour le nombre d’extensions d’intention (selon la section [architecture de l’application pour les extensions](#architecting-the-app-for-extensions) ci-dessus) dont l’application a besoin.

### <a name="configuring-the-infoplist"></a>Configuration du fichier info. plist

Pour chacune des extensions d’intentions ajoutées à la solution de l’application, doit être configuré dans les `Info.plist` fichiers pour fonctionner avec l’application.

À l’instar de toute extension d’application typique, l’application aura les `NSExtension` clés `NSExtensionAttributes`existantes de et. Pour une extension intentions, deux nouveaux attributs doivent être configurés :

[![](implementing-sirikit-images/intents01.png "Les deux nouveaux attributs qui doivent être configurés")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -est requis et se compose d’un tableau de noms de classes d’intention que l’application souhaite prendre en charge à partir de l’extension d’intention.
- **IntentsRestrictedWhileLocked** : clé facultative permettant à l’application de spécifier le comportement de l’écran de verrouillage de l’extension. Il se compose d’un tableau de noms de classes d’intention que l’application veut exiger que l’utilisateur soit connecté pour utiliser à partir de l’extension d’intention.

Pour configurer le fichier de `Info.plist` l’extension d’intention, double-cliquez dessus dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Ensuite, basculez vers la vue **source** , puis `NSExtension` développez les clés et `NSExtensionAttributes` dans l’éditeur :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "Clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "Clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Développez `IntentsSupported` la clé et ajoutez le nom de toute classe d’intention prise en charge par cette extension. Pour l’exemple d’application MonkeyChat, il prend `INSendMessageIntent`en charge les éléments suivants :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "Clé INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "Clé INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Si l’application exige éventuellement que l’utilisateur soit connecté à l’appareil pour utiliser une intention donnée, développez la `IntentRestrictedWhileLocked` clé et ajoutez les noms de classe des intentions ayant un accès restreint. Pour l’exemple d’application MonkeyChat, l’utilisateur doit être connecté pour envoyer un message de conversation afin que nous `INSendMessageIntent`ayons ajouté :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "Clé INSendMessageIntent ajoutée")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "Clé INSendMessageIntent ajoutée")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Pour obtenir la liste complète des domaines d’intention disponibles, consultez la [référence](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)sur les domaines d’intention d’Apple.

### <a name="configuring-the-main-class"></a>Configuration de la classe principale

Ensuite, le développeur doit configurer la classe principale qui agit comme point d’entrée principal pour l’extension d’intention dans Siri. Il doit s’agir d’une sous `INExtension` -classe de qui est conforme `IINIntentHandler` au délégué. Par exemple :

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

Il existe une méthode solitaires que l’application doit implémenter sur la classe principale d’extension d' `GetHandler` intention, la méthode. Cette méthode est passée par SiriKit et l’application doit retourner un gestionnaire d' **intentions** qui correspond au type de l’intention donnée.

Étant donné que l’exemple d’application MonkeyChat ne gère qu’une seule intention, elle `GetHandler` se retourne elle-même dans la méthode. Si l’extension gérait plusieurs intentions, le développeur ajoute une classe pour chaque type d’intention et retourne une instance en fonction du `Intent` passé à la méthode.

### <a name="handling-the-resolve-stage"></a>Gestion de l’étape de résolution

L’étape de résolution est l’endroit où l’extension d’intention clarifie et valide les paramètres transmis à partir de Siri et qui ont été définis par le biais de la conversation de l’utilisateur.

Pour chaque paramètre qui est envoyé à partir de Siri, il `Resolve` existe une méthode. L’application doit implémenter cette méthode pour chaque paramètre pour lequel l’application peut avoir besoin de l’aide de Siri pour obtenir la réponse correcte de l’utilisateur.

Dans le cas de l’exemple d’application MonkeyChat, l’extension d’intention requiert un ou plusieurs destinataires auxquels envoyer le message. Pour chaque destinataire de la liste, l’extension doit effectuer une recherche de contact qui peut avoir les résultats suivants :

- Un seul contact correspondant est trouvé.
- Au moins deux contacts correspondants sont trouvés.
- Aucun contact correspondant n’a été trouvé.

En outre, MonkeyChat requiert du contenu pour le corps du message. Si l’utilisateur ne l’a pas fourni, Siri doit inviter l’utilisateur à entrer son contenu.

L’extension d’intention devra gérer harmonieusement chacun de ces cas.


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

Pour plus d’informations, consultez la référence de [la phase de résolution et la](~/ios/platform/sirikit/understanding-sirikit.md) [référence aux intentions de résolution et de gestion](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)d’Apple.

### <a name="handling-the-confirm-stage"></a>Gestion de l’étape de confirmation

L’étape de confirmation est l’endroit où l’extension d’intention vérifie qu’elle dispose de toutes les informations pour traiter la demande de l’utilisateur. L’application souhaite envoyer une confirmation avec tous les détails de la prise en charge de ce qui va se produire à Siri. elle peut donc être confirmée avec l’utilisateur qu’il s’agit de l’action prévue.

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

Pour plus d’informations, consultez [la référence de l’étape de confirmation](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>Traitement de l’intention

C’est là que l’extension d’intention effectue réellement la tâche pour répondre à la demande de l’utilisateur et renvoie les résultats à Siri pour que l’utilisateur puisse être informé.


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

Pour plus d’informations, consultez [la référence de la phase handle](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Ajout d’une extension d’interface utilisateur intentions

L’extension d’interface utilisateur Intents facultative présente l’opportunité d’amener l’interface utilisateur de l’application et de la marque dans l’expérience Siri et de rendre les utilisateurs bien connectés à l’application. Avec cette extension, l’application peut faire de la réputation, ainsi que des informations visuelles et autres, dans la transcription.

[![](implementing-sirikit-images/intentsui01.png "Exemple de sortie d’une extension d’interface utilisateur Intent")](implementing-sirikit-images/intentsui01.png#lightbox)

Tout comme l’extension intentions, le développeur effectue l’étape suivante pour l’extension d’interface utilisateur Intents :

- Ajoutez un projet d’extension d’interface utilisateur Intents à la solution d’application Xamarin. iOS.
- Configurez le fichier d’extension `Info.plist` d’interface utilisateur Intents.
- Modifiez la classe principale d’extension d’interface utilisateur Intent.

Pour plus d’informations, consultez notre [référence d’extension d’interface utilisateur Intents](~/ios/platform/sirikit/understanding-sirikit.md) et Apple [fournit une référence d’interface personnalisée](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Création de l’extension

Pour ajouter une extension d’interface utilisateur intentions à la solution, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **panneau solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...** .
2. Dans la boîte de dialogue, sélectionnez extension de l'**interface utilisateur** de l’intention des**Extensions** >  **iOS** > , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents11.png "Sélectionner l’extension d’interface utilisateur de l’intention")](implementing-sirikit-images/intents11.png#lightbox)
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents12.png "Entrer un nom pour l’extension d’intention")](implementing-sirikit-images/intents12.png#lightbox)
4. Enfin, cliquez sur le bouton **créer** pour ajouter l’extension d’intention à la solution applications : 

    [![](implementing-sirikit-images/intents13.png "Ajouter l’extension d’intention à la solution apps")](implementing-sirikit-images/intents13.png#lightbox)
5. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension intention nouvellement créée. Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/intents14.png "Sélectionner le nom du projet de bibliothèque de code partagé commun")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...**
2. Dans la boîte de dialogue, sélectionnez extension de l'**interface utilisateur** de l’intention des**Extensions** >  **iOS** > , puis cliquez sur le bouton **suivant** .
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **OK** .
4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension intention nouvellement créée. Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** .
    
-----

### <a name="configuring-the-infoplist"></a>Configuration du fichier info. plist

Configurez le fichier de `Info.plist` l’extension d’interface utilisateur Intents pour qu’il fonctionne avec l’application.

À l’instar de toute extension d’application typique, l’application aura les `NSExtension` clés `NSExtensionAttributes`existantes de et. Pour une extension intentions, un nouvel attribut doit être configuré :

[![](implementing-sirikit-images/intents03.png "Un nouvel attribut qui doit être configuré")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** est obligatoire et se compose d’un tableau de noms de classes d’intention que l’application souhaite prendre en charge à partir de l’extension d’intention.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour configurer le fichier de l’extension `Info.plist` d’interface utilisateur intentionnelle, double-cliquez dessus dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Ensuite, basculez vers la vue **source** , puis `NSExtension` développez les clés et `NSExtensionAttributes` dans l’éditeur :

[![](implementing-sirikit-images/intents04.png "Clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour configurer le fichier de l’extension `Info.plist` d’interface utilisateur intentionnelle, double-cliquez dessus dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Développez `NSExtension` les `NSExtensionAttributes` clés et dans l’éditeur :

[![](implementing-sirikit-images/intents04w.png "Les clés NSExtension et NSExtensionAttributes dans l’éditeur")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Développez `IntentsSupported` la clé et ajoutez le nom de toute classe d’intention prise en charge par cette extension. Pour l’exemple d’application MonkeyChat, il prend `INSendMessageIntent`en charge les éléments suivants :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "Clé INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "Clé INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Pour obtenir la liste complète des domaines d’intention disponibles, consultez la [référence](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)sur les domaines d’intention d’Apple.

### <a name="configuring-the-main-class"></a>Configuration de la classe principale

Configurez la classe principale qui agit comme point d’entrée principal pour l’extension d’interface utilisateur intentionnelle dans Siri. Il doit s’agir d’une sous `UIViewController` -classe de qui est conforme `IINUIHostedViewController` à l’interface. Par exemple :

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

Siri passera une `INInteraction` instance de classe à la `Configure` méthode de l' `UIViewController` instance à l’intérieur de l’extension d’interface utilisateur Intent.

L' `INInteraction` objet fournit trois éléments d’information clés à l’extension :

1. Objet d’intention en cours de traitement.
2. Objet de réponse intentionnelle à partir `Confirm` des `Handle` méthodes et de l’extension d’intention.
3. État d’interaction qui définit l’état de l’interaction entre l’application et Siri.

L' `UIViewController` instance est la classe principale de l’interaction avec Siri et, étant donné qu’elle `UIViewController`hérite de, elle a accès à toutes les fonctionnalités de uikit.

Quand Siri appelle la `Configure` méthode `UIViewController` du service informatique passe dans un contexte d’affichage indiquant que le contrôleur d’affichage sera hébergé dans une carte Siri Snippit ou Maps.

Siri passera également un gestionnaire d’achèvement dont l’application a besoin pour retourner la taille souhaitée de la vue une fois que l’application a terminé de la configurer.

### <a name="design-the-ui-in-ios-designer"></a>Concevoir l’interface utilisateur dans le concepteur iOS

Disposition de l’interface utilisateur de l’extension d’interface utilisateur Intents dans le concepteur iOS. Double-cliquez sur le `MainInterface.storyboard` fichier de l’extension dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Faites glisser dans tous les éléments d’interface utilisateur requis pour créer l’interface utilisateur et enregistrer les modifications.

> [!IMPORTANT]
> Bien qu’il soit possible d’ajouter des éléments interactifs tels que `UIButtons` ou `UITextFields` à l' `UIViewController`extension d’interface utilisateur intentionnelle, ceux-ci sont strictement interdits, car l’interface utilisateur de l’intention n’est pas interactive et l’utilisateur ne peut pas interagir avec eux.

### <a name="wire-up-the-user-interface"></a>Relier l’interface utilisateur

Avec l’interface utilisateur de l’extension d’IU Intent créée dans le concepteur iOS, `UIViewController` modifiez la sous-classe et `Configure` substituez la méthode comme suit :

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

### <a name="overriding-the-default-siri-ui"></a>Substitution de l’interface utilisateur Siri par défaut

L’extension d’interface utilisateur intentions sera toujours affichée avec d’autres contenus Siri tels que l’icône et le nom de l’application, en haut de l’interface utilisateur ou, en fonction de l’intention, les boutons (comme envoyer ou annuler) peuvent s’afficher en bas.

Il existe quelques cas où l’application peut remplacer les informations que Siri affiche par défaut à l’utilisateur, par exemple la messagerie ou les mappages où l’application peut remplacer l’expérience par défaut par une option adaptée à l’application.

Si l’extension d’interface utilisateur intentes doit substituer des éléments de l’interface utilisateur Siri `UIViewController` par défaut, la sous-classe `IINUIHostedViewSiriProviding` doit implémenter l’interface et s’abonner à l’affichage d’un élément d’interface particulier.

Ajoutez le code suivant à la `UIViewController` sous-classe pour indiquer à Siri que l’extension d’interface utilisateur Intent affiche déjà le contenu du message :

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considérations

Apple suggère que le développeur prend en compte les points suivants lors de la conception et de l’implémentation des extensions de l’interface utilisateur intentionnelle :

- **Soyez conscient de l’utilisation de la mémoire** -étant donné que les extensions d’interface utilisateur Intent sont temporaires et affichées uniquement pendant une brève période, le système impose des contraintes de mémoire plus strictes que celles utilisées avec une application complète.
- **Envisagez les tailles de vue minimales et maximales** : Assurez-vous que les extensions d’interface utilisateur sont correctes sur chaque type, taille et orientation d’appareil iOS. En outre, la taille souhaitée que l’application renvoie à Siri peut ne pas pouvoir être accordée.
- **Utilisez des modèles de disposition flexibles et adaptatifs** : encore une fois pour vous assurer que l’interface utilisateur est bien adaptée à tous les appareils.

## <a name="summary"></a>Récapitulatif

Cet article a abordé SiriKit et montre comment il peut être ajouté aux applications Xamarin. iOS pour fournir des services accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS.




## <a name="related-links"></a>Liens associés

- [Exemple ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guide de programmation SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informations de référence sur l’infrastructure Intent](https://developer.apple.com/reference/intents)
- [Informations de référence sur l’infrastructure d’interface utilisateur](https://developer.apple.com/reference/intentsui)
- [Informations de référence sur les domaines d’intention](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)

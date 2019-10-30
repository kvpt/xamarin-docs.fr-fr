---
title: Implémentation de SiriKit dans Xamarin. iOS
description: Ce document décrit les étapes requises pour implémenter la prise en charge de SiriKit dans une application Xamarin. iOS. Il aborde les extensions intentions et les extensions d’interface utilisateur des intentions.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: a80caca0b8c8c48a468b20f63467357300bd6de1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031645"
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

Lorsque l’utilisateur effectue une requête de Siri impliquant l’un des services de l’extension d’application, SiriKit envoie à l’extension un objet d' **intention** qui décrit la demande de l’utilisateur ainsi que toutes les données de prise en charge. L’extension d’application génère ensuite l’objet de **réponse** approprié pour l' **intention**donnée, en détaillant la manière dont l’extension peut traiter la demande.

Ce guide présente un exemple rapide d’intégration de la prise en charge de SiriKit dans une application existante. Pour les besoins de cet exemple, nous allons utiliser l’application factice MonkeyChat :

[![](implementing-sirikit-images/monkeychat01.png "The MonkeyChat icon")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat conserve son propre livre de contacts des amis de l’utilisateur, chacun associé à un nom d’écran (comme Bobo, par exemple) et permet à l’utilisateur d’envoyer des conversations textuelles à chaque ami en fonction de leur nom d’écran.

## <a name="extending-the-app-with-sirikit"></a>Extension de l’application avec SiriKit

Comme indiqué dans le Guide de [Présentation des concepts de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) , il existe trois parties principales impliquées dans l’extension d’une application avec SiriKit :

[![](implementing-sirikit-images/elements01.png "Extending the App with SiriKit diagram")](implementing-sirikit-images/elements01.png#lightbox)

Elles incluent notamment les suivantes :

1. **Extension des intentions** -vérifie les réponses des utilisateurs, confirme que l’application peut traiter la demande et effectue en fait la tâche pour répondre à la demande de l’utilisateur.
2. L' **extension d’IU Intent** - *facultative*, fournit une interface utilisateur personnalisée aux réponses dans l’environnement Siri et peut amener l’interface utilisateur de l’application et la personnaliser à Siri pour enrichir l’expérience de l’utilisateur.
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

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Démarrez Visual Studio pour Mac et ouvrez l’application MonkeyChat.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **panneau solutions** puis sélectionnez **Ajouter** > **nouveau projet...** : 

    [![](implementing-sirikit-images/prep01.png "Add a new project")](implementing-sirikit-images/prep01.png#lightbox)
3. Sélectionnez **bibliothèque de > ** **iOS** > **bibliothèque de classes** , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/prep02.png "Select Class Library")](implementing-sirikit-images/prep02.png#lightbox)
4. Entrez `MonkeyChatCommon` pour le **nom** et cliquez sur le bouton **créer** : 

    [![](implementing-sirikit-images/prep03.png "Enter MonkeyChatCommon for the Name")](implementing-sirikit-images/prep03.png#lightbox)
5. Cliquez avec le bouton droit sur le dossier **références** de l’application principale dans le **Explorateur de solutions** puis sélectionnez **modifier les références...** . Vérifiez le projet **MonkeyChatCommon** et cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/prep05.png "Check the MonkeyChatCommon project")](implementing-sirikit-images/prep05.png#lightbox)
6. Dans le **Explorateur de solutions**, faites glisser le code partagé commun de l’application principale vers la bibliothèque native.
7. Dans le cas de MonkeyChat, faites glisser les dossiers **datamodels** et **Processors** de l’application principale vers la bibliothèque Native : 

    [![](implementing-sirikit-images/prep06.png "The DataModels and Processors folders in the Solution Explorer")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Démarrez Visual Studio et ouvrez l’application MonkeyChat.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **nouveau projet...** .
3. Sélectionnez **Visual C#**  > **projet partagé** , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Select Class Library")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Entrez `MonkeyChatCommon` pour le **nom** et cliquez sur le bouton **créer** .
5. Cliquez avec le bouton droit sur le dossier **références** de l’application principale dans le **Explorateur de solutions** puis sélectionnez **modifier les références...** . Vérifiez le projet **MonkeyChatCommon** et cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/prep05w.png "Check the MonkeyChatCommon project")](implementing-sirikit-images/prep05w.png#lightbox)
6. Dans le **Explorateur de solutions**, faites glisser le code partagé commun de l’application principale vers le projet partagé.
7. Dans le cas de MonkeyChat, faites glisser les dossiers **datamodels** et **Processors** de l’application principale vers la bibliothèque native.

-----

Modifiez l’un des fichiers qui ont été déplacés vers la bibliothèque native et modifiez l’espace de noms pour qu’il corresponde à celui de la bibliothèque. Par exemple, la modification de `MonkeyChat` `MonkeyChatCommon`:

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

Ensuite, revenez à l’application principale et ajoutez une instruction `using` pour l’espace de noms de la bibliothèque Native partout où l’application utilise l’une des classes qui ont été déplacées :

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

1. Double-cliquez sur le fichier `Entitlements.plist` dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Basculez vers l’onglet **source** .
3. Ajoutez la **propriété**`com.apple.developer.siri`, définissez le **type** sur `Boolean` et la **valeur** sur `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Add the com.apple.developer.siri Property")](implementing-sirikit-images/setup01.png#lightbox)
4. Enregistrez les modifications dans le fichier.
5. Double-cliquez sur le **fichier projet** dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
6. Sélectionnez **signature du bundle iOS** et assurez-vous que le fichier `Entitlements.plist` est sélectionné dans le champ **droits personnalisés** : 

    [![](implementing-sirikit-images/setup02.png "Select the Entitlements.plist file in the Custom Entitlements field")](implementing-sirikit-images/setup02.png#lightbox)
7. Cliquez sur le bouton **OK** pour enregistrer les changements.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le fichier `Entitlements.plist` dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Ajoutez la **propriété**`com.apple.developer.siri`, définissez le **type** sur `Boolean` et la **valeur** sur `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Add the com.apple.developer.siri Property")](implementing-sirikit-images/setup01w.png#lightbox)
3. Enregistrez les modifications dans le fichier.
4. Double-cliquez sur le **fichier projet** dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
5. Sélectionnez **signature du bundle iOS** et assurez-vous que le fichier `Entitlements.plist` est sélectionné dans le champ **droits personnalisés** .

-----

Lorsque vous avez terminé, le fichier `Entitlements.plist` de l’application doit ressembler à ce qui suit (ouvert dans un éditeur externe) :

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

1. Dans un navigateur Web, accédez à [https://developer.apple.com](https://developer.apple.com) et connectez-vous à votre compte.
2. Cliquez sur **certificats**, **identificateurs** et **profils**.
3. Sélectionnez **Configuration des profils** et sélectionnez **ID d’application**, puis cliquez sur le bouton **+** .
4. Entrez un **nom** pour le nouveau profil.
5. Entrez un **ID de Bundle** suivant la recommandation d’attribution de noms d’Apple.
6. Faites défiler jusqu’à la section **app services** , sélectionnez **SiriKit** , puis cliquez sur le bouton **continue** : 

    [![](implementing-sirikit-images/setup03.png "Select SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Vérifiez tous les paramètres, puis **soumettez** l’ID de l’application.
8. Sélectionnez **Configuration des profils** > **développement**, cliquez sur le bouton **+** , sélectionnez l' **ID Apple**, puis cliquez sur **Continuer**.
9. Cliquez sur sélectionner **tout**, puis sur **Continuer**.
10. Cliquez à nouveau sur **Sélectionner** , puis sur **Continuer**.
11. Entrez un **nom de profil** à l’aide des suggestions d’affectation de noms d’Apple, puis cliquez sur **Continuer**.
12. Démarrez Xcode.
13. Dans le menu Xcode **, sélectionnez Préférences...**
14. Sélectionnez **comptes**, puis cliquez sur **afficher les détails...** bouton 

    [![](implementing-sirikit-images/setup04.png "Select Accounts")](implementing-sirikit-images/setup04.png#lightbox)
15. Cliquez sur le bouton **Télécharger tous les profils** dans le coin inférieur gauche : 

    [![](implementing-sirikit-images/setup05.png "Download All Profiles")](implementing-sirikit-images/setup05.png#lightbox)
16. Assurez-vous que le **profil de provisionnement** créé ci-dessus a été installé dans Xcode.
17. Ouvrez le projet auquel ajouter la prise en charge SiriKit dans Visual Studio pour Mac.
18. Double-cliquez sur le fichier `Info.plist` dans le **Explorateur de solutions**.
19. Assurez-vous que l' **identificateur de Bundle** correspond à celui créé dans le portail des développeurs d’Apple ci-dessus : 

    [![](implementing-sirikit-images/setup06.png "The Bundle Identifier")](implementing-sirikit-images/setup06.png#lightbox)
20. Dans le **Explorateur de solutions**, sélectionnez le **projet**.
21. Cliquez avec le bouton droit sur le projet et sélectionnez **options**.
22. Sélectionnez **signature du bundle iOS**, sélectionnez l' **identité de signature** et le profil de **provisionnement** créés ci-dessus : 

    [![](implementing-sirikit-images/setup07.png "Select the Signing Identity and Provisioning Profile")](implementing-sirikit-images/setup07.png#lightbox)
23. Cliquez sur le bouton **OK** pour enregistrer les changements.

> [!IMPORTANT]
> Le test de SiriKit fonctionne uniquement sur un périphérique matériel iOS 10 réel et non dans le simulateur iOS 10. Si vous rencontrez des problèmes lors de l’installation d’une application Xamarin. iOS activée pour SiriKit sur du matériel réel, assurez-vous que les droits, l’ID d’application, l’identificateur de signature et le profil de provisionnement requis ont été correctement configurés dans le portail des développeurs d’Apple et dans Visual Studio pour Mac.

### <a name="requesting-siri-authorization"></a>Demande d’autorisation Siri

Avant que l’application n’ajoute de vocabulaire spécifique à l’utilisateur ou que les extensions intentions se connectent à Siri, elle doit demander l’autorisation à l’utilisateur d’accéder à Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Modifiez le fichier `Info.plist` de l’application, basculez vers le mode **source** et ajoutez la clé `NSSiriUsageDescription` avec une valeur de chaîne décrivant la manière dont l’application utilise Siri et les types de données qui seront envoyés. Par exemple, l’application MonkeyChat peut indiquer « les contacts MonkeyChat sont envoyés à Siri » :

[![](implementing-sirikit-images/request01.png "The NSSiriUsageDescription in the Info.plist editor")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Modifiez le fichier `Info.plist` de l’application et ajoutez la clé `NSSiriUsageDescription` avec une valeur de chaîne décrivant la manière dont l’application utilise Siri et les types de données qui seront envoyés. Par exemple, l’application MonkeyChat peut indiquer « les contacts MonkeyChat sont envoyés à Siri » :

[![](implementing-sirikit-images/request01w.png "The NSSiriUsageDescription in the Info.plist editor")](implementing-sirikit-images/request01w.png#lightbox)

-----

Appelez la méthode `RequestSiriAuthorization` de la classe `INPreferences` lors du premier démarrage de l’application. Modifiez la classe `AppDelegate.cs` et faites en sorte que la méthode `FinishedLaunching` ressemble à ce qui suit :

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

La première fois que cette méthode est appelée, une alerte s’affiche pour inviter l’utilisateur à autoriser l’application à accéder à Siri. Message que le développeur a ajouté au `NSSiriUsageDescription` ci-dessus s’affiche dans cette alerte. Si l’utilisateur refuse l’accès initialement, il peut utiliser l’application **paramètres** pour accorder l’accès à l’application.

À tout moment, l’application peut vérifier la capacité de l’application à accéder à Siri en appelant la méthode `SiriAuthorizationStatus` de la classe `INPreferences`.

### <a name="localization-and-siri"></a>Localisation et Siri

Sur un appareil iOS, l’utilisateur est en mesure de sélectionner une langue pour Siri différente de la valeur système par défaut. Lorsque vous travaillez avec des données localisées, l’application doit utiliser la méthode `SiriLanguageCode` de la classe `INPreferences` pour récupérer le code de langue à partir de Siri. Exemple :

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

L’application fournit le vocabulaire spécifique à l’utilisateur en appelant la méthode `SetVocabularyStrings` de la classe `INVocabulary` et en passant une collection `NSOrderedSet` à partir de l’application principale. L’application doit toujours appeler la méthode `RemoveAllVocabularyStrings` en premier, pour supprimer les conditions existantes avant d’en ajouter de nouvelles. Exemple :

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

Le vocabulaire spécifique à l’application définit les mots et expressions spécifiques qui seront connus de tous les utilisateurs de l’application, tels que les types de véhicules ou les noms d’entraînements. Dans la mesure où ils font partie de l’application, ils sont définis dans un fichier `AppIntentVocabulary.plist` dans le cadre de l’offre groupée d’applications principale. En outre, ces mots et ces expressions doivent être localisés.

Les termes du vocabulaire spécifique à l’application doivent appartenir à l’une des catégories suivantes :

- Options d’annulation.
- Noms des exercices.

Le fichier vocabulaire propre à l’application contient deux clés de niveau racine :

- `ParameterVocabularies` **requis** : définit les conditions personnalisées et les paramètres d’intention personnalisés de l’application à laquelle ils s’appliquent.
- `IntentPhrases` **facultatif** : contient des exemples d’expressions utilisant les termes personnalisés définis dans le `ParameterVocabularies`.

Chaque entrée de la `ParameterVocabularies` doit spécifier une chaîne d’ID, un terme et l’intention à laquelle le terme s’applique. En outre, un terme unique peut s’appliquer à plusieurs intentions.

Pour obtenir la liste complète des valeurs acceptables et la structure des fichiers requise, consultez [Référence du format de fichier du vocabulaire d’application](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)Apple.

Pour ajouter un fichier de `AppIntentVocabulary.plist` au projet d’application, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans le **Explorateur de solutions** , cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter** > **nouveau fichier...**  > **iOS**:

    [![](implementing-sirikit-images/plist01.png "Add a property list")](implementing-sirikit-images/plist01.png#lightbox)
2. Double-cliquez sur le fichier `AppIntentVocabulary.plist` dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
3. Cliquez sur la **+** pour ajouter une clé, définissez le **nom** sur `ParameterVocabularies` et le **type** sur `Array`:

    [![](implementing-sirikit-images/plist02.png "Set the Name to ParameterVocabularies and the Type to Array")](implementing-sirikit-images/plist02.png#lightbox)
4. Développez `ParameterVocabularies`, cliquez sur le bouton **+** et définissez le **type** sur `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Set the Type to Dictionary")](implementing-sirikit-images/plist03.png#lightbox)
5. Cliquez sur la **+** pour ajouter une nouvelle clé, définissez le **nom** sur `ParameterNames` et le **type** sur `Array`:

    [![](implementing-sirikit-images/plist04.png "Set the Name to ParameterNames and the Type to Array")](implementing-sirikit-images/plist04.png#lightbox)
6. Cliquez sur la **+** pour ajouter une nouvelle clé avec le **type** de `String` et la valeur comme l’un des noms de paramètres disponibles. Par exemple, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05.png#lightbox)
7. Ajoutez la clé de `ParameterVocabulary` à la clé de `ParameterVocabularies` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist06.png "Add the ParameterVocabulary key to the ParameterVocabularies key with the Type of Array")](implementing-sirikit-images/plist06.png#lightbox)
8. Ajoutez une nouvelle clé avec le **type** de `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist07.png#lightbox)
9. Ajoutez la clé de `VocabularyItemIdentifier` avec le **type** de `String` et spécifiez un ID unique pour le terme :

    [![](implementing-sirikit-images/plist08.png "Add the VocabularyItemIdentifier key with the Type of String and specify a unique ID")](implementing-sirikit-images/plist08.png#lightbox)
10. Ajoutez la clé de `VocabularyItemSynonyms` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist09.png "Add the VocabularyItemSynonyms key with the Type of Array")](implementing-sirikit-images/plist09.png#lightbox)
11. Ajoutez une nouvelle clé avec le **type** de `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist10.png#lightbox)
12. Ajoutez la clé de `VocabularyItemPhrase` avec le **type** de `String` et le terme défini par l’application :

    [![](implementing-sirikit-images/plist11.png "Add the VocabularyItemPhrase key with the Type of String and the term the app are defining")](implementing-sirikit-images/plist11.png#lightbox)
13. Ajoutez la clé de `VocabularyItemPronunciation` avec le **type** de `String` et la prononciation phonétique du terme :

    [![](implementing-sirikit-images/plist12.png "Add the VocabularyItemPronunciation key with the Type of String and the phonetic pronunciation of the term")](implementing-sirikit-images/plist12.png#lightbox)
14. Ajoutez la clé de `VocabularyItemExamples` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist13.png "Add the VocabularyItemExamples key with the Type of Array")](implementing-sirikit-images/plist13.png#lightbox)
15. Ajoutez quelques clés de `String` avec des exemples d’utilisations du terme :

    [![](implementing-sirikit-images/plist14.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist14.png#lightbox)
16. Répétez les étapes ci-dessus pour tout autre terme personnalisé que l’application doit définir.
17. Réduisez la clé de `ParameterVocabularies`.
18. Ajoutez la clé de `IntentPhrases` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist15.png "Add the IntentPhrases key with the Type of Array")](implementing-sirikit-images/plist15.png#lightbox)
19. Ajoutez une nouvelle clé avec le **type** de `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist16.png#lightbox)
20. Ajoutez la clé de `IntentName` avec le **type** de `String` et l’intention de l’exemple :

    [![](implementing-sirikit-images/plist17.png "Add the IntentName key with the Type of String and Intent for the example")](implementing-sirikit-images/plist17.png#lightbox)
21. Ajoutez la clé de `IntentExamples` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist18.png "Add the IntentExamples key with the Type of Array")](implementing-sirikit-images/plist18.png#lightbox)
22. Ajoutez quelques clés de `String` avec des exemples d’utilisations du terme :

    [![](implementing-sirikit-images/plist19.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist19.png#lightbox)
23. Répétez les étapes ci-dessus pour les intentions dont l’application a besoin pour fournir un exemple d’utilisation de.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans le **Explorateur de solutions** , cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Ajouter > nouvel élément... > Liste des propriétés Apple > > info. plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Add a new Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Double-cliquez sur le fichier `AppIntentVocabulary.plist` dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
3. Cliquez sur la **+** pour ajouter une clé, définissez le **nom** sur `ParameterVocabularies` et le **type** sur `Array`:

    [![](implementing-sirikit-images/plist02w.png "Set the Name to ParameterVocabularies and the Type to Array")](implementing-sirikit-images/plist02w.png#lightbox)
4. Développez `ParameterVocabularies`, cliquez sur le bouton **+** et définissez le **type** sur `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Set the Type to Dictionary")](implementing-sirikit-images/plist03w.png#lightbox)
5. Cliquez sur la **+** pour ajouter une nouvelle clé, définissez le **nom** sur `ParameterNames` et le **type** sur `Array`:

    [![](implementing-sirikit-images/plist04w.png "Set the Name to ParameterNames and the Type to Array")](implementing-sirikit-images/plist04w.png#lightbox)
6. Cliquez sur la **+** pour ajouter une nouvelle clé avec le **type** de `String` et la valeur comme l’un des noms de paramètres disponibles. Par exemple, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05w.png#lightbox)
7. Ajoutez la clé de `ParameterVocabulary` à la clé de `ParameterVocabularies` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist06w.png "Add the ParameterVocabulary key to the ParameterVocabularies key with the Type of Array")](implementing-sirikit-images/plist06w.png#lightbox)
8. Ajoutez une nouvelle clé avec le **type** de `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist07w.png#lightbox)
9. Ajoutez la clé de `VocabularyItemIdentifier` avec le **type** de `String` et spécifiez un ID unique pour le terme :

    [![](implementing-sirikit-images/plist08w.png "Add the VocabularyItemIdentifier key with the Type of String and specify a unique ID for the term")](implementing-sirikit-images/plist08w.png#lightbox)
10. Ajoutez la clé de `VocabularyItemSynonyms` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist09w.png "Add the VocabularyItemSynonyms key with the Type of Array")](implementing-sirikit-images/plist09w.png#lightbox)
11. Ajoutez une nouvelle clé avec le **type** de `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist10w.png#lightbox)
12. Ajoutez la clé de `VocabularyItemPhrase` avec le **type** de `String` et le terme défini par l’application :

    [![](implementing-sirikit-images/plist11w.png "Add the VocabularyItemPhrase key with the Type of String and the term the app are defining")](implementing-sirikit-images/plist11w.png#lightbox)
13. Ajoutez la clé de `VocabularyItemPronunciation` avec le **type** de `String` et la prononciation phonétique du terme :

    [![](implementing-sirikit-images/plist12w.png "Add the VocabularyItemPronunciation key with the Type of String and the phonetic pronunciation of the term")](implementing-sirikit-images/plist12w.png#lightbox)
14. Ajoutez la clé de `VocabularyItemExamples` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist13w.png "Add the VocabularyItemExamples key with the Type of Array")](implementing-sirikit-images/plist13w.png#lightbox)
15. Ajoutez quelques clés de `String` avec des exemples d’utilisations du terme :

    [![](implementing-sirikit-images/plist14w.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist14w.png#lightbox)
16. Répétez les étapes ci-dessus pour tout autre terme personnalisé que l’application doit définir.
17. Réduisez la clé de `ParameterVocabularies`.
18. Ajoutez la clé de `IntentPhrases` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist15w.png "Add the IntentPhrases key with the Type of Array")](implementing-sirikit-images/plist15w.png#lightbox)
19. Ajoutez une nouvelle clé avec le **type** de `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist16w.png#lightbox)
20. Ajoutez la clé de `IntentName` avec le **type** de `String` et l’intention de l’exemple :

    [![](implementing-sirikit-images/plist17w.png "Add the IntentName key with the Type of String and Intent for the example")](implementing-sirikit-images/plist17w.png#lightbox)
21. Ajoutez la clé de `IntentExamples` avec le **type** de `Array`:

    [![](implementing-sirikit-images/plist18w.png "Add the IntentExamples key with the Type of Array")](implementing-sirikit-images/plist18w.png#lightbox)
22. Ajoutez quelques clés de `String` avec des exemples d’utilisations du terme :

    [![](implementing-sirikit-images/plist19w.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist19w.png#lightbox)
23. Répétez les étapes ci-dessus pour les intentions dont l’application a besoin pour fournir un exemple d’utilisation de.

-----

> [!IMPORTANT]
> Les `AppIntentVocabulary.plist` seront inscrits auprès de Siri sur les appareils de test pendant le développement et l’intégration du vocabulaire personnalisé peut prendre un certain temps. Par conséquent, le testeur doit patienter quelques minutes avant de tenter de tester le vocabulaire spécifique à l’application lorsqu’il a été mis à jour.

Pour plus d’informations, consultez notre [référence de vocabulaire spécifique](~/ios/platform/sirikit/understanding-sirikit.md) à l’application et la [référence de vocabulaire personnalisé](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)d’Apple.

## <a name="adding-an-intents-extension"></a>Ajout d’une extension intentions

Maintenant que l’application a été préparée à adopter SiriKit, le développeur doit ajouter une ou plusieurs extensions intentions à la solution pour gérer les intentions requises pour l’intégration de Siri.

Pour chaque extension astentes requise, procédez comme suit :

- Ajoutez un projet d’extension intentions à la solution d’application Xamarin. iOS.
- Configurez le fichier `Info.plist` de l’extension intentions.
- Modifiez la classe principale de l’extension intentions.

Pour plus d’informations, consultez notre [référence d’extension intentions](~/ios/platform/sirikit/understanding-sirikit.md) et la [référence d’extension création](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1)d’apples.

### <a name="creating-the-extension"></a>Création de l’extension

Pour ajouter une extension intentions à la solution, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **panneau solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...** .
2. Dans la boîte de dialogue, sélectionnez **iOS** > **Extensions** > l' **extension intention** , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents05.png "Select Intent Extension")](implementing-sirikit-images/intents05.png#lightbox)
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents06.png "Enter a Name for the Intent Extension")](implementing-sirikit-images/intents06.png#lightbox)
4. Enfin, cliquez sur le bouton **créer** pour ajouter l’extension d’intention à la solution applications : 

    [![](implementing-sirikit-images/intents07.png "Add the Intent Extension to the apps solution")](implementing-sirikit-images/intents07.png#lightbox)
5. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension intention nouvellement créée. Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/intents08.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...** .
2. Dans la boîte de dialogue, sélectionnez  **C# Visual > extensions iOS > extension intention** , puis cliquez sur le bouton **suivant** :

    [![](implementing-sirikit-images/intents05.w157-sml.png "Select Intent Extension")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **OK** .
4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension Intents nouvellement créée, puis choisissez **Ajouter une référence de >** . Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** :

    [![](implementing-sirikit-images/intents08w.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Répétez ces étapes pour le nombre d’extensions d’intention (selon la section [architecture de l’application pour les extensions](#architecting-the-app-for-extensions) ci-dessus) dont l’application a besoin.

### <a name="configuring-the-infoplist"></a>Configuration du fichier info. plist

Pour chacune des extensions d’intentions ajoutées à la solution de l’application, doit être configuré dans les fichiers `Info.plist` pour fonctionner avec l’application.

Comme pour toute extension d’application typique, l’application disposera des clés existantes de `NSExtension` et `NSExtensionAttributes`. Pour une extension intentions, deux nouveaux attributs doivent être configurés :

[![](implementing-sirikit-images/intents01.png "The two new attributes that must be configured")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -est requis et se compose d’un tableau de noms de classes d’intention que l’application souhaite prendre en charge à partir de l’extension d’intention.
- **IntentsRestrictedWhileLocked** : clé facultative permettant à l’application de spécifier le comportement de l’écran de verrouillage de l’extension. Il se compose d’un tableau de noms de classes d’intention que l’application veut exiger que l’utilisateur soit connecté pour utiliser à partir de l’extension d’intention.

Pour configurer le fichier `Info.plist` de l’extension d’intention, double-cliquez dessus dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Ensuite, basculez vers la vue **source** , puis développez les clés `NSExtension` et `NSExtensionAttributes` dans l’éditeur :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Développez la clé `IntentsSupported` et ajoutez le nom de toute classe d’intention prise en charge par cette extension. Pour l’exemple d’application MonkeyChat, elle prend en charge les `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Si l’application exige éventuellement que l’utilisateur soit connecté à l’appareil pour utiliser une intention donnée, développez la clé de `IntentRestrictedWhileLocked` et ajoutez les noms de classes des intentions ayant un accès restreint. Pour l’exemple d’application MonkeyChat, l’utilisateur doit être connecté pour envoyer un message de conversation afin que nous ayons ajouté `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "The added INSendMessageIntent key")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "The added INSendMessageIntent key")](implementing-sirikit-images/intents10w.png#lightbox)

-----

Pour obtenir la liste complète des domaines d’intention disponibles, consultez la [référence](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)sur les domaines d’intention d’Apple.

### <a name="configuring-the-main-class"></a>Configuration de la classe principale

Ensuite, le développeur doit configurer la classe principale qui agit comme point d’entrée principal pour l’extension d’intention dans Siri. Il doit s’agir d’une sous-classe de `INExtension` qui est conforme au délégué `IINIntentHandler`. Exemple :

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

Il existe une méthode solitaires que l’application doit implémenter sur la classe principale d’extension d’intention, la méthode `GetHandler`. Cette méthode est passée par SiriKit et l’application doit retourner un gestionnaire d' **intentions** qui correspond au type de l’intention donnée.

Étant donné que l’exemple d’application MonkeyChat ne gère qu’une seule intention, elle se retourne elle-même dans la méthode `GetHandler`. Si l’extension gérait plusieurs intentions, le développeur ajoute une classe pour chaque type d’intention et retourne une instance en fonction de l' `Intent` passé à la méthode.

### <a name="handling-the-resolve-stage"></a>Gestion de l’étape de résolution

L’étape de résolution est l’endroit où l’extension d’intention clarifie et valide les paramètres transmis à partir de Siri et qui ont été définis par le biais de la conversation de l’utilisateur.

Pour chaque paramètre qui est envoyé à partir de Siri, il existe une méthode `Resolve`. L’application doit implémenter cette méthode pour chaque paramètre pour lequel l’application peut avoir besoin de l’aide de Siri pour obtenir la réponse correcte de l’utilisateur.

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

[![](implementing-sirikit-images/intentsui01.png "An example Intents UI Extension output")](implementing-sirikit-images/intentsui01.png#lightbox)

Tout comme l’extension intentions, le développeur effectue l’étape suivante pour l’extension d’interface utilisateur Intents :

- Ajoutez un projet d’extension d’interface utilisateur Intents à la solution d’application Xamarin. iOS.
- Configurez l’extension d’interface utilisateur intentes `Info.plist` fichier.
- Modifiez la classe principale d’extension d’interface utilisateur Intent.

Pour plus d’informations, consultez notre [référence d’extension d’interface utilisateur Intents](~/ios/platform/sirikit/understanding-sirikit.md) et Apple [fournit une référence d’interface personnalisée](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Création de l’extension

Pour ajouter une extension d’interface utilisateur intentions à la solution, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **panneau solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...** .
2. Dans la boîte de dialogue, sélectionnez **iOS** > **Extensions** > **l’extension de l’interface utilisateur intentionnelle** , puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents11.png "Select Intent UI Extension")](implementing-sirikit-images/intents11.png#lightbox)
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **suivant** : 

    [![](implementing-sirikit-images/intents12.png "Enter a Name for the Intent Extension")](implementing-sirikit-images/intents12.png#lightbox)
4. Enfin, cliquez sur le bouton **créer** pour ajouter l’extension d’intention à la solution applications : 

    [![](implementing-sirikit-images/intents13.png "Add the Intent Extension to the apps solution")](implementing-sirikit-images/intents13.png#lightbox)
5. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension intention nouvellement créée. Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** : 

    [![](implementing-sirikit-images/intents14.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cliquez avec le bouton droit sur le **nom** de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet...**
2. Dans la boîte de dialogue, sélectionnez **iOS** > **Extensions** > **l’extension de l’interface utilisateur intentionnelle** , puis cliquez sur le bouton **suivant** .
3. Entrez ensuite un **nom** pour l’extension d’intention, puis cliquez sur le bouton **OK** .
4. Dans le **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **références** de l’extension intention nouvellement créée. Vérifiez le nom du projet de bibliothèque de code partagé commun (que l’application a créée ci-dessus), puis cliquez sur le bouton **OK** .
    
-----

### <a name="configuring-the-infoplist"></a>Configuration du fichier info. plist

Configurez le fichier `Info.plist` de l’extension d’interface utilisateur Intents pour qu’il fonctionne avec l’application.

Comme pour toute extension d’application typique, l’application disposera des clés existantes de `NSExtension` et `NSExtensionAttributes`. Pour une extension intentions, un nouvel attribut doit être configuré :

[![](implementing-sirikit-images/intents03.png "The one new attribute that must be configured")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** est obligatoire et se compose d’un tableau de noms de classes d’intention que l’application souhaite prendre en charge à partir de l’extension d’intention.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Pour configurer le fichier `Info.plist` de l’extension d’interface utilisateur intentionnelle, double-cliquez dessus dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Ensuite, basculez vers la vue **source** , puis développez les clés `NSExtension` et `NSExtensionAttributes` dans l’éditeur :

[![](implementing-sirikit-images/intents04.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pour configurer le fichier `Info.plist` de l’extension d’interface utilisateur intentionnelle, double-cliquez dessus dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Développez les clés `NSExtension` et `NSExtensionAttributes` dans l’éditeur :

[![](implementing-sirikit-images/intents04w.png "Tthe NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Développez la clé `IntentsSupported` et ajoutez le nom de toute classe d’intention prise en charge par cette extension. Pour l’exemple d’application MonkeyChat, elle prend en charge les `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Pour obtenir la liste complète des domaines d’intention disponibles, consultez la [référence](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)sur les domaines d’intention d’Apple.

### <a name="configuring-the-main-class"></a>Configuration de la classe principale

Configurez la classe principale qui agit comme point d’entrée principal pour l’extension d’interface utilisateur intentionnelle dans Siri. Il doit s’agir d’une sous-classe de `UIViewController` qui est conforme à l’interface `IINUIHostedViewController`. Exemple :

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

Siri passe une instance de classe `INInteraction` à la méthode `Configure` de l’instance `UIViewController` à l’intérieur de l’extension d’interface utilisateur Intent.

L’objet `INInteraction` fournit trois éléments d’information clés à l’extension :

1. Objet d’intention en cours de traitement.
2. Objet de réponse intentionnelle à partir des méthodes `Confirm` et `Handle` de l’extension d’intention.
3. État d’interaction qui définit l’état de l’interaction entre l’application et Siri.

L’instance `UIViewController` est la classe principale de l’interaction avec Siri et, étant donné qu’elle hérite de `UIViewController`, elle a accès à toutes les fonctionnalités de UIKit.

Quand Siri appelle la méthode `Configure` du `UIViewController` il passe dans un contexte d’affichage indiquant que le contrôleur d’affichage sera hébergé dans une carte Snippit ou Maps Siri.

Siri passera également un gestionnaire d’achèvement dont l’application a besoin pour retourner la taille souhaitée de la vue une fois que l’application a terminé de la configurer.

### <a name="design-the-ui-in-ios-designer"></a>Concevoir l’interface utilisateur dans le concepteur iOS

Disposition de l’interface utilisateur de l’extension d’interface utilisateur Intents dans le concepteur iOS. Double-cliquez sur le fichier de `MainInterface.storyboard` de l’extension dans le **Explorateur de solutions** pour l’ouvrir et le modifier. Faites glisser dans tous les éléments d’interface utilisateur requis pour créer l’interface utilisateur et enregistrer les modifications.

> [!IMPORTANT]
> Bien qu’il soit possible d’ajouter des éléments interactifs tels que `UIButtons` ou `UITextFields` à l' `UIViewController`de l’extension d’interface utilisateur intentionnelle, ceux-ci sont strictement interdits, car l’interface utilisateur de l’intention n’est pas interactive et l’utilisateur ne peut pas interagir avec eux.

### <a name="wire-up-the-user-interface"></a>Relier l’interface utilisateur

Avec l’interface utilisateur de l’extension d’IU Intent créée dans le concepteur iOS, modifiez la sous-classe `UIViewController` et remplacez la méthode `Configure` comme suit :

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

Si l’extension d’interface utilisateur intentes doit substituer des éléments de l’interface utilisateur Siri par défaut, la sous-classe `UIViewController` doit implémenter l’interface `IINUIHostedViewSiriProviding` et s’abonner à l’affichage d’un élément d’interface particulier.

Ajoutez le code suivant à la sous-classe `UIViewController` pour indiquer à Siri que l’extension d’interface utilisateur Intent affiche déjà le contenu du message :

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Éléments à prendre en considération

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

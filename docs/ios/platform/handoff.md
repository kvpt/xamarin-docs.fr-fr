---
title: Procédure de transfert dans Xamarin.iOS
description: Cet article couvre travaillant avec remise dans une application Xamarin.iOS à transférer des activités des utilisateurs entre les applications en cours d’exécution sur l’utilisateur d’autres dispositifs.
ms.prod: xamarin
ms.assetid: 405F966A-4085-4621-AA15-33D663AD15CD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 084b9924af467459a017413a958ec2e46ff219fc
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865308"
---
# <a name="handoff-in-xamarinios"></a>Procédure de transfert dans Xamarin.iOS

_Cet article couvre travaillant avec remise dans une application Xamarin.iOS à transférer des activités des utilisateurs entre les applications en cours d’exécution sur l’utilisateur d’autres dispositifs._

Apple a introduit remise dans iOS 8 et OS X Yosemite (10.10) pour fournir un mécanisme commun pour l’utilisateur à démarrer sur l’un de leurs appareils, des activités de transfert vers un autre appareil exécutant la même application ou une autre application qui prend en charge de la même activité.

[![](handoff-images/handoff02.png "Un exemple d’une opération de transfert de travail")](handoff-images/handoff02.png#lightbox)

Cet article sera jeter un coup de œil à l’activation d’activité partage dans une application Xamarin.iOS et couvre l’infrastructure de remise en détail :

## <a name="about-handoff"></a>À propos de la procédure de transfert

Procédure de transfert (également connu sous la continuité des activités) a été introduite par Apple dans iOS 8 et OS X Yosemite (10.10) comme un moyen pour l’utilisateur de lancer une activité sur l’un de leurs appareils (iOS ou Mac) et continuer cette même activité sur un autre de leurs appareils (telles qu’identifiées par iClou de l’utilisateur d compte).

Procédure de transfert a été développé dans iOS 9 prend également en charge de nouveau, les capacités de recherche améliorées. Pour plus d’informations, consultez notre [les améliorations de recherche](~/ios/platform/search/index.md) documentation.

Par exemple, l’utilisateur peut démarrer un e-mail sur leur iPhone et continuer sans interruption de l’e-mail sur leur Mac, avec toutes les mêmes informations de message renseigné et le curseur dans le même emplacement qu’ils laissé dans iOS.

Une de vos applications qui partagent le même _ID de l’équipe_ sont éligibles à l’aide de la procédure de transfert pour continuer les activités de l’utilisateur entre applications tant que ces applications sont remis via le Store d’application iTunes ou signé par un développeur inscrit (pour Mac, Enterprise ou des applications Ad Hoc).

N’importe quel `NSDocument` ou `UIDocument` applications ont automatiquement remise prennent en charge intégrée et nécessitent des modifications minimes pour prendre en charge de la procédure de transfert.

### <a name="continuing-user-activities"></a>Activités de l’utilisateur poursuivre

Le `NSUserActivity` classe (ainsi que de quelques modifications minimes pour `UIKit` et `AppKit`) fournit la prise en charge pour la définition d’activité d’un utilisateur qui peut potentiellement être maintenue sur un autre des périphériques de l’utilisateur.

Une activité peut être transmis à un autre des périphériques de l’utilisateur, il doit être encapsulé dans une instance `NSUserActivity`, marquée en tant que le _activité en cours_, ont la charge utile est définie (les données utilisées pour effectuer la continuation) et le activité doit ensuite être transmise à cet appareil.

Procédure de transfert transmet le strict minimum d’informations pour définir l’activité de se poursuivre, avec les paquets de données de grande taille synchronisés via iCloud.

Sur l’appareil de réception, l’utilisateur recevra une notification qu’une activité est disponible pour la continuation. Si l’utilisateur choisit de continuer l’activité sur le nouveau périphérique, l’application spécifiée est lancée (si pas déjà en cours d’exécution) et la charge utile à partir de la `NSUserActivity` est utilisée pour redémarrer l’activité.

[![](handoff-images/handoffinteractions.png "Une vue d’ensemble des activités de l’utilisateur poursuivre")](handoff-images/handoffinteractions.png#lightbox)

Seules les applications qui partagent le même ID de l’équipe de développeur et de répondant à une donnée _Type d’activité_ sont éligibles pour la continuation. Une application définit les Types d’activités pris en charge sous le `NSUserActivityTypes` clé de sa **Info.plist** fichier. Pour cette raison, un appareil continu choisit l’application pour effectuer la continuation basée sur l’ID d’équipe, Type d’activité et éventuellement le _titre de l’activité_.

L’application de réception utilise les informations de la `NSUserActivity`de `UserInfo` dictionnaire qui permet de configurer son interface utilisateur et de restaurer l’état de l’activité donnée afin que la transition s’affiche transparente pour l’utilisateur final.

Si la continuation a besoin de plus d’informations peuvent être envoyées efficacement via un `NSUserActivity`, la reprise d’application peut envoyer un appel à l’application d’origine et établir un ou plusieurs flux pour transmettre les données requises. Par exemple, si l’activité a été modification d’un document de texte de grande taille avec plusieurs images, de diffusion en continu doit transférer les informations nécessaires pour continuer l’activité sur l’appareil de réception. Pour plus d’informations, consultez le [prenant en charge les flux de Continuation](#supporting-continuation-streams) section ci-dessous.

Comme indiqué ci-dessus, `NSDocument` ou `UIDocument` des applications en ont automatiquement remise prennent en charge intégrée. Pour plus d’informations, consultez le [prenant en charge le transfert de travail dans les applications basées sur le Document](#supporting-handoff-in-document-based-apps) section ci-dessous.

### <a name="the-nsuseractivity-class"></a>La classe NSUserActivity

Le `NSUserActivity` classe est l’objet principal dans un échange de remise et est utilisé pour encapsuler l’état d’une activité utilisateur qui est disponible pour la continuation. Une application instanciera une copie de `NSUserActivity` pour toute activité, il prend en charge et souhaite continuer sur un autre appareil. Par exemple, éditeur de document créerait une activité pour chaque document actuellement ouvert. Toutefois, que le document de premier plan (affiché dans la fenêtre de premier plan ou un onglet) est la _activité en cours_ et c’est pourquoi disponibles pour la continuation.

Une instance de `NSUserActivity` est identifié par les deux sa `ActivityType` et `Title` propriétés. Le `UserInfo` propriété de dictionnaire est utilisée pour transporter des informations concernant l’état de l’activité. Définir le `NeedsSave` propriété `true` si vous souhaitez différé charger les informations d’état via le `NSUserActivity`du délégué. Utilisez le `AddUserInfoEntries` méthode pour fusionner les nouvelles données à partir d’autres clients dans le `UserInfo` dictionnaire comme requis pour conserver l’état de l’activité.

### <a name="the-nsuseractivitydelegate-class"></a>La classe NSUserActivityDelegate

Le `NSUserActivityDelegate` est utilisé pour conserver les informations dans un `NSUserActivity`de `UserInfo` dictionnaire à jour et synchronisé avec l’état actuel de l’activité. Lorsque le système a besoin des informations dans l’activité de mise à jour (comme avant la continuation sur un autre périphérique), il appelle le `UserActivityWillSave` méthode du délégué.

Vous devez implémenter le `UserActivityWillSave` (méthode) et apportez les modifications à la `NSUserActivity` (tel que `UserInfo`, `Title`, etc.) pour vous assurer qu’il reflète toujours l’état de l’activité actuelle. Lorsque le système appelle le `UserActivityWillSave` (méthode), le `NeedsSave` indicateur sera effacé. Si vous modifiez les propriétés de données de l’activité, vous devrez définir `NeedsSave` à `true` à nouveau.

Au lieu d’utiliser le `UserActivityWillSave` méthode présentée ci-dessus, vous pouvez éventuellement avoir `UIKit` ou `AppKit` gérer automatiquement de l’activité des utilisateurs. Pour ce faire, définissez l’objet de répondeur `UserActivity` propriété et mettre en œuvre la `UpdateUserActivityState` (méthode). Consultez le [prenant en charge la remise de répondeurs](#supporting-handoff-in-responders) section ci-dessous pour plus d’informations.

### <a name="app-framework-support"></a>Prise en charge de l’infrastructure d’application

Les deux `UIKit` (iOS) et `AppKit` (OS X) fournissent une prise en charge intégrée pour la transférer dans le `NSDocument`, répondeur (`UIResponder`/`NSResponder`), et `AppDelegate` classes. Chaque système d’exploitation implémente remise légèrement différemment, le mécanisme de base et les API sont les mêmes.

#### <a name="user-activities-in-document-based-apps"></a>Activités des utilisateurs dans les applications basées sur le Document

Basés sur document applications iOS et OS X ont automatiquement la prise en charge du transfert de travail intégré. Pour activer cette prise en charge, vous devrez ajouter un `NSUbiquitousDocumentUserActivityType` clé et la valeur pour chaque `CFBundleDocumentTypes` entrée dans l’application **Info.plist** fichier.

Si cette clé est présente, à la fois `NSDocument` et `UIDocument` créer automatiquement `NSUserActivity` instances pour les documents iCloud du type spécifié. Vous devez fournir un type d’activité pour chaque type de document qui prend en charge de l’application et plusieurs types de documents peuvent utiliser le même type d’activité. Les deux `NSDocument` et `UIDocument` remplir automatiquement la `UserInfo` propriété de la `NSUserActivity` avec leurs `FileURL` valeur de la propriété.

Sur OS X, le `NSUserActivity` gérés par `AppKit` et associé répondeurs automatiquement devenir l’activité actuelle lorsque la fenêtre de document devient la fenêtre principale. Sur iOS, pour `NSUserActivity` objets gérés par `UIKit`, vous devez appeler `BecomeCurrent` méthode explicitement ou que le document `UserActivity` propriété définie sur une `UIViewController` lorsque l’application s’affiche au premier plan.

`AppKit` restaure automatiquement les `UserActivity` propriété créée de cette façon sur OS X. Cela se produit si le `ContinueUserActivity` retourne de la méthode `false` ou si elle n’est pas implémentée. Dans ce cas, le document est ouvert avec le `OpenDocument` méthode de la `NSDocumentController` et il recevra ensuite un `RestoreUserActivityState` appel de méthode.

Consultez le [prenant en charge le transfert de travail dans les applications basées sur le Document](#supporting-handoff-in-document-based-apps) section ci-dessous pour plus d’informations.

#### <a name="user-activities-and-responders"></a>Intervenants et les activités des utilisateurs

Les deux `UIKit` et `AppKit` capable de gérer automatiquement une activité de l’utilisateur si vous le définissez en tant qu’objet répondeur `UserActivity` propriété. Si l’état a été modifié, vous devez définir le `NeedsSave` propriété du répondeur `UserActivity` à `true`. Le système enregistre automatiquement le `UserActivity` si nécessaire, après avoir donné à l’heure de répondeur pour mettre à jour l’état en appelant son `UpdateUserActivityState` (méthode).

Si plusieurs répondeurs partagent un seul `NSUserActivity` instance, ils reçoivent un `UpdateUserActivityState` rappel lorsque le système met à jour l’objet d’activité utilisateur. Le répondeur doit appeler le `AddUserInfoEntries` méthode pour mettre à jour le `NSUserActivity`de `UserInfo` dictionnaire afin de refléter l’état d’activité actuel à ce stade. Le `UserInfo` dictionnaire est effacé avant chaque `UpdateUserActivityState` appeler.

Pour dissocier lui-même à partir d’une activité, un répondeur peut définir son `UserActivity` propriété `null`. Quand une infrastructure d’application gérés `NSUserActivity` instance n’a pas plus les répondeurs associés ou les documents, il est automatiquement invalidé.

Consultez le [prenant en charge la remise de répondeurs](#supporting-handoff-in-responders) section ci-dessous pour plus d’informations.

#### <a name="user-activities-and-the-appdelegate"></a>Activités de l’utilisateur et l’AppDelegate

De votre application `AppDelegate` est son point d’entrée principal lors du traitement d’une continuation de remise. Lorsque l’utilisateur répond à une notification de remise, l’application appropriée est lancée (si ce n’est pas déjà en cours d’exécution) et le `WillContinueUserActivityWithType` méthode de la `AppDelegate` sera appelée. À ce stade, l’application doit informer l’utilisateur qui démarre la continuation.

Le `NSUserActivity` instance est remise quand le `AppDelegate`de `ContinueUserActivity` méthode est appelée. À ce stade, vous devez configurer l’interface utilisateur de l’application et continuer l’activité donnée.

Consultez le [remise implémentation](#implementing-handoff) section ci-dessous pour plus d’informations.

## <a name="enabling-handoff-in-a-xamarin-app"></a>L’activation de remise dans une application Xamarin

En raison des exigences de sécurité imposées par la procédure de transfert, une application Xamarin.iOS qui utilise l’infrastructure de remise doit être correctement configurée dans les deux le portail des développeurs Apple et dans le fichier projet Xamarin.iOS.

Effectuez ce qui suit :

1. Connectez-vous à la [portail des développeurs Apple](https://developer.apple.com).
2. Cliquez sur **certificats, identificateurs et profils**.
3. Si vous n’avez pas déjà fait, cliquez sur **identificateurs** et créer un ID pour votre application (par exemple, `com.company.appname`), sinon modifier votre code existant.
4. Vérifiez que le **iCloud** service a été activée pour l’ID donné :

    [![](handoff-images/provision01.png "Activer le service d’iCloud pour l’ID donné")](handoff-images/provision01.png#lightbox)
5. Enregistrez les modifications apportées.
6. Cliquez sur **profils de provisionnement** > **développement** et créer un profil de provisionnement pour vous de développement nouvelle application :

    [![](handoff-images/provision02.png "Créer un environnement de développement nouveau profil de configuration pour l’application")](handoff-images/provision02.png#lightbox)
7. Télécharger et installer le nouveau profil de provisionnement ou utiliser Xcode pour télécharger et installer le profil.
8. Modifier les options de votre projet Xamarin.iOS et vous assurer que vous utilisez le profil de configuration que vous venez de créer :

    [![](handoff-images/provision03.png "Sélectionnez le profil de provisionnement venez de créer")](handoff-images/provision03.png#lightbox)
9. Ensuite, modifiez votre **Info.plist** de fichiers et de vous assurer que vous utilisez l’ID d’application qui a été utilisé pour créer le profil de provisionnement :

    [![](handoff-images/provision04.png "Définir l’ID d’application")](handoff-images/provision04.png#lightbox)
10. Faites défiler vers le **Modes d’arrière-plan** section et vérifiez les éléments suivants :

    [![](handoff-images/provision05.png "Activer les modes d’arrière-plan requis")](handoff-images/provision05.png#lightbox)
11. Enregistrez les modifications à tous les fichiers.

Ces paramètres en place, l’application est maintenant prête à accéder aux API de Framework de remise. Pour obtenir des informations détaillées sur l’approvisionnement, consultez notre [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) et [l’approvisionnement de votre application](~/ios/get-started/installation/device-provisioning/index.md) guides.

## <a name="implementing-handoff"></a>Implémentation de remise

Activités de l’utilisateur peuvent être poursuivies entre les applications qui sont signées avec le même ID de l’équipe de développeur et prendre en charge le même Type d’activité. Implémentation de remise dans une application Xamarin.iOS vous oblige à créer un objet d’activité utilisateur (soit dans `UIKit` ou `AppKit`), mettre à jour l’état d’objet pour effectuer le suivi de l’activité et en continuant de l’activité sur un appareil de réception.

### <a name="identifying-user-activities"></a>Identifier des activités de l’utilisateur

La première étape dans l’implémentation de remise consiste à identifier les types d’activités des utilisateurs qui prend en charge de votre application, et indique quelle de ces activités est de bons candidats pour la continuation sur un autre appareil. Par exemple : une application de tâches peut prendre en charge la modification d’éléments comme une _Type d’activité utilisateur_et prennent en charge la navigation de la liste des éléments disponibles qu’une autre.

Une application peut créer autant utilisateur Types d’activité sont requis, un pour n’importe quelle fonction qui fournit de l’application. Pour chaque Type d’activité utilisateur, l’application devra suivre à quel moment une activité du type commence et se termine et avez besoin gérer les informations d’état à jour pour continuer cette tâche sur un autre appareil.

Activités de l’utilisateur peuvent être poursuivies sur n’importe quelle application signée avec le même ID d’équipe sans un mappage entre les applications émettrices et réceptrices. Par exemple, une application donnée peut créer quatre différents types d’activités, qui sont utilisées par les applications individuelles, différentes sur un autre appareil. Il s’agit d’une occurrence courante entre la version Mac de l’application (qui peut avoir des nombreuses fonctionnalités et fonctions) et des applications iOS, où chaque application est plus petit et axée sur une tâche spécifique.

### <a name="creating-activity-type-identifiers"></a>Création d’identificateurs de Type d’activité

Le _identificateur de Type d’activité_ est une chaîne courte ajoutée à la `NSUserActivityTypes` tableau de l’application **Info.plist** fichier utilisé pour identifier un Type d’activité utilisateur donné. Il y aura une entrée dans le tableau pour chaque activité qui prend en charge de l’application. Apple suggère à l’aide d’une notation DNS inverse-style pour l’identificateur de Type d’activité pour éviter les collisions. Par exemple : `com.company-name.appname.activity` activités basées sur pour une application spécifique ou `com.company-name.activity` pour les activités qui peuvent s’exécuter entre plusieurs applications.

L’identificateur de Type d’activité est utilisé lors de la création un `NSUserActivity` instance pour identifier le type d’activité. Lorsqu’une activité continue sur un autre appareil, le Type d’activité (avec l’ID l’application Team) détermine quelle application à lancer pour continuer l’activité.

Par exemple, nous allons créer un exemple d’application appelé **MonkeyBrowser** ([télécharger ici](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)). Cette application présente quatre onglets, chacun avec une autre URL soit ouverte dans une vue de navigateur web. L’utilisateur pourra continuer à n’importe quel onglet sur un appareil iOS autre que l’application en cours d’exécution.

Pour créer les identificateurs de Type activité requis pour prendre en charge ce comportement, modifiez le **Info.plist** de fichiers et de basculer vers le **Source** vue. Ajouter un `NSUserActivityTypes` de clé et de créer les identificateurs suivants :

[![](handoff-images/type01.png "Les identificateurs requis dans l’éditeur plist NSUserActivityTypes clé")](handoff-images/type01.png#lightbox)

Nous avons créé quatre nouveaux identificateurs de Type d’activité, un pour chacun des onglets dans l’exemple **MonkeyBrowser** application. Lorsque vous créez vos propres applications, remplacez le contenu de la `NSUserActivityTypes` de tableau avec les identificateurs de Type d’activité spécifiques aux activités de votre application prend en charge.

### <a name="tracking-user-activity-changes"></a>Le suivi des modifications d’activité utilisateur

Lorsque nous créons une nouvelle instance de la `NSUserActivity` (classe), nous allons spécifier un `NSUserActivityDelegate` instance pour effectuer le suivi des modifications à l’état de l’activité. Par exemple, le code de suivi peut être utilisé pour effectuer le suivi des modifications d’état :

```csharp
using System;
using CoreGraphics;
using Foundation;
using UIKit;

namespace MonkeyBrowse
{
    public class UserActivityDelegate : NSUserActivityDelegate
    {
        #region Constructors
        public UserActivityDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void UserActivityReceivedData (NSUserActivity userActivity, NSInputStream inputStream, NSOutputStream outputStream)
        {
            // Log
            Console.WriteLine ("User Activity Received Data: {0}", userActivity.Title);
        }

        public override void UserActivityWasContinued (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity Was Continued: {0}", userActivity.Title);
        }

        public override void UserActivityWillSave (NSUserActivity userActivity)
        {
            Console.WriteLine ("User Activity will be Saved: {0}", userActivity.Title);
        }
        #endregion
    }
}
```

Le `UserActivityReceivedData` méthode est appelée lorsqu’un Stream de Continuation a reçu des données à partir d’un appareil d’envoi. Pour plus d’informations, consultez le [prenant en charge les flux de Continuation](#supporting-continuation-streams) section ci-dessous.

Le `UserActivityWasContinued` méthode est appelée lorsqu’une activité à partir de l’appareil en cours a utilisé un autre appareil. Selon le type d’activité, telles que l’ajout d’un nouvel élément dans une liste de tâches, l’application peut doivent annuler l’activité sur l’appareil d’envoi.

Le `UserActivityWillSave` méthode est appelée avant toute modification apportée à l’activité est enregistrées et synchronisées entre les appareils disponibles localement. Vous pouvez utiliser cette méthode pour apporter des modifications de dernière minute pour le `UserInfo` propriété de la `NSUserActivity` instance avant d’être envoyée.

### <a name="creating-a-nsuseractivity-instance"></a>Création d’une Instance NSUserActivity

Chaque activité de votre application souhaite fournir la possibilité de continuer sur un autre appareil doit être encapsulée dans un `NSUserActivity` instance. L’application peut créer autant d’activités en fonction des besoins et la nature de ces activités est dépendante de la fonctionnalité et les fonctionnalités de l’application en question. Par exemple, une application de messagerie peut créer une activité pour la création d’un nouveau message et l’autre pour la lecture d’un message.

Pour notre exemple d’application, un nouveau `NSUserActivity` est créé chaque fois que l’utilisateur entre une nouvelle URL dans un de la vue du navigateur web à onglets. Le code suivant stocke l’état d’une tabulation donnée :

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSUserActivity UserActivity { get; set; }
...

UserActivity = new NSUserActivity (UserActivityTab1);
UserActivity.Title = "Weather Tab";
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

Il crée un nouveau `NSUserActivity` à l’aide de Type d’activité utilisateur créé ci-dessus, puis fournit un titre explicite pour l’activité. Il s’attache à une instance de la `NSUserActivityDelegate` créé ci-dessus à surveiller l’état change et informe iOS que cette activité utilisateur est l’activité en cours.

### <a name="populating-the-userinfo-dictionary"></a>Remplir le dictionnaire UserInfo

Comme nous l’avons vu ci-dessus, le `UserInfo` propriété de la `NSUserActivity` classe est un `NSDictionary` de paires clé-valeur utilisées pour définir l’état d’une activité donnée. Les valeurs stockées dans `UserInfo` doit être un des types suivants : `NSArray`, `NSData`, `NSDate`, `NSDictionary`, `NSNull`, `NSNumber`, `NSSet`, `NSString`, ou `NSURL`. `NSURL` les valeurs de données qui pointent vers les documents iCloud sont automatiquement ajustées afin qu’ils pointent vers les mêmes documents sur un appareil de réception.

Dans l’exemple ci-dessus, nous avons créé un `NSMutableDictionary` de l’objet et il remplie avec une clé unique fournissant l’URL de l’utilisateur a été affichée sous l’onglet donné. Le `AddUserInfoEntries` méthode de l’activité de l’utilisateur a été utilisée pour mettre à jour de l’activité avec les données qui seront utilisées pour restaurer l’activité sur l’appareil de réception :

```csharp
// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);
```

Apple suggère en conservant les informations envoyées au strict minimum pour vous assurer que l’activité est envoyée en temps voulu pour l’appareil de réception. Si des informations plus grande sont nécessaires, comme modifier une image jointe à un document doit être envoyé, le vous devez utiliser des flux de Continuation. Consultez le [prenant en charge les flux de Continuation](#supporting-continuation-streams) section ci-dessous pour plus d’informations.

### <a name="continuing-an-activity"></a>Poursuivre une activité

Procédure de transfert vous informe automatiquement local appareils iOS et OS X à proximité physique de l’appareil d’origine et signés dans le même compte iCloud, de la disponibilité des activités des utilisateurs non bloquantes. Si l’utilisateur choisit de continuer à une activité sur un nouvel appareil, le système lance l’application appropriée (selon l’ID de l’équipe et le Type d’activité) et les informations son `AppDelegate` que la continuation doit se produire.

Tout d’abord, le `WillContinueUserActivityWithType` méthode est appelée afin de l’application peut informer l’utilisateur que la continuation est sur le point de commencer. Nous utilisons le code suivant dans le **AppDelegate.cs** fichier de notre exemple d’application pour gérer de départ de continuation :

```csharp
public NSString UserActivityTab1 = new NSString ("com.xamarin.monkeybrowser.tab1");
public NSString UserActivityTab2 = new NSString ("com.xamarin.monkeybrowser.tab2");
public NSString UserActivityTab3 = new NSString ("com.xamarin.monkeybrowser.tab3");
public NSString UserActivityTab4 = new NSString ("com.xamarin.monkeybrowser.tab4");
...

public FirstViewController Tab1 { get; set; }
public SecondViewController Tab2 { get; set;}
public ThirdViewController Tab3 { get; set; }
public FourthViewController Tab4 { get; set; }
...

public override bool WillContinueUserActivity (UIApplication application, string userActivityType)
{
    // Report Activity
    Console.WriteLine ("Will Continue Activity: {0}", userActivityType);

    // Take action based on the user activity type
    switch (userActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Inform view that it's going to be modified
        Tab1.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Inform view that it's going to be modified
        Tab2.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Inform view that it's going to be modified
        Tab3.PreparingToHandoff ();
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Inform view that it's going to be modified
        Tab4.PreparingToHandoff ();
        break;
    }

    // Inform system we handled this
    return true;
}
```

Dans l’exemple ci-dessus, chaque contrôleur d’affichage inscrit avec le `AppDelegate` et a un public `PreparingToHandoff` méthode qui affiche un indicateur d’activité et un message informant l’utilisateur que l’activité est sur le point d’être remis à l’appareil en cours. Exemple :

```csharp
private void ShowBusy(string reason) {

    // Display reason
    BusyText.Text = reason;

    //Define Animation
    UIView.BeginAnimations("Show");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0.5f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PreparingToHandoff() {
    // Inform caller
    ShowBusy ("Continuing Activity...");
}
```

Le `ContinueUserActivity` de la `AppDelegate` sera appelé pour continuer en fait l’activité donnée. Là encore, à partir de notre exemple d’application :

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

Le grand public `PerformHandoff` méthode de chaque contrôleur d’affichage réellement effectue la procédure de transfert et restaure l’activité sur l’appareil en cours. Dans le cas de l’exemple, il affiche la même URL dans un onglet donné que l’utilisateur a été la navigation sur un autre appareil. Exemple :

```csharp
private void HideBusy() {

    //Define Animation
    UIView.BeginAnimations("Hide");
    UIView.SetAnimationDuration(1.0f);

    Handoff.Alpha = 0f;

    //Execute Animation
    UIView.CommitAnimations();
}
...

public void PerformHandoff(NSUserActivity activity) {

    // Hide busy indicator
    HideBusy ();

    // Extract URL from dictionary
    var url = activity.UserInfo ["Url"].ToString ();

    // Display value
    URL.Text = url;

    // Display the give webpage
    WebView.LoadRequest(new NSUrlRequest(NSUrl.FromString(url)));

    // Save activity
    UserActivity = activity;
    UserActivity.BecomeCurrent ();

}
```

Le `ContinueUserActivity` méthode inclut un `UIApplicationRestorationHandler` que vous pouvez appeler pour le document ou le répondeur en fonction de la reprise d’activité. Vous devez passer un `NSArray` ou des objets pouvant être restaurées au Gestionnaire de restauration lorsqu’elle est appelée. Par exemple :

```csharp
completionHandler (new NSObject[]{Tab4});
```

Pour chaque objet passé, son `RestoreUserActivityState` méthode sera appelée. Chaque objet peut ensuite utiliser les données dans le `UserInfo` dictionnaire pour restaurer son propre état. Par exemple :

```csharp
public override void RestoreUserActivityState (NSUserActivity activity)
{
    base.RestoreUserActivityState (activity);

    // Log activity
    Console.WriteLine ("Restoring Activity {0}", activity.Title);
}
```

Pour les applications basés sur document, si vous n’implémentez pas la `ContinueUserActivity` (méthode), ou elle retourne `false`, `UIKit` ou `AppKit` peut reprendre automatiquement l’activité. Consultez le [prenant en charge le transfert de travail dans les applications basées sur le Document](#supporting-handoff-in-document-based-apps) section ci-dessous pour plus d’informations.

### <a name="failing-handoff-gracefully"></a>Échec de remise normalement

Procédure de transfert s’appuie sur la transmission des informations entre une collection faiblement connecté appareils iOS et OS X, le processus de transfert peut échouer. Vous devez concevoir votre application pour gérer ces échecs correctement et informer l’utilisateur de toute situation qui surviennent.

En cas de défaillance, le `DidFailToContinueUserActivitiy` méthode de la `AppDelegate` sera appelée. Par exemple :

```csharp
public override void DidFailToContinueUserActivitiy (UIApplication application, string userActivityType, NSError error)
{
    // Log information about the failure
    Console.WriteLine ("User Activity {0} failed to continue. Error: {1}", userActivityType, error.LocalizedDescription);
}
```

Vous devez utiliser fourni `NSError` pour fournir des informations à l’utilisateur sur l’échec.

## <a name="native-app-to-web-browser-handoff"></a>Application native pour la remise de navigateur Web

Un utilisateur peut souhaiter continuer une activité sans disposer d’une application native appropriée installée sur l’appareil souhaité. Dans certaines situations, une interface basée sur le web peut fournir la fonctionnalité requise et l’activité peut toujours être poursuivie. Par exemple, compte de messagerie de l’utilisateur peut fournir une interface utilisateur de base de web pour la composition et la lecture des messages.

Si l’application d’origine, native connaît l’URL de l’interface web (et la syntaxe requise pour identifier l’élément en cours de suite), il peut encoder ces informations dans le `WebpageURL` propriété de la `NSUserActivity` instance. Si l’appareil de réception n’a pas une application native appropriés installée pour gérer la continuation, l’interface web fournie peut être appelée.

## <a name="web-browser-to-native-app-handoff"></a>Navigateur Web pour la remise d’application Native

Si l’utilisateur utilisait une interface basée sur le web sur l’appareil d’origine, et une application native sur l’appareil de réception de revendications de la partie domaine de le `WebpageURL` propriété, puis le système utilisera cette application le handle de la continuation. Le nouvel appareil recevra un `NSUserActivity` instance qui marque le Type d’activité en tant que `BrowsingWeb` et le `WebpageURL` contiendra l’URL de la visite de l’utilisateur, le `UserInfo` dictionnaire sera vide.

Pour une application de participer à ce type de transfert, il doit réclamer le domaine dans un `com.apple.developer.associated-domains` droit avec le format `<service>:<fully qualified domain name>` (par exemple : `activity continuation:company.com`).

Si le domaine spécifié correspond à un `WebpageURL` valeur de la propriété, procédure de transfert télécharge la liste des ID d’application approuvée depuis le site Web à ce domaine. Le site Web doit fournir une liste d’ID approuvés dans un fichier JSON signé nommé **apple-application-site-association** (par exemple, `https://company.com/apple-app-site-association`).

Ce fichier JSON contient un dictionnaire qui spécifie une liste des ID d’application sous la forme `<team identifier>.<bundle identifier>`. Par exemple :

```csharp
{
    "activitycontinuation": {
        "apps": [    "YWBN8XTPBJ.com.company.FirstApp",
            "YWBN8XTPBJ.com.company.SecondApp" ]
    }
}
```

Pour signer le fichier JSON (afin qu’il dispose de la bonne `Content-Type` de `application/pkcs7-mime`), utilisez le **Terminal** application et un `openssl` commande avec un certificat et la clé émis par une autorité de certification approuvée par iOS (consultez [ https://support.apple.com/kb/ht5012 ](https://support.apple.com/kb/ht5012) pour obtenir la liste). Par exemple :

```csharp
echo '{"activitycontinuation":{"apps":["YWBN8XTPBJ.com.company.FirstApp",
"YWBN8XTPBJ.com.company.SecondApp"]}}' > json.txt

cat json.txt | openssl smime -sign -inkey company.com.key
-signer company.com.pem
-certfile intermediate.pem
-noattr -nodetach
-outform DER > apple-app-site-association
```

Le `openssl` commande génère un fichier JSON signé que vous placez sur votre site Web à la **apple-application-site-association** URL. Par exemple :

```csharp
https://example.com/apple-app-site-association.
```

L’application ne recevra pas les activités dont `WebpageURL` domaine est dans son `com.apple.developer.associated-domains` droit. Uniquement les `http` et `https` protocoles sont pris en charge, un autre protocole lève une exception.

## <a name="supporting-handoff-in-document-based-apps"></a>Prise en charge de la remise dans les applications basées sur le Document

Comme indiqué précédemment, sur iOS et OS X, les applications basées sur le document prendra automatiquement en charge le transfert de documents basée sur iCloud si l’application **Info.plist** fichier contient un `CFBundleDocumentTypes` clé de `NSUbiquitousDocumentUserActivityType`. Par exemple :

```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeName</key>
        <string>NSRTFDPboardType</string>
        . . .
        <key>LSItemContentTypes</key>
        <array>
        <string>com.myCompany.rtfd</string>
        </array>
        . . .
        <key>NSUbiquitousDocumentUserActivityType</key>
        <string>com.myCompany.myEditor.editing</string>
    </dict>
</array>
```

Dans cet exemple, la chaîne est un indicateur d’application DNS inversé par le nom de l’activité ajoutée. Si l’entrée de cette façon, les entrées de type d’activité n’avez pas besoin être répétée dans le `NSUserActivityTypes` tableau de la **Info.plist** fichier.

L’objet de l’activité des utilisateurs créés automatiquement (disponibles par le biais du document `UserActivity` propriété) peut être référencé par d’autres objets dans l’application et utilisé pour restaurer l’état de continuation. Par exemple, pour effectuer le suivi élément sélection et document de position. Vous devez définir cette activités `NeedsSave` propriété `true` chaque fois que l’état change et mettre à jour le `UserInfo` dictionnaire dans le `UpdateUserActivityState` (méthode).

Le `UserActivity` propriété peut être utilisée à partir de n’importe quel thread et est conforme au protocole de (KVO) Observateur clé-valeur, donc il peut être utilisé pour synchroniser un document lors de leur déplacement et l’extraction d’iCloud. Le `UserActivity` propriété est invalidée lorsque le document est fermé.

Pour plus d’informations, consultez le site d’Apple [prise en charge des activités utilisateur dans les applications basées sur le Document](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html#//apple_ref/doc/uid/TP40014338-CH3-SW5) documentation.

## <a name="supporting-handoff-in-responders"></a>Prise en charge de la remise dans répondeurs

Vous pouvez associer des répondeurs (héritée de `UIResponder` sur iOS ou `NSResponder` sur OS X) aux activités en définissant leurs `UserActivity` propriétés. Le système enregistre automatiquement le `UserActivity` propriété à la fois, en appelant le répondeur `UpdateUserActivityState` pour ajouter des données actuelles à l’objet de l’activité des utilisateurs en utilisant le `AddUserInfoEntriesFromDictionary` (méthode).

## <a name="supporting-continuation-streams"></a>Prise en charge des flux de Continuation

Les situations où la quantité d’informations nécessaires pour continuer une activité ne peut pas être transférée efficacement par la charge utile de transfert initiale. Dans ces situations, l’application réceptrice peut établir un ou plusieurs flux de données entre lui-même et l’application d’origine pour transférer les données.

L’application d’origine définira le `SupportsContinuationStreams` propriété de la `NSUserActivity` l’instance à `true`. Par exemple :

```csharp
// Create a new user Activity to support this tab
UserActivity = new NSUserActivity (ThisApp.UserActivityTab1){
    Title = "Weather Tab",
    SupportsContinuationStreams = true
};
UserActivity.Delegate = new UserActivityDelegate ();

// Update the activity when the tab's URL changes
var userInfo = new NSMutableDictionary ();
userInfo.Add (new NSString ("Url"), new NSString (url));
UserActivity.AddUserInfoEntries (userInfo);

// Inform Activity that it has been updated
UserActivity.BecomeCurrent ();
```

L’application réceptrice peut ensuite appeler la `GetContinuationStreams` méthode de la `NSUserActivity` dans son `AppDelegate` pour établir le flux de données. Par exemple :

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Report Activity
    Console.WriteLine ("Continuing User Activity: {0}", userActivity.ToString());

    // Get input and output streams from the Activity
    userActivity.GetContinuationStreams ((NSInputStream arg1, NSOutputStream arg2, NSError arg3) => {
        // Send required data via the streams
        // ...
    });

    // Take action based on the Activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.monkeybrowser.tab1":
        // Preform handoff
        Tab1.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab1});
        break;
    case "com.xamarin.monkeybrowser.tab2":
        // Preform handoff
        Tab2.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab2});
        break;
    case "com.xamarin.monkeybrowser.tab3":
        // Preform handoff
        Tab3.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab3});
        break;
    case "com.xamarin.monkeybrowser.tab4":
        // Preform handoff
        Tab4.PerformHandoff (userActivity);
        completionHandler (new NSObject[]{Tab4});
        break;
    }

    // Inform system we handled this
    return true;
}
```

Sur l’appareil d’origine, le délégué d’activité utilisateur reçoit les flux de données en appelant son `DidReceiveInputStream` méthode pour fournir les données demandées pour continuer l’activité des utilisateurs sur l’appareil de reprise.

Vous utiliserez un `NSInputStream` pour fournir un accès en lecture seule pour diffuser des données et un `NSOutputStream` fournir un accès en écriture seule. Les flux de données doit être utilisée de manière demande et de réponse, où l’application de réception demande davantage de données et lui fournit l’application d’origine. Afin que les données écrites dans le flux de sortie sur l’appareil d’origine sont lues à partir du flux d’entrée sur l’appareil continue et vice versa.

Même dans les situations où la Continuation Stream sont requises, il doit être un minime de back allers et retours communication entre les deux applications.

Pour plus d’informations, consultez d’Apple [Using Continuation flux](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Handoff/AdoptingHandoff/AdoptingHandoff.html#//apple_ref/doc/uid/TP40014338-CH2-SW13) documentation.

## <a name="handoff-best-practices"></a>Meilleures pratiques de remise

La mise en œuvre réussie de la continuation transparente d’une activité utilisateur par le biais de remise nécessite une conception minutieuse en raison de tous les différents composants impliqués. Apple suggère d’adopter les meilleures pratiques de suivi pour vos applications de remise activé :

- Concevez vos activités d’utilisateur pour exiger la charge utile de plus petite possible d’associer l’état de l’activité de se poursuivre. Plus la charge utile, plus il faut la continuation pour démarrer.
- Si vous devez transférer de grandes quantités de données pour la continuation de réussite, prenez en compte les coûts impliqués dans la configuration et la charge réseau.
- Il est courant pour une grande application Mac créer des activités des utilisateurs qui sont gérées par plusieurs, plus petites, les applications spécifiques aux tâches sur les appareils iOS. L’autre application et les versions de système d’exploitation doivent être conçues pour bien fonctionner ensemble ou échouer normalement.
- Lorsque vous spécifiez vos Types d’activité, utilisez la notation DNS inverse afin d’éviter les collisions. Si une activité est spécifique à une application donnée, son nom doit être inclus dans la définition de type (par exemple `com.myCompany.myEditor.editing`). Si l’activité puisse fonctionner entre plusieurs applications, supprimez le nom de l’application à partir de la définition (par exemple `com.myCompany.editing`).
- Si votre application a besoin mettre à jour l’état d’une activité de l’utilisateur (`NSUserActivity`) définir la `NeedsSave` propriété `true`. Aux moments opportuns et remise appellera le délégué `UserActivityWillSave` méthode, vous pouvez mettre à jour le `UserInfo` dictionnaire en fonction des besoins.
- Étant donné que le processus de remise ne peut pas initialiser instantanément sur l’appareil de réception, vous devez implémenter le `AppDelegate`de `WillContinueUserActivity` et informer l’utilisateur qu’une continuation est prêt à démarrer.

## <a name="example-handoff-app"></a>Exemple d’application Handoff

Ainsi, à l’aide de la procédure de transfert dans une application Xamarin.iOS, nous avons inclus la [ **MonkeyBrowser** ](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/) exemple d’application avec ce guide. L’application comporte quatre onglets que l’utilisateur peut utiliser pour parcourir le web, chacun avec un type d’activité donné : Météo, Favoris, pause café et travail.

Sur n’importe quel onglet, lorsque l’utilisateur entre une nouvelle URL et appuie sur le **accédez** un nouveau bouton `NSUserActivity` est créé pour cet onglet qui contient l’URL de l’utilisateur parcourt actuellement :

[![](handoff-images/handoff01.png "Exemple d’application Handoff")](handoff-images/handoff01.png#lightbox)

Si un autre des périphériques de l’utilisateur a le **MonkeyBrowser** application installée, est connecté à iCloud à l’aide du même compte d’utilisateur, est sur le même réseau et à proximité de l’appareil ci-dessus, l’activité de procédure de transfert s’affichera sur la page d’accueil écran (dans le coin inférieur gauche) :

[![](handoff-images/handoff02.png "L’activité de la remise affichée sur l’écran d’accueil dans le coin inférieur gauche")](handoff-images/handoff02.png#lightbox)

Si l’utilisateur fait glisser vers le haut sur l’icône de transfert, l’application est lancée et l’activité de l’utilisateur spécifié dans le `NSUserActivity` sera poursuivie sur le nouveau périphérique :

[![](handoff-images/handoff03.png "Suite de l’activité des utilisateurs sur le nouvel appareil")](handoff-images/handoff03.png#lightbox)

Lorsque l’activité des utilisateurs a été correctement envoyée à un autre appareil Apple, l’appareil d’envoi `NSUserActivity` reçoit un appel à la `UserActivityWasContinued` (méthode) sur ses `NSUserActivityDelegate` pour lui indiquer que l’activité des utilisateurs a été transférée vers un autre appareil.

## <a name="summary"></a>Récapitulatif

Cet article a donné une introduction à l’infrastructure de remise utilisée afin de poursuivre une activité de l’utilisateur entre plusieurs des appareils Apple de l’utilisateur. Ensuite, il vous a montré comment activer et mettre en œuvre de remise dans une application Xamarin.iOS. Enfin, il décrit les différents types de continuations de remise disponibles et les meilleures pratiques de remise.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Exemple de MonkeyBrowser](https://developer.xamarin.com/samples/monotouch/ios8/MonkeyBrowser/)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Quelles sont les nouveautés dans iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guide de HomeKitDeveloper](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [Recommandations pour l’Interface utilisateur de HomeKit](https://developer.apple.com/homekit/ui-guidelines/)
- [Référence de l’infrastructure de HomeKit](https://developer.apple.com/library/ios/home_kit_framework_ref)

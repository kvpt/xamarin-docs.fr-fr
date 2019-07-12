---
title: Raccourcis de Siri dans Xamarin.iOS
description: Ce document décrit comment utiliser les raccourcis de Siri dans iOS 12. Il aborde NSUserActivities, objectifs personnalisés, affectation de raccourcis de la voix, les raccourcis pertinentes et bien plus encore.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 62b577393a498587ee2e2e899d20bfeec7988c4e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832377"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Raccourcis de Siri dans Xamarin.iOS

Dans [iOS 10](~/ios/platform/sirikit/index.md), Apple a introduit SiriKit, ce qui permet à la messagerie de la build, appel VoIP, paiements, le sommeil, puisse surfer sur la réservation et la photo de rechercher des applications qui interagissent avec Siri.

Dans [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit acquise prise en charge de plusieurs types d’applications et une plus grande souplesse pour la personnalisation de l’interface utilisateur.

iOS 12 ajoute des raccourcis Siri, ce qui permet de tous les types d’applications d’exposer leurs fonctionnalités à Siri. Siri apprend lorsque certaines tâches en fonction des applications sont plus pertinentes pour l’utilisateur et utilise ces connaissances pour suggérer des actions éventuelles via _raccourcis_. En appuyant sur un raccourci ou appeler avec une commande vocale sera ouvrir une application ou exécuter une tâche en arrière-plan.

Raccourcis doivent servir à accélérer la capacité d’un utilisateur pour accomplir une tâche courante – dans de nombreux cas sans avoir à ouvrir l’application en question.

## <a name="sample-app-soup-chef"></a>Exemple d’application : Soupe Chef

Pour mieux comprendre les raccourcis de Siri, examinons le [soupe Chef](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/) exemple d’application. Chef de soupe permet aux utilisateurs de passer des commandes à partir d’un restaurant soupe imaginaire, afficher leur historique de commande et définir des expressions à utiliser lors de la commande soupe en interagissant avec Siri.

> [!TIP]
> Avant de tester la soupe Chef sur un appareil ou un simulateur iOS 12, activez les deux paramètres suivants, qui sont utiles lors du débogage des raccourcis :
>
> - Dans le **paramètres** application, activer **développeur > raccourcis récentes affichage**.
> - Dans le **paramètres** application, activer **développeur > dons d’affichage sur écran de verrouillage**.
>
> Ces déboguer make paramètres facilement trouver récemment créés (au lieu de prédite) raccourcis sur iOS écran de verrouillage et écran de recherche.

Pour utiliser l’exemple d’application :

- Installer et exécuter le Chef de soupe exemple d’application sur un simulateur iOS 12 ou [appareil](#testing-on-device).
- Cliquez sur le **+** bouton dans l’angle supérieur droit pour créer une nouvelle commande.
- Sélectionnez un type de soupe, spécifiez une quantité et les options, puis appuyez sur **passer une commande**.
- Sur le **Order History** écran, appuyez sur la commande qui vient d’être créé pour afficher ses détails.
- En bas de l’écran de détails de commande, appuyez sur **ajouter à Siri**.
- Enregistrer une phrase de la voix pour associer à la commande et appuyez sur **fait**.
- Réduire la soupe Chef, appeler Siri et passer la commande à nouveau à l’aide de l’expression de voix que vous venez d’enregistrer.
- Siri après l’ordre, ouvrez à nouveau soupe Chef et notez que la nouvelle commande est répertoriée sur la **Order History** écran.

L’exemple d’application montre comment :

- [Utilisez un raccourci de NSUserActivity pour ouvrir une application](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Utiliser un raccourci intent personnalisé pour effectuer une tâche](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Fournir une interface utilisateur pour une intention personnalisée](#providing-a-user-interface-for-a-custom-intent).
- [Créer un raccourci de voix](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info.plist et Entitlements.plist

Avant d’Explorer le plus profondément au code soupe Chef, examinons son **Info.plist** et **Entitlements.plist** fichiers.

### <a name="infoplist"></a>Info.plist

Le **Info.plist** de fichiers dans le **SoupChef** projet définit le **identificateur de Bundle** comme `com.xamarin.SoupChef`. Cet identificateur de bundle sera être utilisé comme préfixe pour les identificateurs d’offre groupée de l’Intents et Intents UI extensions décrites plus loin dans ce document.

Le **Info.plist** fichier contient également les éléments suivants :

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Cela `NSUserActivityTypes` paire clé/valeur indique que le Chef de soupe sache comment gérer un `OrderSoupIntent`et un [ `NSUserActivity` ](xref:Foundation.NSUserActivity) ayant une [ `ActivityType` ](xref:Foundation.NSUserActivity.ActivityType) de « com.xamarin.SoupChef.viewMenu ».

Activités et les objectifs personnalisés transmis à l’application elle-même, par opposition à ses extensions, sont gérées dans le `AppDelegate` (un [ `UIApplicationDelegate` ](xref:UIKit.UIApplicationDelegate) par le [ `ContinueUserActivity` ](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) (méthode).

### <a name="entitlementsplist"></a>Entitlements.plist

Le **Entitlements.plist** de fichiers dans le **SoupChef** projet contient les éléments suivants :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Cette configuration indique que l’application utilise le groupe d’application « group.com.xamarin.SoupChef ». Le **SoupChefIntents** extension d’application utilise ce même groupe d’application, ce qui permet les deux projets à partager [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
Données.

Le `com.apple.developer.siri` clé indique que l’application interagit avec Siri.

> [!NOTE]
> Le **SoupChef** jeux de configuration de build du projet **droits personnalisés** à **Entitlements.plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>À l’aide d’un raccourci de NSUserActivity pour ouvrir une application

Pour créer un raccourci qui ouvre une application pour afficher le contenu spécifique, créez un `NSUserActivity` et l’attacher à un contrôleur d’affichage de l’écran que vous souhaitez que le raccourci à ouvrir.

### <a name="setting-up-an-nsuseractivity"></a>Configuration d’un NSUserActivity

Dans l’écran de menu, `SoupMenuViewController` crée un `NSUserActivity` et lui attribue le contrôleur d’affichage [ `UserActivity` ](xref:UIKit.UIResponder.UserActivity) propriété :

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

Définition de la `UserActivity` propriété _contribue_ l’activité de Siri. À partir de ce don, Siri gains d’informations sur quand et où cette activité se rapporte à l’utilisateur et qu’il apprend à mieux le proposer à l’avenir.

`NSUserActivityHelper` est une classe utilitaire incluse dans le **SoupChef** solution, dans le **SoupKit** bibliothèque de classes. Il crée un `NSUserActivity` et définit les diverses propriétés relatives à Siri et de recherche :

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

Notez en particulier les points suivants :

- Paramètre `EligibleForPrediction` à `true` indique que Siri peut prédire cette activité et de faire apparaître sous forme de raccourci.
- Le [ `ContentAttributeSet` ](xref:Foundation.NSUserActivity.ContentAttributeSet) tableau est une norme [ `CSSearchableItemAttributeSet` ](xref:CoreSpotlight.CSSearchableItemAttributeSet) utilisé pour inclure un `NSUserActivity` dans les résultats de recherche iOS.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) est une expression qui Siri suggère à l’utilisateur comme un choix potentiel lorsque vous assignez une expression à un raccourci.

### <a name="handling-an-nsuseractivity-shortcut"></a>Gestion d’un raccourci NSUserActivity

Pour gérer un `NSUserActivity` raccourci appelé par un utilisateur, une application iOS doit remplacer le `ContinueUserActivity` méthode de la `AppDelegate` (classe), réponse basée sur le `ActivityType` champ du passé en `NSUserActivity` objet :

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Cette méthode appelle `HandleUserActivity`, qui recherche le segue à l’écran de menu et l’appelle :

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Affectation d’une expression à une NSUserActivity

Pour affecter une expression à une `NSUserActivity`, ouvrez le fichier iOS **paramètres** application et choisissez **Siri & recherche > Mes raccourcis**. Ensuite, sélectionnez le raccourci (dans ce cas, « Déjeuner de commande ») et enregistrer une phrase.

Appel de Siri et à l’aide de cette expression seront ouvre soupe Chef à l’écran de menu.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>À l’aide d’un raccourci intent personnalisé pour effectuer une tâche

### <a name="defining-a-custom-intent"></a>Définition d’une intention personnalisée

Pour fournir un raccourci qui permet à un utilisateur effectuer rapidement une tâche spécifique liée à votre application, créez une intention personnalisée. Une intention personnalisée représente une tâche à qu'un utilisateur peut souhaiter suivre, les paramètres relatifs à cette tâche et les réponses potentielles résultant de l’exécution de la tâche. Selon la façon dont une intention personnalisée est définie, appeler pouvez ouvrir votre application ou exécuter une tâche en arrière-plan.

Xcode 10 permet de créer des objectifs personnalisés. Dans le [SoupChef référentiel](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), l’intention personnalisée est définie dans **OrderSoupIntentCodeGen**, un projet Objective-C. Ouvrez ce projet, puis sélectionnez le **Intents.intentdefinition** de fichiers dans le **Project Navigator** pour afficher le **OrderSoup** intention.

Notez les points suivants :

- L’objectif a un **catégorie** de **ordre**. Il existe différentes catégories prédéfinies qui peuvent être utilisées pour les objectifs personnalisés ; Sélectionnez celle qui correspond le mieux à la tâche que permettra à votre intention personnalisée. Dans la mesure où il s’agit d’une application, de classement d’a **OrderSoupIntent** utilise **ordre**.
- Le **Confirmation** case à cocher indique ou non Siri doit demander confirmation avant d’exécuter la tâche. Pour le **ordre soupe** intention dans le Chef de soupe, cette option est activée dans la mesure où l’utilisateur effectue un achat.
- Le **paramètres** section du fichier .intentdefinition définit les paramètres relatifs à un raccourci. Pour placer une commande SOUPE, Chef de soupe doit connaître le type de soupe, sa quantité et toutes les options associées.
Chaque paramètre a un type ; paramètre qui ne peut pas être représentée par un type prédéfini sont définies en tant que **personnalisé**.
- Le **raccourci Types** interface décrit les différentes combinaisons de paramètre Siri peut utiliser lorsqu’elle suggère le raccourci. Associé **titre** et **sous-titre** sections permettent de définir les messages Siri utilisera lors de la présentation d’un raccourci suggéré à l’utilisateur.
- Le **prend en charge l’exécution d’arrière-plan** case à cocher doit être sélectionnée pour n’importe quel raccourci qui peut être exécuté sans ouvrir l’application pour l’interaction utilisateur supplémentaire.

### <a name="defining-custom-intent-responses"></a>Définition des réponses personnalisées intentions

Le **réponse** élément imbriqué sous le **OrderSoup** intention représente les réponses potentielles qui résultent d’une commande d’a.

Dans le **OrderSoup** définition de la réponse d’intention, notez les points suivants :

- D’une réponse **propriétés** peut être utilisé pour personnaliser le message renvoyé à l’utilisateur. Le **OrderSoup** réponse intent a **soupe** et **waitTime** propriétés.
- Le **modèles de réponse** spécifier les différents messages de réussite et d’échec qui peuvent être utilisées pour indiquer l’état après la fin tâche d’une intention.
- Le **réussite** case à cocher doit être sélectionnée pour les réponses qui indiquent la réussite.
- Le **OrderSoupIntent** réponse positive utilise le **soupe** et **waitTime** propriétés pour fournir un message convivial et utile décrivant lorsque l’ordre de soupe sera prêt.

### <a name="generating-code-for-the-custom-intent"></a>Génération de code dans un but personnalisé

Génération du projet Xcode qui contient cette définition intent personnalisée provoque Xcode générer le code qui peut être utilisé pour interagir par programmation avec l’intention personnalisée et ses réponses.

Pour afficher ce code généré :

- Ouvrez **AppDelegate.m**.
- Ajouter une importation de fichier d’en-tête de l’intention personnalisé : `#import "OrderSoupIntent.h"`
- Dans n’importe quelle méthode dans la classe, ajoutez une référence à `OrderSoupIntent`.
- Avec le bouton droit sur `OrderSoupIntent` et choisissez **atteindre la définition**.
- Avec le bouton droit dans le fichier qui vient d’être ouvert, **OrderSoupIntent.h**, puis sélectionnez **afficher dans le Finder**.
- Ceci ouvrira une **Finder** fenêtre qui contient un fichier .h et .m contenant le code généré.

Ce code généré inclut :

- `OrderSoupIntent` : Une classe qui représente l’intention personnalisée.
- `OrderSoupIntentHandling` – Un protocole qui définit les méthodes qui seront utilisés pour confirmer que l’objectif doit être exécutée et la méthode qui s’exécute réellement.
- `OrderSoupIntentResponseCode` – Une énumération qui définit les différents États de réponse.
- `OrderSoupIntentResponse` – une classe qui représente la réponse à l’exécution d’une intention.

### <a name="creating-a-binding-to-the-custom-intent"></a>Création d’une liaison à l’intention personnalisée

Pour utiliser le code généré par Xcode apparaissent dans une application Xamarin.iOS, créez un C# de liaison pour celui-ci.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Création d’une bibliothèque statique et C# définitions de liaison

Dans le [SoupChef référentiel](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), jetez un coup de œil le **OrderSoupIntentStaticLib** dossier, puis ouvrez le **OrderSoupIntentStaticLib.xcodeproj** projet Xcode.

Cela **Cocoa Touch une bibliothèque statique** projet contient le **OrderSoupIntent.h** et **OrderSoupIntent.m** fichiers générés par Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configuration des paramètres de build de projet de bibliothèque statique

Dans Xcode **Project Navigator**, sélectionnez le projet de niveau supérieur, **OrderSoupIntentStaticLib**et accédez à **Build Phases > Sources de compiler**.
Notez que **OrderSoupIntent.m** (qui importe **OrderSoupIntent.h**) n’est répertorié ici. Dans **Link Binary With Libraries**, notez que **Intents.framework** et **Foundation.framework** sont inclus.
Ces paramètres en place, le framework se génère correctement.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Création de la bibliothèque statique et en générant C# définitions de liaisons

Pour créer la bibliothèque statique et générer C# définitions de liaisons pour celle-ci, procédez comme suit :

- [Installer objectif Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), l’outil utilisé pour générer des définitions de liaisons à partir des fichiers .h et .m créées par Xcode.

- Configurer votre système pour utiliser les outils de ligne de commande Xcode 10 :

    > [!WARNING]
    > La mise à jour les outils de ligne de commande sélectionnée a un impact sur toutes les versions installées de Xcode sur votre système. Lorsque vous avez terminé à l’aide de la Chef soupe exemple d’application, veillez à rétablir ce paramètre à sa configuration d’origine.

    - Dans Xcode, choisissez **Xcode > Préférences > emplacements** et définissez **les outils de ligne de commande** à l’installation de Xcode 10 la plus récente disponible sur votre système.

- Dans le terminal, `cd` à la **OrderSoupIntentStaticLib** directory.

- Type `make`, quelles sont les builds :

    - La bibliothèque statique, **libOrderSoupIntentStaticLib.a**
    - Dans le **bo** répertoire de sortie, C# définitions de liaisons :
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

Le **OrderSoupIntentBindings** projet, qui s’appuie sur cette bibliothèque statique et ses définitions de liaisons associées, ces éléments est généré automatiquement.
Toutefois, en cours d’exécution manuellement via le processus ci-dessus garantit qu’il se génère comme prévu.

Pour plus d’informations sur la création d’une bibliothèque statique et à l’aide d’objectif Sharpie créer C# définitions de liaisons, examinons le [liaison d’une bibliothèque iOS Objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) procédure pas à pas.

#### <a name="creating-a-bindings-library"></a>Création d’une bibliothèque de liaisons

Avec la bibliothèque statique et le C# liaisons définitions créées, la partie restante nécessaires à l’utilisation de l’objet généré par le Xcode code lié à l’intention dans un projet Xamarin.iOS est une bibliothèque de liaisons.

Dans le [soupe Chef référentiel](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), ouvrez le **SoupChef.sln** fichier. Entre autres choses, cette solution contient **OrderSoupIntentBinding**, une bibliothèque de liaisons pour la bibliothèque statique générée ci-dessus.

En particulier, notez que ce projet comprend :

- **ApiDefinitions.cs** – un fichier généré ci-dessus par objectif Sharpie et ajoutés à ce projet. De ce fichier **Action de génération** a la valeur **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** – un autre fichier généré ci-dessus par objectif Sharpie et ajoutés à ce projet. De ce fichier **Action de génération** a la valeur **ObjcBindingCoreSource**.
- Un **référence Native** à **libOrderSoupIntentStaticLib.a**, la bibliothèque statique générée ci-dessus.

> [!NOTE]
> Les deux **ApiDefinitions.cs** et **StructsAndEnums.cs** contiennent des attributs tels que `[Watch (5,0), iOS (12,0)]`. Ces attributs, générés par objectif Sharpie, ont été placées en commentaire dans la mesure où ils ne sont pas nécessaires pour ce projet.

Pour plus d’informations sur la création d’un C# bibliothèque de liaisons, jetez un coup de œil à la [liaison d’une bibliothèque Objective-C iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) procédure pas à pas.

Notez que le **SoupChef** projet contient une référence à **OrderSoupIntentBinding**, ce qui signifie qu’il peut désormais accéder, en C#, classes, interfaces et énumérations qu’il contient :

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Ajout du fichier de .intentdefinition à votre solution

Dans le C# **SoupChef** solution, le **SoupKit** projet contient du code partagé entre l’application et ses extensions. Le **Intents.intentdefinition** fichier a été placé dans le **Base.lproj** répertoire de **SoupKit**, et il a un **Action de génération** de **Contenu**. Le processus de génération copie ce fichier dans le bundle d’applications soupe Chef, où il est requis pour l’application de fonctionner correctement.

### <a name="donating-an-intent"></a>Donnez une intention

Dans l’ordre de Siri suggérer un raccourci, il doit d’abord comprendre lorsque le raccourci est pertinent.

À cette présentation, la soupe Chef Siri _contribue_ une intention de Siri chaque fois que l’utilisateur place un ordre d’a. Selon cette don – lorsqu’il a été donné, où il a été a donné, les paramètres qu’il contient : Siri apprend lorsque suggérer le raccourci dans le futur.

**SoupChef** utilise le `SoupOrderDataManager` classe pour placer des dons.
Lorsqu’elle est appelée pour placer une soupe pour qu’un utilisateur, le `PlaceOrder` méthode appelle à son tour [ `DonateInteraction` ](xref:Intents.INInteraction.DonateInteraction*):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

Après la lecture d’une intention, il est encapsulé dans un [ `INInteraction` ](xref:Intents.INInteraction).
Le `INInteraction` reçoit un [`Identifier`](xref:Intents.INInteraction.Identifier*)
qui correspond à l’ID unique de l’ordre (cela sera utile ultérieurement lors de la suppression des dons intentions qui ne sont plus valides). Ensuite, l’interaction est donnée à Siri.

L’appel à la `order.Intent` getter extractions un `OrderSoupIntent` qui représente l’ordre en définissant son `Quantity`, `Soup`, `Options`et d’image et d’une expression d’appel à utiliser comme une suggestion lorsque l’utilisateur enregistre une phrase pour Siri à associer dans le but :

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Suppression des dons non valides

Il est important de supprimer les dons qui ne sont plus valides afin que Siri ne rend pas les suggestions de raccourci inutiles ou à confusion.

Dans le Chef d’a, le **Menu configurer** écran peut être utilisé pour marquer un menu élément comme étant indisponible. Siri doit proposer n’est plus un raccourci pour commander un élément de menu non disponibles, donc la `RemoveDonation` méthode de `SoupMenuManager` supprime les dons des éléments de menu qui ne sont plus disponibles. Pour ce faire :

- Recherche des commandes associées à l’élément de menu indisponible pour l’instant.
- En saisissant leurs identificateurs.
- Suppression des interactions qui ont ce même identificateur.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Création d’une extension Intents

Le code qui s’exécute lorsque Siri appelle une intention est placé dans une extension Intents, qui peut être ajoutée en tant que nouveau projet à la solution comme une application Xamarin.iOS existante comme le Chef d’a. Dans le **SoupChef** solution, l’extension est appelée **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – Info.plist et Entitlements.plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info.plist

Le **Info.plist** dans le **SoupChefIntents** projet définit le **identificateur de Bundle** comme `com.xamarin.SoupChef.SoupChefIntents`.

Le **Info.plist** fichier contient également les éléments suivants :

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

Dans l’exemple ci-dessus **Info.plist**:

- `IntentsRestrictedWhileLocked` Répertorie les intentions qui doivent être gérées quand l’appareil est déverrouillé.
- `IntentsSupported` Répertorie les intentions de gérée par cette extension.
- `NSExtensionPointIdentifier` Spécifie le type d’extension d’application (consultez [documentation d’Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) pour plus d’informations).
- `NSExtensionPrincipalClass` Spécifie la classe qui doit être utilisée pour gérer les modes pris en charge par cette extension.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – fichier Entitlements.plist

Le **Entitlements.plist** dans le **SoupChefIntents** projet possède le **App Groups** fonctionnalité. Cette fonctionnalité est configurée pour utiliser le même groupe de l’application que le **SoupChef** projet :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Chef de soupe rend persistantes les données avec `NSUserDefaults`. Afin de partager des données entre l’application et l’extension d’application, ils référencent le même groupe d’application dans leur **Entitlements.plist** fichiers.

> [!NOTE]
> Le **SoupChefIntents** jeux de configuration de build du projet **droits personnalisés** à **Entitlements.plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Gestion d’une tâche d’arrière-plan OrderSoupIntent

Une extension Intents exécute les tâches de base nécessaires pour créer un raccourci selon une intention personnalisée.

Les appels de Siri le [ `GetHandler` ](xref:Intents.INExtension.GetHandler*) méthode de la `IntentHandler` classe (défini dans **Info.plist** en tant que le `NSExtensionPrincipalClass`) pour obtenir une instance d’une classe qui étend `OrderSoupIntentHandling`, qui peut être utilisé pour gérer un `OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, défini dans le **SoupKit** projet de code partagé, les méthodes importantes implémente deux :

- `ConfirmOrderSoup` – Confirme si la tâche associée à l’intention doit réellement être exécutée.
- `HandleOrderSoup` : Place l’ordre d’a et répond à l’utilisateur en appelant le Gestionnaire d’achèvement dans le passé

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Gère un OrderSoupIntent qui ouvre l’application

Une application doit gérer correctement les intentions qui n’exécutent pas en arrière-plan.
Celles-ci sont gérées dans la même façon que `NSUserActivity` raccourcis, dans le `ContinueUserActivity` méthode de `AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>En fournissant une interface utilisateur pour une intention personnalisée

Une extension d’IU Intents fournit une interface utilisateur personnalisée pour une extension Intents. Dans le **SoupChef** solution, **SoupChefIntentsUI** est une extension d’IU Intents qui fournit une interface pour **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info.plist et Entitlements.plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info.plist

Le **Info.plist** dans le **SoupChefIntentsUI** projet définit le **identificateur de Bundle** comme `com.xamarin.SoupChef.SoupChefIntentsui`.

Le **Info.plist** fichier contient également les éléments suivants :

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

Dans l’exemple ci-dessus **Info.plist**:

- `IntentsSupported` Indique que le `OrderSoupIntent` est gérée par cette extension Intents UI.
- `NSExtensionPointIdentifier` Spécifie le type d’extension d’application (consultez [documentation d’Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) pour plus d’informations).
- `NSExtensionMainStoryboard` Spécifie la table de montage séquentiel qui définit l’interface principale de cette extension

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – Entitlements.plist

Le **SoupChefIntentsUI** projet n’a pas besoin un **Entitlements.plist** fichier.

### <a name="creating-the-user-interface"></a>Création de l’interface utilisateur

Dans la mesure où le **Info.plist** pour **SoupChefIntentsUI** définit le `NSExtensionMainStoryboard` clé à `MainInterface`, le **MainInterace.storyboard** fichier définit l’interface pour l’extension Intents UI.

Dans ce plan conceptuel, il est un contrôleur d’affichage unique, de type **IntentViewController**. Il fait référence à deux vues :

- **invoiceView**, de type `InvoiceView`
- **confirmationView**, de type `ConfirmOrderView`

> [!NOTE]
> Les interfaces pour **invoiceView** et **confirmationView** sont définis dans **Main.storyboard** en tant que vues secondaire. Le concepteur dans Visual Studio pour Mac et Visual Studio 2017 iOS ne fournit pas de prise en charge pour l’affichage ou modification des vues secondaire ; Pour ce faire, ouvrez **Main.storyboard** dans Interface Builder de Xcode.

`IntentViewController` implémente le [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interface, utilisée pour fournir une interface personnalisée lorsque vous travaillez avec des intentions Siri. Le [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
méthode est appelée pour personnaliser l’interface, affichage de la confirmation ou la facture, selon si l’interaction est en cours confirmée ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) ou a été exécutée avec succès ([ `INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)):

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Pour plus d’informations sur la `ConfigureView` (méthode), regardez la présentation d’Apple WWDC 2017 [What ' s New in SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Création d’un raccourci de la voix

Soupe Chef fournit une interface pour affecter un raccourci de la voix à chaque commande, ce qui permet à la soupe ordre avec Siri. En fait, l’interface utilisée pour enregistrer et affecter des raccourcis de la voix est fourni par iOS et nécessite peu de code personnalisé.

Dans `OrderDetailViewController`, lorsqu’un utilisateur appuie sur la table **ajouter à Siri** ligne, le [ `RowSelected` ](xref:UIKit.UITableViewSource.RowSelected*) méthode présente un écran pour ajouter ou modifier un raccourci vocal :

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

Basé sur ou non un raccourci existant de la voix existe pour la commande actuellement affiché, `RowSelected` présente un contrôleur d’affichage de type [ `INUIEditVoiceShortcutViewController` ](xref:IntentsUI.INUIEditVoiceShortcutViewController) ou [ `INUIAddVoiceShortcutViewController` ](xref:IntentsUI.INUIAddVoiceShortcutViewController).
Dans chaque cas, `OrderDetailViewController` définit lui-même en tant que le contrôleur d’affichage `Delegate`, c’est pourquoi il implémente également [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
et [ `IINUIEditVoiceShortcutViewControllerDelegate` ](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Tests sur l’appareil

Pour exécuter la soupe Chef sur un appareil, suivez les instructions ci-dessous. Lisez également le [Remarque sur l’approvisionnement automatique](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Application ID de groupe, application, profils de provisionnement

Dans le **Certificates, Identifiers & Profiles** section de la [portail des développeurs Apple](https://developer.apple.com/), procédez comme suit :

- Créez un groupe d’application pour partager des données entre l’application soupe Chef et ses extensions. Par exemple : **group.com.yourcompanyname.SoupChef**

- Créer trois ID d’application : un pour l’application elle-même, l’autre pour l’extension Intents et un pour l’extension Intents UI. Par exemple :

    - Application : **com.yourcompanyname.SoupChef**
        - Cet ID d’application, affectez la SiriKit et **App Groups** fonctionnalités.

    - Extension Intents : **com.yourcompanyname.SoupChef.Intents**
        - Cet ID d’application, affectez la **App Groups** fonctionnalité.

    - Extension d’IU Intents : **com.yourcompanyname.SoupChef.Intentsui**
        - Cet ID d’application a besoin d’aucune fonction spéciale.

- Après avoir créé les ID d’application ci-dessus, vous devez modifier le **App Groups** capacité affectée à l’application et l’extension Intents, en spécifiant le groupe de l’application spécifique créé ci-dessus.

- Créez trois nouveaux développement profils de provisionnement, un pour chacun des nouveaux ID d’application.

- Téléchargez ces profils de provisionnement et double-cliquez sur chacun d’eux pour l’installer. Si Visual Studio pour Mac ou Visual Studio 2017 est déjà en cours d’exécution, redémarrez pour vous assurer qu’il enregistre les nouveaux profils d’approvisionnement.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Modification de fichier Info.plist, Entitlements.plist et code source

Dans Visual Studio pour Mac ou Visual Studio 2017, procédez comme suit :

- Mettre à jour les différents **Info.plist** fichiers dans la solution. Définir l’application, une extension Intents et une extension d’IU Intents **identificateur de Bundle** à l’ID d’application définis ci-dessus :

    - Application : **com.yourcompanyname.SoupChef**
    - Extension Intents : **com.yourcompanyname.SoupChef.Intents**
    - Extension d’IU Intents : **com.yourcompanyname.SoupChef.Intentsui**

- Mise à jour le **Entitlements.plist** de fichiers pour le **SoupChef** projet :
    - Pour le **App Groups** fonctionnalité, définissez le groupe vers le nouveau groupe d’application créé précédemment (dans l’exemple ci-dessus, il a été **group.com.yourcompanyname.SoupChef**).
    - Assurez-vous que l’option **SiriKit** est activé.

- Mise à jour le **Entitlements.plist** de fichiers pour le **SoupChefIntents** projet :
    - Pour le **App Groups** fonctionnalité, définissez le groupe vers le nouveau groupe d’application créé précédemment (dans l’exemple ci-dessus, il a été **group.com.yourcompanyname.SoupChef**).

- Pour finir, ouvrez **NSUserDefaultsHelper.cs**. Définir le `AppGroup` variable à la valeur de votre nouveau groupe de l’application (par exemple, définissez-le sur `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Configuration des paramètres de build

Dans Visual Studio pour Mac ou Visual Studio 2017 :

- Ouvrez les options/Propriétés de la **SoupChef** projet. Sur le **signature du Bundle iOS** onglet, définissez **identité de signature** en mode automatique et **profil de provisionnement** pour l’approvisionnement de la nouvelle spécifiques de l’application de profil que vous avez créé ci-dessus.

- Ouvrez les options/Propriétés de la **SoupChefIntents** projet. Sur le **signature du Bundle iOS** onglet, définissez **identité de signature** en mode automatique et **profil de provisionnement** pour les intentions de nouveau profil de configuration spécifiques à l’extension que vous avez créé ci-dessus.

- Ouvrez les options/Propriétés de la **SoupChefIntentsUI** projet. Sur le **signature du Bundle iOS** onglet, définissez **identité de signature** en mode automatique et **profil de provisionnement** à la nouvelle interface utilisateur des intentions spécifiques à l’extension de profil de provisionnement vous créé ci-dessus.

Avec ces modifications en place, l’application s’exécutera sur un appareil iOS.

### <a name="automatic-provisioning"></a>Provisionnement automatique

Notez que vous pouvez utiliser [l’approvisionnement automatique](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) pour exécuter la plupart de ces tâches directement dans l’IDE de configuration.
Toutefois, l’approvisionnement automatique ne configure pas les groupes d’applications. Vous devez configurer manuellement le **Entitlements.plist** fichiers portant le nom du groupe d’application que vous souhaitez utiliser, visitez le portail des développeurs Apple pour créer le groupe de l’application, affectez ce groupe de l’application à chaque ID d’application créé par automatique approvisionnement, régénérez les profils de provisionnement (application, extension Intents, extension d’IU Intents) à inclure le groupe de l’application nouvellement créée, puis télécharger et les installer.

## <a name="related-links"></a>Liens connexes

- [Chef d’a (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [Chef d’a (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introduction aux raccourcis Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Création de voix avec les raccourcis de Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Raccourcis de Siri sur la Face Siri espion – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [Quelles sont les nouveautés dans SiriKit – WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Créer une Extension d’application Intents (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)

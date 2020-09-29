---
title: HealthKit dans Xamarin. iOS
description: Ce document décrit HealthKit, une infrastructure introduite dans iOS 8 qui fournit une banque de données centralisée, coordonnée et sécurisée pour les informations relatives à l’intégrité. Il explique comment configurer une application HealthKit et comment écrire du code qui utilise l’infrastructure HealthKit.
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 3d5bdfd78658803dcbb159101050aea48008b69e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435231"
---
# <a name="healthkit-in-xamarinios"></a>HealthKit dans Xamarin. iOS

Health kit fournit une banque de données sécurisée pour les informations relatives à l’intégrité de l’utilisateur. Les applications du kit d’intégrité peuvent, avec l’autorisation explicite de l’utilisateur, lire et écrire dans ce magasin de données et recevoir des notifications lorsque des données pertinentes sont ajoutées. Les applications peuvent présenter les données, ou les utilisateurs peuvent utiliser l’application d’intégrité fournie par Apple pour afficher un tableau de bord de toutes leurs données.

Étant donné que les données liées à l’intégrité sont tellement sensibles et cruciales, le kit d’intégrité est fortement typé, avec des unités de mesure et une association explicite avec le type d’informations enregistrées (par exemple, le niveau de glucose sanguin ou le taux cardiaque). En outre, les applications du kit d’intégrité doivent utiliser des droits explicites, doivent demander l’accès aux types d’informations particuliers et l’utilisateur doit explicitement accorder à l’application l’accès à ces types de données.

Cet article présente les éléments suivants :

- Exigences de sécurité du kit de contrôle d’intégrité, y compris l’approvisionnement des applications et l’autorisation des utilisateurs pour accéder à la base de données du kit d’intégrité ;
- Système de type du kit de contrôle d’intégrité, qui minimise la possibilité d’appliquer ou d’interpréter les données de manière incorrecte ;
- Écriture dans le magasin de banques de contrôle d’intégrité partagé à l’ensemble du système.

Cet article ne couvre pas les sujets plus avancés, tels que l’interrogation de la base de données, la conversion entre unités de mesure ou la réception de notifications de nouvelles données.

Dans cet article, nous allons créer un exemple d’application pour enregistrer le taux cardiaque de l’utilisateur :

[![Exemple d’application pour enregistrer le tarif cardiaque des utilisateurs](healthkit-images/image01.png)](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article :

- **Xcode 7 et iOS 8 (ou version ultérieure)** : les dernières API Xcode et iOS d’Apple doivent être installées et configurées sur l’ordinateur du développeur.
- **Visual Studio pour Mac ou Visual Studio** : la dernière version de Visual Studio pour Mac doit être installée et configurée sur l’ordinateur du développeur.
- **appareil iOS 8 (ou supérieur)** : appareil iOS exécutant la dernière version d’iOS 8 ou version ultérieure à des fins de test.

> [!IMPORTANT]
> Le kit de contrôle d’intégrité a été introduit dans iOS 8. Actuellement, le kit de contrôle d’intégrité n’est pas disponible sur le simulateur iOS et le débogage nécessite une connexion à un appareil iOS physique.

## <a name="creating-and-provisioning-a-health-kit-app"></a>Création et configuration d’une application de kit de contrôle d’intégrité
Pour qu’une application Xamarin iOS 8 puisse utiliser l’API HealthKit, elle doit être correctement configurée et approvisionnée. Cette section décrit les étapes nécessaires à la configuration correcte de votre application Xamarin.

Les applications du kit d’intégrité requièrent :

- ID d' **application**explicite.
- Un **profil de provisionnement** associé à cet **ID d’application** explicite et aux autorisations du **Kit d’intégrité** .
- `Entitlements.plist`Avec une `com.apple.developer.healthkit` propriété de type ayant la `Boolean` valeur `Yes` .
- `Info.plist`Dont `UIRequiredDeviceCapabilities` la clé contient une entrée avec la `String` valeur `healthkit` .
- Le `Info.plist` doit également avoir des entrées d’explication confidentielles appropriées : une `String` explication de la clé `NSHealthUpdateUsageDescription` si l’application va écrire des données et une `String` explication pour la clé `NSHealthShareUsageDescription` si l’application doit lire les données du kit d’intégrité.

Pour plus d’informations sur la configuration d’une application iOS, consultez l’article [Configuration des appareils](~/ios/get-started/installation/device-provisioning/index.md) dans la série de **prise en main** de Xamarin décrit la relation entre les certificats de développeur, les ID d’application, les profils de provisionnement et les droits d’application.

<a name="explicit-appid"></a>

### <a name="explicit-app-id-and-provisioning-profile"></a>ID d’application explicite et profil de provisionnement

La création d’un **ID d’application** explicite et d’un **profil de provisionnement** approprié s’effectue dans le centre de [développement iOS](https://developer.apple.com/devcenter/ios/index.action)d’Apple. 

Vos **ID d’application** actuels sont répertoriés dans la section [certificats, identificateurs & profils](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action) du centre de développement. Souvent, cette liste indique les valeurs d' **ID** de `*` , indiquant que le nom de l' **ID d’application**  -  **Name** peut être utilisé avec un nombre quelconque de suffixes. Ces *ID d’application générique* ne peuvent pas être utilisés avec le kit de contrôle d’intégrité.

Pour créer un **ID d’application**explicite, cliquez sur le **+** bouton en haut à droite pour ouvrir la page **inscrire l’ID d’application iOS** :

[![Inscription d’une application sur le portail des développeurs Apple](healthkit-images/image02.png)](healthkit-images/image02.png#lightbox)

Comme indiqué dans l’image ci-dessus, après avoir créé une description d’application, utilisez la section **ID d’application explicite** pour créer un ID pour votre application. Dans la section **app services** , cochez **Health Kit** dans la section **activer les services** .

Lorsque vous avez terminé, appuyez sur le bouton **Continuer** pour enregistrer l' **ID d’application** dans votre compte. Vous serez redirigé vers la page **certificats, identificateurs et profils** . Cliquez sur **Configuration des profils** pour afficher la liste de vos profils de provisionnement actuels, puis cliquez sur le **+** bouton situé dans l’angle supérieur droit pour vous connecter à la page Ajouter un profil d' **approvisionnement iOS** . Sélectionnez l’option **développement d’applications iOS** , puis cliquez sur **Continuer** pour accéder à la page Sélectionner l’ID de l' **application** . Ici, sélectionnez l' **ID d’application** explicite que vous avez spécifié précédemment :

[![Sélectionner l’ID d’application explicite](healthkit-images/image03.png)](healthkit-images/image03.png#lightbox)

Cliquez sur **continuer et passez** en revue les écrans restants, où vous allez spécifier vos **certificat (s) de développeur**, **appareil (s)** et un **nom** pour ce **profil de provisionnement**:

[![Génération du profil de provisionnement](healthkit-images/image04.png)](healthkit-images/image04.png#lightbox)

Cliquez sur **générer** et attendez la création de votre profil. Téléchargez le fichier et double-cliquez dessus pour l’installer dans Xcode. Vous pouvez confirmer son installation dans **Xcode > Preferences > accounts > afficher les détails...** Vous devez voir votre profil de provisionnement d’installation juste-à-côte et l’icône du kit de contrôle d’intégrité et de tout autre service spécial sur la ligne **droits** :

[![Affichage du profil dans Xcode](healthkit-images/image05.png)](healthkit-images/image05.png#lightbox)

<a name="associating-appid"></a>

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>Association de l’ID d’application et du profil de provisionnement à votre application Xamarin. iOS

Une fois que vous avez créé et installé un **profil de provisionnement** approprié comme décrit, il est normalement temps de créer une solution dans Visual Studio pour Mac ou Visual Studio. L’accès au kit de contrôle d’intégrité est disponible pour tous les projets iOS C# ou F #.

Au lieu de suivre le processus de création d’un projet Xamarin iOS 8 à la main, ouvrez l’exemple d’application joint à cet article (qui comprend un Storyboard et du code prédéfinis). Pour associer l’exemple d’application à votre profil de **configuration**compatible avec le kit de contrôle d’intégrité, dans la **panneau solutions**, cliquez avec le bouton droit sur votre projet et affichez la boîte de dialogue **options** . Basculez vers le panneau de l' **application iOS** et entrez l' **ID d’application** explicite que vous avez créé précédemment comme **identificateur de Bundle**de l’application :

[![Entrer l’ID d’application explicite](healthkit-images/image06.png)](healthkit-images/image06.png#lightbox)

Maintenant, basculez vers le panneau de signature de l' **offre groupée iOS** . Votre **profil d’approvisionnement**récemment installé, avec son association à l' **ID d’application**explicite, est désormais disponible en tant que **profil de provisionnement**:

[![Sélectionner le profil de provisionnement](healthkit-images/image07.png)](healthkit-images/image07.png#lightbox)

Si le **profil de configuration** n’est pas disponible, vérifiez l’identificateur du **Bundle** dans le panneau de l' **application iOS** par rapport à celui spécifié dans le **Centre de développement iOS** et que le **profil de configuration** est installé (**Xcode > Preferences > Accounts > afficher les détails...**).

Lorsque le **profil de configuration** compatible avec le kit d’intégrité est sélectionné, cliquez sur **OK** pour fermer la boîte de dialogue Options du projet.

### <a name="entitlementsplist-and-infoplist-values"></a>Valeurs d’habilitations. plist et info. plist

L’exemple d’application inclut un `Entitlements.plist` fichier (qui est nécessaire pour les applications compatibles avec le kit de contrôle d’intégrité) et n’est pas inclus dans chaque modèle de projet. Si votre projet n’inclut pas de droits d’accès, cliquez avec le bouton droit sur votre projet, choisissez **fichier > nouveau fichier... > iOS > habilitations. plist** pour en ajouter un manuellement.

Au final, vous `Entitlements.plist` devez disposer de la paire clé/valeur suivante :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

De même, le `Info.plist` pour l’application doit avoir une valeur `healthkit` associée à la `UIRequiredDeviceCapabilities` clé :

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

L’exemple d’application fourni avec cet article comprend un préconfiguré `Entitlements.plist` qui comprend toutes les clés requises.

<a name="programming"></a>

## <a name="programming-health-kit"></a>Kit d’intégrité de la programmation

Le magasin de banques d’intégrité est une banque de donnés privée, propre à l’utilisateur, qui est partagée entre les applications. Étant donné que les informations d’intégrité sont sensibles, l’utilisateur doit prendre des mesures positives pour permettre l’accès aux données. Cet accès peut être partiel (écriture mais non lu, accès pour certains types de données, etc.) et peut être révoqué à tout moment. Les applications du kit de contrôle d’intégrité doivent être écrites de façon défensive, sachant que de nombreux utilisateurs hésiteront à stocker leurs informations relatives à l’intégrité.

Les données du kit d’intégrité sont limitées aux types spécifiés par Apple. Ces types sont strictement définis : certains, tels que le type de sang, sont limités aux valeurs particulières d’une énumération fournie par Apple, tandis que d’autres combinent une magnitude avec une unité de mesure (par exemple, grammes, calories et litres). Même les données qui partagent une unité de mesure compatible se distinguent par leur `HKObjectType` . par exemple, le système de type interceptera une tentative de stockage d’une `HKQuantityTypeIdentifier.NumberOfTimesFallen` valeur dans un champ qui attendait un, `HKQuantityTypeIdentifier.FlightsClimbed` même si l' `HKUnit.Count` unité de mesure est utilisée.

Les types storables dans le magasin de la Banque de contrôle d’intégrité sont toutes les sous-classes de `HKObjectType` . `HKCharacteristicType` les objets stockent le sexe biologique, le type de sang et la date de naissance. Toutefois, les `HKSampleType` objets, qui représentent des données échantillonnées à un moment donné ou sur une période donnée, sont plus fréquents. 

[![Graphique d’objets HKSampleType](healthkit-images/image08.png)](healthkit-images/image08.png#lightbox)

`HKSampleType` est abstrait et a quatre sous-classes concrètes. Il n’existe actuellement qu’un seul type de `HKCategoryType` données, qui est l’analyse de veille. La grande majorité des données dans Health kit sont de type `HKQuantityType` et stockent leurs données dans des `HKQuantitySample` objets, qui sont créés à l’aide du modèle de conception de fabrique familier :

[![La grande majorité des données dans Health kit sont de type HKQuantityType et stockent leurs données dans des objets HKQuantitySample](healthkit-images/image09.png)](healthkit-images/image09.png#lightbox)

`HKQuantityType` les types vont de `HKQuantityTypeIdentifier.ActiveEnergyBurned` à `HKQuantityTypeIdentifier.StepCount` . 

<a name="requesting-permission"></a>

### <a name="requesting-permission-from-the-user"></a>Demande d’autorisation à l’utilisateur

Les utilisateurs finaux doivent prendre des mesures positives pour permettre à une application de lire ou d’écrire des données du kit d’intégrité. Cette opération s’effectue par le biais de l’application d’intégrité qui est préinstallée sur les appareils iOS 8. La première fois qu’une application de kit de contrôle d’intégrité est exécutée, l’utilisateur reçoit une boîte de dialogue d' **accès** contrôlé par le système :

[![L’utilisateur reçoit une boîte de dialogue d’accès contrôlé à l’intégrité contrôlée par le système](healthkit-images/image10.png)](healthkit-images/image10.png#lightbox)

Plus tard, l’utilisateur peut modifier les autorisations à l’aide de la boîte de dialogue **sources** de l’application d’intégrité :

[![L’utilisateur peut modifier les autorisations à l’aide de la boîte de dialogue sources des applications d’intégrité](healthkit-images/image11.png)](healthkit-images/image11.png#lightbox)

Étant donné que les informations d’intégrité sont extrêmement sensibles, les développeurs d’applications doivent écrire leurs programmes de façon défensive, dans l’attente que les autorisations soient refusées et modifiées pendant l’exécution de l’application. L’idiome le plus courant consiste à demander des autorisations dans la `UIApplicationDelegate.OnActivated` méthode, puis à modifier l’interface utilisateur comme il convient.

### <a name="permissions-walkthrough"></a>Procédure pas à pas

Dans votre projet approvisionné par le kit d’intégrité, ouvrez le `AppDelegate.cs` fichier. Remarquez l’instruction à l’aide `HealthKit` de ; en haut du fichier.

Le code suivant est relatif aux autorisations du kit de contrôle d’intégrité :

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

Tout le code de ces méthodes peut être fait en ligne dans `OnActivated` , mais l’exemple d’application utilise des méthodes distinctes pour rendre leur intention plus claire : `ValidateAuthorization()` présente les étapes nécessaires pour demander l’accès aux types spécifiques écrits (et lire, si l’application est souhaitée) et `ReactToHealthCarePermissions()` est un rappel qui est activé une fois que l’utilisateur a interagi avec la boîte de dialogue des autorisations dans Health. app.

La tâche `ValidateAuthorization()` consiste à générer le jeu de `HKObjectTypes` que l’application écrira et demande l’autorisation de mettre à jour ces données. Dans l’exemple d’application, `HKObjectType` est pour la clé `KHQuantityTypeIdentifierKey.HeartRate` . Ce type est ajouté à l’ensemble `typesToWrite` , tandis que le jeu `typesToRead` est laissé vide. Ces ensembles, et une référence au `ReactToHealthCarePermissions()` rappel, sont passés à `HKHealthStore.RequestAuthorizationToShare()` .

Le `ReactToHealthCarePermissions()` rappel est appelé une fois que l’utilisateur a interagi avec la boîte de dialogue d’autorisations et reçoit deux informations : une `bool` valeur qui est `true` si l’utilisateur a interagi avec la boîte de dialogue des autorisations et un `NSError` qui, si non null, indique un type d’erreur associé à la présentation de la boîte de dialogue des autorisations.

> [!IMPORTANT]
> Pour en savoir plus sur les arguments de cette fonction : les paramètres de _réussite_ et d' _erreur_ n’indiquent pas si l’utilisateur a accordé l’autorisation d’accéder aux données du kit d’intégrité. Ils indiquent uniquement que l’utilisateur a eu la possibilité d’autoriser l’accès aux données.

Pour vérifier si l’application a accès aux données, `HKHealthStore.GetAuthorizationStatus()` est utilisé, en passant `HKQuantityTypeIdentifierKey.HeartRate` . En fonction de l’état retourné, l’application active ou désactive la possibilité d’entrer des données. Il n’existe aucune expérience utilisateur standard pour traiter un refus d’accès et il existe de nombreuses options possibles. Dans l’exemple d’application, l’État est défini sur un `HeartRateModel` objet singleton qui, à son tour, déclenche des événements pertinents.

## <a name="model-view-and-controller"></a>Modèle, vue et contrôleur

Pour examiner l' `HeartRateModel` objet singleton, ouvrez le `HeartRateModel.cs` fichier :

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

La première section est un code réutilisable pour créer des gestionnaires et des événements génériques. La partie initiale de la `HeartRateModel` classe est également réutilisable pour créer un objet singleton thread-safe.

Ensuite, `HeartRateModel` expose 3 événements : 

- `EnabledChanged` -Indique que le stockage de taux cardiaque a été activé ou désactivé (Notez que le stockage est initialement désactivé). 
- `ErrorMessageChanged` -Pour cet exemple d’application, nous disposons d’un modèle de gestion des erreurs très simple : une chaîne avec la dernière erreur. 
- `HeartRateStored` -Déclenché quand un taux cardiaque est stocké dans la base de données du kit d’intégrité.

Notez que lorsque ces événements sont déclenchés, il est effectué via `NSObject.InvokeOnMainThread()` , ce qui permet aux abonnés de mettre à jour l’interface utilisateur. Les événements peuvent également être documentés comme étant déclenchés sur des threads d’arrière-plan et la responsabilité d’assurer la compatibilité peut être laissée à leurs gestionnaires. Les considérations relatives aux threads sont importantes dans les applications du kit de contrôle d’intégrité, car la plupart des fonctions, telles que la demande d’autorisation, sont asynchrones et exécutent leurs rappels sur des threads non principaux.

Le code spécifique du kit de santé dans `HeartRateModel` se trouve dans les deux fonctions `HeartRateInBeatsPerMinute()` et `StoreHeartRate()` . 

`HeartRateInBeatsPerMinute()` Convertit son argument en un kit d’intégrité fortement typé `HKQuantity` . Le type de la quantité est celui spécifié par `HKQuantityTypeIdentifierKey.HeartRate` et les unités de la quantité sont `HKUnit.Count` divisées par `HKUnit.Minute` (en d’autres termes, l’unité est le *temps par minute*). 

La `StoreHeartRate()` fonction prend un `HKQuantity` (dans l’exemple d’application, un créé par `HeartRateInBeatsPerMinute()` ). Pour valider ses données, elle utilise la `HKQuantity.IsCompatible()` méthode, qui retourne `true` si les unités de l’objet peuvent être converties en unités dans l’argument. Si la quantité a été créée avec `HeartRateInBeatsPerMinute()` cette opération retourne évidemment `true` , elle retourne également `true` si la quantité a été créée sous la forme, par exemple, de *temps par heure*. Plus communément, `HKQuantity.IsCompatible()` peut être utilisé pour valider la masse, la distance et l’énergie que l’utilisateur ou un appareil est susceptible d’entrer ou d’afficher dans un système de mesure (par exemple, les unités impériales), mais qui peut être stocké dans un autre système (comme les unités métriques). 

Une fois la compatibilité de la quantité validée, la `HKQuantitySample.FromType()` méthode de fabrique est utilisée pour créer un objet fortement typé `heartRateSample` . `HKSample` les objets ont une date de début et de fin ; pour les lectures instantanées, ces valeurs doivent être identiques, comme dans l’exemple. L’exemple ne définit pas non plus de données de valeur de clé dans son `HKMetadata` argument, mais il est possible d’utiliser un code tel que le code suivant pour spécifier l’emplacement du capteur :

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

Une fois le `heartRateSample` créé, le code crée une nouvelle connexion à la base de données avec le bloc using. Dans ce bloc, la `HKHealthStore.SaveObject()` méthode tente l’écriture asynchrone dans la base de données. L’appel résultant à l’expression lambda déclenche des événements pertinents, `HeartRateStored` ou `ErrorMessageChanged` .

Maintenant que le modèle a été programmé, il est temps de voir comment le contrôleur reflète l’état du modèle. Ouvrez le fichier `HKWorkViewController.cs`. Le constructeur relie simplement le `HeartRateModel` Singleton aux méthodes de gestion des événements (là encore, cela peut être fait en ligne avec les expressions lambda, mais les méthodes distinctes rendent l’intention un peu plus évidente) :

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

Voici les gestionnaires appropriés :

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

Évidemment, dans une application avec un seul contrôleur, il serait possible d’éviter la création d’un objet de modèle distinct et l’utilisation d’événements pour le workflow de contrôle, mais l’utilisation d’objets de modèle est plus appropriée pour les applications réelles.

## <a name="running-the-sample-app"></a>Exécution de l’exemple d’application

Le simulateur iOS ne prend pas en charge le kit de contrôle d’intégrité. Le débogage doit être effectué sur un appareil physique exécutant iOS 8.

Attachez un appareil de développement iOS 8 correctement approvisionné à votre système. Sélectionnez-la en tant que cible de déploiement dans Visual Studio pour Mac puis, dans le menu, choisissez **exécuter > débogage**.

> [!IMPORTANT]
> Les erreurs relatives à l’approvisionnement seront exposées à ce stade. Pour résoudre les erreurs, consultez la section Création et configuration d’une application de kit d’intégrité ci-dessus. Les composants sont les suivants : 
>
> - **Centre de développement iOS** -ID d’application explicite & profil d’approvisionnement activé pour le kit de contrôle d’intégrité. 
> - **Options de projet** -identificateur de Bundle (ID d’application explicite) & profil de provisionnement.
> - **Code source** -habilitations. plist & info. plist

En supposant que les provisions ont été correctement définies, votre application démarre. Lorsqu’il atteint sa `OnActivated` méthode, il demande l’autorisation du kit de contrôle d’intégrité. La première fois que cette opération est rencontrée par le système d’exploitation, l’utilisateur reçoit la boîte de dialogue suivante :

[![Cette boîte de dialogue s’affiche pour l’utilisateur](healthkit-images/image12.png)](healthkit-images/image12.png#lightbox)

Activez votre application pour mettre à jour les données de fréquence cardiaque et votre application réapparaîtra. Le `ReactToHealthCarePermissions` rappel est activé de façon asynchrone. La propriété est alors `HeartRateModel’s` `Enabled` modifiée, ce qui déclenche l' `EnabledChanged` événement, ce qui entraîne l’exécution du `HKPermissionsViewController.OnEnabledChanged()` Gestionnaire d’événements, ce qui active le `StoreData` bouton. Le diagramme suivant illustre la séquence :

[![Ce diagramme illustre la séquence d’événements](healthkit-images/image13.png)](healthkit-images/image13.png#lightbox)

Appuyez sur le bouton **Enregistrer** . Cela entraîne l' `StoreData_TouchUpInside()` exécution du gestionnaire, qui tente d’analyser la valeur du `heartRate` champ de texte, de convertir en un `HKQuantity` via la fonction décrite précédemment `HeartRateModel.HeartRateInBeatsPerMinute()` et de passer cette quantité à `HeartRateModel.StoreHeartRate()` . Comme nous l’avons vu précédemment, cette opération va tenter de stocker les données et déclenchera un `HeartRateStored` `ErrorMessageChanged` événement ou.

Double-cliquez sur le bouton **démarrage** de votre appareil et ouvrez l’application d’intégrité. Cliquez sur l’onglet **sources** pour afficher l’exemple d’application. Choisissez-la et interdisez l’autorisation de mettre à jour les données de fréquence cardiaque. Double-cliquez sur le bouton d' **hébergement** et revenez à votre application. Là encore, `ReactToHealthCarePermissions()` sera appelée, mais cette fois, parce que l’accès est refusé, le bouton **StoreData** deviendra désactivé (Notez que cela se produit de façon asynchrone et que la modification de l’interface utilisateur peut être visible pour l’utilisateur final).

## <a name="advanced-topics"></a>Rubriques avancées

La lecture de données à partir de la base de données du kit d’intégrité est très similaire à l’écriture de données : l’une spécifie les types de données auxquelles une tentative d’accès est soumise, demande l’autorisation et, si cette autorisation est accordée, les données sont disponibles, avec la conversion automatique en unités de mesure compatibles.

Il existe un certain nombre de fonctions de requête plus sophistiquées qui autorisent les requêtes basées sur des prédicats et les requêtes qui effectuent des mises à jour lorsque des données pertinentes sont mises à jour. 

Les développeurs d’applications du kit de contrôle d’intégrité doivent consulter la section Health Kit des [instructions d’examen des applications](https://developer.apple.com/app-store/review/guidelines/#healthkit)Apple.

Une fois que les modèles de sécurité et de système de type sont compris, le stockage et la lecture des données dans la base de données du kit de contrôle d’intégrité partagé sont relativement simples. La plupart des fonctions du kit de contrôle d’intégrité fonctionnent de manière asynchrone et les développeurs d’applications doivent écrire leurs programmes de manière appropriée.

À partir de la rédaction de cet article, il n’existe actuellement aucun équivalent au kit de contrôle d’intégrité dans Android ou Windows Phone.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons vu comment le kit de contrôle d’intégrité permet aux applications de stocker, de récupérer et de partager des informations relatives à l’intégrité, tout en fournissant également une application d’intégrité standard qui permet à l’utilisateur d’accéder à ces données et de les contrôler. 

Nous avons également vu comment la confidentialité, la sécurité et l’intégrité des données sont prioritaires pour les informations relatives à l’intégrité et les applications utilisant le kit de santé doivent gérer l’augmentation de la complexité des aspects de la gestion des applications (approvisionnement), le codage (système de type du kit d’intégrité) et l’expérience utilisateur 

Enfin, nous avons vu une implémentation simple du kit de contrôle d’intégrité à l’aide de l’exemple d’application inclus, qui écrit les données de pulsations dans le magasin du kit de contrôle d’intégrité et présente une conception asynchrone.

## <a name="related-links"></a>Liens associés

- [HKWork (exemple)](/samples/xamarin/ios-samples/ios8-introtohealthkit)
- [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)
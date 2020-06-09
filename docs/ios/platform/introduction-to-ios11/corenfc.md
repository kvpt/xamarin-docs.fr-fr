---
title: NFC principal dans Xamarin. iOS
description: Ce document explique comment lire les balises de communication en champ proche dans Xamarin. iOS à l’aide des API introduites dans iOS 11.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 556ea205e9894a2553224da0dc71c00d9bb55a9b
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84564735"
---
# <a name="core-nfc-in-xamarinios"></a>NFC principal dans Xamarin. iOS

_Lecture des étiquettes NFC (Near Field communication) à l’aide d’iOS 11_

CoreNFC est une nouvelle infrastructure d’iOS 11 qui fournit l’accès à la radio NFC ( _Near Field communication_ ) pour lire des balises à partir d’applications. CoreNFC fonctionne sur iPhone 7, iPhone 7 plus, iPhone 8, iPhone 8 plus, iPhone X, iPhone XS et iPhone 11 (tandis que les modèles iPhone 6 et iPhone 6 plus ont des fonctionnalités de paiement NFC, ils ne prennent pas en charge CoreNFC).

Le lecteur de balise NFC des appareils iOS prend en charge tous les types de balise NFC 1 à 5 qui contiennent des informations sur le _format d’échange de données NFC_ (ndef).

Certaines restrictions sont à prendre en compte :

- CoreNFC prend uniquement en charge la lecture des balises (et non l’écriture ou la mise en forme).
- Les analyses de balises doivent être initialisées par l’utilisateur et expirer après 60 secondes.
- Les applications doivent être visibles au premier plan pour l’analyse.
- CoreNFC peut uniquement être testé sur des appareils réels (pas sur le simulateur).

Cette page décrit la configuration requise pour utiliser CoreNFC et montre comment utiliser l’API à l’aide de l' [exemple de code « NFCTagReader »](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader).

## <a name="configuration"></a>Configuration

Pour activer CoreNFC, vous devez configurer trois éléments dans votre projet :

- Clé de confidentialité **info. plist** .
- Entrée **Entitlements. plist** .
- Profil de provisionnement avec fonction de **lecture de balise NFC** .

### <a name="infoplist"></a>Info.plist

Ajoutez la clé de confidentialité et le texte **NFCReaderUsageDescription** , qui sont affichés à l’utilisateur pendant l’analyse. Utilisez un message approprié pour votre application (par exemple, Expliquez l’objectif de l’analyse) :

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

Votre application doit demander la capacité de **lecture des balises de communication en champ proche** à l’aide de la paire clé/valeur suivante dans votre fichier **Entitlements. plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>Profil de provisionnement

Créez un **ID d’application** et assurez-vous que le service de **lecture de balise NFC** est coché :

[![Portail des développeurs-page nouvel ID d’application avec la lecture de la balise NFC sélectionnée](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

Vous devez ensuite créer un profil de provisionnement pour cet ID d’application, puis le télécharger et l’installer sur votre Mac de développement.

## <a name="reading-a-tag"></a>Lecture d’une balise

Une fois votre projet configuré, ajoutez `using CoreNFC;` au début du fichier et suivez ces trois étapes pour implémenter la fonctionnalité de lecture des balises NFC :

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. implémentez`INFCNdefReaderSessionDelegate`

L’interface possède deux méthodes à implémenter :

- `DidDetect`: Appelée lorsqu’une étiquette est correctement lue.
- `DidInvalidate`: Appelée lorsqu’une erreur se produit ou que le deuxième délai d’expiration de 60 est atteint.

#### <a name="diddetect"></a>DidDetect

Dans l’exemple de code, chaque message numérisé est ajouté à une vue de table :

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

Cette méthode peut être appelée plusieurs fois (et un tableau de messages peut être transmis) si la session autorise plusieurs lectures de balises. Cela est défini à l’aide du troisième paramètre de la `Start` méthode (expliqué à l' [étape 2](#step2)).

#### <a name="didinvalidate"></a>DidInvalidate

Une invalidation peut se produire pour plusieurs raisons :

- Une erreur s’est produite lors de l’analyse.
- L’application a cessé d’être au premier plan.
- L’utilisateur a choisi d’annuler l’analyse.
- L’analyse a été annulée par l’application.

Le code ci-dessous montre comment gérer une erreur :

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

Une fois qu’une session a été invalidée, un nouvel objet de session doit être créé pour effectuer une nouvelle analyse.

<a name="step2"></a>

### <a name="2-start-an-nfcndefreadersession"></a>2. démarrer un`NFCNdefReaderSession`

L’analyse doit commencer par une demande de l’utilisateur, par exemple une pression sur un bouton.
Le code suivant crée et démarre une session d’analyse :

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

Les paramètres du `NFCNdefReaderSession` constructeur sont les suivants :

- `delegate`: Implémentation de `INFCNdefReaderSessionDelegate` . Dans l’exemple de code, le délégué est implémenté dans le contrôleur d’affichage de table. par conséquent, il `this` est utilisé comme paramètre délégué.
- `queue`: La file d’attente sur laquelle les rappels sont gérés. Il peut s’agir `null` de, auquel cas veillez à utiliser `DispatchQueue.MainQueue` lors de la mise à jour des contrôles de l’interface utilisateur (comme indiqué dans l’exemple).
- `invalidateAfterFirstRead`: Quand `true` , l’analyse s’arrête après la première analyse réussie ; lorsque l' `false` analyse se poursuit et que plusieurs résultats sont retournés jusqu’à ce que l’analyse soit annulée ou que le délai d’expiration de 60 est atteint.

### <a name="3-cancel-the-scanning-session"></a>3. annuler la session d’analyse

L’utilisateur peut annuler la session d’analyse à l’aide d’un bouton fourni par le système dans l’interface utilisateur :

![Bouton Annuler lors de l’analyse](corenfc-images/scan-cancel-sml.png)

L’application peut annuler l’analyse par programmation en appelant la `InvalidateSession` méthode :

```csharp
Session.InvalidateSession();
```

Dans les deux cas, la méthode du délégué `DidInvalidate` sera appelée.

## <a name="summary"></a>Résumé

CoreNFC permet à votre application de lire des données à partir de balises NFC. Il prend en charge la lecture d’un grand nombre de formats d’étiquette (types NDEF 1 à 5), mais ne prend pas en charge l’écriture ou la mise en forme.

## <a name="related-links"></a>Liens connexes

- [NFCTagReader (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader)
- [Présentation de Core NFC (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/718/)

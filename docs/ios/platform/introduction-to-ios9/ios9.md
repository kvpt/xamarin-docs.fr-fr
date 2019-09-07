---
title: Compatibilité iOS 9
description: Même si vous n’envisagez pas d’ajouter directement des fonctionnalités iOS 9 à votre application, vous devez recréer vos applications avec la dernière version de Xamarin.
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 246653cee7917141ddd0f911a7c4d1b21f945360
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70751974"
---
# <a name="ios-9-compatibility"></a>Compatibilité iOS 9

_Même si vous n’envisagez pas d’ajouter directement des fonctionnalités iOS 9 à votre application, vous devez recréer vos applications avec la dernière version de Xamarin._

> [!IMPORTANT]
> Les informations de cette page sont destinées aux clients ayant déjà des applications dans l’App Store ciblant iOS 8 ou une version antérieure, qui n’ont pas encore envoyé de mises à jour pour la compatibilité avec iOS 9. Si vous utilisez déjà les versions les plus récentes de Xcode 7 et Xamarin. iOS 9-pour le développement de votre application, consultez la [présentation d’iOS 9](~/ios/platform/introduction-to-ios9/index.md).

Lorsque les premières versions d’iOS 9 bêtas apparaissaient, nous avons identifié deux problèmes avec des versions antérieures de Xamarin qui se sont manifestées lorsque les anciennes applications ne peuvent pas démarrer sur iOS 9.

Ces deux problèmes (comme [détaillé sur nos forums](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) étaient les suivants :

- Les applications générées pour iOS 8 ou une version antérieure ne sont pas en mesure de démarrer sur des appareils 32 bits (y compris les applications générées avec le [API unifiée](~/cross-platform/macios/unified/index.md)).
- Échec de P/Invoke avec le chemin d’accès complet non spécifié.

La mise à jour de votre installation Xamarin vers la dernière version de canal stable, puis la reconstruction et le redéploiement de vos applications, résout ces deux problèmes.

_Même si vous n’envisagez pas de mettre à jour votre application avec les fonctionnalités iOS 9 immédiatement, nous vous recommandons de recréer la version la plus récente de Xamarin et de la soumettre à nouveau à l’App Store_.

Cela permet de s’assurer que votre application s’exécutera sur iOS 9 après la mise à niveau de vos clients.
Vous pouvez continuer à prendre en charge iOS 8-la reconstruction avec la dernière version n’affecte pas la version cible de l’application.

Si vous rencontrez d’autres problèmes lors du test de vos applications existantes sur iOS 9, consultez la section amélioration de la [compatibilité](#compat) ci-dessous.

### <a name="updating-with-visual-studio"></a>Mise à jour avec Visual Studio

Il est recommandé de vérifier explicitement que Visual Studio est mis à jour vers la dernière version stable.

## <a name="what-about-components-nugets-and-other-libraries"></a>Qu’en est-il des composants, packages NuGet et autres bibliothèques ?

Vous n’avez **pas** besoin d’attendre les nouvelles versions des composants ou packages NuGet que vous utilisez pour résoudre les deux problèmes mentionnés ci-dessus.
Ces problèmes sont résolus simplement en reconstruisant votre application avec la dernière version stable de Xamarin. iOS.

De même, les fournisseurs de composants et les créateurs NuGet ne sont **pas** tenus d’envoyer de nouvelles builds uniquement pour résoudre les deux problèmes mentionnés ci-dessus. Toutefois, si un composant ou NuGet utilise `UICollectionView` ou charge des vues à partir de fichiers **XIB** , une mise à jour *peut* être nécessaire pour résoudre les problèmes de compatibilité iOS 9 mentionnés ci-dessous.

<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>Amélioration de la compatibilité dans votre code

Il existe quelques cas de modèles de code *utilisés* pour travailler dans des versions antérieures de l’iOS dans iOS 9. Voici quelques problèmes possibles (et leurs solutions) qui peuvent survenir lors des tests sur iOS 9 :

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell. ContentView a la valeur null dans les constructeurs

**Donc** Dans iOS 9, `initWithFrame:` le constructeur est désormais requis, en raison des changements de comportement dans iOS 9 en tant qu’États de la [documentation UICollectionView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath). Si vous avez inscrit une classe pour l’identificateur spécifié et qu’une nouvelle cellule doit être créée, la cellule est maintenant initialisée en appelant `initWithFrame:` sa méthode.

**Résoudre** Ajoutez le `initWithFrame:` constructeur comme suit :

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemples connexes : [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb), [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView ne parvient pas à initialiser avec le codeur lors du chargement d’une vue à partir d’un XIB/d’un bec

**Donc** Le `initWithCoder:` constructeur est celui appelé lors du chargement d’une vue à partir d’un fichier Interface Builder XIB. Si ce constructeur n’est pas exporté, le code non managé ne peut pas appeler notre version managée de celui-ci. Précédemment (par exemple, dans iOS 8), `IntPtr` le constructeur a été appelé pour initialiser la vue.

**Résoudre** Créez et exportez le `initWithCoder:` constructeur comme suit :

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

Exemple connexe : [Mode](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

### <a name="dyld-message-no-cache-image-with-name"></a>Message dyld : aucune image du cache avec le nom...

Vous pouvez rencontrer un incident avec les informations suivantes dans le journal :

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**Donc** Il s’agit d’un bogue dans l’éditeur de liens natif d’Apple, qui se produit quand un Framework privé est public (JavaScriptCore a été rendu public dans iOS 7, avant qu’il ne s’agisse d’une infrastructure privée) et que la cible de déploiement de l’application soit pour une version iOS lorsque l’infrastructure était privée. Dans ce cas, l’éditeur de liens d’Apple crée un lien avec la version privée du Framework au lieu de la version publique.

**Résoudre** Ce problème sera résolu pour iOS 9, mais il existe une solution de contournement simple que vous pouvez vous appliquer en attendant : ciblez une version iOS ultérieure dans votre projet (vous pouvez essayer iOS 7 dans ce cas). D’autres infrastructures peuvent présenter des problèmes similaires, par exemple l’infrastructure WebKit a été rendue publique dans iOS 8 (et, par conséquent, le ciblage d’iOS 7 entraînera cette erreur ; vous devez cibler iOS 8 pour utiliser WebKit dans votre application).

## <a name="related-links"></a>Liens associés

- [informations sur la version de compatibilité iOS 9](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [Introduction à iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [Mise à jour de vos applications Xamarin. iOS vers iOS9 (vidéo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)

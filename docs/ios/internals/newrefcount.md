---
title: Nouveau système de comptage de références dans Xamarin. iOS
description: Ce document décrit le système de comptage de références amélioré de Xamarin, activé dans toutes les applications Xamarin. iOS par défaut.
ms.prod: xamarin
ms.assetid: 0221ED8C-5382-4C1C-B182-6C3F3AA47DB1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 56e35662230a3c529eb48a0ae742c2b063c1ac10
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753341"
---
# <a name="new-reference-counting-system-in-xamarinios"></a>Nouveau système de comptage de références dans Xamarin. iOS

Xamarin. iOS 9.2.1 a introduit par défaut le système de comptage de références amélioré pour toutes les applications. Il peut être utilisé pour éliminer de nombreux problèmes de mémoire qui étaient difficiles à suivre et à corriger dans les versions antérieures de Xamarin. iOS.

## <a name="enabling-the-new-reference-counting-system"></a>Activation du nouveau système de comptage de références

À partir de Xamarin 9.2.1, le nouveau système de comptage de références est activé pour **toutes les** applications par défaut.

Si vous développez une application existante, vous pouvez vérifier le fichier. csproj pour vous assurer que toutes les occurrences de `MtouchUseRefCounting` ont la `true`valeur, comme ci-dessous :

```xml
<MtouchUseRefCounting>true</MtouchUseRefCounting>
```

S’il est défini sur `false` votre application, il n’utilise pas les nouveaux outils.

### <a name="using-older-versions-of-xamarin"></a>Utilisation de versions antérieures de Xamarin

Xamarin. iOS 7.2.1 et versions ultérieures offre une version préliminaire améliorée de notre nouveau système de comptage de références.

**API classique :**

Pour activer ce nouveau système de comptage de références, activez la case à cocher **utiliser l’extension de comptage de références** située dans l’onglet **avancé** des **options de génération iOS**de votre projet, comme indiqué ci-dessous : 

[![](newrefcount-images/image1.png "Activer le nouveau système de comptage de références")](newrefcount-images/image1.png#lightbox)

Notez que ces options ont été supprimées dans les versions plus récentes de Visual Studio pour Mac.

 **[API unifiée :](~/cross-platform/macios/unified/index.md)**

 La nouvelle extension de comptage de références est requise pour le API unifiée et doit être activée par défaut. Cette valeur n’est peut-être pas activée automatiquement pour les anciennes versions de votre IDE et vous devrez peut-être en effectuer une vérification vous-même.

> [!IMPORTANT]
> Une version antérieure de cette fonctionnalité a été créée depuis MonoTouch 5,2 mais n’était disponible que pour **SGen** en version préliminaire expérimentale. Cette nouvelle version améliorée est désormais également disponible pour le garbage collector **Boehm** .

Historiquement, il y a deux types d’objets gérés par Xamarin. iOS : ceux qui étaient simplement un wrapper autour d’un objet natif (objets homologues) et ceux qui ont étendu ou incorporé de nouvelles fonctionnalités (objets dérivés), généralement en conservant un État mémoire supplémentaire. Auparavant, il était possible d’étendre un objet homologue avec l’État (par exemple, en C# ajoutant un gestionnaire d’événements), mais nous laissons l’objet non référencé, puis collecté. Cela peut entraîner un blocage ultérieur (par exemple, si le runtime objective-C a rappelé dans l’objet managé).

Le nouveau système met automatiquement à niveau les objets homologues dans des objets gérés par le runtime lorsqu’ils stockent des informations supplémentaires.

Cela résout différents incidents qui se sont produits dans des situations telles que celle-ci :

```csharp
class MyTableSource : UITableViewSource {
   public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath) {
        var cell = tableView.DequeueReusableCell ("myId");
        if (cell != null)
                return cell;

        cell = new UITableViewCell (UITableViewCellStyle.Default, "myId");
        var txt = new UITextField ();
        txt.TouchDown += delegate { Console.WriteLine ("...."); };
        cell.ContentView.AddSubview (txt);
        return cell;
   }
}
```

Sans l’extension du décompte de références, ce `cell` code se bloquerait parce qu' `TouchDown` il est collecté, et donc son délégué, qui se traduira par un pointeur non résolu.

L’extension de décompte de références garantit que l’objet managé reste actif et empêche sa collection, à condition que l’objet natif soit conservé par le code natif.

Le nouveau système supprime également la nécessité d’utiliser la *plupart* des champs de stockage privés utilisés dans les liaisons, qui est l’approche par défaut pour conserver l’instance active. L’éditeur de liens managé est suffisamment intelligent pour supprimer tous les champs *inutiles* des applications à l’aide de la nouvelle extension du décompte de références.

Cela signifie que chaque instance d’objet managé consomme moins de mémoire qu’avant. Il résout également un problème connexe dans lequel certains champs de stockage contiendront des références qui n’étaient plus nécessaires par le runtime objective-C, ce qui rendait difficile la récupération de mémoire.

---
title: Attribut débogable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487969"
---
# <a name="debuggable-attribute"></a>Attribut débogable

Pour permettre le débogage, Android prend en charge Java Debug Wire Protocol (JDWP). Cette technologie permet à des outils tels qu’ADB de communiquer avec une machine virtuelle Java. Bien que JDWP soit important pendant le développement, il doit être désactivé avant la publication de l’application.

JDWP peut être configuré `android:debuggable` par la valeur de l’attribut dans une application Android. Choisissez _l’une_ des trois façons suivantes de définir cet attribut dans Xamarin.Android:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Créez ou `AndroidManifext.xml` ouvrez le `android:debuggable` fichier, et définissez l’attribut là-bas. Prenez soin de ne pas expédier votre version avec débogage activé.

## <a name="add-an-application-class-attribute"></a>Ajouter un attribut de classe d’application

Si votre application Xamarin.Android a `[Application]` une classe avec `[Application(Debuggable = true)]`un attribut, mettre à jour l’attribut à . Réglez-le pour `false` désactiver.

## <a name="add-an-assembly-attribute"></a>Ajouter un attribut d’assemblage

Si votre application Xamarin.Android n’a pas déjà d’attribut `[Application]` de classe, ajoutez un attribut `[assembly: Application(Debuggable=true)]` de niveau d’assemblage dans un fichier c. Réglez-le pour `false` désactiver.

## <a name="summary"></a>Récapitulatif

Si `AndroidManifest.xml` et `ApplicationAttribute` sont tous deux présents, le contenu de `AndroidManifest.xml` est prioritaire devant ce qui est spécifié par `ApplicationAttribute`.

Si vous ajoutez à la fois un attribut de classe _et_ un attribut d’assemblage, il y aura une erreur de compilateur :

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

Par défaut - `AndroidManifest.xml` si `ApplicationAttribute` ni le ni le `android:debuggable` n’est présent - la valeur de l’attribut dépend de si oui ou non les symboles débogé sont générés. Si des symboles de débogé sont présents, `android:debuggable` alors `true` Xamarin.Android définira l’attribut pour vous.

> [!WARNING]
> La valeur `android:debuggable` de l’attribut ne dépend pas nécessairement de la configuration de construction. Il est possible que l’attribut `android:debuggable` des versions release soit défini sur true. Si vous utilisez un attribut pour définir cette valeur, vous pouvez choisir d’envelopper l’attribut dans une directive compilateur :
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>Liens connexes

- [Applications débogables sur le marché Android](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/)

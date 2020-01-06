---
title: Attribut débogable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487969"
---
# <a name="debuggable-attribute"></a>Attribut débogable

Pour permettre le débogage, Android prend en charge Java Debug Wire Protocol (JDWP). Cette technologie permet à des outils tels qu’ADB de communiquer avec une machine virtuelle Java. Bien que JDWP soit important pendant le développement, il doit être désactivé avant la publication de l’application.

JDWP peut être configurée par la valeur de l’attribut `android:debuggable` dans une application Android. Choisissez l' _une_ des trois méthodes suivantes pour définir cet attribut dans Xamarin. Android :

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Créez ou ouvrez `AndroidManifext.xml` fichier et définissez l’attribut `android:debuggable` ici. Veillez à ne pas livrer votre version Release avec le débogage activé.

## <a name="add-an-application-class-attribute"></a>Ajouter un attribut de classe d’application

Si votre application Xamarin. Android a une classe avec un attribut `[Application]`, mettez à jour l’attribut sur `[Application(Debuggable = true)]`. Affectez-lui la valeur `false` pour désactiver.

## <a name="add-an-assembly-attribute"></a>Ajouter un attribut d’assembly

Si votre application Xamarin. Android n’a pas encore d’attribut de classe `[Application]`, ajoutez un attribut de niveau assembly `[assembly: Application(Debuggable=true)]` dans un fichier c#. Affectez-lui la valeur `false` pour désactiver.

## <a name="summary"></a>Récapitulatif

Si `AndroidManifest.xml` et `ApplicationAttribute` sont tous deux présents, le contenu de `AndroidManifest.xml` est prioritaire devant ce qui est spécifié par `ApplicationAttribute`.

Si vous ajoutez un attribut de classe _et_ un attribut d’assembly, il y aura une erreur du compilateur :

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

Par défaut : Si ni le `AndroidManifest.xml` ni le `ApplicationAttribute` n’est présent, la valeur de l’attribut `android:debuggable` varie selon que les symboles de débogage sont générés ou non. Si des symboles de débogage sont présents, Xamarin. Android définit l’attribut `android:debuggable` sur `true` pour vous.

> [!WARNING]
> La valeur de l’attribut `android:debuggable` ne dépend pas nécessairement de la configuration de Build. Il est possible que l’attribut `android:debuggable` des versions release soit défini sur true. Si vous utilisez un attribut pour définir cette valeur, vous pouvez choisir d’encapsuler l’attribut dans une directive de compilateur :
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>Liens associés

- [Applications débogables sur le marché Android](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/)

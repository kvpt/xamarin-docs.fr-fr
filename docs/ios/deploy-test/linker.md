---
title: Liaison d’applications Xamarin.iOS
description: Ce document décrit l’éditeur de liens Xamarin.iOS, qui sert à supprimer le code inutilisé d’une application Xamarin.iOS afin de réduire sa taille.
ms.prod: xamarin
ms.assetid: 3A4B2178-F264-0E93-16D1-8C63C940B2F9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/24/2017
ms.openlocfilehash: 284705973f9c0ec606572170f7e927ed4745ddd1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73030227"
---
# <a name="linking-xamarinios-apps"></a>Liaison d’applications Xamarin.iOS

Lorsque vous générez votre application, Visual Studio pour Mac ou Visual Studio appelle un outil nommé **mtouch** qui inclut un éditeur de liens pour le code managé. Il est utilisé pour supprimer des bibliothèques de classes les fonctionnalités que l’application n’utilise pas. L’objectif est de réduire la taille de l’application, livrée avec uniquement les bits nécessaires.

L’éditeur de liens utilise l’analyse statique pour déterminer les différents chemins d’accès au code que votre application est susceptible de suivre. Celle-ci est un peu lourde car elle doit passer par tous les détails de chaque assembly, pour vous assurer que rien de détectable n’est supprimé. Elle n’est pas désactivée par défaut sur les builds du simulateur pour accélérer la durée du build pendant le débogage. Toutefois, puisqu’elle génère des applications de plus petite taille pouvant accélérer la compilation AOT et le téléchargement sur l’appareil, tous les *builds (version) d’appareils* utilisent l’éditeur de liens par défaut.

Étant donné que l’éditeur de liens est un outil statique, il ne peut pas marquer les types et les méthodes d’inclusion appelés par la réflexion ou instanciés de manière dynamique. Il existe plusieurs options pour contourner cette limitation.

<a name="Linker_Behavior" />

## <a name="linker-behavior"></a>Comportement de l'éditeur de liens

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Le processus de liaison peut être personnalisé via le menu déroulant du comportement de l’éditeur de liens dans **Options du projet**. Pour accéder à ce processus, double-cliquez sur le projet iOS et accédez à **Build iOS > Options de l’éditeur de liens**, comme illustré ci-dessous :

[![](linker-images/image1.png "Linker Options")](linker-images/image1.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Le processus de liaison peut être personnalisé via le menu déroulant du comportement de l’éditeur de liens dans **Propriétés du projet** dans Visual Studio.

Effectuez les actions suivantes :

1. Cliquez à droite sur le nom du **projet** dans **l’explorer de solution** et sélectionnez **propriétés**:

    ![](linker-images/linking01w.png "Right-click on the Project Name in the Solution Explorer and select Properties")
2. Dans les **Propriétés du projet**, sélectionnez **Build IOS** :

    ![](linker-images/linking02w.png "Select IOS Build")
3. Suivez les instructions ci-dessous pour modifier les options de liaison.

-----

Les trois options principales proposées sont décrites ci-dessous :

### <a name="dont-link"></a>Ne pas lier

La désactivation de la liaison permet de garantir qu’aucun assembly n’est modifié. Pour des raisons de performances, il s’agit du paramètre par défaut lorsque votre IDE cible le simulateur iOS. Pour les builds d’appareils cela doit uniquement être utilisé comme solution de contournement chaque fois que l’éditeur de liens contient un bogue qui empêche l’exécution de votre application. Si votre application ne fonctionne qu’avec *-nolink*, veuillez soumettre un [rapport de bogue](https://github.com/xamarin/xamarin-macios/issues/new).

Cela correspond à l’option *-nolink* durant l’utilisation de l’outil en ligne de commande mtouch.

<a name="Link_SDK_assemblies_only" />

### <a name="link-sdk-assemblies-only"></a>Lier les assemblys du Kit de développement logiciel uniquement

Dans ce mode, l’éditeur de liens conservera vos assemblys intactes et réduira la taille des assemblys du Kit de développement logiciel (par exemple, ce qui est livré avec Xamarin.iOS) en supprimant tout ce qui votre application n’utilise pas. Il s’agit du paramètre par défaut lorsque votre IDE cible les appareils iOS.

Il s’agit de l’option la plus simple, car elle ne nécessite pas de modification de votre code. La différence avec la liaison de tout est que l’éditeur de liens ne peut pas effectuer certaines optimisations dans ce mode, il s’agit donc d’un compromis entre le travail nécessaire pour lier tous les éléments et la taille de l’application finale.

Cela correspond à l’option *-linksdk* lors de l’utilisation de l’outil de ligne de commande mtouch.

<a name="Link_all_assemblies" />

### <a name="link-all-assemblies"></a>Lier tous les assemblys

Lors de la liaison de tous les éléments, l’éditeur de liens peut utiliser l’ensemble de ses optimisations pour rendre l’application aussi petite que possible. Il modifie le code utilisateur, ce qui peut s’arrêter dès que le code utilise des fonctionnalités d’une manière non détectable par l’analyse statique de l’éditeur de liens. Dans ce cas, par exemple, webservices, réflexion ou sérialisation, certains ajustements peuvent être requis dans votre application pour lier tous les éléments.

Cela correspond à l’option *-linkall* lors de l’utilisation de l’outil de ligne de commande **mtouch**.

<a name="Controlling_the_Linker" />

## <a name="controlling-the-linker"></a>Contrôle de l’éditeur de liens

Lorsque vous utilisez l’éditeur de liens il peut parfois supprimer le code appelé dynamiquement, même indirectement. Pour couvrir ces cas, l’éditeur de liens fournit quelques fonctionnalités et options pour vous permettre un meilleur contrôle sur ses actions.

<a name="Preserving_Code" />

### <a name="preserving-code"></a>Conservation du code

Lorsque vous utilisez l’éditeur de liens, celui-ci peut parfois supprimer le code appelé dynamiquement à l’aide de System.Reflection.MemberInfo.Invoke ou par l’exportation de vos méthodes vers Objective-C à l’aide de l’attribut `[Export]` et lors de l’appel du sélecteur manuellement.

Dans ces cas, vous pouvez demander à l’éditeur de liens de prendre en compte les classes entières à utiliser ou les membres individuels à conservés en appliquant l’attribut `[Xamarin.iOS.Foundation.Preserve]` au niveau de la classe ou au niveau du membre. Chaque membre qui n’est pas lié de manière statique par l’application est soumis à suppression. Cet attribut est donc utilisé pour marquer les membres qui ne sont pas statiquement référencés, mais qui sont toujours requis par votre application.

Par exemple, si vous instanciez dynamiquement des types, vous souhaiterez éventuellement conserver le constructeur par défaut de vos types. Si vous utilisez la sérialisation XML, vous souhaiterez éventuellement conserver les propriétés de vos types.

Vous pouvez appliquer cet attribut à chaque membre d’un type ou au type lui-même. Si vous souhaitez conserver l’ensemble du type, vous pouvez utiliser la syntaxe `[Preserve
(AllMembers = true)]` sur le type.

Parfois, vous souhaitez conserver certains membres, mais uniquement si le type conteneur a été conservé. Dans ces cas, utilisez `[Preserve (Conditional=true)]`

Si vous ne souhaitez pas prendre une dépendance sur les bibliothèques Xamarin, par exemple, disons que vous générez une bibliothèque de classes portables multiplateforme (PCL), vous pouvez encore utiliser cet attribut.

Pour ce faire, vous devez simplement déclarer une classe PreserveAttribute et l’utiliser dans votre code, comme suit :

```csharp
public sealed class PreserveAttribute : System.Attribute {
    public bool AllMembers;
    public bool Conditional;
}
```

Le nom d’espace défini n’a pas beaucoup d’importance, l’éditeur de liens recherche cet attribut par nom de type.

 <a name="Skipping_Assemblies" />

### <a name="skipping-assemblies"></a>Ignorer des assemblys

Il est possible de spécifier des assemblys exclus du processus de l’éditeur de liens, tout en permettant aux autres assemblys d’être liés normalement. Cela est utile si l’utilisation de `[Preserve]` sur certains assemblys est impossible (par exemple, code tiers) ou en tant que solution de contournement temporaire d’un bogue.

Cela correspond à l’option `--linkskip` lors de l’utilisation de l’outil de ligne de commande mtouch.

Lorsque vous utilisez l’option **Lier tous les assemblys**, si vous souhaitez indiquer à l’éditeur de liens d’ignorer des assemblys entiers, placez les éléments suivants dans l’option **Arguments mtouch supplémentaires** de votre assembly de niveau supérieur :

```csharp
--linkskip=NameOfAssemblyToSkipWithoutFileExtension
```

Si vous souhaitez que l’éditeur de liens ignore plusieurs assemblys, incluez plusieurs arguments `linkskip` :

```csharp
--linkskip=NameOfFirstAssembly --linkskip=NameOfSecondAssembly
```

Aucune interface utilisateur n’utilise cette option, mais elle peut être fournie dans la boîte de dialogue Options de projet Visual Studio pour Mac ou le volet Propriétés de projet Visual Studio, dans le champ de texte **Arguments mtouch supplémentaires**. (Par exemple, *--linkskip=mscorlib* ne lie pas mscorlib.dll, mais lie d’autres assemblys dans la solution).

<a name="Disabling_Link_Away" />

### <a name="disabling-link-away"></a>Désactivation de « Écarter »

L’éditeur de liens supprimera le code qui ne sera très probablement pas utilisé sur les appareils, par exemple, non pris en charge ou non autorisé. Dans de rares cas, il est possible qu’une application ou une bibliothèque dépende de ce code (de travail ou pas). Depuis Xamarin.iOS 5.0.1, l’éditeur de liens peut être configuré pour ignorer cette optimisation.

Cela correspond à l’option *-nolinkaway* lors de l’utilisation de l’outil de ligne de commande mtouch.

Aucune interface utilisateur n’utilise cette option, mais elle peut être fournie dans la boîte de dialogue Options de projet Visual Studio pour Mac ou le volet Propriétés de projet Visual Studio, dans le champ de texte **Arguments mtouch supplémentaires**. (Par exemple, *--nolinkaway* ne supprime pas le code supplémentaire (environ 100 Ko).

### <a name="marking-your-assembly-as-linker-ready"></a>Marquage de votre assembly comme prêt pour l’éditeur de liens

Les utilisateurs peuvent sélectionner d’uniquement lier les assemblys du Kit de développement logiciel et de ne procéder à aucune liaison vers leur code.  Cela signifie également que toutes les bibliothèques tierces qui ne font pas partie du Kit de développement logiciel Xamarin central ne seront pas liées.

Cela se produit en général car ils ne souhaitent pas ajouter manuellement des attributs `[Preserve]` à leur code.  L’effet secondaire est que les bibliothèques tierces ne seront pas liées, il s’agit là en général d’une bonne valeur par défaut, car il n’est pas possible de savoir si une bibliothèque tierce est conviviale ou pas à l’éditeur de liens.

Si vous avez une bibliothèque dans votre projet, ou si vous êtes un développeur de bibliothèques réutilisables et que vous [`LinkerSafe`](xref:Foundation.LinkerSafeAttribute)voulez que le linker traite votre assemblage comme liant, tout ce que vous avez à faire est d’ajouter l’attribut de niveau d’assemblage, comme ceci:

```csharp
[assembly:LinkerSafe]
```

Votre bibliothèque n’a pas réellement besoin de référencer les bibliothèques Xamarin pour cela.  Par exemple, si vous générez une bibliothèque de classes portables qui s’exécutera dans d’autres plateformes vous pouvez toujours utiliser un attribut `LinkerSafe`.
L’éditeur de liens Xamarin recherche l’attribut `LinkerSafe` par nom et non par son type réel.  Cela signifie que vous pouvez écrire ce code, il fonctionne également :

```csharp
[assembly:LinkerSafe]
// ... assembly attribute should be at top, before source
class LinkerSafeAttribute : System.Attribute {}
```

## <a name="custom-linker-configuration"></a>Configuration personnalisée de l’éditeur de liens

Suivez les [instructions pour la création d’un fichier de configuration de l’éditeur de liens](~/cross-platform/deploy-test/linker.md).

## <a name="related-links"></a>Liens connexes

- [Configuration De Linker personnalisée](~/cross-platform/deploy-test/linker.md)
- [Liaison sur Mac](~/mac/deploy-test/linker.md)
- [Liaison sur Android](~/android/deploy-test/linker.md)

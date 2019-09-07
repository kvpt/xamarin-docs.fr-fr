---
title: Utilisation de liaisons Xamarin. Mac pour les applications console
description: Créez une application console sans périphérique à l’aide de Xamarin. Mac pour accéder aux API macOS natives.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: a38543d575f655a3b1b70ff94eece7fef1bf2d40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769897"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Liaisons Xamarin. Mac dans les applications console

Dans certains scénarios, vous pouvez utiliser certaines API natives Apple C# dans pour générer une application &ndash; headless sans interface &ndash; utilisateur qui utilise. C#

Les modèles de projet pour les applications Mac incluent un `NSApplication.Init()` appel à suivi d’un `NSApplication.Main(args)`appel à, ce qui se présente généralement comme suit :

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

L’appel à `Init` prépare le runtime Xamarin. Mac, l’appel à `Main(args)` démarre la boucle principale de l’application cacao, qui prépare l’application à recevoir des événements de clavier et de souris et à afficher la fenêtre principale de votre application.   L’appel à `Main` tente également de localiser les ressources de cacao, de préparer une fenêtre de niveau et s’attend à ce que le programme fasse partie d’un lot d’applications (programmes distribués `.app` dans un répertoire avec l’extension et une disposition très spécifique).

Les applications headless n’ont pas besoin d’une interface utilisateur et n’ont pas besoin de s’exécuter dans le cadre d’un bundle d’applications.

## <a name="creating-the-console-app"></a>Création de l’application console

Il est donc préférable de commencer par un type de projet de console .NET normal.

Vous devez effectuer quelques opérations :

- Créez un projet vide.
- Référencez la bibliothèque Xamarin. Mac. dll.
- Ajoutez la dépendance non managée à votre projet.

Ces étapes sont expliquées plus en détail ci-dessous :

### <a name="create-an-empty-console-project"></a>Créer un projet de console vide

Créez un projet de console .NET, assurez-vous qu’il s’agit de .NET et non de .NET Core, car Xamarin. Mac. dll ne s’exécute pas sous le Runtime .NET Core, il s’exécute uniquement avec le runtime mono.

### <a name="reference-the-xamarinmac-library"></a>Référencer la bibliothèque Xamarin. Mac

Pour compiler votre code, vous pouvez référencer l' `Xamarin.Mac.dll` assembly à partir de ce répertoire :`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

Pour ce faire, accédez aux références du projet, sélectionnez l’onglet **assembly .net** , puis cliquez sur le bouton **Parcourir** pour localiser le fichier sur le système de fichiers.  Accédez au chemin d’accès ci-dessus, puis sélectionnez **Xamarin. Mac. dll** dans ce répertoire.

Vous obtiendrez ainsi l’accès aux API de cacao au moment de la compilation.   À ce stade, vous pouvez ajouter `using AppKit` au début de votre fichier et appeler la `NSApplication.Init()` méthode.   Il n’y a qu’une seule étape supplémentaire avant de pouvoir exécuter votre application.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Placer la bibliothèque de prise en charge non managée dans votre projet

Avant que votre application ne s’exécute, vous devez placer `Xamarin.Mac` la bibliothèque de prise en charge dans votre projet.   Pour ce faire, ajoutez un nouveau fichier à votre projet (dans options du projet, sélectionnez **Ajouter**, puis **Ajoutez le fichier existant**) et accédez à ce répertoire :

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

Ici, sélectionnez le fichier **libxammac. dylib**.   Vous êtes invité à copier, lier ou déplacer.   Je préfère que la liaison, mais la copie fonctionne également.    Vous devez ensuite sélectionner le fichier, puis, dans le panneau des propriétés (sélectionnez **afficher > propriétés** de remplissage > si le panneau des propriétés n’est pas visible), accédez à la section **générer** et définissez le paramètre **copier dans le répertoire de sortie** sur **copier si plus récent**.

Vous pouvez maintenant exécuter votre application Xamarin. Mac.

Le résultat de votre répertoire bin ressemble à ce qui suit :

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Pour exécuter cette application, vous aurez besoin de tous ces fichiers dans le même répertoire.

## <a name="building-a-standalone-application-for-distribution"></a>Création d’une application autonome pour la distribution

Vous souhaiterez peut-être distribuer un seul exécutable à vos utilisateurs.  Pour ce faire, vous pouvez utiliser l' `mkbundle` outil pour convertir les différents fichiers en un fichier exécutable autonome.

Tout d’abord, assurez-vous que votre application est compilée et exécutée.   Une fois que vous êtes satisfait des résultats, vous pouvez exécuter la commande suivante à partir de la ligne de commande :

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

Dans l’appel de ligne de commande ci-dessus `-o` , l’option est utilisée pour spécifier la sortie générée, dans le cas `/tmp/consoleapp`présent, nous avons réussi.   Il s’agit désormais d’une application autonome que vous pouvez distribuer et qui n’a pas de dépendances externes sur mono ou Xamarin. Mac, il s’agit d’un exécutable entièrement autonome.

La ligne de commande a spécifié manuellement le fichier **machine. config** à utiliser, ainsi qu’un fichier de configuration de mappage de bibliothèque à l’ensemble du système.   Ils ne sont pas nécessaires pour toutes les applications, mais il est pratique de les regrouper, car ils sont utilisés lorsque vous utilisez plus de fonctionnalités de .NET

## <a name="project-less-builds"></a>Builds sans projet

Vous n’avez pas besoin d’un projet complet pour créer une application Xamarin. Mac autonome. vous pouvez également utiliser des makefiles UNIX simples pour faire le travail.   L’exemple suivant montre comment vous pouvez configurer un makefile pour une application en ligne de commande simple :

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)

bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

Les éléments `Makefile` ci-dessus fournissent trois cibles :

- `make`génère le programme
- `make run`générera et exécutera le programme dans le répertoire actif
- `make bundle`va créer un exécutable autonome

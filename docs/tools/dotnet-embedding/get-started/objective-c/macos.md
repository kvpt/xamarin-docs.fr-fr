---
title: Prise en main de macOS
description: Ce document décrit la prise en main de l’intégration de .NET avec macOS. Il traite des exigences et présente un exemple d’application pour montrer comment lier l’assembly managé et utiliser la sortie générée dans un projet XCode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: d081795ecfcc5d0aa2559b866fcc57076fe8248b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007022"
---
# <a name="getting-started-with-macos"></a>Prise en main de macOS

## <a name="what-you-will-need"></a>Ce dont vous aurez besoin

* Suivez les instructions du Guide de prise en main [d’Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) .

## <a name="hello-world"></a>Hello World

Tout d’abord, générez un exemple Hello C#World simple dans.

### <a name="create-c-sample"></a>Créer C# un exemple

Ouvrez Visual Studio pour Mac, créez un projet de bibliothèque de classes Mac nommé **Hello-from-CSharp**, puis enregistrez-le dans **~/projects/hello-from-CSharp**.

Remplacez le code du fichier **MyClass.cs** par l’extrait de code suivant :

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

Générez le projet. L’assembly résultant sera enregistré sous la forme **~/projects/hello-from-CSharp/hello-from-CSharp/bin/debug/hello-from-CSharp.dll**.

### <a name="bind-the-managed-assembly"></a>Lier l’assembly managé

Une fois que vous disposez d’un assembly managé, liez-le en appelant l’incorporation .NET.

Comme décrit dans le Guide d' [installation](~/tools/dotnet-embedding/get-started/install/install.md) , cette opération peut être effectuée comme une étape après génération dans votre projet, avec une cible MSBuild personnalisée, ou manuellement :

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

Le Framework sera placé dans **~/projects/hello-from-CSharp/output/hello-from-CSharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Utiliser la sortie générée dans un projet XCode

Ouvrez Xcode et créez une application de cacao. Nommez-le **Hello-from-CSharp** et sélectionnez le langage **objective-C** .

Ouvrez le répertoire **~/projects/hello-from-CSharp/output** dans Finder, sélectionnez **Hello-from-CSharp. Framework**, faites-le glisser vers le projet XCode et déposez-le juste au-dessus du dossier **Hello-from-CSharp** dans le projet.

![Framework de glisser-déplacer](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Assurez-vous que l’option **copier les éléments si nécessaire** est cochée dans la boîte de dialogue qui s’affiche, puis cliquez sur **Terminer**.

![Copier les éléments si nécessaire](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Sélectionnez le projet **Hello-from-CSharp** , puis accédez à l’onglet **général** de la cible **Hello-from-CSharp** . Dans la section **binaire incorporé** , ajoutez **Hello-from-CSharp. Framework**.

![Fichiers binaires incorporés](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Ouvrez **ViewController. m**et remplacez le contenu par :

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

Enfin, exécutez le projet XCode et ce qui suit s’affiche :

![Bonjour de C# l’exemple s’exécutant dans le simulateur](macos-images/hello-from-csharp-mac.png)

Un exemple plus complet et plus attrayant [est disponible ici](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).

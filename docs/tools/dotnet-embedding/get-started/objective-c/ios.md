---
title: Prise en main d’iOS
description: Ce document explique comment commencer à utiliser l’incorporation .NET avec iOS. Il traite des exigences et présente un exemple d’application pour illustrer comment lier un assembly managé et utiliser la sortie dans un projet XCode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5697d20077b746d9d33db985111c2d04908d5d01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029765"
---
# <a name="getting-started-with-ios"></a>Prise en main d’iOS

## <a name="requirements"></a>spécifications

Outre les exigences de notre guide de prise en main d' [objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) , vous aurez également besoin des éléments suivants :

* [Xamarin. iOS 10,11](https://visualstudio.microsoft.com/xamarin/) ou version ultérieure

## <a name="hello-world"></a>Hello World

Tout d’abord, générez un exemple Hello C#World simple dans.

### <a name="create-c-sample"></a>Créer C# un exemple

Ouvrez Visual Studio pour Mac, créez un projet de bibliothèque de classes iOS, nommez-le **Hello-from-CSharp**, puis enregistrez-le dans **~/projects/hello-from-CSharp**.

Remplacez le code du fichier **MyClass.cs** par l’extrait de code suivant :

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

Générez le projet, et l’assembly résultant sera enregistré sous la forme **~/projects/hello-from-CSharp/hello-from-CSharp/bin/debug/hello-from-CSharp.dll**.

### <a name="bind-the-managed-assembly"></a>Lier l’assembly managé

Une fois que vous disposez d’un assembly managé, liez-le en appelant l’incorporation .NET.

Comme décrit dans le Guide d' [installation](~/tools/dotnet-embedding/get-started/install/install.md) , cette opération peut être effectuée comme une étape après génération dans votre projet, avec une cible MSBuild personnalisée, ou manuellement :

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Le Framework sera placé dans **~/projects/hello-from-CSharp/output/hello-from-CSharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Utiliser la sortie générée dans un projet XCode

Ouvrez Xcode, créez une application de vue unique iOS, nommez-la **Hello-from-CSharp**, puis sélectionnez le langage **objective-C** .

Ouvrez le répertoire **~/projects/hello-from-CSharp/output** dans Finder, sélectionnez **Hello-from-CSharp. Framework**, faites-le glisser vers le projet XCode et déposez-le juste au-dessus du dossier **Hello-from-CSharp** dans le projet.

![Framework de glisser-déplacer](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Assurez-vous que l’option **copier les éléments si nécessaire** est cochée dans la boîte de dialogue qui s’affiche, puis cliquez sur **Terminer**.

![Copier les éléments si nécessaire](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Sélectionnez le projet **Hello-from-CSharp** , puis accédez à l' **onglet général**de la cible **Hello-from-CSharp** . Dans la section **binaire incorporé** , ajoutez **Hello-from-CSharp. Framework**.

![Fichiers binaires incorporés](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Ouvrez **ViewController. m**et remplacez le contenu par :

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

L’incorporation .NET ne prend pas actuellement en charge bitcode sur iOS, qui est activé pour certains modèles de projet XCode. 

Désactivez-la dans les paramètres de votre projet :

![Option Bitcode](../../images/ios-bitcode-option.png)

Enfin, exécutez le projet XCode et ce qui suit s’affiche :

![Bonjour de C# l’exemple s’exécutant dans le simulateur](ios-images/hello-from-csharp-ios.png)

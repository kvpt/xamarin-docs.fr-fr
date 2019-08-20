---
title: Programmation de UrhoSharp avecF#
description: Ce document explique comment créer une application Hello World UrhoSharp simple à l' F# aide de dans Visual Studio pour Mac.
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: d87749bd74cf2c478e96284060fed7386d10b853
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69621006"
---
# <a name="programming-urhosharp-with-f"></a>Programmation de UrhoSharp avec F\#

UrhoSharp peut être programmé avec F# les mêmes bibliothèques et les mêmes concepts que C# ceux utilisés par les programmeurs. L’article [utilisation de UrhoSharp](~/graphics-games/urhosharp/using.md) donne une vue d’ensemble du moteur UrhoSharp et doit être lu avant cet article.

Comme de nombreuses bibliothèques provenant du C++ monde, de nombreuses fonctions UrhoSharp retournent des valeurs booléennes ou des entiers indiquant la réussite ou l’échec. Vous devez utiliser `|> ignore` pour ignorer ces valeurs.

L' [exemple de programme](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) est un «Hello World» pour UrhoSharp F#à partir de.

## <a name="creating-an-empty-project"></a>Création d’un projet vide

Aucun F# modèle n’est encore disponible pour UrhoSharp. par conséquent, pour créer votre propre projet UrhoSharp, vous pouvez commencer par l' [exemple](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) ou suivre les étapes suivantes:

1. À partir de Visual Studio pour Mac, créez une nouvelle **solution**. Choisissez **iOS > application > application avec affichage unique** et **F#** sélectionnez comme langue d’implémentation. 
1. Supprimez le fichier **main. Storyboard** . Ouvrez le fichier **info. plist** et dans le volet d' **informations de déploiement iPhone/iPod** , supprimez la `Main` chaîne dans la liste déroulante **interface principale** .
1. Supprimez également le fichier **ViewController. FS** .

## <a name="building-hello-world-in-urho"></a>Génération de Hello World dans Urho

Vous êtes maintenant prêt à commencer à définir les classes de votre jeu. Au minimum, vous devrez définir une sous-classe de `Urho.Application` et substituer sa `Start` méthode. Pour créer ce fichier, cliquez avec le bouton droit F# sur votre projet, choisissez **Ajouter un nouveau fichier...** et F# ajoutez une classe vide à votre projet. Le nouveau fichier est ajouté à la fin de la liste des fichiers de votre projet, mais vous devez le faire glisser pour qu’il apparaisse *avant* d’être utilisé dans **AppDelegate. FS**.

1. Ajoutez une référence au package NuGet Urho.
1. À partir d’un projet Urho existant, copiez les répertoires (volumineux) **CoreData/** et **Data/** dans le répertoire Resources **/** de votre projet. Dans votre F# projet, cliquez avec le bouton droit sur le dossier **ressources** et utilisez **Ajouter/Ajouter un dossier existant** pour ajouter tous ces fichiers à votre projet.

La structure de votre projet doit maintenant ressembler à ce qui suit:

![](fsharp-images/solutionpane.png "La structure du projet doit maintenant ressembler à")

Définissez votre classe nouvellement créée en tant que sous-type `Urho.Application` de et remplacez sa `Start` méthode:

```fsharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

Le code est très simple. Elle utilise la `Urho.Gui.Text` classe pour afficher une chaîne alignée au centre avec une certaine taille de police et de couleur. 

Avant de pouvoir exécuter ce code, UrhoSharp doit être initialisé. 

Ouvrez le fichier AppDelegate. FS et modifiez la `FinishedLaunching` méthode comme suit:

```fsharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

Le `ApplicationOptions.Default` fournit les options par défaut pour une application en mode paysage. Transmettez-les `Application` `HelloWorld` `inherit Application(o)` au constructeur par défaut de votre sous-classe (Notez que lorsque vous avez défini la classe, la ligne appelle le constructeur de classe de base). `ApplicationOptions`

La `Run` méthode de votre `Application` lance le programme. Elle est définie en tant que `int`retournant un, qui peut `ignore`être redirigé vers.

Le programme obtenu doit ressembler à la capture d’écran suivante:

![Capture d’écran du programme obtenu](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>Liens associés

- [Parcourir sur GitHub (exemple)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)

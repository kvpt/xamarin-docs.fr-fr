---
title: Tests unitaires d’applications Xamarin.iOS
description: Ce document fournit une vue d’ensemble des tests unitaires d’une application Xamarin.iOS. Il décrit comment créer un projet de tests unitaires et comment écrire et exécuter des tests.
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6757f25cb54adc464f7e41a1cd59db8cf6c7d22a
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511710"
---
# <a name="unit-testing-xamarinios-apps"></a>Tests unitaires d’applications Xamarin.iOS

Ce document décrit comment créer des tests unitaires pour vos projets Xamarin.iOS.
Les tests unitaires avec Xamarin.iOS s’effectuent à l’aide du framework Touch.Unit. Ce framework contient un outil Test Runner iOS, ainsi qu’une version modifiée de NUnit, appelée [Touch.Unit](https://github.com/xamarin/Touch.Unit), qui fournit un ensemble d’API couramment utilisées pour l’écriture de tests unitaires.

## <a name="setting-up-a-test-project-in-visual-studio-for-mac"></a>Configuration d’un projet de test dans Visual Studio pour Mac

Pour configurer un framework pour votre projet de tests unitaires, ajoutez simplement à votre solution un projet de type **Projet de tests unitaires iOS**. Cliquez avec le bouton droit sur votre solution, puis sélectionnez **Ajouter > Ajouter un nouveau projet**. Dans la liste, sélectionnez **iOS > Tests > API unifiée > Projet de tests unitaires iOS** (vous pouvez choisir C# ou F#).

![](touch.unit-images/00.png "Choisir C# ou F#")

Les étapes précédentes créent un projet de base qui contient un programme Runner simple et qui fait référence au nouvel assembly MonoTouch.NUnitLite. Votre projet doit ressembler à ceci :

![](touch.unit-images/01.png "Projet dans l’Explorateur de solutions")

La classe `AppDelegate.cs` contient Test Runner, comme illustré ci-dessous :

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TouchRunner runner;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow (UIScreen.MainScreen.Bounds);
        runner = new TouchRunner (window);

        // register every tests included in the main application/assembly
        runner.Add (System.Reflection.Assembly.GetExecutingAssembly ());

        window.RootViewController = new UINavigationController (runner.GetViewController ());

        // make the window visible
        window.MakeKeyAndVisible ();

        return true;
    }
}
```

> [!NOTE]
> Le type de projet de tests unitaires iOS n’est pas disponible dans Visual Studio 2019 ou Visual Studio 2017 sur Windows.

## <a name="writing-some-tests"></a>Écriture de tests

Votre environnement de base est en place. Vous pouvez donc commencer à écrire votre première série de tests.

Pour écrire des tests, vous créez des classes auxquelles vous appliquez l’attribut `[TestFixture]`. Dans chaque classe TestFixture, vous devez appliquer l’attribut `[Test]` à chaque méthode devant être appelée par Test Runner. Les fixtures de test peuvent se trouver dans n’importe quel fichier dans votre projet de tests.

Pour démarrer rapidement, sélectionnez **Ajouter/Ajouter un nouveau fichier** et sélectionnez **UnitTests** dans le groupe Xamarin.iOS. Cela ajoute un fichier squelette qui contient un test réussi, un test échoué et un test ignoré, comme illustré ci-dessous :

```csharp
using System;
using NUnit.Framework;

namespace Fixtures {

    [TestFixture]
    public class Tests {

        [Test]
        public void Pass ()
        {
                Assert.True (true);
        }

        [Test]
        public void Fail ()
        {
                Assert.False (true);
        }

        [Test]
        [Ignore ("another time")]
        public void Ignore ()
        {
                Assert.True (false);
        }
    }
}
```

## <a name="running-your-tests"></a>Exécution de vos tests

Pour exécuter ce projet dans votre solution, cliquez dessus avec le bouton droit et sélectionnez **Déboguer l’élément** ou **Exécuter l’élément**.

Test Runner affiche les tests qui sont enregistrés et vous permet de sélectionner individuellement les tests à exécuter.

[![](touch.unit-images/02-sml.png "Liste des tests inscrits")](touch.unit-images/02.png#lightbox) 
[![](touch.unit-images/03-sml.png "Texte individuel")](touch.unit-images/03.png#lightbox) 

[![](touch.unit-images/04-sml.png "Résultats de l’exécution")](touch.unit-images/04.png#lightbox)

Vous pouvez exécuter les fixtures de test de votre choix en les sélectionnant dans les vues imbriquées, ou exécuter tous les tests en sélectionnant le bouton « Exécuter tout ». Si vous exécutez le test par défaut, celui-ci est censé inclure un test réussi, un test échoué et un test ignoré. Voici à quoi ressemble le rapport. Vous pouvez accéder directement aux détails des tests ayant échoué pour déterminer la cause de l’échec :

[![](touch.unit-images/05-sml.png "Exemple de rapport")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/06-sml.png "Exemple de rapport")](touch.unit-images/06.png#lightbox) [![](touch.unit-images/07-sml.png "Exemple de rapport")](touch.unit-images/07.png#lightbox)

Vous pouvez également examiner les tests en cours d’exécution et leur état actuel dans la fenêtre Sortie de l’application dans votre environnement IDE.

## <a name="writing-new-tests"></a>Écriture de nouveaux tests

NUnitLite est une version modifiée de NUnit, appelée projet [Touch.Unit](https://github.com/xamarin/Touch.Unit). Il s’agit d’un framework de test léger pour .NET, basé sur les concepts [NUnit](http://nunit.com/) et offrant une partie de ses fonctionnalités.
Il utilise peu de ressources et s’exécute sur les plateformes limitées en ressources, telles que celles utilisées dans les environnements de développement incorporé et mobile. L’API NUnitLite est disponible dans Xamarin.iOS. Avec la structure de base fournie par le modèle de test unitaire, les méthodes de la [classe Assert](xref:NUnit.Framework.Assert) constituent votre point d’entrée principal.

En plus des méthodes de la classe Assert, la fonctionnalité de test unitaire est partagée par les espaces de noms suivants qui font partie de NUnitLite :

- [NUnit.Framework](xref:NUnit.Framework)
- [NUnit.Constraints](xref:NUnit.Framework.Constraints)
- [NUniteLite.Runner](xref:NUnitLite.Runner)

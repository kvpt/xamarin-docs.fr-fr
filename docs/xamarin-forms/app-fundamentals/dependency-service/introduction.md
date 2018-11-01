---
title: Introduction à DependencyService
description: Cet article explique le fonctionne de la classe Xamarin.Forms DependencyService pour accéder aux fonctionnalités de plateforme native.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675014"
---
# <a name="introduction-to-dependencyservice"></a>Introduction à DependencyService

## <a name="overview"></a>Vue d'ensemble

[`DependencyService`](xref:Xamarin.Forms.DependencyService) permet aux applications d’appeler dans les fonctionnalités spécifiques à la plateforme à partir de code partagé. Cette fonctionnalité permet aux applications Xamarin.Forms faire tout ce qu’une application native peut faire.

`DependencyService` est un localisateur de service. Dans la pratique, une interface est définie et `DependencyService` détecte que l’implémentation correcte de cette interface à partir de différents projets de plateforme.

> [!NOTE]
> Par défaut, le [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) résout uniquement les implémentations de plateforme qui ont des constructeurs sans paramètre. Toutefois, une méthode de résolution de dépendance peut être injectée dans Xamarin.Forms qui utilise un conteneur d’injection de dépendance ou des méthodes de fabrique pour résoudre les implémentations de plateforme. Cette approche peut être utilisée pour résoudre les implémentations de plateforme qui ont des constructeurs avec des paramètres. Pour plus d’informations, consultez [résolution des dépendances dans Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Fonctionne de DependencyService

Les applications de Xamarin.Forms doivent utiliser quatre composants `DependencyService`:

- **Interface** &ndash; la fonctionnalité requise est définie par une interface dans le code partagé.
- **Mise en œuvre par la plateforme** &ndash; les Classes qui implémentent l’interface doivent être ajoutés à chaque projet de plateforme.
- **L’inscription** &ndash; chaque classe d’implémentation doit être inscrite avec `DependencyService` via un attribut de métadonnées. L’inscription permet `DependencyService` pour rechercher la classe d’implémentation et de fournir à la place de l’interface au moment de l’exécution.
- **L’appel à DependencyService** &ndash; partagé code doit appeler explicitement `DependencyService` à poser pour les implémentations de l’interface.

Notez que les implémentations doivent être fournies pour chaque projet de plateforme dans votre solution. Projets de plateforme sans les implémentations échoue lors de l’exécution.

La structure de l’application est expliquée par le diagramme suivant :

![](introduction-images/overview-diagram.png "Structure de l’Application DependencyService")

### <a name="interface"></a>Interface

L’interface que vous concevez définira l’interaction avec des fonctionnalités spécifiques à la plateforme. Soyez prudent si vous développez un composant à être partagé en tant que composant ou package NuGet. Conception d’API peut ou l’échec d’un package. L’exemple ci-dessous spécifie une interface simple pour parler de texte qui permet une grande flexibilité en spécifiant les mots à énoncer mais laisse l’implémentation pour être personnalisées pour chaque plateforme :

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implémentation par plateforme

Une fois qu’une interface appropriée a été conçue, cette interface doit être implémentée dans le projet pour chaque plateforme que vous ciblez. Par exemple, la classe suivante implémente la `ITextToSpeech` interface sur iOS :

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>Inscription

Chaque implémentation de l’interface doit être inscrite avec `DependencyService` avec un attribut de métadonnées. Le code suivant inscrit l’implémentation pour iOS :

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

Vue d’ensemble, l’implémentation spécifique à la plateforme ressemble à ceci :

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

Remarque : l’inscription effectuée au niveau de l’espace de noms, pas au niveau de la classe.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilation Native de .NET de plateforme Windows universelle

Les projets UWP qui utilisent l’option de compilation .NET Native doivent suivre un [configuration légèrement différentes](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) lors de l’initialisation de Xamarin.Forms. Compilation .NET native nécessite également l’inscription légèrement différente pour les services de dépendance.

Dans le **App.xaml.cs** de fichier, enregistrer manuellement chaque service de dépendance définie dans le projet UWP avec le `Register<T>` (méthode), comme indiqué ci-dessous :

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Remarque : à l’aide de l’inscription manuelle `Register<T>` est uniquement efficace dans version builds à l’aide de compilation .NET Native. Si vous omettez cette ligne, les versions Debug continuera de fonctionner, mais les versions Release ne sera pas chargé le service de dépendance.

### <a name="call-to-dependencyservice"></a>L’appel à DependencyService

Une fois que le projet a été configuré avec une interface commune et des implémentations pour chaque plateforme, utilisez `DependencyService` pour obtenir de l’implémentation appropriée lors de l’exécution :

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` trouve l’implémentation correcte de l’interface `T`.

### <a name="solution-structure"></a>Structure de la solution

Le [exemple de solution de UsingDependencyService](https://developer.xamarin.com/samples/UsingDependencyService/) est indiqué ci-dessous pour iOS et Android, avec les modifications de code présentées ci-dessus mise en surbrillance.

 [![iOS et Android solution](introduction-images/solution-sml.png "Structure de la Solution exemple DependencyService")](introduction-images/solution.png#lightbox "DependencyService exemple de Structure de Solution")

> [!NOTE]
> Vous **doit** fournir une implémentation dans chaque projet de plateforme. Si aucune implémentation de l’Interface n’est inscrit, puis le `DependencyService` sera impossible de résoudre la `Get<T>()` méthode lors de l’exécution.

## <a name="related-links"></a>Liens associés

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Exemples Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)

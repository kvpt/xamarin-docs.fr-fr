---
title: Prise en main avec le kit de développement logiciel (SDK) Xamarin Workbooks
description: Ce document décrit comment prendre en main le kit de développement logiciel (SDK) Xamarin Workbooks, qui peut être utilisé pour développer des intégrations pour Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: e4a9e9113f83dd89b622de3e1f74f458efd4f07f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018683"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Prise en main avec le kit de développement logiciel (SDK) Xamarin Workbooks

Ce document fournit un guide rapide sur la prise en main du développement d’intégrations pour Xamarin Workbooks. La plupart d’entre elles fonctionnent avec l’Xamarin Workbooks stable, mais le **chargement des intégrations via des packages NuGet est pris en charge uniquement dans les classeurs 1,3**, dans le canal alpha au moment de l’écriture.

## <a name="general-overview"></a>Vue d’ensemble générale

Xamarin Workbooks intégrations sont de petites bibliothèques qui utilisent le kit de développement logiciel (SDK) [NuGet`Xamarin.Workbooks.Integrations`][nuget] pour s’intégrer avec les agents Xamarin Workbooks et Inspector afin de fournir des expériences améliorées.

Il existe 3 étapes majeures pour commencer à développer une intégration : nous allons les décrire ici.

## <a name="creating-the-integration-project"></a>Création du projet d’intégration

Les bibliothèques d’intégration sont mieux développées en tant que bibliothèques multiplateformes. Étant donné que vous souhaitez fournir la meilleure intégration sur tous les agents disponibles, passées et futures, vous souhaiterez choisir un ensemble de bibliothèques largement pris en charge. Nous vous recommandons d’utiliser le modèle « Bibliothèque portable » pour la prise en charge la plus large :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![modèle de bibliothèque portable Visual Studio pour Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![modèle de bibliothèque portable Visual Studio](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

Dans Visual Studio, vous souhaiterez vous assurer que vous sélectionnez les plateformes cibles suivantes pour votre bibliothèque portable :

[Plateformes de bibliothèque portable ![Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

Une fois que vous avez créé le projet de bibliothèque, ajoutez une référence à notre `Xamarin.Workbooks.Integration` bibliothèque NuGet via le gestionnaire de package NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![NuGet Visual Studio pour Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Vous pouvez supprimer la classe vide qui est créée pour vous dans le cadre du projet : vous n’en aurez pas besoin pour cela. Une fois que vous avez effectué ces étapes, vous êtes prêt à commencer à créer votre intégration.

## <a name="building-an-integration"></a>Génération d’une intégration

Nous allons créer une intégration simple. Nous apprécions vraiment la couleur verte. nous allons donc ajouter la couleur verte sous forme de représentation à chaque objet. Tout d’abord, créez une classe nommée `SampleIntegration`et implémentez notre interface `IAgentIntegration` :

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Ce que nous voulons faire, c’est ajouter une [représentation](~/tools/workbooks/sdk/representations.md) pour chaque objet qui est une couleur verte. Nous allons le faire à l’aide d’un fournisseur de représentations. Les fournisseurs héritent de la classe `RepresentationProvider`, pour la nôtre, il suffit de remplacer `ProvideRepresentations`:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

Nous revenons à un `Color`, un type de représentation prédéfinie dans notre kit de développement logiciel (SDK).
Vous remarquerez que le type de retour est un `IEnumerable<object>`&mdash;un fournisseur de représentation peut retourner de nombreuses représentations pour un objet. Tous les fournisseurs de représentations sont appelés pour chaque objet. il est donc important de ne pas faire de suppositions quant aux objets qui sont passés à vous.

La dernière étape consiste à inscrire notre fournisseur auprès de l’agent et à indiquer aux classeurs où trouver notre type d’intégration. Pour inscrire le fournisseur, ajoutez ce code à la méthode `IntegrateWith` de la classe `SampleIntegration` que nous avons créée précédemment :

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

La définition du type d’intégration s’effectue via un attribut au niveau de l’assembly. Vous pouvez le placer dans votre AssemblyInfo.cs ou dans la même classe que votre type d’intégration pour plus de commodité :

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Pendant le développement, il peut s’avérer plus pratique d’utiliser `AddProvider` surcharges sur `RepresentationManager` qui vous permettent d’inscrire un rappel simple pour fournir des représentations à l’intérieur d’un classeur, puis de déplacer ce code dans votre implémentation de `RepresentationProvider` une fois que vous avez terminé. Voici un exemple de rendu d’une `PlotModel` de [`OxyPlot`][oxyplot] :

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Ces API vous permettent d’être rapidement opérationnel, mais nous ne recommandons pas l’expédition d’une intégration entière uniquement à l’aide de ces API&mdash;elles offrent un contrôle très limité sur la façon dont vos types sont traités par le client.

Une fois la représentation inscrite, votre intégration est prête à être expédiée.

## <a name="shipping-your-integration"></a>Expédition de votre intégration

Pour expédier votre intégration, vous devez l’ajouter à un package NuGet.
Vous pouvez l’expédier avec le NuGet de votre bibliothèque existante, ou si vous créez un nouveau package, vous pouvez utiliser ce fichier Template. NuSpec comme point de départ.
Vous devez remplir les sections correspondant à votre intégration. La partie la plus importante est que tous les fichiers de votre intégration doivent se trouver dans un répertoire `xamarin.interactive` à la racine du package. Cela nous permet de trouver facilement tous les fichiers appropriés pour votre intégration, que vous utilisiez ou non un package existant ou que vous en créez un nouveau.

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

Une fois que vous avez créé le fichier. NuSpec, vous pouvez compresser votre NuGet comme suit :

```csharp
nuget pack MyIntegration.nuspec
```

puis publiez-le dans [NuGet][nugetorg]. Une fois cette opération effectuée, vous pouvez la référencer à partir de n’importe quel classeur et la voir en action. Dans la capture d’écran ci-dessous, nous avons empaqueté l’exemple d’intégration que nous avons créé dans ce document et installé le package NuGet dans un classeur :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![le classeur avec l’intégration](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![le classeur avec l’intégration](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Notez que vous ne voyez aucune directive `#r` ni aucune directive pour initialiser l’intégration. les classeurs ont été pris en charge pour vous en arrière-plan !

## <a name="next-steps"></a>Étapes suivantes

Consultez notre autre documentation pour plus d’informations sur les éléments mobiles qui composent le kit de développement logiciel (SDK), ainsi que sur nos [exemples d’intégration](~/tools/workbooks/samples/index.md) pour d’autres opérations que vous pouvez effectuer à partir de votre intégration, telles que la fourniture de code JavaScript personnalisé qui est exécuté dans le classeur client.

[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[oxyplot]: http://www.oxyplot.org/

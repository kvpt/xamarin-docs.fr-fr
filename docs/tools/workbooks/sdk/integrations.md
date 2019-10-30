---
title: Rubriques liées à l’intégration avancée
description: Ce document décrit les rubriques avancées relatives aux intégrations de Xamarin Workbooks. Il traite du package NuGet Xamarin. Workbook. integrations et de l’exposition de l’API dans un classeur Xamarin.
ms.prod: xamarin
ms.assetid: 002CE0B1-96CC-4AD7-97B7-43B233EF57A6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 159fecbe1385c091c56a6ececb61bf7d020dfc1b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029602"
---
# <a name="advanced-integration-topics"></a>Rubriques liées à l’intégration avancée

Les assemblys d’intégration doivent référencer le [`Xamarin.Workbooks.Integrations` NuGet][nuget]. Consultez notre [documentation de démarrage rapide](~/tools/workbooks/sdk/index.md) pour plus d’informations sur la prise en main du package NuGet.

Les intégrations de client sont également prises en charge et sont lancées en plaçant des fichiers JavaScript ou CSS portant le même nom que l’assembly d’intégration d’agent dans le même répertoire. Par exemple, si l’assembly d’intégration d’agent (qui fait référence à NuGet) est nommé `SampleExternalIntegration.dll`, `SampleExternalIntegration.js` et `SampleExternalIntegration.css` seront également intégrés au client s’ils existent. Les intégrations de client sont facultatives.

L’intégration externe elle-même peut être empaquetée en tant que NuGet, fournie et référencée directement à l’intérieur de l’application qui héberge l’agent, ou simplement placée à côté d’un fichier `.workbook` qui souhaite le consommer.

Les intégrations externes (agent et client) dans les packages NuGet seront automatiquement chargées lorsque le package sera référencé, conformément à la documentation de démarrage rapide, tandis que les assemblys d’intégration livrés avec le classeur devront le référencer comme suit :

```csharp
#r "SampleExternalIntegration.dll"
```

Lors du référencement d’une intégration de cette manière, il n’est pas chargé par le client immédiatement&mdash;vous devez appeler du code à partir de l’intégration pour qu’il se charge. Nous traiterons ce bogue à l’avenir.

Le `Xamarin.Interactive` PCL fournit quelques API d’intégration importantes. Chaque intégration doit au moins fournir un point d’entrée d’intégration :

```csharp
using Xamarin.Interactive;

[assembly: AgentIntegration (typeof (AgentIntegration))]

class AgentIntegration : IAgentIntegration
{
    const string TAG = nameof (AgentIntegration);

    public void IntegrateWith (IAgent agent)
    {
        // hook into IAgent APIs
    }
}
```

À ce stade, une fois que l’assembly d’intégration est référencé, le client chargera implicitement les fichiers d’intégration JavaScript et CSS.

## <a name="apis"></a>API

Comme pour tout assembly référencé par un classeur ou une session d’inspection en direct, toutes ses API publiques sont accessibles à la session. Par conséquent, il est important de disposer d’une surface d’API sûre et raisonnable que les utilisateurs peuvent explorer.

L’assembly d’intégration est effectivement un pont entre une application ou un kit de développement logiciel (SDK) intéressant et la session. Il peut fournir de nouvelles API qui ont un sens spécifique dans le contexte d’un classeur ou d’une session d’inspection en direct, ou ne fournir aucune API publique et exécuter simplement des tâches « en coulisses », telles que le traitement des [représentations](~/tools/workbooks/sdk/representations.md)d’objets.

> [!NOTE]
> Les API qui doivent être publiques mais ne doivent pas être signalées via IntelliSense peuvent être marquées avec l’attribut `[EditorBrowsable (EditorBrowsableState.Never)]` habituel.

[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration

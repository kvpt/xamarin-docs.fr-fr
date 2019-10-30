---
title: Représentations dans Xamarin Workbooks
description: Ce document décrit le pipeline de représentation Xamarin Workbooks, qui permet le rendu de résultats enrichis pour tout code qui retourne une valeur.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: c1afba6943faa03ee07a3ec70624f668748041cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018581"
---
# <a name="representations-in-xamarin-workbooks"></a>Représentations dans Xamarin Workbooks

## <a name="representations"></a>Present

Dans une session de classeur ou d’inspecteur, le code exécuté et génère un résultat (par exemple, une méthode qui retourne une valeur ou le résultat d’une expression) est traité via le pipeline de représentation de l’agent. Tous les objets, à l’exception des primitives telles que les entiers, sont reflétés pour produire des graphes membres interactifs et passent par un processus pour fournir des représentations alternatives que le client peut restituer de manière plus riche. Les objets de toute taille et profondeur sont pris en charge en toute sécurité (y compris les cycles et les énumérables infinis) en raison de la réflexion différée et interactive et de la communication à distance.

Xamarin Workbooks fournit quelques types communs à tous les agents et clients qui permettent un rendu complet des résultats. `Color` est un exemple de ce type, où, par exemple, sur iOS, l’agent est responsable de la conversion des objets `CGColor` ou `UIColor` en un objet `Xamarin.Interactive.Representations.Color`.

Outre les représentations communes, le kit de développement logiciel (SDK) d’intégration fournit des API pour sérialiser des représentations personnalisées dans l’agent et des représentations de rendu dans le client.

## <a name="external-representations"></a>Représentations externes

`Xamarin.Interactive.IAgent.RepresentationManager` offre la possibilité d’enregistrer un `RepresentationProvider`, qu’une intégration doit implémenter pour convertir un objet arbitraire en formulaire agnostique à restituer. Ces formulaires agnostiques doivent implémenter l’interface `ISerializableObject`.

L’implémentation de l’interface `ISerializableObject` ajoute une méthode Serialize qui contrôle précisément la manière dont les objets sont sérialisés. La méthode `Serialize` s’attend à ce qu’un développeur spécifie exactement les propriétés qui doivent être sérialisées, ainsi que le nom final. En examinant l’objet `Person` dans notre [exemple`KitchenSink`] [exemple], nous pouvons voir comment cela fonctionne :

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Si nous voulions fournir un sur-ensemble ou sous-ensemble de propriétés à partir de l’objet d’origine, nous pouvons le faire avec `Serialize`. Par exemple, nous pouvons effectuer une opération semblable à celle-ci pour fournir une propriété de `Age` précalculée sur `Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> Les API qui produisent directement des objets `ISerializableObject` n’ont pas besoin d’être gérées par un `RepresentationProvider`. Si l’objet que vous souhaitez afficher n’est **pas** un `ISerializableObject`, vous pouvez le gérer dans votre `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Rendu d’une représentation

Les convertisseurs sont implémentés en JavaScript et auront accès à une version JavaScript de l’objet représenté via `ISerializableObject`. La copie JavaScript aura également une `$type` propriété de chaîne qui indique le nom de type .NET.

Nous vous recommandons d’utiliser la machine à écrire pour le code d’intégration du client, qui, bien sûr, est compilé en JavaScript vanille. Dans les deux cas, le kit de développement logiciel (SDK) fournit des [types][typings] qui peuvent être référencés directement par écriture manuscrite ou simplement référencés manuellement si vous préférez écrire du code JavaScript vanille.

Le principal point d’intégration pour le rendu est `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

Ici, `PersonRenderer` implémente l’interface `Renderer`. Pour plus d’informations, consultez la rubrique [saisie][typings] .

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts

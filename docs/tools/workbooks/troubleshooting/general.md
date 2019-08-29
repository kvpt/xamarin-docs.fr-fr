---
title: Problèmes connus & solutions de contournement
description: Ce document décrit les problèmes connus et les solutions de contournement pour Xamarin Workbooks. Il aborde les problèmes CultureInfo, les problèmes JSON et bien plus encore.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 3701a2a111c5c18f694def241b1e888fa6f4ce19
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120315"
---
# <a name="known-issues--workarounds"></a>Problèmes connus & solutions de contournement

## <a name="persistence-of-cultureinfo-across-cells"></a>Persistance de CultureInfo sur les cellules

Le `System.Threading.CurrentThread.CurrentCulture` paramètre `System.Globalization.CultureInfo.CurrentCulture` ou n’est pas conservé entre les cellules de classeur sur des classeurs mono, cibles (Mac, iOS et Android) en raison d’un bogue dans l’implémentation [de `AppContext.SetSwitch` mono][appcontext-bug] .

### <a name="workarounds"></a>Solutions

- Définissez l’application-domaine local `DefaultThreadCurrentCulture`:

```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

- Ou, mettez à jour vers les classeurs 1.2.1 ou une version plus récente, `System.Threading.CurrentThread.CurrentCulture` qui `System.Globalization.CultureInfo.CurrentCulture` réécrira les assignations dans et pour fournir le comportement souhaité (contournement du bogue mono).

## <a name="unable-to-use-newtonsoftjson"></a>Impossible d’utiliser Newtonsoft. JSON

### <a name="workaround"></a>Solution de contournement

- Mise à jour des classeurs 1.2.1, qui installe Newtonsoft. JSON 9.0.1.
  Les classeurs 1,3, actuellement dans le canal alpha, prennent en charge les versions 10 et ultérieures.

### <a name="details"></a>Détails

Newtonsoft. JSON 10 a été publié, ce qui a vu sa dépendance vis-à-vis de Microsoft. CSharp, qui `dynamic`est en conflit avec les classeurs de version fournis pour la prise en charge. Cela est traité dans la version préliminaire du classeur 1,3, mais pour l’instant, nous avons contourné Newtonsoft. JSON spécifiquement pour la version 9.0.1.

Les packages NuGet explicitement en fonction de Newtonsoft. JSON 10 ou plus récents sont uniquement pris en charge dans les classeurs 1,3, actuellement dans le canal alpha.

## <a name="code-tooltips-are-blank"></a>Les info-bulles de code sont vides

Il existe un [bogue dans l’éditeur Monaco][monaco-bug] dans Safari/WebKit, qui est utilisé dans l’application classeurs Mac, qui génère le rendu des info-bulles de code sans texte.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Solution de contournement

- Le fait de cliquer sur l’info-bulle après l’affichage force le rendu du texte.

- Ou mettre à jour des classeurs 1.2.1 ou version ultérieure

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>Les convertisseurs SkiaSharp sont absents dans les classeurs 1,3

À compter des classeurs 1,3, nous avons supprimé les convertisseurs SkiaSharp que nous avons fournis dans les classeurs 0.99.0, en faveur de SkiaSharp fournissant les convertisseurs eux-mêmes, à l’aide de notre [Kit de développement logiciel (SDK)](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Solution de contournement

- Mettez à jour SkiaSharp vers la version la plus récente dans NuGet. Au moment de la rédaction de cet article, il s’agit de 1.57.1.

## <a name="related-links"></a>Liens associés

- [Signalement des bogues](~/tools/workbooks/install.md#reporting-bugs)

---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f20a0586aee998c10372c60c96577321e697aad
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137265"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Restituer des contrôles personnalisés dans le générateur d’aperçu XAML

_Parfois, les contrôles personnalisés ne fonctionnent pas comme prévu dans le générateur d’aperçu XAML. Utilisez les instructions de cet article pour comprendre les limitations de l’aperçu de vos contrôles personnalisés._

## <a name="basic-preview-mode"></a>Mode aperçu de base

Même si vous n’avez pas généré votre projet, le générateur d’aperçu XAML affiche vos pages. Jusqu’à ce que vous génériez, tout contrôle qui s’appuie sur code-behind affichera son type de base Xamarin.Forms . Quand votre projet est généré, le générateur d’aperçu XAML essaie d’afficher des contrôles personnalisés avec le rendu au moment du design activé. Si le rendu échoue, le type de base est affiché Xamarin.Forms .

## <a name="enable-design-time-rendering-for-custom-controls"></a>Activer le rendu au moment du design pour les contrôles personnalisés

Si vous créez vos propres contrôles personnalisés ou utilisez des contrôles à partir d’une bibliothèque tierce, le générateur d’aperçu peut les afficher de manière incorrecte. Les contrôles personnalisés doivent accepter le rendu au moment du design pour apparaître dans le générateur d’aperçu, que vous ayez écrit le contrôle ou importé à partir d’une bibliothèque. Avec les contrôles que vous avez créés, ajoutez [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) à la classe de votre contrôle pour l’afficher dans le générateur d’aperçu :

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Utilisez [la classe de base ImageCirclePlugin’s de James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) comme exemple.

## <a name="skiasharp-controls"></a>Contrôles SkiaSharp

Actuellement, les contrôles SkiaSharp sont uniquement pris en charge lorsque vous affichez un aperçu sur iOS. Ils ne s’afficheront pas dans la version préliminaire d’Android.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="check-your-xamarinforms-version"></a>Vérifier votre Xamarin.Forms version
Vérifiez que vous disposez d’au moins Xamarin.Forms 3,6. Vous pouvez mettre à jour votre Xamarin.Forms version de NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Même avec `[DesignTimeVisible(true)]` , mon contrôle personnalisé ne s’affichera pas correctement.
Les contrôles personnalisés qui s’appuient fortement sur des données code-behind ou backend ne fonctionnent pas toujours dans le générateur d’aperçu XAML. Vous pouvez essayer de procéder comme suit :

* Déplacer le contrôle afin qu’il ne s’initialise pas si le [mode création est activé](index.md#detect-design-mode)
* Configuration de [données au moment](design-time-data.md) de la conception pour afficher des données factices à partir du serveur principal

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>Le générateur d’aperçu XAML affiche l’erreur « les contrôles personnalisés ne sont pas correctement restitués »
Essayez de nettoyer et de reconstruire votre projet, ou fermez et rouvrez le fichier XAML.

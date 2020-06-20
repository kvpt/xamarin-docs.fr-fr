---
title: Générateur d’aperçu XAML pourXamarin.Forms
description: Cet article explique comment utiliser le générateur d’aperçu XAML pour afficher les Xamarin.Forms dispositions rendues à mesure que vous tapez. Le générateur d’aperçu XAML est disponible dans Visual Studio 2019 et Visual Studio 2019 pour Mac.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6916d5fde688c5b1162f12db0d36bc3ca27156d8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137317"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Générateur d’aperçu XAML pourXamarin.Forms

_Afficher les Xamarin.Forms dispositions rendues à mesure que vous tapez_

## <a name="overview"></a>Vue d’ensemble

Le générateur d’aperçu XAML vous montre comment votre Xamarin.Forms page XAML s’affichera sur iOS et Android. Lorsque vous apportez des modifications à votre code XAML, vous les voyez immédiatement en regard de votre code. Le générateur d’aperçu XAML est disponible dans Visual Studio et Visual Studio pour Mac.

## <a name="getting-started"></a>Prise en main

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

Vous pouvez ouvrir le générateur d’aperçu XAML en cliquant sur les flèches du volet mode fractionné. Si vous souhaitez modifier le comportement par défaut du mode fractionné, utilisez les **outils > Options > boîte de dialogue Xamarin > générateur d' Xamarin.Forms Aperçu XAML** . Dans cette boîte de dialogue, vous pouvez sélectionner la vue de document par défaut et l’orientation de fractionnement.

[![Xamarin.FormsOptions du générateur d’aperçu dans Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "[! Opérationnel. NO-LOC (Xamarin. Forms)] options du générateur d’aperçu dans Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Lorsque vous ouvrez un fichier XAML, l’éditeur ouvre le fichier de taille complète ou en regard du générateur d’aperçu, en fonction des paramètres sélectionnés dans la boîte de dialogue **outils > Options > boîte de dialogue Xamarin > Xamarin.Forms Générateur d’aperçu XAML** . Toutefois, le fractionnement peut être modifié pour chaque fichier dans la fenêtre de l’éditeur.

#### <a name="xaml-preview-controls"></a>Contrôles d’aperçu XAML

Choisissez si vous souhaitez voir votre code, le générateur d’aperçu XAML, ou les deux, en sélectionnant ces boutons dans le volet mode fractionné. Le bouton du milieu permute le côté générateur d’aperçu et votre code :

[![Xamarin.FormsContrôles d’aperçu pour basculer entre la conception, la source et le mode fractionné dans Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "[! Opérationnel. NO-LOC (Xamarin. Forms)] contrôles d’aperçu pour basculer entre le mode Design, source et le mode fractionné dans Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

Vous pouvez modifier l’écran pour le fractionner verticalement ou horizontalement, ou pour réduire entièrement un volet :

[![Xamarin.FormsContrôles d’orientation du volet de l’aperçu dans Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "[! Opérationnel. NO-LOC (Xamarin. Forms)] contrôles d’orientation du volet d’aperçu dans Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

#### <a name="enable-or-disable-the-xaml-previewer"></a>Activer ou désactiver le générateur d’aperçu XAML

Vous pouvez désactiver le générateur d’aperçu XAML dans la boîte de dialogue **outils > Options > boîte de dialogue Xamarin > Xamarin.Forms Générateur d’aperçu XAML** en sélectionnant **éditeur XML par** défaut comme **éditeur XAML par défaut**. Cela désactive également la structure du document, le panneau des propriétés et la boîte à outils XAML. Pour réactiver le générateur d’aperçu XAML et les outils, modifiez votre **éditeur XAML par défaut** en ** Xamarin.Forms Générateur d’aperçu**.

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

Le bouton **Aperçu** est affiché dans l’éditeur lorsque vous ouvrez une page XAML. Affichez ou masquez le générateur d’aperçu en appuyant sur les boutons d' **Aperçu** ou de **fractionnement** dans le coin inférieur gauche de toute fenêtre de document XAML :

[![Xamarin.FormsGénérateur d’aperçu activé avec le bouton d’aperçu ou de fractionnement](xaml-previewer-images/xamlp-list-sml.png)](xaml-previewer-images/xamlp-list.png#lightbox)

> [!NOTE]
> Dans les versions antérieures de Visual Studio pour Mac, le bouton **Aperçu** était situé en haut à droite de la fenêtre.

#### <a name="enable-or-disable-the-xaml-previewer"></a>Activer ou désactiver le générateur d’aperçu XAML

Vous pouvez désactiver le générateur d’aperçu XAML dans les **Préférences du > Visual Studio > éditeur de texte >** boîte de dialogue XAML en sélectionnant **éditeur XML par défaut** comme **éditeur XAML par défaut**. Cela désactive également la structure du document, le panneau des propriétés et la boîte à outils XAML. Pour réactiver le générateur d’aperçu XAML et les outils, modifiez votre **éditeur XAML par défaut** en ** Xamarin.Forms Générateur d’aperçu**.

::: zone-end

## <a name="xaml-previewer-options"></a>Options du générateur d’aperçu XAML

Les options situées en haut du volet de visualisation sont les suivantes :

* **Android** : afficher la version Android de l’écran
* **iOS** : afficher la version IOS de l’écran (*Remarque : Si vous utilisez Visual Studio sur Windows, vous devez être [associé à un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) pour utiliser ce mode*)
* Liste déroulante **périphérique** des appareils Android ou iOS, y compris la résolution et la taille d’écran
* **Portrait (icône)** : utilise l’orientation portrait pour la préversion
* **Paysage (icône)** : utilise l’orientation paysage pour la préversion

## <a name="detect-design-mode"></a>Détecter le mode création

La [`DesignMode.IsDesignModeEnabled`](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) propriété statique vous indique si l’application est en cours d’exécution dans le générateur d’aperçu. À l’aide de ce dernier, vous pouvez spécifier le code qui s’exécutera uniquement lorsque l’application est ou n’est pas exécutée dans le générateur d’aperçu :

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

Cette propriété est utile si vous initialisez une bibliothèque dans votre constructeur de page dont l’exécution échoue au moment du Design.

## <a name="troubleshooting"></a>Dépannage

Si le générateur d’aperçu ne fonctionne pas, vérifiez les problèmes ci-dessous et les [Forums Xamarin](https://forums.xamarin.com/categories/xamarin-forms).

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Le générateur d’aperçu XAML n’affiche pas ou affiche une erreur

* Le démarrage du générateur d’aperçu peut prendre un certain temps. vous verrez « initialisation du rendu » jusqu’à ce qu’il soit prêt.
* Essayez de fermer et de rouvrir le fichier XAML.
* Vérifiez que votre `App` classe possède un constructeur sans paramètre.
* Vérifiez votre Xamarin.Forms version. elle doit être au moins égale à Xamarin.Forms 3,6. Vous pouvez mettre à jour vers la version la plus récente Xamarin.Forms via NuGet.
* Vérifiez votre installation de JDK-l’aperçu d’Android nécessite au moins [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Essayez d’encapsuler toutes les classes initialisées dans le code-behind C# de la page dans `if (!DesignMode.IsDesignModeEnabled)` .

### <a name="custom-controls-arent-rendering"></a>Les contrôles personnalisés ne sont pas rendus

Essayez de générer votre projet. Le générateur d’aperçu affiche la classe de base du contrôle s’il ne parvient pas à restituer le contrôle, ou si le créateur du contrôle a opté pour le rendu au moment du Design. Pour plus d’informations, consultez [restituer des contrôles personnalisés dans le générateur d’aperçu XAML](render-custom-controls.md).

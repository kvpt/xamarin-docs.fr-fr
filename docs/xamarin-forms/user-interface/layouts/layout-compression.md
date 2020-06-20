---
title: Compression des dispositions
description: La compression de disposition supprime les dispositions spécifiées de l’arborescence d’éléments visuels afin d’améliorer les performances de rendu des pages. Cet article explique comment activer la compression de disposition et les avantages qu’elle peut apporter.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 40af5aeaa51025dae70113faa6f7ff83edf43c73
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138023"
---
# <a name="layout-compression"></a>Compression des dispositions

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_La compression de disposition supprime les dispositions spécifiées de l’arborescence d’éléments visuels afin d’améliorer les performances de rendu des pages. Cet article explique comment activer la compression de disposition et les avantages qu’elle peut apporter._

## <a name="overview"></a>Vue d’ensemble

Xamarin.Formseffectue la disposition à l’aide de deux séries d’appels de méthode récursives :

- La disposition commence en haut de l’arborescence d’éléments visuels par une page et se poursuit dans toutes les branches de l’arborescence d’éléments visuels pour englober chaque élément visuel d’une page. Les éléments qui sont parents d’autres éléments sont responsables du dimensionnement et du positionnement de leurs enfants par rapport à eux-mêmes.
- L’invalidation est le processus par lequel une modification dans un élément d’une page déclenche un nouveau cycle de disposition. Les éléments sont considérés comme non valides lorsqu’ils n’ont plus la taille ou la position correcte. Chaque élément de l’arborescence d’éléments visuels qui a des enfants est alerté chaque fois que l’un de ses enfants change de taille. Par conséquent, une modification de la taille d’un élément dans l’arborescence d’éléments visuels peut entraîner des modifications qui remontent l’arborescence.

Pour plus d’informations sur l’exécution de la Xamarin.Forms disposition, consultez [création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md).

Le résultat du processus de disposition est une hiérarchie de contrôles natifs. Toutefois, cette hiérarchie comprend des convertisseurs de conteneur supplémentaires et des wrappers pour les convertisseurs de plateforme, ce qui augmente davantage l’imbrication de la hiérarchie d’affichage. Plus le niveau d’imbrication est élevé, plus la quantité de travail à Xamarin.Forms effectuer pour afficher une page est importante. Pour les dispositions complexes, la hiérarchie d’affichage peut être à la fois profonde et large, avec plusieurs niveaux d’imbrication.

Par exemple, considérez le bouton suivant de l’exemple d’application pour la connexion à Facebook :

![](layout-compression-images/facebook-button.png "Facebook Button")

Ce bouton est spécifié en tant que contrôle personnalisé avec la hiérarchie de vue XAML suivante :

```xaml
<ContentView ...>
    <StackLayout>
        <StackLayout ...>
            <AbsoluteLayout ...>
                <Button ... />    
                <Image ... />
                <Image ... />
                <BoxView ... />
                <Label ... />
                <Button ... />
            </AbsoluteLayout>
        </StackLayout>
        <Label ... />
    </StackLayout>    
</ContentView>
```

La hiérarchie d’affichage imbriquée résultante peut être examinée avec [Xamarin Inspector](~/tools/inspector/index.md). Sur Android, la hiérarchie d’affichage imbriqué contient 17 vues :

![](layout-compression-images/no-compression.png "View Hierarchy for Facebook Button")

La compression de la disposition, qui est disponible pour Xamarin.Forms les applications sur les plateformes iOS et Android, vise à aplatir l’imbrication des affichages en supprimant les dispositions spécifiées de l’arborescence d’éléments visuels, ce qui peut améliorer les performances de rendu des pages. L’avantage en matière de performances fourni dépend de la complexité d’une page, de la version du système d’exploitation utilisé et du périphérique sur lequel l’application s’exécute. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens.

> [!NOTE]
> Bien que cet article se concentre sur les résultats de l’application de la compression de disposition sur Android, il est également applicable à iOS.

## <a name="layout-compression"></a>Compression des dispositions

En XAML, la compression de disposition peut être activée en affectant `CompressedLayout.IsHeadless` à la propriété jointe la valeur `true` sur une classe de disposition :

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Elle peut également être activée en C# en spécifiant l’instance de disposition comme premier argument de la `CompressedLayout.SetIsHeadless` méthode :

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Étant donné que la compression de disposition supprime une disposition de l’arborescence d’éléments visuels, elle ne convient pas pour les dispositions qui ont une apparence visuelle ou qui obtiennent une entrée tactile. Par conséquent, les dispositions qui définissent des [`VisualElement`](xref:Xamarin.Forms.VisualElement) Propriétés (telles que,,, [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) et [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) ou qui acceptent les gestes) ne sont pas des candidats pour la compression de la disposition. Toutefois, l’activation de la compression de disposition sur une disposition qui définit des propriétés d’apparence visuelle, ou qui accepte les gestes, n’entraîne pas d’erreur de génération ou d’exécution. Au lieu de cela, la compression de disposition est appliquée et les propriétés d’apparence visuelle, ainsi que la reconnaissance de mouvement, échouent silencieusement.

Pour le bouton Facebook, la compression de disposition peut être activée sur les trois classes de disposition :

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
    <StackLayout CompressedLayout.IsHeadless="true" ...>
        <AbsoluteLayout CompressedLayout.IsHeadless="true" ...>
            ...
        </AbsoluteLayout>
    </StackLayout>
    ...
</StackLayout>  
```

Sur Android, il en résulte une hiérarchie d’affichage imbriquée de 14 vues :

![](layout-compression-images/layout-compression.png "View Hierarchy for Facebook Button with Layout Compression")

Par rapport à la hiérarchie d’affichage imbriqué d’origine de 17 vues, cela représente une réduction du nombre de vues de 17%. Bien que cette réduction puisse sembler non significative, la réduction de la vue sur une page entière peut être plus significative.

### <a name="fast-renderers"></a>Renderers rapides

Les convertisseurs rapides réduisent les coûts d’inflation et de rendu des Xamarin.Forms contrôles sur Android en aplatint la hiérarchie d’affichage Native résultante. Cela améliore davantage les performances en créant moins d’objets, ce qui à son tour entraîne une arborescence visuelle moins complexe et une utilisation moins importante de la mémoire. Pour plus d’informations sur les convertisseurs rapides, consultez les [convertisseurs rapides](~/xamarin-forms/internals/fast-renderers.md).

Pour le bouton Facebook dans l’exemple d’application, la combinaison de la compression de disposition et des convertisseurs rapides produit une hiérarchie d’affichage imbriquée de 8 vues :

![](layout-compression-images/layout-compression-with-fast-renderers.png "View Hierarchy for Facebook Button with Layout Compression and Fast Renderers")

Par rapport à la hiérarchie d’affichage imbriqué d’origine de 17 vues, cela représente une réduction de 52%.

L’exemple d’application contient une page extraite d’une application réelle. Sans la compression de disposition et les convertisseurs rapides, la page produit une hiérarchie d’affichage imbriquée de 130 affichages sur Android. L’activation de convertisseurs rapides et la compression de disposition sur les classes de disposition appropriées réduit la hiérarchie d’affichage imbriquée à 70 affichages, soit une réduction de 46%.

## <a name="summary"></a>Résumé

La compression de disposition supprime les dispositions spécifiées de l’arborescence d’éléments visuels afin d’améliorer les performances de rendu des pages. Les avantages en matière de performances de cette technique varient selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens.

## <a name="related-links"></a>Liens connexes

- [Création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md)
- [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

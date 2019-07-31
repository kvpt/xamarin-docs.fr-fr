---
title: Compression des dispositions
description: La compression des dispositions supprime les dispositions spécifiées à partir de l’arborescence visuelle dans le but d’améliorer les performances de rendu de page. Cet article explique comment activer la compression des dispositions et les avantages qu’elles peuvent apporter.
ms.prod: xamarin
ms.assetid: da9e1b26-9d31-4762-94c3-4039f306b7f2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2017
ms.openlocfilehash: 97aa8c321362ebccc954a79f99b7bc69b5a0ad63
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657076"
---
# <a name="layout-compression"></a>Compression des dispositions

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

_La compression des dispositions supprime les dispositions spécifiées à partir de l’arborescence visuelle dans le but d’améliorer les performances de rendu de page. Cet article explique comment activer la compression des dispositions et les avantages qu’elles peuvent apporter._

## <a name="overview"></a>Vue d'ensemble

Xamarin.Forms effectue la mise en page à l’aide de deux séries récursive d’appels de méthode :

- Disposition commence en haut de l’arborescence visuelle avec une page, et elle se poursuit dans toutes les branches de l’arborescence visuelle pour englober tous les éléments visuels sur une page. Les éléments qui sont des parents à d’autres éléments sont responsables de dimensionnement et de positionnement de leurs enfants par rapport à eux-mêmes.
- Invalidation est le processus par lequel une modification dans un élément sur une page déclenche un nouveau cycle de disposition. Éléments sont considérés comme non valides quand ils n’ont plus la bonne taille ou la position. Chaque élément dans l’arborescence visuelle qui a des enfants est averti chaque fois qu’un de ses enfants change les tailles. Par conséquent, une modification de la taille d’un élément dans l’arborescence visuelle peut entraîner des modifications ripple haut dans l’arborescence.

Pour plus d’informations sur comment Xamarin.Forms effectue la mise en page, consultez [création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md).

Le résultat du processus de mise en page est une hiérarchie de contrôles natifs. Toutefois, cette hiérarchie inclut des convertisseurs de conteneur supplémentaire et des wrappers pour les convertisseurs de plateforme, gonfler davantage la hiérarchie d’affichage d’imbrication. Plus le niveau d’imbrication, plus la quantité de travail que Xamarin.Forms doit effectuer pour afficher une page. Pour des dispositions complexes, la hiérarchie d’affichage peut être approfondie et large, avec plusieurs niveaux d’imbrication.

Par exemple, considérez le bouton suivant à partir de l’exemple d’application pour la connexion à Facebook :

![](layout-compression-images/facebook-button.png "Bouton de Facebook")

Ce bouton est spécifié comme un contrôle personnalisé avec la hiérarchie d’affichage XAML suivant :

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

La hiérarchie d’affichage imbriqué qui en résulte peut être examinée avec [Xamarin Inspector](~/tools/inspector/index.md). Sur Android, la hiérarchie d’affichage imbriquée contient 17 vues :

![](layout-compression-images/no-compression.png "Hiérarchie d’affichage pour le bouton de Facebook")

La compression des dispositions, qui est disponible pour les applications Xamarin.Forms sur les plateformes iOS et Android, vise à aplatir la vue en supprimant les dispositions spécifiées à partir de l’arborescence d’éléments visuels, ce qui peut améliorer les performances de rendu de page d’imbrication. L’amélioration des performances qui est remise varie selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens.

> [!NOTE]
> Si cet article se concentre sur les résultats de l’application de la compression des dispositions sur Android, il s’applique à iOS.

## <a name="layout-compression"></a>Compression des dispositions

Dans XAML, la compression des dispositions peut être activée en définissant le `CompressedLayout.IsHeadless` propriété jointe `true` sur une classe de disposition :

```xaml
<StackLayout CompressedLayout.IsHeadless="true">
  ...
</StackLayout>   
```

Vous pouvez également, il peut être activé en c# en spécifiant l’instance mise en page comme le premier argument de la `CompressedLayout.SetIsHeadless` méthode :

```csharp
CompressedLayout.SetIsHeadless(stackLayout, true);
```

> [!IMPORTANT]
> Étant donné que la compression des dispositions supprime une mise en page de l’arborescence visuelle, il n’est pas approprié pour les dispositions qui ont une apparence visuelle, ou qui obtiennent des entrées tactiles. Par conséquent, les dispositions qui définir [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) propriétés (telles que [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible), [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation), [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) et [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) ou qui accepter des gestes, ne sont pas des candidats pour la disposition compression. Toutefois, l’activation de la compression des dispositions sur une disposition qui définit les propriétés d’apparence visuelle, ou qui accepte des gestes, pas entraîne une erreur de build ou d’exécution. Au lieu de cela, la compression des dispositions s’appliqueront et propriétés d’apparence visuelle et de reconnaissance de mouvement, en mode silencieux échoue.

Pour le bouton de Facebook, la compression des dispositions peut être activée sur les classes de trois disposition :

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

Sur Android, il en résulte dans une hiérarchie d’affichage imbriqué de 14 vues :

![](layout-compression-images/layout-compression.png "Hiérarchie d’affichage pour le bouton de Facebook avec la Compression des dispositions")

Par rapport à la hiérarchie d’origine de l’affichage imbriqué de 17 vues, cela représente une réduction du nombre de vues de 17 %. Alors que cette réduction s’affichent non significatif, la réduction de la vue sur une page entière peut être plus importante.

### <a name="fast-renderers"></a>Renderers rapides

Renderers rapides réduisent l’inflation et les coûts de rendu des contrôles Xamarin.Forms sur Android en aplanissant la hiérarchie d’affichage natif qui en résulte. Cela améliore davantage les performances en créant moins d’objets, ce qui entraîne à son tour dans une arborescence d’éléments visuels moins complexe et moins de mémoire. Pour plus d’informations sur les renderers rapides, consultez [Renderers rapides](~/xamarin-forms/internals/fast-renderers.md).

Pour le bouton de Facebook dans l’exemple d’application, combinant la compression des dispositions et des renderers rapides génère une hiérarchie d’affichage imbriqué de 8 vues :

![](layout-compression-images/layout-compression-with-fast-renderers.png "Hiérarchie d’affichage pour le bouton de Facebook avec la Compression des dispositions et des Renderers rapides")

Par rapport à la hiérarchie d’origine de l’affichage imbriqué de 17 vues, cela représente une réduction de 52 %.

L’exemple d’application contient une page extraite à partir d’une application réelle. Sans la compression des dispositions et des renderers rapides, la page génère une hiérarchie d’affichage imbriqué de 130 vues sur Android. L’activation des renderers rapides et la compression des dispositions sur les classes de disposition appropriée permet de réduire la hiérarchie d’affichage imbriqué à 70 affichages, une réduction de 46 %.

## <a name="summary"></a>Récapitulatif

La compression des dispositions supprime les dispositions spécifiées à partir de l’arborescence visuelle dans le but d’améliorer les performances de rendu de page. Les avantages en matière de performances de cette technique varient selon la complexité d’une page, la version du système d’exploitation utilisé et l’appareil sur lequel l’application est en cours d’exécution. Toutefois, les gains en termes de performances les plus importants seront visibles sur les appareils les plus anciens.


## <a name="related-links"></a>Liens associés

- [Création d’une disposition personnalisée](~/xamarin-forms/user-interface/layouts/custom.md)
- [Convertisseurs rapides](~/xamarin-forms/internals/fast-renderers.md)
- [LayoutCompression (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutcompression)

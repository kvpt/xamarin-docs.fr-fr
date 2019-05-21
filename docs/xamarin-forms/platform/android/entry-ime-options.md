---
title: Options d’éditeur de méthode d’entrée entrée sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser l’Android spécifique à la plateforme qui définit des options de l’éditeur pour le clavier logiciel pour une entrée de la méthode d’entrée.
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4da446cf342065ce7766f8df0c71008ab47c31c4
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926821"
---
# <a name="entry-input-method-editor-options-on-android"></a>Options d’éditeur de méthode d’entrée entrée sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Cette spécifiques à la plateforme Android définit la méthode d’entrée des options de l’éditeur pour le clavier logiciel pour un [ `Entry` ](xref:Xamarin.Forms.Entry). Cela inclut la définition de bouton d’action de l’utilisateur dans l’angle inférieur du clavier logiciel et les interactions avec le `Entry`. Elle est consommée dans XAML en définissant le [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propriété attachée à une valeur de la [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) énumération :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

Le `Entry.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. Le [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour définir l’option d’action de méthode d’entrée pour le clavier logiciel pour le [ `Entry` ](xref:Xamarin.Forms.Entry), avec le [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) énumération fournissant les valeurs suivantes :

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indique qu’aucune action spécifique de clé est requise, et que le contrôle sous-jacent génère ses propres si possible. Il s’agit de `Next` ou `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indique qu’aucune clé de l’action ne sera disponible.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – Indique que la clé d’action effectue une opération de « go », qui mène l’utilisateur à la cible du texte tapé.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – Indique que la clé d’action effectue une opération de « recherche », qui mène l’utilisateur pour les résultats de recherche pour le texte qu’il a entré.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – Indique que la clé d’action effectuera une opération « Envoyer », en fournissant le texte à sa cible.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – Indique que la clé d’action effectuera une opération « suivante », qui mène l’utilisateur dans le champ suivant qui accepte le texte.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – Indique que la clé d’action effectuera une opération « done », de fermeture du clavier de manière réversible.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – Indique que la clé d’action effectuera une opération « précédente », qui mène l’utilisateur sur le champ précédent qui acceptera le texte.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – le masque pour sélectionner les options d’action.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – Indique que le vérificateur orthographique sera en savoir plus à partir de l’utilisateur, ni suggérer des corrections selon ce que l’utilisateur a tapé précédemment.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – Indique que l’interface utilisateur ne doit pas atteindre en plein écran.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indique qu’aucune interface utilisateur ne doivent être affichée pour le texte extrait.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indique qu’aucune interface utilisateur ne s’affichera pour les actions personnalisées.

Le résultat qui est spécifié [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valeur est appliquée pour le clavier logiciel pour le [ `Entry` ](xref:Xamarin.Forms.Entry), qui définit la méthode d’entrée options de l’éditeur :

[![Entrée d’entrée de méthode éditeur spécifique à la plateforme](entry-ime-options-images/entry-imeoptions.png "entrée d’entrée de méthode éditeur spécifique à la plateforme")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "entrée d’entrée de méthode éditeur spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)

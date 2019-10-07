---
title: Accessibilité du clavier
description: Au lieu d’utiliser la séquence d’onglets par défaut, il est parfois nécessaire de régler l’accessibilité de votre interface utilisateur en spécifiant la séquence d’onglets avec une combinaison des propriétés TabIndex et IsTabStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: b8c6ed9e803b8dec05b2279ed93f956ed11a1c07
ms.sourcegitcommit: 4cf434b126eb7df6b2fd9bb1d71613bf2b6aac0e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997106"
---
# <a name="keyboard-accessibility-in-xamarinforms"></a>Accessibilité du clavier dans Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

Les utilisateurs qui utilisent des lecteurs d’écran ou ayant des problèmes avec la mobilité peuvent rencontrer des difficultés à utiliser des applications qui ne fournissent pas un accès par clavier approprié. Il se peut que les applications Xamarin.Forms aient un ordre de tabulation attendu spécifié afin d’améliorer leur facilité d’utilisation et leur accessibilité. Spécifier un ordre de tabulation pour les contrôles permet la navigation au clavier, prépare les pages d’application à recevoir des entrées dans un ordre particulier et laisse les lecteurs d’écran lire les éléments actifs à leur utilisateur.

Par défaut, l’ordre de tabulation des contrôles correspond à celui où ils sont listés en XAML ou ajoutés par programmation à une collection enfant. Cet ordre est l’ordre dans lequel vous naviguerez entre les contrôles avec un clavier et dans lequel les contrôles seront lus par les lecteurs d’écran. Il constitue souvent le meilleur ordre possible. Toutefois, l’ordre par défaut ne correspond pas toujours à l’ordre attendu, comme le montre l’exemple de code XAML suivant :

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname" />
</Grid>
```

La capture d’écran suivante montre l’ordre des tabulations par défaut pour cet exemple de code :

![](keyboard-images/default-tab-order.png "Ordre des tabulations par défaut, basé sur les lignes")

L’ordre des tabulations est ici basé sur les lignes et correspond à l’ordre dans lequel les contrôles sont listés dans le code XAML. Par conséquent, lorsque vous appuyez sur la touche Tab, vous parcourez les instances [`Entry`](xref:Xamarin.Forms.Entry) de prénom, puis les instances `Entry` de nom de famille. Toutefois, une expérience plus intuitive consisterait à utiliser un parcours des tabulations par colonne en premier, afin de parcourir les paires prénom-nom de famille en appuyant sur la touche Tab. Cela est possible en spécifiant l’ordre de tabulation des contrôles d’entrée.

> [!NOTE]
> Sur la plateforme Windows universelle, vous pouvez définir des raccourcis clavier pour permettre aux utilisateurs de naviguer rapidement de manière intuitive et d’interagir avec l’interface utilisateur visible de l’application via le clavier plutôt que tactilement ou avec la souris. Pour plus d’informations, consultez [Définition des clés d’accès de VisualElement](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="setting-the-tab-order"></a>Définition de l’ordre des tabulations

La propriété `VisualElement.TabIndex` sert à indiquer l’ordre dans lequel les instances [`VisualElement`](xref:Xamarin.Forms.VisualElement) doivent recevoir le focus lorsque l’utilisateur parcourt les contrôles en appuyant sur la touche Tab. La valeur par défaut de la propriété est 0 et elle peut être définie sur une valeur `int` quelconque.

Les règles suivantes s’appliquent lorsque vous utilisez l’ordre des tabulations par défaut ou définissez la propriété `TabIndex` :

- Les instances [`VisualElement`](xref:Xamarin.Forms.VisualElement) avec une propriété `TabIndex` égale à 0 sont ajoutées à l’ordre des tabulations sur la base de leur ordre de déclaration dans le code XAML ou les collections enfants.
- Les instances [`VisualElement`](xref:Xamarin.Forms.VisualElement) avec une propriété `TabIndex` supérieure à 0 sont ajoutées à l’ordre des tabulations sur la base de leur valeur `TabIndex`.
- Les instances [`VisualElement`](xref:Xamarin.Forms.VisualElement) avec une propriété `TabIndex` inférieure à 0 sont ajoutées à l’ordre des tabulations et apparaissent avant toute valeur zéro.
- Les conflits sur une propriété `TabIndex` sont résolus par ordre de déclaration.

Après avoir défini l’ordre des tabulations, en appuyant sur la touche Tab, vous déplacez le focus d’un contrôle à l’autre, dans l’ordre croissant de `TabIndex`, en revenant au premier contrôle après avoir atteint le contrôle final.

L’exemple XAML suivant montre la propriété `TabIndex` définie sur les contrôles d’entrée pour activer le parcours des tabulations par colonne en premier :

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="0.5*" />
        <ColumnDefinition Width="0.5*" />
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Label Text="You"
           HorizontalOptions="Center" />
    <Label Grid.Column="1"
           Text="Manager"
           HorizontalOptions="Center" />
    <Entry Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="1" />
    <Entry Grid.Column="1"
           Grid.Row="1"
           Placeholder="Enter forename"
           TabIndex="3" />
    <Entry Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="2" />
    <Entry Grid.Column="1"
           Grid.Row="2"
           Placeholder="Enter surname"
           TabIndex="4" />
</Grid>
```

La capture d’écran suivante montre l’ordre des tabulations pour cet exemple de code :

![](keyboard-images/correct-tab-order.png "Ordre des tabulations basé sur les colonnes")

L’ordre des tabulations est ici basé sur les colonnes. Par conséquent, lorsque vous appuyez sur la touche Tab, vous parcourez les paires [`Entry`](xref:Xamarin.Forms.Entry) prénom-nom de famille.

> [!IMPORTANT]
> Les lecteurs d’écran sur iOS et Android respecteront le `TabIndex` d’un élément [`VisualElement`](xref:Xamarin.Forms.VisualElement) lorsqu’ils liront les éléments accessibles sur l’écran.

## <a name="excluding-controls-from-the-tab-order"></a>Exclusion de certains contrôles de l’ordre des tabulations

Outre la définition de l’ordre de tabulation des contrôles, il peut s’avérer nécessaire d’exclure certains contrôles de l’ordre des tabulations. Une façon de procéder consiste à définir la propriété [`IsEnabled`](xref:Xamarin.Forms.VisualElement) des contrôles sur `false`, car les contrôles désactivés sont exclus de l’ordre de tabulation.

Toutefois, il peut être nécessaire d’exclure des contrôles de l’ordre de tabulation même lorsqu’ils ne sont pas désactivés. Cela peut se faire avec la propriété `VisualElement.IsTabStop`, qui indique si un [`VisualElement`](xref:Xamarin.Forms.VisualElement) est inclus dans le parcours des tabulations. Sa valeur par défaut est `true`, et lorsque sa valeur est `false`, le contrôle est ignoré par l’infrastructure de parcours des tabulations, indépendamment de la définition d’une propriété `TabIndex`.

## <a name="supported-controls"></a>Contrôles pris en charge

Les propriétés `TabIndex` et `IsTabStop` sont prises en charge sur les contrôles suivants, qui acceptent une entrée au clavier sur une ou plusieurs plateformes :

- [`Button`](xref:Xamarin.Forms.Button)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`Switch`](xref:Xamarin.Forms.Switch)
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

> [!NOTE]
> Tous ces contrôles ne sont pas activables par tabulation sur toutes les plateformes.

## <a name="related-links"></a>Liens associés

- [Accessibilité (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

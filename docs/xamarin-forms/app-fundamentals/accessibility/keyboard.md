---
title: Accessibilité du clavier
description: Au lieu d’utiliser la séquence d’onglets par défaut, il est parfois nécessaire de régler l’accessibilité de votre interface utilisateur en spécifiant la séquence d’onglets avec une combinaison des propriétés TabIndex et IsTabStop.
ms.prod: xamarin
ms.assetid: 8be8f498-558a-4894-a01f-91a0d3ef927e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1590d0a4f9716541f18bc4f50a2c480c5e4478a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84129777"
---
# <a name="keyboard-accessibility-in-xamarinforms"></a>Accessibilité du clavier dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

Les utilisateurs qui utilisent des lecteurs d’écran ou ayant des problèmes avec la mobilité peuvent rencontrer des difficultés à utiliser des applications qui ne fournissent pas un accès par clavier approprié. Xamarin.Formsles applications peuvent avoir un ordre de tabulation attendu spécifié pour améliorer leur facilité d’utilisation et leur accessibilité. Spécifier un ordre de tabulation pour les contrôles permet la navigation au clavier, prépare les pages d’application à recevoir des entrées dans un ordre particulier et laisse les lecteurs d’écran lire les éléments actifs à leur utilisateur.

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

![](keyboard-images/default-tab-order.png "Default Row-based Tab Order")

L’ordre des tabulations est ici basé sur les lignes et correspond à l’ordre dans lequel les contrôles sont listés dans le code XAML. Par conséquent, le fait d’appuyer sur la touche Tab déplace les instances de prénom [`Entry`](xref:Xamarin.Forms.Entry) , suivies des instances de Surname `Entry` . Toutefois, une expérience plus intuitive consisterait à utiliser un parcours des tabulations par colonne en premier, afin de parcourir les paires prénom-nom de famille en appuyant sur la touche Tab. Cela est possible en spécifiant l’ordre de tabulation des contrôles d’entrée.

> [!NOTE]
> Sur la plateforme Windows universelle, vous pouvez définir des raccourcis clavier pour permettre aux utilisateurs de naviguer rapidement de manière intuitive et d’interagir avec l’interface utilisateur visible de l’application via le clavier plutôt que tactilement ou avec la souris. Pour plus d’informations, consultez [Définition des clés d’accès de VisualElement](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="setting-the-tab-order"></a>Définition de l’ordre des tabulations

La `VisualElement.TabIndex` propriété est utilisée pour indiquer l’ordre dans lequel les [`VisualElement`](xref:Xamarin.Forms.VisualElement) instances reçoivent le focus lorsque l’utilisateur parcourt les contrôles en appuyant sur la touche Tab. La valeur par défaut de la propriété est 0 et elle peut être définie sur une valeur `int` quelconque.

Les règles suivantes s’appliquent lorsque vous utilisez l’ordre des tabulations par défaut ou définissez la propriété `TabIndex` :

- [`VisualElement`](xref:Xamarin.Forms.VisualElement)les instances avec un `TabIndex` égal à 0 sont ajoutées à l’ordre de tabulation en fonction de leur ordre de déclaration dans les collections XAML ou enfants.
- [`VisualElement`](xref:Xamarin.Forms.VisualElement)les instances avec un `TabIndex` supérieur à 0 sont ajoutées à l’ordre de tabulation en fonction de leur `TabIndex` valeur.
- [`VisualElement`](xref:Xamarin.Forms.VisualElement)les instances d’une `TabIndex` valeur inférieure à 0 sont ajoutées à l’ordre de tabulation et apparaissent avant toute valeur nulle.
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

![](keyboard-images/correct-tab-order.png "Column-based Tab Order")

L’ordre des tabulations est ici basé sur les colonnes. Par conséquent, si vous appuyez sur la touche Tab, vous parcourez les paires prénom-nom [`Entry`](xref:Xamarin.Forms.Entry) .

> [!IMPORTANT]
> Les lecteurs d’écran sur iOS et Android respectent le `TabIndex` d’un lors de la [`VisualElement`](xref:Xamarin.Forms.VisualElement) lecture des éléments accessibles à l’écran.

## <a name="excluding-controls-from-the-tab-order"></a>Exclusion de certains contrôles de l’ordre des tabulations

Outre la définition de l’ordre de tabulation des contrôles, il peut s’avérer nécessaire d’exclure certains contrôles de l’ordre des tabulations. Pour ce faire, vous pouvez définir la [`IsEnabled`](xref:Xamarin.Forms.VisualElement) propriété des contrôles sur `false` , car les contrôles désactivés sont exclus de l’ordre de tabulation.

Toutefois, il peut être nécessaire d’exclure des contrôles de l’ordre de tabulation même lorsqu’ils ne sont pas désactivés. Cela peut être accompli avec la `VisualElement.IsTabStop` propriété, qui indique si un [`VisualElement`](xref:Xamarin.Forms.VisualElement) est inclus dans la navigation par onglets. Sa valeur par défaut est `true`, et lorsque sa valeur est `false`, le contrôle est ignoré par l’infrastructure de parcours des tabulations, indépendamment de la définition d’une propriété `TabIndex`.

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

## <a name="related-links"></a>Liens connexes

- [Accessibilité (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)
